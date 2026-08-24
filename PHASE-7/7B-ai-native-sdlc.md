# 7B — AI-Native SDLC & Engineering Judgment

**Duration:** 6 weeks (108h) · **Months 19.6 – 21.0** · **Prereq:** 7A
**Flagship increment:** v7.5 — AI-native repository

> **Outcome:** I use AI across the entire software lifecycle in a way that measurably
> speeds me up, while retaining full ownership of every decision that matters — and I
> can show a team how to do the same without losing engineering rigour.

**The core tension this sub-roadmap resolves:** maximum leverage from AI, zero
abdication of judgment. The source document's *Human Responsibilities* list is the
constitution here.

---

## Week 1 — Requirements & architecture with AI

- **Learn (5h):** Using AI for requirement analysis: extracting implicit requirements,
  finding ambiguity, generating edge cases and negative scenarios, producing Gherkin
  acceptance criteria, stakeholder-question generation. Architecture brainstorming:
  generating option sets rather than answers, forcing explicit trade-off tables, using AI
  as a devil's advocate. Where AI systematically fails: it does not know your
  organisation, your constraints, your history, or your politics.
- **Build (8h):** Take a real, vague feature request (from work or invented) and run the
  full AI-assisted analysis: requirements → ambiguities → edge cases → acceptance criteria
  → three architecture options with trade-offs. Then implement the winning option's skeleton.
- **Reason (3h):** Which requirements did AI find that you missed? Which did *you* find
  that it missed, and why (context it cannot have)? That second list defines your
  irreplaceable value — write it down explicitly.
- **AI drill (2h):** Give the same vague request to three separate fresh sessions.
  Compare. The variance tells you how much to trust any single response.
- **Deliverable:** a full analysis document + a written note on human-vs-AI contribution.
- **Self-check:** ☐ I can name what AI structurally cannot know about my system.

## Week 2 — API design, coding & the review discipline

- **Learn (5h):** AI-assisted API design with OpenAPI-first (generate spec → review →
  generate code, never the reverse). Effective coding prompts: specify constraints,
  conventions, error handling and tests up front; provide the surrounding code as context.
  Context management for large codebases (repo maps, conventions files, skills/rules
  files). The **explain-the-design-first rule** from the source doc: you state the design,
  AI implements it — never the reverse for anything non-trivial.
- **Build (8h):** Write a `CONVENTIONS.md` / rules file for the flagship encoding your
  architecture rules, naming, testing and error-handling conventions. Then implement one
  full feature AI-assisted under that file and measure: time taken, review findings,
  defects escaping to tests.
- **Reason (3h):** How many review findings did you have per 100 lines of AI code versus
  your own? What category of defect dominates? (Usually: missing edge cases, wrong error
  handling, subtle concurrency issues.)
- **AI drill (2h):** Implement the same feature twice — once specifying the design first,
  once just describing the goal. Compare quality. This validates (or refutes) the rule.
- **Deliverable:** conventions file + one AI-assisted feature + a defect-category tally.
- **Self-check:** ☐ I state the design before AI writes non-trivial code.

## Week 3 — Testing, refactoring & debugging with AI

- **Learn (5h):** AI for tests: generating edge cases you didn't think of, property-based
  test generation, mutation-testing-driven gap finding, integration and contract tests.
  The trap: AI writing tests that assert current (buggy) behaviour. Test review discipline.
  AI for refactoring: large mechanical refactors, extracting abstractions, migration
  across API versions — with a test suite as the safety net (never refactor without one).
  AI for debugging: hypothesis generation from a stack trace, log analysis, and the
  discipline of verifying rather than accepting the first plausible cause.
- **Build (8h):** Raise flagship mutation-test coverage using AI-generated tests. Perform
  one large AI-assisted refactor (e.g. migrate a module to a new abstraction) behind the
  test suite. Debug one deliberately-injected subtle bug with AI assistance and log the process.
- **Reason (3h):** How many AI-generated tests asserted wrong behaviour? How did you catch
  them? What was AI's first hypothesis for the injected bug, and was it right?
- **AI drill (2h):** Inject three subtle bugs (an off-by-one, a race, a wrong isolation
  level) and see which AI finds. The one it misses is the class of bug you must always own.
- **Deliverable:** mutation coverage improvement + refactor + a debugging log.
- **Self-check:** ☐ I know which bug classes AI reliably misses in my codebase.

## Week 4 — Code review, documentation & migration planning

- **Learn (5h):** AI as a first-pass reviewer for: bugs, concurrency issues, security
  risks, performance problems, maintainability — the exact list from the source document.
  Building a review prompt from your conventions. AI review in CI (as advisory, never as
  a merge gate on its own). Its limitations: no architectural context, no knowledge of
  why the code is the way it is, high false-positive rate on style. Documentation: ADRs,
  runbooks, API docs, onboarding docs — AI drafts, you verify facts. Migration planning:
  dependency analysis, phased plans, rollback design.
- **Build (8h):** Add an AI review step to the flagship's CI that posts advisory comments
  using your conventions file. Measure its precision and recall against your own review of
  the same 20 PRs/commits. Then generate the flagship's documentation set and fact-check
  every claim.
- **Reason (3h):** What is your AI reviewer's false-positive rate? At what rate do
  engineers start ignoring it entirely? Tune it to below that threshold.
- **AI drill (2h):** Have AI review a PR where you deliberately introduced an
  architectural violation that is locally fine but globally wrong. Note whether it can see it.
- **Deliverable:** AI review in CI with measured precision/recall + fact-checked docs.
- **Self-check:** ☐ I know my AI reviewer's precision and have tuned it to be trusted.

## Week 5 — Human responsibilities: the non-delegation constitution

- **Learn (5h):** Work through each item from the source document and define, for each,
  *what AI may do* and *where the human line is*: architecture decisions · security
  decisions · data model decisions · business-critical logic · production risk decisions ·
  compliance decisions · final code review. Automation bias and how to counter it.
  Verification discipline: never accept a factual claim without a primary source; never
  accept a design without understanding it well enough to defend it. Skill atrophy — the
  real long-term risk — and deliberate practice to counter it.
- **Build (8h):** Write `docs/ai/human-responsibilities.md` as an actual team policy: for
  each of the seven areas, the allowed AI role, the required human action, and the
  evidence required. Then apply it retroactively to your last 3 months of work and find
  where you violated it.
- **Reason (3h):** Where have you already over-delegated? What skill has atrophied since
  month 15? Design a weekly practice to counter it (e.g. one feature per month written
  entirely without AI).
- **AI drill (2h):** Ask AI to make an architecture decision for you with confident
  framing. Notice the pull to accept it. That pull is the thing to train against.
- **Deliverable:** human-responsibilities policy + a self-audit of violations.
- **Self-check:** ☐ I can state exactly where my line is and why.

## Week 6 — AI-native workflow at team scale + consolidation

- **Learn (5h):** Rolling this out beyond yourself: shared conventions/rules files, prompt
  and skill libraries, MCP servers exposing internal systems to the whole team, evaluation
  of the team's AI usage, cost governance, IP and data-handling policy (what may be sent
  to a provider), onboarding new engineers into an AI-native workflow. Measuring impact
  honestly — cycle time, defect rate, review load — and resisting vanity metrics.
- **Build (8h):** Ship **v7.5**: the flagship repo as a reference AI-native project —
  conventions file, prompt library, an internal MCP server for the team's tooling, AI
  review in CI, eval gates, and a written workflow guide someone else could follow.
- **Reason (3h):** Measure your own before/after: cycle time and defect rate on comparable
  features from month 6 versus now. Where did AI actually help, and where did it just feel fast?
- **AI drill (2h):** Present the whole AI-native workflow to AI as a skeptical engineering
  manager worried about quality and skill decay. Answer honestly; record what you cannot defend.
- **Deliverable:** **v7.5 tagged** + `docs/ai/ai-native-workflow.md` + an impact measurement.
- **Self-check:** ☐ I could onboard a colleague into this workflow in one afternoon.

---

## Exit checklist — all must pass before 8A

- ☐ Conventions/rules file drives AI code generation across the repo
- ☐ Defect-category tally exists for AI-generated code; I know its failure classes
- ☐ Mutation coverage improved; one large refactor completed behind a test suite
- ☐ AI review in CI with measured precision/recall, tuned below the ignore-threshold
- ☐ Human-responsibilities policy written and self-audited against real work
- ☐ Skill-atrophy counter-practice defined and started
- ☐ Before/after impact measured on comparable features
- ☐ v7.5 tagged

**Phase 7 complete → Month 21.** Monthly review. You should now score highest on AI
Engineering and be ready for the architecture-leadership phase.
