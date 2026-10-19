# Concurrency: threads, locks, async

Parallelism is the largest available speedup and the easiest place to lose it all again. Correctness here is compile-checked; performance is not.

## Choosing the model

- **CPU-bound and parallelizable** → threads, usually via rayon.
- **I/O-bound with many concurrent waits** → async.
- **Both** → both. Blocking work on threads, results delivered over an async channel. This is the normal shape of real systems, not a compromise.

Std threads are 1:1 with OS threads and cost real memory and scheduler resources. Spawning one per work item will fall over under load. Async tasks are managed by the runtime and are far cheaper: a Tokio task spawn measures around **10 ns**, at most 50 ns – small enough that it is hard to measure without the measurement being noise. Do not avoid spawning on performance grounds.

## The shared-state ladder

Increasing performance, increasing complexity. Climb only as far as the measurement requires.

| Rung | Use when |
|---|---|
| `Mutex<HashMap>` | Cold paths, rare access. Correct and boring |
| `RwLock<HashMap>` | Reads are ≥95% and writes are rare. **RwLock degrades sharply as writes increase** because it tracks reader/writer counts and orchestrates wakeups; Mutex stays flat |
| `DashMap` (sharded) | The pragmatic default for a production service. Crossover against a single lock is around **2–4 contending threads**; below that the hash and shard lookup are pure overhead |
| Per-thread state + periodic flush | Heavy contention, eventual consistency acceptable. This is what the `metrics` crate does |
| Plain atomics | A single counter or flag. Always fastest |

CAS loops waste work under collision but still beat a mutex on modern CPUs.

## Locks

The single highest-value rule: **a lock guard lives until the end of its scope, and the scope is not always what you think.**

```rust
// BAD: temporaries in `while let` / `if let` / `match` live to the end of the block,
// so the lock is held for the whole of job() and every other worker is serialized.
while let Ok(job) = receiver.lock().unwrap().recv() {
    job();
}

// GOOD: with `let`, temporaries are dropped at the end of the statement.
loop {
    let job = receiver.lock().unwrap().recv();
    match job { Ok(job) => job(), Err(_) => break }
}
```

This exact bug appears in the Rust book's own thread pool and it is common in production code. Look for it every time a pool refuses to scale.

Other rules:

- Drop the guard before slow work: a tighter scope, or `drop(guard)`.
- **Never hold a std `Mutex` guard across `.await`.** It is not just slow, it deadlocks. Use `tokio::sync::Mutex` if you must hold across await points, and prefer restructuring so you do not.
- Lock granularity: one lock around a coarse structure serializes everything; many fine locks invite deadlock. Shard when contention is measured.
- Deadlock risk comes from acquiring two locks in different orders. Rust does not prevent it.
- Prefer `Rc` to `Arc` in single-threaded code. Atomic refcounting is not free, which is exactly why std does not use it by default.
- For a single counter or flag, use `std::sync::atomic` rather than `Mutex<T>`.

**parking_lot** is worth trying: Mutex about 1.5x faster uncontended and up to 5x contended, RwLock almost always faster and up to 50x in some read/write mixes, and its Mutex is 1 byte against std's larger footprint. Std has improved substantially on some platforms, so measure rather than assume. Use `clippy.toml` `disallowed-types` to stop std versions creeping back in if you standardize on it.

**Diagnosing lock problems**: `hotpath` tracks wait time and hold time separately for std, parking_lot, tokio and async-lock, splitting read from write acquisitions. `parking_lot`'s `deadlock_detection` feature runs a background thread calling `deadlock::check_deadlock()` and reports cycles with backtraces. `lockbud` does static analysis across std, parking_lot and spin.

## Atomics

Pick the **weakest ordering that is still correct** – every stronger ordering emits more fences and blocks more compiler and CPU reordering.

| Ordering | Guarantee | Use for |
|---|---|---|
| `Relaxed` | Atomicity and a total modification order per variable. No happens-before | Counters, statistics |
| `Release` (store) | Everything written before is visible to a matching `Acquire` | Publishing data |
| `Acquire` (load) | Sees everything written before the matching `Release` | Consuming published data |
| `AcqRel` | Both, for read-modify-write | `compare_exchange`, `fetch_update` |
| `SeqCst` | Global total order. Strongest and slowest | When you cannot reason about the alternative |

Both sides must cooperate: the writer puts its data before the `Release` store, the reader does its reads after the `Acquire` load.

**The cost depends entirely on the architecture, and this is why x86-tuned code regresses on ARM:**

- **x86-64** is total-store-ordered. Loads compile to a plain `mov` at every ordering. Stores compile to `mov` for Relaxed and Release but **`xchg` for SeqCst** – that is the only place ordering costs anything. You can be sloppy here and never notice.
- **aarch64** is weakly ordered. Acquire, Release and AcqRel are all roughly as expensive as SeqCst; only **Relaxed** is genuinely cheap. This is where wrong orderings actually cost you.

Atomics are not free anywhere: they temporarily lock cache lines in other cores and act as fences that cost missed optimizations. One reported case took p99 from 42 µs to 8 µs by replacing mutexes with atomics – but only after the data structure allowed it.

## False sharing and the bandwidth ceiling

Two atomics written by different cores in the same cache line will ping-pong that line between them. It does not appear as lock contention and **no profiler will point at it** – it appears only as throughput below what the algorithm predicts.

```rust
use crossbeam_utils::CachePadded;
struct Shared { producer: CachePadded<AtomicUsize>, consumer: CachePadded<AtomicUsize> }
```

Eight `AtomicU64` counters are exactly one 64-byte cache line, which is why per-thread counter arrays are the textbook case. `CachePadded` uses 128 bytes on x86-64/aarch64/powerpc64 because those prefetch line pairs, 32 on arm/mips/riscv64. Verify the fix with `perf c2c` and a throughput benchmark. Once fixed, throughput often scales linearly with core count.

The other invisible ceiling is **memory bandwidth**. A single thread cannot saturate the bus, and **4–6 cores usually saturate one memory domain**. A memory-bound workload that stops scaling at six cores is finished, not broken. Classify with the roofline model before adding threads (see `measure.md`).

## Channels

`std::sync::mpsc` is multi-producer, single-consumer, and `send` moves ownership. Alternatives are usually faster:

- **flume** – always faster than std, sometimes faster than crossbeam, low latency, small footprint.
- **crossbeam-channel** – often the fastest for 1:1 multi-threaded communication, `select!` support.
- **rtrb** – wait-free SPSC ring buffer, no allocation in the hot path. The right choice for audio and anything that must not block. A measured comparison against crossbeam-channel: p50 188 µs vs 255 µs, p99 207 µs vs 349 µs – the tail is where the specialized structure pays.
- **kanal**, **omango** – maintain benchmark suites against all of the above.

Which wins depends on core count, message size and producer/consumer ratio, so benchmark on your shape; the crates' own READMEs say their benchmarks are flawed. `try_recv` instead of `recv` when the thread has other work to do while waiting.

Sending large values by channel copies them. Send `Box`/`Arc` for big payloads.

**Use bounded channels.** An unbounded channel between a fast producer and a slow consumer grows until OOM. The bound is the backpressure mechanism, not a limitation: when it is full, senders wait or shed load. Pair with a `Semaphore` to cap concurrency explicitly rather than letting the runtime guess.

## Rayon

```rust
use rayon::prelude::*;
let total: u64 = items.par_iter().map(expensive).sum();
```

- The work-stealing scheduler handles chunking. **Do not tune chunk size** unless you have measured a problem; `with_min_len` / `with_max_len` exist for when you have.
- If the per-item work is trivial, rayon loses to sequential. Its worker threads poll for jobs even when no `par_iter` is running, and work stealing splits into a binary tree of sub-slices rather than per item, so per-item work below a few microseconds is dominated by overhead. Parallelize image resizing, not integer addition.
- Rayon uses one global pool. Nesting rayon inside rayon is fine; nesting rayon inside a blocking async task is not (see below).
- Libraries should generally not impose threading. Expose parallelizable APIs and let the caller decide.

## Async

**Futures are lazy state machines.** Nothing runs until a runtime polls it. `async fn` desugars to a function returning `impl Future`, and every `.await` is a suspension point and a state-machine variant.

### Everything between await points is synchronous

A long compute stretch inside an async block starves every other future on that worker. Fixes, in order:

1. Move the work off the runtime: `spawn_blocking` for one-offs, a dedicated `std::thread` for a hot path.
2. `tokio::task::yield_now().await` at sensible boundaries. This is cheaper and more precise than `sleep`, which has a floor of about one millisecond.
3. Accept it. Breaking up a compute-bound task can make it significantly slower. Measure before splitting.

Never call blocking I/O or `std::thread::sleep` inside async. Prefer async-native clients for network, files and databases.

### spawn_blocking

Runs on a **separate** pool from the async workers, created lazily, capped around 512 threads by default (`max_blocking_threads`). Each blocking task occupies one thread for its entire duration.

- Only for CPU-bound work or genuinely blocking I/O with no async alternative.
- Bound concurrency with a `Semaphore`, otherwise a burst exhausts the pool and every other blocking task queues behind it.
- **Never call it in a tight loop.** Use a dedicated thread for hot paths and keep `spawn_blocking` for one-offs.

### async traits

Native `async fn` in traits (AFIT/RPITIT) desugars to a return-position `impl Trait` backed by an anonymous associated type: each impl gets its own concrete future, sized exactly, inlined, on the stack. Zero overhead.

`#[async_trait]` boxes every call. Measured: **~20–30 ns per call** for a small future with a warm allocator (malloc, memcpy of the state machine, vtable, eventual free), and **up to ~1.2 µs** for large futures with a cold allocator and deep nesting. Removing it from axum 0.8 halved p99 on one reported service.

Use `#[async_trait]` only when you genuinely need `dyn Trait`. For purely generic usage the native form is strictly better.

### FuturesUnordered

`FuturesUnordered` and `buffer_unordered` only poll their contents **when they are themselves polled**. If you take an item from the stream and then do significant wall-clock work in the loop body, every other future in the set is frozen – and remote peers time out. This is the standard "my concurrent requests randomly fail" bug.

Keep the loop body trivial and spawn the work. `futures-buffered`'s `FuturesUnorderedBounded` has a fixed capacity, much lower memory overhead and a small speed gain; `future-queue` adds ordering and group limits.

### Future size

The generated state machine grows with nesting, and `async fn` **captures all of its arguments into the future**, so passing a large argument down a call chain duplicates it at each level. Fuchsia measured a single future over 400 kB. At `opt-level = 3` the compiler often collapses the machine, but not when the future tree is deep.

- Keep async functions shallow.
- Pass `&T` or `Arc<T>` rather than large owned values.
- `std::pin::pin!` pins a local future with no allocation; `Box::pin` heap-allocates and is only needed when you must return or store the pinned value. Note that inside an `async fn`, a local crossing an `.await` lives in the future's own storage anyway.
- A recursive async function must box its future.

### Send and the runtime

A future is `Send` only if no `!Send` value is held **across** an `.await`. Holding an `Rc` or a `RefCell` borrow across an await makes the whole future `!Send`, which locks you out of multi-threaded schedulers. Note "across", not "inside": creating and dropping an `Rc` between two awaits is fine.

### Structure

- Code inside one async block runs in source order. Concurrency requires separate futures joined (`join`, `join!`, `join_all`) or spawned tasks. Sequentially awaiting two futures is just sequential code.
- `join` is fair and alternates; `select` polls in argument order and is not fair – put the future you want to win first.
- A future can be dropped mid-flight and never resume. If you leave state broken across an `.await`, use a drop guard to repair it.
- Thread-locals set before an `.await` are meaningless after it: the task may resume on a different worker.
- Compose your own combinators from `select` and `sleep` (timeouts, retries with backoff) instead of reaching for a dependency.

## Tokio runtime tuning

Leave these alone unless `tokio-metrics` shows a specific problem.

- `worker_threads` – the multi-threaded runtime creates all of them at startup. **Set this explicitly in a container** (see below).
- `event_interval` (default **61**) – scheduler ticks between polls for external events (timers, I/O). Lower delivers I/O sooner; higher prioritizes already-ready tasks.
- `global_queue_interval` – ticks before checking the global queue. If unset, tokio computes it dynamically targeting ~10 ms between checks using `worker_mean_poll_time`. Lower is fairer at the cost of synchronization.
- `current_thread` runtime spawns no workers and only runs inside `block_on`. `LocalSet` carries considerable overhead compared with spawning `!Send` tasks directly on a current-thread runtime.

### Diagnosing async

**tokio-console** is the tool. It flags tasks that ran a long time without yielding and tasks that woke themselves more often than others woke them. Sort by **busy time** and **poll count**. The metrics are Total (alive), Busy (executing), Sched (waiting to be polled) and Idle.

**tokio-metrics** gives the same signals as numbers: `worker_mean_poll_time` rising means tasks are blocking, and the blocking-queue depth and idle blocking-thread count separate honest `spawn_blocking` from accidental synchronous work.

The ScyllaDB driver postmortem is the best worked example of async profiling: a "regression" that only appeared on a *fast local* network, fixed by wrapping the streams in `BufReader`/`BufWriter`. Once the network stops being the bottleneck, syscall count becomes it.

## Containers, pinning and oversubscription

`available_parallelism()` respects cpusets but can overcount under a CFS **quota**. Tokio sizes its workers from detected cores, Rayon does the same, and in a 2-CPU-quota pod you get double oversubscription plus throttling. The symptom is latency spikes at low average CPU.

PostHog's feature-flag service is the canonical case: CPU-bound Rayon work running on Tokio worker threads produced **2.5 s p99 spikes**. The fix was a dispatcher with semaphore-bounded concurrency and explicit pool sizing for both runtimes, accepting ~50% CFS oversubscription and 7–30% throttling. Result: a **flat 94 ms** p99, roughly 4x better.

Core pinning: `core_affinity::set_for_current()` inside tokio's `on_thread_start`, or `affinitypool` for blocking pools (it ties or beats `spawn_blocking` on most workloads). Pinning keeps caches warm and avoids scheduler jitter, and is standard for low-latency work. Remember NUMA first-touch: pin before you allocate (see `memory.md`).

## Thread pools

- Size CPU pools to the **actual CPU budget**, not the host's core count.
- Size I/O pools by measured concurrency and latency, not by intuition.
- A bounded pool is also a DoS defence: unbounded spawning is an attack surface.
- Graceful shutdown: drop the sender so receivers see a disconnect, then join the workers. Use `Vec::drain(..)` to move the handles out rather than wrapping every one in `Option`.

## Batching

io_uring submission batching, `writev`, Redis pipelining, connection pooling and buffered writers are all the same idea: amortize a fixed per-operation cost over more work.

Redis pipelining measured **317,500 → 2,424,242 req/s** on the same client and connection count. io_uring batching trades the same way: small batches keep latency under ~25 µs at higher syscall frequency, large batches cut submission cost but raise variance and tail latency. Adaptive batching – sizing the batch by the ratio of outstanding operations to waiting consumers – gets both.

Always state the trade: batching buys throughput with latency variance and data-at-risk.

## Real-time constraints

For audio, control loops and anything with a hard deadline, the processing thread must never block for an unbounded time. That means **no allocation, no deallocation, no locks** on that thread.

- Preallocate everything; hand work in over a wait-free SPSC queue (`rtrb`).
- Deallocation is as dangerous as allocation. `basedrop` defers drops to another thread; `defer-drop` does the same generally.
- `assert_no_alloc` catches violations in tests. It is the most useful tool in this category.
- `audio_thread_priority` for the scheduling class.

TigerBeetle takes this to its conclusion: all memory allocated at startup, zero allocation afterwards, every structure with an explicit upper bound checked at the system edge, all I/O including time externalized. You rarely need to go that far, but "what is the upper bound on this?" is the right question in any latency-sensitive path.
