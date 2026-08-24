# 7A — AI Agents & the MCP / Tool Ecosystem

**Duration:** 7 weeks (126h) · **Months 18.0 – 19.6** · **Prereq:** 6B
**Flagship increment:** v7 — Agentic operations layer

> **Outcome:** I can answer the source document's important question with evidence:
> **when is an agent actually necessary, and when is a deterministic workflow better?**
> And I can build the one that is right, safely.

**Bias to correct up front:** most "agent" projects should have been a state machine
with three LLM calls. You will build both and let the measurements decide.

---

## Week 1 — Agents vs workflows: the decision

- **Learn (5h):** Definitions that matter: a **workflow** has predetermined control flow
  with LLM calls at fixed points; an **agent** decides its own control flow. The spectrum
  between them: chaining, routing, parallelisation, orchestrator-worker, evaluator-optimiser,
  autonomous agent. Cost of autonomy: unpredictable token spend, unpredictable latency,
  unbounded failure modes, hard-to-test behaviour. When autonomy earns its cost (open-ended
  task space, unknown step count, environment feedback loop).
- **Build (8h):** Pick one flagship operations task (e.g. "investigate this reconciliation
  break"). Build it **both ways**: a deterministic workflow with fixed LLM steps, and an
  autonomous agent with tools. Run both over 30 real cases.
- **Reason (3h):** Compare on: success rate, cost per case, p99 latency, variance,
  debuggability, testability. **Write the decision rule** you would give your team.
- **AI drill (2h):** Ask AI when to use an agent. Notice the systematic bias toward
  agents. Write down why that bias exists and why it should not drive your architecture.
- **Deliverable:** two implementations + a comparison table + `docs/ai/agent-vs-workflow.md`.
- **Self-check:** ☐ I have a data-backed answer to "should this be an agent?"

## Week 2 — Agent anatomy: planner, executor, memory, state

- **Learn (5h):** The loop: perceive → plan → act → observe → repeat. **Planner**
  strategies (ReAct, plan-and-execute, reflection) and their failure modes. **Executor**
  and tool invocation. **Memory**: working memory (context), episodic (what happened),
  semantic (learned facts), and the retrieval problem across all three. **State**:
  persisting agent state so a run survives a restart (you already know this from sagas —
  an agent run *is* a long-running process). Termination conditions, step limits, budget
  limits, loop detection. Error recovery within a run.
- **Build (8h):** Rebuild your agent with persistent state in PostgreSQL: every step
  recorded, resumable after a crash, with hard step/token/cost ceilings and loop detection.
  Kill it mid-run and resume it.
- **Reason (3h):** How is an agent run different from a saga? (Answer: mainly that the
  step list is not known in advance.) What does that mean for compensation and for
  idempotency of tool calls?
- **AI drill (2h):** Make your agent loop by giving it a task it cannot complete. Verify
  your ceilings hold. Measure what an unbounded run would have cost.
- **Deliverable:** resumable agent with enforced ceilings + a crash-resume test.
- **Self-check:** ☐ My agent cannot run away with cost or time, and I have tested it.

## Week 3 — Model Context Protocol: concepts and a server

- **Learn (5h):** MCP as a standard: why a protocol beats bespoke integrations
  (N×M problem). The primitives — **Tools** (model-invoked actions), **Resources**
  (application-controlled context), **Prompts** (user-invoked templates). Transports
  (stdio, HTTP/SSE). Capability negotiation and discovery. Server vs client
  responsibilities. Where MCP sits relative to your existing API layer — and why an MCP
  server is not a substitute for a well-designed API, it is a different consumer of one.
- **Build (8h):** Build an MCP server exposing safe flagship capabilities:
  `get_account_summary`, `search_transactions`, `get_reconciliation_break`,
  `list_audit_events`. All read-only this week. Connect it to a real MCP client and use it.
- **Reason (3h):** Which flagship capabilities should be MCP tools and which absolutely
  should not? What makes a good tool description (it is a prompt, not documentation)?
- **AI drill (2h):** Give the model your tool schemas and a task, and watch which tools it
  picks. Where it picks wrong, the fix is almost always the *description*, not the model.
- **Deliverable:** read-only MCP server + a transcript of a real client session.
- **Self-check:** ☐ I can explain Tools vs Resources vs Prompts and when to use each.

## Week 4 — Tool design, permissions & tool security

- **Learn (5h):** Tool design as API design for a non-deterministic caller: naming,
  granularity (few powerful vs many narrow), parameter schemas, defaults, error messages
  the model can act on, idempotency, pagination for large results, token cost of results.
  **Tool security**: the confused-deputy problem, authorisation on every call (never trust
  the model's claim of identity), scoping tools to the *user's* permissions not the
  service account's, injection via tool *results*, destructive-action confirmation,
  audit logging of every invocation. Sandboxing. Rate limiting per agent run.
- **Build (8h):** Add write-capable tools (`create_case`, `flag_transaction`,
  `request_reversal`) with: per-user authorisation on every call, an approval gate for
  destructive actions, full audit logging, and rate limits. Then attack it — try to get
  the agent to act outside the user's permissions via injected content in a transaction
  description.
- **Reason (3h):** Where is the confused deputy in your design? What happens if a malicious
  transaction memo contains instructions? Which of your tools would you never expose, at
  any level of guardrail?
- **AI drill (2h):** Red-team your own tool layer via injected data. Every successful
  privilege escalation is a critical finding — document and fix each.
- **Deliverable:** authorised write tools + injection red-team report + audit log.
- **Self-check:** ☐ Every tool authorises against the end user, and I have proven it.

## Week 5 — Human-in-the-loop & multi-agent systems

- **Learn (5h):** HITL patterns: approve-before-act, review-after-act, confidence-based
  escalation, and designing the review UX so humans do not rubber-stamp. Where HITL is
  mandatory in finance. **Multi-agent**: supervisor/worker, specialist agents, debate,
  handoffs — and an honest account of when multi-agent is genuinely better versus when it
  is one agent with extra latency and cost. Shared state and communication between agents.
  Failure containment across agents.
- **Build (8h):** Add HITL to the flagship agent: any action above a value threshold or
  below a confidence threshold enters an approval queue with full reasoning context for
  the reviewer. Then build a two-agent version (investigator + verifier) and measure
  whether the verifier actually catches errors the single agent made.
- **Reason (3h):** Did multi-agent improve accuracy enough to justify its cost and
  latency? Answer with your numbers. What makes a human reviewer effective rather than a
  rubber stamp?
- **AI drill (2h):** Have the verifier agent review 30 investigator outputs where you
  injected 10 known errors. Measure catch rate. This is the honest test of multi-agent value.
- **Deliverable:** HITL approval queue + multi-agent comparison with measured catch rate.
- **Self-check:** ☐ I know whether multi-agent helps *my* system, from data.

## Week 6 — Agent evaluation & reliability

- **Learn (5h):** Evaluating agents is harder than evaluating single calls: trajectory
  evaluation vs outcome evaluation, partial credit, deterministic replay of tool
  responses for regression testing, simulation environments, cost-per-successful-task as
  the true metric. Reliability engineering for agents: idempotent tools, checkpointing,
  compensation for completed steps, timeouts per step and per run, degradation to a
  deterministic path when the agent fails.
- **Build (8h):** Build an agent eval harness with recorded tool responses for
  deterministic replay, a suite of 30 scenarios, and metrics for success rate,
  cost-per-success and step count. Add it to CI. Add a fallback: when the agent fails or
  exceeds budget, fall back to the deterministic workflow from Week 1.
- **Reason (3h):** What is your cost per *successful* task (not per run)? At what success
  rate does the agent stop being worth it versus the deterministic workflow?
- **AI drill (2h):** Ask AI to design your agent evaluation. Check whether it distinguishes
  trajectory from outcome evaluation — most treat agents like single-turn calls.
- **Deliverable:** agent eval harness in CI + deterministic fallback path.
- **Self-check:** ☐ I can state cost-per-successful-task for my agent.

## Week 7 — MCP ecosystem, external integration & consolidation

- **Learn (5h):** Consuming third-party MCP servers and the supply-chain risk that creates
  (tool descriptions are untrusted input; a malicious server can inject instructions).
  Server allowlisting and review. Composing internal and external tools. External system
  integration through agents versus through traditional integration — when each is correct.
  Operating agents in production: monitoring, cost alerting, kill switches, gradual rollout.
- **Build (8h):** Ship **v7**: production-ready agentic ops layer — MCP server, authorised
  tools, HITL queue, eval gate, deterministic fallback, cost/kill-switch controls, and
  full observability wired into the 6B dashboard. Connect one carefully-reviewed external
  MCP server and document the risk assessment.
- **Reason (3h):** Write your team's policy for adopting a third-party MCP server. What is
  the review checklist? What would you never connect to a system holding customer money?
- **AI drill (2h):** Present the agentic layer to AI as a skeptical security architect.
  Answer every challenge; record the gaps.
- **Deliverable:** **v7 tagged** + `docs/ai/mcp-adoption-policy.md` + risk assessment.
- **Self-check:** ☐ I can explain MCP supply-chain risk to a security team.

---

## Exit checklist — all must pass before 7B

- ☐ Same task built as a workflow and as an agent, compared on real data; decision rule written
- ☐ Agent state persisted and resumable; hard step/token/cost ceilings tested
- ☐ MCP server exposing read and write tools with per-user authorisation
- ☐ Tool layer red-teamed via injected content; escalations found and fixed
- ☐ HITL approval queue working; multi-agent value measured (or rejected with data)
- ☐ Agent eval harness in CI with deterministic replay; cost-per-success known
- ☐ Deterministic fallback path exists and triggers correctly
- ☐ MCP adoption policy written; v7 tagged
