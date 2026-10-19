# Domains

Domain-specific knowledge that would otherwise bloat the general files. Read only the section that matches the project.

## Web services

**The framework is almost never the bottleneck.** actix-web leads raw throughput by roughly 10–15% over axum (TechEmpower-class numbers: ~720k vs ~640k req/s JSON, ~980k vs ~870k plaintext, p99 1.2 ms vs 1.4 ms), and axum uses less memory and integrates better with the Tokio/Tower ecosystem. Both are far past the point where the database and serialization dominate.

Where the time actually goes, in order:

1. **Connection pooling.** Repeatedly described as the single biggest throughput win. It amortizes the TCP handshake, TLS negotiation and per-connection allocation, and critically keeps the **per-connection prepared-statement plan cache** warm. `deadpool` is the async default, `bb8` the other option, `r2d2` for synchronous drivers. Tune `max_size` against the server's own connection limit, not upwards until it stops erroring. `pool_max_idle_per_host` on an HTTP client should start at the backend's worker count.
2. **Serialization.** See `crates.md`. Swapping `serde_json` for `sonic-rs` is often larger than anything you will do to handler code.
3. **The allocator.** Especially on musl. See `build.md`.
4. **Runtime sizing under a CPU quota.** See `concurrency.md`.

`TCP_NODELAY` (`set_nodelay(true)`) disables Nagle and matters for request/response latency; it is irrelevant for bulk transfer. Leaving it off interacts badly with delayed ACK.

`write_vectored` / `writev` passes several non-contiguous buffers to the kernel in one syscall instead of coalescing in user space or making N syscalls.

**HTTP clients** measure within about 1% of each other (hyper, reqwest, ureq). The client is not your problem; the pool configuration is.

**TLS**: rustls beats OpenSSL on handshakes (10% faster server-side setup, 20–40% client-side, up to 2.06x client throughput on RSA) but **loses on bulk transfer** – 0.3–0.6x with AES, ~45% less with ChaCha20 – because OpenSSL's primitives use AVX-512. Disable AVX-512 and they match. Choose by whether your workload is handshake-heavy or byte-heavy.

**gRPC**: `tonic` won a large-scale benchmark at **16 MB RSS with 10,000 concurrent connections**, where Java and .NET sat at 150–200 MB. Memory efficiency, not raw RPS, is where it wins.

**Logging and metrics** are cheap if you use them correctly. `tracing` costs ~2.6–3.8 ns per disabled call (`log` ~1.6 ns) and near zero when the level is off. The mistakes are creating spans in tight loops, computing arguments outside the macro, and `#[instrument]` without `skip` on large arguments. Use `tracing-appender`'s non-blocking writer (a dedicated worker thread with periodic flushing and a bounded queue that drops when full) rather than writing from the request path. Prometheus histograms are much cheaper than summaries; `prometheus-static-metric` removes the label lookup when the labels are known at compile time.

## Databases and query engines

- **Query pipelining** is the underused win: crates.io got a **20% endpoint improvement** from it. `diesel-async` and `tokio-postgres` support it; `sqlx` and `sea-orm` do not.
- Diesel is fastest in ORM mode; sqlx is faster for raw SQL. Diesel's query builder is ~50% slower than sqlx on raw queries. Benchmark your own workload – the difference is smaller than the pooling and pipelining decisions.
- **Redis pipelining** measured 317,500 → 2,424,242 req/s on the same client and pool. If you are issuing many small commands, this is the whole optimization.
- **Embedded KV**: `redb` (pure Rust, copy-on-write B+trees) is comparable to LMDB and RocksDB. Reth uses MDBX and is evaluating redb.
- **Analytics**: Polars and DuckDB are 5–50x pandas depending on the operation; Polars runs 30–60% lower peak memory on large joins thanks to projection pruning and lazy plans. DataFusion streams one `RecordBatch` (≈`batch_size` rows) at a time, which amortizes per-batch overhead – tune `batch_size` before anything else, and note PGO was worth up to 25% there.
- **Search**: `tantivy` is roughly 2x Lucene on search latency, and its tail is flatter because there is no GC and no JIT warm-up.

## Data-oriented and games

- **ECS**: archetype storage (Bevy) packs same-shape entities together for locality and fewer branch mispredictions, and lets systems declare read/write dependencies so scheduling is lock-free. Adding or removing a component moves the entity between archetypes, which is not free. Sparse-set storage is faster to add and remove, slower to iterate. Bevy's own history is instructive: stateful queries amortized archetype checks to zero, `Query::for_each` was 1.5–3x faster on fragmented iteration, and enabling autovectorization for it gave another 50–87.5%.
- **Frame budget first.** Set the per-frame budget, then split CPU (logic, physics, scripts) from GPU (rendering). GPU over budget with CPU headroom means GPU-bound; the reverse means optimize simulation or draw submission. Tracy is the profiler for this – microsecond precision and CPU/GPU correlation.
- Stabilize memory with pools and per-frame arenas. A frame spike is almost always an allocation or a drop, not arithmetic.
- **UI rendering**: Zed's GPUI reaches 120 fps, ~0.12 s cold start and ~2 ms input latency with roughly 16x less memory than VS Code on large repos. The techniques generalize: diff a declarative tree per frame and emit draw calls only for changed regions, cache the glyph atlas on the GPU, and keep the parse incremental (Tree-sitter) so a 100k-line file highlights in single-digit milliseconds.

## Numeric and ML

- **Linear algebra**: `nalgebra` treats matrices as geometry with compile-time dimensions – the choice for games, robotics and anything with points, rotations and isometries. `faer` prioritizes raw throughput with runtime dimensions. `ndarray` sits between. `matrixmultiply` provides the blocked microkernels for x86-64 and NEON.
- Cache blocking is the whole game for GEMM: pack a block of B for L3, a block of A for L2, and a panel for L1, then run a register-level microkernel. Every fast BLAS follows the GotoBLAS2 five-loop structure.
- Match the loop order to the layout so the inner loop is stride-1. Transposing before a column-wise pass often pays for itself despite the copy.
- **GPU**: `wgpu` is the portable abstraction (Vulkan/Metal/DX12/WebGL, and the browser); `cubecl` adds a JIT with autovectorization, comptime and autotune; `rust-gpu` and `rustc_codegen_nvvm` compile Rust to kernels. Note the vectorization asymmetry: CPUs benefit from wider vectors up to AVX-512, GPUs saturate around 128 bits because that matches a global memory load.
- **ML frameworks**: Burn+CUDA measured **97% of PyTorch+CUDA** on Phi-3 with lower memory and no GC. Candle's published numbers are inconsistent across sources (47% faster on BERT in one, 3–4x slower on GPU in another) – treat single-source ML benchmarks with suspicion and run your own model.

## Embedded and no_std

```toml
[profile.release]
opt-level = "z"     # or "s"
lto = true
codegen-units = 1
debug = true        # debug info does not occupy flash
```

- `opt-level >= 2` unrolls loops, which costs real flash. `"s"` still allows vectorization and more inlining; `"z"` disables loop vectorization.
- **Known regression**: the machine-outliner pass at `opt-level = "z"` can corrupt the Link Register on Cortex-M, from rustc 1.73 onward. Use `"s"` if you hit it.
- Arrays live on the stack, and the typical stack limit is around 2 MB (far less on a microcontroller). Const generics let you keep a fixed-size buffer in a struct without boxing.
- `ArrayVec` over `SmallVec` when there is a hard maximum: no heap fallback and no inline-vs-heap branch on every access.
- The full size ladder ends at `-Z build-std` with `-C panic=immediate-abort`, which strips `core::fmt` and reaches roughly 30 KB stripped.

## WebAssembly

- `wasm-opt -Oz` after `wasm-bindgen` – typically another 10–30%, including tree-shaking that LTO alone does not do. Combine with `lto` for cross-module dead code elimination.
- Binary size *is* startup latency: browsers stream-compile while downloading.
- Swap the allocator when the default's thread-local caches inflate the binary; consider `#![no_std]` for pure computation modules.
- Profile in the browser's own devtools. Build with debug symbols so the `name` section is present, otherwise you get `wasm-function[123]` instead of Rust names. `performance.mark()`/`measure()` around FFI calls, or `console.time`. Always profile an optimized build – `wasm-pack build` optimizes by default.
- `twiggy`, not `cargo-bloat`, for WASM size analysis.

## FFI and language bindings

- `extern "C"` calls cost nothing beyond a C call, but **nothing inlines across the boundary**. Batch the work rather than crossing per item.
- **PyO3**: realistic gains are 5–50x for parsing, crypto and numeric transforms. The lever that matters is `py.allow_threads()` / `Python::detach` to release the GIL around Rust work – without it you have a fast single-threaded extension. Free-threaded Python (3.14+) changes the calculus. The classic mistake is benchmarking `maturin develop` without `--release`.
- **Node**: napi-rs and Neon both have negligible call overhead; the cost is **type conversion at the boundary**. Operate on Buffers and TypedArrays, not JS arrays and objects, and minimize copies. napi-rs native addons beat the same logic compiled to WASM.
- Rust's own `Command::spawn` is slower than `posix_spawn` (measured 406.8 µs for fork+execve, 291.8 µs for posix_spawn, with std slowest) because std forks and sets up a Unix domain socket pair to report exec errors. Irrelevant unless you spawn thousands of processes.

## Kernel and systems

Rust-for-Linux drivers run within low single-digit percent of C on sustained throughput, with a small predictable latency overhead on short call-path-heavy loads, and **underperform notably on memory-intensive workloads**. Parity is the honest claim, not a win.

`io_uring` runtimes are Linux-only with a recent kernel, which usually decides the question before performance does. Numbers, for when it does not: monoio is roughly equal to Tokio at one core, ~2x at four, ~3x at sixteen. A four-thread HTTP benchmark put tokio-uring at 1.39M req/s, monoio 1.36M, compio 1.27M, glommio 1.14M, with all io_uring runtimes 27–64% ahead of standard Tokio. Note that tokio-uring watches the io_uring fd with epoll, which is an extra indirection versus a native uring loop.

## Production case studies worth citing

Useful when the user needs a reason to believe an approach, or a realistic expectation.

| Org | Change | Result |
|---|---|---|
| Cloudflare (Pingora) | NGINX → Rust proxy, >1T req/day | 70% less CPU, 67% less memory, −5 ms median, −80 ms p95 |
| Discord | Go → Rust, Read States, 11M concurrent | GC spikes of 10–40 ms every ~2 min eliminated; ms → µs; 6.5x best case, 160x worst |
| TikTok | Go → Rust payment service | 105k → 210k QPS, CPU 78% → 52%, memory 7.4% → 2.07%, p99 19.87 ms → 4.79 ms |
| PostHog | Rayon/Tokio pool sizing under a CPU quota | 2.5 s p99 spikes → flat 94 ms |
| image-rs | Floats → integer accumulators, division → reciprocal multiply | 5.9x on `fast_blur`, no unsafe |
| Mozilla (Stylo) | C++ → parallel Rust CSS engine | 30% page-load speedup on amazon.com |
| Vortexa | Python hot path → 400 lines of Rust | 24 h → 1 h |

The Discord one is the most useful teaching example. The win was **predictability**, not raw speed: Go's GC forced a collection at least every two minutes and had to scan a large LRU cache. The first Rust version merely matched Go's latency with no spikes; beating Go on every metric came only after profiling and optimization. That is the honest shape of a rewrite, and it is what you should tell a user who expects the rewrite itself to be the win.
