# 3B — Distributed Transactions & Observability

**Duration:** 6 weeks (108h) · **Months 7.6 – 9.0** · **Prereq:** 3A
**Flagship increment:** v3 — Observable & transactional

> **Outcome:** I can implement a saga with compensation that survives every failure
> mode I can name, and I can debug a distributed request end-to-end from a trace
> instead of by guessing.

---

## Week 1 — 2PC, and why you probably can't use it

- **Learn (5h):** Two-phase commit: prepare/commit protocol, the coordinator, the
  blocking problem when the coordinator dies, XA transactions, why 2PC is rare in modern
  microservices (availability cost, resource holding, no support across HTTP/Kafka).
  3PC and why it doesn't save you. FLP impossibility, intuitively.
- **Build (8h):** Implement a naive 2PC coordinator across two local resources. Then kill
  the coordinator between prepare and commit and observe the participants blocked
  in-doubt. Document the recovery procedure.
- **Reason (3h):** When *is* 2PC the right answer? (Single DB with multiple schemas; XA
  with a JMS broker; tightly-coupled legacy.) Why does it fail as a default in a system
  with Kafka in the path?
- **AI drill (2h):** Ask AI to design a distributed transaction across three services. If
  it reaches for 2PC, ask it to defend that against the availability argument.
- **Deliverable:** 2PC demo + failure log + `docs/architecture/why-not-2pc.md`.
- **Self-check:** ☐ I can explain the coordinator-failure blocking problem clearly.

## Week 2 — Sagas: choreography

- **Learn (5h):** Saga pattern origins. Choreography: each service reacts to events and
  emits its own. Compensating transactions and why compensation ≠ rollback (semantic
  undo, visible intermediate state). ACD instead of ACID. Isolation anomalies in sagas:
  lost update, dirty read, fuzzy read — and countermeasures (semantic lock, commutative
  updates, pessimistic view, re-read value, version file, by-value strategy).
- **Build (8h):** Implement the source doc's scenario — **Payment → Order → Inventory →
  Notification** — as a choreographed saga on Kafka in the flagship. Include compensations.
- **Reason (3h):** For each step, write the compensation and identify what is *not*
  compensable (a sent notification, a real bank transfer). How do you design around
  non-compensable steps? (Answer: order them last, or make them provisional.)
- **AI drill (2h):** Give AI the saga and ask for every state the system can be observed
  in mid-flight. Verify by writing tests for the three worst ones.
- **Deliverable:** choreographed saga with compensations + state-diagram.
- **Self-check:** ☐ I can name a non-compensable step and my strategy for it.

## Week 3 — Sagas: orchestration + failure analysis

- **Learn (5h):** Orchestration: a saga coordinator/state machine, persistent saga state,
  timeouts per step, retries per step, compensation ordering, saga recovery on
  coordinator restart. Orchestration vs choreography trade-offs (coupling vs
  observability vs cyclic dependencies). State-machine libraries vs hand-rolled. Process
  managers. Inbox pattern on the consumer side.
- **Build (8h):** Reimplement the same saga with an **orchestrator** whose state lives in
  PostgreSQL. Then run the source document's full failure analysis as automated tests:
  timeout · duplicate request · duplicate event · partial failure · service restart ·
  database failure · Kafka failure. All seven must be covered.
- **Reason (3h):** Now that you've built both — which one would you choose for the
  flagship and why? What specifically made you change your mind (if you did)? This goes
  in the ADR.
- **AI drill (2h):** *"Give me the strongest argument for choreography over my
  orchestrator."* Then write the counter-argument. Both go in the ADR.
- **Deliverable:** orchestrated saga + 7-failure test matrix + `docs/adr/0006-saga-style.md`.
- **Self-check:** ☐ I can recover a saga after an orchestrator crash mid-flight.

## Week 4 — Metrics: RED, USE & business signals

- **Learn (5h):** Metric types (counter, gauge, histogram, summary) and why histograms
  beat averages. **RED** (Rate, Errors, Duration) for request-driven services. **USE**
  (Utilisation, Saturation, Errors) for resources. Business metrics (transfers/sec,
  failed-payment rate, reconciliation break count). Cardinality — the thing that will
  bankrupt your metrics bill. Micrometer + Prometheus, histogram buckets vs native
  histograms, recording rules, PromQL fundamentals.
- **Build (8h):** Instrument the flagship with RED per endpoint, USE per resource
  (connection pool, thread pool, Kafka consumer lag), and 5 business metrics. Build one
  Grafana dashboard that answers *"is the system healthy?"* in under 10 seconds.
- **Reason (3h):** Which single metric would you look at first at 3am? Why is average
  latency actively misleading? Which of your labels has unbounded cardinality?
- **AI drill (2h):** Ask AI what to instrument. Then check: did it mention consumer lag,
  saturation, and cardinality? Score its completeness.
- **Deliverable:** Grafana dashboard + `docs/observability/metrics-catalog.md`.
- **Self-check:** ☐ I can write PromQL for p99 latency by endpoint from memory.

## Week 5 — Logging & distributed tracing

- **Learn (5h):** Structured logging (JSON via Logback), log levels as a real contract
  (what belongs at ERROR vs WARN), MDC, correlation ID vs trace ID vs span ID, what
  **never** to log (PII, card data, tokens — this matters in Phase 5). Sampling and cost.
  **OpenTelemetry**: traces, spans, context propagation (W3C traceparent), baggage,
  auto vs manual instrumentation, propagation across Kafka (headers), span attributes and
  events, tail-based sampling. Logs↔traces↔metrics correlation (exemplars).
- **Build (8h):** Add OpenTelemetry to the flagship end-to-end: HTTP → service → DB →
  Kafka producer → consumer → DB. Verify the trace stays connected **across the Kafka
  hop** (this is the part everyone gets wrong). Wire trace IDs into every log line.
- **Reason (3h):** Take one production-style incident from your own experience. Which
  span would have shown you the cause? What is missing from your instrumentation?
- **AI drill (2h):** Give AI a trace waterfall and ask for the bottleneck. Then diagnose
  it yourself. Note whether AI reasoned about the critical path or just picked the longest span.
- **Deliverable:** connected end-to-end trace through Kafka, with a screenshot in docs.
- **Self-check:** ☐ I can propagate trace context through a Kafka message manually.

## Week 6 — SLOs, alerting & consolidation

- **Learn (5h):** SLI → SLO → SLA. Choosing good SLIs (availability, latency, correctness,
  freshness). Error budgets and burn-rate alerting (multi-window multi-burn-rate).
  Symptom-based vs cause-based alerting. Alert fatigue and the "every alert must be
  actionable" rule. Runbooks. On-call practice, incident response, blameless postmortems.
- **Build (8h):** Ship **v3**: define 4 SLOs for the flagship with error budgets, build
  multi-burn-rate alerts, write 3 runbooks, then run a **game day** — inject a failure
  without warning, respond using only your dashboards and runbooks, and write the postmortem.
- **Reason (3h):** What is your availability SLO and what does it permit per month in
  minutes? Which alert would you delete today because nobody acts on it?
- **AI drill (2h):** Have AI write your postmortem's "contributing factors" section from
  your timeline. Then rewrite it yourself — compare depth of causal reasoning.
- **Deliverable:** **v3 tagged** + SLO doc + 3 runbooks + 1 real postmortem.
- **Self-check:** ☐ I can explain multi-burn-rate alerting and why single-threshold alerts fail.

---

## Exit checklist — all must pass before 4A

- ☐ Saga implemented both ways; ADR-0006 defends the chosen style
- ☐ All 7 failure scenarios from the source document covered by automated tests
- ☐ RED + USE + business metrics on one dashboard that answers "healthy?" in 10 seconds
- ☐ End-to-end trace connected across the Kafka hop, correlated with logs
- ☐ 4 SLOs with error budgets and burn-rate alerts
- ☐ One game day run and one postmortem written
- ☐ v3 tagged

**Phase 3 complete → Month 9.** Monthly review + re-score. You are now halfway to
"Senior Backend Engineer" on the roadmap's terms.
