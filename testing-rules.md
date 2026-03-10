# Testing Rules - CinemaBook

## Zasady ogólne

### 1. Testujemy zachowanie, nie implementację

Testy weryfikują **co** robi kod (zachowanie widoczne z zewnątrz), a nie **jak** to robi (szczegóły implementacji).

- Nie testuj prywatnych metod — jeśli logika jest złożona, wyodrębnij ją do osobnej klasy
- Nie sprawdzaj, ile razy wywołano metodę wewnętrzną — sprawdzaj efekt końcowy
- Refactoring kodu nie powinien wymagać zmian w testach, o ile zachowanie pozostaje takie samo

```java
// ZLE - testujemy szczegół implementacji
verify(reservationRepository, times(1)).save(any());

// DOBRZE - testujemy efekt / stan
assertThat(reservation.getStatus()).isEqualTo(ReservationStatus.CONFIRMED);
```

---

### 2. Struktura testu: // given / // when / // then

Każdy test musi być podzielony na trzy sekcje oddzielone komentarzami:

```java
@Test
void shouldConfirmReservation_whenPaymentSucceeds() {
    // given
    Reservation reservation = buildPendingReservation();

    // when
    reservationService.confirm(reservation.getId());

    // then
    assertThat(reservation.getStatus()).isEqualTo(ReservationStatus.CONFIRMED);
}
```

- **// given** — przygotowanie stanu, danych wejściowych, stubów
- **// when** — wywołanie testowanej operacji (dokładnie jedna akcja)
- **// then** — weryfikacja rezultatu

---

### 3. Nazewnictwo testów

Format: `should[OczekiwaneZachowanie]_when[Warunek]`

```java
shouldReturnAvailableSeats_whenScreeningExists()
shouldThrowException_whenSeatIsAlreadyLocked()
shouldExpireReservation_whenConfirmationWindowExceeded()
shouldReturn403_whenUserIsNotAdmin()
```

- Nazwa testu powinna być czytelna jak zdanie w języku angielskim
- Unikaj nazw takich jak `test1`, `testBooking`, `checkReservation`

---

### 4. Jeden assert koncepcyjnie na test

Jeden test weryfikuje **jedną rzecz**. Dopuszczalne jest wiele asercji, jeśli razem opisują jeden spójny stan (np. obiekt po operacji), ale niedopuszczalne jest łączenie niezwiązanych asercji.

```java
// DOBRZE - wszystkie asercje dotyczą stanu jednego obiektu po operacji
assertThat(reservation.getStatus()).isEqualTo(CONFIRMED);
assertThat(reservation.getConfirmedAt()).isNotNull();

// ZLE - dwie niepowiązane weryfikacje w jednym teście
assertThat(reservation.getStatus()).isEqualTo(CONFIRMED);
assertThat(emailService).isNotNull(); // niezwiązane z główną asercją
```

---

## Testy unitowe

### Kiedy stosować

- Testy logiki biznesowej: reguły rezerwacji, wyliczanie cen, walidacja blokad miejsc, expiry
- Testy klas domenowych: `ReservationService`, `SeatLockPolicy`, obliczenia w `Reservation`

### Zasady

- Używaj **Mockito** do zastępowania zależności (repozytoria, zewnętrzne serwisy)
- **Nie mockuj** prostych obiektów domenowych (value objects, encje, klasy bez zależności) — twórz je bezpośrednio
- Klasa testowa: `[NazwaKlasy]Test`, np. `ReservationServiceTest`

```java
@ExtendWith(MockitoExtension.class)
class ReservationServiceTest {

    @Mock
    private ReservationRepository reservationRepository;

    @Mock
    private SeatRepository seatRepository;

    @InjectMocks
    private ReservationService reservationService;

    @Test
    void shouldThrowException_whenSeatIsAlreadyLocked() {
        // given
        Seat seat = buildLockedSeat();
        given(seatRepository.findById(seat.getId())).willReturn(Optional.of(seat));

        // when
        ThrowableAssert.ThrowingCallable action = () -> reservationService.lockSeat(seat.getId(), USER_ID);

        // then
        assertThatThrownBy(action)
            .isInstanceOf(SeatAlreadyLockedException.class);
    }
}
```

### Czego NIE mockować

```java
// ZLE - mockowanie prostego obiektu domenowego
Seat seat = mock(Seat.class);
when(seat.isLocked()).thenReturn(true);

// DOBRZE - tworzenie obiektu bezpośrednio lub przez builder
Seat seat = Seat.builder()
    .id(1L)
    .status(SeatStatus.LOCKED)
    .lockedUntil(LocalDateTime.now().plusMinutes(5))
    .build();
```

---

## Testy integracyjne

### Kiedy stosować

- Testy pełnego flow HTTP (od request do response)
- Weryfikacja integracji: kontroler → serwis → repozytorium → baza danych (H2)
- Testy reguł bezpieczeństwa (brak headera `X-User-Id`, brak roli ADMIN)

### Zasady

- Używaj `@SpringBootTest` + `MockMvc` — pełny kontekst aplikacji, baza H2 in-memory
- Klasa testowa: `[NazwaKontrolera]IntegrationTest`, np. `ReservationControllerIntegrationTest`
- Każdy test powinien być niezależny — dane przygotowuj w `@BeforeEach` lub wewnątrz testu

```java
@SpringBootTest
@AutoConfigureMockMvc
class ReservationControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ScreeningRepository screeningRepository;

    @Test
    void shouldReturn400_whenXUserIdHeaderIsMissing() throws Exception {
        // given
        long screeningId = existingScreeningId();

        // when
        ResultActions result = mockMvc.perform(
            post("/screenings/{id}/reservations", screeningId)
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"seatIds\": [1]}")
        );

        // then
        result.andExpect(status().isBadRequest());
    }

    @Test
    void shouldReturn403_whenUserIsNotAdmin() throws Exception {
        // given
        long screeningId = existingScreeningId();

        // when
        ResultActions result = mockMvc.perform(
            post("/screenings")
                .header("X-User-Id", CUSTOMER_USER_ID)
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    { "movieId": 1, "hallId": 1, "startsAt": "2025-06-01T18:00:00" }
                """)
        );

        // then
        result.andExpect(status().isForbidden());
    }
}
```

---

## Podsumowanie zasad

| Zasada | Opis |
|--------|------|
| Zachowanie, nie implementacja | Weryfikuj efekty, nie wywołania metod wewnętrznych |
| `// given / // when / // then` | Obowiązkowy podział każdego testu |
| Nazewnictwo | `should[Zachowanie]_when[Warunek]` |
| Jeden assert koncepcyjnie | Jeden test = jedna weryfikowana właściwość systemu |
| Mockito w unit testach | Mockuj zależności (repo, serwisy), nie obiekty domenowe |
| `@SpringBootTest` + `MockMvc` | Wymagane dla testów integracyjnych HTTP |
| Brak mocków dla logiki domenowej | Proste klasy domenowe twórz bezpośrednio przez konstruktor/builder |
