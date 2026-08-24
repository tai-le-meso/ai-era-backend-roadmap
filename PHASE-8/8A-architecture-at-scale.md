# 8A — Architecture Design at Scale

**Duration:** 7 weeks (126h) · **Months 21.0 – 22.6** · **Prereq:** 7B
**Flagship increment:** v8 — Load-tested, failure-tested, multi-region designed

> **Outcome:** I can design and defend monolith, modular monolith, microservice,
> event-driven, data-intensive, high-throughput, high-availability and multi-region
> systems — choosing between them on evidence, not fashion.

**Note on realism:** you will not run a true multi-region system on a laptop. For the
scale topics, the deliverable is a *rigorous design document plus the largest
experiment you can actually run*. That is exactly what the job requires.

---

## Week 1 — Architecture styles: the honest comparison

- **Learn (5h):** Revisit with 21 months of evidence: Monolith · Modular Monolith ·
  Microservices · Event-driven · Serverless · Cell-based architecture. For each: team
  topology fit (Conway's Law), deployment independence, failure isolation, data ownership,
  operational cost, debugging cost, and the honest failure story. The distributed monolith
  antipattern and how to detect you have built one. Migration paths between styles in
  both directions (including microservices → modular monolith, which is real).
- **Build (8h):** Write a comparison document scored against the flagship's actual
  measured characteristics. Then instrument the flagship to *detect* distributed-monolith
  symptoms: synchronous call chains, shared databases, lockstep deployments.
- **Reason (3h):** Given your team size (be honest — your real team), which style is
  correct for the flagship *today*? What would have to change for the answer to change?
- **AI drill (2h):** *"Give me the strongest argument for the style I did not choose."*
  Then write the rebuttal. This is the exact debate you will have in real reviews.
- **Deliverable:** `docs/architecture/style-comparison.md` + distributed-monolith detectors.
- **Self-check:** ☐ I can argue for and against microservices with equal competence.

## Week 2 — High-throughput systems

- **Learn (5h):** Throughput engineering: batching, pipelining, async I/O, zero-copy,
  mechanical sympathy (cache lines, false sharing, NUMA), lock-free structures, the LMAX
  Disruptor pattern. Queue theory: Little's Law, utilisation vs latency (the knee at ~70%),
  why 100% utilisation means infinite queueing. Backpressure end-to-end. Write-path
  optimisation: append-only, WAL, LSM vs B-tree. Read-path: caching layers, read replicas,
  materialised views, precomputation.
- **Build (8h):** Take the flagship's highest-throughput path (ledger posting) and 10× it.
  Measure before, apply batching/async/pipeline changes, measure after. Find the new
  bottleneck and name it precisely.
- **Reason (3h):** Plot latency against utilisation for your service. Where is the knee?
  What is your maximum safe operating utilisation, and does your autoscaling respect it?
- **AI drill (2h):** Ask AI how to 10× throughput. Grade its suggestions against what
  actually worked — this is a strong test of whether it reasons or pattern-matches.
- **Deliverable:** 10× throughput improvement with before/after data + a bottleneck analysis.
- **Self-check:** ☐ I can explain why 90% utilisation is dangerous, with the queueing maths.

## Week 3 — High-availability systems

- **Learn (5h):** Availability maths: series vs parallel components, why adding
  dependencies multiplies failure, redundancy N+1 vs 2N, correlated failure and why "two
  replicas" often isn't. Failure domains: process, host, rack, AZ, region. Health checks
  (liveness vs readiness vs startup) and how a bad health check causes the outage.
  Graceful shutdown and connection draining. Zero-downtime deployment: rolling, blue-green,
  canary, feature flags. Database failover and its data-loss window. Disaster recovery:
  RTO/RPO, backup and — critically — **restore testing**.
- **Build (8h):** Make the flagship survive: rolling deploys with zero dropped requests
  (prove it under load), correct readiness/liveness probes, graceful shutdown with
  draining, and a **tested** database restore with a measured RTO and RPO.
- **Reason (3h):** Compute the flagship's theoretical availability from its dependency
  chain. What is your actual RTO and RPO, measured not assumed? Which single dependency
  most limits your availability?
- **AI drill (2h):** *"What single point of failure did I miss?"* Then verify by actually
  killing that component.
- **Deliverable:** zero-downtime deploy proof + measured RTO/RPO + availability calculation.
- **Self-check:** ☐ I have restored a backup and know how long it took.

## Week 4 — Data-intensive systems

- **Learn (5h):** Batch vs stream vs lambda vs kappa architectures. Stream processing:
  windowing (tumbling, sliding, session), watermarks, late data, event time vs processing
  time — the concepts that make streaming genuinely hard. Exactly-once in stream
  processing. Data lake/warehouse/lakehouse, OLTP vs OLAP separation, CDC pipelines
  (Debezium) as the bridge. Data quality, contracts and lineage. Backfill and reprocessing
  design — the thing everyone forgets until they need it.
- **Build (8h):** Build a CDC pipeline from the flagship's PostgreSQL into an analytical
  store, plus a streaming aggregation with correct event-time windowing and late-data
  handling. Then perform a **full backfill** and prove idempotency of the pipeline.
- **Reason (3h):** What is your late-data policy and what does it cost in correctness?
  How would you reprocess 6 months of data without double-counting? How does CDC interact
  with your outbox pattern — are they redundant?
- **AI drill (2h):** Ask AI to explain watermarks and late data. Verify against the
  Dataflow model paper. This is a topic where AI explanations are frequently shallow.
- **Deliverable:** CDC pipeline + event-time windowed aggregation + proven idempotent backfill.
- **Self-check:** ☐ I can explain event time vs processing time with a concrete failure.

## Week 5 — Multi-region & global systems

- **Learn (5h):** Why multi-region: latency, availability, data residency, disaster
  recovery — each implies a *different* architecture. Topologies: active-passive,
  active-active, read-local/write-global, per-region sharding by tenant. Cross-region data
  replication and the physics of the speed of light (~150ms round trip is not negotiable).
  Conflict resolution in active-active. Global traffic management (anycast, GeoDNS,
  latency-based routing). Data residency and the regulatory constraint that overrides
  every technical preference. Failover and — the harder part — **failback**. Split-brain.
- **Build (8h):** Design the flagship's multi-region architecture as a full document with
  C4 diagrams, a consistency analysis per data type, a failover runbook and a cost model.
  Then simulate what you can locally: two "regions" as separate stacks with async
  replication, and run a failover.
- **Reason (3h):** Which flagship data can be regionally partitioned and which cannot?
  (The ledger is the interesting case.) What is your RPO in an active-active setup, and
  what does a split-brain cost you in a financial ledger?
- **AI drill (2h):** *"Give me the strongest argument that this system should stay
  single-region."* It usually is the right answer — make it convince you properly.
- **Deliverable:** multi-region design doc + a simulated failover exercise.
- **Self-check:** ☐ I can explain why active-active is dangerous for a ledger.

## Week 6 — Chaos, load & failure testing

- **Learn (5h):** Chaos engineering method: steady-state hypothesis, blast-radius
  limitation, running in production (eventually), automated continuous chaos. Failure
  injection types: latency, error, resource exhaustion, dependency loss, clock skew,
  partial network partition. Load testing done properly: realistic profiles, ramp
  patterns, soak tests (memory leaks appear at hour 6, not minute 6), spike tests, stress
  to breaking point to find the actual limit. Capacity planning from measured data.
- **Build (8h):** Ship **v8**: run a full campaign against the flagship — a soak test, a
  spike test, a stress-to-failure test, and 8 chaos experiments each with a written
  hypothesis and result. Fix everything you break.
- **Reason (3h):** What is the flagship's actual breaking point, and what fails first? Was
  it what you predicted? Every wrong prediction is a gap in your mental model — list them.
- **AI drill (2h):** Before each chaos experiment, have AI predict the outcome, and predict
  it yourself. Score both against reality. Track whose mental model is better over 8 experiments.
- **Deliverable:** **v8 tagged** + `docs/reliability/chaos-report.md` with 8 experiments.
- **Self-check:** ☐ I know my system's breaking point as a number.

## Week 7 — Cost, sustainability & consolidation

- **Learn (5h):** Cost architecture: cost per request, per tenant, per feature; the
  compute/storage/network/managed-service breakdown; reserved vs spot vs on-demand
  reasoning; the cost of over-provisioning for peak. FinOps basics and cost attribution.
  Right-sizing from measurement. The cost of *complexity* — engineer hours as the largest
  line item. Sustainability and efficiency as an architecture characteristic. Technical
  debt as a quantified, prioritised register rather than a feeling.
- **Build (8h):** Build a cost model for the flagship: cost per transaction at 1×, 10×
  and 100× volume. Identify the top 3 cost drivers and reduce one measurably. Produce a
  quantified technical-debt register with interest estimates.
- **Reason (3h):** What is your cost per transaction, and how does it scale — linearly,
  sub-linearly, or worse? Which architectural choice costs the most, and would you make it
  again?
- **AI drill (2h):** *"Where is this architecture wasting money?"* Verify its top three
  claims against your actual cost model.
- **Deliverable:** cost model + one measured cost reduction + tech-debt register.
- **Self-check:** ☐ I can state cost per transaction and its scaling behaviour.

---

## Exit checklist — all must pass before 8B

- ☐ Architecture style comparison scored against measured characteristics; both sides argued
- ☐ 10× throughput improvement achieved and explained; new bottleneck named
- ☐ Zero-downtime deploys proven under load; RTO/RPO measured via a real restore
- ☐ CDC + event-time streaming with a proven idempotent backfill
- ☐ Multi-region design document with per-data-type consistency analysis and a failover exercise
- ☐ 8 chaos experiments with hypotheses; breaking point known as a number
- ☐ Cost model built; one cost driver measurably reduced; tech-debt register quantified
- ☐ v8 tagged
