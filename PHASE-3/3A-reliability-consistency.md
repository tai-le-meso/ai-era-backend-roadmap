# 3A — Reliability, Consistency & Coordination

**Duration:** 7 weeks (126h) · **Months 6.0 – 7.6** · **Prereq:** 2C
**Flagship increment:** v2.5 — Resilient

> **Outcome:** Given any system diagram, I can enumerate what fails, what the blast
> radius is, and which pattern contains it — before it happens in production.

**Study rule from the source doc:** *problems first, technologies second.* Every week
starts with the failure, not the library.

---

## Week 1 — Failure models & timeouts

- **Learn (5h):** The eight fallacies of distributed computing. Failure taxonomy: crash-stop,
  crash-recovery, omission, timing, Byzantine. Partial failure — the defining problem.
  Timeouts: connect vs read vs total, why "no timeout" is the most common production bug,
  timeout budgets and propagation across a call chain, deadline propagation. The
  **unbounded-queue antipattern**.
- **Build (8h):** Audit every outbound call in the flagship (DB, Redis, Kafka, HTTP).
  Set explicit timeouts everywhere with a documented budget. Add a `toxiproxy` (or
  equivalent) harness to inject latency and drops. Show what happened *before* your fix.
- **Reason (3h):** Compute the flagship's timeout budget: if the client SLA is 500ms,
  what is each hop allowed? What happens when a downstream call takes 30s and you have
  200 threads?
- **AI drill (2h):** *"List everything that can fail in this request path."* Compare
  against your own list. What did each of you miss?
- **Deliverable:** timeout-budget table + fault-injection harness.
- **Self-check:** ☐ Every outbound call in my service has an explicit, justified timeout.

## Week 2 — Retry, backoff & the retry storm

- **Learn (5h):** When retry is safe (idempotent ops only) and when it corrupts data.
  Exponential backoff, full vs equal jitter (read the AWS jitter article), retry budgets,
  retry amplification across N layers (2 retries × 3 layers = 27× load), the
  **metastable failure** / retry storm. Client-side vs server-side retry. `Retry-After`.
  Hedged requests.
- **Build (8h):** Add Resilience4j retry with jitter to the flagship's outbound calls.
  Then **cause a retry storm** in your fault harness — show the amplification in metrics
  — and fix it with a retry budget + circuit breaker interaction.
- **Reason (3h):** Which flagship operations are safe to retry and why? Draw the
  amplification factor through your call graph. Why does jitter matter more than backoff?
- **AI drill (2h):** Ask AI to add retries to your payment call. Check whether it noticed
  the idempotency requirement. Most don't.
- **Deliverable:** retry config + a metrics screenshot showing storm and mitigation.
- **Self-check:** ☐ I can explain retry amplification with a number.

## Week 3 — Circuit breakers, bulkheads & load shedding

- **Learn (5h):** Circuit breaker states (closed/open/half-open), sliding windows
  (count vs time), failure-rate vs slow-call-rate thresholds, and the tuning trap of a
  breaker that never opens. Bulkhead: thread-pool vs semaphore isolation, why one slow
  dependency should not consume all threads. Load shedding: queue-depth-based, priority
  shedding, admission control, backpressure vs shedding. Graceful degradation and
  fallbacks that are actually useful.
- **Build (8h):** Ship **v2.5**: Resilience4j circuit breakers + bulkheads on every
  external dependency; a load shedder that returns 503 with `Retry-After` above a
  measured queue depth. Prove each works under fault injection.
- **Reason (3h):** For each flagship dependency: what is the correct degraded behaviour?
  (Cached balance? Reject? Queue for later?) Write it per dependency — this is a
  product decision as much as a technical one.
- **AI drill (2h):** *"My circuit breaker never opens in production. Why?"* Then check
  your own thresholds against your measured failure rates.
- **Deliverable:** **v2.5 tagged** + `docs/reliability/degradation-matrix.md`.
- **Self-check:** ☐ I can explain why a bulkhead and a circuit breaker solve different problems.

## Week 4 — Consistency models

- **Learn (5h):** Linearizability vs sequential vs causal vs eventual consistency —
  precise definitions, not vibes. Session guarantees: read-your-writes, monotonic reads,
  monotonic writes, writes-follow-reads. **CAP** stated correctly (and why it is widely
  misquoted), **PACELC** as the more useful framing. Convergence, conflict resolution,
  last-write-wins and its data loss, vector clocks, CRDT concepts.
- **Build (8h):** Build a demo that visibly violates read-your-writes via a read replica,
  then implement three fixes (sticky routing, write-timestamp token, read-from-primary
  for N seconds) and compare their cost. Extend to a monotonic-reads violation across
  two replicas.
- **Reason (3h):** For each flagship read path, name the *weakest* consistency you can
  tolerate and the business consequence of getting it wrong. Balance display vs balance
  used for an authorisation decision are different answers.
- **AI drill (2h):** Ask AI "is my system CP or AP?" It will usually oversimplify. Push it
  to PACELC and to per-operation rather than per-system reasoning.
- **Deliverable:** `docs/architecture/consistency-map.md` — one row per read path.
- **Self-check:** ☐ I can state PACELC and apply it to my own system.

## Week 5 — Idempotency & deduplication at system scale

- **Learn (5h):** Idempotency as a system property, not an endpoint decoration. Natural
  vs synthetic idempotency keys, deterministic ID derivation, dedupe windows and their
  storage cost, the difference between idempotent and commutative operations, effectively-
  once end-to-end. Exactly-once as a myth at the transport layer and a reality at the
  application layer.
- **Build (8h):** Build an end-to-end idempotency test: fire the same transfer through
  API → Kafka → consumer → ledger, duplicating at *every* stage, and assert exactly one
  ledger effect. Add dedupe-window expiry and prove what happens after it expires.
- **Reason (3h):** How long must your dedupe window be? Derive it from your maximum
  retry horizon plus clock skew plus DLT replay window — not from a guess.
- **AI drill (2h):** Ask AI to design dedupe for your pipeline, then attack it with:
  replay after 30 days, clock skew, consumer restart mid-batch, DLT reprocessing.
- **Deliverable:** end-to-end duplication test suite, green.
- **Self-check:** ☐ I can justify my dedupe window with arithmetic.

## Week 6 — Distributed coordination

- **Learn (5h):** Leader election (why you probably want it, and why you probably don't
  want to implement it), consensus intuition: Paxos vs **Raft** (leader election, log
  replication, safety), quorums, split brain, fencing tokens. Failure detection:
  heartbeats, phi-accrual, the impossibility of distinguishing slow from dead. Distributed
  locking revisited with fencing. Coordination services: etcd/ZooKeeper/Consul roles.
  Lamport clocks, vector clocks, hybrid logical clocks, why wall-clock time is unsafe.
- **Build (8h):** Implement leader election for the outbox relay (only one instance
  should publish) using either a PostgreSQL advisory lock or a lease table with fencing
  tokens. Test it: partition the leader, verify no dual-publish, measure failover time.
- **Reason (3h):** Why can you not distinguish a partitioned node from a crashed one?
  What does that imply for your failover timeout? What is the cost of choosing wrong in
  each direction?
- **AI drill (2h):** *"Explain Raft leader election"* — then check every claim against the
  Raft paper (it is readable). Note precisely where AI blurred details.
- **Deliverable:** leader-elected relay + partition test + failover-time measurement.
- **Self-check:** ☐ I can explain Raft leader election and log replication on a whiteboard.

## Week 7 — Data distribution & consolidation

- **Learn (5h):** Replication topologies (single-leader, multi-leader, leaderless),
  quorum reads/writes (W + R > N) and why it still isn't linearizable, read repair,
  anti-entropy. Sharding: range vs hash vs directory; **consistent hashing** with virtual
  nodes; rebalancing; hot shards; resharding without downtime. Secondary indexes in
  sharded systems (local vs global). Cross-shard queries and joins.
- **Build (8h):** Implement consistent hashing yourself (with virtual nodes) and measure
  key movement on node add/remove vs naive modulo. Then design (on paper) how the
  flagship would shard the ledger by account, and what queries would break.
- **Reason (3h):** What is the flagship's shard key and what does it make impossible?
  Where would a cross-shard transaction appear, and how would you avoid needing one?
- **AI drill (2h):** *"Give me the strongest argument against sharding this system now."*
  (The right answer usually is: don't, yet. Make it convince you.)
- **Deliverable:** consistent-hashing implementation + `docs/architecture/sharding-analysis.md`.
- **Self-check:** ☐ I can explain why W+R>N does not give linearizability.

---

## Exit checklist — all must pass before 3B

- ☐ Every outbound call has a justified timeout inside a documented budget
- ☐ Retry storm reproduced and mitigated; amplification factor computed
- ☐ Circuit breakers, bulkheads and load shedding working under fault injection
- ☐ Degradation matrix written — one decision per dependency
- ☐ Consistency map written — one row per read path with the weakest tolerable model
- ☐ End-to-end duplication test green with a justified dedupe window
- ☐ Leader election working with a measured failover time
- ☐ Consistent hashing implemented and measured
- ☐ v2.5 tagged
