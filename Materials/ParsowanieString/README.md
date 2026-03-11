# Korzystanie z funkcji `tokStringParse()` w MOOS-IvP

W środowisku **MOOS-IvP** bardzo często przesyłamy dane w postaci tekstowej w formacie:

```
klucz=wartość,klucz=wartość,...
```

Przykład takiej wiadomości:

```
x=10,y=20,label=P1
```

Do wygodnego odczytywania wartości z takiego stringa używa się funkcji:

```cpp
tokStringParse()
```

która znajduje się w bibliotece **MBUtils**.

---

## Składnia funkcji

```cpp
string tokStringParse(string str, string key, char block_sep, char kv_sep);
```

Parametry funkcji:

| parametr | znaczenie |
|---|---|
| `str` | cały tekst do przetworzenia |
| `key` | nazwa klucza, którego wartość chcemy odczytać |
| `block_sep` | separator oddzielający kolejne pary `klucz=wartość` |
| `kv_sep` | separator oddzielający klucz od wartości |

---

### Przykład

Rozważmy komunikat:

```
x=10,y=20,label=P1
```

Chcemy z niego odczytać wartości `x`, `y` oraz `label`.

---

### Kod

```cpp
#include "MBUtils.h"
#include <iostream>

int main() {

    std::string msg = "x=10,y=20,label=P1";

    std::string x_val = tokStringParse(msg, "x", ',', '=');
    std::string y_val = tokStringParse(msg, "y", ',', '=');
    std::string label_val = tokStringParse(msg, "label", ',', '=');

    std::cout << "x = " << x_val << std::endl;
    std::cout << "y = " << y_val << std::endl;
    std::cout << "label = " << label_val << std::endl;

    return 0;
}
```

---

### Wynik działania

Program wypisze:

```
x = 10
y = 20
label = P1
```

---

## Jak działa funkcja

Funkcja wykonuje następujące kroki:

1. Dzieli tekst na fragmenty według separatora `block_sep`.

```
x=10
y=20
label=P1
```

2. Każdy fragment dzieli według separatora `kv_sep`.

```
x | 10
y | 20
label | P1
```

3. Jeśli znajdzie klucz równy temu podanemu w parametrze `key`, zwraca jego wartość.

---

## Co jeśli klucz nie istnieje?

Jeśli klucz nie zostanie znaleziony:

```cpp
std::string val = tokStringParse(msg, "speed", ',', '=');
```

wynik będzie:

```
""
```

czyli pusty string.

---

## Typowy sposób użycia w MOOS

W praktyce wartości często trzeba zamienić na liczby:

```cpp
double x = atof(tokStringParse(msg,"x",',','=').c_str());
double y = atof(tokStringParse(msg,"y",',','=').c_str());
```

---

## Podsumowanie

Funkcja:

```cpp
tokStringParse(str, key, block_sep, kv_sep)
```

pozwala łatwo odczytać wartość parametru z tekstu w formacie:

```
klucz=wartość,klucz=wartość
```

Dzięki temu można wygodnie parsować komunikaty przesyłane w systemie **MOOS-IvP**.



# Instrukcja: Funkcja `parseString()`

## Cel funkcji

Funkcja `parseString()` służy do **dzielenia napisu (stringa) na fragmenty według separatora**.  
Jest to bardzo często wykorzystywana technika przy parsowaniu komunikatów tekstowych.

Przykład komunikatu:

```
(10,20);(30,40);(50,60)
```

Chcemy podzielić go na fragmenty:

```
(10,20)
(30,40)
(50,60)
```

Separator w tym przypadku to:

```
;
```

---

## Idea działania funkcji

Funkcja powinna:

1. przyjąć napis wejściowy,
2. znaleźć separator (np. `;` lub `,`),
3. wyciąć fragment przed separatorem,
4. zapisać go w wektorze,
5. powtarzać operację aż do końca napisu.

---

## Prototyp funkcji

```cpp
vector<string> parseString(string text, string delimiter);
```

Parametry:

| parametr | opis |
|--------|------|
| `text` | napis do podziału |
| `delimiter` | separator |

Wartość zwracana:

```
vector<string>
```

czyli **wektor fragmentów napisu**.

---



# Przykład działania funkcji

## Dane wejściowe

```
text = "(10,20);(30,40);(50,60)"
delimiter = ";"
```

## Wynik

```
result[0] = "(10,20)"
result[1] = "(30,40)"
result[2] = "(50,60)"
```

---

## Kolejny przykład

Podział współrzędnych:

```
text = "10,20"
delimiter = ","
```

wynik:

```
result[0] = "10"
result[1] = "20"
```

---

# Schemat działania

```
napis wejściowy
"(10,20);(30,40);(50,60)"
        │
        ▼
find(";")
        │
        ▼
"(10,20)"  "(30,40)"  "(50,60)"
        │
        ▼
vector<string>
```

---

