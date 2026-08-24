# 4A — Domain-Driven Design

**Duration:** 7 weeks (126h) · **Months 9.0 – 10.6** · **Prereq:** 3B
**Flagship increment:** v4 — Modular Monolith

> **Outcome:** I can take an ambiguous business conversation and produce a bounded-context
> map, an aggregate design with defensible boundaries, and code where the domain model —
> not the ORM — is in charge.

**Mindset shift for this sub-roadmap:** Phases 1–3 were about machines. This one is
about people and language. If you cannot get access to a real domain expert, use your
own product owner, or model a domain you genuinely understand.

---

## Week 1 — Strategic DDD: domains and subdomains

- **Learn (5h):** Domain vs subdomain. **Core** (your competitive advantage — build it,
  invest your best people), **Supporting** (necessary, not differentiating — build simply),
  **Generic** (buy or use off-the-shelf). Domain vision statement. Why misclassifying a
  subdomain is the most expensive architecture mistake you can make. Ubiquitous language
  and how it dies.
- **Build (8h):** Map the flagship banking platform into subdomains. Classify each as
  core/supporting/generic *with justification*. For a real bank: is payment routing core?
  Is KYC generic? Argue it.
- **Reason (3h):** For your actual employer's product — which subdomain is truly core?
  Where is the company spending effort on generic subdomains? Write it down honestly.
- **AI drill (2h):** Ask AI to classify your subdomains. Where it disagrees with you,
  find out which of you has the better argument — AI has no business context, so it
  should lose, but check *why*.
- **Deliverable:** `docs/domain/subdomain-map.md` with classification and rationale.
- **Self-check:** ☐ I can explain why classifying a subdomain wrong costs money.

## Week 2 — Bounded contexts & context mapping

- **Learn (5h):** Bounded Context as a **linguistic** boundary, not a technical one. The
  same word meaning different things ("Account" in ledger vs in identity vs in CRM).
  Context Map relationship patterns: Shared Kernel, Customer/Supplier, Conformist,
  **Anticorruption Layer**, Open Host Service, Published Language, Separate Ways,
  Partnership, Big Ball of Mud. Context maps as an org-and-politics artefact (Conway's Law).
- **Build (8h):** Draw the flagship's context map: Customer, Account, Ledger, Payment,
  Notification, Reconciliation, Audit. Define each relationship type explicitly. Build an
  **anticorruption layer** for one external integration (a mock core-banking system with
  a deliberately awful API).
- **Reason (3h):** Where does "Account" mean different things in your map? What does the
  ACL protect you from, concretely? Which relationship in your map is really a Conformist
  because of org politics rather than design?
- **AI drill (2h):** *"Where are my bounded-context boundaries wrong?"* Push AI to find
  a boundary that will leak. Then check whether it would actually leak.
- **Deliverable:** context map diagram + ACL implementation + `docs/domain/context-map.md`.
- **Self-check:** ☐ I can explain Conformist vs ACL and when to accept each.

## Week 3 — Event Storming & discovering the model

- **Learn (5h):** Event Storming (big-picture → process-level → design-level): domain
  events, commands, actors, policies, read models, aggregates, hotspots. Facilitation
  technique. Domain Storytelling. Example Mapping for requirements. How to run this with
  non-technical stakeholders.
- **Build (8h):** Run a solo (or with a colleague) event storm on the **payment flow**.
  Produce the orange-sticky timeline of domain events, then derive commands, policies and
  aggregates. Convert the output into an actual event catalogue for the flagship.
- **Reason (3h):** Which events did you discover that your current code has no concept of?
  Which of your existing Kafka events are actually *commands* mislabelled as events?
- **AI drill (2h):** Give AI the event timeline and ask it to propose aggregate
  boundaries. Compare with your own. Where you differ, find the invariant that decides it.
- **Deliverable:** event-storm photo/diagram + `docs/domain/event-catalogue.md`.
- **Self-check:** ☐ I can tell a command from an event from a policy, every time.

## Week 4 — Tactical DDD: entities, value objects, aggregates

- **Learn (5h):** Entity (identity over time) vs **Value Object** (defined by attributes,
  immutable, side-effect-free functions). Aggregate and Aggregate Root: the **consistency
  boundary**. Vernon's four rules: model true invariants in boundaries; design small
  aggregates; reference other aggregates by identity only; update other aggregates
  eventually (via domain events). Optimistic concurrency on the aggregate root. Why one
  transaction should modify one aggregate.
- **Build (8h):** Refactor the flagship's `Account` and `Transfer` into proper aggregates.
  Enforce: no cross-aggregate object references (IDs only), all invariants inside the
  root, one aggregate per transaction. Make `Money`, `AccountNumber`, `Currency`, `IBAN`
  proper value objects with validation.
- **Reason (3h):** What is the true invariant that defines each aggregate boundary? Where
  did you have a large aggregate and what contention does it cause (link back to 2A's
  hot-row work)?
- **AI drill (2h):** *"Is my aggregate too large?"* Then verify with a load test — a large
  aggregate shows up as lock contention, not just as an opinion.
- **Deliverable:** aggregate implementations + invariant tests + contention measurement.
- **Self-check:** ☐ I can state the four aggregate design rules and why each exists.

## Week 5 — Repositories, domain services, domain events & application services

- **Learn (5h):** Repository as a **collection abstraction** for aggregate roots only —
  and why a repository per entity is a smell. Persistence-ignorant domain models vs JPA
  reality (the leaky-abstraction fight). Domain Service (logic that belongs to no single
  entity) vs Application Service (orchestration, transactions, no business rules).
  Domain Events: raising them inside the aggregate, publishing after commit,
  `@TransactionalEventListener(AFTER_COMMIT)`, and connecting to the outbox from 2C.
  Factories. Specification pattern.
- **Build (8h):** Restructure the flagship: repositories only for aggregate roots; a
  `TransferPolicy` domain service; application services that contain zero business logic;
  domain events raised in aggregates and published via the existing outbox.
- **Reason (3h):** Which of your current "services" are actually domain services and which
  are application services? Where has business logic leaked into a controller or a repository?
- **AI drill (2h):** Give AI an application service and ask it to identify business logic
  that belongs in the domain. Verify each finding by trying to move it.
- **Deliverable:** clean domain/application separation with a package-structure test
  (ArchUnit) enforcing the dependency rule.
- **Self-check:** ☐ I can point at any class and say which layer it belongs in and why.

## Week 6 — Architecture styles: hexagonal, clean, modular monolith

- **Learn (5h):** Layered vs Hexagonal (Ports & Adapters) vs Onion vs Clean — what is
  actually the same idea and what differs. The **dependency rule**. Driving vs driven
  ports. Adapters for web, persistence, messaging. Modular Monolith: module boundaries,
  enforced dependencies, module-internal packages, a shared kernel done properly, and the
  path to extraction. Microservices — when the *organisational* cost is justified. The
  distributed monolith antipattern.
- **Build (8h):** Ship **v4**: restructure the flagship as a **Modular Monolith** with
  hexagonal modules per bounded context. Enforce boundaries with ArchUnit (no module may
  import another's internal package; communication only via published events or public API).
- **Reason (3h):** Which two modules are most coupled, and is that coupling essential or
  accidental? Which module would you extract first as a service, and what would you need
  before you could?
- **AI drill (2h):** *"Give me the strongest argument that this should be microservices
  today."* Then write the rebuttal. Keep both — this is the classic architecture-review
  debate you must be able to run.
- **Deliverable:** **v4 tagged** + ArchUnit boundary tests + `docs/adr/0007-modular-monolith.md`.
- **Self-check:** ☐ I can draw hexagonal architecture and name every port and adapter in my repo.

## Week 7 — Domain modelling under pressure + consolidation

- **Learn (5h):** Modelling time: temporal models, effective dating, bitemporal data
  (valid time vs transaction time) — essential for banking. Modelling money and
  quantities. Invariants that span aggregates and how to enforce them eventually.
  Refactoring toward deeper insight. Model rot and how ubiquitous language decays. CQRS
  as a modelling tool (separate read models from the domain model).
- **Build (8h):** Add bitemporal support to one flagship entity (e.g. account limits
  change over time; you must answer "what was the limit on 3 March as we understood it on
  5 March?"). Build one CQRS read model projected from domain events.
- **Reason (3h):** Which flagship questions require bitemporal data to answer correctly?
  Where would a naive `updated_at` column give you a wrong audit answer?
- **AI drill (2h):** Describe a fuzzy business rule to AI and ask for three different
  domain models. Pick one, and write down why the other two are worse.
- **Deliverable:** bitemporal entity + CQRS read model + updated context map.
- **Self-check:** ☐ I can explain valid time vs transaction time with a banking example.

---

## Exit checklist — all must pass before 4B

- ☐ Subdomain map with core/supporting/generic classification and rationale
- ☐ Context map with explicit relationship patterns; one ACL implemented
- ☐ Event storm run; event catalogue produced; commands vs events correctly separated
- ☐ Aggregates follow all four design rules; invariants tested; contention measured
- ☐ Domain vs application service separation enforced by ArchUnit
- ☐ Modular monolith with enforced module boundaries; ADR-0007 written
- ☐ One bitemporal model and one CQRS read model working
- ☐ v4 tagged
