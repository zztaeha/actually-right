# Measuring

Everything in this skill depends on this file being applied first.

## Pick the metric

| Goal | Metric | Tool |
|---|---|---|
| "It feels slow" | Wall-clock on a realistic workload | Hyperfine, Criterion |
| Throughput | Ops/sec at a fixed concurrency | Criterion, load generator |
| Tail latency | p50 / p99 / p99.9, never the mean | HdrHistogram in-process, or the load generator |
| Memory | Peak RSS **and** allocation count | dhat, heaptrack, bytehound |
| Binary size | Bytes per crate and per function | cargo-bloat (twiggy for WASM) |
| Build speed | Per-crate compile time | `cargo build --timings` |
| CI-safe comparison | Instruction counts, not time | iai-callgrind |

Wall-clock matches what users feel but has high variance – tiny memory-layout shifts cause real but ephemeral swings. Instruction counts and cycles are far more stable when you need to detect a 1% change.

**Report p50, p99 and p99.9.** An average hides exactly the thing you are usually trying to fix.

## Harnesses

**Criterion** – the default on stable. Statistical analysis, HTML reports with plots, built-in regression detection between runs.

```rust
// benches/bench.rs, with [[bench]] name = "bench", harness = false
use criterion::{criterion_group, criterion_main, Criterion, black_box};

fn bench(c: &mut Criterion) {
    let input = build_realistic_input();
    c.bench_function("parse", |b| b.iter(|| parse(black_box(&input))));
}
criterion_group!(benches, bench);
criterion_main!(benches);
```

**Divan** – lower overhead, attribute-based, benchmarks generic functions, and its `AllocProfiler` counts allocations and bytes per benchmark, which Criterion cannot do. Reach for it when allocation count is the thing you are tracking.

**Hyperfine** – for whole binaries. Ten runs by default, discarded warm-up runs, min/max/mean/median/stddev, outlier detection, markdown/CSV/JSON export.

```
hyperfine --warmup 3 './target/release/app old.json' './target/release/app new.json'
```

It runs commands through a shell and subtracts the shell's startup time. For very fast programs pass `--shell=None` so that subtraction is not most of your measurement.

**iai-callgrind** – Valgrind Callgrind under the hood. Counts instructions, cache hits/misses and estimated cycles. Each benchmark runs once. **This is the tool for CI**: it is immune to noisy shared runners and its numbers are comparable across machines. It can also drive Cachegrind or DHAT.

**Built-in `#[bench]`** is nightly-only. Do not build on it.

### black_box

Benchmarks are artificial and the optimizer knows it. `black_box` defeats **dead code elimination, constant folding and loop-invariant hoisting**.

- Wrap the **input** so the compiler cannot constant-fold through it.
- Wrap the **output** so the call is not dead code.

What it does **not** defeat: caching, prefetching, branch prediction, out-of-order execution. It affects the compiler, not the CPU. Where you place it inside a loop changes what you measure – a loop that only writes to memory once at the end generates almost no cache-coherency traffic, which may or may not be what you meant to measure.

A benchmark that got 100x faster after a "small" change is usually a benchmark that got deleted by the optimizer. Check the asm.

### The relative-difference rule

If you are measuring a relative difference rather than an absolute number, **do not measure nanoseconds**. Loop the work until the difference is visible to `/usr/bin/time`. Nanosecond-scale measurement of a 5% effect is mostly measuring your timer: `Instant::now()` itself costs about 27 ns (`clock_gettime` through the vDSO, of which the vDSO call is ~15.5 ns). `minstant` gets that to ~10.5 ns using the TSC on x86; `quanta` is ~31.5 ns.

## Noise control for wall-clock

In descending order of effect:

1. Build with `--release`, and with the same profile you will ship.
2. Close everything else. Never benchmark wall-clock on CI.
3. Disable turbo/boost.
4. Disable SMT/hyper-threading (one measured case: coefficient of variation improved 3x, down to 0.26%).
5. Pin the CPU frequency (`cpupower frequency-set`) to a fixed medium-low value so thermal behaviour does not drift mid-run.
6. Pin the process to a core (`taskset -c 2`). `isolcpus` removes cores from the scheduler entirely, which is too blunt for multi-threaded programs – use `chrt` to change the scheduling class instead.
7. Interleave A/B runs rather than running all of A then all of B.

Teams that do this routinely cut benchmark variance from 20–30% to under 5%. If you cannot control the machine, switch metrics to instruction counts.

## Tail latency and coordinated omission

If you sample latency on a fixed schedule and the system stalls, the samples that would have been slow never get taken. Your p99 is then a lie, and the worse the stall the better the number looks.

```rust
let mut hist = hdrhistogram::Histogram::<u64>::new(3)?;
hist.record_correct(elapsed_us, expected_interval_us)?;  // synthesizes the missing samples
```

`hdrhistogram` also offers a post-hoc correction; the two are mutually exclusive. `wrk2` exists specifically to generate load at a constant rate for this reason. If your load generator ramps down when the server slows, its latency numbers are worthless.

For HTTP: `oha` for quick throughput and percentile checks with a live TUI, `k6` when you need scripted multi-step scenarios.

## Profiling

Give the profiler symbols, or the profile is a wall of addresses. Use a dedicated profile so you never ship debug info by accident:

```toml
[profile.profiling]
inherits = "release"
debug = true          # or "line-tables-only"
```

Add `-C force-frame-pointers=yes`. This is not a small detail: one profiling run took **over 8 minutes** to post-process with DWARF unwinding and **under a second** with frame pointers, because eBPF and perf can walk frame pointers in-kernel instead of calling `addr2line` afterwards.

Use `-C symbol-mangling-version=v0` if the profiler mangles names badly; `rustfilt` demangles after the fact.

| Tool | Platform | Use for |
|---|---|---|
| **samply** | macOS, Linux, Windows | First choice. `cargo install --locked samply`, `samply record ./target/release/app`, opens in Firefox Profiler |
| perf | Linux | Hardware counters, `perf stat` for cache/branch summaries, `perf c2c` for false sharing |
| cargo-flamegraph | Linux + DTrace platforms | Quick flame graph without leaving cargo |
| cargo-instruments | macOS | Wraps Xcode Instruments: Time Profiler, Allocations, Leaks |
| VTune / μProf | Linux, Windows | Microarchitectural detail |
| Cachegrind / Callgrind | Linux | Per-line instruction counts, simulated cache and branch data |
| dhat | Anywhere | Allocation sites, rates, lifetimes, peak, hot `memcpy` |
| heaptrack / bytehound | Linux | Whole-process heap profiling. heaptrack costs ~1.5x runtime; bytehound is lighter and streams |
| Coz | Linux | Causal profiling: which speedup would actually help |
| Tracy / puffin | Anywhere | Frame-based instrumented profiling for games and UI |
| hotpath | Anywhere | Per-function time and allocations, lock wait/hold time, channel state. Good for async |

**perf specifics.** `--call-graph fp` needs frame pointers, `dwarf` is accurate but slow (default 8192-byte stack dumps; `--call-graph dwarf,4096` shrinks them), `lbr` is fast on Haswell and later. Sample at `-F99` – 99 Hz avoids falling into lockstep with periodic system activity.

**macOS constraints.** There is no `perf`. SIP blocks DTrace syscall tracing and DTrace needs root. Use `samply` or `cargo-instruments`.

**Instrumented profiling.** The `profiling` crate is a thin abstraction over puffin, optick, tracy and superluminal: annotate with `profiling::scope!("name")` or `#[profiling::function]` and pick the backend at build time. Puffin scopes cost about 60 ns on an M1, so they are cheap enough to leave in behind a feature flag.

Std ships without debug info, so std frames stay opaque. Building your own toolchain with `debuginfo-level = 1`, or unstable `build-std`, fixes it if you truly need to see inside std.

### Reading a profile

- `malloc`/`free`/`realloc` near the top → allocation rate. Go to `memory.md`.
- `memcpy` near the top → types over 128 bytes being moved, or collection growth. Go to `memory.md`.
- `core::ptr::drop_in_place` significant → destructor cost on large structures.
- Everything flat and nothing above a few percent → abstraction overhead or a build-config problem, not a hotspot.
- Time inside a lock's `lock`/`park` → contention, not computation.
- High `sys` time → syscalls. Confirm with `strace -c` (or `-fc`) before doing anything else; it is the fastest way to find an unbuffered writer or a missing connection pool.

Flame graph vs icicle graph is the same data drawn in opposite directions. Icicle puts the root at the top so the entry point is always on screen. Width is always the share of samples; the horizontal axis is **not** time order.

### Cache, branches and the ceiling

```
valgrind --tool=cachegrind --branch-sim=yes ./target/release/app
perf stat -e cache-misses,cache-references,branch-misses,branches,instructions,cycles ./target/release/app
```

Costs to reason with: L1 miss ≈ 10 cycles, last-level miss up to ≈ 200 cycles, mispredicted branch 10–20 cycles (Skylake 16–17, Ice Lake 17–21, Zen 1–2 ~19). A miss rate that looks small can dominate if the loop count is large.

Classify with the roofline model: arithmetic intensity is useful operations per byte moved from memory; the ridge point is peak FLOP/s divided by peak GB/s. Below the ridge you are **memory-bound** and only data movement and layout will help. Above it you are **compute-bound** and only the algorithm will help.

A single thread cannot saturate the memory bus, and **4–6 cores usually saturate one memory domain**. A memory-bound workload that stops scaling at six cores is not broken; it is finished.

### Memory

```rust
#[global_allocator]
static ALLOC: dhat::Alloc = dhat::Alloc;

fn main() {
    let _profiler = dhat::Profiler::new_heap(); // writes dhat-heap.json on drop
}
```

dhat reports:

- `total_blocks` / `total_bytes` – allocations over the whole run. High counts with low peak means churn, not size.
- `max_blocks` / `max_bytes` – state at the peak ("At t-gmax"). This is what to shrink if RSS is the problem.
- `curr_blocks` / `curr_bytes` – still live at exit ("At t-end"). This is what leaked.

Experience on rustc: cutting 10 allocations per million instructions was worth roughly 1%.

**Heap profilers show live allocations, not RSS.** Allocator-reserved memory counts toward RSS and never appears in a heap profile. If dhat says the heap is flat and RSS keeps climbing, the problem is the allocator, not your code – see `memory.md`.

dhat-rs also supports assertions, which is how you stop a regression coming back:

```rust
let stats = dhat::HeapStats::get();
dhat::assert_eq!(stats.total_blocks, 96);
dhat::assert!(stats.max_bytes < 10 * 1024 * 1024);
```

## Ad hoc profiling

When no profiler answers the question, instrument directly: `eprintln!` the value you care about (vector lengths at a hot `push`, branch taken counts, collection sizes) and post-process the frequencies. This is how you learn that 99% of your vectors hold 0 or 1 elements – which is the fact that unlocks the real fix.

## Continuous profiling

For a production service, sampling profilers based on eBPF cost under 1% and need no code changes for native binaries. Parca Agent samples 19 times per second per logical CPU; Pyroscope (now Grafana) is the other mature option. The argument that matters: when the incident happens, the profile is already there.

## Guarding against regressions

- **Bencher** wraps your existing harness, tracks results over time, applies statistical thresholds and alerts on regressions.
- **CodSpeed** isolates performance data to get variance under 1% and can block merges that degrade speed. It supports Criterion and Divan.
- Track **instruction counts** in CI (iai-callgrind), not wall-clock. Wall-clock on a shared runner measures the runner.
- `cargo build --timings` in CI catches compile-time regressions.
- `static_assertions::assert_eq_size!(HotType, [u8; 64])` behind `#[cfg(target_arch = "x86_64")]` catches type-size regressions.

**Regressions also arrive from upstream.** Documented rustc cases: 1.71 → 1.72 took one async-heavy server's compile time from under 5 s to 30 minutes; 1.81 → 1.82 made a compression routine ~60% slower; 1.86 → 1.87 made an auto-vectorized function 60% slower. Pin the toolchain in CI, and when a regression has no corresponding code change, bisect against rustc.

## Honesty checklist

Before claiming a win:

- Same machine, same profile, same input, same load.
- More than one run; report the spread, not just the best.
- Confirm the benchmark still does the work (check `black_box`, check the output is used).
- Confirm the tests still pass.
- Confirm you did not shift cost to another metric: check binary size, peak RSS, tail latency and compile time too.
