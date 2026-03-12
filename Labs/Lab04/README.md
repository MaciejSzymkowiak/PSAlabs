# Zadanie: Dynamiczne rysowanie `XYPolygon` w `pMarineViewer`

## Cel zadania
Napisz aplikację MOOS, której zadaniem będzie:

1. odbieranie z `MOOSDB` wiadomości o nazwie `DEF_POLY`,
2. dekodowanie treści tej wiadomości z użyciem funkcji `tokStringParse()`,
3. tworzenie oraz publikowanie obiektu `XYPolygon` do wizualizacji w `pMarineViewer`,
4. zmiana koloru obwodu poligonu w zależności od położenia pojazdu.

---

## Treść zadania

Należy stworzyć aplikację, która subskrybuje zmienną:

`DEF_POLY`

Wartość tej zmiennej będzie miała format:

`"w=10,h=20,center=(10,10)"`

gdzie:

- `w` — szerokość prostokąta,
- `h` — wysokość prostokąta,
- `center=(x,y)` — współrzędne środka figury.

Na podstawie odebranego komunikatu aplikacja ma utworzyć prostokątny `XYPolygon` i opublikować go tak, aby był widoczny w `pMarineViewer`.

---

## Wymagania funkcjonalne

### 1. Odbiór danych
Aplikacja musi odbierać z `MOOSDB` zmienną `DEF_POLY`.

### 2. Dekodowanie wiadomości
Do odczytu parametrów komunikatu student **musi użyć funkcji**:

`tokStringParse()`

Należy zdekodować z komunikatu co najmniej:

- szerokość `w`,
- wysokość `h`,
- współrzędną `x` środka,
- współrzędną `y` środka.

### 3. Budowa poligonu
Na podstawie parametrów `w`, `h` oraz `center=(x,y)` należy wyznaczyć cztery wierzchołki prostokąta i utworzyć obiekt `XYPolygon`.

### 4. Wizualizacja w `pMarineViewer`
Utworzony poligon ma być publikowany do `pMarineViewer` w sposób umożliwiający jego obserwację podczas działania misji.

### 5. Zmiana koloru obwodu
Podczas wykonywania misji należy monitorować aktualne położenie pojazdu.

- jeżeli pojazd **znajduje się wewnątrz** wyrysowanego poligonu, obwód poligonu ma mieć kolor **zielony**,
- w przeciwnym wypadku obwód poligonu ma mieć kolor **czerwony**.

---

## Dane wejściowe

Przykładowa wiadomość:

`DEF_POLY = "w=10,h=20,center=(10,10)"`

Interpretacja:

- szerokość prostokąta: `10`,
- wysokość prostokąta: `20`,
- środek prostokąta: `(10,10)`.

---

## Oczekiwane działanie

Dla wiadomości:

`"w=10,h=20,center=(10,10)"`

aplikacja powinna utworzyć prostokąt o środku w punkcie `(10,10)`.

Jeżeli aktualna pozycja pojazdu znajdzie się wewnątrz tego prostokąta, w `pMarineViewer` jego obwód powinien zmienić kolor na zielony. W przeciwnym razie obwód powinien być czerwony.

---

## Wymagania techniczne

Rozwiązanie powinno:

- być napisane w języku C++,
- działać jako aplikacja MOOS,
- korzystać z `tokStringParse()` do dekodowania komunikatu,
- wykorzystywać `XYPolygon`,
- publikować wynik do wizualizacji w `pMarineViewer`,
- reagować na bieżące położenie pojazdu.

---

## Podpowiedzi

- Z komunikatu należy odczytać parametry tekstowe i przekonwertować je do wartości liczbowych.
- Wierzchołki prostokąta można wyznaczyć na podstawie połowy szerokości i połowy wysokości.
- Do sprawdzenia, czy pojazd znajduje się wewnątrz obszaru, należy wykorzystać odpowiednią metodę klasy `XYPolygon`.
- Należy zadbać o regularną aktualizację koloru poligonu w trakcie trwania misji.

---

## Kryteria oceny

### Ocena dostateczna
- aplikacja odbiera zmienną `DEF_POLY`,
- poprawnie dekoduje komunikat z użyciem `tokStringParse()`,
- rysuje prostokąt w `pMarineViewer`.

### Ocena dobra
- aplikacja poprawnie wykrywa, czy pojazd znajduje się wewnątrz poligonu,
- zmienia kolor obwodu zgodnie z warunkiem zadania.

### Ocena bardzo dobra
- rozwiązanie jest czytelne, poprawnie zorganizowane,
- obsługuje błędne lub niepełne dane wejściowe,
- działa stabilnie podczas całej misji.

---

## Uwagi
- Format wejściowy komunikatu należy traktować jako obowiązujący:
  `"w=10,h=20,center=(10,10)"`
- Do dekodowania należy obowiązkowo użyć `tokStringParse()`.
- Kolor zmienia się wyłącznie dla **obwodu** poligonu.