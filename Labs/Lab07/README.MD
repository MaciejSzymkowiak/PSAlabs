# Laboratorium: konfiguracja misji MOOS-IVP dla wielu pojazdów

## Temat
Konfiguracja prostej misji MOOS-IVP dla trzech społeczności MOOS:
- **shoreside** (konsola operatora),
- **alpha**,
- **bravo**.

---

## Cel laboratorium
Celem ćwiczenia jest poprawne skonfigurowanie komunikacji między wieloma społecznościami MOOS z użyciem aplikacji **pShare**, tak aby:

1. operator otrzymywał od pojazdów zmienne **NODE_REPORT**,
2. operator mógł wyświetlić pozycje pojazdów,
3. operator mógł wysłać do pojazdów zmienne:
   - **DEPLOY**
   - **RETURN**

---

## Wprowadzenie

W misjach wielopojazdowych MOOS-IVP każda jednostka działa jako osobna społeczność MOOS, zwykle z własnym serwerem MOOSDB i własnymi aplikacjami.  
Aby umożliwić wymianę informacji między społecznościami, wykorzystujemy aplikację **pShare**.

W tym laboratorium:
- pojazdy **alpha** i **bravo** będą publikowały swoje **NODE_REPORT**,
- społeczność **operator** będzie te raporty odbierać,
- operator będzie wysyłał do obu pojazdów polecenia **DEPLOY** i **RETURN**.

---

## Architektura misji

W ćwiczeniu zakładamy trzy społeczności MOOS:

- **shoreside** – port MOOSDB: `9000`
- **alpha** – port MOOSDB: `9001`
- **bravo** – port MOOSDB: `9002`

Schemat komunikacji:

- **alpha** → **shoreside** : `NODE_REPORT`
- **bravo** → **shoreside** : `NODE_REPORT`
- **shoreside** → **alpha** : `DEPLOY`, `RETURN`
- **shoreside** → **bravo** : `DEPLOY`, `RETURN`

---

## WAŻNE – wykorzystanie istniejącej misji

Student **nie tworzy konfiguracji pojazdu od zera**.

Należy:
- skorzystać z konfiguracji pojazdu z misji **s1_alpha**,
- przekopiować:
  - pliki `.moos`,
  - pliki zachowań (np. `.bhv`),
- dostosować je do dwóch pojazdów:
  - **alpha**
  - **bravo**

Celem ćwiczenia **nie jest konfiguracja zachowań**, lecz **komunikacja między społecznościami (pShare)**.

---

## Struktura katalogu misji

Przykładowa struktura:

```bash
missions/
└── lab06/
    ├── shoreside.moos
    ├── alpha.moos
    ├── alpha.bhv
    ├── bravo.moos
    ├── bravo.bhv
    └── launch.sh

```

### Przykładowy plik alpha.moos
```
ServerHost = localhost
ServerPort = 9001
Community  = alpha

ProcessConfig = MOOSDB
{
  AppTick   = 4
  CommsTick = 4
}

ProcessConfig = pShare
{
  AppTick   = 4
  CommsTick = 4

  input = route = localhost:9001 // adres na jakim nasłuchuje aplikacja pShare

  output = src_name=NODE_REPORT, route=localhost:9000
}
``` 
### Przykładowy plik shoreside.moos
```
ServerHost = localhost
ServerPort = 9000
Community  = shoreside

ProcessConfig = MOOSDB
{
  AppTick   = 4
  CommsTick = 4
}

ProcessConfig = pShare
{
  AppTick   = 4
  CommsTick = 4

  input = route = localhost:9000 

  output = src_name=DEPLOY, route=localhost:9001 // wysłanie informacji do pojazdu alpha
}
``` 

### Przykładowy plik launch.sh 
```bash
#!/bin/bash

pAntler shoreside.moos > /dev/null 2>&1 &
pAntler alpha.moos > /dev/null 2>&1 &
pAntler bravo.moos > /dev/null 2>&1 &

echo "Misja uruchomiona."
uMAC shoreside.moos
```
