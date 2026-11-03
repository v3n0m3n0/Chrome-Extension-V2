# Przygotowanie Produkcyjnej Paczki ZIP do Chrome Web Store

Przygotowanie oficjalnej, zoptymalizowanej paczki archiwalnej `.zip` rozszerzenia Chrome w wersji v0.1.0 w dedykowanym katalogu `release/`, zintegrowanej ze skryptem automatyzacji `npm run build:zip` w `package.json` oraz pełną weryfikacją zgodności z wymogami Chrome Web Store (Manifest V3, brak plików deweloperskich i niepotrzebnych map).

## User Review & Critical Decisions

> [!IMPORTANT]
> Podsumowanie decyzji potwierdzonych przez użytkownika w fazie wywiadu:

- **Lokalizacja i format paczki**: Zgodnie z wyborem użytkownika, skompilowane archiwum zostanie umieszczone w dedykowanym katalogu `release/` pod nazwą `release/chrome-extension-v0.1.0.zip`.
- **Automatyzacja budowania**: W `package.json` zostanie zdefiniowany dedykowany skrypt `npm run build:zip` łączący weryfikację typów, kompilację produkcyjną Vite (`tsc && vite build`) oraz pakowanie czystej zawartości katalogu `dist/` do pliku zip.
- **Higiena paczki Chrome Web Store**: Archiwum ZIP zawierać będzie wyłącznie pliki runtime z `dist/` (manifest.json, background.js, content.js, sidepanel.html, assets, ikony) bez plików testowych (`*.test.*`), kodu źródłowego TypeScript (`src/`), plików konfiguracyjnych dewelopera ani zbędnych plików systemowych (`.DS_Store`, `Thumbs.db`).

---

## 1. Overview & Core Concept

- **Cel**: Wygenerowanie gotowej do wgrania w Chrome Developer Dashboard paczki instalacyjnej ZIP spełniającej standardy Google Chrome Web Store.
- **Wersja wydania**: `0.1.0` (zgodna z `package.json` oraz `manifest.json`).
- **Główna wartość**: Pewność, że paczka zawiera wszystkie moduły z 3 etapów ewolucji (TTS, Baza wiedzy, Weryfikacja, Dwujęzyczny czytnik, Bypass paywalli) w zminifikowanej, produkcyjnej formie, gotowej do natychmiastowej certyfikacji w sklepie.

---

## 2. User Experience & Visual Design (Struktura Paczki)

Paczka ZIP po rozpakowaniu przez Chrome Web Store musi posiadać bezpośrednio w korzeniu plik `manifest.json` oraz powiązane zasoby (brak zagnieżdżonego podfolderu `dist/` wewnątrz archiwum, co jest częstym błędem powodującym odrzucenie w panelu Google):

```
chrome-extension-v0.1.0.zip (korzeń archiwum):
├── manifest.json              # Manifest V3 (wersja 0.1.0, permissions, icons)
├── background.js              # Service Worker rozszerzenia
├── content.js                 # Skrypt wstrzykiwany (ekstrakcja, bypass, TTS, highlighter)
├── sidepanel.html             # Interfejs bocznego panelu Chrome
├── assets/                    # Skompilowane bundle JS, CSS i assety UI
└── icons/                     # Ikony rozszerzenia (16, 32, 48, 128 px)
```

---

## 3. Key Product Decisions & Trade-Offs

- **Struktura archiwum (Flat dist packaging)**:
  - *Wybrane podejście*: Archiwizacja bezpośrednio z poziomu katalogu `dist/` (`cd dist && zip -r ../release/chrome-extension-v0.1.0.zip .`).
  - *Dlaczego*: Chrome Web Store wymaga, aby `manifest.json` znajdował się bezpośrednio w głównym katalogu archiwum ZIP. Wrzucenie folderu `dist/` jako podkatalogu uniemożliwia weryfikację w panelu dewelopera.
- **Narzędzie do tworzenia ZIP**:
  - *Wybrane podejście*: Użycie narzędzia systemowego `zip` w powłoce oraz skryptu Node.js / npm cross-platform w `scripts/package-zip.js` lub zwięzłego polecenia npm, aby proces był powtarzalny w środowiskach CI/CD.
- **Katalog `release/` w `.gitignore`**:
  - *Wybrane podejście*: Upewnienie się, że `release/*.zip` jest uwzględnione lub dodane do repozytorium w zależności od potrzeb release workflow.

---

## 4. Technical Architecture & Data Strategy

### Schemat procesu budowania i pakowania wydania:

```
┌─────────────────────────────────────────────────────────────┐
│                   npm run build:zip                         │
└──────────────────────────────┬──────────────────────────────┘
                               │
            ┌──────────────────▼──────────────────┐
            │  1. Kontrola typów i testy         │
            │     tsc && npm test                 │
            │     (429/429 testów, 0 błędów TS)   │
            └──────────────────┬──────────────────┘
                               │
            ┌──────────────────▼──────────────────┐
            │  2. Kompilacja Vite                 │
            │     vite build                      │
            │     -> Czysty katalog /dist         │
            └──────────────────┬──────────────────┘
                               │
            ┌──────────────────▼──────────────────┐
            │  3. Pakowanie do archiwum ZIP       │
            │     mkdir -p release                │
            │     cd dist && zip -r -9 ...        │
            │     -> release/chrome-ext-v0.1.0.zip│
            └──────────────────┬──────────────────┘
                               │
            ┌──────────────────▼──────────────────┐
            │  4. Walidacja zawartości archiwum   │
            │     - Sprawdzenie manifest.json     │
            │     - Weryfikacja rozmiaru i sumy   │
            └─────────────────────────────────────┘
```

### Plan realizacji krok po kroku (po zatwierdzeniu planu):
1. Utworzenie katalogu `release/` w `/tmp/Chrome-Extension`.
2. Dodanie do `package.json` skryptu `"build:zip": "npm run build && node scripts/package-zip.js"` (lub bezpośredniego polecenia pakującego).
3. Przygotowanie niezawodnego skryptu `scripts/package-zip.js` korzystającego z wbudowanych mechanizmów Node.js / archiver, gwarantującego czyste pakowanie bez śmieci systemowych.
4. Uruchomienie `npm run build:zip` i wygenerowanie `release/chrome-extension-v0.1.0.zip`.
5. Weryfikacja integralności archiwum: inspekcja listy plików (`unzip -l`), sprawdzenie obecności `manifest.json`, obliczenie rozmiaru i sumy kontrolnej SHA-256.
6. Commit zmian konfiguracyjnych w repozytorium git.
