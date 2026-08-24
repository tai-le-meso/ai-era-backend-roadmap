# 2C — Kafka & Event-Driven Architecture

**Duration:** 4 weeks (72h) · **Months 5.1 – 6.0** · **Prereq:** 2A, 2B
**Flagship increment:** v2 — Event-driven

> **Outcome:** I can build an event-driven system that survives duplicate messages,
> consumer restarts, rebalances, and schema evolution — and I can explain exactly what
> "exactly-once" does and does not mean.

---

## Week 1 — Fundamentals & the log

- **Learn (5h):** The log as a data structure. Topics, partitions, offsets, segments,
  retention (time/size/compaction). Producers: batching, `linger.ms`, `acks=0/1/all`,
  `min.insync.replicas`, compression. Consumers: consumer groups, assignment, offset
  commit (auto vs manual, sync vs async), `max.poll.records`/`max.poll.interval.ms`
  and the poll-loop contract. Replication: leader/follower, ISR, unclean leader election.
  KRaft vs ZooKeeper.
- **Build (8h):** Kafka + Schema Registry in Docker Compose. Publish `TransferInitiated`
  from the flagship; consume it in a notification service. Then break it deliberately:
  kill a broker mid-produce with `acks=1` and observe data loss; repeat with
  `acks=all` + `min.insync.replicas=2`.
- **Reason (3h):** Draw the durability guarantee for each `acks` setting. What exactly is
  lost on unclean leader election? Why does `acks=all` alone not guarantee no loss?
- **AI drill (2h):** *"What are the failure modes of this producer config?"* Verify each
  claimed failure mode by actually reproducing it.
- **Deliverable:** working produce/consume path + `docs/kafka/durability-experiments.md`.
- **Self-check:** ☐ I can state what `acks=all` + `min.insync.replicas=1` actually guarantees.

## Week 2 — Delivery semantics, ordering, retries & DLT

- **Learn (5h):** At-most-once / at-least-once / effectively-once. **Idempotent producer**
  (PID + sequence numbers) — what it fixes and its scope. Transactions & `read_committed`
  — and why "exactly-once" is only end-to-end within Kafka, not across your database.
  Ordering: guaranteed per-partition only; partition key design; the hot-partition
  problem. Retry topics, blocking vs non-blocking retry, exponential backoff, Dead Letter
  Topic design, poison-pill handling. Consumer rebalancing: eager vs cooperative sticky,
  static membership, `session.timeout.ms`, the stop-the-world rebalance.
- **Build (8h):** Make every flagship consumer **idempotent** (dedupe on event ID in the
  DB). Add a retry topic + DLT with backoff. Write chaos tests: duplicate delivery,
  out-of-order within a key, consumer crash mid-processing, rebalance storm.
- **Reason (3h):** Choose the partition key for transfers. What does it guarantee? What
  goes wrong if one account is 40% of volume? Why is at-least-once + idempotency the
  right default over Kafka transactions?
- **AI drill (2h):** Ask AI to "make this consumer exactly-once". Interrogate every claim
  — most answers conflate Kafka transactions with end-to-end exactly-once.
- **Deliverable:** idempotent consumers + retry/DLT + chaos test suite.
- **Self-check:** ☐ I can explain why exactly-once across Kafka *and* PostgreSQL requires
  the outbox/dedupe pattern, not Kafka transactions.

## Week 3 — Schema Registry, Avro & Kafka Streams

- **Learn (5h):** Avro schemas, the schema registry, subject naming strategies,
  compatibility modes (backward / forward / full / transitive) and what each allows you
  to change. Schema evolution rules: adding fields with defaults, removing fields,
  renaming (you can't), enums. Event design: event-carried state transfer vs thin events
  vs notification events; event versioning; the "events are your public API" rule.
  Kafka Streams: KStream/KTable duality, state stores, windowing, joins, exactly-once
  processing guarantees, interactive queries.
- **Build (8h):** Move all flagship events to Avro + Schema Registry with `BACKWARD`
  compatibility enforced in CI. Perform a real schema evolution (add a field) and prove
  old consumers still work. Build one Kafka Streams job: rolling per-account transaction
  totals in a windowed aggregate.
- **Reason (3h):** Which compatibility mode fits a system where consumers deploy
  independently and why? Should your event carry the full account state or just the
  delta? Argue both.
- **AI drill (2h):** Give AI two versions of a schema and ask if the change is backward
  compatible. Verify with the registry's compatibility API. Find a case it gets wrong.
- **Deliverable:** Avro schemas in a registry, CI compatibility gate, one Streams job.
- **Self-check:** ☐ I can name three schema changes that break backward compatibility.

## Week 4 — Event-driven architecture, Outbox & consolidation

- **Learn (5h):** Event-driven architecture styles: choreography vs orchestration and
  their observability/coupling trade-offs. The **dual-write problem** and why it is
  unavoidable without outbox. **Transactional Outbox** — table design, relay via polling
  vs CDC (Debezium), ordering guarantees, cleanup. Inbox pattern for consumer-side
  dedupe. Event sourcing concepts: event store, projections, replay, snapshots — and an
  honest account of when event sourcing is over-engineering. CQRS.
- **Build (8h):** Ship **v2**: implement the Outbox pattern in the flagship (write
  business change + outbox row in one PostgreSQL transaction; relay publishes to Kafka).
  Prove it: kill the app between DB commit and publish, restart, and show the event still
  arrives exactly once (with consumer dedupe). Then draw and commit the full event flow.
- **Reason (3h):** Write out the dual-write failure with a timeline diagram. Where does
  choreography make your system undebuggable? What would push you to orchestration
  (answered properly in 3B)?
- **AI drill (2h):** *"Give me the strongest argument against event-driven architecture
  for this system."* Then write your rebuttal. Both go in the ADR.
- **Deliverable:** **v2 tagged** + `docs/adr/0005-event-driven-outbox.md` + event-flow diagram.
- **Self-check:** ☐ I can explain the dual-write problem in 60 seconds with a diagram.

---

## Exit checklist — all must pass before 3A

- ☐ Durability experiments run: I have observed real data loss with weak `acks`
- ☐ Every consumer is idempotent, with a duplicate-delivery test proving it
- ☐ Retry topic + DLT working, with poison-pill handling
- ☐ Avro + Schema Registry with a CI compatibility gate; one real evolution performed
- ☐ Outbox pattern implemented and proven by a kill-between-commit-and-publish test
- ☐ One Kafka Streams job running with a state store
- ☐ ADR-0005 written; I can argue both for and against event-driven here
- ☐ v2 tagged

**Phase 2 complete → Month 6.** Take a rest day. Then do a full monthly review and
re-score all nine areas.
