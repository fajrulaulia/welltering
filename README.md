# Welltering Backend

Java-only backend and REST API repository for **welltering.com**.

> **Repository status:** Initial technical documentation only. The Spring Boot application, Maven Wrapper, Docker Compose configuration, migrations, and automated tests have **not** been scaffolded yet. The commands below are the intended developer workflow **after** the project is initialized.

## Technology decisions

| Area | Choice |
| --- | --- |
| Language | Java 21 LTS |
| Framework | Spring Boot (stable version compatible with Java 21) |
| Build | Maven + Maven Wrapper (`mvnw`) |
| Persistence | Hibernate ORM via Spring Data JPA |
| Database | PostgreSQL |
| Schema migrations | Flyway (SQL migrations) |
| Security | Spring Security; stateless JWT authentication |
| Request validation | Jakarta Bean Validation |
| API contract | OpenAPI 3.x; Springdoc/Swagger UI |
| Testing | JUnit 5, Mockito, Spring Boot Test, Testcontainers for PostgreSQL |
| Local infrastructure | Docker Compose for PostgreSQL |
| Architecture | Modular monolith, package by feature |

**Explicitly out of scope:** frontend, NestJS/Node.js backend, Kotlin application code, Gradle, Redis, microservices, Kafka, and Kubernetes. Additional infrastructure or dependencies require a documented reason and review.

## Engineering conventions

- **API-first:** Define or update the OpenAPI contract before changing REST endpoints. Use `/api/v1` for versioned application APIs.
- **Java-only application:** Source files live under `src/main/java/com/welltering`. Test files mirror the package structure under `src/test/java`.
- **Module boundaries:** Organize packages by feature; separate controllers, services, repositories, DTOs, and persistence entities where appropriate.
- **Clear responsibilities:** Controllers handle HTTP and validation; services own use cases and transactions; repositories handle persistence.
- **DTOs over entities:** Never expose JPA entities directly in public API responses.
- **Database-first integrity:** Apply schema changes through versioned Flyway migrations. Configure Hibernate with `spring.jpa.hibernate.ddl-auto=validate`, not `update` or `create` in shared environments.
- **Tenant-aware access:** Scope tenant-owned data by business identifier and authorize access server-side. A supplied business ID is not proof of access.
- **Safe transactions:** Use `@Transactional`, constraints, and appropriate locking for concurrent writes. Prefer explicit idempotency for retryable state-changing operations.
- **Consistent errors:** Use appropriate HTTP status codes and a centralized handler based on Spring `ProblemDetail` / RFC 9457.
- **Secrets:** Keep credentials and JWT signing keys out of source control. Use environment variables and provide only safe examples.
- **Observability:** Use structured, useful logs; never log credentials, raw bearer tokens, or sensitive payloads.

## Planned repository structure

```text
welltering/
├── pom.xml
├── mvnw
├── mvnw.cmd
├── .mvn/
├── compose.yaml
├── .env.example
├── docs/
│   └── openapi.yaml
├── src/
│   ├── main/
│   │   ├── java/com/welltering/
│   │   │   ├── WellteringApplication.java
│   │   │   ├── common/
│   │   │   └── modules/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── db/migration/
│   └── test/java/com/welltering/
├── README.md
└── AGENT.MD
```

This is a **target layout**, not a claim that these files exist today. Create packages only when required; avoid empty placeholder modules.

## Local development (after scaffolding)

**Prerequisites:** JDK 21, Docker with Compose, and a working shell. Use the checked-in Maven Wrapper rather than relying on a machine-specific Maven installation.

1. Copy `.env.example` to `.env` and populate local-only values.
2. Start PostgreSQL with `docker compose up -d`.
3. Run the application with `./mvnw spring-boot:run` (Windows: `mvnw.cmd spring-boot:run`).
4. Run unit/integration tests with `./mvnw verify`.
5. Review generated OpenAPI/Swagger UI once Springdoc has been configured.

Default endpoints such as `/v3/api-docs`, `/swagger-ui/index.html`, and `/actuator/health` must be verified against actual application configuration before documenting them as available.

## Definition of done

A backend change is complete only when:

- The API contract (if affected) and implementation agree.
- Schema changes include a forward-only Flyway migration.
- Authorization, validation, error handling, and transaction behavior are addressed.
- Relevant unit/integration tests exist and pass.
- `./mvnw verify` passes in the configured environment, or the blocker is explicitly documented.
- The README or supporting technical docs are updated if setup or behavior changed.

## AI coding agents

Read [AGENT.MD](AGENT.MD) before generating or modifying source code. This repository is **technical Java backend scope only**; do not introduce frontend work or product feature specifications into these repository guidelines.
