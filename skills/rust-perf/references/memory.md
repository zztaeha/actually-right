# Memory: allocation, layout, collections

Allocation and cache behaviour are where most real Rust performance is won or lost.

## The cost model

Every allocation and deallocation typically takes a global lock, walks a non-trivial data structure and may make a syscall. **Small allocations are not necessarily cheaper than large ones** – the count matters more than the size. Heap access also costs a pointer chase, and modern CPUs are much faster when they jump around less.

Reference points: L1 miss ≈ 10 cycles, last-level miss ≈ 200 cycles. A single cache miss can outweigh hundreds of arithmetic operations.

## Vec

Three words: pointer, length, capacity. Growth is quasi-doubling – 0, 4, 8, 16, 32… so filling 1M elements costs about 20 reallocations plus 20 full copies.

| Situation | Do this |
|---|---|
| Size known or estimable | `Vec::with_capacity(n)` |
| Size learned partway through | `reserve(n)` – grows in exponential steps, so it stays amortized O(n) even in a loop. `reserve_exact` does not, so never call it in a loop |
| Zero-filled vec of length n | `vec![0; n]` – uses OS assistance, beats `resize`, `extend` and hand-written `unsafe` |
| Copying a slice in | `extend_from_slice` – specializes to `copy_from_slice`/memcpy for `T: Copy` and never calls `clone`. A `push` loop cannot do this |
| Removing by index, order irrelevant | `swap_remove` is O(1); `remove` is O(n) |
| Removing many items | `retain` (also on `String`, `HashSet`, `HashMap`) |
| Vec that will never grow again | `into_boxed_slice()` – 2 words instead of 3 |
| Vec built from a known-length iterator | `let b: Box<[T]> = it.collect();` avoids the reallocation entirely |
| Vec that is usually empty, stored inside a hot type | `ThinVec` – one word, len and capacity live with the elements |

`collect` calls `with_capacity` when the iterator's `size_hint` gives a bound – that is the entire mechanism. Implementing `size_hint` and `ExactSizeIterator::len` on a custom iterator is what buys single-allocation `collect` and `extend` (std's `TrustedLen` specialization). Known gap: `collect::<Result<Vec<_>, _>>()` does not reserve in advance.

To learn the right capacity, do not guess: `eprintln!` the length at the hot `push` site and post-process the distribution. Many short vectors and a few very long ones lead to opposite fixes.

**Small vectors**: `SmallVec<[T; N]>` stores up to N inline then spills to the heap. It is slightly slower than `Vec` for normal operations because every access checks inline-vs-heap, and if N or T is large the `SmallVec` itself becomes expensive to move. `ArrayVec` has a hard maximum and no heap fallback, which makes it the fastest of the three. `TinyVec` avoids `unsafe` but requires `T: Default`. The biggest real win is a *collection of* small vectors, where inline storage buys locality.

## Strings

`String` is a `Vec<u8>` with a UTF-8 guarantee, so all the capacity advice applies.

| Pattern | Replace with |
|---|---|
| `format!` producing a `String` in a loop | `write!(&mut buf, ...)` into a reused `String` |
| `x.to_string()` on a number in a hot loop | `itoa::Buffer` / `ryu::Buffer` – no allocation per conversion, roughly 2–5x faster than std |
| `to_string()` on a literal | The literal, or `Cow<'static, str>` |
| Mixed borrowed and owned text | `Cow<'a, str>` – borrow the unchanged case, allocate only when mutated (`to_mut`) |
| Many short strings | `SmartString` (inline ≤23 bytes on 64-bit, same size as `String`, drop-in) or `compact_str` |
| Strings cloned constantly | `smol_str` – O(1) clone via `Arc`, slight overhead above 22 bytes |
| The same strings repeated many times | Interning: `lasso`, `string-interner`, `symbol_table`. Comparison becomes an integer compare and storage becomes a `u32` |
| Reading text you only inspect as bytes | `BufRead::read_until` – skips UTF-8 validation |
| Bulk UTF-8 validation on the hot path | `simdutf8` – up to 23x std on valid non-ASCII, 4x on pure ASCII |

Interning is not a micro-optimization: rustc's own symbol interning changes were worth up to 1% each, and one hygiene-data change won 10% on a benchmark.

String equality already specializes to `memcmp` in std (20x the naive iterator version in the original PR, 8–22% in realistic code), and lengths are compared first, so unequal-length strings never touch memcmp. There is nothing to do here.

`chars()` yields 4-byte scalars; `bytes()` and `as_bytes()` stay dense. Use the cheapest view that answers the question. String slicing panics on a non-boundary index.

Reading lines without allocating per line:

```rust
let mut line = String::new();
while lock.read_line(&mut line)? != 0 {
    process(&line);
    line.clear();     // keeps the capacity
}
```

Formatting itself is not free even without allocation. Routing a plain string literal through `format_args!` and `write_fmt` is much slower than `write_str`, and formatting a `&str` with `"{}"` pulls in the whole `Display` implementation including unused padding and alignment code. `format_args!` does not allocate; `format!` does.

## Clone, and how to not

`clone` on anything holding heap memory allocates. Exceptions: `Rc`/`Arc` clone is a refcount bump, and `Copy` types are bitwise.

- `a.clone_from(&b)` is `a = b.clone()` but **reuses a's existing allocation**. Free win wherever you clone over an existing value.
- Store `&str`/`&[T]` in the struct instead of owned data when the lifetime allows. This adds lifetime annotations, so only do it with profiling evidence.
- Take ownership of an iterator rather than borrowing a slice, and move values out instead of cloning them.
- `Rc::make_mut` / `Arc::make_mut` give clone-on-write: they clone only when the refcount is above one.
- Some clones are simply stale, left behind by an earlier borrow-checker fight. Look for them.

Cloning to satisfy the borrow checker is the single most common avoidable cost in Rust.

Prefer `Rc::clone(&x)` over `x.clone()` on refcounted values. It makes deep clones visually distinct from refcount bumps when you are scanning for allocation sources.

**`Arc` clones are not free under contention.** On x86 the increment is a `lock xadd`; every thread touching the same `Arc` fights for the cache line, and the refcount often shares a line with the first bytes of the data, so a "cheap pointer clone" broadcasts an invalidation to every core holding that line. In genuinely low-contention code a deep copy into thread-local memory can beat sharing.

## Reusing collections

```rust
let mut buf = Vec::new();
for item in items {
    buf.clear();          // keeps capacity, drops contents
    fill(&mut buf, item);
    consume(&buf);
}
```

Take `&mut Vec<T>` in functions that produce collections instead of returning fresh ones, and keep long-lived workhorse buffers in the struct for methods called repeatedly. Cost: it obscures the fact that iterations are independent. Comment it.

For network buffers, `bytes::BytesMut` is the reuse-friendly version: `split_to` hands a frame downstream with a refcount bump instead of a copy, and `BufMut` lets you write into uninitialized capacity rather than zero-filling like `Vec<u8>`.

## Arenas and pools

When many allocations share one lifetime (per request, per compiler pass, per frame), an arena replaces N allocations and N frees with one bump per object and a single reset.

- **bumpalo** – heterogeneous, `Bump::alloc`, reset frees everything at once. Destructors are **not** run unless you use its `boxed` feature.
- **typed-arena** – one type only, simpler.

Allocation is a pointer bump; deallocation is O(1) and predictable; memory is contiguous and cache-friendly. The limitation is that you cannot free one object, and a single long-lived allocation keeps the whole chunk alive.

**Arenas beat `Rc` for graphs on every axis except holding external references to individual nodes.** Nodes refer to each other by `Copy` integer index, so there is no refcount traffic and no borrow-checker fight – and critically, **`Rc` cycles never free**, because Rust has no cycle collector. Use `slotmap` when nodes are removed (generational indices) and `slab` when they are not.

**Object pools** (`opool`, `object-pool`, `syncpool`, `concurrent-pool`) only beat the allocator when the object is large enough and the reset step is cheap. Otherwise you have reimplemented malloc, worse. `opool` is lock-free and inlines the acquire path.

## Type sizes

Shrinking oft-instantiated types cuts peak memory, memory traffic and cache pressure. **Types larger than 128 bytes are moved with `memcpy` rather than inline code** – if `memcpy` is hot, this is why.

```
RUSTFLAGS=-Zprint-type-sizes cargo +nightly build --release
```

prints size, alignment, discriminant size, per-variant size (largest first), field order and every byte of padding. `top-type-sizes` renders it compactly.

| Technique | Effect |
|---|---|
| `Box` a fat enum variant | Enum shrinks to the next largest variant; costs an allocation when that variant is used, so it wins when the variant is rare |
| `u32`/`u16`/`u8` indices instead of `usize` | Half or a quarter of the space; coerce at the point of use |
| `into_boxed_slice()` | 3 words to 2 |
| `ThinVec` | 3 words to 1 |
| Split a padded struct into parallel vectors | Removes padding entirely |

Error types deserve the same attention: a `Result<T, E>` is as large as its larger variant and is returned by value at every level of the call chain. `clippy::result_large_err` flags errors over 128 bytes. `anyhow::Error` is a narrow pointer – one word, not two like `Box<dyn Error>`.

The compiler already reorders fields to minimize padding. Do not hand-order, and do not add `#[repr(C)]` unless FFI or `transmute` requires it – that switches reordering off and reintroduces padding.

Niche optimization makes `Option<&T>`, `Option<Box<T>>` and `Option<NonZeroU32>` the same size as the inner type: the compiler uses an invalid bit pattern as the discriminant. Designing types so a niche exists is free memory.

Lock in the result:

```rust
#[cfg(target_arch = "x86_64")]
static_assertions::assert_eq_size!(HotType, [u8; 64]);
```

## Layout and locality

**Array of structs vs struct of arrays.** If you iterate millions of elements and touch only some fields, AoS drags whole cache lines of unused data through the cache. SoA stores each field in its own contiguous array: better cache use, less wasted bandwidth, and it is what makes vectorization possible. Measured gains around 50% are common in this shape of workload.

Use AoS when you touch all fields together or access entities individually. Use SoA for large homogeneous iteration: simulations, games, analytics. `soa-rs` (one allocation) and `soa_derive` (a Vec per field) generate it. Note that `push`/`pop` get *slower* under SoA because the fields are far apart – SoA is for iteration, not mutation.

Columnar storage is the same idea at data-engine scale, which is why Polars and Arrow are fast.

**Hot/cold field zoning.** A struct that bundles a frequently read field with rarely read ones forces the CPU to pull the cold bytes into cache on every hot access. Keep a hot header struct and reach the cold data through an index or an `Arc<Vec<u8>>`.

**Iteration order.** Match the loop order to the memory layout so the inner loop is stride-1. For a 2D array stored row-major, iterate rows in the outer loop. Transposing before a column-wise pass can be a net win even though it copies the whole matrix. For matrix work, cache blocking (tiling) is the standard technique; `matrixmultiply` and `faer` already do it.

**False sharing.** Two atomics written by two cores that share a cache line will ping-pong the line between them and destroy scaling, without showing up as lock contention.

```rust
use crossbeam_utils::CachePadded;
struct Counters { a: CachePadded<AtomicU64>, b: CachePadded<AtomicU64> }
```

The hardware line is 64 bytes on x86-64, but `CachePadded` uses 128 because Sandy Bridge and later prefetch line pairs. Eight `AtomicU64` counters are exactly one cache line, which is why per-thread counter arrays are the classic example. Verify with `perf c2c`.

**Prefetching.** `core::arch::x86_64::_mm_prefetch` with `_MM_HINT_T0..T2` or `_MM_HINT_NTA` (non-temporal, avoids polluting the cache). It is a hint the CPU may ignore, and over-prefetching causes evictions. Worth trying for pointer chasing through linked structures and for Eytzinger-layout search, not much else.

**Huge pages.** 2 MB pages instead of 4 KB cut TLB misses by 60–70% and improved throughput 15–35% in measured cases; on a 4 GB working set `perf stat` shows a 10–50x difference in TLB miss count. Enable via the allocator (`MALLOC_CONF="thp:always"`) or `madvise(MADV_HUGEPAGE)` on your arenas. THP is finicky and can regress memory usage – it was investigated for rustc and not enabled by default.

**NUMA.** Local-node access is faster and higher-bandwidth, and the remote penalty grows sharply with core count; a NUMA-aware implementation measured ~6x faster at 64 threads, with ~25% more typical on servers. Critical detail: **pinning threads is not enough.** Linux allocates on first touch, so a thread that allocates before it is pinned leaves its memory on the wrong node permanently. Control placement and allocation together.

## Hash maps

`HashSet`/`HashMap` allocate once contiguously and reallocate as they grow, exactly like `Vec` – `with_capacity` applies.

std's map is hashbrown, a SwissTable port: control bytes are probed **16 at a time with SIMD**, with triangular probing for collisions and one byte of metadata overhead per entry. It is a good hash table; the default *hasher* is the slow part.

The default hasher is SipHash-1-3: high quality, HashDoS-resistant, and slow for short keys such as integers. If profiling shows hashing is hot **and the input is trusted**, swap it. See `crates.md` for the ranked options and the security caveat.

**Below roughly 15–20 entries, a `Vec<(K, V)>` with a linear scan beats a `HashMap`** – the hash and the cache miss cost more than the scan. With a fast hasher the map wins at almost every size, so the real rule is: if a small map is hot, either flatten it or change the hasher, but do not leave the default in place.

**The `entry()` API is one lookup instead of two, except when it is not.** `entry` takes the key by value, so if you must clone a `String` to call it, the double lookup (`get_mut`, then `insert` on miss) can win – one benchmark measured it over 2x faster, and hashbrown's `entry_ref`/`raw_entry_mut` nearly 7x. Cheap `Copy` key: use `entry`. Key you would have to clone: do not.

Some types do not need hashing at all: a newtype over an already-random integer can use `nohash_hasher`. For types with no padding, byte-wise hashing (`ByteHash` from zerocopy or bytemuck) can beat the derived per-field `Hash`.

## Wrapper types

Accessing `RefCell`, `Mutex` and friends is not free. If several values are always accessed together, put them under one wrapper:

```rust
// before: two locks per operation
struct S { x: Arc<Mutex<u32>>, y: Arc<Mutex<u32>> }
// after: one
struct S { xy: Arc<Mutex<(u32, u32)>> }
```

`RefCell` moves borrow checking to runtime and charges a small runtime cost plus a panic risk for it.

The deeper cost is invisible: anything containing an `UnsafeCell` (which is every interior-mutability type) loses the LLVM `noalias` and readonly attributes. The access itself optimizes to nearly nothing; what you pay for is the optimizations that no longer fire around it.

`thread_local!` is also not free: it carries a "is this destroyed?" check and a lazy-init check on every access, measured about 2x C's TLS. Use the const form when the initializer allows it:

```rust
thread_local! {
    static COUNT: Cell<u32> = const { Cell::new(0) };
}
```

## Drop

Drop glue is recursive and is not free. Dropping a large nested structure can take milliseconds, and it happens at scope exit where you are not looking for it.

- Look for `drop_in_place` in the profile.
- Reusing a buffer avoids dropping and reallocating it.
- `defer-drop` moves the drop of a huge value to a background thread, turning milliseconds into microseconds on the hot thread. Only with profiling evidence, and only if you cannot outproduce the drop thread – otherwise memory grows without bound.
- Dropping a lock guard early (`drop(guard)` or a tighter scope) is a concurrency fix, not a memory one. See `concurrency.md`.

Reference cycles of `Rc`/`Arc` leak. Break them with `Weak`, or use an arena.

## When RSS grows and the heap does not

This is usually allocator behaviour, not a leak, and no amount of code reading will find it.

glibc keeps freed blocks in per-thread caches and per-arena bins rather than returning them to the OS. A single long-lived allocation made between two short-lived ones **pins an entire arena**, so a mostly empty arena is never released. Fragmentation of 30% unused RSS is normal on a bursty workload.

Heap profilers report live allocations. RSS includes everything the allocator is holding. The gap between them is the diagnosis.

Fixes, in order: switch to jemalloc (background purging returns pages on a decay timer instead of waiting for future allocations) or mimalloc; tune `dirty_decay_ms`/`muzzy_decay_ms` and `narenas` if you stay on jemalloc; only then look at your own allocation pattern.
