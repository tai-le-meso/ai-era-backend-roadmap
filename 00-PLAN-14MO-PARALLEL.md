# Parallel-Track Plan — Full Roadmap in ~14.5 Months

> **Owner:** Tai Le · **Start:** ___________
> **Cadence:** 5h/day × 6 days = **30h/week**, split **22.5h backend + 7.5h AI**
> **Duration:** 62.4 calendar weeks ≈ **14.4 months** (strict) · ~**14.9 months** with the two recommended deload weeks
> **Content: 100% of the 24-month roadmap — 1,872 hours, nothing cut.**

---

## 0. What changed and why

The original plan ran the 8 phases in sequence, which put all AI content in months
15–21. Your objection is correct: **AI moves too fast to be learned last.** Models,
tool ecosystems and employer expectations change monthly; an engineer who starts RAG
in month 15 shows up to every interview and review a year behind. Meanwhile backend
fundamentals (MVCC, the JMM, consistency models) barely move — they can be learned on
a steady track without decay.

So this plan splits every week into **two parallel lanes**:

| Lane | Hours/week | Content | Finishes |
|---|---|---|---|
| **Backend track** | 22.5h | Phases 1–5 + 8 (12 sub-roadmaps, 1,404h) | week 62.4 |
| **AI track** | 7.5h | Phases 6–7 (4 sub-roadmaps, 468h), re-sequenced | week 62.4 |

The content ratio happens to be exactly 3:1, so at a 22.5/7.5 split **both lanes
finish together at week 62.4 = month 14.4**. Nothing is cut, nothing is rushed —
the AI phases are simply *spread across* the calendar instead of stacked at the end,
with each AI block scheduled **after its backend prerequisite**, not after everything.

**Every sub-roadmap file stays exactly as written.** This document only changes the
order and pacing. Content weeks are 18-hour blocks: a backend block takes 0.8
calendar weeks (four weekdays at ~4.5h); an AI block takes 2.4 calendar weeks
(three Saturdays plus Friday halves).

---

## 1. The 30-hour week

| Day | Hours | Lane | What |
|---|---|---|---|
| Mon | 5h | Backend | Learn |
| Tue | 5h | Backend | Learn → design |
| Wed | 5h | Backend | Build (hardest part first) |
| Thu | 5h | Backend | Build + tests + break it |
| Fri | 2.5h + 2.5h | Backend / AI | Backend: measure + weekly Reason write-up · AI: build |
| Sat | 5h | AI | 1h **AI radar** · 3h build/learn · 1h weekly review |
| Sun | 0 | — | Off. Guard it. |

**Cut order on a bad week:** Saturday's 3h build → a Learn hour → never the Reason
work, never a deliverable. If you miss more than 20% of hours in any 3-week window,
pause the AI lane for one week (backend continues) rather than half-doing both lanes.

### The AI radar (1h, every Saturday — the answer to "AI changes every day")

A fixed ritual, not doom-scrolling:

1. **Scan (20 min):** release notes of your 2–3 providers (Anthropic / OpenAI /
   open-weights), one aggregator, your company's AI channel.
2. **Try (30 min):** ONE new thing hands-on in a scratch branch — a new API feature,
   a new model against your golden dataset, a new MCP capability.
3. **Log (10 min):** one paragraph in `logs/ai-radar.md`: what changed, does it alter
   any decision I've made, does anything in my plan need re-ordering?

Rule: the radar may *re-order* upcoming AI blocks (a major release can justify pulling
a topic forward). It may never *replace* a block — chasing every new framework is
exactly what the source doc's Rule 1 forbids. Twelve months of radar logs is itself a
review artifact: evidence you track the field with judgment rather than hype.

---

## 2. Backend lane — 22.5h/week, order unchanged

| ID | Sub-roadmap | 18h blocks | Cal weeks | Months |
|---|---|---|---|---|
| **1A** | Modern Java Mastery | 7 | 1–6 | 0.0–1.3 |
| **1B** | JVM Internals, Concurrency & Performance | 6 | 7–10 | 1.3–2.4 |
| **2A** | PostgreSQL Deep Dive | 6 | 11–15 | 2.4–3.5 |
| **2B** | Redis, Caching & Idempotency | 3 | 16–18 | 3.5–4.1 |
| **2C** | Kafka & Event-Driven Architecture | 4 | 19–21 | 4.1–4.8 |
| **3A** | Reliability, Consistency & Coordination | 7 | 22–26 | 4.8–6.1 |
| **3B** | Distributed Transactions & Observability | 6 | 27–31 | 6.1–7.2 |
| **4A** | Domain-Driven Design | 7 | 32–37 | 7.2–8.5 |
| **4B** | System Design & Architecture Practice | 6 | 38–42 | 8.5–9.6 |
| **5A** | Banking Domain & Ledger Engineering | 7 | 43–47 | 9.6–10.9 |
| **5B** | Payments, Compliance & Risk | 6 | 48–52 | 10.9–12.0 |
| **8A** | Architecture Design at Scale | 7 | 53–58 | 12.0–13.3 |
| **8B** | Architecture Decision-Making, Communication & Capstone | 6 | 59–62 | 13.3–14.4 |

Flagship versions v0 → v5.5 and v8 ship exactly as the files specify.

---

## 3. AI lane — 7.5h/week, re-sequenced

The four AI sub-roadmaps (6A, 6B, 7A, 7B) are interleaved by dependency. 7B
(AI-native SDLC) is deliberately split across the whole year — it is a *practice*,
not a chapter: its coding-discipline weeks come first so that every subsequent
backend week is done AI-assisted, with rigour.

| # | Block | Topic | Cal weeks | Ends mo | Sync / note |
|---|---|---|---|---|---|
| 1 | **6A**-W1 | LLM mechanics from a backend engineer's view | 1–2 | 0.6 | needs only Spring basics — start day 1 |
| 2 | **6A**-W2 | Prompting, structured output & tool calling | 3–5 | 1.1 |  |
| 3 | **7B**-W1 | Requirements & architecture with AI | 6–7 | 1.7 | AI-assisted analysis — practise on real work tickets |
| 4 | **7B**-W2 | API design, coding & the review discipline | 8–10 | 2.2 | write the flagship `CONVENTIONS.md` here |
| 5 | **6A**-W3 | Embeddings & vector search | 11–12 | 2.8 | **sync:** runs alongside backend 2A PostgreSQL (pgvector) |
| 6 | **6A**-W4 | Chunking, hybrid search & reranking | 13–14 | 3.3 |  |
| 7 | **6A**-W5 | Evaluation: making quality a number | 15–17 | 3.9 | golden dataset + eval harness — the interview artifact |
| 8 | **6A**-W6 | Grounding, citation & the full RAG architecture | 18–19 | 4.4 |  |
| 9 | **6A**-W7 | When NOT to use an LLM + consolidation | 20–22 | 5.0 | the judgment week — de-LLM one feature |
| 10 | **7B**-W3 | Testing, refactoring & debugging with AI | 23–24 | 5.5 |  |
| 11 | **6B**-W1 | The AI Gateway | 25–26 | 6.1 | **sync:** backend 3A gives the timeout/retry/CB vocabulary |
| 12 | **6B**-W2 | Model routing, cost control & caching | 27–29 | 6.6 |  |
| 13 | **6B**-W3 | Prompt management & context management | 30–31 | 7.2 |  |
| 14 | **6B**-W4 | Guardrails & safety | 32–34 | 7.8 |  |
| 15 | **6B**-W5 | Observability & evaluation in production | 35–36 | 8.3 | **sync:** backend 3B observability just finished |
| 16 | **6B**-W6 | AI architecture patterns & consolidation | 37–38 | 8.9 | **sync:** AI layer as hexagonal adapter — 4A running now |
| 17 | **7B**-W4 | Code review, documentation & migration planning | 39–41 | 9.4 |  |
| 18 | **7A**-W1 | Agents vs workflows: the decision | 42–43 | 10.0 | **sync:** sagas (3B) done — agent-run-as-long-process makes sense |
| 19 | **7A**-W2 | Agent anatomy: planner, executor, memory, state | 44–46 | 10.5 |  |
| 20 | **7A**-W3 | Model Context Protocol: concepts and a server | 47–48 | 11.1 |  |
| 21 | **7A**-W4 | Tool design, permissions & tool security | 49–50 | 11.6 | tool security — pairs with 4B STRIDE work |
| 22 | **7A**-W5 | Human-in-the-loop & multi-agent systems | 51–53 | 12.2 |  |
| 23 | **7A**-W6 | Agent evaluation & reliability | 54–55 | 12.7 |  |
| 24 | **7A**-W7 | MCP ecosystem, external integration & consolidation | 56–58 | 13.3 |  |
| 25 | **7B**-W5 | Human responsibilities: the non-delegation constitution | 59–60 | 13.8 | the non-delegation constitution |
| 26 | **7B**-W6 | AI-native workflow at team scale + consolidation | 61–62 | 14.4 | lands with 8B — feeds the capstone defence |

**Prerequisite guards** (if the backend lane slips, these hold): 6A-W3 (vector
search) wants 2A started · 6B-W1 (gateway) wants 3A started · 6B-W5 (AI
observability) wants 3B done · 7A-W1 (agents) wants 3B done (sagas) · 6B-W6 (AI as
adapter) wants 4A started. If a guard fails, swap the AI block with the next one that
has no unmet guard — never skip it.

---

## 4. What you can show, and when

Why this ordering wins interviews and reviews — dated, demonstrable artifacts:

| By month | You can demonstrate |
|---|---|
| **1.1** | Production-grade LLM client (timeouts, retries, cost accounting) + structured output in a real Spring service |
| **2.2** | AI-assisted development discipline: `CONVENTIONS.md`, design-first prompting, a defect tally of AI-generated code |
| **4.4** | **A measured RAG feature**: pgvector + hybrid search + golden dataset + CI eval gate — quality as a number, not a demo |
| **5.0** | The judgment story: a feature you *removed* an LLM from, with data |
| **6.6** | Kafka event-driven flagship (v2) + retry storms reproduced and tamed |
| **8.9** | **An AI platform**: gateway, routing, cost control, red-teamed guardrails, canary evals (v6.5) |
| **9.6** | Modular monolith with enforced boundaries + 10 system-design write-ups (v4/v4.5) |
| **12.0** | Double-entry ledger + payments with UNKNOWN-state handling (v5/v5.5) |
| **13.3** | **Agentic ops layer**: MCP server, authorised tools, HITL, agent-vs-workflow decision backed by data (v7) |
| **14.4** | Chaos-tested, load-tested platform + full ADR set + defended capstone (v8.final) |

At every point after month 4 you hold a current, non-trivial AI artifact — you are
never the candidate whose AI knowledge is "planned for next year".

Note on tags: flagship versions now land interleaved, so keep `vN` for the backend
spine and tag AI increments as `ai/<topic>-<n>` — the git history stays readable.

---

## 5. Sustainability rules (30h/week for 14 months is serious)

1. **Two deload weeks** — after week 21 (RAG + reliability done) and after week 42
   (system design done). 15h, consolidation only. Take them; the total becomes ~14.9 months.
2. **Friday's backend half includes the weekly Reason write-up** — the promotion is
   in that writing; it is the one thing with no substitute.
3. **Sunday off is part of the plan, not slack in it.**
4. **Monthly review** (unchanged): score the nine areas 1–5, keep/stop/start/deepen,
   and re-order the AI lane against the radar log if needed.
5. **Fallback:** if 30h/week breaks, drop to 25h by halving the AI lane (radar + one
   block per month); backend pace holds and the finish moves to ~17 months. A planned
   degradation, not a failure.

---

## 6. Files

Everything else is unchanged and stays authoritative for *content*: `00-OVERVIEW.md`
(the 24-month framing), the 17 sub-roadmap files, and `DAILY-EXPANSION-KIT.md` (the
weekly expansion prompts still work — expand backend blocks into Mon–Fri sessions and
AI blocks into Fri/Sat sessions). **This file is authoritative for order and pace.**
The tracker has been rebuilt with both lanes.
