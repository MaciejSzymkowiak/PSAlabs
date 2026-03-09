# Generowanie czystej aplikacji w MOOS-IVP – instrukcja krok po kroku

Poniższa instrukcja pokazuje jak wygenerować nową aplikację w środowisku **MOOS-IVP**, skompilować ją oraz sprawdzić jej działanie w przykładowej misji.

---

# 1. Przejście do katalogu `src`

Najpierw przejdź do katalogu `src` w repozytorium `moos-ivp-extend`.

```bash
cd ~/Pulpit/moos-ivp-extend/src
```

---

# 2. Wygenerowanie nowej aplikacji

Do wygenerowania szkieletu aplikacji użyj narzędzia **GenMOOSApp_AppCasting**.

```bash
GenMOOSApp_AppCasting NazwaAplikacji p "Imie Nazwisko"
```

### Przykład

```bash
GenMOOSApp_AppCasting Zad1 p "Jan Kowalski"
```

Po wykonaniu polecenia w katalogu `src` zostanie utworzony folder zawierający nową aplikację.

---

# 3. Dodanie aplikacji do systemu budowania

Przejdź do katalogu głównego projektu `moos-ivp-extend`.

```bash
cd ~/Pulpit/moos-ivp-extend
```

Otwórz plik `CMakeLists.txt`.

```bash
nano CMakeLists.txt
```

Dodaj podkatalog zawierający nową aplikację.

```cmake
ADD_SUBDIRECTORY(src/Zad1)
```

Zapisz plik i zamknij edytor.

---

# 4. Kompilacja aplikacji

W katalogu `moos-ivp-extend` uruchom skrypt budowania.

```bash
./build.sh
```

Po poprawnej kompilacji plik wykonywalny aplikacji zostanie utworzony w katalogu:

```
moos-ivp-extend/bin
```

---

# 5. Skopiowanie przykładowej misji

Przejdź do katalogu `missions`.

```bash
cd ~/Pulpit/moos-ivp-extend/missions
```

Skopiuj przykładową misję z repozytorium `moos-ivp`.

```bash
cp -r ~/Pulpit/moos-ivp/ivp/missions/s1_alpha .
```

---

# 6. Dodanie aplikacji do misji

Przejdź do katalogu misji.

```bash
cd s1_alpha
```

Otwórz plik `alpha.moos`.

```bash
nano alpha.moos
```

Dodaj konfigurację swojej aplikacji.

```moos
ProcessConfig = Zad1
{
  AppTick   = 4
  CommsTick = 4
}
```

---

# 7. Dodanie zmiennej do podglądu w pMarineViewer

W pliku `.moos` znajdź konfigurację **pMarineViewer** i dodaj zmienną do obserwacji.

```moos
scope = DIST
```

Dzięki temu zmienna będzie widoczna w narzędziu **MOOS Scope**.

---

# 8. Uruchomienie misji

W katalogu misji uruchom:

```bash
pAntler alpha.moos
```

Powinno uruchomić się okno **pMarineViewer** oraz wszystkie aplikacje misji.

---

# 9. Sprawdzenie działania aplikacji

Aby sprawdzić działanie aplikacji:

1. Otwórz **pMarineViewer**
2. Wybierz z menu **MOOS Scope**
3. Wybierz zmienną publikowaną przez aplikację (np. `DIST`)
4. Obserwuj zmieniającą się wartość zmiennej

Naciśnij przycisk **Deploy**, aby rozpocząć ruch pojazdu.

---

# 10. Zatrzymanie misji

Aby zakończyć działanie misji użyj w terminalu skrótu:

```bash
CTRL + C
```

---

# 11. Czyszczenie logów

Po zakończeniu pracy wyczyść logi i pliki tymczasowe.

```bash
./clean.sh
```

---

# Najczęstsze błędy

## 1. Aplikacja nie kompiluje się

Najczęstsze przyczyny:

- aplikacja **nie została dodana do `CMakeLists.txt`**
- literówka w nazwie katalogu aplikacji
- brak ponownej kompilacji po zmianach

Rozwiązanie:

```bash
cd ~/Pulpit/moos-ivp-extend
./build.sh
```

---

## 2. Aplikacja nie uruchamia się podczas misji

Najczęstszy powód:

- aplikacja **nie została dodana do pliku `.moos`**

Sprawdź czy w pliku `alpha.moos` znajduje się sekcja:

```moos
ProcessConfig = Zad1
{
  AppTick   = 4
  CommsTick = 4
}
```

---

## 3. Zmienna nie pojawia się w MOOS Scope

Przyczyny:

- aplikacja **nie publikuje zmiennej**
- zmienna **nie została dodana do `scope` w pMarineViewer**
- literówka w nazwie zmiennej

Przykład poprawnej konfiguracji:

```moos
scope = DIST
```

---

## 4. Misja uruchamia się, ale pojazd się nie porusza

Najczęściej zapomniano kliknąć przycisk:

```
Deploy
```

w oknie **pMarineViewer**.

---

## 5. Brak pliku wykonywalnego aplikacji

Sprawdź czy plik znajduje się w katalogu:

```
moos-ivp-extend/bin
```

Jeśli go nie ma, wykonaj ponownie kompilację:

```bash
./build.sh
```

---

# Minimalna lista kontrolna

- [ ] wygenerowano aplikację `GenMOOSApp_AppCasting`
- [ ] dodano aplikację do `CMakeLists.txt`
- [ ] wykonano `./build.sh`
- [ ] dodano aplikację do pliku `.moos`
- [ ] dodano zmienną do `scope`
- [ ] uruchomiono misję `pAntler`
- [ ] sprawdzono działanie w **MOOS Scope**