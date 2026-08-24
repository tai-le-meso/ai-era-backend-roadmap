# 1B — JVM Internals, Concurrency & Performance

**Duration:** 6 weeks (108h) · **Months 1.6 – 3.0** · **Prereq:** 1A
**Flagship increment:** v0.5 — measured

> **Outcome:** When a service is slow, I do not guess. I profile it, name the
> mechanism (GC pause / lock contention / allocation rate / JIT deopt), prove it with
> a benchmark, and explain the fix in terms of the JVM's actual behaviour.

**Why this before databases:** almost every "the database is slow" incident is
actually a connection-pool, thread-pool, or allocation problem. You cannot diagnose
that without this sub-roadmap.

---

## Week 1 — JVM memory model & the object lifecycle in memory

- **Learn (5h):** Runtime data areas — heap (young/old, eden/survivor), thread stacks,
  metaspace, code cache, direct/off-heap memory. Object layout, headers, compressed oops,
  padding, TLAB allocation. `-Xms/-Xmx/-XX:MaxMetaspaceSize` and what happens when each
  is exhausted. Reading a heap dump.
- **Build (8h):** Instrument the flagship with JFR (Java Flight Recorder). Write three
  deliberate leaks — static collection, unbounded cache, `ThreadLocal` in a pooled
  thread — and find each one from a heap dump using Eclipse MAT or `jcmd`.
- **Reason (3h):** Draw where each object in one flagship request lives. Which ones
  escape? Which die in eden? What is your allocation rate per request?
- **AI drill (2h):** Paste a heap-dump histogram and ask AI to diagnose. Compare with
  your own diagnosis. Who was right, and did AI reason or pattern-match?
- **Deliverable:** `docs/perf/heap-analysis.md` with three leaks found and fixed.
- **Self-check:** ☐ I can name every JVM memory region and what OOMs each.

## Week 2 — Garbage collection: G1, ZGC, and the pause you can actually feel

- **Learn (5h):** GC theory — generational hypothesis, mark/sweep/compact, roots,
  write barriers, remembered sets. **G1**: regions, concurrent marking, mixed
  collections, humongous objects, pause-time goal. **ZGC / Generational ZGC**: coloured
  pointers, load barriers, sub-millisecond pauses, memory overhead trade-off. Reading GC
  logs (`-Xlog:gc*`). Allocation rate vs pause frequency.
- **Build (8h):** Run the flagship under sustained load with G1 and with ZGC. Capture GC
  logs, plot pause distribution, and measure p50/p95/p99 request latency under each.
- **Reason (3h):** For the flagship's workload, which collector wins and why? What would
  change that answer? When is a *higher* throughput collector correct despite pauses?
- **AI drill (2h):** *"Give me the strongest argument against my collector choice."*
  Then check its claims against your own GC logs.
- **Deliverable:** `docs/perf/gc-comparison.md` with real plots and a recommendation.
- **Self-check:** ☐ I can read a GC log and say what the collector was doing.

## Week 3 — JIT, profiling & diagnostics

- **Learn (5h):** Interpreter → C1 → C2 tiering, method inlining, escape analysis &
  scalar replacement, loop unrolling, deoptimisation triggers, warmup, `-XX:+PrintCompilation`.
  Profilers: sampling vs instrumenting, async-profiler flame graphs, JFR event types.
  `jcmd`, `jstack`, `jmap`, thread-dump analysis.
- **Build (8h):** Produce a flame graph of the flagship under load. Find the top 3 hot
  paths. Optimise one — and prove the improvement with measurements, not belief.
- **Reason (3h):** Why did a method get deoptimised in your run? Why is benchmarking
  without warmup meaningless? What did escape analysis eliminate?
- **AI drill (2h):** Give AI a flame graph description and your optimisation plan. Ask it
  to predict the speedup. Measure. Record the gap between prediction and reality.
- **Deliverable:** flame graph + before/after numbers + written explanation of the mechanism.
- **Self-check:** ☐ I can read a thread dump and identify a blocked thread's owner.

## Week 4 — Concurrency primitives & the Java Memory Model

- **Learn (5h):** `Thread` lifecycle, `ExecutorService` types and their queue semantics,
  `CompletableFuture` composition + the executor it actually runs on, `ForkJoinPool`
  common-pool hazards. `synchronized` (biased/thin/fat locks), `ReentrantLock`,
  `ReadWriteLock`, `StampedLock`, `Condition`. Atomics, CAS, ABA, `LongAdder` vs
  `AtomicLong`. `ConcurrentHashMap` internals. **JMM**: happens-before, `volatile`,
  final-field freeze, safe publication, reordering, false sharing.
- **Build (8h):** Build a concurrent balance-update component three ways —
  `synchronized`, `ReentrantLock`, and optimistic CAS retry. Write a test that *fails*
  under race conditions without correct publication (use jcstress if you can).
- **Reason (3h):** Write the happens-before chain for each of your three implementations.
  Where exactly does `volatile` become insufficient?
- **AI drill (2h):** Ask AI for a "thread-safe cache". Find the race condition in what it
  gives you (there almost always is one: check-then-act, or unsafe publication).
- **Deliverable:** three implementations + a race-condition test + correctness argument.
- **Self-check:** ☐ I can explain happens-before without saying "it just works".

## Week 5 — Virtual threads, structured concurrency & thread-safety design

- **Learn (5h):** Virtual threads — mounting/unmounting, carrier threads, pinning
  (`synchronized` blocks, native frames), when they help (I/O-bound) and when they don't
  (CPU-bound). Structured concurrency (`StructuredTaskScope`), scoped values vs
  `ThreadLocal`. Thread-per-request vs reactive vs virtual threads. Backpressure.
  Thread-pool sizing (Little's Law).
- **Build (8h):** Run the flagship on platform threads vs virtual threads under an
  I/O-heavy load profile. Find and fix a pinning case. Measure throughput and tail latency.
- **Reason (3h):** Apply Little's Law to the flagship: what pool size does your target
  throughput and latency imply? Where does the *real* bottleneck sit — threads, DB pool,
  or CPU?
- **AI drill (2h):** *"Should this service use virtual threads?"* Force AI to argue both
  sides, then decide yourself and write the ADR.
- **Deliverable:** `docs/adr/0002-threading-model.md` backed by measurements.
- **Self-check:** ☐ I can identify a pinning case by reading code.

## Week 6 — Performance engineering & JMH + consolidation

- **Learn (5h):** Latency vs throughput vs utilisation. Percentiles: why the mean lies,
  why p99 matters, coordinated omission. Amdahl's & Universal Scalability Law. JMH:
  warmup, forks, blackholes, state scopes, dead-code elimination, benchmark modes.
  Load-testing tools (k6/Gatling) and how to build a realistic profile.
- **Build (8h):** Build a JMH suite for the flagship's hot paths. Build a k6 load profile
  matching realistic banking traffic (bursty, skewed hot accounts). Establish the
  **performance baseline** you will regression-test against for the next 21 months.
- **Reason (3h):** Where is your service's knee point? What resource saturates first?
  What is the single change that would most improve p99, and what does it cost?
- **AI drill (2h):** Ask AI to review your JMH benchmarks for measurement errors (a
  classic: forgetting a blackhole so the JIT deletes your code). Verify each claim.
- **Deliverable:** **v0.5 tagged** — JMH suite + load profile + `docs/perf/baseline.md`.
- **Self-check:** ☐ I can explain coordinated omission and how my tool handles it.

---

## Exit checklist — all must pass before 2A

- ☐ I can read GC logs, a heap dump, a thread dump, and a flame graph unaided
- ☐ GC comparison document exists with my own measurements and a defended choice
- ☐ Three concurrency implementations exist with a written happens-before argument
- ☐ A pinning case was found and fixed; threading-model ADR is written
- ☐ JMH suite + load profile committed; performance baseline recorded
- ☐ I found at least 3 concurrency bugs in AI-generated code and can explain each
- ☐ p50/p95/p99 for the flagship's main endpoint are known numbers, not guesses

**If 2+ fail:** 1-week repair sprint, focused on whichever of {GC, profiling, JMM} failed.
