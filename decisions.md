# Decision Log — CinemaBook

---

### Rectangular hall grid
**Why**: Arbitrary per-seat layout configuration adds accidental complexity with no training value.
**Decision**: A hall is defined by `name`, `rows`, and `seatsPerRow`. All seats are auto-generated as a rectangular grid on hall creation.

---

### Seat types assigned by row position
**Why**: Avoids per-seat type configuration while keeping pricing differentiation meaningful.
**Decision**: Row 1 → `ACCESSIBLE`; last 2 rows → `PREMIUM`; all other rows → `STANDARD`.

---

### No authentication framework — X-User-Id header
**Why**: Spring Security + JWT setup is complex boilerplate that blocks non-Java participants and adds no domain value. The training goal is demonstrating Claude Code on business logic, not security configuration.
**Decision**: No Spring Security, no JWT. Identity is passed as a plain `X-User-Id: {userId}` header. Users (1 admin + 3 customers) are pre-seeded on startup. Admin role is enforced by checking the user's role in the database. Missing header → HTTP 400; non-admin on admin endpoint → HTTP 403.

---

### Payment modelled as status only — no gateway
**Why**: Payment gateway integration is out of scope and would require external dependencies.
**Decision**: Payment is a status field on the reservation: `PENDING → PAID → REFUNDED`. Confirming a reservation sets it to `PAID`; cancelling sets it to `REFUNDED`.

---

### Seat locking via database timestamp — no external cache
**Why**: Keeps infrastructure simple; no Redis or external process required.
**Decision**: Locking is stored as a `lockedUntil` timestamp on `ShowSeat`. A `@Scheduled` job runs every minute and releases seats whose lock has expired and whose reservation is still `PENDING`.

---

### Seed reference data — defer admin CRUD
**Why**: The core product value is the reservation flow, not admin data management. Building admin CRUD (US-010, US-011, US-012) before the booking flow delays validation of the most important business logic.
**Decision**: Movies, halls (with seats), and screenings (with seat entries) are pre-seeded on application startup — just like users. The core booking flow (US-003 → US-004 → US-005 → US-006 → US-007) is implemented first against seeded data. Admin CRUD endpoints are deferred until after the core flow is complete.
