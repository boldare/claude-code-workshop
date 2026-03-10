# Prompts - CinemaBook Workshop

## Przygotowanie projektu

+++
#1 - Inicjalizacja projektu na podstawie tech-stack - [PLAN MODE]
Zgodnie ze specyfikacją z @tech-stack.md oraz @prd.md stwórz projekt Spring Boot o nazwie `cinemabooking`. Nie implementuj na razie żadnej logiki ani domeny biznesowej - tym zajmiemy się później. Projekt powinien się poprawnie uruchamiać, a możemy to sprawdzić poprzez stworzenie pustego testu integracyjnego, który sprawdza czy context springa uruchamia się poprawnie.

+++
#2 - Konfiguracja CLAUDE.md
/init Na podstawie @tech-stack.md. Dodaj w CLAUDE.md link do tech-stack.md jako źródło prawdy o stosie technologicznym.

--- CHECKPOINT

## Kontrakt API

#3 - Zdefiniuj OpenAPI spec dla UC-1 - [PLAN MODE, Opus]
Na podstawie US-003 z @prd.md zdefiniuj kontrakt API w pliku `openapi.yaml` dla endpointu `GET /screenings/{screeningId}/seats`.
Zdefiniuj schemat odpowiedzi (`SeatResponse`, `SeatStatus` enum, itp.), kody HTTP (200, 404) i przykładowe odpowiedzi. Zastosuj konwencje z CLAUDE.md.
Nie implementuj jeszcze niczego — to jest kontrakt, nie implementacja. Zaczynamy od tego, co system mówi światu.

--- CHECKPOINT

## UC-1: Wizualizacja sali (Backend)

#4 - Skill do pisania testów
Na podstawie pliku `testing-rules.md` zdefiniuj projektowy skill Claude Code, który jest automatycznie stosowany podczas pisania testów. Skill powinien wymuszać zasady zdefiniowane we wspomnianym pliku.

#5 - Implementacja endpointu mapy sali (BE) - [PLAN MODE, Opus]
Przejdź do plan mode. Zaimplementuj endpoint `GET /screenings/{screeningId}/seats` zgodnie z kontraktem z `openapi.yaml` i US-003 z @prd.md.

Wymagania endpointu:
- Zwraca listę wszystkich miejsc seansu z polami: `seatId`, `row`, `seatNumber`, `type`, `status`.
- Miejsca posortowane po rzędzie, potem po numerze.
- HTTP 404 gdy seans nie istnieje.
- Dedykowane DTO zgodne ze schematem z `openapi.yaml` — unikaj podejścia encja na twarz.
- Encje JPA i repozytoria pojawiają się jako detal implementacyjny — nie projektuj ich z góry, niech wynikną z potrzeb logiki biznesowej.

Zacznij od testu integracyjnego, który weryfikuje kontrakt z openapi.yaml: HTTP 200 z poprawną strukturą odpowiedzi oraz HTTP 404 dla nieistniejącego seansu. Test powinien być czerwony, dopóki endpoint nie zostanie zaimplementowany — a potem zielony po implementacji.

--- CHECKPOINT

## Dane wsadowe

[META-PROMPTING]
#6 - Musimy teraz znaleźć jakiś sposób na przygotowanie danych testowych, które będą przygotowane tylko raz - podczas pierwszego uruchomienia aplikacji. Później dane powinny pozostać spersystowane w bazie danych. Przygotuj proszę prompta, którego możemy wykorzystać do tego celu - bądź zwięzły, oraz konkretny. Zróbmy szybki brainstorming jak to zrobić w najlepszy sposób.

#7 - Zaimplementuj seed data uruchamiany przy starcie aplikacji. Utwórz klasy `UserSeeder`, `MovieSeeder` i `HallSeeder` implementujące `CommandLineRunner`. `UserSeeder` powinien tworzyć 1 admina i 3 klientów ze stałymi ID i rolami. `MovieSeeder` powinien tworzyć 3 filmy: Inception, The Dark Knight, Interstellar. `HallSeeder` powinien tworzyć przykładową salę kinową z 10 rzędami i 15 miejscami w rzędzie. Dane powinny być idempotentne — sprawdzaj przed zapisem czy już istnieją. Baza H2 jest file-based, więc dane powinny przetrwać restart aplikacji.

#8 - Aktualizacja CLAUDE.md o dane wsadowe
Zaktualizuj plik CLAUDE.md, dodając sekcję opisującą predefiniowane dane testowe: znane ID i role użytkowników, tytuły preseeded filmów, konfigurację przykładowej sali. Te dane są używane w testach i przy ręcznym testowaniu przez Swagger UI.

--- CHECKPOINT

## UC-1: Wizualizacja sali (Frontend)

#9 - Frontend: wizualizacja mapy sali [PLAN_MODE, Opus]
Zaimplementuj widok mapy sali w `src/main/resources/static/index.html` używając technologii zgodnych z @tech-stack.md.
Znajdź odpowiedni endpoint backendowy do pobrania danych o miejscach i zaproponuj koncepcję wizualizacji w plan mode.
Załączam screenshot referencyjny sali kinowej jako wzorzec wizualny: [wstaw screenshot].

--- CHECKPOINT

#10 - Testy jednostkowe UC-1
Napisz testy jednostkowe dla serwisu obsługującego mapę sali. Przetestuj: poprawne zwracanie listy miejsc posortowanej po rzędzie i numerze miejsca, filtrowanie statusów (AVAILABLE, LOCKED, RESERVED), rzucanie wyjątku gdy seans nie istnieje, poprawne mapowanie encji na DTO. Testy powinny być izolowane i szybkie.

--- CHECKPOINT
