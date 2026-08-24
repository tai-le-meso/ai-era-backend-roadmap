# 6A — LLM Fundamentals & RAG

**Duration:** 7 weeks (126h) · **Months 15.0 – 16.6** · **Prereq:** 5B (or 4B if you swapped order)
**Flagship increment:** v6 — AI-assisted with a measured evaluation set

> **Outcome:** I can build a retrieval-augmented feature whose quality I can **measure**,
> whose cost and latency I can predict, and whose failure modes I can name — rather than
> a demo that works on five hand-picked questions.

**Framing:** treat the LLM as an unreliable remote dependency with a probabilistic
contract. Everything you learned in Phase 3 about timeouts, retries, circuit breakers,
fallbacks and idempotency applies here — most people forget that, which is exactly the
gap you are filling.

---

## Week 1 — LLM mechanics from a backend engineer's view

- **Learn (5h):** Tokens and tokenisation (why token ≠ word, why cost and limits are in
  tokens). Context window: what fits, what gets truncated, position effects
  ("lost in the middle"). Sampling: temperature, top-p, and determinism (or the lack of
  it). Latency structure: time-to-first-token vs tokens-per-second, streaming, and why
  prompt length drives prefill cost. Pricing models, prompt caching. Model families and
  the capability/cost/latency triangle. **Limitations**: hallucination, no ground truth,
  no reliable self-knowledge, prompt sensitivity, reasoning vs retrieval.
- **Build (8h):** Build a thin LLM client in the flagship with: explicit timeouts,
  retries with jitter (only on retryable errors), a circuit breaker, token counting,
  cost accounting per request, and full request/response logging with trace correlation.
  Measure TTFT and total latency distribution over 200 calls.
- **Reason (3h):** What is your p99 LLM latency and what does that do to your API's SLO?
  What is the cost per request, and per 1000 users per month? Where should this call be
  async instead of in the request path?
- **AI drill (2h):** Ask the model factual questions about your own domain where you know
  the answer. Log the hallucination rate. This number is your calibration for everything
  that follows.
- **Deliverable:** production-grade LLM client + latency/cost measurement report.
- **Self-check:** ☐ I can state my cost per request and p99 latency as numbers.

## Week 2 — Prompting, structured output & tool calling

- **Learn (5h):** Prompt structure that actually matters: clear instructions, examples
  (few-shot), explicit output format, negative examples, step-by-step reasoning requests,
  role/system framing. **Structured output**: JSON schema enforcement, why free-text
  parsing is a bug, validation and repair loops. **Tool/function calling**: schema design,
  when the model calls, parallel calls, handling refusals and malformed calls. Prompt
  injection as an input-validation problem (you already know how to think about untrusted
  input — apply it).
- **Build (8h):** Build a structured-extraction feature: parse an unstructured payment
  instruction into a validated command object with a JSON schema, a validation layer, and
  a repair-retry. Measure the schema-violation rate before and after your repair loop.
- **Reason (3h):** What happens when the model returns valid JSON with wrong values? Which
  of your fields must be verified against your own data rather than trusted? Where would a
  prompt injection in a payment reference field do damage?
- **AI drill (2h):** Try to prompt-inject your own feature. Document every successful
  attack and your mitigation.
- **Deliverable:** structured extraction with a measured violation rate + injection test suite.
- **Self-check:** ☐ I never parse free text where a schema would do.

## Week 3 — Embeddings & vector search

- **Learn (5h):** Embeddings — what a vector actually represents, model choice,
  dimensionality, normalisation, cosine vs dot vs euclidean. Vector indexes: exact kNN vs
  approximate (HNSW, IVF), recall/latency trade-off, index parameters that matter.
  **pgvector** in PostgreSQL (you already run PostgreSQL — start here, and only add a
  dedicated vector DB if you can justify it). Metadata filtering and the pre-filter vs
  post-filter problem. Multi-tenancy in a vector store.
- **Build (8h):** Add pgvector to the flagship. Embed a corpus (transaction descriptions,
  product docs, support articles). Build vector search with metadata filtering scoped by
  tenant. Measure recall against an exact-kNN baseline at several index settings.
- **Reason (3h):** What recall are you actually getting, and what does the loss cost you?
  Why does post-filtering break your top-k? What is the storage and index-build cost at 10× corpus?
- **AI drill (2h):** *"Should I use a dedicated vector database instead of pgvector?"*
  Make AI argue both sides, then decide from your measured numbers, not its preference.
- **Deliverable:** pgvector search + recall measurement + `docs/adr/0009-vector-store.md`.
- **Self-check:** ☐ I can explain the pre-filter vs post-filter trade-off.

## Week 4 — Chunking, hybrid search & reranking

- **Learn (5h):** Chunking strategies: fixed-size, overlap, sentence/paragraph, semantic,
  structure-aware (headings, tables), parent-document retrieval. Why chunking is usually
  the single biggest lever on RAG quality. **Hybrid search**: BM25/full-text + vector,
  score fusion (reciprocal rank fusion), and why keyword search still wins for IDs,
  codes and exact terms — highly relevant in banking. **Reranking**: cross-encoders,
  retrieve-many-rerank-few, latency cost. Query transformation: rewriting, expansion,
  HyDE, multi-query.
- **Build (8h):** Build hybrid retrieval (PostgreSQL full-text + pgvector, fused with RRF)
  plus a reranker stage. Run an ablation: vector-only vs keyword-only vs hybrid vs
  hybrid+rerank, measured on your eval set (built next week — or build a small one now).
- **Reason (3h):** Which query types does vector search fail on in your corpus? (Account
  numbers, error codes, exact amounts.) What does reranking cost in latency, and is it worth it?
- **AI drill (2h):** Ask AI for the "best chunking strategy". Note that it cannot know
  without your corpus — the lesson is that this is an empirical question, not a knowledge one.
- **Deliverable:** hybrid retrieval + reranker + ablation table with numbers.
- **Self-check:** ☐ I can name a query type where vector search reliably loses.

## Week 5 — Evaluation: making quality a number

- **Learn (5h):** Why "it looks good" is not an engineering standard. Building a **golden
  dataset** (50–200 real questions with expected answers/sources). Retrieval metrics:
  recall@k, precision@k, MRR, NDCG. Generation metrics: faithfulness/groundedness, answer
  relevance, citation accuracy. LLM-as-judge — its uses, its biases (position, verbosity,
  self-preference) and how to calibrate it against human labels. Regression testing for
  prompts. Offline vs online eval, A/B testing, user feedback signals.
- **Build (8h):** Build the eval harness: a golden dataset for the flagship, automated
  retrieval + generation metrics, and a **CI gate** that fails the build when quality
  regresses. Calibrate your LLM judge against 30 of your own human labels.
- **Reason (3h):** What is your current faithfulness score, and what is an acceptable
  threshold for a *financial* assistant? Which failure is worse here — a wrong answer or
  a refusal? That answer sets your entire tuning direction.
- **AI drill (2h):** Have the judge model evaluate answers you deliberately corrupted.
  Does it catch them? Measure judge accuracy — do not trust an uncalibrated judge.
- **Deliverable:** golden dataset + eval harness + CI quality gate + judge calibration report.
- **Self-check:** ☐ I can state my RAG's faithfulness and recall@5 as current numbers.

## Week 6 — Grounding, citation & the full RAG architecture

- **Learn (5h):** Context construction: ordering, deduplication, token budgeting, what to
  drop first. **Citation and grounding**: forcing source attribution, verifying citations
  actually support the claim, refusing when context is insufficient (the single most
  valuable behaviour in a regulated domain). Handling conflicting sources. Freshness and
  incremental re-indexing. Access control in retrieval — **never retrieve documents the
  user cannot see** (the most common serious RAG security bug).
- **Build (8h):** Ship **v6**: the full RAG path `User → API → Retrieval → LLM →
  Structured Response` with per-user access-scoped retrieval, enforced citations, a
  verification step that checks each citation supports its claim, and an explicit refusal
  path. Write a test proving a user cannot retrieve another tenant's documents.
- **Reason (3h):** How do you prove an answer was grounded? What is your refusal rate and
  is it too low (over-confident) or too high (useless)? What is the audit trail for an
  AI-generated answer in a bank?
- **AI drill (2h):** Try to make your own system produce an ungrounded confident answer.
  Every success is a bug to fix.
- **Deliverable:** **v6 tagged** + access-control test + citation verification.
- **Self-check:** ☐ My retrieval is access-scoped and I have a test proving it.

## Week 7 — When NOT to use an LLM + consolidation

- **Learn (5h):** The decision framework: is this task deterministic? Is a rule engine,
  a search index, or a SQL query the correct answer? Cost per outcome vs traditional
  implementation. Latency budget. Auditability and explainability requirements —
  and where regulation effectively forbids a non-deterministic decision (credit
  decisions, sanctions blocking). Fine-tuning vs RAG vs prompt engineering vs plain code.
  Small models and where they suffice.
- **Build (8h):** Take three flagship features currently using the LLM. For each, build
  the non-LLM alternative and compare quality, latency and cost. **Remove the LLM from at
  least one of them** and document why.
- **Reason (3h):** Write the decision framework as a document your team could use. Where
  in the flagship would an LLM decision be non-compliant regardless of accuracy?
- **AI drill (2h):** *"Give me the strongest argument that this feature should not use an
  LLM at all."* Then apply that argument to your remaining features.
- **Deliverable:** `docs/ai/when-not-to-use-an-llm.md` + one feature de-LLM'd with data.
- **Self-check:** ☐ I have removed an LLM from a feature and can justify it with numbers.

---

## Exit checklist — all must pass before 6B

- ☐ LLM client has timeouts, retries, circuit breaker, cost accounting and trace correlation
- ☐ Structured output with schema validation; prompt-injection tests written
- ☐ pgvector retrieval with measured recall against an exact baseline
- ☐ Hybrid search + reranking with an ablation table of real numbers
- ☐ Golden dataset + eval harness + CI quality gate; LLM judge calibrated against human labels
- ☐ Access-scoped retrieval with a cross-tenant leakage test
- ☐ Citations enforced and verified; explicit refusal path exists
- ☐ At least one feature de-LLM'd with data justifying it; v6 tagged
