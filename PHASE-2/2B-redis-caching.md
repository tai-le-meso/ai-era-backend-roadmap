# 2B — Redis, Caching & Idempotency

**Duration:** 3 weeks (54h) · **Months 4.4 – 5.1** · **Prereq:** 2A
**Flagship increment:** v1.5 — cached

> **Outcome:** I can answer the roadmap's key question — *when should Redis NOT be
> used?* — with reasons, and I can implement caching, distributed locking, rate
> limiting and idempotency without introducing correctness bugs.

**Why only 3 weeks:** Redis is small in surface area and large in footguns. The value
here is judgment, not API coverage. Spend the time on the failure modes.

---

## Week 1 — Data structures, cache patterns & invalidation

- **Learn (5h):** Redis data types and their real cost (strings, hashes, lists, sets,
  sorted sets, HyperLogLog, bitmaps). Single-threaded execution model and why one slow
  command stalls everything (`KEYS` is banned; use `SCAN`). Persistence: RDB vs AOF and
  what you lose on crash. Eviction policies (`allkeys-lru`, `volatile-ttl`, ...) and
  `maxmemory` behaviour. Cache patterns: cache-aside, read-through, write-through,
  write-behind. TTL strategy, jitter, cache stampede, thundering herd, negative caching.
  **Invalidation**: the hard problem — TTL vs event-driven vs versioned keys.
- **Build (8h):** Add Redis cache-aside to the flagship's account/balance reads. Then
  deliberately create a stale-balance bug (cache not invalidated on write), demonstrate
  it in a test, and fix it three ways: short TTL, explicit invalidation, versioned key.
  Add stampede protection (probabilistic early expiry or a lock).
- **Reason (3h):** **Answer the key question in writing:** when should Redis NOT be used?
  (Candidates: as a source of truth; for data you cannot recompute; when the cache-miss
  path can't survive; when consistency requirements exceed what TTLs give you; when the
  DB was fast enough and you added a distributed system for nothing.)
- **AI drill (2h):** *"Give me the strongest argument that adding Redis here is a
  mistake."* Then argue back. Keep the transcript — this is architecture-review practice.
- **Deliverable:** cache layer + stale-read test + `docs/adr/0003-caching-strategy.md`.
- **Self-check:** ☐ I can explain cache stampede and two independent mitigations.

## Week 2 — Distributed locks, atomic operations & rate limiting

- **Learn (5h):** Why `SETNX` alone is not a lock (no fencing, no ownership check on
  release). Lock with TTL + owner token + Lua release. Redlock and **the Kleppmann
  critique** — read both sides. Fencing tokens as the actual correct solution. Lua
  scripting for atomicity, `MULTI/EXEC` vs Lua, `WATCH`/optimistic locking. Rate limiting
  algorithms: fixed window, sliding window log, sliding window counter, token bucket,
  leaky bucket — with Redis implementations.
- **Build (8h):** Implement a correct distributed lock (TTL + owner + Lua release) and
  write a test that shows the *incorrect* version releasing another holder's lock.
  Implement a token-bucket rate limiter in Lua and load-test it for accuracy under
  concurrency.
- **Reason (3h):** Under what failure (GC pause, network partition, clock skew) does your
  lock give mutual-exclusion violation? Why can no lock with a TTL be fully safe without
  fencing? Where in the flagship do you need a lock and where is a DB constraint better?
- **AI drill (2h):** Ask AI for a "distributed lock in Redis". Find every safety hole in
  what it produces. This is one of the most reliably wrong things AI generates.
- **Deliverable:** lock + rate limiter with correctness tests; written failure analysis.
- **Self-check:** ☐ I can explain fencing tokens and why TTL alone is insufficient.

## Week 3 — Idempotency, Streams/Pub-Sub & consolidation

- **Learn (5h):** Idempotency keys — where the key comes from, storage, TTL, the
  in-flight/completed/failed state machine, returning the original response on replay,
  and why "check then insert" is a race (use a unique constraint). Redis Streams
  (consumer groups, `XACK`, pending entries list) vs Pub/Sub (fire-and-forget, no
  durability) vs Kafka — the comparison that matters. Redis Cluster basics: hash slots,
  cross-slot operations, resharding.
- **Build (8h):** Ship **v1.5**: idempotent `POST /transfers` with an idempotency-key
  table in **PostgreSQL** (not Redis — argue this in the ADR) plus a Redis fast path.
  Write a concurrency test firing the same key 50× in parallel and asserting exactly one
  effect. Add per-customer rate limiting to the API.
- **Reason (3h):** Why should the idempotency record live in the same transaction as the
  business effect? What breaks if it lives only in Redis? Compare Redis Streams vs Kafka
  for the flagship's needs and justify the choice you'll make in 2C.
- **AI drill (2h):** Have AI design your idempotency scheme, then attack it yourself with
  four scenarios: duplicate request, concurrent duplicates, retry after partial failure,
  retry after response lost.
- **Deliverable:** **v1.5 tagged** + `docs/adr/0004-idempotency.md`.
- **Self-check:** ☐ I can explain why an idempotency check outside the business
  transaction is a race condition.

---

## Exit checklist — all must pass before 2C

- ☐ A written, defensible answer to "when should Redis NOT be used?"
- ☐ Cache-aside with invalidation and stampede protection, with a stale-read test
- ☐ Correct distributed lock + a test proving the naive version is unsafe
- ☐ Token-bucket rate limiter validated under concurrent load
- ☐ Idempotent transfer endpoint passing a 50-parallel-duplicate test
- ☐ ADRs 0003 and 0004 written and defensible
- ☐ v1.5 tagged

**If 2+ fail:** extend by 1 week focusing on idempotency — it is a prerequisite for
every remaining phase.
