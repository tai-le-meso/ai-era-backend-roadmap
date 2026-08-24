# AI-Era Backend Engineer — Master Roadmap (24 Months)

> **Owner:** Tai Le · **Start:** ___________ · **Target end:** ___________
> **Cadence:** 3 hours/day × 6 days/week = **18h/week ≈ 78h/month**
> **Total investment:** ~104 weeks × 18h = **~1,870 hours**

---

## 0. How to read this roadmap

The source PDF defines **8 phases of 3 months each**. Three months is too long a unit
to hold in your head, so every phase here is split into **sub-roadmaps of 6–7 weeks
(max 2 months)**. There are **17 sub-roadmaps** total.

Each sub-roadmap is a self-contained file with:

- a **single outcome sentence** ("at the end I can ___")
- a **week-by-week plan** (Learn / Build / Reason / AI-drill / Deliverable)
- an **exit checklist** you must pass before moving on
- the **flagship-project increment** it contributes

**Day-by-day breakdown is deliberately NOT written in advance.** You expand one week
into six 3-hour sessions on the Sunday before that week starts, using
`DAILY-EXPANSION-KIT.md`. Reason: a plan written 18 months ahead is fiction; a plan
written 3 days ahead is a schedule.

### Time budget per week (18h)

| Block | Hours | What it is |
|---|---|---|
| **Learn** | 5h | Docs, source code, papers, book chapters. Not video tutorials. |
| **Build** | 8h | Code that runs, with tests. Committed to the flagship repo. |
| **Reason** | 3h | Written answers to trade-off questions. This is the differentiator. |
| **AI drill** | 2h | Using AI as tutor / adversary / reviewer — and grading its output. |

> The **Reason** block is the one you will be tempted to skip. It is the one that
> converts "I used Kafka" into "I can defend this design in an architecture review."

---

## 1. Vision (from the source document)

**Target role:** AI-Augmented Backend Engineer → Senior Backend Engineer → Solution Architect / Staff Engineer

**Core principle:**
> *Do not compete with AI at writing code. Become the engineer who can define the
> problem, design the system, validate AI-generated solutions, and make sound
> engineering trade-offs.*

**North star at month 24:**
> *"I can take an ambiguous business problem, model the domain, design a reliable
> system, implement it, validate it under realistic conditions, explain the
> trade-offs, and use AI to multiply my engineering productivity."*

---

## 2. Overview map — 8 phases, 17 sub-roadmaps, 104 weeks

| # | Sub-roadmap | Weeks | Months | Primary outcome |
|---|---|---|---|---|
| **1A** | Modern Java Mastery | 7 | 0.0–1.6 | Write Java that a senior reviewer cannot fault |
| **1B** | JVM Internals, Concurrency & Performance | 6 | 1.6–3.0 | Explain *why* the JVM behaved that way, with numbers |
| **2A** | PostgreSQL Deep Dive | 6 | 3.0–4.4 | Design & tune a schema under real concurrent load |
| **2B** | Redis, Caching & Idempotency | 3 | 4.4–5.1 | Know when Redis is right — and when it is a bug |
| **2C** | Kafka & Event-Driven Architecture | 4 | 5.1–6.0 | Build an event system that survives duplicates & restarts |
| **3A** | Reliability, Consistency & Coordination | 7 | 6.0–7.6 | Reason about failure before it happens |
| **3B** | Distributed Transactions & Observability | 6 | 7.6–9.0 | Saga + outbox + traces you can actually debug with |
| **4A** | Domain-Driven Design | 7 | 9.0–10.6 | Model a domain, not a database |
| **4B** | System Design & Architecture Styles | 6 | 10.6–12.0 | Run a whiteboard design session end-to-end |
| **5A** | Banking Domain & Ledger Engineering | 7 | 12.0–13.6 | Build a correct double-entry ledger |
| **5B** | Payments, Money, Compliance & Risk | 6 | 13.6–15.0 | Speak fintech fluently with product & risk people |
| **6A** | LLM Fundamentals & RAG | 7 | 15.0–16.6 | Ship retrieval that is measurably good, not vibes-good |
| **6B** | AI Backend Architecture | 6 | 16.6–18.0 | Design the AI gateway, guardrails, evals, cost controls |
| **7A** | Agents & the MCP / Tool Ecosystem | 7 | 18.0–19.6 | Know when an agent is right and when it is over-engineering |
| **7B** | AI-Native SDLC & Engineering Judgment | 6 | 19.6–21.0 | Use AI across the whole lifecycle without losing control |
| **8A** | Architecture Design at Scale | 7 | 21.0–22.6 | Design HA, high-throughput, multi-region systems |
| **8B** | Decision-Making, Communication & Capstone | 6 | 22.6–24.0 | ADRs, architecture reviews, and a defensible capstone |

**Total: 104 weeks ≈ 24 months.** No sub-roadmap exceeds 7 weeks (< 2 months).

### A note on one inconsistency in the source PDF

The "Roadmap at a Glance" table places PostgreSQL in Phase 1 and Concurrency in
Phase 2, but the detailed sections put Concurrency inside §3.2 (JVM) and PostgreSQL
inside §4 (Data & Messaging). **This roadmap follows the detailed sections**, because
concurrency is a JVM topic and only makes sense after the memory model. Phase 1 =
Java + JVM + concurrency; Phase 2 = PostgreSQL + Redis + Kafka.

---

## 3. Dependency graph — what actually must come first

```
1A Java ──► 1B JVM/Concurrency ──┬──► 2A PostgreSQL ──► 2B Redis ──► 2C Kafka
                                 │                                      │
                                 └──────────────────────────────────────┤
                                                                        ▼
                          3A Reliability & Consistency ──► 3B Sagas & Observability
                                                                        │
                                                                        ▼
                                   4A DDD ──► 4B System Design
                                                        │
                                        ┌───────────────┴───────────────┐
                                        ▼                               ▼
                          5A Ledger ──► 5B Payments          6A LLM/RAG ──► 6B AI Backend
                                        │                               │
                                        └───────────────┬───────────────┘
                                                        ▼
                                     7A Agents/MCP ──► 7B AI-Native SDLC
                                                        │
                                                        ▼
                                     8A Architecture at Scale ──► 8B Capstone & ADRs
```

**Hard prerequisites** (do not reorder): 1A → 1B → 2A → 2C → 3A → 3B → 4A → 4B.
**Soft ordering:** Phase 5 (domain) and Phase 6 (AI) can swap if a work project
pushes you toward one. Phase 8 must be last.

**Always-on threads** (the PDF's note that phases are not strictly sequential):
AI-assisted development, writing, testing, and system-design practice run through
*every* sub-roadmap — they are baked into the weekly Reason and AI-drill blocks.

---

## 4. The flagship project — Mini Core Banking / Payment Platform

One serious repository, evolved across 24 months. Not 30 tutorial repos.

**Modules:** Customer · Account · Ledger · Payment · Transfer · Balance ·
Transaction · Notification · Reconciliation · Audit

**Stack:** Java 21+ · Spring Boot · PostgreSQL · Redis · Kafka · Avro + Schema
Registry · Docker · Testcontainers · OpenTelemetry stack · Kubernetes concepts ·
AI assistant/agent layer

### Version timeline — which sub-roadmap ships which version

| Version | Ships in | What lands |
|---|---|---|
| **v0** — skeleton | 1A | Spring Boot service, Customer + Account CRUD, layered structure, full test pyramid |
| **v0.5** — measured | 1B | JMH benchmarks, GC/profiling report, virtual-thread experiment |
| **v1** — Monolith | 2A | Real PostgreSQL schema, transactions, indexes, load test, EXPLAIN-driven tuning |
| **v1.5** — cached | 2B | Redis cache-aside, distributed lock, rate limiter, idempotency keys |
| **v2** — Event-driven | 2C | Kafka, Outbox pattern, Avro schemas, idempotent consumers, DLT |
| **v2.5** — Resilient | 3A | Timeouts, retries with backoff, circuit breakers, bulkheads, load shedding |
| **v3** — Observable & transactional | 3B | Saga orchestration, OpenTelemetry traces, RED/USE dashboards, SLOs |
| **v4** — Modular Monolith | 4A | Bounded contexts, aggregates, domain events, hexagonal ports & adapters |
| **v4.5** — Service boundaries | 4B | Two contexts extracted as services, documented with ADRs |
| **v5** — Real ledger | 5A | Double-entry immutable ledger, balances, reversals, adjustments |
| **v5.5** — Payments | 5B | Payment states, FX, fees, limits, reconciliation, audit trail |
| **v6** — AI-assisted | 6A | RAG over transactions/docs with a measured evaluation set |
| **v6.5** — AI platform | 6B | AI gateway, prompt management, guardrails, cost & latency controls |
| **v7** — Agentic | 7A | MCP tool server + human-in-the-loop agent for ops/support workflows |
| **v7.5** — AI-native repo | 7B | AI-in-CI: review, test generation, doc generation, with human gates |
| **v8** — At scale | 8A | Load + failure + chaos testing, multi-region design doc |
| **v8.final** — Defended | 8B | Full ADR set, architecture review recording, capstone presentation |

> **Rule:** every sub-roadmap must leave a *visible diff* in this repo. If a
> sub-roadmap ends with no commit, it did not happen.

---

## 5. Review cadence

### Daily (built into every session — see DAILY-EXPANSION-KIT.md)
One learning objective · one engineering exercise · one system-design question ·
one AI experiment. Log it.

### Weekly (Sunday, ~60 min — replaces one Build hour)
Ask AI to review, then grade its review:
- Knowledge: what did I learn? what is still shallow?
- Engineering: what did I build? is it production-quality?
- Architecture: can I explain the trade-offs out loud?
- AI: am I using it well, or over-relying on it?
- Career: did this week move me toward Senior/Architect?

### Monthly (last Sunday, ~2h)
Score 1–5 and track the trend:

| Area | M1 | M2 | M3 | ... |
|---|---|---|---|---|
| Java / JVM | | | | |
| Database | | | | |
| Distributed Systems | | | | |
| Messaging | | | | |
| Architecture | | | | |
| Domain Knowledge | | | | |
| AI Engineering | | | | |
| System Design | | | | |
| Communication | | | | |

Then: **Keep** (what works) · **Stop** (what wastes time) · **Start** (what's missing)
· **Deepen** (which topic deserves an extra month).

### Sub-roadmap gate (end of each of the 17 files)
Do not advance until every exit-checklist item passes. If two or more fail, add a
**1-week repair sprint** and push the schedule. The schedule is a servant, not a boss.

---

## 6. Learning rules (non-negotiable)

1. **Depth over technology count.** Before learning anything, answer: *what engineering problem does this solve?*
2. **Problem first, technology second.** e.g. *need reliable async communication* → messaging → delivery semantics → ordering → retry → idempotency → *then* Kafka.
3. **Build before collecting certificates.** Design → Build → Test → **Break** → Measure → Explain.
4. **AI must challenge you, not agree with you.** Weekly: *"What is wrong with my design? What assumptions am I making? Give me the strongest argument against this architecture."*
5. **Production thinking.** For every feature: what happens under load / when dependencies fail / when messages duplicate / when requests retry / after deployment / how do we observe it / how do we recover?

### What NOT to optimize for
Technologies learned · GitHub repos · tutorials completed · prompts written ·
memorized framework APIs · blindly following AI code · chasing every new AI framework.

**Optimize for: engineering judgment.**

### Human responsibilities — never blindly delegate to AI
Architecture decisions · security decisions · data model decisions · business-critical
logic · production risk decisions · compliance decisions · final code review.

---

## 7. Files in this roadmap

```
00-OVERVIEW.md              ← you are here
DAILY-EXPANSION-KIT.md      ← turn any week into 6 daily sessions (use at week start)
PHASE-1/  1A-modern-java.md            1B-jvm-concurrency-performance.md
PHASE-2/  2A-postgresql.md   2B-redis-caching.md   2C-kafka-event-driven.md
PHASE-3/  3A-reliability-consistency.md          3B-transactions-observability.md
PHASE-4/  4A-domain-driven-design.md             4B-system-design.md
PHASE-5/  5A-ledger-engineering.md               5B-payments-compliance.md
PHASE-6/  6A-llm-fundamentals-rag.md             6B-ai-backend-architecture.md
PHASE-7/  7A-agents-mcp.md                       7B-ai-native-sdlc.md
PHASE-8/  8A-architecture-at-scale.md            8B-decisions-capstone.md
```

---

## 8. Progress tracker

| Sub-roadmap | Planned start | Actual start | Planned end | Actual end | Gate passed |
|---|---|---|---|---|---|
| 1A Modern Java | | | | | ☐ |
| 1B JVM & Concurrency | | | | | ☐ |
| 2A PostgreSQL | | | | | ☐ |
| 2B Redis | | | | | ☐ |
| 2C Kafka | | | | | ☐ |
| 3A Reliability | | | | | ☐ |
| 3B Sagas & Observability | | | | | ☐ |
| 4A DDD | | | | | ☐ |
| 4B System Design | | | | | ☐ |
| 5A Ledger | | | | | ☐ |
| 5B Payments | | | | | ☐ |
| 6A LLM & RAG | | | | | ☐ |
| 6B AI Backend | | | | | ☐ |
| 7A Agents & MCP | | | | | ☐ |
| 7B AI-Native SDLC | | | | | ☐ |
| 8A Architecture at Scale | | | | | ☐ |
| 8B Capstone | | | | | ☐ |
