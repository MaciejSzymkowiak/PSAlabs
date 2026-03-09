# Biblioteka `geometry` w MOOS-IvP – skrótowy opis najważniejszych klas i funkcji

Biblioteka `geometry` w MOOS-IvP służy do reprezentowania obiektów geometrycznych używanych przez aplikacje MOOS, zachowania IvP Helm oraz narzędzia wizualizacyjne takie jak `pMarineViewer`. Najczęściej używane klasy to:

- `XYObject`
- `XYPoint`
- `XYSegList`
- `XYPolygon`
- `XYVector`
- `XYSeglr`

---

# 1. `XYObject`

Klasa bazowa dla obiektów geometrycznych. Przechowuje wspólne właściwości opisowe i wizualne, takie jak:

- `label` – etykieta obiektu
- `msg` – tekst wyświetlany zamiast etykiety
- `type` – typ obiektu
- `source` – źródło obiektu
- `time` – znacznik czasu
- `active` – czy obiekt ma być renderowany
- `vertex_size` – rozmiar wierzchołków
- `edge_size` – grubość krawędzi
- `vertex_color` – kolor wierzchołków
- `edge_color` – kolor krawędzi
- `label_color` – kolor etykiety

## Najczęściej używane metody

- `set_label(...)` – ustawia etykietę obiektu
- `set_color(...)` – ustawia kolor elementu obiektu
- `set_param(...)` – ustawia parametr dodatkowy
- `get_spec()` – zwraca tekstową reprezentację obiektu

---

# 2. `XYPoint`

Klasa reprezentująca pojedynczy punkt w przestrzeni 2D. Jest używana np. do oznaczania waypointów, punktów celu lub pozycji obiektów na mapie. `pMarineViewer` może wyświetlać takie punkty po opublikowaniu ich np. do `VIEW_POINT`.

## Typowe zastosowania

- oznaczanie pozycji
- publikowanie punktów do wizualizacji
- reprezentowanie pojedynczego waypointa

## Najważniejsze operacje

- utworzenie punktu na podstawie współrzędnych `x`, `y`
- ustawienie etykiety
- ustawienie koloru i rozmiaru punktu
- serializacja przez `get_spec()`

## Przykład

```cpp
XYPoint pt(x, y);
pt.set_label("P1");
pt.set_color("vertex", "yellow");
pt.set_param("vertex_size", "4");
string spec = pt.get_spec();
Notify("VIEW_POINT", spec);
```

---

# 3. `XYSegList`

Klasa reprezentująca listę punktów połączonych odcinkami. Jest bardzo często używana do opisu tras, ścieżek, waypointów i przebiegu pojazdu.

## Typowe zastosowania

- lista waypointów
- przebieg trasy
- rysowanie łamanej w `pMarineViewer`

## Najważniejsze operacje

- dodawanie kolejnych wierzchołków
- budowanie ścieżki z wielu punktów
- eksport do postaci tekstowej przez `get_spec()`

## Przydatna intuicja

Jeżeli chcesz pokazać trasę pojazdu albo sekwencję punktów do odwiedzenia, to zwykle używasz właśnie `XYSegList`.

---

# 4. `XYPolygon`

Klasa reprezentująca wielokąt, najczęściej wypukły. W dokumentacji MOOS-IvP szczególny nacisk położony jest właśnie na obsługę polygonów wypukłych. Klasa ta jest używana m.in. do opisu obszarów działania, stref bezpieczeństwa, regionów patrolu lub przeszkód. :contentReference[oaicite:1]{index=1}

## Typowe zastosowania

- obszary operacyjne
- strefy zabronione
- obszary patrolu
- geometria przeszkód

## Najważniejsze operacje

- dodawanie wierzchołków wielokąta
- sprawdzanie i utrzymywanie poprawności wielokąta
- wizualizacja przez `VIEW_POLYGON`
- serializacja przez `get_spec()`

## Kiedy używać

Gdy chcesz opisać **obszar**, a nie pojedynczy punkt lub trasę.

---

# 5. `XYVector`

Klasa reprezentująca wektor w 2D, zwykle zadany przez:

- punkt początkowy
- kąt
- długość

Może być wykorzystywana np. do pokazywania kierunku ruchu, wiatru, siły lub zadanej orientacji. W dokumentacji geometry pokazano, że wektor może być serializowany z polami takimi jak `x`, `y`, `ang`, `mag` oraz dodatkowymi hintami graficznymi. :contentReference[oaicite:2]{index=2}

## Typowe zastosowania

- kierunek ruchu
- wektory sterowania
- wizualizacja orientacji lub oddziaływania sił

## Najważniejsze operacje

- ustawienie początku wektora
- ustawienie kąta i długości
- ustawienie rozmiaru grotu i koloru
- eksport do tekstu przez `get_spec()`

---

# 6. `XYSeglr`

Klasa reprezentująca strukturę złożoną z łamanej zakończonej promieniem. W dokumentacji MOOS-IvP opisano ją jako listę odcinków, po której następuje ray. Jest używana rzadziej niż `XYPoint`, `XYSegList` czy `XYPolygon`, ale przydaje się do opisu manewrów i przewidywanego kierunku dalszego ruchu. :contentReference[oaicite:3]{index=3}

## Typowe zastosowania

- wizualizacja manewru
- przewidywany dalszy kierunek ruchu
- trajektorie z częścią „otwartą”

---

# 7. Najważniejsze funkcje / metody, które warto znać

Poniżej zestaw metod, które pojawiają się najczęściej w praktyce:

| Metoda | Znaczenie |
|---|---|
| `set_label(...)` | ustawia etykietę obiektu |
| `set_color(...)` | ustawia kolor wskazanego elementu |
| `set_param(...)` | ustawia parametry dodatkowe, np. `vertex_size` |
| `get_spec()` | zwraca tekstowy opis obiektu do publikacji w MOOS |
| `add_vertex(...)` | dodaje punkt do `XYSegList` lub `XYPolygon` |

> Uwaga: dokładny zestaw metod zależy od konkretnej klasy, ale te operacje są najczęściej spotykane podczas pracy laboratoryjnej i wizualizacji.

---

# 8. Wizualizacja w `pMarineViewer`

Obiekty geometryczne są często publikowane do specjalnych zmiennych MOOS, które `pMarineViewer` potrafi narysować. Dokumentacja geometry opisuje w szczególności zmienne:

- `VIEW_POINT`
- `VIEW_SEGLIST`
- `VIEW_POLYGON`
- `VIEW_VECTOR`

Dzięki temu można łatwo wizualizować punkty, trasy, wielokąty i wektory w trakcie działania misji. :contentReference[oaicite:4]{index=4}

---

# 9. Linkowanie biblioteki `geometry`

Aby używać tych klas w swojej aplikacji, trzeba dodać bibliotekę `geometry` do sekcji `TARGET_LINK_LIBRARIES(...)` w `CMakeLists.txt`. W materiałach MOOS-IvP zwrócono uwagę, że na Linuksie kolejność bibliotek ma znaczenie i `geometry` powinno występować przed `mbutil`. :contentReference[oaicite:5]{index=5}

## Przykład

```cmake
TARGET_LINK_LIBRARIES(MyApp
   ${MOOS_LIBRARIES}
   geometry
   mbutil
   m
   pthread)
```

---

# 10. Co warto zapamiętać

## Najważniejsze klasy

- `XYPoint` – pojedynczy punkt
- `XYSegList` – lista punktów połączonych odcinkami
- `XYPolygon` – wielokąt / obszar
- `XYVector` – wektor
- `XYSeglr` – łamana zakończona promieniem
- `XYObject` – wspólna klasa bazowa

## Najważniejsze metody

- `set_label(...)`
- `set_color(...)`
- `set_param(...)`
- `get_spec()`
- `add_vertex(...)`

## Najczęstszy schemat użycia

1. tworzysz obiekt geometryczny
2. ustawiasz jego parametry wizualne
3. wywołujesz `get_spec()`
4. publikujesz wynik przez `Notify(...)`
5. oglądasz obiekt w `pMarineViewer`