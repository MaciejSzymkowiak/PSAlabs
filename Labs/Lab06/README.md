# Laboratorium: Wizualizacja punktów komunikacyjnych i zasięgu łączności w MOOS-IVP

## Cel laboratorium

Celem laboratorium jest przygotowanie aplikacji dla środowiska **MOOS-IVP**, która:

- wczytuje z pliku konfiguracyjnego punkty komunikacyjne na trasie,
- obsługuje od **2 do 10 punktów**,
- dla każdego punktu wyznacza relację pomiędzy punktem komunikacyjnym a pojazdem,
- rysuje w `pMarineViewer` linię za pomocą **seglisty** od punktu komunikacyjnego do aktualnej pozycji pojazdu,
- zmienia kolor linii w zależności od odległości pojazdu od punktu,
- uwzględnia maksymalny zasięg komunikacji zdefiniowany w pliku konfiguracyjnym,
- po wyjściu pojazdu poza zasięg usuwa seglistę z wizualizacji i przestaje ją publikować,
- w przypadku braku zasięgu do **conajmniej jednego punktu komunikacyjnego** pojazd ma zakończyć misję.

---

## Kontekst zadania

W systemach autonomicznych istotne jest monitorowanie dostępności komunikacji z infrastrukturą.  
W tym laboratorium student implementuje aplikację analizującą relację pojazdu z punktami komunikacyjnymi oraz reagującą na utratę łączności.

---

## Wymagania funkcjonalne

Aplikacja powinna:

1. Wczytać z pliku konfiguracyjnego:
   - listę punktów komunikacyjnych,
   - maksymalny zasięg komunikacji,
   - opcjonalne progi kolorów.

2. Obsługiwać:
   - minimum **2 punkty**,
   - maksimum **10 punktów**.

3. Subskrybować:
   - `NAV_X`
   - `NAV_Y`

4. Dla każdego punktu:
   - obliczyć odległość od pojazdu,
   - określić, czy punkt jest w zasięgu,
   - wygenerować seglistę (jeśli w zasięgu).

5. Wizualizacja:
   - seglista widoczna tylko w zasięgu,
   - kolor zależny od odległości (100%-50% kolor zielony, 50%-25% kolor żółty, 25%-1% kolor czerwony),
   - usunięcie seglisty poza zasięgiem.

6. Logika misji:
   - jeśli pojazd **nie ma zasięgu do żadnego punktu**, aplikacja powinna:
     - opublikować informację o zakończeniu misji (np. `RETURN=true` lub odpowiedni komunikat do helm),
     - zatrzymać dalszą wizualizację.

---

## Wymagania techniczne

- aplikacja jako moduł MOOS-IVP,
- publikacja do:
  - `VIEW_SEGLIST`,
  - dodatkowo `RETURN`.

---

## Dane wejściowe

### Przykładowy plik konfiguracyjny

```moos
ProcessConfig = pCommRangeViz
{
  AppTick   = 4
  CommsTick = 4

  max_range = 20

  point = name=pt1,x=10,y=20
  point = name=pt2,x=80,y=40
  point = name=pt3,x=150,y=90
}
```

## Ograniczenia

- liczba punktów: 2–10,
- brak danych pozycji nie może powodować błędu,
- aplikacja musi działać dynamicznie,
- seglisty nie mogą pozostawać po utracie zasięgu,
- misja musi się zakończyć przy utracie wszystkich połączeń.

## Minimalny zakres oddania (ocena 3)
Rozwiązanie powinno zawierać:

- działająca aplikacja,
- min. 2 punkty,
- poprawne obliczanie odległości,
- rysowanie seglisty,
- brak seglisty poza zasięgiem (uwzględnienie max_range).
- zakończenie misji przy braku zasięgu do wszystkich punktów,


### Zakres na ocenę 4

- wszystko z 3.0 +
- obsługa 2–10 punktów,
- zmiana kolorów (min. 3 poziomy).


### Zakres na ocenę 5

- wszystko z 4.0 +
- odporność na błędne dane,
- Raportowanie o sile najmocniejszego sygnału oraz liczbie dostępnych punktów komunikacyjnych do osobnej zmiennej w MOOS_DB np. `COMM_INFO = signal_str=90%, comm_nodes=3` 


