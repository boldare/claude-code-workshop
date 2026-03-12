# Ćwiczenie: Złap co zginęło

## Cel

Doświadczyć w praktyce różnicy między **"AI powiedziało że skończyło"** a **"jest faktycznie skończone"**.

## Wymagania

- Java (JDK 8+)
- Claude Code
- Terminal

---

## Krok 1 — Wygeneruj kod (2 minuty)

Otwórz terminal w pustym katalogu i uruchom Claude Code:

```bash
mkdir password-exercise && cd password-exercise
claude
```

Wklej dokładnie ten prompt:

```
napisz funkcję która waliduje hasło. Wymagania:
1. minimum 8 znaków
2. przynajmniej jedna wielka litera
3. przynajmniej jedna cyfra
4. przynajmniej jeden znak specjalny
5. nie może zawierać spacji
6. zwraca konkretny komunikat błędu dla każdego niespełnionego wymagania

napisz metodę main która wczytuje hasło z argumentu linii komend i wypisuje wynik walidacji
```

Zaakceptuj wygenerowany kod **bez czytania**.

---

## Krok 2 — Skompiluj

```bash
javac PasswordValidator.java
```

---

## Krok 3 — Wygeneruj przypadki testowe i sprawdź wyniki (5 minut)

Poproś Claude Code o wygenerowanie przypadków testowych i ich uruchomienie:

```
dla każdego z 6 wymagań wygeneruj hasło które łamie tylko to jedno wymaganie,
uruchom PasswordValidator dla każdego przypadku i pokaż wyniki
```

Claude Code wygeneruje hasła, odpali je i wyświetli wyniki dla każdego przypadku.

---

## Krok 4 — Odpowiedz sobie na pytania

1. Czy każde wymaganie z listy jest sprawdzane?
2. Czy dla każdego błędu dostajesz **konkretny** komunikat (np. *"Hasło musi zawierać cyfrę"*) czy **ogólny** (np. *"Hasło jest nieprawidłowe"*)?
3. Co AI pominęło lub tylko udało że zrobiło?

---

## Czego szukamy

Dwa typowe problemy które AI generuje w tym ćwiczeniu:

**Baby-counting** — jedno z wymagań po prostu nie istnieje w kodzie. Najczęściej: spacja (#5) lub obsługa wielu błędów jednocześnie.

**Cardboard muffin** — wymaganie #6 pozornie spełnione: funkcja zwraca komunikat błędu, ale ten sam dla wszystkich przypadków. Technicznie błędu brak — ale wymaganie nie jest spełnione.

---

> Nie chodzi o to żeby AI poprawić. Chodzi o to żeby zobaczyć że **systematyczna weryfikacja wymaganie po wymaganiu** daje zupełnie inny obraz niż "odpaliło się i wygląda OK".
