# 6B — AI Backend Architecture

**Duration:** 6 weeks (108h) · **Months 16.6 – 18.0** · **Prereq:** 6A
**Flagship increment:** v6.5 — AI platform

> **Outcome:** I can design the *platform* layer that makes LLM features safe,
> affordable, observable and governable across many teams — not just one working feature.

**Framing:** in 6A you built a feature. Here you build the thing every feature goes
through. This is the difference between "engineer who used an LLM" and "engineer who
designed our AI platform" — and it is the most senior-differentiating sub-roadmap in
Phase 6.

---

## Week 1 — The AI Gateway

- **Learn (5h):** Why a gateway: one place for auth, quota, routing, caching, logging,
  cost attribution, safety and provider abstraction. Provider abstraction without
  lowest-common-denominator design (streaming, tool calling and caching differ per
  provider). Multi-tenancy and per-tenant quota. Failover between providers. Streaming
  proxying (SSE) and its backpressure problems. Timeouts, and cancellation propagation
  when a client disconnects mid-stream.
- **Build (8h):** Build the AI Gateway as a module in the flagship: a single entry point
  for all LLM calls, provider-agnostic interface, per-tenant auth and quota, streaming
  passthrough with cancellation, and structured logging of every call.
- **Reason (3h):** What does the gateway make possible that per-service SDK calls do not?
  What does it cost (latency hop, single point of failure, abstraction leakage)? Is it
  worth it at your team size — argue honestly.
- **AI drill (2h):** *"Give me the strongest argument against building an AI gateway."*
  (Real answers exist: premature abstraction, one team, provider features lost.) Then decide.
- **Deliverable:** gateway module + `docs/adr/0010-ai-gateway.md`.
- **Self-check:** ☐ I can explain what belongs in the gateway and what does not.

## Week 2 — Model routing, cost control & caching

- **Learn (5h):** Model routing strategies: by task complexity, by tenant tier, by cost
  budget, cascade/fallback (try small, escalate on low confidence). Measuring routing
  quality — a cheaper model that fails more can cost more end-to-end. **Cost control**:
  per-tenant budgets, hard vs soft limits, token quotas, spend alerting, cost attribution
  to features. **Caching**: exact-match cache, semantic cache (and its correctness risk),
  provider prompt caching, and when a cached answer becomes a compliance problem.
  Batching and async processing for non-interactive work.
- **Build (8h):** Add routing (cheap model → escalate on low confidence), a semantic cache
  with a similarity threshold you tuned from data, and per-tenant cost budgets with
  enforcement. Measure cost per request before and after; measure the cache's false-hit rate.
- **Reason (3h):** What is your semantic cache's false-hit rate, and what is the business
  cost of one wrong cached answer in a banking context? Where must caching be disabled entirely?
- **AI drill (2h):** Ask AI to design your routing policy. Then check whether it accounted
  for the cost of escalation (you pay twice) — most don't.
- **Deliverable:** routing + semantic cache with measured false-hit rate + budget enforcement.
- **Self-check:** ☐ I can state my cost per request and the effect of each optimisation.

## Week 3 — Prompt management & context management

- **Learn (5h):** Prompts as versioned artefacts: storage, versioning, review, staged
  rollout, rollback, A/B testing. Prompt templates with typed variables. Separating prompt
  from code deployment (and the risk that creates). **Context management**: conversation
  persistence, message-history strategies (full / windowed / summarised / hierarchical),
  token budgeting across system prompt + history + retrieved context + output reservation,
  and what to evict first. Session state and multi-turn coherence. Memory: short-term vs
  long-term, user profiles, and the privacy implications of persisting them.
- **Build (8h):** Build prompt management (versioned, reviewable, A/B-testable with the
  eval harness from 6A as the gate) and conversation persistence with a summarisation-based
  history strategy under an explicit token budget.
- **Reason (3h):** Should a prompt change be able to ship without a code deploy? Argue both
  sides, then decide and write the governance rule. What happens to a long conversation at
  turn 200 in your design?
- **AI drill (2h):** Have AI critique your context-management strategy for a 50-turn
  conversation. Then test it — measure where coherence actually degrades.
- **Deliverable:** prompt registry + conversation store + token-budget policy.
- **Self-check:** ☐ I can explain my token budget allocation and eviction order.

## Week 4 — Guardrails & safety

- **Learn (5h):** Input guardrails: prompt-injection detection, PII detection and
  redaction before the provider sees it, topic and jurisdiction restriction, rate-limiting
  abuse. Output guardrails: schema validation, groundedness checking, PII leakage
  detection, toxicity/appropriateness, forbidden-advice detection (in fintech: no
  investment advice, no credit decisions). Defence in depth — no single guardrail is
  sufficient. Human-in-the-loop escalation. Fail-closed vs fail-open, and why the answer
  differs per feature. Red-teaming your own system.
- **Build (8h):** Build a guardrail pipeline in the gateway: PII redaction inbound,
  injection detection, output schema + groundedness + forbidden-content checks, with a
  configurable fail-closed policy per feature. Then **red-team it** for a full session and
  log every bypass.
- **Reason (3h):** For each flagship AI feature: fail-closed or fail-open, and why? What
  is the worst output your system could produce, and what specifically prevents it?
- **AI drill (2h):** Use one model to attack the system guarded by another. Document every
  successful bypass — this list is your security backlog.
- **Deliverable:** guardrail pipeline + red-team report with bypasses and fixes.
- **Self-check:** ☐ I can name the worst possible output and the specific control that stops it.

## Week 5 — Observability & evaluation in production

- **Learn (5h):** LLM-specific telemetry: token counts, cost, TTFT, total latency, cache
  hit rate, guardrail trigger rate, refusal rate, tool-call success rate, per-model and
  per-tenant breakdowns. Tracing an LLM call chain (retrieval spans, model spans, tool
  spans) with OpenTelemetry GenAI conventions. Sampling full prompt/response payloads —
  and the privacy rules for doing so. Online evaluation: user feedback, implicit signals,
  shadow evaluation, canary prompts. Drift detection when a provider silently updates a
  model. Incident response for AI features.
- **Build (8h):** Full observability for the AI path: dashboard with cost, latency,
  quality and guardrail metrics; traces covering retrieval → model → tools; a nightly
  canary eval that alerts on quality regression; PII-safe payload sampling.
- **Reason (3h):** How would you detect that the provider silently changed the model? What
  is your rollback plan when quality drops and you didn't change anything?
- **AI drill (2h):** Simulate a quality regression (swap in a weaker model) and see whether
  your monitoring catches it before you tell it to. If not, fix the monitoring.
- **Deliverable:** AI observability dashboard + nightly canary eval + drift alert.
- **Self-check:** ☐ I would detect a silent model change within 24 hours.

## Week 6 — AI architecture patterns & consolidation

- **Learn (5h):** Reference patterns: synchronous assist, async enrichment, batch
  processing, human-in-the-loop review queues, AI-as-a-suggestion vs AI-as-a-decision.
  Where the AI layer sits relative to the domain (hint: as an adapter in your hexagonal
  architecture, never inside the domain). Data flows for training/eval and the governance
  around them. Vendor lock-in and exit strategy. Build vs buy for AI infrastructure. Cost
  modelling at scale. The **AI decision-rights** question: which decisions may a model
  make, which may it only suggest.
- **Build (8h):** Ship **v6.5**: place the AI layer correctly in the hexagonal
  architecture (ports/adapters, domain stays pure), add a human-in-the-loop review queue
  for AI suggestions on high-value operations, and write the full architecture document.
- **Reason (3h):** Which flagship decisions may AI make autonomously, which require human
  approval, and which must never involve AI? Write this as policy with reasoning — it is
  the artefact a Head of Engineering would actually ask you for.
- **AI drill (2h):** Present the whole AI platform to AI as a skeptical CTO focused on
  cost and risk. Answer every challenge. Record what you couldn't answer.
- **Deliverable:** **v6.5 tagged** + `docs/ai/ai-platform-architecture.md` +
  `docs/ai/decision-rights-policy.md`.
- **Self-check:** ☐ I can present this platform to a non-AI-specialist executive.

---

## Exit checklist — all must pass before 7A

- ☐ AI gateway handling auth, quota, routing, streaming and cancellation
- ☐ Model routing + semantic cache with a measured false-hit rate + enforced budgets
- ☐ Versioned prompt registry gated by the 6A eval harness
- ☐ Guardrail pipeline red-teamed, with bypasses documented and fixed
- ☐ AI observability dashboard + nightly canary eval that catches an injected regression
- ☐ AI layer sits as an adapter; the domain model contains no AI dependency
- ☐ Decision-rights policy written
- ☐ v6.5 tagged

**Phase 6 complete → Month 18.** Monthly review. Your AI Engineering score should now be
the *highest-growth* line on your chart.
