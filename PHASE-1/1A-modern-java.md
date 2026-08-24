# 1A — Modern Java Mastery

**Duration:** 7 weeks (126h) · **Months 0.0 – 1.6** · **Prereq:** none
**Flagship increment:** v0 — service skeleton

> **Outcome:** I can write Java that a senior reviewer cannot fault, and I can
> explain *why* every construct I used is the right one — including the runtime
> consequences of `equals`/`hashCode`, immutability, and the type system.

**Calibration for mid-level (3–5 yrs):** you already write Java daily. Weeks 1–2 are
not "learn the syntax", they are "find the holes you don't know you have". Do the
Reason blocks honestly — most mid-level engineers fail on generics variance, `hashCode`
contracts, and stream laziness.

---

## Week 1 — Language fundamentals audit & the type system

- **Learn (5h):** Java 21/25 language spec areas you skip in daily work — pattern
  matching for `switch`, `instanceof` patterns, text blocks, `var` semantics, enhanced
  `for`, labelled break. Primitive vs reference semantics, autoboxing traps, integer
  caching, `==` vs `equals`. The type system: subtyping, erasure, raw types, wildcards.
- **Build (8h):** Create the flagship repo. Spring Boot 3.x skeleton, Java 21, Gradle
  or Maven, Spotless, layered package structure (`api / application / domain /
  infrastructure`). One endpoint: `POST /customers`. Testcontainers wired but unused yet.
- **Reason (3h):** Write answers: Why does `Integer.valueOf(127) == Integer.valueOf(127)`
  differ from `128`? What breaks if you use `var` on a lambda-heavy chain? When does
  erasure actually bite you in production code?
- **AI drill (2h):** Give AI your package structure and ask: *"Give me the strongest
  argument that this structure is wrong."* Grade its answer — is it real, or generic advice?
- **Deliverable:** repo initialised, README with architecture sketch, first commit.
- **Self-check:** ☐ I can explain erasure to a junior with a concrete failing example.

## Week 2 — Collections, deeply

- **Learn (5h):** `ArrayList` vs `LinkedList` real memory/cache behaviour, `HashMap`
  internals (buckets, treeification at 8, resize), `LinkedHashMap` access-order,
  `TreeMap` comparators, `EnumMap`, `ArrayDeque` vs `Stack`, `Set` implementations,
  `Collections.unmodifiable*` vs `List.of` (view vs copy), iteration order guarantees,
  fail-fast iterators and `ConcurrentModificationException`.
- **Build (8h):** Implement Customer/Account in-memory repositories using three
  different collection strategies. Write a micro-measurement harness (plain, JMH comes
  in 1B) comparing lookup and insert at 10³/10⁵/10⁷ entries.
- **Reason (3h):** For each collection you used at work last month — was it the right
  choice? Write the table: operation → complexity → actual measured cost.
- **AI drill (2h):** Ask AI to pick collections for 5 scenarios you invent. Find the
  scenario where it gives a defensible-but-wrong answer, and write down why.
- **Deliverable:** `collections-benchmark.md` with your numbers, not the internet's.
- **Self-check:** ☐ I can draw `HashMap` resize on a whiteboard.

## Week 3 — Equality, hashing & object lifecycle

- **Learn (5h):** `equals`/`hashCode` contract, symmetry/transitivity violations with
  inheritance, `Comparable`/`Comparator` consistency with equals, `Objects` helpers,
  identity vs value equality, object creation & finalisation, escape analysis basics,
  `Cleaner` vs `finalize`, `AutoCloseable`/try-with-resources ordering.
- **Build (8h):** Add JPA entities for Customer and Account. Deliberately introduce a
  broken `equals` on an entity with a generated ID, demonstrate the bug with a failing
  test (entity lost in a `HashSet` after persist), then fix it properly.
- **Reason (3h):** Why is `equals` on a JPA entity with a database-generated ID a
  known trap? What are the three valid strategies and when does each fail?
- **AI drill (2h):** Ask AI to generate `equals`/`hashCode` for a JPA entity. Review it
  as a code reviewer would; document what you rejected and why.
- **Deliverable:** failing-then-passing test that proves the entity-equality trap.
- **Self-check:** ☐ I know which strategy I would defend in a code review and why.

## Week 4 — Generics, exceptions & API design

- **Learn (5h):** Bounded types, PECS, wildcard capture, generic methods, type
  inference limits, `@SafeVarargs`, heap pollution. Exceptions: checked vs unchecked
  as a design decision, exception translation across layers, suppressed exceptions,
  stack-trace cost, when *not* to catch.
- **Build (8h):** Define the flagship exception hierarchy: one `ApplicationException`
  base, domain-specific subtypes, and a single `@RestControllerAdvice` producing
  RFC 9457 `ProblemDetail` responses. Add a generic `Result<T>`-style or `Either`-style
  type and decide (in writing) whether to keep it.
- **Reason (3h):** Where should an exception be translated — repository, service, or
  controller? Argue both sides, then pick and justify. What is the actual cost of
  throwing exceptions in a hot path?
- **AI drill (2h):** Ask AI to critique your exception hierarchy from the perspective of
  (a) an API consumer, (b) an on-call engineer. Two different lenses, two different answers.
- **Deliverable:** exception hierarchy + global handler + integration test of error shape.
- **Self-check:** ☐ I can explain PECS with an example from my own code.

## Week 5 — Streams & functional programming

- **Learn (5h):** Stream laziness & short-circuiting, spliterators, stateful vs
  stateless operations, `flatMap`, `Collectors` (grouping, partitioning, teeing,
  downstream), custom collectors, `reduce` associativity, parallel streams and when they
  lose, `Optional` as a return type (and not as a field/parameter), method references,
  `Function` composition, higher-order design.
- **Build (8h):** Build the transaction-query and reporting layer of the flagship using
  streams; write the same logic imperatively; benchmark and compare readability + speed.
- **Reason (3h):** When is a parallel stream slower? Give the three conditions. When is
  `Optional` the wrong tool? Why is `reduce` with a mutable accumulator a bug?
- **AI drill (2h):** Ask AI to rewrite your imperative code as streams, then ask a
  *second, fresh* AI session to find bugs in that rewrite. Note what the first one missed.
- **Deliverable:** side-by-side implementations + a short note on which you'd ship.
- **Self-check:** ☐ I can state exactly when a stream is evaluated.

## Week 6 — Immutability, records, sealed classes & modern Java

- **Learn (5h):** Records (canonical/compact constructors, validation, serialization,
  when records are wrong), sealed interfaces + exhaustive pattern matching for modelling
  state machines, value-based classes, defensive copying, immutable collections, builder
  vs record, `Optional` in records. Modern features: virtual threads (preview of 1B),
  structured concurrency, sequenced collections, string templates.
- **Build (8h):** Refactor the flagship's DTOs to records; model `TransactionState` as a
  sealed interface with exhaustive `switch`; make all value objects (Money, AccountId,
  Currency) immutable with validation in the compact constructor.
- **Reason (3h):** What does immutability buy you that is *not* thread safety? Where in
  the flagship is mutability actually required, and why?
- **AI drill (2h):** Give AI your sealed state machine and ask it to find an unreachable
  or missing state transition. Verify by writing the test yourself.
- **Deliverable:** value-object package with full test coverage; state machine diagram.
- **Self-check:** ☐ I can name a case where a record is the wrong choice.

## Week 7 — Reflection, annotations, serialization, class loading + consolidation

- **Learn (5h):** How Spring actually works: class loading, classloader hierarchy,
  reflection cost, `MethodHandle`/`invokedynamic`, annotation retention & processing,
  proxies (JDK dynamic vs CGLIB) and why `@Transactional` self-invocation fails.
  Serialization: Java serialization hazards, Jackson internals, polymorphic
  deserialization risks, `readObject` gadget chains (why Java serialization is banned).
- **Build (8h):** Write one custom annotation + a small aspect (e.g. `@AuditLogged`)
  and make it work. Then break `@Transactional` with self-invocation, prove it with a
  test, and fix it. Finish the v0 test pyramid: unit / `@WebMvcTest` / `@DataJpaTest`
  with Testcontainers (never H2) / one `@SpringBootTest`.
- **Reason (3h):** Draw the request path from HTTP socket to your repository method,
  naming every proxy and filter it passes through.
- **AI drill (2h):** *"Explain what Spring does between my controller returning and the
  HTTP response being written."* Then verify against the actual source. Log every point
  the AI was vague or wrong — this calibrates how much you can trust it.
- **Deliverable:** **v0 tagged in the flagship repo** + `docs/adr/0001-project-structure.md`.
- **Self-check:** ☐ I can explain why self-invocation breaks `@Transactional`.

---

## Exit checklist — all must pass before 1B

- ☐ Flagship v0 runs via `docker compose up`, with a green test pyramid using Testcontainers
- ☐ I can explain `HashMap` internals, erasure, and the `equals`/`hashCode` contract without notes
- ☐ Every DTO is a record; every value object is immutable and self-validating
- ☐ One state machine is modelled with sealed types and exhaustive matching
- ☐ Error responses follow RFC 9457 and are integration-tested
- ☐ ADR-0001 written; I can defend the package structure against a challenge
- ☐ I have a written list of at least 5 things AI told me that were wrong or shallow

**If 2+ fail:** insert a 1-week repair sprint. Do not proceed on a shaky foundation —
everything in Phase 2 and 3 compounds on this.
