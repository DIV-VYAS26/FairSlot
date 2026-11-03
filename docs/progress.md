# FairSlot progress

## Current phase

Phase 0 — project foundation, completed on 2026-11-03. Milestone 1 is proposed and
requires explicit approval. No application functionality is implemented.

## Completed

- Inspected the workspace: initially .gitignore and .DS_Store only; no local Git
  repository, AGENTS.md or application code.
- Confirmed the original master specification attachment was accessible. Preserved
  its exact contents in docs/FairSlot_Project_Specification.md; byte equality checked
  with cmp. Later explicit instructions govern the current approved milestone.
- Created working instructions, overview, architecture, progress and ADR guidance.
- Preserved existing ignore rules and added local secret, build, log and temporary rules.
- Initialized local Git without commits or remotes.

## Verification

Phase 0 checks: required documents exist and are nonempty; Git identifies this directory
as the repository root; no commits or remotes exist; documentation is untracked;
local metadata and representative secret/build paths are ignored. No Java source,
Maven/build configuration, environment files or infrastructure configuration was added.
Reviewed the new content for credentials; none were added. Runtime components are
explicitly labelled planned, with unresolved behavior recorded.

No application tests, builds, dependency checks or performance tests have run: no
application/build exists. Phase 0 verification is repository/documentation inspection.

## Decisions and limits

- Modular monolith; PostgreSQL owns capacity; Kafka is deferred to reliable events.
- No application code, dependency installation, databases or containers in Phase 0.
- No commits, remotes, pushes or publication authorized.
- Milestone 1 follows the latest approved planning scope below; database integration
  is a subsequent milestone, superseding the earlier suggested broader first milestone.
- No blockers to foundation planning. Java/Maven/Docker availability and exact
  dependency compatibility have not been checked and must not be assumed.

## Pending decisions

For Milestone 1: base package/project coordinates, maintained Spring Boot/dependency
versions compatible with Java 21 and the planned JUnit 5 baseline, health-check path
and minimal response, and whether Actuator is justified now. Agree before implementation.

Before reservation work: settle the open domain, API and persistence decisions in
[architecture](architecture.md). Before Phase 2: settle outbox ownership, ordering,
retry/replay and consumer behavior. Do not silently resolve these in code.

## Proposed Milestone 1 — awaiting approval

**Problem:** establish a runnable, testable Java backend before domain complexity.

**Scope:** Java 21, Spring Boot and Maven; one minimal application; clean initial
package structure without empty speculative layers; one simple health-check endpoint;
a basic automated test; build/run instructions. No reservation APIs, PostgreSQL,
Flyway migrations, Kafka, frontend or authentication implementation.

**Expected changes:** pom.xml, Maven Wrapper files, application entry point, minimal
health-check implementation/configuration, a test under src/test/java, and updated
README/progress. Select exact names and dependencies after compatibility review.

**Acceptance criteria:**

- A Maven Wrapper build succeeds on Java 21 using documented commands.
- The application starts locally and the documented health-check request returns
  the agreed successful response without database/Kafka services.
- A basic automated test starts the application and verifies the health response.
- Instructions explain dependencies, configuration and commands; actual verification
  results and environment limitations are recorded.
- Stop for review and learning questions before beginning the next milestone.

## Keeping project memory accurate

Update this file at milestone boundaries with completed scope, actual checks,
limitations and the next approval decision. Update architecture when the accepted
design changes, README when setup/status changes, and AGENTS.md when working rules
change. Use ADRs only for significant accepted choices. Discuss requirement conflicts
with the user and record their resolution before affected implementation proceeds.
