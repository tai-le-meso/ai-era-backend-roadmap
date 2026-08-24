# AI-Era Backend Engineer Roadmap

**A 416-lesson, 1,872-hour self-directed curriculum that takes a mid-level backend engineer to senior / solution-architect level — with backend depth and AI engineering running in parallel from week one.**

🌐 **[Open the live site →](https://tai-le-meso.github.io/ai-era-backend-roadmap/)** · 📊 **[Progress tracker](https://tai-le-meso.github.io/ai-era-backend-roadmap/roadmap-tracker.html)**

---

## What this is

A complete study plan covering Java and the JVM, PostgreSQL, Redis, Kafka, distributed systems, domain-driven design, the core-banking domain, LLM application engineering, AI agents, and solution architecture.

It is not a link list. Every one of the **104 study blocks** is broken down into **four day-by-day lessons**, each with specific technical content, concrete actions, the trade-offs to take away, flashcard facts, and cited official sources.

| | |
|---|---|
| **Daily lessons** | 416 |
| **Study blocks** (18h each) | 104 |
| **Total hours** | 1,872 |
| **Duration** | ~14.5 months at 30h/week (5h/day × 6 days) |
| **Phases** | 8 |
| **Cited sources** | 426 (269 URL-verified at build time) |
| **Study text** | ~525,000 words |

---

## The two-lane structure

The original plan ran all eight phases in sequence, which put every AI topic in months 15–21. That is too late — models, tooling and employer expectations move monthly, while MVCC and the Java Memory Model do not.

So the schedule splits every week into two lanes that **finish together**:

| Lane | Hours/week | Content | Sub-roadmaps |
|---|---|---|---|
| **Backend** | 22.5h | Phases 1–5 + 8, in sequence | 12 (1,404h) |
| **AI** | 7.5h | Phases 6–7, interleaved by dependency | 4 (468h) |

The content ratio is exactly 3:1, so at a 22.5/7.5 split both lanes complete at **week 62.4 ≈ month 14.4**. Nothing is cut.

Each AI block is scheduled **after its backend prerequisite**, not after everything:

- Vector search runs alongside the PostgreSQL deep dive (pgvector)
- The AI gateway lands as you learn timeouts, retries and circuit breakers
- Agents start once sagas are done — an agent run *is* a long-running process
- AI-assisted coding discipline lands in weeks 6–10, so the remaining year of backend work is done AI-assisted with rigour

**What you can demonstrate, and when:** a measured RAG feature with a CI eval gate by month 4.4 · an AI platform with red-teamed guardrails by month 8.9 · an agentic MCP ops layer by month 13.3.

---

## The eight phases

| Phase | Focus | Blocks | Lane |
|---|---|---|---|
| **1** Engineering Foundation | Modern Java, JVM internals, GC, concurrency, JMH | 13 | Backend |
| **2** Data & Messaging | PostgreSQL depth, Redis, Kafka, outbox | 13 | Backend |
| **3** Distributed Systems | Reliability, consistency, sagas, observability, SLOs | 13 | Backend |
| **4** DDD & Architecture | Bounded contexts, aggregates, hexagonal, system design | 13 | Backend |
| **5** Fintech / Core Banking | Double-entry ledger, money, payments, compliance | 13 | Backend |
| **6** AI Application Engineering | LLM fundamentals, RAG, evaluation, AI platform | 13 | AI |
| **7** Agents & AI-Native Dev | Agents vs workflows, MCP, tool security, AI-native SDLC | 13 | AI |
| **8** Solution Architecture | Scale, chaos, cost, ADRs, capstone defence | 13 | Backend |

---

## What a daily lesson looks like

Each of the 416 pages follows the same four-part structure:

**What I learn** — 3–6 specific technical points with real detail. Not "learn about HashMap" but *why a bin treeifies at 8 entries when the table is ≥64, and what that means when your `hashCode` is poor*.

**What I do** — 3–5 concrete actions for the session. Reproduce this anomaly with two psql sessions. Kill the broker mid-produce with `acks=1` and count lost records. Measure recall@10 against an exact-kNN baseline.

**What I conclude** — the trade-offs and judgment the day's work should leave behind.

**What I remember & apply** — flashcard facts, each ending in an `→ apply:` pointer to real work. *"Only Serializable prevents write skew → apply: use it for invariant-protecting transactions like shared-limit checks."*

**Sources** — 4–6 official references per week, badged **verified** where the URL was confirmed reachable at build time.

Sources are official-first throughout: Oracle JDK docs, PostgreSQL manual, Apache Kafka docs, Spring Boot reference, Redis docs, pgvector, Model Context Protocol, OWASP GenAI Top 10, OpenTelemetry, Google SRE Book, the Raft paper, Jepsen, and microservices.io.

---

## The flagship project

One serious repository evolves across the whole roadmap — a **mini core-banking / payment platform** — rather than thirty tutorial projects.

`v0` service skeleton → `v1` monolith on a tuned schema → `v2` event-driven with outbox → `v2.5` resilient → `v3` observable with SLOs → `v4` modular monolith → `v5` double-entry ledger → `v6` measured RAG → `v6.5` AI platform → `v7` agentic ops → `v8.final` chaos-tested and defended.

Every sub-roadmap must leave a visible diff in that repo. If a block ends with no commit, it did not happen.

---

## Repository layout

```
index.html                    Landing page
roadmap-tracker.html          Interactive progress tracker
00-PLAN-14MO-PARALLEL.md      The schedule — authoritative for order and pace
00-OVERVIEW.md                Original 24-month framing and dependency graph
DAILY-EXPANSION-KIT.md        Prompts to re-cut any week around what you're stuck on
PHASE-1..8/                   17 sub-roadmap files (week-by-week specs)
lessons/P1..P8/
  index.html                    Phase lesson index
  <ID>-W<n>.html                Week overview
  <ID>-W<n>/day1..day4.html     The four daily lessons
```

---

## Using it

1. Open the **[tracker](roadmap-tracker.html)**, set a start date, expand the sub-roadmap you're on.
2. Each week row links to its four daily lessons. Work through them.
3. Tick blocks and exit-checklist items as you go.
4. **Progress saves automatically** in your browser (`localStorage`) — start date, ticked blocks, checklist items and monthly scores are all restored when you reopen the page. Saved data is per-browser and per-origin, so use **Export** / **Import** to back it up or move it between machines. **Clear saved progress** wipes it. If a browser blocks storage (private windows, strict privacy settings) the tracker says so and falls back to Export/Import.
5. Every sub-roadmap ends with an exit checklist. Don't advance with more than two items failing — add a repair week instead.

The plan assumes 5h/day × 6 days. At 3h/day it runs about 24 months; the block content is unchanged, only the calendar stretches.

---

## Deploying your own copy

```bash
git clone https://github.com/tai-le-meso/ai-era-backend-roadmap.git
cd ai-era-backend-roadmap
python3 -m http.server 8000     # then open http://localhost:8000
```

To publish on GitHub Pages: **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**. The site is fully static — no build step, no dependencies, no tracking, no external requests except font-free system UI. A `.nojekyll` file is included so Jekyll doesn't reprocess the HTML.

---

## Honest caveats

**Lesson content is AI-assisted and human-reviewed.** It was authored with Claude against a manually verified source registry, then checked structurally (all 416 pages, all four sections populated). It has not been reviewed line-by-line by a domain expert.

**269 of 426 source URLs were fetch-verified** at build time and carry a `verified` badge. The remaining 157 are deep links following documented URL patterns on official sites (specific JEP numbers, PostgreSQL chapter pages, javadoc classes). If one 404s, navigate from the site root.

**Version-sensitive details drift.** Where the material names versions (Java 25, Spring Boot 4.x, Kafka 4.x, PostgreSQL current), check the current docs — that is what the source links are for.

**Treat it as a study plan, not a reference manual.** The value is the sequence, the exercises and the trade-off questions. Verify anything before relying on it professionally.

---

## Credits & licence

Built from a personal 24-month roadmap, restructured into a two-lane parallel schedule and expanded into daily lessons.

Content is licensed **[CC BY 4.0](LICENSE)** — use it, adapt it, share it, with attribution.

If you fork it for your own stack (Go, Python, .NET), the phase structure and the four-part lesson format transfer directly; only the source registry and the specific technical points need swapping.
