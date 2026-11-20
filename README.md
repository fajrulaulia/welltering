# Welltering

Technical monorepo for **welltering.com**, with two independently maintained applications:

| Directory | Project | Technology | Responsibility |
| --- | --- | --- | --- |
| `elena/` | **Elena** | Angular | Frontend application |
| `irena/` | **Irena** | Java + Spring Boot | Backend REST API |

**Current state:** This PR documents the intended repository layout. The repository has not yet been scaffolded with either application. Commands below apply **after** the corresponding application files exist.

## Repository layout

```text
welltering/
├── elena/                    # Angular frontend (separate project)
├── irena/                    # Spring Boot backend (separate project)
│   ├── pom.xml
│   ├── mvnw
│   ├── mvnw.cmd
│   ├── .mvn/
│   ├── compose.yaml
│   ├── .env.example
│   ├── docs/
│   │   └── openapi.yaml
│   └── src/
│       ├── main/
│       │   ├── java/com/welltering/
│       │   └── resources/
│       │       ├── application.yml
│       │       └── db/migration/
│       └── test/java/com/welltering/
├── README.md
└── AGENT.MD
```

The tree is a **target structure**, not a list of files that currently exist. Keep project-specific build tools and configuration inside their respective application directories; do not add a root-level Java build.

## Irena — backend technical stack

| Area | Decision |
| --- | --- |
| Language | Java 25 LTS |
| Framework | Spring Boot 4.x (compatible stable release) |
| Build | Maven + Maven Wrapper |
| ORM | Hibernate via Spring Data JPA |
| Database | PostgreSQL |
| Migrations | Flyway SQL migrations |
| Authentication | Spring Security + stateless JWT |
| Validation | Jakarta Bean Validation |
| API specification | OpenAPI 3.x |
| API documentation | Springdoc / Swagger UI |
| Tests | JUnit 5, Mockito, Spring Boot Test, Testcontainers (PostgreSQL) |
| Local infrastructure | Docker Compose for PostgreSQL |
| Architecture | Modular monolith, packages by feature |
| Development approach | API-first and AI-assisted |

Irena does **not** use Redis, Gradle, Kotlin application code, NestJS/Node.js backend, Kafka, Kubernetes, or microservices.

### Java version and runtime consistency

- **Target:** Java 25 LTS. Configure Maven with `<java.version>25</java.version>` and `<maven.compiler.release>25</maven.compiler.release>` in `irena/pom.xml`.
- **Developer machines:** OpenJDK 27 may be used locally if the chosen Spring Boot and tooling support it. Using `--release 25` prevents accidental use of Java 27 language/API features.
- **CI and production:** Use JDK 25 to test the actual production runtime. Also run tests on JDK 25 before merging; compiling under JDK 27 alone is not sufficient evidence of Java 25 runtime compatibility.

### Backend engineering rules

- Keep all Irena application code and backend-specific configuration under `irena/`.
- Define/update the OpenAPI contract **before** implementing or changing public REST endpoints; use `/api/v1` as the API prefix.
- Use constructor injection, request/response DTOs, Jakarta validation, clear service-layer transactions, and centralized `ProblemDetail` error handling.
- Do not expose Hibernate/JPA entities directly in API responses.
- Apply every schema change through versioned Flyway migrations; use Hibernate `ddl-auto=validate`.
- Enforce backend permissions and tenant data isolation in the service/query layers.
- Use `@Transactional`, database constraints, locking where necessary, and idempotency for retryable writes.
- Never commit secrets or log bearer tokens, credentials, or sensitive request payloads.
- Keep tests close to changed code; use PostgreSQL Testcontainers for transaction, locking, and persistence integration tests.

### Local backend workflow (after Irena is scaffolded)

```bash
cd irena
cp .env.example .env
docker compose up -d
./mvnw spring-boot:run
./mvnw verify
```

On Windows, use `mvnw.cmd` instead of `./mvnw`. Environment values should be configured locally; never commit `.env`. Default Swagger and health URLs must be verified against the actual Spring Boot configuration.

## Elena — frontend technology and boundary

`elena/` is a **separate Angular frontend project**. The selected baseline as of 21 November 2026 is:

| Area | Decision |
| --- | --- |
| Framework | Angular **22.3.x** (latest stable patch within the supported 22.x line) |
| Angular CLI | Match Angular major/minor versions |
| Language | TypeScript **6.0.x** (Angular 22 compatibility range: >=6.0.0 and <6.1.0) |
| Node.js for tooling | **24 LTS**, version >=24.15.0 (within Angular 22's supported range) |
| RxJS | **7.4.x** |
| Architecture | Standalone Components |
| API integration | Angular HttpClient communicating with Irena REST endpoints |

Version compatibility reference: https://angular.dev/reference/versions
Release/support reference: https://angular.dev/reference/releases

Use a released stable patch (never `next` or `rc`) and verify latest compatible versions when `elena/` is scaffolded. Frontend project files and build commands will be documented **after** Elena exists; do not invent commands or claim the app already exists.

`elena/` must not be generated, modified, or configured as part of Java backend tasks.

The agreed focus for the current development phase is **Irena API-first**. Backend changes should not introduce frontend code or require Elena to run in order to test the REST API.

## Definition of done for Irena changes

- API implementation matches its OpenAPI contract.
- New persistence schema changes include versioned Flyway SQL migrations.
- Authorization, validation, error handling, and transactional consistency are covered.
- Relevant unit/integration tests exist.
- Run `cd irena && ./mvnw verify` when Irena exists; disclose blockers or unexecuted tests explicitly.
- Update the relevant documentation if technical setup or behavior changes.

For AI coding guidelines and path boundaries, see [AGENT.MD](AGENT.MD).
