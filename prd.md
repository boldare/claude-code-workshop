# Product Requirement Document (PRD) - CinemaBook

## 1. Product overview

CinemaBook is a cinema ticket reservation system that allows customers to browse movies and screenings, select specific seats, and confirm reservations online. Real-time seat availability with temporary seat locking ensures no two customers can book the same seat simultaneously. Cinema administrators manage the full repertoire — movies, halls, and screenings — and access occupancy and revenue reports. The system enforces strict booking rules: seats are locked for 15 minutes upon selection and automatically released if the reservation is not confirmed within that window.

---

## 2. User problem

Buying cinema tickets today often means arriving at the cinema without knowing whether preferred seats are available, resulting in queues, poor seat choices, or missed screenings. Without a digital reservation system, there is no mechanism to prevent two customers from claiming the same seat, and administrators have no real-time visibility into occupancy or revenue. CinemaBook solves this by offering customers a self-service seat selection flow with instant confirmation, while giving admins a single tool to manage repertoire and monitor business performance.

---

## 3. User personas

### Customer

A moviegoer who wants to find a screening, pick specific seats, and receive a confirmed reservation before arriving at the cinema. Customers can browse movies and screenings without an account but must be logged in to complete a booking.

- **Casual viewer**: Books occasionally, values simplicity and clear seat availability.
- **Regular cinemagoer**: Books in advance, wants to select premium seats and track booking history.

**Key motivations**: guaranteed seat choice, fast booking process, easy cancellation
**Key concerns**: double-booking, losing selected seats while paying, unclear pricing
**Technical comfort**: Medium — comfortable with standard web booking flows

---

### Cinema Admin

A cinema operations manager responsible for maintaining the repertoire, configuring screening schedules, and monitoring business performance.

**Primary responsibilities**: manage movies, halls, and screenings; monitor seat occupancy; review revenue
**Key motivations**: efficient repertoire management, real-time occupancy data, accurate revenue reporting
**Key concerns**: scheduling conflicts, unauthorized modifications to repertoire, inaccurate seat data
**Technical comfort**: High — uses internal management tools daily

---

## 4. Functional requirements

- The system allows unauthenticated users to browse all movies with upcoming screenings and filter screenings by date.
- The system displays a seat map for a selected screening, showing each seat's type (STANDARD, PREMIUM, ACCESSIBLE) and current status (AVAILABLE, LOCKED, RESERVED).
- When an authenticated customer selects available seats, the system locks them for 15 minutes and prevents any other customer from selecting those seats during that window.
- The system automatically releases locked seats and restores their status to AVAILABLE when the 15-minute lock expires without a confirmed reservation.
- The system creates a reservation with a unique alphanumeric code when a customer confirms their selection, calculates the total price based on seat types and the screening's price tier, and advances the reservation status to CONFIRMED with payment status PAID.
- The system allows an authenticated customer to cancel a CONFIRMED reservation, releasing the seats back to AVAILABLE and marking the payment status as REFUNDED.
- Cinema admins can create and manage movies, define cinema halls by providing a name, a row count, and a seats-per-row count (the system auto-generates all seats as a rectangular grid), and schedule screenings by assigning a movie, a hall, a start time, and price tiers.
- The system rejects any reservation attempt that includes a seat already in LOCKED or RESERVED status for the same screening, regardless of concurrent requests.
- The system provides admins with a per-screening occupancy summary (seats sold, locked, available, and total revenue) and a per-movie revenue aggregation, filterable by date range.
- All booking, cancellation, and history operations require the user to be identified; all admin operations additionally require the user to have the Admin role.

---

## 5. Product borders

- **In scope**: movie/screening/hall CRUD, real-time seat map with seat types, 15-minute seat locking with automatic expiry, reservation lifecycle (PENDING → CONFIRMED → CANCELLED / EXPIRED), payment status model (PENDING → PAID → REFUNDED), reservation cancellation by customer and admin, user identification with pre-seeded accounts, admin role enforcement, hall scheduling conflict detection, occupancy and revenue reports, unit and integration test coverage for the core booking flow
- **Out of scope**: payment gateway or third-party payment integration, frontend or UI of any kind, email or SMS notifications, multi-venue or multi-city support, concessions or food ordering, loyalty programs, mobile application, ticket printing or QR code generation

---

## 6. User stories

- **US-001: Browse movies with upcoming screenings** [TODO]

  As a Customer,
  I want to browse all movies that have upcoming screenings,
  So that I can decide what to watch.

  **Acceptance Criteria:**

  - Given movies with upcoming screenings exist, when a customer browses the catalogue, then each movie is shown with its title, genre, duration, age rating, and nearest upcoming screening date.
  - Given a date filter is applied, when the customer browses the catalogue for a specific date, then only movies with at least one screening on that date are shown.
  - Given no movies have upcoming screenings, when the catalogue is browsed, then an empty list is shown.
  - Given no date filter is applied, when the catalogue is browsed, then movies with screenings within the next 30 days are shown.

- **US-002: View screenings for a movie** [TODO]

  As a Customer,
  I want to see all upcoming screenings for a specific movie,
  So that I can pick a convenient time and see seat availability at a glance.

  **Acceptance Criteria:**

  - Given a movie with upcoming screenings, when a customer views that movie's screenings, then each screening is shown with its hall name, start time, available seat count, and price tiers.
  - Given a date filter is applied, when the customer views screenings for a specific date, then only screenings on that date are shown.
  - Given the movie does not exist, when a customer tries to view its screenings, then an appropriate error is shown.
  - Given a movie has no upcoming screenings, when a customer views its screenings, then an empty list is shown.

- **US-003: View seat map for a screening** [TODO]

  As a Customer,
  I want to see the full seat map for a screening,
  So that I can choose my preferred seats before booking.

  **Acceptance Criteria:**

  - Given a screening exists, when a customer opens the seat map, then every seat is shown with its row number, seat number, type (STANDARD, PREMIUM, ACCESSIBLE), and status (AVAILABLE, LOCKED, RESERVED), ordered by row then by seat number.
  - Given some seats are locked by another customer, when the seat map is viewed, then those seats appear as LOCKED.
  - Given all seats are reserved, when the seat map is viewed, then all seats appear as RESERVED and available seat count is 0.
  - Given the screening does not exist, when a customer tries to open its seat map, then an appropriate error is shown.

- **US-004: Initiate a reservation (seat locking)** [TODO]

  As a Customer,
  I want to select seats for a screening and have them held for me,
  So that no one else can take them while I complete my booking.

  **Acceptance Criteria:**

  - Given all selected seats are AVAILABLE, when a customer submits a seat selection for a screening, then a reservation is created with status PENDING, selected seats become LOCKED, and the customer receives a unique reservation code, total price, and lock expiry time.
  - Given one or more selected seats are LOCKED or RESERVED, when the customer submits the selection, then the request is rejected and the customer is informed which seats are unavailable; no reservation is created.
  - Given the user is not identified, when a seat selection is submitted, then the request is rejected.
  - Given no seats are selected, when a seat selection is submitted, then the request is rejected.
  - Given more than 10 seats are selected, when a seat selection is submitted, then the request is rejected with an appropriate message.
  - Given a selected seat does not belong to the chosen screening, when the selection is submitted, then the request is rejected.

- **US-005: Confirm a reservation** [TODO]

  As a Customer,
  I want to confirm my pending reservation,
  So that my seats are permanently reserved and my payment is processed.

  **Acceptance Criteria:**

  - Given a PENDING reservation belonging to the customer and the lock has not expired, when the customer confirms the reservation, then the reservation becomes CONFIRMED, payment becomes PAID, and seats become RESERVED.
  - Given the 15-minute lock has expired, when the customer tries to confirm, then the confirmation fails with message "Reservation expired", the reservation is marked EXPIRED, and the seats are released.
  - Given the reservation belongs to a different customer, when a customer tries to confirm it, then the request is rejected.
  - Given the reservation is already CONFIRMED, when the customer tries to confirm it again, then the request is rejected.
  - Given the reservation does not exist, when a customer tries to confirm it, then an appropriate error is shown.

- **US-006: Automatic seat lock expiry** [TODO]

  As a Customer,
  I want seats I did not confirm to be released automatically,
  So that other customers can book them after my session times out.

  **Acceptance Criteria:**

  - Given a PENDING reservation created more than 15 minutes ago, when the system runs its expiry check, then the reservation status changes to EXPIRED and all associated seats return to AVAILABLE.
  - Given a PENDING reservation confirmed before 15 minutes have elapsed, when the expiry check runs, then that reservation is not affected.
  - Given seats are released by expiry, when any customer fetches the seat map afterward, then those seats appear as AVAILABLE.

- **US-007: Cancel a reservation** [TODO]

  As a Customer,
  I want to cancel my confirmed reservation,
  So that I can get a refund and free up the seats for others.

  **Acceptance Criteria:**

  - Given a CONFIRMED reservation belonging to the customer, when the customer cancels it, then the reservation becomes CANCELLED, payment becomes REFUNDED, and all associated seats return to AVAILABLE.
  - Given the reservation is already CANCELLED or EXPIRED, when the customer tries to cancel it, then the request is rejected.
  - Given the reservation belongs to a different customer, when a customer tries to cancel it, then the request is rejected.
  - Given the reservation does not exist, when a customer tries to cancel it, then an appropriate error is shown.

- **US-008: View my reservations** [TODO]

  As a Customer,
  I want to see all my reservations,
  So that I can track upcoming bookings and past transactions.

  **Acceptance Criteria:**

  - Given a customer with reservations, when the customer views their reservation history, then each reservation is shown with its code, movie title, screening date/time, hall name, seat list, total price, and status.
  - Given a status filter is applied, when the customer views their history, then only reservations with that status are shown.
  - Given the customer has no reservations, when their history is viewed, then an empty list is shown.
  - Given the user is not identified, when reservation history is requested, then the request is rejected.

- **US-009: User identification** [TODO]

  As a Customer or Cinema Admin,
  I want the system to know who I am when I make a request,
  So that my actions are attributed to my account and my permissions are enforced.

  **Acceptance Criteria:**

  - Given an identified user, when they perform any user-scoped action, then the action is attributed to their account and processed according to their role.
  - Given the user is not identified, when a user-scoped action is attempted, then the request is rejected.
  - Given an unknown user, when a user-scoped action is attempted, then an appropriate error is shown.
  - Given a customer, when an admin-only action is attempted, then the request is rejected.
  - Given an admin, when an admin action is performed, then the request proceeds.

- **US-010: Admin manages movies** [TODO]

  As a Cinema Admin,
  I want to create, update, and deactivate movies,
  So that the public catalogue reflects the current repertoire.

  **Acceptance Criteria:**

  - Given an admin, when a movie is created with title, description, duration, genre, and age rating, then the movie is saved and available for scheduling.
  - Given an admin, when a movie's details are updated, then the changes are saved.
  - Given an admin and the movie has no future screenings, when the movie is deactivated, then it no longer appears in the customer-facing catalogue.
  - Given the movie has future screenings, when an admin tries to deactivate it, then the request is rejected.
  - Given a non-admin user, when any admin action is attempted, then the request is rejected.

- **US-011: Admin manages cinema halls** [TODO]

  As a Cinema Admin,
  I want to define cinema halls with their seat layouts,
  So that screenings can be scheduled in correctly configured rooms.

  **Acceptance Criteria:**

  - Given an admin, when a hall is created with a name, number of rows, and seats per row, then the hall is saved and all seats are auto-generated as a rectangular grid; seat types are assigned by row: row 1 is ACCESSIBLE, the last 2 rows are PREMIUM, all other rows are STANDARD.
  - Given an admin, when a hall's details are viewed, then the hall name, dimensions, total seat count, and full seat list are shown.
  - Given an admin, when a hall's name is updated, then the change is saved.
  - Given the hall has future screenings scheduled, when an admin tries to change its dimensions, then the request is rejected.
  - Given invalid dimensions (rows or seats per row less than 1), when an admin tries to create a hall, then the request is rejected.

- **US-012: Admin schedules screenings** [TODO]

  As a Cinema Admin,
  I want to schedule a screening by assigning a movie, a hall, a start time, and pricing,
  So that customers can browse and book tickets for it.

  **Acceptance Criteria:**

  - Given a movie and a hall exist, when an admin schedules a screening with a start time, standard price, and premium price, then the screening is created and a seat entry is generated for every seat in the hall.
  - Given the hall is already occupied for an overlapping time slot (including a 30-minute buffer after the movie ends), when an admin tries to schedule a screening, then the request is rejected with message "Hall not available for the requested time slot".
  - Given no reservations exist for the screening, when an admin deletes it, then the screening and all its seat entries are removed.
  - Given at least one reservation exists for the screening, when an admin tries to delete it, then the request is rejected.

- **US-013: Concurrent booking protection** [TODO]

  As a Customer,
  I want the system to guarantee that two customers cannot book the same seat,
  So that I can trust that a confirmed reservation means I have that seat.

  **Acceptance Criteria:**

  - Given two customers simultaneously try to reserve the same seat for the same screening, when both requests are processed, then exactly one succeeds and the other is rejected with a message identifying the conflicting seat.
  - Given a seat is LOCKED by one customer, when a second customer tries to include it in their reservation, then the request is immediately rejected and no reservation is created.
  - Given a seat is RESERVED, when any customer tries to include it in a new reservation, then the request is rejected.

- **US-014: Admin views screening occupancy** [TODO]

  As a Cinema Admin,
  I want to see the occupancy breakdown for a screening,
  So that I can monitor how well individual screenings are performing.

  **Acceptance Criteria:**

  - Given a screening exists, when an admin views its occupancy report, then the total, reserved, locked, and available seat counts and total revenue from confirmed reservations are shown.
  - Given a screening with no reservations, when the occupancy report is viewed, then all seats show as available and revenue is 0.
  - Given a date range filter, when an admin views an aggregated screening report, then only screenings within that date range are included.

- **US-015: Admin views revenue per movie** [TODO]

  As a Cinema Admin,
  I want to see total revenue aggregated per movie,
  So that I can evaluate the commercial performance of the repertoire.

  **Acceptance Criteria:**

  - Given confirmed reservations exist for a movie, when an admin views the movie's revenue, then the total revenue from paid reservations is shown, broken down by screening.
  - Given no confirmed reservations exist for a movie, when the revenue report is viewed, then total revenue is 0.
  - Given a date range filter, when revenue is viewed, then only reservations confirmed within that date range are included.

- **US-016: Admin cancels a reservation** [TODO]

  As a Cinema Admin,
  I want to cancel any customer's confirmed reservation,
  So that I can handle exceptional situations such as in-person customer requests or operational issues.

  **Acceptance Criteria:**

  - Given an admin and a CONFIRMED reservation, when the admin cancels it, then the reservation becomes CANCELLED, payment becomes REFUNDED, and all associated seats return to AVAILABLE.
  - Given the reservation is already CANCELLED or EXPIRED, when an admin tries to cancel it, then the request is rejected.
  - Given the reservation does not exist, when an admin tries to cancel it, then an appropriate error is shown.
  - Given a non-admin user, when the admin cancellation action is attempted, then the request is rejected.

---

## 7. Success metrics

- **No double-booking**: No two CONFIRMED reservations share the same seat for the same screening under any conditions, including concurrent requests.
- **Seat lock lifecycle**: Locked seats automatically return to AVAILABLE after exactly 15 minutes if not confirmed; CONFIRMED seats permanently hold RESERVED status.
- **Reservation code uniqueness**: Every reservation carries a globally unique alphanumeric code; no two reservations in the system share the same code.
- **Role enforcement**: Admin-only operations are rejected for non-admin users; user-scoped operations are rejected when no user is identified.
- **Hall scheduling conflict detection**: Attempting to schedule two screenings in the same hall with overlapping time windows (including the 30-minute buffer) always returns HTTP 409.
- **Revenue accuracy**: The total revenue returned for a movie equals the sum of total prices of all CONFIRMED reservations across all screenings of that movie.
- **Core booking flow test coverage**: The full sequence — browse screenings → view seat map → lock seats → confirm reservation → cancel reservation — is covered by at least one integration test for the happy path and one for each critical error case (expired lock, conflict, unauthorized access).
