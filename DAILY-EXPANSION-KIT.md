# Daily Expansion Kit

**Use this at the start of each week — not in advance.**

Each sub-roadmap gives you a week-level plan: Learn / Build / Reason / AI-drill /
Deliverable, with an hour budget. This kit turns one of those weeks into **six 3-hour
sessions**. Do the expansion on the Sunday before the week starts (20 minutes), and a
lighter version at the start of each sub-roadmap for the whole 6–7 weeks.

> **Why not pre-write all 104 weeks day-by-day?** Because by week 3 you will be ahead on
> one topic and behind on another, a work project will eat two evenings, and a rabbit
> hole will turn out to be worth three days. A plan written 18 months out is fiction. A
> plan written 3 days out is a schedule you will actually keep.

---

## 1. The standard week → 6 days

18h per week, 3h per day, 6 days. The default distribution:

| Day | Focus | Hours | Shape |
|---|---|---|---|
| **Day 1 (Mon)** | Learn — first half | 3h | 2h reading/source-diving · 1h notes in your own words |
| **Day 2 (Tue)** | Learn — second half + design | 3h | 1.5h finish learning · 1.5h design what you'll build |
| **Day 3 (Wed)** | Build — core | 3h | 3h implementation (hardest part first) |
| **Day 4 (Thu)** | Build — tests & edge cases | 3h | 2h tests · 1h break it deliberately |
| **Day 5 (Fri)** | Build — finish + measure | 3h | 1.5h finish deliverable · 1.5h measure/benchmark |
| **Day 6 (Sat)** | Reason + AI drill + review | 3h | 1h written reasoning · 1h AI drill · 1h weekly review |

**Variants:**

- **Concept-heavy week** (e.g. 3A Week 4 consistency models, 4A Week 1 subdomains):
  shift to 4 learn-days / 2 build-days.
- **Build-heavy week** (e.g. 2C Week 4 outbox, 8A Week 6 chaos): 1 learn-day / 4 build-days
  / 1 reason-day.
- **Drill week** (all of 4B): each day = one design exercise (45 min timed) + write-up.
- **A day is lost:** do not compress — push the week. Deliverables matter, dates don't.

---

## 2. Daily session structure (3 hours)

```
0:00–0:10   Orientation
            - Re-read yesterday's "what remains unclear"
            - State today's ONE objective in one sentence
0:10–2:30   Deep work (the day's focus block above)
            - Phone in another room. No Slack.
            - 50/10 or 90/15 split, your choice
2:30–2:45   Evidence
            - Commit code / write the note / save the benchmark
            - If there is no artifact, the session did not happen
2:45–3:00   Log (the template below)
```

---

## 3. Daily log template

Copy this into `logs/YYYY-MM-DD.md` every day. Keep it short — 5 minutes, not 20.

```markdown
# YYYY-MM-DD · Sub-roadmap __ · Week __ · Day __

## Daily Goal
- Topic:
- Why it matters:
- Expected outcome:

## Learn
- Concepts:
- Documentation / source read:
- Examples:

## Build
- Coding task:
- Test:
- Benchmark:

## Reason
- What could fail?
- What are the trade-offs?
- What would change at 10x scale?

## AI
- Prompt used:
- AI suggestion:
- What I accepted:
- What I rejected:
- Why:

## Review
- What did I learn?
- What remains unclear?      <-- read this first tomorrow
- What should I revisit?

## Evidence produced
- [ ] code   [ ] test   [ ] benchmark   [ ] diagram
- [ ] ADR    [ ] design doc   [ ] architecture review   [ ] performance report
- Link/commit:
```

---

## 4. The morning prompt

Ask at the start of each session (adapted from the source document):

```
Based on my roadmap sub-file <e.g. 2A-postgresql.md>, Week <N>, and my log from
yesterday (<paste "what remains unclear">), what should I learn and practise today?

Give me exactly:
1. One primary learning objective (one sentence)
2. One engineering exercise I can complete in ~2 hours
3. One system-design question to answer in writing
4. One AI experiment where I test your reliability on this topic

Do not give me a curriculum. Do not be encouraging. Be specific to where I am stuck.
```

---

## 5. The week-expansion prompt (use on Sunday, 20 min)

```
Here is one week from my learning roadmap:

<paste the full week block: Learn / Build / Reason / AI drill / Deliverable / Self-check>

Context:
- I am a mid-level backend engineer (Java/Spring Boot/PostgreSQL, 3-5 years).
- I have 3 hours per day, 6 days.
- My flagship project is a mini core-banking platform (current version: <vX>).
- Last week I struggled with: <topic>.
- Already comfortable with: <topics>.

Produce a 6-day plan. For EACH day give me:
- one objective (a single sentence, testable)
- a 3-hour breakdown in 30-minute blocks
- the exact resource to read (docs section, book chapter, paper, source file) —
  no generic "read about X"
- the concrete artifact I must produce that day
- one question I must be able to answer by the end of the day

Rules:
- Hardest work goes on Day 3 and Day 4, when momentum is highest.
- Every build day ends with a commit.
- Do not pad. If the week only needs 14 hours, say so and tell me what to cut.
- Flag anything in this week that you think is over- or under-scoped for 18 hours.
```

Then **edit the result yourself.** The model does not know your actual gaps; you do.

---

## 6. The sub-roadmap kickoff prompt (use on day 1 of each of the 17 files)

```
I am starting sub-roadmap <ID: name> from my 24-month roadmap.

<paste the whole sub-roadmap file>

Before I begin:
1. Which weeks here are likely over-scoped for 18 hours, and which are under-scoped?
2. Given I already know <list>, what should I compress or skip?
3. What is the single highest-risk topic here — the one I am most likely to think I
   understand while actually not?
4. What prerequisite from an earlier sub-roadmap should I revisit first?
5. Propose a concrete flagship-repo branch plan for the increment this ships.

Be critical. Assume I will overestimate my existing knowledge.
```

---

## 7. Weekly review prompt (Saturday, 1 hour)

```
Review my week. Here are my 6 daily logs and my commits:

<paste logs + git log --stat for the week>

Assess honestly, and be willing to tell me the week was weak:
- Knowledge: what did I actually learn? which concepts are still shallow?
- Engineering: what did I build? is it production-quality? what would fail a review?
- Architecture: can I explain the trade-offs? where is my reasoning hand-wavy?
- AI: was I using AI as a tutor and adversary, or as an autocomplete crutch?
- Career: did this week move me measurably toward Senior/Architect?

End with: the ONE thing to do differently next week.
```

---

## 8. Monthly review (last Sunday, 2 hours)

1. **Score 1–5** on all nine areas and add a column to the table in `00-OVERVIEW.md`:
   Java/JVM · Database · Distributed Systems · Messaging · Architecture ·
   Domain Knowledge · AI Engineering · System Design · Communication
2. **Keep** — what is working?
3. **Stop** — what is wasting time?
4. **Start** — what should be added?
5. **Deepen** — which topic deserves another month? (If a topic keeps scoring 3, extend
   its sub-roadmap by a week rather than moving on and pretending.)
6. **Re-plan** — adjust the remaining schedule. Slipping is expected; hiding it is not.

---

## 9. Rules for expansion (so the plan stays honest)

1. **Never expand more than one week ahead in detail.** Two weeks max at a sub-roadmap boundary.
2. **Every day ends with an artifact.** Code, test, benchmark, diagram, ADR, design doc,
   architecture review, or performance report. No artifact = the session didn't count.
3. **The Reason block is not optional.** If you must cut something, cut a Learn hour, never
   the written reasoning. Reasoning is the thing that makes you senior.
4. **If a week's deliverable is not done, the week is not done.** Extend it. The 24-month
   number is an estimate, not a commitment.
5. **Every sub-roadmap must leave a visible diff in the flagship repo.**
6. **When AI and your measurements disagree, the measurements win.** Always.
7. **One week per quarter with no AI at all.** Deliberate practice against skill atrophy —
   this becomes formal policy in sub-roadmap 7B, but start it in Phase 3.

---

## 10. Quick reference — where to find things

| I need... | Look in |
|---|---|
| The big picture / dependency graph | `00-OVERVIEW.md` §2, §3 |
| Which flagship version ships when | `00-OVERVIEW.md` §4 |
| This week's plan | the relevant `PHASE-N/<ID>-*.md` |
| Whether I can move on | the Exit checklist at the bottom of each sub-roadmap |
| How to plan today | this file, §2–§4 |
| Monthly scoring table | `00-OVERVIEW.md` §5 |
