# FairSlot

FairSlot is a planned reservation and capacity-management platform for limited
resources, initially aimed at college events, workshops and sessions.

If 200 people compete for 20 seats, the backend must prevent overbooking, make
client retries safe, roll back failed bookings and restore cancelled capacity once.
The project aims to demonstrate rigorous Java backend engineering and, after
validation with organizers, support a useful product.

## Current status

**Phase 0: repository foundation.** Working artifacts are repository instructions,
planning documentation and ignore rules. A local Git repository is initialized;
there are no commits or remotes. There is no application, build configuration,
API, database, container configuration, messaging or benchmark result yet.
There are no application build/run commands or automated tests to execute yet.

## Planned architecture and stack

A modular Spring Boot monolith will use PostgreSQL as the source of truth.
Atomic conditional capacity updates and database transactions will protect bookings.
Later, a transactional outbox will feed Kafka and consumers will deduplicate events.

Planned stack: Java 21, Maven, Spring Boot/Web, Bean Validation, PostgreSQL,
Flyway, JPA where appropriate and JDBC/native SQL for critical operations.
Testing will use JUnit 5, Spring Boot Test and Testcontainers. Later phases add
Kafka/Spring Kafka, Docker Compose, Actuator, Micrometer and structured logs.
Exact compatible dependency versions have not been selected or verified.

## Roadmap

| Phase | Planned result |
| --- | --- |
| 0 | Repository conventions, architecture and approved milestones |
| 1 | Application foundation, then PostgreSQL-backed reservation correctness |
| 2 | Transactional outbox, Kafka, deduplication and recovery |
| 3 | Expanded concurrency/failure tests, measured performance and observability |
| 4 | Authenticated organizer and participant product MVP |
| 5 | Small real-world pilot and feedback |
| 6 | Scaling justified by measured bottlenecks or validated requirements |

The basic overbooking concurrency test belongs in Phase 1; Phase 3 expands the
engineering evidence. Frontend and authentication are deferred from the local
prototype, but authentication and authorization are required before real-user use.

## Proposed Milestone 1 — awaiting approval

Create only the Java 21 + Spring Boot + Maven application foundation: a minimal
application, clean feature-oriented package structure, a simple health-check
endpoint, a basic automated test, and reproducible build/run instructions.

No reservation logic, database integration or Kafka is included in this milestone.
See [progress](docs/progress.md) for acceptance criteria and open decisions.

## Project references

- [Working instructions](AGENTS.md)
- [Planned architecture](docs/architecture.md)
- [Progress and next milestone](docs/progress.md)
- [Architecture decision records](docs/decisions/README.md)

The requirements baseline is the master FairSlot specification supplied in the
project conversation, refined by subsequent explicit user instructions. The [original specification](docs/FairSlot_Project_Specification.md) is preserved
byte-for-byte from its attachment. It is historical context: later explicit user
instructions authorize subsequent milestones and refine the baseline. The other
documents provide the current working summary.
Update design and status with each approved change and record significant accepted
decisions. Resolve conflicts with the user before implementing affected behavior.
