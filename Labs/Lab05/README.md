# Laboratorium — MOPS / IVP  
## Temat: Dynamiczna wizualizacja sonaru i detekcja punktów na trasie

### Cel laboratorium
Celem laboratorium jest przygotowanie aplikacji MOOS/IVP symulującej prosty sonar pojazdu autonomicznego.
Zadania aplikacji:

- wczytać parametry sonaru z pliku konfiguracyjnego,
- zwizualizować obszar działania sonaru zależnie od `heading` pojazdu,
- rozrzucać punkty w przestrzeni za pomocą komunikatu `DEF_POINTS`,
- sprawdzać, które punkty znajdują się w zasięgu sonaru,
- publikować do `MOOS_DB` informację o wykrytych obiektach,
- umożliwić dynamiczną zmianę parametrów sonaru przez zmienną `SONAR_CONFIG_UPDATE`.

---

## Zakres zadania

Należy zaimplementować aplikację MOOS, która będzie pracowała w środowisku MOPS/IVP i realizowała opisane niżej funkcjonalności.

---

## Funkcjonalność wymagana

### 1. Wczytywanie parametrów sonaru z pliku konfiguracyjnego
Aplikacja ma wczytywać z pliku `.moos` dwie zmienne konfiguracyjne:

- `sonar_angle` — kąt widzenia sonaru w stopniach,
- `sonar_range` — maksymalny zasięg sonaru w metrach.

#### Wartości początkowe
- `sonar_angle = 90`
- `sonar_range = 20`

Jeżeli parametry nie zostaną podane w pliku konfiguracyjnym, aplikacja powinna użyć wartości domyślnych.

---

### 2. Wizualizacja sonaru
Należy przygotować wizualizację obszaru sonaru zależnie od aktualnego kursu pojazdu (`heading`).

#### Założenia:
- sonar ma być przedstawiony jako wycinek koła,
- środek sonaru znajduje się w aktualnej pozycji pojazdu (`NAV_X`, `NAV_Y`),
- orientacja sonaru zależy od `NAV_HEADING`,
- szerokość wycinka zależy od `sonar_angle`,
- promień wycinka zależy od `sonar_range`.

#### Sugestia:
Do wizualizacji można użyć publikacji odpowiednich komunikatów do `VIEW_POLYGON` lub innego mechanizmu wizualizacji dostępnego w pMarineViewer.

---

### 3. Definiowanie punktów na trasie
W trakcie wykonywania misji użytkownik powinien móc przekazywać punkty do aplikacji za pomocą zmiennej:

- `DEF_POINTS`

Punkty te mają zostać:
- zapisane w aplikacji,
- zwizualizowane na mapie,
- sprawdzane pod kątem przynależności do obszaru sonaru.

#### Proponowany format komunikatu `DEF_POINTS`
Naley przyjąć format komunikatu z poprzednich laboratoriów.


Dopuszcza się własny format, ale musi być czytelny i opisany w dokumentacji rozwiązania.

---

### 4. Detekcja punktów w obszarze sonaru
Aplikacja ma cyklicznie sprawdzać, które z zadanych punktów znajdują się aktualnie w obszarze sonaru.

Punkt uznajemy za wykryty, jeżeli jednocześnie:
- jego odległość od pojazdu jest nie większa niż `sonar_range`,
- znajduje się w obrębie kąta widzenia sonaru względem aktualnego `heading`.


---

### 5. Publikacja informacji do MOOS_DB
Jeżeli co najmniej jeden punkt znajduje się w obrębie sonaru, aplikacja ma opublikować zmienną:

- `SONAR_INFO`

Zmienna powinna zawierać:
- liczbę wykrytych obiektów,
- odległość do najbliższego obiektu.

#### Przykładowy format
`count=3,nearest=4.72`

Jeżeli nie wykryto żadnych obiektów, można przyjąć jeden z dwóch wariantów:
- publikować `count=0,nearest=-1`
- nie publikować nic nowego

Wybrany wariant należy opisać i stosować konsekwentnie.

---

### 6. Dynamiczna zmiana konfiguracji sonaru
Parametry sonaru mają dać się zmieniać w trakcie działania aplikacji za pomocą zmiennej:

- `SONAR_CONFIG_UPDATE`

Komunikat ma zawierać te same informacje, co konfiguracja z pliku, czyli:
- `sonar_angle`
- `sonar_range`

#### Przykładowy format
`sonar_angle=60,sonar_range=12`

Po odebraniu komunikatu aplikacja powinna:
- sparsować nowe wartości,
- zaktualizować parametry sonaru,
- odświeżyć wizualizację,
- uwzględnić nowe parametry przy kolejnych detekcjach.

---

## Wymagane subskrypcje i publikacje

### Aplikacja powinna subskrybować:
- `NAV_X`
- `NAV_Y`
- `NAV_HEADING`
- `DEF_POINTS`
- `SONAR_CONFIG_UPDATE`

### Aplikacja powinna publikować:
- `VIEW_POLYGON` lub inny komunikat wizualizacyjny do obszaru sonaru
- komunikaty wizualizujące punkty `VIEW_POINT`
- `SONAR_INFO`

---

## Szczegóły implementacyjne

### Dane wejściowe
Aplikacja powinna korzystać z:
- bieżącej pozycji pojazdu,
- bieżącego kursu pojazdu,
- konfiguracji początkowej z pliku `.moos`,
- wiadomości przychodzących z `DEF_POINTS`,
- wiadomości przychodzących z `SONAR_CONFIG_UPDATE`.

### Dane przechowywane w aplikacji
Przykładowo:
- aktualny `x`, `y`,
- aktualny `heading`,
- aktualny `sonar_angle`,
- aktualny `sonar_range`,
- lista zdefiniowanych punktów.

### Obliczenia geometryczne
Dla każdego punktu należy policzyć:
1. odległość od pojazdu,
2. kąt kierunku od pojazdu do punktu,
3. różnicę pomiędzy tym kątem a `heading`,
4. sprawdzić, czy punkt mieści się w:
   - promieniu sonaru,
   - połowie kąta widzenia po lewej i prawej stronie osi sonaru.
   - lub wykorzystać metodę contains() zawartą w klasie XYPoligon()

---

## Minimalny zakres oddania (ocena 3)
Rozwiązanie powinno zawierać:

- działającą aplikację MOOS,
- wczytywanie parametrów z pliku konfiguracyjnego,
- wizualizację sonaru,
- odbiór i wizualizację punktów,
- detekcję punktów w obszarze sonaru,
- publikację `SONAR_INFO`,
- obsługę `SONAR_CONFIG_UPDATE`.

---

### Zakres na ocenę 4

- obsługa usuwania punktów,
- nadawanie punktom identyfikatorów,
- rozróżnienie punktów wykrytych i niewykrytych kolorami,

### Zakres na ocenę 5

- wizualizacja najbliższego wykrytego punktu w inny sposób,
- walidacja błędnych komunikatów wejściowych,
- logowanie zmian konfiguracji sonaru,
- publikacja dodatkowej zmiennej, np. listy wykrytych identyfikatorów.

---

## Przykładowa konfiguracja w pliku `.moos`

```ini
ProcessConfig = pSonarSim
{
  AppTick   = 10
  CommsTick = 10

  sonar_angle = 90
  sonar_range = 20
}