# Tech Stack - CinemaBook

## Runtime & Language

- **Java 21** (LTS) — use records, sealed classes, and pattern matching where appropriate
- **Spring Boot 3.3.x** — latest stable 3.x release

## Build

- **Maven** — standard `pom.xml`, no wrapper required

## Data Layer

- **H2** — file-based relational database (`jdbc:h2:file:./data/<appname>`); no external process or Docker needed; data persists between restarts
- **Spring Data JPA / Hibernate** — ORM for all persistence; use JPA annotations on all entities
- **Spring Data repositories** — `JpaRepository` as the base for all repositories

H2 console is enabled for local inspection at `/h2-console`.

## User identification

- No authentication framework. Identity is passed via a plain HTTP header: `X-User-Id: {userId}`.
- Users (1 admin + 3 customers) are pre-seeded on application startup with known IDs and roles (`UserSeeder`).
- Movies (3 titles) are pre-seeded on application startup (`MovieSeeder`): Inception, The Dark Knight, Interstellar.
- Endpoints that require a user read `X-User-Id` from the request; if the header is absent, return HTTP 400.
- Admin endpoints verify that the user identified by `X-User-Id` has role `ADMIN`; if not, return HTTP 403.
- Do NOT add Spring Security or any authentication filter.

## API

- **Spring MVC** — REST controllers, `@RestController`
- **Springdoc OpenAPI 2.x** (`springdoc-openapi-starter-webmvc-ui`) — Swagger UI available at `/swagger-ui.html`
- All request/response bodies use dedicated DTO classes (no entity leakage into the API layer)
- **Spec-first approach** — API contract is defined in `src/main/resources/static/openapi.yaml` and is the single source of truth. Swagger UI reads from this file. Do NOT add OpenAPI annotations (`@Operation`, `@ApiResponse`, `@Schema`, etc.) to controllers or DTOs — the YAML is the only place where API documentation lives.
- **Request body content-type: `application/json`** — all endpoints that accept a request body use `@RequestBody` and bind parameters via Jackson deserialization. Do NOT use `@ModelAttribute` with `application/x-www-form-urlencoded` for incoming data.

## Frontend

- **Vanilla JS + HTML5 + CSS3** — single-file GUI at `src/main/resources/static/index.html`, served directly by Spring Boot as a static resource; no build step, no bundler, no npm
- **No frontend framework** — do NOT add React, Vue, Angular, or any JS framework; plain `fetch()` for API calls and DOM manipulation via template literals are sufficient
- **CSS Variables + Flexbox/Grid** — use CSS custom properties for theming and Flexbox/Grid for layout; no external CSS libraries (no Bootstrap, Tailwind, etc.)
- Hall layout is rendered visually as a seat grid with color-coded types (ACCESSIBLE, STANDARD, PREMIUM)
- `X-User-Id` header is set globally in the UI and sent with every request

## Scheduling

- No background job — seat lock expiry is handled lazily:
    - `isEffectivelyAvailable()` in `ReservationService` treats a LOCKED seat as available if `lockedUntil` is in the past
    - `confirmReservation()` checks whether `createdAt + 15 min` has passed and marks the reservation EXPIRED if so, releasing the seats on the spot

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
- Liquibase / Flyway — schema is managed by Hibernate `ddl-auto=update`
- Docker / Docker Compose — H2 removes the need for an external database
- Redis or any external cache — seat locking is handled in-database via JPA transactions
- Frontend frameworks (React, Vue, Angular) — Vanilla JS is sufficient
- Templating engines (Thymeleaf, Freemarker) — static HTML + fetch() is preferred
