# 4B — System Design & Architecture Practice

**Duration:** 6 weeks (108h) · **Months 10.6 – 12.0** · **Prereq:** 4A
**Flagship increment:** v4.5 — Service boundaries extracted

> **Outcome:** I can run a 45-minute whiteboard design session end-to-end —
> requirements, capacity, API, data model, components, failure analysis, trade-offs —
> and defend every choice under challenge.

**Format note:** this sub-roadmap is deliberately drill-heavy. You do **two full design
exercises per week**, timed, written up. Volume plus feedback is what builds this skill.

---

## The design framework (use it every single time)

1. **Requirements** — functional; non-functional (scale, latency, availability,
   consistency, security, cost); explicitly out of scope
2. **Capacity estimation** — QPS, read/write ratio, storage/day, bandwidth, peak vs average
3. **API design** — endpoints/events, idempotency, pagination, versioning, errors
4. **Data model** — entities, access patterns, storage choice per access pattern
5. **High-level components** — draw it; name every arrow's protocol
6. **Deep dive** — the 1–2 hardest parts
7. **Failure analysis** — what fails, blast radius, recovery, partial-failure behaviour
8. **Trade-offs** — Option A vs Option B: benefits, drawbacks, cost, operational complexity
9. **Evolution** — what changes at 10× and at 100×

---

## Week 1 — Requirements, capacity & the API

- **Learn (5h):** Extracting non-functional requirements from vague asks. Availability
  arithmetic (what 99.9% vs 99.99% means in minutes, and what each costs). Latency
  budgets. Back-of-envelope numbers every engineer should know (memory vs SSD vs network
  vs cross-region latency; rough throughput of a single PostgreSQL node). REST design
  (URL structure, verbs, status codes, RFC 9457 errors, pagination: offset vs cursor,
  versioning strategies), OpenAPI-first workflow, gRPC and GraphQL — when each wins.
- **Build (8h):** **Design drills:** (1) URL shortener, (2) rate limiter as a service.
  Full framework, timed 45 min each, then 90 min written write-up. Also: convert the
  flagship to OpenAPI-first with generated clients.
- **Reason (3h):** For each drill, write the three questions you failed to ask in the
  requirements phase. That list is your real weakness.
- **AI drill (2h):** Give AI your design and the prompt: *"Act as a staff engineer in an
  architecture review. Find three serious problems."* Then defend or concede each.
- **Deliverable:** 2 design write-ups + OpenAPI-first flagship.
- **Self-check:** ☐ I can estimate QPS and storage for any system in under 5 minutes.

## Week 2 — Data modelling & storage selection

- **Learn (5h):** Choosing storage by **access pattern**, not by fashion: relational,
  document, key-value, wide-column, time-series, search, object, graph. Polyglot
  persistence and its operational tax. Denormalisation trade-offs. Read vs write
  optimisation, materialised views, secondary indexes at scale. Data lifecycle: hot/warm/
  cold, archival, retention, GDPR deletion. Multi-tenancy models (shared schema /
  schema-per-tenant / DB-per-tenant) and their isolation vs cost curve.
- **Build (8h):** **Design drills:** (3) news feed, (4) chat/messaging system. Then: add a
  search read model (OpenSearch or PostgreSQL full-text) to the flagship, fed by domain events.
- **Reason (3h):** For the flagship, justify every storage engine you use. Which one could
  you remove? What is the operational cost per additional datastore (backup, monitoring,
  upgrade, expertise)?
- **AI drill (2h):** *"Which of these five databases is right and why?"* Make AI state the
  access pattern that decides it, not the feature list.
- **Deliverable:** 2 design write-ups + search read model.
- **Self-check:** ☐ I choose storage from access patterns, and can say so out loud.

## Week 3 — Communication, caching & scaling patterns

- **Learn (5h):** Sync vs async communication and how to decide. Request/response vs
  events vs streaming. API gateway responsibilities, BFF pattern, service mesh concepts.
  Caching layers end-to-end: client → CDN → gateway → application → database, and where
  invalidation is hardest. Scaling: vertical vs horizontal, stateless services, sticky
  sessions and why to avoid them, load-balancing algorithms, autoscaling signals (why CPU
  is often the wrong one), queue-based load levelling.
- **Build (8h):** **Design drills:** (5) payment system, (6) notification/fan-out system.
  Then: add an API gateway to the flagship with auth, rate limiting and request tracing.
- **Reason (3h):** For every arrow in your flagship diagram: should it be sync or async?
  Write the reason. Where does a sync call create a distributed monolith?
- **AI drill (2h):** *"What breaks in my design at 100× traffic?"* Verify its top claim
  with an actual load test if you can.
- **Deliverable:** 2 design write-ups + gateway in front of the flagship.
- **Self-check:** ☐ I can justify sync vs async for any given integration.

## Week 4 — Security & multi-tenancy in design

- **Learn (5h):** AuthN vs AuthZ. OAuth2 flows, OIDC, JWT (validation, rotation, why not
  to put authorisation decisions in a long-lived token), session vs token, mTLS,
  service-to-service auth. Secrets management, key rotation, envelope encryption,
  encryption at rest/in transit/in use, tokenisation vs encryption for card data. OWASP
  API Top 10. Threat modelling (STRIDE). Audit logging as a design requirement. Rate
  limiting as an abuse control. (This is the on-ramp to Phase 5's compliance work.)
- **Build (8h):** **Design drills:** (7) multi-tenant SaaS platform, (8) file-storage/CDN
  system. Then: threat-model the flagship with STRIDE and fix the top 3 findings.
- **Reason (3h):** Where does the flagship trust input it shouldn't? What is the blast
  radius if one service's credentials leak? What data would be catastrophic to log?
- **AI drill (2h):** *"Threat-model this design."* Compare against your STRIDE output.
  Note which categories AI systematically under-covers (usually repudiation and
  elevation-of-privilege).
- **Deliverable:** 2 design write-ups + threat model + 3 security fixes.
- **Self-check:** ☐ I can run STRIDE over a diagram without a cheat sheet.

## Week 5 — Trade-off articulation & extracting a service

- **Learn (5h):** How to present a design: audience-aware framing (exec / peer / junior),
  the C4 model (context, container, component, code), diagram hygiene, arc42 / design-doc
  templates. Comparing options honestly: cost, operational complexity, team capability,
  reversibility (one-way vs two-way doors). Estimating operational cost. Migration
  strategies: strangler fig, branch-by-abstraction, parallel run, dark launch, expand-contract.
- **Build (8h):** Ship **v4.5**: extract **two** bounded contexts from the modular
  monolith into standalone services using the strangler-fig pattern, with a parallel-run
  verification phase. Document the migration plan before you start it.
- **Reason (3h):** What did extraction cost you (latency, transactions, deployment
  complexity, debugging)? Was it worth it here? Would you extract a third?
- **AI drill (2h):** Present your extraction plan to AI as a skeptical VP of Engineering
  asking *"why should we spend a quarter on this?"* Answer in business terms, not technical ones.
- **Deliverable:** **v4.5 tagged** + C4 diagrams + migration plan + `docs/adr/0008-service-extraction.md`.
- **Self-check:** ☐ I can explain a technical trade-off in cost-and-risk language.

## Week 6 — Design under constraints + consolidation

- **Learn (5h):** Designing with real constraints: legacy systems, fixed budgets, small
  teams, regulatory limits, vendor lock-in, data residency. Build vs buy. Cost modelling
  (per-request cost, cost per tenant). Evolutionary architecture and fitness functions.
  Architecture characteristics prioritisation — you can't have all of them.
- **Build (8h):** **Design drills:** (9) core banking / ledger system (dry run for Phase
  5), (10) a system of your own choosing under a hard constraint (e.g. "must run
  on-premise for a regulated client, 3 engineers, 6 months"). Then: add fitness functions
  to the flagship's CI (ArchUnit rules, performance regression gate, dependency rules).
- **Reason (3h):** Review all 10 design write-ups. What mistake recurs? That is the thing
  to drill in Phase 8.
- **AI drill (2h):** Feed AI all 10 write-ups and ask for the pattern in your weaknesses.
  Then judge whether it found the real one.
- **Deliverable:** 2 design write-ups + fitness functions in CI + a self-assessment note.
- **Self-check:** ☐ I have 10 completed design write-ups I would show an interviewer.

---

## Exit checklist — all must pass before 5A

- ☐ 10 timed system-design exercises completed and written up using the framework
- ☐ Capacity estimation done from memory, without a calculator crutch
- ☐ Flagship is OpenAPI-first with generated clients
- ☐ Two contexts extracted via strangler fig with a parallel-run verification
- ☐ C4 diagrams exist and are current
- ☐ STRIDE threat model done; top findings fixed
- ☐ Fitness functions running in CI
- ☐ ADR-0008 written; v4.5 tagged

**Phase 4 complete → Month 12. Halfway.** Do an extended review: re-score all nine
areas, compare against month 0, and decide whether Phase 5 (domain) or Phase 6 (AI)
should come next based on where your work is heading.
