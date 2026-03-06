# Tech Stack - CinemaBook

## Runtime & Language

- **Java 21** (LTS) — use records, sealed classes, and pattern matching where appropriate
- **Spring Boot 3.3.x** — latest stable 3.x release

## Build

- **Maven** — standard `pom.xml`, no wrapper required

## Data Layer

- **H2** — in-memory relational database; no external process or Docker needed
- **Spring Data JPA / Hibernate** — ORM for all persistence; use JPA annotations on all entities
- **Spring Data repositories** — `JpaRepository` as the base for all repositories

H2 console is enabled for local inspection at `/h2-console`.

## User identification

- No authentication framework. Identity is passed via a plain HTTP header: `X-User-Id: {userId}`.
- Users (1 admin + 3 customers) are pre-seeded on application startup with known IDs and roles.
- Endpoints that require a user read `X-User-Id` from the request; if the header is absent, return HTTP 400.
- Admin endpoints verify that the user identified by `X-User-Id` has role `ADMIN`; if not, return HTTP 403.
- Do NOT add Spring Security or any authentication filter.

## API

- **Spring MVC** — REST controllers, `@RestController`
- **Springdoc OpenAPI 2.x** (`springdoc-openapi-starter-webmvc-ui`) — Swagger UI available at `/swagger-ui.html`
- All request/response bodies use dedicated DTO classes (no entity leakage into the API layer)

## Scheduling

- **Spring `@Scheduled`** — runs the seat lock expiry job every minute to release PENDING reservations older than 15 minutes

## Testing

- **JUnit 5** — all tests
- **Mockito** — unit test mocking
- **Spring Boot Test** (`@SpringBootTest`) — integration tests
- **MockMvc** — HTTP layer testing in integration tests
- Target: unit tests for business logic (booking rules, lock expiry, pricing), integration tests for the full booking flow end-to-end

## Code Style

- **Lombok** — `@Getter`, `@Builder`, `@RequiredArgsConstructor` on service and entity classes; avoid `@Data` on JPA entities
- Package structure by feature, not by layer:
  ```
  com.cinemabooking
  ├── movie
  ├── hall
  ├── screening
  ├── reservation
  ├── user
  └── shared
  ```

## Out of Scope (do not add)

- Spring Security or any authentication/authorization framework
- JWT or any token-based auth
- Liquibase / Flyway — schema is managed by Hibernate `ddl-auto=create-drop`
- Docker / Docker Compose — H2 removes the need for an external database
- Redis or any external cache — seat locking is handled in-database via JPA transactions
- Frontend / templating engines
