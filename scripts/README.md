# Generator Kart Pracy — skrypt produkcyjny

Samodzielny skrypt Python (`generuj_karte_pracy.py`) do produkcji kart pracy dla
szkoły podstawowej (klasy 1-8), niezależny od aplikacji webowej PromptAppGPT.
Wywołuje bezpośrednio OpenAI API i zapisuje gotowe pliki `.docx` (do druku)
oraz `.txt`.

## Instalacja

```bash
python3 -m venv .venv
source .venv/bin/activate       # Windows: .venv\Scripts\activate
pip install -r scripts/requirements.txt
```

## Konfiguracja klucza API

```bash
export OPENAI_API_KEY="sk-..."
```

albo przekaż klucz bezpośrednio flagą `--api-key`.

## Użycie — pojedyncza karta pracy

```bash
python scripts/generuj_karte_pracy.py \
  --przedmiot "Matematyka" \
  --klasa 4 \
  --temat "Mnożenie pisemne" \
  --liczba-zadan 5 \
  --trudnosc "zróżnicowany" \
  --typ-zadan "mieszane" \
  --klucz tak \
  --output karty/mnozenie_pisemne
```

Wynik: `karty/mnozenie_pisemne.docx` i `karty/mnozenie_pisemne.txt`.

## Użycie — produkcja wsadowa (wiele kart naraz)

Przygotuj plik CSV, np. `tematy.csv`:

```csv
przedmiot,klasa,temat,liczba_zadan,trudnosc,typ_zadan,klucz
Matematyka,4,Mnożenie pisemne,5,zróżnicowany,mieszane,tak
Język polski,3,Rzeczownik i przymiotnik,4,średni,otwarte,tak
Przyroda,5,Obieg wody w przyrodzie,6,łatwy,zamknięte (test wyboru),nie
```

Uruchom:

```bash
python scripts/generuj_karte_pracy.py --batch tematy.csv --output-dir karty/
```

Skrypt wygeneruje kolejno kartę pracy dla każdego wiersza, zapisując pliki
`01_mnozenie_pisemne.docx`, `02_rzeczownik_i_przymiotnik.docx` itd. w podanym
katalogu, wraz z wersjami `.txt`. Błędy pojedynczych wierszy nie przerywają
całej produkcji — są zbierane i wypisywane na końcu.

## Parametry

| Flaga | Opis | Domyślnie |
|---|---|---|
| `--przedmiot` | Przedmiot szkolny | `Matematyka` |
| `--klasa` | Klasa 1-8 | `4` |
| `--temat` | Temat karty pracy (wymagany w trybie pojedynczym) | — |
| `--wskazowki` | Dodatkowe wskazówki dla modelu | brak |
| `--liczba-zadan` | Liczba zadań na karcie | `5` |
| `--trudnosc` | Poziom trudności | `zróżnicowany (od łatwych do trudnych)` |
| `--typ-zadan` | Typy zadań (mieszane / zamknięte / otwarte / uzupełnianie luk / prawda-fałsz / dopasowywanie / zadania tekstowe) | `mieszane` |
| `--klucz` | `tak` / `nie` — czy dołączyć klucz odpowiedzi | `tak` |
| `--model` | Model OpenAI | `gpt-4o-mini` |
| `--output` | Ścieżka bazowa pliku (tryb pojedynczy) | `karty/<slug-tematu>` |
| `--batch` | Ścieżka do CSV z listą tematów (tryb wsadowy) | — |
| `--output-dir` | Katalog wyjściowy (tryb wsadowy) | `karty/` |
