# FairSlot architecture — planned, not implemented

All runtime components and flows below are planned. Phase 0 creates documentation
and Git metadata only; no schema, endpoints or infrastructure exists.

## Modular monolith

One Spring Boot application will expose REST endpoints and coordinate use cases.
Planned feature boundaries are slots (reservable inventory), reservations (booking,
idempotency and cancellation), and later messaging (outbox publishing and event
consumption). Minimal shared error/configuration support may be added when needed.
These are code boundaries within one application, not separately deployed services.

Controllers handle HTTP/validation, services own use cases and transactions, and
persistence components perform database operations. PostgreSQL is authoritative for
inventory and reservations. Kafka will distribute committed changes asynchronously.
No Kafka dependency is required for the first application milestone.

## Conceptual booking flow

1. Accept slot ID, customer identity and an idempotency key; validate the request.
2. Start a database transaction and arbitrate the key with database uniqueness.
3. For a duplicate, compare its stored request fingerprint; return the agreed replay
   result or a conflict for different request contents.
4. For a new request, conditionally decrement capacity only where available > 0.
5. Insert the confirmed reservation. A failed insert must roll back the decrement.
6. In Phase 2, insert a corresponding outbox event in that same transaction.
7. Commit, then return the result. A lost response is resolved by safe retry.

The exact idempotency storage and failed-request replay contract remain unresolved.
Do not catch a PostgreSQL uniqueness failure and assume the failed transaction can
continue; use an explicitly designed conflict-handling/transaction strategy.

## Database correctness responsibilities

Planned tables are slots and reservations, followed by outbox_events and
processed_events in Phase 2. Migrations will define types, keys and constraints.

- Enforce capacity >= 0 and 0 <= available <= capacity with row constraints.
- Maintain available + confirmed allocations = capacity through transaction logic;
  row checks alone do not prove this cross-table invariant.
- Use foreign keys, status constraints and an explicitly scoped unique idempotency key.
- Atomically transition CONFIRMED to CANCELLED; restore capacity only if that
  transition succeeds. Both changes must share a transaction.
- Establish consistent lock ordering, bounded transactions and deliberate retry rules.
- Keep critical SQL and JPA entity state consistent if both persistence styles are used.

Real PostgreSQL integration tests must verify contention (including 200 attempts
for 20 seats), duplicate retries, conflicting payloads, rollback and cancellation.

## Transactional outbox and integration points — Phase 2

The reservation transaction will insert an event with a stable event ID. A background
publisher reads committed outbox rows, sends to Kafka and records successful
publication after broker acknowledgement. A crash between sending and recording can
produce duplicates: delivery is at least once.

Consumers will insert a (consumer_name, event_id) deduplication marker and apply
protected database effects in one transaction. Kafka offsets advance after successful
processing; redelivery must be safe. External effects such as email require their own
idempotency/recovery strategy.

Publishing must not keep database locks open across Kafka calls. Before multiple
publishers, define claim/lease ownership, expiry and recovery. Event schema/versioning,
confirmation/cancellation ordering, bounded retries, dead-letter handling, replay and
retention must be designed and tested before messaging implementation.

## Open decisions

Before reservation implementation: single-seat policy; multiple bookings per customer;
key scope, retention, fingerprint and failed-request replay; booking/cancellation time
rules; capacity edits; local customer identity; API errors/replay responses; critical
persistence strategy. Timestamp handling and list pagination also need explicit contracts.

Before product exposure: authentication, organizer/participant authorization, access to
reservation data, registration policies and personal-data retention. Before performance
work: reproducible workload and measurement definitions. Choose tools and dependency
versions when their milestone is approved, verifying compatibility at that time.
