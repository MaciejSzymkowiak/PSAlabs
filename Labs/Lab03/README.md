# Zadanie: Wizualizacja punktów w pMarineViewer z wykorzystaniem MOOSDB

## Cel zadania

Celem zadania jest napisanie aplikacji MOOS, która:

1. odczyta komunikat z **MOOSDB** ze zmiennej `DEF_POINTS`,
2. sparsuje współrzędne punktów zapisanych w postaci tekstowej,
3. wyświetli te punkty w aplikacji **pMarineViewer**.

---

# Format danych wejściowych

Punkty zapisane są w zmiennej MOOS:

```
DEF_POINTS
```

w formacie tekstowym:

```
(x1,y1);(x2,y2);(x3,y3)
```

Przykład:

```
DEF_POINTS = "(10,20);(30,40);(50,60)"
```

Każdy fragment:

```
(x,y)
```

oznacza **punkt w układzie współrzędnych MarineViewer**.

---

# Oczekiwany efekt

Po uruchomieniu programu w **pMarineViewer** powinny pojawić się punkty odpowiadające współrzędnym zapisanym w `DEF_POINTS`.

Dla przykładu:

```
(10,20);(30,40);(50,60)
```

powinny zostać wyświetlone **trzy punkty** w odpowiednich miejscach mapy.

---

# Wymagania funkcjonalne

Program powinien:

1. Subskrybować zmienną:

```
DEF_POINTS
```

2. Odebrać komunikat tekstowy z MOOSDB.

3. Podzielić komunikat na fragmenty odpowiadające punktom:

```
(x1,y1)
(x2,y2)
(x3,y3)
```

4. Usunąć nawiasy z każdego fragmentu.

5. Rozdzielić współrzędne `x` i `y`.

6. Zamienić wartości tekstowe na liczby (`double`).

7. Utworzyć obiekt punktu (`XYPoint`).

8. Wysłać punkt do wizualizacji w **pMarineViewer** przy użyciu zmiennej:

```
VIEW_POINT
```

---

# Sugerowany algorytm

1. Odbierz komunikat z MOOSDB:

```
"(10,20);(30,40);(50,60)"
```

2. Podziel tekst po separatorze:

```
;
```

3. Otrzymasz listę fragmentów:

```
"(10,20)"
"(30,40)"
"(50,60)"
```

4. Dla każdego fragmentu:

- usuń `(` oraz `)`
- podziel tekst po `,`
- zamień wartości na `double`

5. Utwórz punkt:

```
XYPoint
```

6. Wyślij punkt do pMarineViewer:

```cpp
Notify("VIEW_POINT", point.get_spec());
```

---

# Wymagania implementacyjne

Program powinien korzystać z:

- `parseString()` lub podobnej funkcji do dzielenia tekstu
- `vector<string>`
- `stod()` do konwersji tekst → liczba
- klasy `XYPoint` z biblioteki Geometry MOOS-IvP (Opis wykorzystania biblioteki jak i klasy znajduje się w /Materials/biblioteki)

dodatkowo:

- Program musi obsługiwać dowolną liczbę punktów w komunikacie.
- Każdy punkt musi mieć **unikalną nazwę** (np. `P1`, `P2`, `P3`).
- Punkty były wyświetlane w **innym kolorze** niż domyślny.


---

# Przykładowy przebieg programu

Dla komunikatu:

```
DEF_POINTS = "(10,20);(30,40);(50,60)"
```

program powinien utworzyć punkty:

```
Point 1: x=10 y=20
Point 2: x=30 y=40
Point 3: x=50 y=60
```

które zostaną wyświetlone w **pMarineViewer**.

---

# Wskazówki

- Sprawdź czy komunikat z `DEF_POINTS` nie jest pusty.
- Upewnij się, że parser poprawnie usuwa nawiasy `(` i `)`.
- Każdy punkt należy wysłać osobno do `VIEW_POINT`.
- Przeczytaj dobrze opis bilioteki geometry w folderze Materials.

---

