# AI Agent Instructions — Welltering Java Backend

These instructions apply to this repository. Work on **Java backend engineering only**. This document is a technical guide, **not** a product roadmap or feature specification.

## Fixed technology choices

- **Language:** Java 21 LTS.
- **Framework:** Spring Boot; select compatible stable library versions during initial setup.
- **Build:** Maven with Maven Wrapper and `pom.xml`.
- **ORM:** Hibernate, used through Spring Data JPA.
- **Database:** PostgreSQL; use Flyway SQL migrations.
- **Security:** Spring Security with stateless JWT authentication.
- **Validation and API docs:** Jakarta Bean Validation and OpenAPI/Swagger.
- **Tests:** JUnit 5, Mockito, Spring Boot Test, Testcontainers with PostgreSQL.
- **Architecture:** Modular monolith organized by feature.

**Do not add:** a frontend, NestJS/Node.js services, Kotlin application code, Gradle, Redis, Kafka, Kubernetes, or microservices unless the user explicitly changes the technical decision.

## Before editing

1. Inspect the existing repository, current branch, and relevant files. Do not assume the Spring Boot project already exists.
2. Read `README.md`, this file, and any existing `docs/openapi.yaml`.
3. Keep the change within the requested technical scope. Avoid speculative domain modules or unrelated refactors.
4. Confirm dependency compatibility before changing `pom.xml`. Prefer Spring Boot dependency management over independently pinning managed dependencies.
5. Propose the smallest coherent implementation, then make it compile and test.

## Java and Spring Boot conventions

- Use clear Java types, descriptive names, and small classes with single responsibilities.
- Prefer constructor injection; avoid field injection.
- Structure features as controller → application/service → repository/persistence, without adding layers that do no real work.
- Keep HTTP-specific types out of the domain/service layer where practical.
- Define request/response DTOs; never serialize persistence entities directly.
- Use Java records for immutable DTOs where appropriate.
- Put Jakarta validation constraints on input DTOs and trigger validation at API boundaries.
- Use `@RestControllerAdvice` and Spring `ProblemDetail` for predictable error responses.
- Use configuration properties and environment variables; never hardcode secrets.
- Avoid unnecessary Lombok annotations, reflection, magic constants, or broad generic base classes.

## Persistence and migration rules

- Use Hibernate/JPA for standard persistence operations and Spring Data JPA repositories.
- Put **every schema change** in a new versioned Flyway migration. Never edit a migration already applied in a shared environment.
- Set `spring.jpa.hibernate.ddl-auto=validate`; do not depend on Hibernate to create/update production schemas.
- Define foreign keys, unique constraints, not-null constraints, and indexes based on actual access patterns.
- Use `BigDecimal` for currency, and appropriate Java time types; make time zone assumptions explicit.
- Plan fetch strategies deliberately; check for N+1 queries and avoid unnecessary eager relationships.
- Apply `@Transactional` at service/use-case boundaries. Use database constraints and locking where concurrent updates may conflict.
- Retryable writes must be idempotent where needed; never assume an HTTP request is delivered only once.
- For tenant-owned data, verify membership/authorization and constrain database queries by tenant. Do not trust a client-provided tenant identifier alone.

## API-first contract

- Treat the OpenAPI specification as the contract for public HTTP endpoints.
- Document request/response DTOs, status codes, validation errors, security requirements, and examples.
- Keep route names RESTful and place versioned endpoints under `/api/v1`.
- Use standard HTTP semantics (including 400, 401, 403, 404, 409, and 422 when appropriate).
- Apply authorization in backend enforcement points, not only in controller annotations or documentation.
- Do not expose stack traces, credentials, tokens, or internal entity implementation details in API responses.

## Testing expectations

- Write unit tests for nontrivial service logic and validation behavior.
- Use integration tests with **real PostgreSQL via Testcontainers** for JPA mappings, migrations, transactions, isolation, and locking.
- Test unauthorized and cross-tenant access for protected resources.
- Include concurrency and duplicate-request tests for stateful operations where applicable.
- Do not replace important database integration tests with H2-only tests.
- Never claim a build/test passed unless it was actually executed; report exact commands and blockers.

## Initial scaffolding sequence

If the repository is still documentation-only:

1. Generate a minimal Spring Boot Maven project using Java 21 and the chosen compatible stable Spring Boot release.
2. Commit Maven Wrapper files and `pom.xml`.
3. Configure PostgreSQL, Flyway, and a Docker Compose development database.
4. Add a minimal health check, centralized error handling, and Spring Security defaults.
5. Add OpenAPI support and basic automated tests.
6. Execute `./mvnw verify` and document the observed result.

Do **not** pre-generate an entire business domain before a feature contract has been agreed.

## Delivery checklist

When finishing a change, report:

- Files changed and why.
- Relevant architectural or dependency decisions.
- Commands actually run and their outcomes.
- Security, migration, or compatibility considerations.
- Any missing implementation or tests (explicitly, without overstating readiness).

Use a focused branch and pull request for changes. Do not commit secrets, environment files with credentials, build outputs, or generated local artifacts.
