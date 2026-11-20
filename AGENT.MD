# AI Agent Instructions — Welltering Monorepo

This repository contains **two independent projects**, identified by their directory:

- `elena/` — **Elena**, Angular frontend.
- `irena/` — **Irena**, Java Spring Boot backend.

**Current assignment scope:** Irena backend engineering only. Do not generate or modify Elena frontend code while working on Java/API tasks. This document contains technical agent rules, not product feature requirements.

## Repository and change boundaries

1. Inspect the actual repository and branch before editing. The applications might not be scaffolded yet.
2. Place Java source, Maven files, Spring configuration, database migrations, and backend tests inside `irena/` only.
3. Never create `pom.xml`, `src/main/java`, `application.yml`, or Java migrations at the monorepo root.
4. Never edit `elena/` unless the user explicitly requests frontend work. Its framework is **Angular**, not Next.js.
5. Cross-project changes require explicit justification. Avoid speculative frontend/backend integration changes.
6. Keep a narrow, reviewable change set. Do not add unrequested domain modules or premature infrastructure.

## Fixed Irena technology choices

- Java 21 LTS and Spring Boot (select a mutually compatible stable release).
- **Maven** with Maven Wrapper (`irena/mvnw`) and `irena/pom.xml`.
- **Hibernate ORM** through Spring Data JPA.
- **PostgreSQL**, with versioned **Flyway SQL migrations**.
- Spring Security and stateless JWT authentication.
- Jakarta Bean Validation and OpenAPI/Swagger documentation.
- JUnit 5, Mockito, Spring Boot Test, and Testcontainers using PostgreSQL.
- Modular monolith, organized by feature/package.

Do **not** substitute Gradle, Kotlin application code, Node.js/NestJS backend, Redis, Kafka, Kubernetes, or microservices without an explicit revised decision.

## Before implementation

1. Read `README.md`, this file, and `irena/docs/openapi.yaml` if present.
2. Confirm the requested change concerns `irena/`. Inspect existing code rather than assuming a particular package or version.
3. Check build/dependency compatibility; prefer Spring Boot dependency management.
4. Define/update the API contract first for HTTP changes.
5. Implement the smallest complete change and run applicable checks.

## Java and Spring conventions

- Prefer constructor injection over field injection.
- Use descriptive Java classes, methods, records for suitable immutable DTOs, and small cohesive services.
- Keep controllers responsible for HTTP mapping and input validation; application services own use cases and transaction boundaries; repositories own persistence.
- Never expose JPA entities directly from REST controllers.
- Validate input DTOs using Jakarta Bean Validation.
- Centralize API error handling using `@RestControllerAdvice` and Spring `ProblemDetail` (RFC 9457).
- Use `BigDecimal` for monetary amounts and explicit time-zone conventions.
- Keep authentication material and environment-specific credentials outside source control.
- Prefer direct, readable code over generic base classes and unnecessary abstractions.

## Hibernate, PostgreSQL, and Flyway

- Use Hibernate/JPA for normal persistence; use constrained, parameterized native SQL only where necessary.
- Put **all schema changes** in new versioned Flyway migrations under `irena/src/main/resources/db/migration/`.
- Never silently edit previously applied migrations in a shared environment.
- Configure `spring.jpa.hibernate.ddl-auto=validate`; never rely on `update` for deployed schemas.
- Design primary keys, foreign keys, not-null/unique constraints, and indexes intentionally.
- Review fetch strategies and avoid accidental N+1 queries.
- Use `@Transactional` for service-level atomic operations and locking or database constraints for concurrent mutations.
- Ensure retryable state-changing operations are idempotent where required.
- For tenant-owned records, enforce membership/permissions and tenant-scoped queries server-side. Never assume a client-provided tenant ID grants access.

## API-first specification

- Store Irena's API contract at `irena/docs/openapi.yaml` when scaffolded.
- Use `/api/v1` for versioned application endpoints.
- Specify request/response schemas, authorization, validation, error/status codes, and examples.
- Follow standard HTTP semantics, including 400, 401, 403, 404, 409, and 422 as appropriate.
- Keep implementation, OpenAPI, and tests consistent.
- Do not expose stack traces, tokens, secrets, or JPA internals in public responses.

## Testing and verification

- Unit test nontrivial logic and validation cases.
- Use real PostgreSQL via Testcontainers for migrations, entity mappings, transactions, locks, and tenant isolation.
- Test unauthorized access and cross-tenant data access where applicable.
- Include concurrency and duplicate-request coverage for stateful operations.
- Do not use H2-only tests as a replacement for PostgreSQL integration tests.
- Run checks from **`irena/`**, e.g. `cd irena && ./mvnw verify`, once the project exists.
- Never claim a command or test passed unless it actually ran; provide accurate blockers and outcomes.

## If Irena is not yet scaffolded

1. Generate a minimal Spring Boot Maven application in `irena/` only.
2. Add Maven Wrapper, compatible dependencies, and Java 21 toolchain/release configuration.
3. Configure PostgreSQL, Flyway, and `irena/compose.yaml` for local development.
4. Add a minimal secured application foundation, centralized error handling, and OpenAPI support.
5. Add basic automated tests; run `cd irena && ./mvnw verify`.
6. Document any setup steps in the appropriate README.

Do not scaffold `elena/` during this workflow.

## Completion report

Summarize files changed, key technical decisions, exact commands/tests run and their outcomes, and any outstanding security or migration issues. Use a dedicated branch and PR. Do not commit `.env`, secrets, build output, or local-generated files.
