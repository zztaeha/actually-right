# Code shape: what the compiler can and cannot do for you

Rust's abstractions are genuinely zero-cost when the optimizer can see through them. This file is about the cases where it cannot.

## Iterators are not the problem

Benchmarked in the book on a real workload: a hand-written `for` loop at 19,620,300 ns/iter against the equivalent iterator chain at 19,234,900 ns/iter. Iterators compile to roughly the assembly you would write, and they get loop unrolling and bounds-check elimination for free. The indexed loop is the slower one.

So: use iterators. Where they do cost something:

| Pattern | Why | Fix |
|---|---|---|
| `.collect()` into a collection that is only iterated again | Materializes for nothing | Return `impl Iterator<Item = T>` |
| `.collect()` then `append` | Two allocations | `extend` the existing collection |
| `filter(..).map(..)` on a hot iterator | Two adapter layers | `filter_map` |
| `chain` on a hot iterator | Can be slower than a single iterator | Restructure if it shows in the profile |
| `chunks` where the size divides the length | Slower than the exact variant | `chunks_exact` + `remainder()` (same for `rchunks`; `_mut` variants use `into_remainder()`) |
| `iter()` over small `Copy` types | LLVM sometimes does better by value | `iter().copied()` – advanced, verify in the asm |
| Custom iterator without `size_hint` | `collect`/`extend` cannot pre-reserve | Implement `size_hint`, `ExactSizeIterator::len` |

Returning an iterator instead of a `Vec` also changes latency behaviour: results stream as they are produced instead of appearing only after everything is collected.

## Bounds checks

Indexing carries a bounds check plus a panic edge, and the panic edge is often the bigger cost because it blocks other optimizations.

Keep the cost in proportion. A network-driver study measured Rust needing **65% more instructions** than C per packet because of bounds checks, but only **6% more cycles** – an out-of-order superscalar core hides most of the work. Bounds checks are an instruction-count problem far more often than a time problem.

Safe removals, in order of preference:

1. **Iterate instead of indexing.** `for x in &v` has nothing to check.
2. **Slice once before the loop.** `let s = &self.data[..];` then index `s` – the compiler can then prove the length is loop-invariant.
3. **Assert the range up front.** `assert!(i + len <= v.len());` before the loop gives LLVM the fact it needs to drop the per-iteration check.
4. **Iterate two slices with `zip`** rather than indexing one by the other's index.
5. **Const-sized arrays** (`[T; N]`, const generics) remove the check entirely because the length is known at compile time.

The Bounds Check Cookbook (Shnatsel) collects the full set of safe recipes, each with the command to view its assembly. The largest win recorded there is about 15%.

Only after exhausting those: `get_unchecked` / `get_unchecked_mut`. Measured range is roughly **3–15%**, commonly quoted at ~5%, approaching 50% in extreme scanning workloads (regex over a large haystack) – and at least one measured case where the unsafe single-array version was **14.8% slower** than the safe slice version in parallel execution. It is `unsafe` and requires a proof, so it needs a `// SAFETY:` comment, a Miri run, and a benchmark that justifies it.

## Inlining

Four states: none (compiler decides based on opt-level, size, genericity and crate boundary), `#[inline]`, `#[inline(always)]`, `#[inline(never)]`.

The part that matters and is easy to miss: **`#[inline]` serializes the function body into crate metadata, which is what makes cross-crate inlining possible at all.** Without it, a downstream crate sees only the signature, unless LTO is on. Modern rustc auto-enables this for small functions, but for a small hot function in a library, `#[inline]` is the right annotation.

- Best candidates: very small functions, or functions with a single call site.
- **Inlining is not transitive.** If `f` calls `g` and you want both inlined at a call site to `f`, annotate both.
- `#[inline(always)]` is almost never right. Measure after adding any inline attribute: the effect is unpredictable, it can push out a neighbouring function that was previously inlined, and cross-crate inlining costs compile time.
- Cachegrind tells you whether a function was inlined: it was, if and only if its first and last lines carry no event counts.

When `#[inline(never)]` turns out faster, resist the reflex to blame the instruction cache. That explanation is repeated everywhere and rarely traced to an actual icache measurement.

The inverse is **outlining**: move a big rarely-taken branch into its own `#[cold]` function so the hot path stays compact.

Split the two when a big function has one hot call site and several cold ones:

```rust
#[inline(always)] fn work_inlined() { /* ... */ }
#[inline(never)]  fn work_outlined() { work_inlined() }
```

## Branches

A mispredicted branch costs 10–20 cycles (Skylake 16–17, Ice Lake 17–21, Zen 1–2 ~19), and the pipeline flush throws away every instruction in flight.

The canonical demonstration: filter a million random floats at a 50% threshold. The predictor is reduced to a coin flip, wrong every second element – roughly half a million flushes, about **2 ms of pure penalty** on a 4 GHz core. Sort the input first and the same code runs **4.5x faster**, because the branch says "skip" for the whole first half and "keep" for the whole second.

**Selectivity decides whether branchless wins.** At 50% it wins hugely. At 1% the branchy version wins, because the branch is nearly always predicted correctly while the branchless version unconditionally pays for the extra write. Measure the hit rate before rewriting.

Other rules:

- `#[cold]` on a function tells the optimizer it is rarely called, which improves the layout of the hot path.
- `std::hint::cold_path()` (safe, no `unsafe` block) marks a branch or match arm cold, and can be used to build `likely`/`unlikely` helpers. It lowers to `llvm.expect` / branch weights.
- A `match` over many variants usually becomes a jump table. Since Rust 1.65 / LLVM 15, `match` and an equivalent if-else chain generally emit the same assembly – the old "match is faster" advice has converged.
- Optimistically check the simple common case before the general one. Special-casing collections with 0, 1 or 2 elements is a repeatedly proven win when small sizes dominate.
- Wrong hints make things slower. Only add them with measured frequencies, and record the measurement in a comment.

## Dispatch

Monomorphized generics are static dispatch: no indirection, full inlining, at the cost of compile time and code size. `dyn Trait` is dynamic dispatch: a vtable load and an indirect jump, and – usually the bigger cost – **no inlining across the boundary**, which blocks every optimization downstream of it.

Measured over 1024 elements: boxed `dyn` 5,900,191 ns/iter, `&dyn` 5,658,461 ns/iter, enum dispatch 479,630 ns/iter. With `black_box` to defeat the optimizer: 2,131,736 / 2,131,978 / 471,740. A separate comparison over 20M elements: static 64 ms, dynamic 216 ms.

**The most actionable version of this: hoist the dispatch out of the loop.** Decide which implementation once, outside, then run a monomorphic body. The compiler cannot do that for you across a `dyn` call inside the loop.

| Situation | Use |
|---|---|
| Closed set of types you own | Enum + `match`, or the `enum_dispatch` crate |
| Open set, callers add types (plugins, user extensions) | `dyn Trait` |
| Homogeneous collection | Generic `Vec<T>` |
| Heterogeneous collection, set known | `Vec<MyEnum>` |
| Heterogeneous collection, set open | `Vec<Box<dyn Trait>>` |
| Many instantiations blowing up compile time and binary size | `dyn` on the cold paths |

`dyn Trait` also has real benefits: smaller binaries, faster compiles, and it is the only option for a genuinely open set. Choose deliberately.

Closures follow the same rule: `impl Fn` is monomorphized with the environment on the stack and full inlining; `Box<dyn Fn>` builds the environment on the stack, copies it to the heap, and adds a vtable pointer.

Monomorphization bloat has a standard fix – keep the generic shell tiny and put the body in a non-generic inner function. See `build.md` for the pattern and `cargo llvm-lines` for finding the offenders.

## Compile-time computation

`const fn` and const generics move work out of runtime entirely.

- Precompute lookup tables in a `const` block instead of building them at startup.
- Const generic array sizes let the compiler drop bounds checks, unroll, inline and vectorize with full knowledge.
- `const` values are inlined at every use site; `static` values have one fixed address. A 10,000-element const table adds 10,000 elements to the binary at each materialization point – check the size impact.
- Compile a regex once into a `LazyLock` (stable since 1.80) rather than per call. `Regex::new` in a loop costs microseconds to milliseconds each time and defeats the engine's internal allocation reuse. `Regex` is `Sync`, so one instance serves every thread.

Const eval has a practical ceiling: a computation that takes a few seconds at compile time makes builds painful. Past that, use a build script or code generation.

## Numerics

**Integer overflow checks** are on in dev and off in release. Enabling them everywhere is estimated at 1.5x–5x. Release wraps silently, so `200u8 + 200 == 144` and `-128i8 * -1 == -128`. Be explicit where it matters: `checked_*` (returns `Option`), `wrapping_*`, `saturating_*`, `overflowing_*`. Setting `overflow-checks = true` in release costs roughly the last 1% and buys real safety – worth offering.

**Integer division is expensive**: a single `div` stalls the pipeline for 20–30 cycles. Divide by a constant where possible so the compiler turns it into a multiply-and-shift, or use `strength_reduce` for a runtime divisor (5–10x).

**Floats.** `f64` is the default and is about as fast as `f32` for scalar work on modern CPUs, but `f32` halves memory traffic and doubles SIMD lane count, so it wins in bulk. The compiler cannot reassociate float arithmetic – `a + (b + c)` is not `(a + b) + c` – which is the usual reason a float loop refuses to vectorize. Options: restructure the accumulation manually (multiple partial sums), or opt into relaxed semantics with the `fast_fp` wrappers where the numerics allow it.

Parsing floats with std is 3–10x slower than `lexical` or `fast-float` (up to 1.5 GB/s on an M1 against std's ~130 MiB/s). The algorithm was later adopted into core, so check your toolchain before adding the dependency.

The image-rs `fast_blur` case is the model for this whole section: **5.9x from exactly two changes** – replacing float arithmetic with integer accumulators (~1.83x on its own) and replacing integer division with reciprocal multiplication. No rewrite, no unsafe, no new data structures.

## Loops

rustc and LLVM are not aggressive about unrolling. Manual unrolling has been measured at up to 3x on tight loops; with `chunks_exact_mut` the assembly vectorizes but processes at most eight elements per iteration. Gotcha: **using the loop variable inside the body can prevent unrolling entirely**, because it blocks constant-folding of the index. The `unroll` and `crunchy` crates do it with macros.

## SIMD

Three levels, in increasing order of effort:

1. **Let LLVM auto-vectorize.** Give it what it needs: contiguous slices, no bounds checks, no aliasing ambiguity, no float reassociation requirement, simple loop bodies. Typically ~3x when it works. It also silently fails on obvious loops, and in complex code a bad auto-vectorization can be slower than scalar.
2. **`std::simd`** (portable-simd) – still **nightly only**; the blockers are mask element types and the swizzle API. ~4x consistently in measured comparisons. On stable, `wide` gives portable SIMD (somewhat slower than `std::simd`, vastly faster than scalar); `pulp`, `macerator` and `fearless_simd` are higher-level options.
3. **`core::arch` intrinsics** with `is_x86_feature_detected!` runtime dispatch, or `#[target_feature(enable = "avx2")]` on an `unsafe fn`. Maximum control, one implementation per target.

Runtime dispatch costs about 0.3 ns (one indirect call) with the `multiversion` crate – effectively free, so there is no performance reason to ship a non-portable binary.

**When it does not vectorize, ask the compiler why** rather than guessing:

```
RUSTFLAGS="-C remark=all" cargo build --release
RUSTFLAGS="-Cllvm-args=-pass-remarks-missed=loop-vectorize" cargo build --release
```

LLVM vectorizes only when it can prove the iterations are independent, the memory accesses are well behaved, and its cost model says it is worth it. Known trap: a loop that unrolls on x86_64 and ppc64el but not aarch64 (rust#105857) – always check the asm for the target you ship.

Do not hand-write SIMD before laying out the data for it. SoA first, SIMD second (see `memory.md`).

For string and byte search, do not write it yourself: `memchr` (SSE2 without std, AVX2 with runtime detection) and `aho-corasick` (Teddy packed multi-pattern SIMD prefilter) are already the state of the art. SIMD substring search wins with few patterns; Aho-Corasick stays flat as the pattern count grows.

## Recursion

Rust has **no guaranteed tail call optimization** and `become` is not stabilized, so deep recursion overflows the stack. Spawned threads get 2 MB by default; the main thread's is platform-determined.

Options, in order: rewrite as a loop with an explicit stack; the `tailcall` crate (turns self-recursion into deferred computations run in a loop); or `stacker`, which allocates a fresh 1 MiB segment when under 100 KiB remains. `stacker` costs about 1% on Linux, more on Windows (it uses fibers), and is a no-op on unsupported platforms. rustc itself uses this pattern.

An async recursive function must box its future.

## Inspecting the output

```
cargo install cargo-show-asm
cargo asm --release my_crate::my_function
```

`cargo-show-asm` also prints LLVM IR, MIR and WASM. Compiler Explorer is better for shareable snippets and cross-compiler comparison. Use either whenever you claim something "should" vectorize or "should" be inlined.

## FFI

`extern "C"` calls are genuinely zero-cost – identical to a C call. The real cost is that **the compiler cannot inline across the boundary**, so a tight loop calling into C repeatedly loses every optimization downstream of the call. The fix is batching: move the loop across the boundary rather than crossing it per item.

## Unsafe

`unsafe` buys performance in exactly three situations: skipping a check the compiler cannot eliminate, a data structure the borrow checker cannot express, and FFI. It does not disable the borrow checker; it enables five specific operations.

There is one place where Rust is genuinely behind C: **there is no `restrict` equivalent for raw pointers.** `&mut T` implies `noalias` and that is a real advantage over unannotated C, but hand-written unsafe pointer code gets no aliasing guarantee at all and can optimize *worse* than the safe version it replaced.

Rules:

- Exhaust the safe alternatives first, and say which you tried.
- Keep the block as small as possible and wrap it in a safe API, the way `split_at_mut` wraps `slice::from_raw_parts_mut`.
- Every `unsafe fn` and every `unsafe` operation gets a `// SAFETY:` comment stating the invariant and why it holds.
- **Run Miri**: `rustup +nightly component add miri`, then `cargo +nightly miri test`. It catches dangling pointers, UB and provenance mistakes at runtime. If Miri finds something, there is a bug; if it finds nothing, that is not a proof – it only sees code that ran.
- Benchmark it. If the unsafe version is not measurably faster, delete it.
- Never downgrade an `assert!` to `debug_assert!` when it upholds an `unsafe` invariant.

Undefined behaviour is not "wrong output". Rust targets an abstract machine that exploits invalid bit patterns for niches, tracks pointer provenance, and assumes reads of uninitialized memory are impossible. Violating that contract can produce effects far from the violation site, only under certain builds, and passing tests prove nothing.

## The one place optimization is a bug

Constant-time cryptographic code deliberately gives up performance for timing invariance. `subtle` implements comparisons with bitwise operations and hides the inner value from the optimizer behind inline assembly or a volatile read specifically so LLVM cannot reintroduce a branch. Never "optimize" a constant-time routine, and never suggest branch hints or early exits there.
