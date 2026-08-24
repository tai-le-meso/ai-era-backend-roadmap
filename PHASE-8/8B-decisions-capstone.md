# 8B — Architecture Decision-Making, Communication & Capstone

**Duration:** 6 weeks (108h) · **Months 22.6 – 24.0** · **Prereq:** 8A
**Flagship increment:** v8.final — Defended

> **Outcome:** The North Star, demonstrated rather than claimed: *I can take an
> ambiguous business problem, model the domain, design a reliable system, implement it,
> validate it under realistic conditions, explain the trade-offs, and use AI to multiply
> my engineering productivity.*

**Character of this sub-roadmap:** it is less about learning new technology and almost
entirely about *demonstration and communication* — the two things that actually convert
capability into a Solution Architect / Staff Engineer role.

---

## Week 1 — Architecture Decision Records as a habit

- **Learn (5h):** ADR structure from the source document: **Context** (what problem are we
  solving) · **Constraints** (what limitations exist) · **Options** (what alternatives were
  considered) · **Decision** (what was selected) · **Trade-offs** (what are we giving up) ·
  **Consequences** (what happens operationally). Writing options fairly — including the one
  you rejected. Recording assumptions and their expiry. Superseding vs amending an ADR.
  ADRs as onboarding material and as institutional memory. Lightweight ADR tooling.
- **Build (8h):** Audit every ADR you have written (0001–0010+). Rewrite the three weakest
  to the full standard. Then write ADRs retroactively for **five significant undocumented
  decisions** in the flagship. Build an ADR index.
- **Reason (3h):** Which of your past decisions would you now reverse? Write the superseding
  ADR honestly, including what you got wrong and what you learned.
- **AI drill (2h):** Have AI critique your ADRs for missing options and unstated
  assumptions. Its strongest use here is finding options you never considered.
- **Deliverable:** complete, indexed ADR set with at least one honest superseding ADR.
- **Self-check:** ☐ Someone joining the project could understand its history from the ADRs.

## Week 2 — Technical communication across audiences

- **Learn (5h):** The same architecture explained to: an executive (cost, risk, timeline,
  business outcome), a peer architect (trade-offs and constraints), an engineer
  (mechanisms and interfaces), a junior (concepts and why). Written communication: design
  docs, RFCs, one-pagers, executive summaries — leading with the decision, not the
  journey. Diagram discipline (C4 levels, one message per diagram, consistent notation).
  Presenting under challenge: acknowledging uncertainty, saying "I don't know, I'll find
  out", separating fact from judgment.
- **Build (8h):** Produce **four versions** of the flagship architecture explanation: a
  1-page executive brief, a peer-level design doc, an engineer-level component guide, and
  a 15-minute talk with slides. Record yourself delivering the talk and watch it back.
- **Reason (3h):** What did you cut for the executive version, and what did that cut
  reveal about what actually matters? Where did you hide uncertainty behind jargon?
- **AI drill (2h):** Have AI role-play each audience and ask questions in character. The
  executive one is hardest — practise answering "why does this cost a quarter of a year?"
- **Deliverable:** four audience-specific artefacts + a self-reviewed recorded talk.
- **Self-check:** ☐ I can explain my architecture to a CFO in three minutes.

## Week 3 — Running an architecture review

- **Learn (5h):** Architecture review as a process: preparation, the pre-read, structured
  agenda, review checklists (scalability, reliability, security, cost, operability,
  evolvability, compliance). Giving review feedback that improves designs rather than
  demonstrating cleverness. Receiving challenge without defensiveness. ATAM-style
  scenario-based evaluation. Handling disagreement and unresolvable trade-offs.
  Documenting outcomes and follow-up actions. Architecture governance without becoming a
  bottleneck.
- **Build (8h):** Run a **real architecture review** of the flagship with at least one
  other human (a colleague, a mentor, a community peer). Prepare the pre-read, run the
  session, capture findings, and produce an action list. Then act on the top three findings.
- **Reason (3h):** What did a human reviewer see that 22 months of AI review never
  surfaced? That gap is worth understanding precisely.
- **AI drill (2h):** Run a parallel AI review panel (three separate sessions with
  different lenses: security, cost, operability). Compare its findings to the human
  review's. Score both.
- **Deliverable:** review pre-read, findings log, action list, and three findings resolved.
- **Self-check:** ☐ I have received hard architectural criticism and used it well.

## Week 4 — The capstone: design from a blank page

- **Learn (5h):** Nothing new. This week is application.
- **Build (8h):** Take a genuinely **ambiguous business problem** you have not designed
  before — ideally a real one from your company, otherwise something like "design the
  payment infrastructure for a marketplace expanding into three new countries". Run the
  complete pipeline solo and timeboxed: requirements elicitation → domain model →
  architecture options → decision with ADR → capacity and cost model → failure analysis →
  implementation plan with estimates → risk register.
- **Reason (3h):** Where did you get stuck? Which phase took longest? Compare to how you
  would have handled this at month 0 — write that comparison down; it is your growth evidence.
- **AI drill (2h):** Use AI throughout, but log every point where you accepted, rejected
  or modified its input, and why. This log is the artefact that proves you use AI with judgment.
- **Deliverable:** a complete capstone design package + an AI-decision log.
- **Self-check:** ☐ I designed a non-trivial system end-to-end without a template.

## Week 5 — Capstone implementation & validation

- **Learn (5h):** Nothing new. Application week.
- **Build (8h):** Implement the **riskiest part** of the capstone design as a working
  vertical slice — the part where you are least confident the design holds. Validate it:
  load test it, fail it, measure it. Then update the design document with what the
  implementation taught you.
- **Reason (3h):** What did building it change about the design? (There is always
  something — this is the single most important lesson about architecture: designs are
  hypotheses.) Which of your estimates were wrong and by how much?
- **AI drill (2h):** Have AI predict, before you build, what will go wrong. Compare with
  what actually did. Score its prediction and your own.
- **Deliverable:** working vertical slice + validation results + a revised design document.
- **Self-check:** ☐ I let evidence change my design, and documented why.

## Week 6 — Defence, portfolio & what comes next

- **Learn (5h):** Positioning the work: portfolio structure, the architecture-focused CV,
  interview narratives (STAR for architecture decisions), the staff/architect competency
  ladders and how to evidence each level. Career direction from the source document —
  short term (stronger backend engineer), medium term (senior engineer / system designer),
  long term (solution architect / staff engineer). Continuing practice after month 24:
  what the next 12 months should look like.
- **Build (8h):** Ship **v8.final**: complete the flagship's documentation set, publish
  the ADR index, record a 30-minute architecture walkthrough of the whole platform, and
  build a portfolio page linking the artefacts (designs, ADRs, benchmarks, chaos reports,
  eval results, postmortems). Then **defend it**: present to at least two people who will
  challenge you.
- **Reason (3h):** Score yourself honestly on all nine areas against month 0. Where are you
  strongest? Which area is *still* a 3, and what is the 12-month plan for it? Write the
  next roadmap — this one is finished.
- **AI drill (2h):** Final calibration exercise: over 24 months, where did AI help most,
  where did it mislead you, and what is your personal, evidence-based policy for using it?
  Write it as one page. This is the most valuable single document you will produce.
- **Deliverable:** **v8.final tagged** + portfolio + recorded walkthrough + next-24-month plan.
- **Self-check:** ☐ I can defend every significant decision in this platform under challenge.

---

## Final exit checklist — the roadmap's completion criteria

Against the source document's 24-month outcomes:

- ☐ I can design production-grade backend systems (evidence: 10 design write-ups + capstone)
- ☐ I can explain architectural trade-offs clearly (evidence: ADR set + recorded talk + review)
- ☐ I can build reliable distributed systems with Java/Spring Boot (evidence: v8.final)
- ☐ I reason deeply about PostgreSQL, Redis, Kafka, concurrency and JVM behaviour
      (evidence: benchmark reports, GC analysis, isolation tests, chaos results)
- ☐ I understand the fintech/core-banking domain (evidence: ledger, payments, compliance modules)
- ☐ I can build AI-powered backend applications and agentic workflows (evidence: v6.5, v7)
- ☐ I use AI across the SDLC without blindly trusting it (evidence: AI-decision log + policy)
- ☐ I can communicate architecture at Senior/Architect level (evidence: portfolio + defence)

**And the North Star, in one sentence you can now say honestly:**

> *"I can take an ambiguous business problem, model the domain, design a reliable system,
> implement it, validate it under realistic conditions, explain the trade-offs, and use AI
> to multiply my engineering productivity."*
