# Prompts - CinemaBook Workshop

## Przygotowanie projektu

+++
#1 - Inicjalizacja projektu na podstawie tech-stack - [PLAN MODE]
Zgodnie ze specyfikacją z @tech-stack.md stwórz projekt Spring Boot o nazwie `cinemabooking`. Wygeneruj strukturę katalogów według zdefinionwaych pakietów, skonfiguruj plik `pom.xml` z wszystkimi, niezbędnymi zależnościami oraz ustaw H2 jako bazę file-based. Nie implementuj na razie żadnej logiki ani domeny biznesowej - tym zajmiemy sie później. Projekt powinien się poprawnie uruchamiać, a możemy to sprawdzić poprzez stworzenie pustego testu integracyjnego, który sprawdza czy context springa uruchamia się poprawnie. Jeśli masz jakieś wątpliwości, śmiało pytaj - postaram się odpowiedzieć. Przygotuj także plik .gitignore z regułami dla Javy, IntelliJ oraz VS Code.

+++
#2 - Konfiguracja CLAUDE.md
/init Skonfiguruj plik CLAUDE.md dla tego projektu opisując: tech stack, strukturę pakietów oraz konwencje kodu (użycie Lombok, DTO zamiast encji w API, spec-first OpenAPI w `openapi.yaml`) i zasady identyfikacji użytkownika.

--- CHECKPOINT

## Model danych

#3 - Zdefiniuj model danych - [PLAN MODE, Opus]
Na podstawie prd.md (sekcje 4 i 6) oraz decisions.md zaprojektuj i zaimplementuj encje JPA i typy wyliczeniowe dla wszystkich domen projektu. Zastosuj konwencje z CLAUDE.md. Jeśli cokolwiek jest niejednoznaczne — pytaj zanim zaczniesz implementację.
Warunek akceptacji: mvn clean install przechodzi bez błędów.
Nie implementuj jeszcze kontrolerów, serwisów, repozytoriów ani DTO.

--- CHECKPOINT

## Dane wsadowe
[META-PROMPTING]
#4 - Musimy teraz znaleźć jakiś sposób na przygotowanie danych testowych, które będą przygotowane tylko raz - podczas pierwszego uruchomienia aplikacji. Później dane powinny pozostać spersystowane w bazie danych. Przygotuj proszę prompta, którego możemy wykorzystać do tego celu - bądź zwięzły, oraz konkretny. Zróbmy szybki brainstorming jak to zrobić w najlepszy sposób.

#5 - Zaimplementuj seed data uruchamiany przy starcie aplikacji. Utwórz klasy `UserSeeder`, `MovieSeeder` i `HallSeeder` implementujące `CommandLineRunner`. `UserSeeder` powinien tworzyć 1 admina i 3 klientów ze stałymi ID i rolami. `MovieSeeder` powinien tworzyć 3 filmy: Inception, The Dark Knight, Interstellar. `HallSeeder` powinien tworzyć przykładową salę kinową z 10 rzędami i 15 miejscami w rzędzie. Dane powinny być idempotentne — sprawdzaj przed zapisem czy już istnieją. Baza H2 jest file-based, więc dane powinny przetrwać restart aplikacji.

#6 - Aktualizacja CLAUDE.md o dane wsadowe
Zaktualizuj plik CLAUDE.md, dodając sekcję opisującą predefiniowane dane testowe: znane ID i role użytkowników, tytuły preseeded filmów, konfigurację przykładowej sali. Te dane są używane w testach i przy ręcznym testowaniu przez Swagger UI.

--- CHECKPOINT

#7 - Skill do pisania testów
Na podstawie pliku `testing-rules.md` zdefiniuj projektowy skill Claude Code, który jest automatycznie stosowany podczas pisania testów. Skill powinien wymuszać zasady zdefiniowane we wspomnianym pliku.

## UC-1: Wizualizacja sali (Backend)

#8 - Test akceptacyjny UC-1 (wizualizacja sali)
Napisz test integracyjny (`@SpringBootTest` + `MockMvc`) dla US-003 (widok mapy sali). Test powinien: pobrać identyfikator istniejącego seansu, wywołać endpoint `GET /screenings/{id}/seats`, zweryfikować HTTP 200 i że odpowiedź zawiera listę miejsc z polami `row`, `seatNumber`, `type` (STANDARD/PREMIUM/ACCESSIBLE), `status` (AVAILABLE/LOCKED/RESERVED), posortowanych po rzędzie i numerze miejsca. Uwzględnij też przypadek: nieistniejący seans → HTTP 404. Test ma być czerwony dopóki endpoint nie zostanie zaimplementowany.

#9 - Implementacja endpointu mapy sali (BE) - [PLAN MODE, Opus]
Przejdź do plan mode. Zaimplementuj endpoint `GET /screenings/{screeningId}/seats` zgodnie z US-003 z @prd.md. Endpoint powinien zwracać listę wszystkich miejsc seansu z polami: `seatId`, `row`, `seatNumber`, `type` (STANDARD/PREMIUM/ACCESSIBLE), `status` (AVAILABLE/LOCKED/RESERVED). Miejsca posortowane po rzędzie, potem po numerze. HTTP 404 gdy seans nie istnieje. Użyj dedykowanego DTO, nie wyciekaj encji. Zaktualizuj `openapi.yaml` o nowy endpoint.

--- CHECKPOINT

#10 - Frontend: wizualizacja mapy sali
Zaimplementuj widok mapy sali w `src/main/resources/static/index.html` używając technologii zgodnych z @tech-stack.md (HTML5, CSS3 i Vanilla JS - bez adnych dodatkowych frameworków).
Strona powinna: wyświetlać siatkę miejsc sali pobraną z `GET /screenings/{id}/seats`, kolorować miejsca według typu (ACCESSIBLE — niebieski, STANDARD — szary, PREMIUM — złoty) i statusu (AVAILABLE — pełny kolor, LOCKED — półprzezroczysty, RESERVED — ciemny/niedostępny), używać CSS Grid do układu rzędów i kolumn, pobierać dane przez `fetch()` z nagłówkiem `X-User-Id`. 
Załączam przykładowy screenshot sali kinowej jako wzorzec wizualny: 

opcja 1:
Załączam przykładowy screenshot sali kinowej jako wzorzec wizualny: [wstaw screenshot].

opcja 2:
Przygotuj 5 różnych wariantów wizualnych widoku mapy sali kinowej jako osobne bloki HTML/CSS w jednym pliku podglądowym `hall-variants.html`. Każdy wariant powinien różnić się stylem (np. dark cinema theme, minimalistyczny, retro, nowoczesny gradientowy, pastelowy). Zachowaj tę samą strukturę danych i semantykę kolorowania miejsc. Po zapoznaniu się z wariantami wybiorę jeden do dalszego rozwoju.

Sprawdź z pomocą Claudea czy rzeczywiście działa to poprawnie.
--- CHECKPOINT

#11 - Testy jednostkowe UC-1
Napisz testy jednostkowe dla serwisu obsługującego mapę sali. Przetestuj: poprawne zwracanie listy miejsc posortowanej po rzędzie i numerze miejsca, filtrowanie statusów (AVAILABLE, LOCKED, RESERVED), rzucanie wyjątku gdy seans nie istnieje, poprawne mapowanie encji na DTO. Testy powinny być izolowane i szybkieK---
-- CHECKPOINT
