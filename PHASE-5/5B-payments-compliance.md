# 5B — Payments, Compliance & Risk

**Duration:** 6 weeks (108h) · **Months 13.6 – 15.0** · **Prereq:** 5A
**Flagship increment:** v5.5 — Payments platform

> **Outcome:** I can hold a design conversation with a payments product owner, a risk
> officer and a compliance officer without needing a translator — and design systems
> that satisfy all three.

**Scope discipline (from the source doc):** *the goal is engineering/domain literacy,
not becoming a compliance specialist.* Learn enough to design correctly and ask the
right questions. Do not go down the certification rabbit hole.

---

## Week 1 — Payment rails & schemes

- **Learn (5h):** How money actually moves: card networks (issuer, acquirer, scheme,
  authorisation vs clearing vs settlement, interchange), ACH/SEPA (batch, cut-off times,
  returns, R-transactions), wire/RTGS (SWIFT, real-time gross settlement), instant
  payments (SEPA Instant, FPS, and regionally relevant rails). Correspondent banking and
  nostro/vostro in practice. **ISO 20022** message structure (pain/pacs/camt) and the
  ongoing migration from MT. Open Banking / PSD2 APIs.
- **Build (8h):** Model three payment rails in the flagship as strategy implementations
  behind one `PaymentRail` port, each with different timing, finality and failure
  semantics. Parse one real ISO 20022 `pacs.008` message.
- **Reason (3h):** For each rail: when is the payment final and irrevocable? What can be
  returned, and within what window? How does that change your ledger design and your
  customer-facing status model?
- **AI drill (2h):** Ask AI to explain settlement finality per rail. Verify against scheme
  documentation — this is an area with a lot of confidently-wrong AI output.
- **Deliverable:** three rail adapters + finality/returns matrix + one parsed ISO 20022 message.
- **Self-check:** ☐ I can explain authorisation vs clearing vs settlement for a card payment.

## Week 2 — Payment orchestration & state

- **Learn (5h):** The payment state machine in full: initiated, validated, risk-checked,
  authorised, captured, sent, settled, returned, reversed, failed, expired. Retry vs
  reversal vs return. Payment routing and rail selection (cost vs speed vs reliability).
  Timeout and the **unknown-outcome problem** — the hardest state in payments: you sent
  it, the response was lost, and you do not know if it went through. Status enquiry /
  reconciliation-driven resolution. Cut-off times and batch windows. Scheduled and
  recurring payments, mandates.
- **Build (8h):** Implement payment orchestration as a saga (reusing 3B) with an explicit
  `UNKNOWN` state, a status-enquiry poller, and reconciliation-driven resolution. Test:
  send a payment, lose the response, then resolve it correctly both ways.
- **Reason (3h):** What does the customer see while a payment is in `UNKNOWN`? Why is
  auto-retrying an unknown-outcome payment potentially a double-spend, and what makes it safe?
- **AI drill (2h):** *"What happens if the payment response is lost?"* Most AI answers say
  "retry". Push until it reaches idempotency keys plus status enquiry.
- **Deliverable:** payment saga with UNKNOWN handling + lost-response test.
- **Self-check:** ☐ I can explain the unknown-outcome problem and my resolution strategy.

## Week 3 — Fees, limits & risk controls

- **Learn (5h):** Fee models (fixed, percentage, tiered, blended), fee timing (upfront,
  on settlement, monthly), who bears the fee (OUR/SHA/BEN in wires), fee reversal on
  refund. Limits: per transaction, daily, monthly, velocity, per-channel, per-risk-tier;
  limit checks as part of the authorisation invariant. Risk: transaction scoring,
  rule engines vs ML models, false-positive cost, step-up authentication, 3-D Secure,
  strong customer authentication (SCA) and its exemptions.
- **Build (8h):** Implement a fee engine (configurable, versioned — fees change and old
  transactions must keep their original terms) and a limits engine that participates in
  the same transaction as the ledger posting. Add a simple rule-based risk score with a
  decision audit trail.
- **Reason (3h):** Where must the limit check happen so it cannot be raced? (Hint: 2A
  isolation levels + 4A aggregate boundaries.) What is the cost of a false positive to
  the business, and how should that shape thresholds?
- **AI drill (2h):** Ask AI to design the limits check. Test its design for a race
  condition with two concurrent transactions that individually pass but together breach.
- **Deliverable:** versioned fee engine + race-proof limits engine + risk decision log.
- **Self-check:** ☐ I can prove my limit check cannot be raced.

## Week 4 — Compliance literacy: KYC, AML, sanctions

- **Learn (5h):** **KYC/CDD/EDD** — identity verification, document checks, beneficial
  ownership, periodic review, and what each means as a *data and workflow* requirement.
  **AML** — transaction monitoring, typologies (structuring, layering), alerts, case
  management, SAR/STR filing, and why the system must never tip off the customer.
  **Sanctions screening** — list sources, name matching (fuzzy, transliteration), false
  positives, screening at onboarding vs per-transaction, blocking vs rejecting. **PCI DSS**
  at a design level — cardholder data environment scope, tokenisation as a scope-reduction
  strategy, what you must never store (CVV, full track data). **Basel** concepts and
  **ISO 20022** as an audit/reporting substrate.
- **Build (8h):** Add a compliance module to the flagship: a KYC status model gating
  account capability, a sanctions-screening port with fuzzy matching and a
  false-positive review queue, and a transaction-monitoring rule that raises a case.
  Tokenise all card-like data.
- **Reason (3h):** Which flagship data fields are PII, which are cardholder data, and
  which are neither? Which of your logs currently violate that? Fix them. Where does a
  compliance requirement conflict with a UX requirement, and who decides?
- **AI drill (2h):** Ask AI for a compliance checklist for your system, then verify three
  claims against primary sources (PCI SSC, your local regulator). Note how often AI is
  outdated or jurisdiction-blind — the key lesson: **never take regulatory advice from
  an LLM without verification.**
- **Deliverable:** compliance module + a PII/CHD data-classification table + log audit.
- **Self-check:** ☐ I can name three things PCI DSS forbids storing.

## Week 5 — Data protection, residency & the audit conversation

- **Learn (5h):** GDPR-style rights (access, rectification, erasure, portability) and the
  **conflict between the right to erasure and an immutable financial ledger** — how real
  systems resolve it (crypto-shredding, pseudonymisation, legal-basis retention).
  Data residency and cross-border transfer. Encryption key management and rotation over a
  10-year retention period. Right-to-be-forgotten in an event-sourced system. Data
  minimisation as an architecture principle.
- **Build (8h):** Implement crypto-shredding in the flagship: personal data encrypted with
  a per-subject key; erasure destroys the key while the ledger entries remain intact and
  balanced. Prove the ledger still reconciles after an erasure.
- **Reason (3h):** How do you satisfy erasure without breaking the audit trail? Write the
  argument you would give a Data Protection Officer. What did you have to compromise?
- **AI drill (2h):** *"How do I delete a customer from an immutable ledger?"* Judge
  whether AI understands the tension or just picks one side.
- **Deliverable:** crypto-shredding implementation + erasure-preserves-reconciliation test.
- **Self-check:** ☐ I can explain crypto-shredding to a DPO and to an engineer.

## Week 6 — Integration, resilience & consolidation

- **Learn (5h):** Integrating with external financial systems: certificate-based auth,
  IP allowlisting, file-based (SFTP batch) integration and its failure modes, message
  queues with partners, webhook delivery and verification (signatures, replay windows),
  partner sandbox vs production drift. SLA management with partners. Handling partner
  downtime — store-and-forward, queue-and-retry, and the cut-off-time constraint that
  makes "just retry later" unacceptable.
- **Build (8h):** Ship **v5.5**: a partner integration adapter with signed webhooks, an
  SFTP batch file flow with checksum verification and duplicate-file detection, and
  store-and-forward for partner downtime with cut-off-aware alerting. Chaos-test partner
  downtime spanning a cut-off.
- **Reason (3h):** What happens to a payment queued at 16:55 when the cut-off is 17:00 and
  the partner is down until 17:30? Write the policy — this is a business decision you must
  surface, not silently implement.
- **AI drill (2h):** Present the full payments architecture to AI as a skeptical risk
  officer and answer every challenge. Then write down the questions you couldn't answer.
- **Deliverable:** **v5.5 tagged** + partner integration + cut-off policy document.
- **Self-check:** ☐ I can discuss a payments design with product, risk and compliance.

---

## Exit checklist — all must pass before 6A

- ☐ Three payment rails modelled with correct finality and returns semantics
- ☐ Payment saga handles the UNKNOWN state with status enquiry and reconciliation resolution
- ☐ Fee engine is versioned; limits engine is race-proof and tested as such
- ☐ Compliance module: KYC gating, sanctions screening with review queue, monitoring rule
- ☐ PII/CHD data classification done; logs audited and fixed
- ☐ Crypto-shredding works without breaking ledger reconciliation
- ☐ Partner integration with signed webhooks, batch files and cut-off-aware store-and-forward
- ☐ v5.5 tagged

**Phase 5 complete → Month 15.** You now have the domain depth that separates a backend
engineer from a *fintech* backend engineer. Monthly review + re-score.
