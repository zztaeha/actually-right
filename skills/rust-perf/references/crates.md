# Swap table

What to replace with what, and what it is measured to be worth. Every row is conditional on the profiler pointing at it first.

## Allocators

| Replace | With | Gain | Caveat |
|---|---|---|---|
| System allocator | `mimalloc` | Best broad default. Actix API: 10,704 req/s vs 9,981 for jemalloc. 5.3x glibc under heavy multi-threading with ~half the RSS | Bigger binary, longer compile |
| System allocator | `tikv-jemallocator` | Strong on Linux/macOS. Background purging returns pages on a decay timer, which is the fix for RSS creep | More memory with THP |
| System allocator | `snmalloc-rs` | Good under heavy multi-threading (≈1.25x jemalloc in one study) | Less widely used |
| **musl default** | **Anything above** | Non-negotiable. musl's allocator is ~4x slower multi-threaded, with reports of 10–40x on async workloads | – |

## Hashing

Default `SipHash-1-3` is DoS-resistant and slow for short keys.

| Hasher | Position | Notes |
|---|---|---|
| `gxhash` | Fastest at all sizes | Heavy SIMD + AES instructions. **Not portable** – needs `target-cpu` flags. Best when you control the hardware and mostly hash strings |
| `rapidhash` | Fastest portable | Same integer construction as foldhash, notably faster on strings. Works at compile time |
| `foldhash` | Balanced default | Rank 1.58, geomean 6.21 on a Xeon 8481C. Already hashbrown's default |
| `rustc-hash` / FxHash | Fastest and smallest | Rank 2.09, 32 B. Low quality. **Trusted input only** |
| `ahash` | Rank 3.70, 64 B | Uses AES where available |
| SipHash (std) | Rank 4.97, geomean 28.70 | The default. HashDoS-resistant |

Historic rustc data: fnv → fxhash up to 6% faster; fxhash → ahash 1–4% **slower**; fxhash → default 4–84% slower.

**Never swap the hasher on a map keyed by untrusted input.** That is the HashDoS attack surface the default exists to close.

Special case: a newtype over an already-random integer needs no hashing at all – `nohash_hasher`.

If you standardize on a non-default map, add a `clippy.toml` so the std one cannot creep back in:
```toml
disallowed-types = ["std::collections::HashMap", "std::collections::HashSet"]
```

## Collections

| Need | Use |
|---|---|
| Unordered key-value, O(1) | `HashMap` (+ faster hasher) |
| Under ~15–20 entries | `Vec<(K, V)>` with a linear scan, or `micromap` / `hybridmap` |
| Sorted keys, range queries | `BTreeMap` – competitive with HashMap up to roughly a thousand entries, and no resize spikes |
| Insertion order preserved with hash performance | `indexmap` |
| Stable keys for entities or graph nodes | `slotmap` (generational, survives removal) or `slab` (no removal) |
| Small collections, mostly inline | `smallvec` (spills to heap), `arrayvec` (hard max, fastest), `tinyvec` (no unsafe, needs `Default`) |
| Vector that is usually empty inside a hot type | `thin_vec` – one word |
| Concurrent map | `dashmap` above 2–4 contending threads; a single `RwLock<HashMap>` below that |
| Priority queue with priority updates | `priority-queue` / `keyed_priority_queue` – O(log n) change-priority instead of a rebuild |
| Monotone priority queue (Dijkstra, bounded weights) | `radix-heap` – O(1) insert, pop cost depends on key range not element count |
| Tunable heap arity | `dary_heap` |
| Ring buffer | `VecDeque` – but note the contents may be non-contiguous |
| Dense bitset | `bitvec` |
| Large sparse bitset with set operations | `roaring` – native AND/OR/ANDNOT on dense chunks; powers ClickHouse, Lucene, Druid, Elasticsearch |
| Persistent / immutable collections | `im` (Arc) or `im-rc` (Rc) – mutates in place when the refcount says you are the sole owner, about an order of magnitude faster than the immutable path |
| Struct of arrays | `soa-rs` (one allocation) or `soa_derive` (a Vec per field). Iteration only – `push`/`pop` get slower |
| Graphs | `petgraph`; `graphalgs` for algorithms it does not ship |
| Linked list | Almost never. It destroys cache locality |

## Strings and text

| Need | Use |
|---|---|
| Mostly short strings | `smartstring` – inline ≤23 bytes on 64-bit, same size as `String`, drop-in |
| Strings cloned constantly | `smol_str` – O(1) clone via `Arc`; slight overhead above 22 bytes |
| Compact general replacement | `compact_str` |
| Mostly literals, occasionally owned | `Cow<'static, str>` (what clap does) |
| The same strings repeated many times | `lasso` (the only one with multithreaded interning), `string-interner`, `symbol_table` (sharded, fastest under contention) |
| Number to string in a hot loop | `itoa` (integers), `ryu` (floats) – no per-conversion allocation, ≈2–5x faster than std |
| String to float | `fast-float` or `lexical` – 3–10x std, up to 1.5 GB/s on an M1. Check your toolchain first, the algorithm was upstreamed into core |
| Byte strings | `bstr` |
| Bulk UTF-8 validation | `simdutf8` – up to 23x std on valid non-ASCII, 4x on pure ASCII (x86-64); 11x / 4x on ARM |
| Text rope for an editor | `jumprope` (fastest, ~35–40M edits/s), `crop` (~50% faster concatenation than ropey), `ropey` (most featureful, SIMD since 2025) |
| Diff | `imara-diff` – up to 30x `similar` on real inputs; its histogram algorithm beats Myers by 10–100%. `similar` is dependency-free with more algorithms |

## Search and parsing

| Need | Use |
|---|---|
| Find a byte or substring | `memchr` – SSE2 without std, AVX2 with runtime detection |
| Find many patterns at once | `aho-corasick` – Teddy SIMD prefilter; stays flat as pattern count grows |
| Regex | `regex` – linear time, no catastrophic backtracking. **Compile once into a `LazyLock`.** The `perf-literal` features (which pull in memchr for the prefilter) are where most of the speed lives. Default build ≈1000 KB and 1.4 s compile; `regex-lite` is ≈94 KB with no Unicode and no perf work |
| Match many globs against one path | `globset` – compiles the set into one automaton |
| JSON | `sonic-rs` (2–4 GB/s, 1.5–2x simd-json, 3–4x serde_json – it parses straight into the target struct instead of an intermediate tape; needs `target-cpu` flags) |
| JSON, portable | `simd-json`, then `serde_json`. `arbitrary_precision` costs ~2x on float parsing. `RawValue` borrows with `from_str`/`from_slice` but must be boxed with `from_reader` |
| CSV | `csv` (compiles `csv-core` to a table-driven DFA in a few hundred stack bytes); `csv_lib` for memmap + AVX2/NEON |
| XML | `quick-xml` – beats `roxmltree` at every size, ~50x `xml-rs`. Streaming tokenizer vs tree builder is the real choice |
| Markdown | `pulldown-cmark` – 7x `comrak`, because it pull-parses instead of building an AST |
| Parser combinators | `winnow` > `chumsky` > `nom` > `pest` |
| HTTP | `httparse` |
| Templates | `askama` ~1.27 µs/render (compile-time codegen, needs a rebuild per template edit); `minijinja` ~4.49 µs with hot reload; `tera` ~6.97 µs |
| Config (TOML/YAML) | Nobody optimizes these and it runs once at startup. Do not spend time here |

## Serialization

| Need | Use |
|---|---|
| Zero-copy deserialization | `rkyv` – the encoded form is the in-memory form, so deserialization does no work. Fastest overall |
| Zero-copy with a cross-language schema | Cap'n Proto (fast serialize, fast zero-copy read) or FlatBuffers (genuinely zero-allocation on read, poor serialize on structured data) |
| Fastest binary, Rust-only | `bincode` |
| Small binary, embedded | `postcard` – ~1.5x slower than bincode at ~70% the size (60 ns ser / 180 ns deser, 41 bytes in one measurement) |
| Smallest self-describing | MessagePack |
| Protobuf | `prost` (ecosystem default) or `quick-protobuf`; both beat `rust-protobuf` |
| Borrowed fields inside serde | `#[serde(borrow)]` on `&str`/`Cow` fields; `zerovec` for typed slices |
| Byte-level zero copy | `zerocopy`, `bytes` |

## Compression

| Need | Use |
|---|---|
| Best all-round | `zstd` – ~72% ratio, ~280 MB/s compress, ~1200 MB/s decompress. Levels 1–22 span near-lz4 speed to near-brotli ratio |
| Maximum decompression speed | `lz4_flex` |
| Maximum ratio (web assets) | `brotli` |
| Interop with Google stacks | `snappy` – similar ratio to lz4, slower to decompress |

## Concurrency

| Replace | With | Gain |
|---|---|---|
| `std::sync::Mutex` | `parking_lot::Mutex` | ~1.5x uncontended, up to 5x contended; 1 byte |
| `std::sync::RwLock` | `parking_lot::RwLock` | Almost always faster, up to 50x in some read/write mixes |
| `std::sync::mpsc` | `flume` | Always faster than std, sometimes faster than crossbeam |
| `std::sync::mpsc` | `crossbeam-channel` | Often fastest 1:1; has `select!` |
| Bounded SPSC in a real-time thread | `rtrb` | Wait-free, no allocation. p99 207 µs vs 349 µs against crossbeam in one measurement |
| Manual thread pools | `rayon` | Work stealing, no chunk tuning needed |
| Unpadded shared atomics | `crossbeam_utils::CachePadded` | Removes false sharing; linear scaling with cores |
| Manual `Once` init | `std::sync::LazyLock` (1.80+) / `OnceLock` | No dependency needed. `once_cell` measured ~20% faster than `lazy_static` at 32 threads |
| Hand-rolled object reuse | `opool` | Lock-free, inlinable acquire path. Only worth it for large objects with a cheap reset |
| In-process cache | `moka` | TinyLFU admission + LRU eviction, sync and async. crates.io runs it at ~85% hit rate in front of PostgreSQL |
| Thread pinning | `core_affinity`, `affinitypool` | Warm caches, no scheduler jitter |

## Tooling

| Tool | Install | For |
|---|---|---|
| `samply` | `cargo install --locked samply` | Sampling profiler, all three platforms, Firefox Profiler UI |
| `cargo-flamegraph` | `cargo install flamegraph` | Flame graphs from cargo |
| `cargo-instruments` | `cargo install cargo-instruments` | macOS: Time Profiler, Allocations, Leaks |
| `criterion` | dev-dependency | Statistical benchmarking on stable |
| `divan` | dev-dependency | Lower-overhead benchmarking, generic fns, **allocation counts** |
| `iai-callgrind` | dev-dependency | Instruction counts – the CI-safe metric |
| `hyperfine` | `cargo install hyperfine` | Whole-binary wall-clock comparison |
| `oha` | `cargo install oha` | HTTP load with live percentiles |
| `hdrhistogram` | dependency | Latency percentiles with coordinated-omission correction |
| `minstant` | dependency | ~10.5 ns timestamps (TSC) vs std's ~27 ns |
| `dhat` | dev-dependency | Heap profiling plus allocation assertions in tests |
| `bytehound` / `heaptrack` | system | Whole-process heap profiling on Linux |
| `hotpath` | dependency | Per-function time and allocations, lock wait/hold, channel state |
| `profiling` + tracy/puffin | dependency | Frame-based instrumented profiling (~60 ns/scope) |
| `tokio-console` / `tokio-metrics` | dependency | Async task starvation and poll times |
| `cargo-show-asm` | `cargo install cargo-show-asm` | Asm, LLVM IR, MIR, WASM per function |
| `cargo-bloat` / `twiggy` | `cargo install cargo-bloat` | What occupies the binary (twiggy for WASM) |
| `cargo-llvm-lines` | `cargo install cargo-llvm-lines` | IR lines and instantiations per generic function |
| `cargo-pgo` | `cargo install cargo-pgo` | PGO and BOLT |
| `cargo-multivers` | `cargo install cargo-multivers` | One binary per microarchitecture level, dispatched at startup |
| `cargo-wizard` | `cargo install cargo-wizard` | Applies vetted profile presets |
| `cargo-nextest` | `cargo install cargo-nextest` | Up to 3x faster test runs |
| `cargo-chef` | in the Dockerfile | 5x Docker builds via layer caching |
| `sccache` | `cargo install sccache` | Shared compilation cache, 50–80% off rebuilds |
| `mold` / `wild` | system package | Faster linking on Linux |
| `miri` | `rustup +nightly component add miri` | UB detection for unsafe code |
| `assert_no_alloc` | dev-dependency | Proves a real-time path does not allocate |
| `top-type-sizes` | `cargo install top-type-sizes` | Renders `-Zprint-type-sizes` compactly |
| Bencher / CodSpeed | service | CI regression gating |
| Parca / Pyroscope | service | Continuous eBPF profiling in production, <1% overhead |

## Reference material worth citing to the user

- The Rust Performance Book (Nethercote) – the canonical reference this skill is built on.
- The Bounds Check Cookbook (Shnatsel) – safe recipes for eliminating bounds checks, each with the asm.
- min-sized-rust (johnthagen) – the full binary size ladder.
- Rust Atomics and Locks (Bos) – for anything lock-free; chapter 7 is the per-architecture cost model.
- The Rustonomicon – required before writing `unsafe`.
- "How to speed up the Rust compiler" (Nethercote, ongoing) – the best worked examples of the measure, find a silly inefficiency, fix, repeat loop.
- "It's Not Always ICache" (matklad) – on measuring relative differences honestly.
- The image-rs `fast_blur` write-up – 5.9x from two arithmetic changes, no unsafe.
