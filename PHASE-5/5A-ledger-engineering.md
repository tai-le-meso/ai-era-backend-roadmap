# 5A — Banking Domain & Ledger Engineering

**Duration:** 7 weeks (126h) · **Months 12.0 – 13.6** · **Prereq:** 4B
**Flagship increment:** v5 — Real double-entry ledger

> **Outcome:** I can build a double-entry ledger that is provably correct, immutable,
> auditable, and fast enough — and I can explain balance semantics to a product manager
> and to an auditor in their own language.

**Why domain depth matters:** AI can write a ledger service. It cannot tell you that
your "balance" column will fail an audit, or that pending authorisations must reduce
available balance but not ledger balance. Domain knowledge is where senior engineers
become irreplaceable.

---

## Week 1 — The banking domain vocabulary

- **Learn (5h):** The core entities and how they relate: Customer, Account (types:
  current, savings, nostro/vostro, suspense, clearing), Wallet, Ledger, Transaction,
  Payment, Transfer, Settlement, Reconciliation, Balance, Currency, FX, Fees, Limits,
  Risk, Audit. Party vs customer vs account holder. Product vs account. Account
  hierarchy and chart of accounts. Read a real bank's API docs (open banking specs are
  public) to see the vocabulary in production use.
- **Build (8h):** Build a glossary in the flagship as executable documentation: each term
  as a value object or type with a Javadoc definition. Draw the entity-relationship map
  for the whole banking domain.
- **Reason (3h):** Where does your current employer's system use these words *differently*
  from the industry meaning? Those mismatches are where bugs and miscommunication live.
- **AI drill (2h):** Ask AI to define each term, then compare against an authoritative
  source (ISO 20022 data dictionary, a central-bank glossary). Note every drift.
- **Deliverable:** `docs/domain/banking-glossary.md` + ER map.
- **Self-check:** ☐ I can explain nostro/vostro and suspense accounts unprompted.

## Week 2 — Double-entry bookkeeping

- **Learn (5h):** The accounting equation (Assets = Liabilities + Equity). Debits and
  credits — and the fact that a customer deposit is a **liability** to the bank. Journal
  entries, T-accounts, the general ledger, sub-ledgers, chart of accounts, trial balance,
  the rule that every entry must balance to zero. Why software engineers get this wrong:
  treating balance as a mutable column instead of a derived fact.
- **Build (8h):** Implement the ledger core: immutable `JournalEntry` with ≥2
  `LedgerLine`s, a database constraint or trigger enforcing sum-to-zero per entry, and
  append-only enforcement (no UPDATE, no DELETE — revoke the grants, don't just promise).
  Write a property-based test (jqwik) asserting the trial balance is always zero across
  thousands of random operations.
- **Reason (3h):** Model a customer deposit, a withdrawal, a fee, and an inter-bank
  transfer as journal entries. Which account is debited and which credited, and why?
- **AI drill (2h):** Ask AI to model these four flows in double entry. Verify against an
  accounting reference — models frequently get the customer-deposit-as-liability
  direction backwards.
- **Deliverable:** immutable ledger + zero-sum property test + 4 worked journal entries.
- **Self-check:** ☐ I can say which side a customer deposit lands on and why.

## Week 3 — Balances: ledger, available, pending

- **Learn (5h):** Ledger balance vs **available** balance vs **pending**. Authorisation
  holds and their expiry. Overdraft and credit limits as part of available balance.
  Balance derivation strategies: compute-from-entries (correct, slow), running-balance
  column (fast, dangerous), **snapshot + delta** (the production answer). Snapshot
  cadence and rebuild-from-scratch capability. Concurrency: the hot-account problem
  (link back to 2A Week 3 and 4A aggregate sizing).
- **Build (8h):** Implement all three balance types with a snapshot+delta strategy and a
  rebuild command that recomputes every balance from journal entries and asserts equality
  with the snapshots. Load-test a hot account and record the throughput ceiling.
- **Reason (3h):** Why is a running-balance column a correctness hazard? What is your
  snapshot cadence and what does it cost you on rebuild? What is your hot-account limit
  in TPS, measured?
- **AI drill (2h):** *"Give me the strongest argument against my balance strategy."*
  Force it beyond "eventual consistency is hard" into specifics about your design.
- **Deliverable:** three balance types + rebuild-and-verify job + hot-account benchmark.
- **Self-check:** ☐ I can explain to a PM why the app shows two different balances.

## Week 4 — Money, monetary precision & currency

- **Learn (5h):** Why floating point is banned for money (demonstrate it, don't just
  believe it). `BigDecimal` scale and rounding modes, minor units and integer
  representation, currency-specific decimal places (JPY 0, BHD 3), ISO 4217. Rounding:
  half-even (banker's) vs half-up, and where regulation dictates it. **Allocation** —
  splitting 100.00 three ways without losing a cent (largest-remainder method). FX: rate
  sources, bid/ask spread, rate validity windows, triangulation, and the rule that you
  never mix currencies in one arithmetic operation.
- **Build (8h):** Build a `Money` type properly: currency-aware, immutable, no
  cross-currency arithmetic without an explicit conversion carrying its rate and
  timestamp. Implement `allocate(int n)` and prove no cent is created or lost with a
  property test. Add multi-currency accounts and an FX conversion that records the rate used.
- **Reason (3h):** Where does rounding error accumulate in the flagship? Who wins the
  fraction — the bank or the customer — and is that a policy decision someone made
  deliberately?
- **AI drill (2h):** Ask AI to implement money allocation. Test its output for cent loss.
  This is a reliable failure case.
- **Deliverable:** `Money` type + allocation property test + FX with rate provenance.
- **Self-check:** ☐ I can explain banker's rounding and when regulation requires it.

## Week 5 — Transaction lifecycle, reversal & adjustment

- **Learn (5h):** Transaction states: initiated → authorised → captured → settled →
  reversed / failed / expired. State-machine design with sealed types (from 1A). Why you
  never delete or edit a ledger entry: **reversal** (a compensating entry) vs
  **adjustment** (a correcting entry) vs **cancellation** (before posting). Backdating
  and value dates vs booking dates (bitemporal from 4A Week 7). Partial capture, partial
  refund, chargeback.
- **Build (8h):** Implement the full transaction state machine with reversal and
  adjustment. Enforce at the type level that a ledger entry cannot be mutated. Add value
  date vs booking date and a query that answers "what was the balance on date D as known
  on date D+2".
- **Reason (3h):** What is the difference between reversal and adjustment to an auditor?
  Why does a "just fix the row" hotfix destroy audit integrity — what specifically breaks?
- **AI drill (2h):** Ask AI to "fix an incorrect transaction". If it suggests an UPDATE,
  that is the teachable moment — document why it is wrong.
- **Deliverable:** state machine + reversal/adjustment + as-of-date balance query.
- **Self-check:** ☐ I can explain reversal vs adjustment to a non-technical person.

## Week 6 — Reconciliation

- **Learn (5h):** Why reconciliation exists: two systems will disagree, and the question
  is only when you find out. Internal reconciliation (sub-ledger vs general ledger),
  external reconciliation (your ledger vs the bank/scheme statement). Matching strategies:
  exact, fuzzy, one-to-many, many-to-many. Break categories: timing differences, missing,
  duplicated, amount mismatch. Break investigation workflow and ageing. Suspense-account
  handling. Automated vs manual resolution. Reconciliation as a **first-class product
  feature**, not a batch script.
- **Build (8h):** Build the reconciliation module: ingest an external statement file,
  match against ledger entries, classify breaks, age them, expose a break-resolution API.
  Seed deliberate breaks of each category and verify classification.
- **Reason (3h):** What is your acceptable break rate and ageing SLA? Which break category
  indicates a bug versus a normal timing difference? How would a silent data-loss bug show
  up here first?
- **AI drill (2h):** Give AI two ledgers with injected differences and ask it to classify.
  Then check its false-positive and false-negative rate — a concrete lesson in AI reliability.
- **Deliverable:** reconciliation module + break classification tests.
- **Self-check:** ☐ I can design a matching strategy for a new external partner.

## Week 7 — Audit, immutability & consolidation

- **Learn (5h):** Audit requirements: who did what, when, from where, and what the value
  was before and after. Append-only design, hash chaining / tamper evidence, WORM storage.
  Event sourcing for the ledger — where it genuinely fits (this is one of the few places
  it does). Retention requirements in finance (typically 7–10 years). Segregation of
  duties, maker-checker workflows, four-eyes approval. Data lineage.
- **Build (8h):** Ship **v5**: add a tamper-evident audit log (hash-chained entries),
  maker-checker approval for high-value operations, and a full audit-trail query API.
  Run a verification job that detects a tampered record.
- **Reason (3h):** If a regulator asked "prove this balance was correct on 30 June", what
  exactly would you show them and how long would it take to produce? If the answer is
  "I'd write a query", that is not good enough — build the report.
- **AI drill (2h):** Have AI act as an auditor and interrogate your ledger design. Answer
  every question. The ones you can't answer are your gaps.
- **Deliverable:** **v5 tagged** + tamper-evidence verification + a regulator-ready
  point-in-time balance report.
- **Self-check:** ☐ I can produce a point-in-time balance proof on demand.

---

## Exit checklist — all must pass before 5B

- ☐ Ledger is append-only at the database-permission level, not just by convention
- ☐ Trial balance sums to zero, proven by a property test over thousands of operations
- ☐ Ledger / available / pending balances all implemented with a rebuild-and-verify job
- ☐ `Money` type with allocation that provably loses no cents; FX carries rate provenance
- ☐ Reversal and adjustment implemented; no mutation path exists
- ☐ Reconciliation module classifies all break categories
- ☐ Tamper-evident audit trail with a working verification job
- ☐ Point-in-time balance report produced on demand; v5 tagged
