# Programowanie Systemów Autonomicznych -- Laboratoria

Repozytorium zawiera materiały do laboratoriów z przedmiotu
**Programowanie Systemów Autonomicznych**.

## Cel przedmiotu

Celem zajęć jest zapoznanie studentów z metodami projektowania i
implementacji oprogramowania dla systemów autonomicznych.\
W trakcie laboratoriów studenci będą rozwijać praktyczne umiejętności
związane z tworzeniem modularnych systemów sterowania oraz komunikacji
między komponentami systemów robotycznych.

Głównym środowiskiem wykorzystywanym podczas zajęć jest **MOOS‑IVP
(Mission Oriented Operating Suite -- Interval Programming)**.

## Środowisko: MOOS‑IVP

MOOS‑IVP jest platformą middleware wykorzystywaną do budowy systemów
autonomicznych, szczególnie w zastosowaniach robotyki morskiej.\
Zapewnia ona:

-   architekturę komunikacji opartą na publikacji i subskrypcji,
-   modularną budowę aplikacji (MOOS Apps),
-   możliwość integracji wielu komponentów systemu autonomicznego,
-   narzędzia do symulacji i testowania algorytmów.

Podczas laboratoriów studenci będą tworzyć własne aplikacje MOOS oraz
integrować je w większe systemy.

## Struktura repozytorium

    .
    ├── labs/               # Zadania laboratoryjne
    │   ├── lab01/
    │   ├── lab02/
    │   └── ...
    │
    ├── examples/           # Przykłady aplikacji MOOS
    │
    ├── materials/          # Materiały pomocnicze, prezentacje, dokumentacja
    │
    └── README.md           # Opis repozytorium

## Zakres laboratoriów

W trakcie kursu studenci będą realizować między innymi:

-   konfigurację środowiska MOOS‑IVP,
-   tworzenie własnych aplikacji MOOS,
-   komunikację między modułami systemu,
-   implementację prostych zachowań autonomicznych,
-   integrację komponentów w scenariuszach symulacyjnych.

## Wymagania

Do realizacji laboratoriów wymagane jest:

-   system **Linux** (zalecany Ubuntu),
-   zainstalowane środowisko **MOOS‑IVP**,
-   kompilator **C++** (np. `g++`),
-   podstawowa znajomość programowania w **C++**.

## Materiały

Przydatne źródła:

-   Oficjalna dokumentacja MOOS‑IVP\
-   Repozytoria przykładów MOOS\
-   Materiały udostępnione podczas zajęć

## Zasady pracy

1.  Każde laboratorium znajduje się w osobnym katalogu.
2.  Zadania należy wykonywać zgodnie z instrukcją w katalogu danego
    laboratorium.
3.  Rozwiązania należy umieszczać w swoim repozytorium zgodnie ze wskazaną
    strukturą.

## Autor

Materiały przygotowane na potrzeby zajęć **Programowanie Systemów
Autonomicznych**.
