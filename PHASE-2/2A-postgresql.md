# 2A — PostgreSQL Deep Dive

**Duration:** 6 weeks (108h) · **Months 3.0 – 4.4** · **Prereq:** 1B
**Flagship increment:** v1 — Monolith on a real schema

> **Outcome:** I can design a transactional schema, predict what the planner will do,
> read `EXPLAIN ANALYZE` fluently, and explain isolation-level behaviour with concrete
> anomalies I have reproduced myself.

**Calibration for mid-level:** you know SQL. Week 1 is a *speed run* to find gaps —
window functions, CTE materialisation, and lateral joins are the usual holes. The real
work starts at Week 2 with MVCC.

---

## Week 1 — SQL you thought you knew

- **Learn (5h):** Join algorithms (nested loop / hash / merge) and when the planner picks
  each. Aggregation, `GROUPING SETS`/`ROLLUP`, `FILTER`. CTEs — materialised vs inlined
  (PG12+ `NOT MATERIALIZED`), recursive CTEs. **Window functions**: frames, `ROWS` vs
  `RANGE`, `LAG`/`LEAD`, running balances. `LATERAL` joins. `DISTINCT ON`. NULL semantics
  in every operator. `ON CONFLICT`.
- **Build (8h):** Design the flagship's core schema properly: customers, accounts,
  transactions, ledger entries. Write the running-balance query with a window function,
  the top-N-per-group query, and the reconciliation query with a recursive CTE.
- **Reason (3h):** For your running-balance query — what happens at 10M rows? Which of
  your queries is O(n log n) and which is accidentally O(n²)?
- **AI drill (2h):** Have AI write your 3 hardest queries. Verify correctness against a
  seeded dataset *with edge cases* (nulls, ties, empty groups). Log where it broke.
- **Deliverable:** `db/schema.sql` + `db/queries/` with a seeded 1M-row dataset.
- **Self-check:** ☐ I can write a running balance with a window function from memory.

## Week 2 — MVCC, transactions & isolation levels

- **Learn (5h):** MVCC mechanics — xmin/xmax, tuple visibility, snapshots, transaction
  IDs, wraparound. Isolation levels in PostgreSQL specifically: Read Committed (default,
  statement-level snapshot), Repeatable Read (snapshot isolation), Serializable (SSI,
  predicate locks, serialization failures). The anomalies: dirty read, non-repeatable
  read, phantom, **write skew**, lost update. `SELECT FOR UPDATE / FOR SHARE / SKIP
  LOCKED / NOWAIT`.
- **Build (8h):** Write an integration test suite that **reproduces each anomaly** with
  two concurrent sessions, then shows the isolation level or lock that prevents it.
  This suite is your reference for the rest of the roadmap.
- **Reason (3h):** Write skew in a banking context: two withdrawals against a joint
  overdraft limit. Which isolation level fixes it? What does that cost under load?
- **AI drill (2h):** Describe a money-transfer flow and ask AI which isolation level to
  use. Almost every model over-recommends Serializable — make it justify the throughput cost.
- **Deliverable:** `IsolationAnomalyTest` — one test per anomaly, all green.
- **Self-check:** ☐ I can define write skew and give a banking example unprompted.

## Week 3 — Locks, deadlocks & contention

- **Learn (5h):** Lock levels (row / page / table), the full table-lock conflict matrix,
  advisory locks, `pg_locks`, lock queues and how one `ALTER TABLE` blocks everything
  behind it. Deadlock detection and the deadlock cycle. Hot-row contention.
  `lock_timeout`, `statement_timeout`, `idle_in_transaction_session_timeout`.
- **Build (8h):** Deliberately create a deadlock between two transactions and capture the
  PostgreSQL deadlock report. Then create a hot-row scenario (all traffic to one account)
  and measure throughput collapse. Fix it two ways: ordered locking, and a per-account
  queue/sharded counter.
- **Reason (3h):** Why do consistent lock ordering and short transactions prevent most
  deadlocks? What is the cost of `SKIP LOCKED` in a work-queue design?
- **AI drill (2h):** Give AI two transaction bodies and ask *"can these deadlock?"*
  Verify by running them. Try to construct a case where AI is confidently wrong.
- **Deliverable:** `docs/db/locking.md` with the deadlock report and the hot-row fix.
- **Self-check:** ☐ I can name the two most common causes of production deadlocks.

## Week 4 — Indexes & the query planner

- **Learn (5h):** B-tree structure, multicolumn index column order, index-only scans and
  the visibility map, covering indexes (`INCLUDE`), partial indexes, expression indexes,
  GIN/GiST/BRIN/Hash and when each wins. Planner internals: statistics, `n_distinct`,
  most-common-values, selectivity estimation, cost constants, why the planner picks a
  seq scan. `EXPLAIN` vs `EXPLAIN ANALYZE` vs `BUFFERS`, reading nested plan nodes,
  rows-estimated vs rows-actual as the key diagnostic signal.
- **Build (8h):** Take the 5 slowest queries from Week 1 at 1M+ rows. For each: read the
  plan, form a hypothesis, add an index, re-measure. Then **delete an index the planner
  ignores** and explain why it was useless.
- **Reason (3h):** When is a sequential scan the *correct* plan? Why did an index make
  one of your queries slower? What is the write-amplification cost of each index you added?
- **AI drill (2h):** Paste an `EXPLAIN ANALYZE` output and ask AI to diagnose. Then do it
  yourself. Compare — AI often misses the estimate-vs-actual row discrepancy that matters most.
- **Deliverable:** `docs/db/query-tuning.md` — 5 queries, before/after plans and timings.
- **Self-check:** ☐ I can spot a bad row estimate in a plan in under 30 seconds.

## Week 5 — Partitioning, replication, pooling & vacuum

- **Learn (5h):** Declarative partitioning (range/list/hash), partition pruning,
  constraint exclusion, partition-wise joins, when partitioning hurts. Replication:
  WAL, streaming replication, sync vs async, replication lag, read-replica routing and
  **read-after-write** hazards, logical replication. Connection pooling: why PostgreSQL
  connections are expensive, HikariCP sizing, PgBouncer pooling modes (session /
  transaction / statement) and what breaks in each. `VACUUM`/`ANALYZE`, autovacuum
  tuning, bloat, transaction-ID wraparound.
- **Build (8h):** Partition the transactions table by month. Prove pruning works in the
  plan. Add a read replica in Docker, route reads to it, then **demonstrate a
  read-after-write bug** and fix it (sticky-primary for N seconds, or read-your-writes token).
- **Reason (3h):** Which flagship queries can safely go to a replica and which cannot?
  What is your maximum tolerable replication lag, and how would you alert on it?
- **AI drill (2h):** *"What are the operational downsides of partitioning this table?"*
  Push until it gives you the non-obvious ones (index maintenance, planning time, DDL locks).
- **Deliverable:** partitioned table + replica routing + a read-after-write test.
- **Self-check:** ☐ I can explain why a transaction-pooling PgBouncer breaks prepared statements.

## Week 6 — Production concerns & consolidation

- **Learn (5h):** Slow-query detection (`pg_stat_statements`, `auto_explain`), connection
  exhaustion patterns, transaction boundaries in Spring (`@Transactional` propagation,
  read-only, timeout, rollback rules), the **transaction-per-request antipattern**,
  long-running transactions blocking vacuum, N+1 queries in JPA, batch inserts,
  `OptimisticLockException` vs pessimistic locking, schema migrations without downtime
  (Flyway/Liquibase, expand-contract pattern).
- **Build (8h):** Ship **v1**: the flagship as a real monolith on this schema. Add
  `pg_stat_statements`, a slow-query dashboard, Flyway migrations, and an expand-contract
  migration executed with zero downtime under load. Run the k6 profile from 1B and record
  the new baseline.
- **Reason (3h):** Where are the transaction boundaries in the flagship, and is each one
  as short as it can be? What happens when the connection pool is exhausted — what does
  the user see?
- **AI drill (2h):** Ask AI to review your `@Transactional` placement for correctness *and*
  for lock-hold duration. Then verify by measuring actual transaction durations.
- **Deliverable:** **v1 tagged** + `docs/db/production-runbook.md`.
- **Self-check:** ☐ I can perform an online column rename with expand-contract from memory.

---

## Exit checklist — all must pass before 2B

- ☐ Anomaly test suite green: dirty read, non-repeatable read, phantom, write skew, lost update
- ☐ I can read `EXPLAIN ANALYZE` and diagnose from rows-estimated vs rows-actual
- ☐ 5 queries measurably tuned with documented before/after plans
- ☐ Partitioning works with proven pruning; read-replica read-after-write bug reproduced and fixed
- ☐ A zero-downtime migration was executed under load
- ☐ Connection pool sized from measurement, not from a blog post
- ☐ v1 tagged; k6 baseline re-recorded

**If 2+ fail:** 1-week repair sprint on MVCC + planner. These two are load-bearing for
Phase 3 and Phase 5.
