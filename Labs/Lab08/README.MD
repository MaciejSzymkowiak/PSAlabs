# Zadanie: Koordynowany loiter dwóch pojazdów 

## 📌 Cel zadania

Celem zadania jest rozszerzenie istniejącej misji (z poprzednich zajęć) dla scenariusza `s1_alpha` tak, aby dwa pojazdy autonomiczne (**A** i **B**) potrafiły:

1. odebrać punkt docelowy `(X,Y)`,
2. dotrzeć do tego punktu,
3. rozpocząć skoordynowane zachowanie **loiter** wokół tego punktu,
4. przydzielić role w zależności od tego, który pojazd dotrze pierwszy.

To zadanie sprawdza połączenie:
- komunikacji w systemie MOOS,
- logiki zdarzeń,
- współpracy wielu pojazdów,
- dynamicznego przełączania behaviorów w IvP.

---

## 🧠 Opis działania systemu

Po ustawieniu punktu docelowego `(X,Y)` oba pojazdy mają rozpocząć ruch do tego punktu.

Następnie system ma wykryć, **który pojazd osiągnął cel jako pierwszy**. Na tej podstawie należy przydzielić role dla obu pojazdów.

### Zasada ogólna

- **Pojazd, który dotrze pierwszy:**
  - wykonuje **loiter po okręgu wewnętrznym**,
  - porusza się **zgodnie z ruchem wskazówek zegara (CW)**.

- **Pojazd, który dotrze jako drugi:**
  - wykonuje **loiter po okręgu zewnętrznym**,
  - porusza się **przeciwnie do ruchu wskazówek zegara (CCW)**.

### Przykład

#### Gdy pierwszy dotrze pojazd A:
- **A** → okrąg wewnętrzny, **CW**
- **B** → okrąg zewnętrzny, **CCW**

#### Gdy pierwszy dotrze pojazd B:
- **B** → okrąg wewnętrzny, **CW**
- **A** → okrąg zewnętrzny, **CCW**

Innymi słowy:

> pierwszy pojazd na miejscu zawsze dostaje okrąg wewnętrzny i ruch zgodny z ruchem wskazówek zegara, a drugi pojazd zawsze dostaje okrąg zewnętrzny i ruch przeciwny do ruchu wskazówek zegara.

---

## ⚙️ Parametry obowiązkowe

W zadaniu należy użyć dokładnie następujących parametrów:

- **promień uznania osiągnięcia celu:**
  - `R_arrival = 5 m`

- **promień loiter wewnętrznego:**
  - `R_inner = 15 m`

- **promień loiter zewnętrznego:**
  - `R_outer = 30 m`

- **środek loiter dla obu pojazdów:**
  - punkt `(X,Y)` wskazany przez operatora / wpisany do bazy MOOS

---

## 📋 Wymagania funkcjonalne

Rozwiązanie musi:

1. pobierać punkt `(X,Y)` z systemu MOOS,
2. skierować oba pojazdy do tego punktu,
3. wykryć moment osiągnięcia celu przez każdy pojazd,
4. ustalić, który pojazd był pierwszy,
5. przydzielić role:
   - wewnętrzny / zewnętrzny,
   - CW / CCW,
6. uruchomić odpowiednie zachowania `loiter` dla obu pojazdów,
7. zapewnić, że:
   - decyzja o rolach zapada tylko raz dla danego celu,
   - oba pojazdy używają tego samego środka `(X,Y)`.

---

## ❗ Definicja „osiągnięcia celu”

Pojazd uznajemy za będący „na miejscu”, jeśli jego odległość od punktu docelowego jest nie większa niż:

```text
distance(vehicle_position, (X,Y)) <= 5 m
```

Można to liczyć na podstawie bieżącej pozycji pojazdu, np. z użyciem `NAV_X` i `NAV_Y`.

Przykładowy wzór:

```text
dist = sqrt((NAV_X - X)^2 + (NAV_Y - Y)^2)
```

Jeżeli:

```text
dist <= R_arrival
```

to pojazd uznajemy za taki, który osiągnął cel.

---

## 🧩 Ściągawki / wskazówki

Poniższe wskazówki mają naprowadzić Was na sposób implementacji, ale nie stanowią gotowego rozwiązania.

Przykładowa konfiguracja zachowania loiter:
```
Behavior = BHV_Loiter
{
  name      = role_loiter
  pwt       = 100
  condition = MODE==LOITER

  updates   = LOITER_UPDATES
  speed     = 1.2
  clockwise = true

  polygon   = radial:: x=0, y=0, radius=15, pts=8, snap=1
}
```
sposób aktualizacji zachowania: 
```
LOITER_UPDATES = polygon=radial:: x=120, y=-45, radius=15, pts=8, snap=1
```



---

### 💡 2. Jak wykryć, kto był pierwszy?

Proponowane ustalenie kolejności:
- gdy pojazd A osiągnie cel, publikuje np.:
  ```text
  A_ARRIVED = true
  ```
- gdy pojazd B osiągnie cel, publikuje np.:
  ```text
  B_ARRIVED = true
  ```

Na bazie publikowanych zmiennych należy ustalić, który pojazd opublikował informację pierwszy:

```text
FIRST_ARRIVAL = A
```

lub

```text
FIRST_ARRIVAL = B
```

**Bardzo ważne:** ta decyzja powinna zostać ustawiona tylko raz dla danego celu.

---

### 💡 3. Gdzie najlepiej trzymać logikę decyzji?

Najbezpieczniej jest podejmować decyzję w **jednym miejscu**:
- w osobnej aplikacji MOOS,
- albo w jednym wybranym komponencie pełniącym rolę koordynatora.

**Nie** warto robić tego niezależnie w obu pojazdach, bo może to prowadzić do konfliktów i niespójności.

---

### 💡 4. Jak ustawić `BHV_Loiter`?

W behaviorze `BHV_Loiter` zwykle interesują Was takie parametry jak:
- środek loiter,
- promień,
- kierunek obiegu.

Przykładowo:
- `center = X,Y`
- `radius = 15` albo `30`
- kierunek zgodny / przeciwny do ruchu wskazówek zegara

Interpretacja:
- **CW** → ruch zgodny z ruchem wskazówek zegara
- **CCW** → ruch przeciwny do ruchu wskazówek zegara

---

### 💡 5. Jak przełączyć się z „go to point” na „loiter”?

Typowe podejścia:
- aktywacja behavioru warunkiem (`condition = ...`),
- zmienna trybu, np.:
  ```text
  MODE = TRANSIT
  MODE = LOITER
  ```

Możecie też uzależnić różne zachowania od stanu systemu:
- dojazd do punktu,
- oczekiwanie na przydział roli,
- loiter po przydzielonym okręgu.


aby ustawić `MODE` pojazdu należy w pliku `*.bhv` określić nazwę trybu oraz warunki jakie muszą zostać spełnione aby pojazd zaczął działać w tym trybie.
przykład:
```
set MODE = ACTIVE {
  DEPLOY = true
  RETURN = false
}
```

---

### 💡 6. Na jaki błąd trzeba uważać?

Jeżeli oba pojazdy dotrą prawie jednocześnie, może zdarzyć się, że role zostaną przypisane więcej niż raz.

Dlatego warto użyć dodatkowej flagi, np.:

```text
ROLES_ASSIGNED = true
```

Jeżeli role są już przypisane, kolejne zdarzenia przyjścia do celu nie powinny zmieniać decyzji.

---

## 🧪 Minimalne scenariusze testowe

Przed oddaniem rozwiązania należy sprawdzić co najmniej następujące przypadki:

1. **Pojazd A startuje bliżej celu**  
   Oczekiwany wynik:
   - A → wewnętrzny, CW
   - B → zewnętrzny, CCW

2. **Pojazd B startuje bliżej celu**  
   Oczekiwany wynik:
   - B → wewnętrzny, CW
   - A → zewnętrzny, CCW

3. **Różne położenia punktu `(X,Y)`**

4. **Sytuacja prawie równoczesnego dotarcia**  
   Rozwiązanie powinno działać stabilnie i nie zmieniać ról wielokrotnie.

---

## 🏁 Kryteria oceny

### Ocena 3.0
- oba pojazdy poprawnie docierają do punktu,
- oba wykonują loiter wokół wskazanego środka.

### Ocena 4.0
- system poprawnie wykrywa, który pojazd był pierwszy,
- role wewnętrzny / zewnętrzny są przydzielane zgodnie z kolejnością dotarcia.

### Ocena 5.0
- aktualizacja punktu prze `uPokeDB` w trakcie działania misji,
- wykorzystanie `MODE` zamiast czystych zmiennych z `MOOS DB` do zmiany zachowań pojazdu.




System ma reagować na nowe wartości punktu docelowego wpisywane bezpośrednio do bazy MOOS.

---



Punkt docelowy ma być ustawiany przez dwie zmienne:

```text
TARGET_X
TARGET_Y
```

---

## 💻 Przykład użycia `uPokeDB`

Przykładowe ustawienie celu z poziomu terminala:

```bash
uPokeDB targ.moos TARGET_X=120
uPokeDB targ.moos TARGET_Y=-45
```

lub:

```bash
uPokeDB targ.moos TARGET_X=120 TARGET_Y=-45
```

---

## 🔄 Wymagana reakcja systemu po zmianie celu

Po zmianie `TARGET_X` i/lub `TARGET_Y` system powinien:

1. zaktualizować punkt docelowy dla obu pojazdów,
2. zresetować stan logiki, np.:

```text
ROLES_ASSIGNED = false
FIRST_ARRIVAL = ""
A_ARRIVED = false
B_ARRIVED = false
```

3. ponownie uruchomić pełną procedurę:
   - dojazd do celu,
   - wykrycie kolejności,
   - przydział ról,
   - loiter.

---

## 🧩 Ściągawki do rozszerzenia 5.0

### 💡 1. Jak wykryć zmianę celu?

Możecie nasłuchiwać zmian wartości:
- `TARGET_X`
- `TARGET_Y`

Pomocne może być przechowywanie poprzednich wartości, np.:
- `LAST_TARGET_X`
- `LAST_TARGET_Y`

Jeżeli nowy cel różni się od poprzedniego, należy potraktować to jako rozpoczęcie nowej misji dojścia i loiter.

---

### 💡 2. Co dokładnie trzeba zresetować?

Przy zmianie celu nie wystarczy zmienić samego `(X,Y)`. Trzeba też wyczyścić stan logiki związany z poprzednim zadaniem.

Przykładowo:
- informację o tym, kto był pierwszy,
- flagę przypisania ról,
- flagi osiągnięcia celu przez oba pojazdy.

---

### 💡 3. Typowy błąd

Częsty problem:
- tylko jeden pojazd przełącza się na nowy cel,
- drugi nadal realizuje starą trajektorię lub stary loiter.

Dlatego ważne jest, aby oba pojazdy korzystały z tych samych zmiennych celu oraz z tego samego mechanizmu resetu.

---

### 💡 4. Dobra praktyka

Dobrze jest rozdzielić:
- **logikę decyzji** („kto pierwszy?”),
- od **logiki ruchu** („jak płynąć / po czym krążyć?”).

Dzięki temu łatwiej:
- resetować system,
- testować rozwiązanie,
- unikać błędów przy kolejnych zmianach punktu.

---

## 🧪 Testy wymagane dla oceny 5.0

Należy sprawdzić przynajmniej:

1. **Zmianę celu w trakcie loiter**
2. **Zmianę celu w trakcie dojazdu**
3. **Kilka kolejnych zmian celu**
4. **Przypadki, w których po zmianie celu inny pojazd dociera pierwszy niż poprzednio**

---

## ✅ Podsumowanie wymagań

### Wersja podstawowa
- oba pojazdy płyną do wskazanego punktu,
- system wykrywa, który był pierwszy,
- pierwszy pojazd krąży po okręgu wewnętrznym zgodnie z ruchem wskazówek zegara,
- drugi pojazd krąży po okręgu zewnętrznym przeciwnie do ruchu wskazówek zegara.

### Wersja na 5.0
- punkt docelowy przekazywany jest przez `uPokeDB`,
- wykorzystywane są zmienne `TARGET_X` i `TARGET_Y`,
- system poprawnie reaguje na zmianę celu,
- stan logiki jest resetowany,
- role są nadawane od nowa dla nowego punktu.


