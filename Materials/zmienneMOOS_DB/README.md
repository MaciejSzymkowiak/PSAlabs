# Typowe zmienne w bazie MOOSDB

W środowisku **MOOS-IVP** aplikacje komunikują się poprzez **MOOSDB**, publikując i subskrybując zmienne. Poniżej przedstawiono najczęściej używane zmienne związane z nawigacją oraz sterowaniem pojazdem.

---

# Zmienne nawigacyjne (Navigation)

| Zmienna | Opis |
|---|---|
| `NAV_X` | Aktualna pozycja pojazdu w osi **X** (metry w układzie lokalnym) |
| `NAV_Y` | Aktualna pozycja pojazdu w osi **Y** (metry w układzie lokalnym) |
| `NAV_LAT` | Szerokość geograficzna pojazdu |
| `NAV_LONG` | Długość geograficzna pojazdu |
| `NAV_HEADING` | Aktualny kurs pojazdu (stopnie) |
| `NAV_SPEED` | Aktualna prędkość pojazdu |
| `NAV_DEPTH` | Aktualna głębokość pojazdu |
| `NAV_ALTITUDE` | Wysokość pojazdu nad dnem |
| `NAV_PITCH` | Kąt pochylenia pojazdu |
| `NAV_ROLL` | Kąt przechyłu pojazdu |
| `NAV_YAW` | Rotacja wokół osi pionowej |

---

# Zmienne sterujące (Desired / Command Variables)

Zmienne te są zwykle publikowane przez **IvP Helm** lub inne moduły sterujące.

| Zmienna | Opis |
|---|---|
| `DESIRED_SPEED` | Docelowa prędkość pojazdu |
| `DESIRED_HEADING` | Docelowy kurs pojazdu |
| `DESIRED_DEPTH` | Docelowa głębokość |
| `DESIRED_THRUST` | Poziom ciągu silnika |
| `DESIRED_RUDDER` | Kąt steru |
| `DESIRED_ELEVATOR` | Kąt steru głębokości |

---

# Zmienne waypointów (Waypoint Navigation)

| Zmienna | Opis |
|---|---|
| `WPT_INDEX` | Aktualny indeks waypointa |
| `WPT_DIST_TO_NEXT` | Odległość do następnego waypointa |
| `WPT_ETA_TO_NEXT` | Szacowany czas dotarcia |
| `WPT_STAT` | Status nawigacji waypointów |

---

# Zmienne systemowe (System / Helm)

| Zmienna | Opis |
|---|---|
| `IVPHELM_STATE` | Aktualny stan IvP Helm |
| `IVPHELM_MODESET` | Aktualny zestaw trybów |
| `MOOS_MANUAL_OVERRIDE` | Przełączenie między trybem autonomicznym i ręcznym |
| `DB_UPTIME` | Czas działania MOOSDB |
| `DB_CLIENTS` | Lista aktywnych aplikacji MOOS |

---

# Przykład rejestracji zmiennych w aplikacji MOOS

```cpp
Register("NAV_X", 0);
Register("NAV_Y", 0);
Register("NAV_SPEED", 0);

Register("DESIRED_SPEED", 0);
Register("DESIRED_HEADING", 0);