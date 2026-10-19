# Build configuration

The cheapest wins in Rust require no code change. Start here, because it costs minutes and can be worth 10–20%+ before you have read a single function.

**Cargo only reads profile settings from the workspace-root `Cargo.toml`.** Profile sections inside dependencies are ignored. These settings are for binary crates and workspace roots.

## Maximum runtime speed

```toml
[profile.release]
lto = "fat"           # or "thin" – measure both
codegen-units = 1
panic = "abort"       # only if you never catch_unwind
```

| Setting | Effect | Cost |
|---|---|---|
| `lto = false` | Thin **local** LTO across codegen units in one crate. This is already the default at any opt-level > 0 | – |
| `lto = "thin"` | Cross-crate, parallelizes well. Roughly 80% of fat LTO's benefit at a fraction of the compile cost. The right default | Moderate compile time |
| `lto = "fat"` | Whole-program. Sometimes more runtime and smaller binaries, "but not always" | Slowest compile, poor parallelism |
| `lto = "off"` | Disables even thin-local. Slower code, bigger binary | Fastest compile |
| `codegen-units = 1` | Removes optimization barriers between units. Measured on rust-doom: `codegen-units=16` built ~10% faster and ran ~13% slower | Serializes compilation |
| `panic = "abort"` | No landing pads, no unwind tables. Measured on Cargo-as-a-library: 11% faster builds (18 s → 16 s), 13% smaller (15 MB → 13 MB) | No `catch_unwind`; backtrace still printed |
| `opt-level = 3` | Default for release: more inlining and vectorization than 2 | Bigger code, and **not reliably faster** |

**`opt-level = 3` is not a free upgrade over 2.** Measured on regex and quickersort: level 2 was a few percent faster on several benchmarks, the same on several more, slower on some, and built 1–3% quicker. Level 3 produced ~6.5% larger code on Servo's stylo. The rustc docs warn explicitly that 3 can be slower than 2 and that `"s"`/`"z"` are not necessarily smaller. Benchmark rather than assume the ladder is monotonic.

Rule of thumb from production use: services take `lto = "thin"`; performance-critical CLIs take `lto = "fat"` + `codegen-units = 1`.

## CPU-specific instructions

```
RUSTFLAGS="-C target-cpu=native" cargo build --release
```
or in `.cargo/config.toml`:
```toml
[build]
rustflags = ["-C", "target-cpu=native"]
```

Verify the features actually landed:
```
diff <(rustc --print cfg) <(rustc --print cfg -C target-cpu=native)
```

**`native` produces a binary that raises SIGILL on any older CPU.** This is not theoretical: documented failures include backtrace-rs builds and GitHub Actions runners where the builder was newer than the target. Note also that Cargo does not separate host and target RUSTFLAGS, so build scripts get the same flags.

The portable ladder is the x86-64 microarchitecture levels:

| Level | Adds | Practical meaning |
|---|---|---|
| `x86-64` | Baseline (~2003) | Runs anywhere |
| `x86-64-v2` | SSE4.2, popcnt | Anything from the last decade |
| `x86-64-v3` | AVX2, FMA, BMI | **The usual portable-fast choice** |
| `x86-64-v4` | AVX-512 | Breaks on a lot of current hardware, including many cloud instances |

For distributed binaries, `cargo-multivers` builds one version per level, stores the extras as compressed patches and picks at startup. For a library, do runtime dispatch instead: `is_x86_feature_detected!`, `#[target_feature]`, or the `multiversion` crate (dispatch overhead measured at ~0.3 ns, effectively free).

Some crates need explicit features to use SIMD at all – sonic-rs requires `target-cpu` flags.

## Alternative allocator

Often the single largest one-line win on allocation-heavy workloads.

```toml
[dependencies]
mimalloc = "0.1"
```
```rust
#[global_allocator]
static GLOBAL: mimalloc::MiMalloc = mimalloc::MiMalloc;
```

Measured on an Actix API: mimalloc 10,704 req/s on glibc and 9,312 on musl; jemalloc 9,981 and 8,872; both well ahead of the defaults. Under heavy multi-threaded load mimalloc measured 5.3x glibc malloc with roughly half the RSS.

jemalloc (`tikv-jemallocator`) is strong on Linux and macOS and can be pushed further with transparent huge pages:

```
MALLOC_CONF="thp:always,metadata_thp:always"
```

Other jemalloc knobs worth knowing when RSS matters more than throughput: `background_thread:true` (asynchronous purging on a decay timer), lower `narenas` (fewer arenas means less fragmentation but more lock contention), and shorter `dirty_decay_ms`/`muzzy_decay_ms` to return pages sooner.

snmalloc is worth trying on heavily multi-threaded workloads. All of them increase binary size and compile time.

**On musl this is mandatory, not optional.** musl's allocator is tuned for small static binaries, not concurrency. Measured: Alpine roughly 4x slower multi-threaded than a glibc image, a web benchmark dropping from 9,945 to 4,828 requests, and reports of 10–40x degradation on async workloads. With mimalloc the glibc/musl gap closes to about 13%. Teams pick Alpine for image size, skip the allocator swap, and then cannot explain why production is slower than their laptop.

## Containers and thread counts

`std::thread::available_parallelism()` respects `sched_getaffinity` and cgroup **cpusets**, but it can still overcount when the limit is a CFS **quota** (`cpu.cfs_quota_us`) rather than a cpuset, or when the cgroup filesystem is not readable.

The failure mode: Tokio sizes its worker pool from detected cores, Rayon does the same, and in a 2-CPU-quota pod you get double oversubscription plus CFS throttling. The symptom is latency spikes at low average CPU. Set worker counts explicitly from the actual budget. See `references/concurrency.md` for the worked case.

## Profile-guided optimization

Worth double-digit percentages on branch-heavy code (compilers, parsers, interpreters, query engines). Measured: up to 25% on DataFusion; Mozilla took 9% off Firefox compile times by PGO-building Clang. Setup cost is real, so do it once the code-level work is done.

```
cargo install cargo-pgo
rustup component add llvm-tools-preview

cargo pgo build              # instrumented build
./target/.../app <realistic workload>
cargo pgo optimize build     # rebuild using the profile
```

BOLT is a post-link optimizer stacked on top of PGO: `cargo pgo bolt build` then `cargo pgo bolt optimize`. It rearranges the linked binary's code layout from a sampling profile and needs `--emit-relocs`. BOLT-optimized Clang was 25% faster standalone, 12% on top of LTO+PGO. Expect less: at least one Rust project measured **no** BOLT gain on top of PGO. Linux only, since the profiling step needs `perf`. Not usable for binaries distributed through `cargo install`.

The training workload must resemble production. PGO trained on the wrong workload is a regression.

## Faster linking

No trade-off. If the platform supports a faster linker, use it.

| Linker | Platform | Note |
|---|---|---|
| lld | Linux, Windows | **Default on x86_64-unknown-linux-gnu since Rust 1.90.** Not yet default on Windows but works |
| mold | Linux | Usually faster than lld |
| wild | Linux | Rust-written, often fastest (rustc-driver: lld 1601 ms, mold 946 ms, wild 486 ms). No LTO support yet |
| system ld64 | macOS | Already fast, leave it |

```toml
[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=mold"]
```

Dynamic linking is not a build-speed fix. It moves work to load time, makes the loader find the library at startup, and defeats LTO.

## Minimum binary size

```toml
[profile.release]
opt-level = "z"       # or "s"
lto = true
codegen-units = 1
panic = "abort"
strip = true
```

`"s"` optimizes for size but still allows loop vectorization and more inlining; `"z"` is more aggressive and disables loop vectorization, which can be noticeably slower on compute-heavy code. Start at `"s"`.

Embedded warning: a machine-outliner regression from rustc 1.73 onward can corrupt the Link Register on Cortex-M at `opt-level = "z"`. Use `"s"` if you hit it.

Then measure what is left:
```
cargo bloat --release --crates      # biggest dependencies
cargo bloat --release -n 20         # biggest functions
```
Crate attribution for generic functions is a heuristic – drill into individual functions before believing it. For WASM use `twiggy`.

Beyond that, in order: drop default features, replace heavy dependencies, use `dyn` on cold paths to cut monomorphization, `no_std`, and finally `-Z build-std` with `-C panic=immediate-abort`, which strips `core::fmt` and gets a stripped binary to roughly 30 KB.

For WASM, run `wasm-opt -Oz` after `wasm-bindgen` – typically another 10–30% on top of the compiler flags, plus tree-shaking that LTO alone does not do. Browsers stream-compile, so download size is startup latency.

Debug info does not need stripping from release builds: it is not generated for local release builds by default, and std's has been stripped since 1.77.

## Compile times

Fastest wins first:

1. **Faster linker** (above). A large share of an incremental rebuild is linking.
2. **Fewer dependencies, fewer features.** `default-features = false` plus only what you use. `cargo tree -d` finds duplicate versions; `cargo tree -e features` shows who enabled what. Cargo builds each shared dependency with the **union** of all requested features, which is a common source of unexplained bloat – `resolver = "2"` at least stops build/dev/target features leaking into the normal build.
3. `cargo build --timings` – an HTML Gantt chart of the crate graph. It shows which crates serialize the build.
4. **Split into workspace crates** so a change recompiles one crate, not everything. One case study reports 9x on incremental changes; Feldera cut 30 minutes to 2 by generating many small crates. Limits: no circular dependencies, and splitting a tightly coupled crate is genuinely hard.
5. ```toml
   [profile.dev]
   debug = false                 # 20–40% faster dev builds
   # or debug = "line-tables-only" to keep line numbers
   ```
6. ```toml
   [profile.dev.package."*"]
   opt-level = 2                 # optimize dependencies, not your crate
   ```
   Sharp edge: generic code is codegen'd in the crate that **instantiates** it, so it picks up that crate's settings. Profile overrides do not do what you expect for generics.
7. ```toml
   [profile.dev.build-override]
   opt-level = 3                 # build scripts default to 0, which makes bindgen crawl
   ```
8. `sccache` for a shared compilation cache – 50–80% off rebuilds. With S3/GCS every runner sharing the bucket benefits; set `SCCACHE_BASEDIRS` when checkout paths differ. Watch for network latency eating the savings on incremental builds with many small artifacts.
9. `cargo nextest run` instead of `cargo test`: a process per test rather than threads in one process, up to 3x faster, and the gain grows with the number of test binaries because `cargo test` runs binaries serially.
10. `CARGO_INCREMENTAL=0` in CI, where nothing is incremental anyway.
11. Nightly: `-Zthreads=8` parallel frontend (20–30% typical, up to 50% on real code, more on debug builds than release); Cranelift for dev builds only (~20% less codegen time, about 5% of a clean build):
    ```toml
    [unstable]
    codegen-backend = true
    [profile.dev]
    codegen-backend = "cranelift"
    ```

### Docker and CI

`cargo-chef` separates dependency compilation from source compilation so Docker layer caching works: `cargo chef prepare` then `cargo chef cook`. Measured 5x on a ~14k-line, ~500-dependency codebase (10 minutes to 2). Combine with BuildKit cache mounts; add sccache only if you need cross-machine sharing. None of it helps unless the cache is actually persisted between builds.

### Code-level compile time

- **`cargo llvm-lines`** reports LLVM IR lines and **instantiation counts** per generic function. Run it as `CARGO_PROFILE_RELEASE_LTO=fat cargo llvm-lines --release` to see across dependencies, since LTO moves codegen into the root crate. The usual offenders are `Vec::push`, `Option::map` and `Result::map_err` instantiated hundreds of times.

  Fix by moving the non-generic body into an inner function instantiated once:
  ```rust
  pub fn read<P: AsRef<Path>>(path: P) -> io::Result<Vec<u8>> {
      fn inner(path: &Path) -> io::Result<Vec<u8>> { /* real work, compiled once */ }
      inner(path.as_ref())
  }
  ```
  This is what `std::fs::read` does. It shrinks the binary too. Replacing hot combinators with plain `match` has the same effect.

- **Proc macros cost four separate things**, and people usually only count the first: compiling the macro crate, compiling its dependencies (`proc-macro2` → `quote` → `syn` is 20+ seconds cold), running the expansions, and **compiling the generated code**. The last one is usually the largest. Use `cargo expand` to see the output and nightly `-Zmacro-stats` to see how much each macro generates. A macro producing as much code as your hand-written source is worth replacing. Feature-gate `derive` where you can.

- **`build.rs` re-runs whenever any file in the package changes** unless you emit `cargo::rerun-if-changed=build.rs`. That one line is a large saving in a big crate. bindgen is slow enough to break rust-analyzer responsiveness – check the generated bindings into the repo and regenerate deliberately. Avoid CMake, autotools and pkg-config in build scripts; they serialize the build.

- **Incremental compilation** uses a red-green algorithm over a query graph: a node is green if all its dependencies are green, and if a dependency went red the query re-runs and its **result is hashed** – unchanged hash means the node goes green anyway and invalidation stops there. That recovery from false positives is why a trivial edit to a widely used item can still be cheap, and why a change to proc-macro output can be catastrophic.

- **rust-analyzer** is a real cost on large workspaces: 12–13 GB at around 800 dependencies is normal. `rust-analyzer.cachePriming.enable = false` cuts startup RAM; lowering `lru.capacity` helps a little. `rust-analyzer analysis-stats <dir>` shows where it goes.

## cargo-wizard

`cargo install cargo-wizard` applies vetted presets so you do not have to remember the table above:

```
cargo wizard apply fast-runtime release
cargo wizard apply fast-compile dev
cargo wizard apply min-size release
```

`fast-compile` also enables Cranelift and the parallel frontend on nightly. Treat the output as a starting point and benchmark each setting one at a time.

## Nightly and unstable

- `-Z build-std` (needs the `rust-src` component) recompiles std with your profile and flags. Required when a flag must apply to std too, or for size-optimized no_std targets.
- `-Zprint-type-sizes` prints size, alignment, per-variant size and padding for every type in the crate. See `memory.md`.
- `-Zmacro-stats`, `-Zthreads=N`, `-Zcodegen-backend=cranelift` as above.
- **`-Zmir-opt-level` above the default has caused miscompilations** (several open issues). Do not ship with it.

Never leave a project on nightly for a performance flag without telling the user what it costs them.
