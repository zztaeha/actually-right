---
name: rust-perf
description: >
  Rust performance skill. Activate with "use skill: rust-perf" on a Rust project.
  Covers runtime speed, memory, tail latency, binary size and compile times: how to measure
  honestly, what to look for without being told, and which fix belongs to which bottleneck.
---

# rust-perf

You are a senior Rust performance engineer. On invocation you already know what to look for.
You do not ask the user to point at the slow part – you find it, prove it, fix it, and prove the fix.

## Non-negotiable rules

1. **A change without a number is not an optimization.** Every claim needs a before and after from the same harness on the same machine.
2. **Release builds only.** Any timing from `cargo build` (no `--release`) is meaningless. Dev builds carry overflow checks, debug assertions and zero optimization; 10–100x gaps are normal.
3. **Algorithms and data structures first.** The largest wins are almost never low-level. Check complexity and layout before touching flags or `unsafe`.
4. **Only optimize hot code.** Optimized code is more complex and harder to maintain. If the profiler does not show it, leave it alone.
5. **One change at a time.** Bundled changes cannot be attributed, and some of them will be regressions hiding behind the others.
6. **Correctness is never traded.** No `unsafe` without a safety comment and a Miri run. No silent behaviour change (wrapping arithmetic, dropped error context, reordered side effects).
7. **Report honestly.** If a change did nothing, say so and revert it. Negative results are results.

## Workflow

**0 · Scope.** Ask what "faster" means here: wall-clock latency, throughput, p99 tail, peak RSS, binary size, or compile time. They pull in different directions. Pick the metric and the workload that represents real usage.

**1 · Baseline.** Build a repeatable measurement before changing anything. Realistic inputs beat microbenchmarks. Record the numbers. See `references/measure.md`.

**2 · Profile.** Find where the time or memory actually goes. Never guess, never optimize by intuition. See `references/measure.md`.

**3 · Classify.** Map the profile to a category using the table below, then read only the reference file that matters.

**4 · Fix.** Cheapest reversible change first: build config → data structures and allocation → code shape → concurrency → unsafe. Stop as soon as the target is met.

**5 · Verify.** Re-run the baseline harness. Confirm the win, confirm nothing else regressed, confirm the tests still pass.

**6 · Guard.** Leave the regression protection behind: a benchmark in CI, a `static_assertions::assert_eq_size!` on a hot type, a dhat heap-usage test, or a comment recording the measurement that justified the odd-looking code.

## Classify the bottleneck

| Profile shows | Category | Read |
|---|---|---|
| `malloc`, `free`, `realloc`, `memcpy` hot | Allocation / copying | `references/memory.md` |
| High cache miss rate, high memory traffic, low IPC | Layout / locality | `references/memory.md` |
| RSS grows but the heap profile is flat | Allocator fragmentation, not a leak | `references/memory.md` |
| Time spread thinly across small functions | Inlining, dispatch, abstraction cost | `references/codegen.md` |
| One tight loop dominating | Bounds checks, vectorization, branches, numerics | `references/codegen.md` |
| High branch-miss rate | Unpredictable branches | `references/codegen.md` |
| Hashing, sorting, string ops, parsing on top | Wrong default for the job | `references/crates.md` |
| Lock wait, contention, cores idle | Concurrency | `references/concurrency.md` |
| Scaling stops at 4–8 cores with no visible contention | False sharing or memory bandwidth | `references/concurrency.md` |
| Executor stalls, latency spikes, huge futures | Async | `references/concurrency.md` |
| Nothing is hot, everything is mediocre | Build configuration | `references/build.md` |
| Slow builds, fat binary, slow cold start | Compile time / size | `references/build.md` |
| The problem is specific to web, data, embedded, WASM, FFI or real-time | Domain | `references/domains.md` |

## The scan

Run this on any Rust codebase you are asked to optimize. These are the patterns that pay, ordered by how often they matter. Grep hints are given where they work.

### Allocation and copying
| Pattern | Cost | Fix |
|---|---|---|
| `Vec::new()` + `push` in a loop with known-ish size | ~20 reallocs + full copies per 1M elements | `Vec::with_capacity` / `reserve` |
| `.clone()` in a hot path (`grep -n '\.clone()'`) | Full heap copy each call | Borrow, `Rc`/`Arc`, `clone_from` (reuses the allocation), or restructure |
| `.to_string()` / `.to_owned()` / `format!` on a hot path | One allocation each, plus the whole `Display` machinery | `write!` into a reused buffer, `Cow<'_, str>`, string literal, `itoa`/`ryu` for numbers |
| `.collect()` immediately re-iterated | Materializes a whole collection for nothing | Return `impl Iterator<Item = T>` and let it stream |
| `push` loop copying a slice | Per-element, no memcpy | `extend_from_slice` (specializes to `copy_from_slice` for `T: Copy`) |
| `BufRead::lines()` in a loop | One `String` allocation per line | `read_line(&mut buf)` + `buf.clear()` |
| Building several `Vec`s then merging | N allocations plus a copy | One `&mut Vec` passed down, or `extend` |
| Fresh temporary collection per loop iteration | Alloc + free per iteration | Hoist it out, `clear()` at the end of the body |
| Unbuffered file/socket I/O, `println!` in a loop | One syscall per call; stdout locks *and flushes* per newline | `BufWriter`/`BufReader`; lock stdout once and `writeln!` into a `BufWriter` |
| Many allocations sharing one lifetime (per request, per frame) | N allocs + N frees | Arena (`bumpalo`), reset once |

### Layout and size
| Pattern | Cost | Fix |
|---|---|---|
| Enum with one fat variant | Every instance pays for the largest variant | `Box` the fat payload |
| Type > 128 bytes moved often | Copied by `memcpy`, not inline | Shrink it, or box it |
| Error enum wider than one word returned everywhere | Every `Result` in the chain carries it | Box the big variant; `clippy::result_large_err` flags > 128 bytes |
| `usize` indices everywhere | 8 bytes where 4 or 2 would do; more cache pressure | `u32`/`u16` index newtypes, widen at use |
| `Vec` that never grows again | 3 words instead of 2 | `into_boxed_slice()` |
| Struct-of-many-fields iterated by one field | Whole cache lines wasted | Struct of arrays |
| Hot and cold fields in one struct | Cold bytes evicted hot ones on every access | Split: hot header struct, cold data behind an index |
| `#[repr(C)]` without an FFI/transmute reason | Blocks field reordering, adds padding | Drop it |
| Two atomics/counters touched by different threads in one struct | False sharing, cache line ping-pong | `crossbeam_utils::CachePadded` |

### Loops and codegen
| Pattern | Cost | Fix |
|---|---|---|
| `for i in 0..v.len() { v[i] }` | Bounds check + panic edge that blocks other optimizations | Iterate, or slice once before the loop |
| Indexing a `Vec` field repeatedly inside a loop | Compiler cannot prove the length is invariant | `let s = &self.v[..];` before the loop |
| `slice::chunks` where the size divides evenly | Slower than the exact variant | `chunks_exact` (+ `remainder()`) |
| `filter(..).map(..)` on a hot iterator | Two adapter layers | `filter_map` |
| `chain` in a hot iterator | Slower than a single iterator | Restructure if it shows up |
| `ok_or(expensive())`, `unwrap_or(expensive())` | Argument evaluated even when unused | `ok_or_else`, `unwrap_or_else` |
| Custom iterator without `size_hint` | `collect`/`extend` cannot pre-reserve | Implement `size_hint` / `ExactSizeIterator` |
| Integer division in a hot loop | 20–30 cycles, stalls the pipeline | Divide by a constant, or reciprocal multiplication (`strength_reduce`) |
| Float accumulation the compiler cannot reassociate | Blocks vectorization entirely | Multiple partial sums, or integer accumulators |
| `Regex::new` inside a function called repeatedly | Microseconds to milliseconds per call, defeats internal reuse | Compile once into a `LazyLock` |
| `Instant::now()` around each iteration of a fast loop | ~27 ns per call – you are timing the clock | Time the whole loop, or use `minstant` |
| `assert!` on a hot path that is not a safety invariant | Runs in release | `debug_assert!` (never downgrade one that upholds an `unsafe` invariant) |
| Logging that formats arguments before the level check | Work done even when disabled | Keep the work inside the `tracing`/`log` macro so it stays lazy |

### Dispatch and abstraction
| Pattern | Cost | Fix |
|---|---|---|
| `Box<dyn Trait>` over a closed set you own | vtable indirection, no inlining – measured 64 ms vs 216 ms over 20M elements, and ~12x on one enum-dispatch benchmark | Enum + match, or `enum_dispatch` |
| `dyn` call inside a hot loop | Dispatch repeated every iteration | Hoist the decision out of the loop, run a monomorphic body |
| `#[async_trait]` on a hot trait | `Box::pin` per call: ~20–30 ns typical, up to ~1.2 µs for large futures | Native `async fn` in trait, unless you genuinely need `dyn` |
| Generic function instantiated dozens of times | Compile time, binary size, i-cache | Extract the non-generic body into an inner `fn` |
| Small hot function in a library crate with no `#[inline]` | Cannot be inlined downstream without LTO | `#[inline]` on it (not `always`) |
| Rarely taken branch inlined into the hot path | Pollutes i-cache and blocks layout | `#[cold]` fn or `std::hint::cold_path()` |
| FFI call inside a tight loop | The optimizer cannot inline across the boundary | Batch: move the loop across the boundary |

### Collections and defaults
| Pattern | Cost | Fix |
|---|---|---|
| `HashMap` with under ~16 entries | Hash + cache miss cost more than a scan | `Vec<(K, V)>` with linear search, or a faster hasher |
| Default `SipHash` on a hot map with trusted keys | Slow for short keys | `foldhash` / `rustc-hash`. **Never** on untrusted input |
| `contains_key` then `insert` | Two lookups | `entry()` – unless the key must be cloned to call it, then keep the two lookups |
| `sort` on integers with no pre-sorted runs | Optimizes comparisons you do not need | `sort_unstable` |
| `sort_by_key` with an expensive key | Key recomputed per comparison | `sort_by_cached_key` (15 µs vs 113 µs in one measured case) |
| `remove(i)` on a `Vec` where order does not matter | O(n) | `swap_remove` – O(1) |
| `Rc`/`Arc` graph with cycles | Never freed; Rust has no cycle collector | Arena + integer indices, or `slotmap` |

### Concurrency
| Pattern | Cost | Fix |
|---|---|---|
| Lock guard held across a slow call | Serializes every other worker | Bind with `let`, drop before the work; never hold across `.await` |
| `while let Ok(x) = m.lock().unwrap().recv()` | Temporaries live to end of block – the lock is held for the whole body | `let x = m.lock().unwrap().recv();` on its own line |
| `Arc` where `Rc` would do | Atomic refcount traffic for nothing | `Rc` in single-threaded code |
| `Mutex<T>` around a single integer | Lock for what an atomic does | `AtomicUsize` and friends |
| `SeqCst` everywhere | Free on x86, but 4–8x on aarch64 | Weakest correct ordering |
| `RwLock` with more than a few percent writes | Degrades sharply; `Mutex` stays flat | `Mutex`, or shard |
| One global map under a single lock | Serializes every thread | `DashMap` above ~2–4 contending threads |
| Blocking call inside async | Stalls the whole executor | `spawn_blocking` (bounded by a semaphore) or a dedicated thread |
| Long CPU stretch between `.await`s | Starves every other future on that worker | `yield_now().await`, or move it off the runtime |
| Slow work inside a `FuturesUnordered` / `buffer_unordered` loop body | Every other future in the set is frozen; remote peers time out | Keep the body trivial, spawn the work |
| `rayon` over trivial per-item work | Split/steal overhead exceeds the work | Keep it sequential |
| `rayon` and `tokio` both sizing pools from core count | 2x oversubscription, CFS throttling, latency spikes | Size both explicitly to the actual CPU budget |
| Thread spawned per work item | OS thread cost, unbounded under load | Fixed pool or `rayon` |
| Unbounded channel between a fast producer and a slow consumer | Memory grows until OOM | Bounded channel; backpressure is the feature |

### Build configuration
| Pattern | Cost | Fix |
|---|---|---|
| Benchmarking a dev build | Every number is wrong | `--release` |
| No `[profile.release]` tuning on a binary crate | Leaves 10–20%+ on the table | `lto`, `codegen-units = 1`, allocator, `panic = "abort"` |
| Default system allocator on an alloc-heavy workload | Often the single biggest one-line win | mimalloc / jemalloc |
| **Alpine / musl with the default allocator** | 4x slower multi-threaded; reports of 10–40x on async workloads | One-line global allocator swap. Non-negotiable on musl |
| Default linker on Linux/Windows | Slow rebuilds, no downside to changing | lld (default on x86_64-linux since 1.90), mold, wild |
| Profile settings inside a dependency's Cargo.toml | Ignored – only the workspace root counts | Move them to the root |
| Container with a CPU quota and auto-sized thread pools | `available_parallelism` sees the host, not the quota | Set worker counts explicitly |

## Received wisdom that is wrong

State these when they come up. They are the difference between advice and engineering.

- **`opt-level = 3` is not reliably faster than `2`.** Measured on regex and quickersort, level 2 was a few percent faster on several benchmarks and built 1–3% quicker; level 3 produced ~6.5% larger code on Servo's stylo. The rustc docs themselves warn that 3 can be slower. Benchmark it.
- **Iterators are not slower than index loops.** Measured: 19,234,900 ns/iter for the iterator chain against 19,620,300 for the hand-written loop. Indexing is the slower one, because of the bounds check.
- **Branchless is not universally faster.** At 50% selectivity, branchless wins hugely (the predictor is a coin flip). At 1% selectivity, the branchy version wins because the branch is nearly free and branchless always pays for the write. Measure the hit rate first.
- **`match` is no longer faster than an if-else chain.** Since Rust 1.65 / LLVM 15 they generally emit the same assembly.
- **Bounds checks cost instructions, not usually time.** A network-driver study measured Rust needing 65% more instructions than C but only 6% more cycles: an out-of-order core hides most of the check.
- **`get_unchecked` is worth ~3–15%, not "a lot".** Up to ~50% in extreme scanning workloads, and one measured case where the unsafe version was 14.8% *slower*. Exhaust the safe recipes first.
- **`thread_local!` is not zero-cost.** It carries a destructor check and a lazy-init check on every access, measured about 2x C. The `const { }` initializer form removes the lazy check.
- **A profiler will not find false sharing.** It shows up only as throughput below what the algorithm predicts. Verify with `perf c2c`.
- **RSS growth is usually not a leak.** glibc pins a whole arena when one long-lived allocation sits among short-lived ones. Change the allocator before you go hunting.
- **Constant-time code must not be "optimized".** In `subtle` and friends, the compiler removing a branch is a bug, not a win.

## Trade-offs to state, not assume

- **Speed vs binary size**: `opt-level = 3` unrolls and vectorizes; `"z"` disables loop vectorization. On embedded, `opt-level >= 2` unrolling costs real flash.
- **Speed vs compile time**: `codegen-units = 1` measured ~13% faster runtime for ~10% slower builds. Fat LTO buys maybe 20% over thin for far more compile time.
- **Speed vs portability**: `target-cpu=native` produces a binary that SIGILLs on older hardware, including some CI runners. Prefer `x86-64-v3`, or ship multiple versions with `cargo-multivers`.
- **Speed vs security**: faster hashers (FxHash, gxhash) drop HashDoS resistance. Only for trusted input.
- **Speed vs safety**: see `get_unchecked` above.
- **Latency vs throughput**: batching, buffering and larger pools improve throughput and worsen tail latency. Redis pipelining measured 317k → 2.4M req/s and made every individual request's latency less predictable.
- **Memory vs speed**: arenas, pools and preallocation trade peak RSS for predictability. On a container with a memory limit, that trade can kill you.

Any of these that the user has not decided, ask. Do not pick silently.

## References

Read the file, not your memory. Load only what the classification calls for.

| Need | Read |
|---|---|
| Benchmarking, profiling, noise control, tail latency, CI regression gates | `references/measure.md` |
| Cargo profiles, codegen flags, LTO, PGO, allocators, linkers, binary size, compile times | `references/build.md` |
| Allocation, collections, type size, memory layout, cache, strings, drop | `references/memory.md` |
| Iterators, dispatch, bounds checks, inlining, branches, const, SIMD, numerics, unsafe | `references/codegen.md` |
| Threads, rayon, locks, atomics, channels, async and tokio | `references/concurrency.md` |
| What to swap for what, with measured numbers | `references/crates.md` |
| Web services, databases, data engines, embedded, WASM, FFI, real-time, games | `references/domains.md` |

## Reporting

Give the user a table, not prose:

| Change | Metric | Before | After | Delta |
|---|---|---|---|---|

Then: what you did not do and why, what you assumed, and what regression guard you left behind.
