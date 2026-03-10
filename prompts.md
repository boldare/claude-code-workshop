# Prompts - CinemaBook Workshop

## Przygotowanie projektu

#1 - Inicjalizacja projektu na podstawie tech-stack - [PLAN MODE]
Zgodnie ze specyfikacją z @tech-stack.md oraz @prd.md stwórz projekt Spring Boot o nazwie `cinemabooking`. Nie implementuj na razie żadnej logiki ani domeny biznesowej - tym zajmiemy się później. Projekt powinien się poprawnie uruchamiać, a możemy to sprawdzić poprzez stworzenie pustego testu integracyjnego, który sprawdza czy context springa uruchamia się poprawnie.

#2 - Konfiguracja CLAUDE.md
/init Na podstawie @tech-stack.md. Dodaj w CLAUDE.md link do tech-stack.md jako źródło prawdy o stosie technologicznym.

--- CHECKPOINT

#3 - Własna komenda `/commit`
Stwórz custom slash command dla Claude Code, który: analizuje ostatnie zmiany w repozytorium, generuje zwięzłą wiadomość commita w języku angielskim zgodną z Conventional Commits, i wykonuje commit. Komenda powinna być dostępna jako `/commit`.

--- CHECKPOINT

## Kontrakt API

#4 - Zdefiniuj OpenAPI spec dla UC-1 - [PLAN MODE, Opus]
Na podstawie US-003 z @prd.md zdefiniuj kontrakt API w pliku `openapi.yaml`.
Zdefiniuj schemat odpowiedzi, kody HTTP i przykładowe odpowiedzi.
Nie implementuj jeszcze niczego — to jest kontrakt, nie implementacja. Zaczynamy od tego, co system mówi światu.

--- CHECKPOINT

## UC-1: Wizualizacja sali (Backend)

#5 - Skill do pisania testów
Na podstawie pliku `testing-rules.md` zdefiniuj projektowy skill Claude Code, który jest automatycznie stosowany podczas pisania testów. Skill powinien wymuszać zasady zdefiniowane we wspomnianym pliku.

#6 - Implementacja endpointu mapy sali (BE) - [PLAN MODE, Opus]
Zaimplementuj zdefiniowany uprzednio endpoint zgodnie z kontraktem z `openapi.yaml` i US-003 z @prd.md.

Wymagania endpointu:
- Zwraca listę wszystkich miejsc seansu.
- Miejsca posortowane po rzędzie, potem po numerze.
- HTTP 404 gdy seans nie istnieje.
- Dedykowane DTO zgodne ze schematem z `openapi.yaml` — unikaj podejścia encja na twarz.
- Encje JPA i repozytoria pojawiają się jako detal implementacyjny — nie projektuj ich z góry, niech wynikną z potrzeb logiki biznesowej.

Zacznij od testu integracyjnego, który weryfikuje kontrakt z openapi.yaml: HTTP 200 z poprawną strukturą odpowiedzi oraz HTTP 404 dla nieistniejącego seansu. Test powinien być czerwony, dopóki endpoint nie zostanie zaimplementowany — a potem zielony po implementacji.
Warunek akceptacji: mvn clean install przechodzi bez błędów.

--- CHECKPOINT

## Dane wsadowe

[META-PROMPTING]
#7 - Przygotowanie prompta do danych wsadowych
Musimy teraz znaleźć jakiś sposób na przygotowanie danych testowych, które będą przygotowane tylko raz - podczas pierwszego uruchomienia aplikacji. Później dane powinny pozostać spersystowane w bazie danych. Przygotuj proszę prompta, którego możemy wykorzystać do tego celu - bądź zwięzły, oraz konkretny. Zróbmy szybki brainstorming jak to zrobić w najlepszy sposób. Odwołaj się do @tech-stack.md gdzie zdefiniowane są wstępne wymagania. Dane powinny być idempotentne — sprawdzaj przed zapisem czy już istnieją. Baza H2 jest file-based, więc dane powinny przetrwać restart aplikacji.

#8 - Aktualizacja CLAUDE.md o dane wsadowe
Zaktualizuj plik CLAUDE.md, dodając sekcję opisującą predefiniowane dane testowe: znane ID i role użytkowników, tytuły preseeded filmów, konfigurację przykładowej sali. Te dane są używane w testach i przy ręcznym testowaniu przez Swagger UI.

--- CHECKPOINT

## UC-1: Wizualizacja sali (Frontend)

#9 - Frontend: wizualizacja mapy sali [PLAN_MODE, Opus]
Zaimplementuj widok mapy sali w `src/main/resources/static/index.html` używając technologii zgodnych z @tech-stack.md.
Znajdź odpowiedni endpoint backendowy do pobrania danych o miejscach i zaproponuj koncepcję wizualizacji w plan mode.
Załączam screenshot referencyjny sali kinowej jako wzorzec wizualny: [wstaw screenshot].
Warunek akceptacji: strona ładuje się pod `localhost:8080` bez błędów w konsoli, mapa sali jest widoczna i czytelna.

--- CHECKPOINT

#10 - Testy jednostkowe UC-1
Napisz testy jednostkowe dla serwisu obsługującego mapę sali. Przetestuj: poprawne zwracanie listy miejsc posortowanej po rzędzie i numerze miejsca, filtrowanie statusów (AVAILABLE, LOCKED, RESERVED), rzucanie wyjątku gdy seans nie istnieje, poprawne mapowanie encji na DTO. Testy powinny być izolowane i szybkie.

--- CHECKPOINT

## UC-2: Rezerwacja biletu (Backend)

#11 - Zdefiniuj kontrakt API dla UC-2 - [PLAN MODE, Opus]
Na podstawie US-004, US-005, US-006 i US-007 z @prd.md rozszerz `openapi.yaml` o kontrakt dla pełnego flow rezerwacji: blokowanie miejsc, potwierdzenie, anulowanie i historia rezerwacji klienta. Zdefiniuj schematy żądań i odpowiedzi, kody HTTP i przykładowe odpowiedzi. Nie implementuj niczego — skupmy się wyłączenie na kontrakcie.

#12 - Implementacja rezerwacji (BE) - [PLAN MODE, Opus]
Zaimplementuj flow rezerwacji zgodnie z US-004, US-005, US-006, US-007 z @prd.md oraz kontraktem z `openapi.yaml`.

Wymagane zdolności systemu: blokowanie miejsc, potwierdzenie rezerwacji, anulowanie przez klienta, pobieranie historii rezerwacji klienta. Zadbaj o ochronę przed double-booking z użyciem lockowania JPA.

Zacznij od akceptacyjnego testu integracyjnego, który weryfikuje kontrakt z `openapi.yaml` — happy path oraz kluczowe scenariusze błędów. Test powinien być czerwony, dopóki endpointy nie zostaną zaimplementowane — a potem zielony po implementacji.
Warunek akceptacji: mvn clean install przechodzi bez błędów.

--- CHECKPOINT

## UC-2: Rezerwacja biletu (Frontend)

#13 - Frontend: flow rezerwacji
Rozszerz `index.html` o interaktywny flow rezerwacji zgodnie z kontraktem z `openapi.yaml`: kliknięcie na dostępne miejsce zaznacza je (toggle), panel boczny pokazuje wybrane miejsca i łączną cenę, przycisk "Zarezerwuj" blokuje wybrane miejsca, po sukcesie wyświetla modal z kodem rezerwacji i czasem do potwierdzenia, przycisk "Potwierdź" finalizuje rezerwację, komunikaty błędów wyświetlane użytkownikowi inline.
Warunek akceptacji: pełny flow zaznaczenie → rezerwacja → potwierdzenie działa w przeglądarce bez błędów w konsoli.

--- CHECKPOINT

#14 - Testy jednostkowe UC-2
Napisz testy jednostkowe dla serwisu rezerwacji. Przetestuj: blokowanie dostępnych miejsc i tworzenie rezerwacji PENDING, odrzucenie gdy któreś miejsce jest LOCKED lub RESERVED, rozpoznawanie wygasłej blokady, potwierdzenie rezerwacji w oknie 15 minut, odrzucenie potwierdzenia po upływie 15 minut z oznaczeniem jako EXPIRED i zwolnieniem miejsc, obliczanie łącznej ceny na podstawie typów miejsc i progów cenowych seansu.

--- CHECKPOINT

## Testy integracyjne i jakość

#15 - Dodatkowe testy integracyjne (edge cases)
Napisz dodatkowe testy integracyjne obejmujące edge cases i scenariusze współbieżności: (1) wygaśnięcie blokady — miejsce LOCKED z `lockedUntil` w przeszłości pojawia się jako AVAILABLE w mapie sali i można je zarezerwować; (2) próba potwierdzenia wygasłej rezerwacji → HTTP 409 z komunikatem "Reservation expired", miejsca zwolnione; (3) współbieżna rezerwacja tego samego miejsca — dokładnie jedno żądanie kończy się sukcesem; (4) anulowanie rezerwacji → miejsca wracają do AVAILABLE; (5) admin anuluje rezerwację klienta; (6) klient próbuje anulować rezerwację innego klienta → HTTP 403.

#16 - Weryfikacja wszystkich testów
Uruchom wszystkie testy i upewnij się, że przechodzą. Zidentyfikuj i napraw wszelkie błędy. Sprawdź pokrycie testami core flow rezerwacji: przeglądanie seansów → mapa sali → blokowanie miejsc → potwierdzenie → anulowanie — każdy krok powinien mieć co najmniej jeden test happy path i testy dla kluczowych przypadków błędów.

--- CHECKPOINT

## Finalizacja

#17 - Manualna weryfikacja w przeglądarce
Uruchom aplikację i pomóż mi zweryfikować manualnie przez przeglądarkę: otwórz `http://localhost:8080`, sprawdź czy mapa sali wyświetla się poprawnie dla przykładowego seansu, przetestuj flow rezerwacji (wybór miejsc → rezerwacja → potwierdzenie), sprawdź Swagger UI pod `/swagger-ui.html`, otwórz konsolę H2 pod `/h2-console` i zweryfikuj stan danych po operacjach. Zaproponuj kroki do manualnego smoke testu.

#18 - Code review
Przeprowadź code review zaimplementowanego projektu. Sprawdź: 
1) poprawność logiki biznesowej zgodnie z @prd.md;
2) bezpieczeństwo — czy nie ma wycieku encji do API, czy autoryzacja jest poprawna;
3) jakość kodu — użycie Lombok, brak antywzorców JPA, poprawna obsługa transakcji;
4) pokrycie testami;
5) zgodność z @tech-stack.md. 
Przedstaw listę znalezionych problemów z priorytetem (critical/major/minor).

--- CHECKPOINT
