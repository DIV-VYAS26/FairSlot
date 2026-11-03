# FairSlot working instructions

## Purpose and status

Build a reliable reservation and capacity-management backend, with evidence of
concurrency correctness and a future path to college event/workshop use.
Engineering evidence and validated product needs matter more than technology count.
Phase 0 established documentation; Milestone 1 adds only an application health foundation.
Read README.md, docs/architecture.md and
docs/progress.md before changing the project; do not assume planned features exist.

## Approved stack and boundaries

- Java 21, Spring Boot, Maven, Spring Web and Bean Validation.
- PostgreSQL and Flyway; Spring Data JPA where useful, JDBC/native SQL for critical operations.
- Later: Kafka, Spring Kafka, transactional outbox and idempotent consumers.
- JUnit Jupiter 6 (approved with Spring Boot 4.1.2), Spring Boot Test and later
  Testcontainers; later, measured load tests. The original specification named JUnit 5.
- Local Docker/Compose, Actuator, Micrometer and structured logs when introduced.
- Verify maintained versions and compatibility before adding dependencies.
- Start with a modular monolith. PostgreSQL owns capacity and reservation state.
  Kafka handles downstream events, not inventory correctness.
- Keep HTTP handling in controllers, use-case transactions in services, and SQL
  in persistence code. Prefer feature-oriented packages and explicit dependencies.
- No frontend, microservices, Kubernetes or speculative caching in the foundation.

## Coding and correctness

- Prefer readable Java, consistent formatting, clear names and small focused units.
  Avoid excessive abstraction, unrelated refactoring and giant controllers/services.
- Validate API inputs and enforce persistent invariants with database constraints.
- Never allocate capacity using an unprotected read-modify-write sequence.
- Capacity allocation and reservation insertion must commit or roll back together.
- Use database uniqueness and defined request matching for concurrent idempotency.
  A pre-insert existence check alone is insufficient.
- Restore capacity only for a successful CONFIRMED-to-CANCELLED transition, in
  the same transaction. Define lock ordering and bounded retry behavior deliberately.
- When added, outbox records commit with reservation changes. Assume at-least-once
  delivery; deduplication and protected consumer database effects commit together.
- Do not hold database locks during Kafka network calls or promise unconditional
  end-to-end exactly-once external effects.

## Verification and safety

- Run checks appropriate to each change; report actual results and reproduction commands.
- Reservation correctness requires real PostgreSQL integration tests, including
  contention, duplicate requests, rollback and concurrent cancellation.
- Add recovery and duplicate-delivery tests with messaging. Never invent benchmarks.
- Do not hardcode, print or commit secrets. Use documented placeholders and ignored
  local configuration. Review tracked files; .gitignore does not protect tracked secrets.
- Authentication and authorization are mandatory before real-user exposure.
- No commits, pushes, remotes, publishing, deployment, data deletion or destructive
  operations without explicit user authorization. The initial Phase 0 commit was specifically approved and created. Further commits
  and all remotes require separate explicit authorization.

## Collaboration and project memory

- Before each major milestone, explain scope, approach, trade-offs, files,
  dependencies/infrastructure and completion criteria; obtain approval to begin.
- Implement small approved units. Explain important code, commands, configuration,
  transaction boundaries, failures and verification through actual repository files.
- After significant features, ask three conceptual questions, one debugging scenario
  and one hands-on exercise; review the user's attempts. Tiny fixes need no quiz.
- Stop at milestone boundaries. Do not begin the next milestone automatically.
- Maintain documentation alongside approved changes. Record unresolved requirements
  instead of silently deciding them. Distinguish planned, implemented and verified work.
- The original master specification was supplied in chat; these instructions summarize
  it rather than replace it. Later explicit user decisions refine the baseline.
  Surface conflicts before implementation and record accepted decisions in the docs.
