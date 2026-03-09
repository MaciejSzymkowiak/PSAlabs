# Diagram architektury MOOS-IVP

Poniższy diagram przedstawia uproszczoną architekturę środowiska **MOOS-IVP**.  
Centralnym elementem systemu jest **MOOSDB**, która pośredniczy w komunikacji pomiędzy wszystkimi aplikacjami.

```mermaid
flowchart TD
    subgraph Sensors["Warstwa danych wejściowych"]
        GPS["GPS / Navigation Sensor"]
        IMU["IMU / Compass"]
        SIM["uSimMarine / Simulator"]
    end

    subgraph Core["Rdzeń komunikacyjny"]
        DB["MOOSDB"]
    end

    subgraph Apps["Aplikacje MOOS"]
        NAV["pNav / moduły nawigacyjne"]
        LOGGER["pLogger"]
        VIEW["pMarineViewer"]
        CUSTOM["Własna aplikacja<br/>np. Zad1 / pDistance"]
    end

    subgraph Decision["Warstwa decyzyjna IVP"]
        HELM["pHelmIvP"]
        BHV["Behaviors<br/>np. waypoint, station-keep, avoid obstacle"]
    end

    subgraph Control["Warstwa sterowania"]
        CMD["pMarinePID / kontroler"]
        ACT["Napęd / ster / model pojazdu"]
    end

    GPS --> DB
    IMU --> DB
    SIM --> DB

    DB <--> NAV
    DB <--> LOGGER
    DB <--> VIEW
    DB <--> CUSTOM

    DB <--> HELM
    HELM <--> BHV

    HELM --> DB
    DB --> CMD
    CMD --> ACT
    ACT --> SIM

    CUSTOM -->|"publikuje np. DIST"| DB
    DB -->|"NAV_X, NAV_Y, NAV_SPEED"| CUSTOM
    DB -->|"DESIRED_HEADING, DESIRED_SPEED"| VIEW
```

## Opis elementów

- **MOOSDB** – centralna baza wiadomości, przez którą komunikują się wszystkie aplikacje.
- **uSimMarine** – symulator pojazdu, publikujący dane nawigacyjne i odbierający komendy sterujące.
- **pHelmIvP** – moduł decyzyjny wybierający zachowanie pojazdu na podstawie aktywnych behaviorów.
- **Behaviors** – reguły sterowania, np. podążanie po waypointach lub utrzymywanie pozycji.
- **pMarinePID** – kontroler zamieniający komendy zadane na sygnały sterujące.
- **pMarineViewer** – narzędzie do wizualizacji stanu misji i podglądu zmiennych z MOOSDB.
- **pLogger** – zapisuje przebieg misji do logów.
- **Własna aplikacja** – program tworzony przez studenta, który może subskrybować i publikować własne zmienne.

## Typowy przepływ danych

1. Symulator lub sensory publikują dane, np. `NAV_X`, `NAV_Y`, `NAV_HEADING`.
2. Dane trafiają do **MOOSDB**.
3. **pHelmIvP** odczytuje dane i wyznacza komendy zadane, np. `DESIRED_SPEED`, `DESIRED_HEADING`.
4. Komendy trafiają przez **MOOSDB** do kontrolera.
5. Kontroler steruje ruchem pojazdu.
6. Własne aplikacje mogą równolegle analizować dane i publikować nowe zmienne.
7. **pMarineViewer** oraz **pLogger** obserwują przebieg misji.

## Przykład dla aplikacji studenckiej

Aplikacja licząca przebyty dystans może działać według schematu:

- odbiera `NAV_X`
- odbiera `NAV_Y`
- oblicza odległość między kolejnymi pozycjami
- publikuje wynik do zmiennej `DIST`

Wtedy zmienna `DIST` może być obserwowana w **pMarineViewer** przez **MOOS Scope**.
