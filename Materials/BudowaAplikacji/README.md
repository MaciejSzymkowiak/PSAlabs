# Budowa aplikacji w MOOS-IVP

Aplikacja w środowisku **MOOS-IVP** jest programem komunikującym się z innymi komponentami systemu poprzez **MOOSDB**.  
Każda aplikacja może:

- odbierać wiadomości z bazy danych MOOS (`subscribe`)
- publikować nowe wiadomości (`publish`)
- wykonywać obliczenia w pętli działania programu

Szkielet aplikacji generowany jest zwykle za pomocą narzędzia:

```bash
GenMOOSApp_AppCasting NazwaAplikacji p "Imie Nazwisko"
```

Generator tworzy katalog zawierający podstawową strukturę aplikacji.

---

# Struktura katalogu aplikacji

Po wygenerowaniu aplikacji powstaje katalog o strukturze podobnej do:

```
src/
 └ MyApp/
     ├ CMakeLists.txt
     ├ MyApp.cpp
     ├ MyApp.h
     └ main.cpp
```

Opis poszczególnych plików:

| Plik | Opis |
|---|---|
| `main.cpp` | punkt wejścia programu |
| `MyApp.cpp` | implementacja metod aplikacji |
| `MyApp.h` | deklaracje klasy aplikacji |
| `CMakeLists.txt` | konfiguracja kompilacji |

---

# Główna klasa aplikacji

Każda aplikacja MOOS dziedziczy po klasie:

```
AppCastingMOOSApp
```

lub starszej:

```
MOOSApp
```

Najczęściej stosowany schemat:

```cpp
class MyApp : public AppCastingMOOSApp
{
public:
   MyApp();
   ~MyApp();

protected:

   bool OnNewMail(MOOSMSG_LIST &NewMail);
   bool Iterate();
   bool OnConnectToServer();
   bool OnStartUp();

   void registerVariables();
};
```

---

# Najważniejsze metody aplikacji

## `OnStartUp()`

Wywoływana podczas uruchamiania aplikacji.  
Służy do:

- wczytywania parametrów z pliku `.moos`
- inicjalizacji zmiennych
- konfiguracji aplikacji

Przykład:

```cpp
bool MyApp::OnStartUp()
{
   registerVariables();
   return(true);
}
```

---

## `OnConnectToServer()`

Wywoływana po połączeniu z **MOOSDB**.

Najczęściej wykorzystywana do:

- rejestracji zmiennych (`Register`)

```cpp
bool MyApp::OnConnectToServer()
{
   registerVariables();
   return(true);
}
```

---

## `registerVariables()`

Metoda pomocnicza służąca do rejestracji zmiennych z MOOSDB.

```cpp
void MyApp::registerVariables()
{
   Register("NAV_X", 0);
   Register("NAV_Y", 0);
   Register("NAV_SPEED", 0);
}
```

Dzięki temu aplikacja otrzymuje wiadomości z MOOSDB.

---

## `OnNewMail()`

Wywoływana gdy aplikacja otrzyma nowe dane z MOOSDB.

Typowe zastosowania:

- odczyt danych nawigacyjnych
- aktualizacja stanu aplikacji
- zapisywanie wartości zmiennych

Przykład:

```cpp
bool MyApp::OnNewMail(MOOSMSG_LIST &NewMail)
{
   for(auto &msg : NewMail)
   {
      std::string key = msg.GetKey();

      if(key == "NAV_X")
         curr_x = msg.GetDouble();

      if(key == "NAV_Y")
         curr_y = msg.GetDouble();
   }

   return(true);
}
```

---

## `Iterate()`

Metoda wykonywana cyklicznie zgodnie z parametrem `AppTick`.

Służy do:

- wykonywania obliczeń
- publikowania nowych danych
- realizacji logiki aplikacji

Przykład:

```cpp
bool MyApp::Iterate()
{
   double dist = sqrt(curr_x*curr_x + curr_y*curr_y);
   Notify("DIST", dist);

   return(true);
}
```

---

# Publikowanie zmiennych

Aplikacje MOOS publikują dane do bazy za pomocą funkcji:

```cpp
Notify("ZMIENNA", wartosc);
```

Przykład:

```cpp
Notify("DIST", total_distance);
```

Opublikowana zmienna może być później:

- odczytana przez inne aplikacje
- wyświetlona w `pMarineViewer`
- zapisana przez `pLogger`

---

# Plik konfiguracyjny `.moos`

Aby aplikacja została uruchomiona w misji, musi być dodana do pliku `.moos`.

Przykład konfiguracji:

```
ProcessConfig = MyApp
{
  AppTick   = 4
  CommsTick = 4
}
```

Opis parametrów:

| Parametr | Znaczenie |
|---|---|
| `AppTick` | częstotliwość wywoływania `Iterate()` |
| `CommsTick` | częstotliwość komunikacji z MOOSDB |

---

# Cykl działania aplikacji

Schemat działania aplikacji MOOS:

```
Start aplikacji
       ↓
OnStartUp()
       ↓
OnConnectToServer()
       ↓
Rejestracja zmiennych
       ↓
Odbiór danych → OnNewMail()
       ↓
Obliczenia → Iterate()
       ↓
Publikacja danych → Notify()
       ↓
MOOSDB → inne aplikacje
```

---

# Typowy przepływ danych

Przykład dla aplikacji liczącej przebyty dystans:

1. `NAV_X` i `NAV_Y` publikowane przez `uSimMarine`
2. aplikacja odbiera dane w `OnNewMail()`
3. oblicza dystans w `Iterate()`
4. publikuje wynik jako `DIST`
5. `pMarineViewer` wyświetla wartość w **MOOS Scope**

---

# Najważniejsze elementy aplikacji

| Element | Funkcja |
|---|---|
| `OnStartUp()` | inicjalizacja aplikacji |
| `OnConnectToServer()` | połączenie z MOOSDB |
| `OnNewMail()` | odbiór wiadomości |
| `Iterate()` | logika programu |
| `Notify()` | publikowanie danych |
| `Register()` | subskrypcja zmiennych |

---

# Najczęstsze błędy

- brak rejestracji zmiennych (`Register`)
- literówki w nazwach zmiennych MOOS
- brak dodania aplikacji do pliku `.moos`
- brak kompilacji po zmianach w kodzie
- publikowanie zmiennych bez wcześniejszej inicjalizacji