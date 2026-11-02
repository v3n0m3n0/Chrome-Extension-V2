# Przygotowanie Wersji Produkcyjnej Chrome Extension (v0.1.0) Przed Wysłaniem na GitHub

Kompleksowy plan walidacji, standaryzacji metadanych i przygotowania stabilnego wydania produkcyjnego rozszerzenia Gemini Page Lens w wersji v0.1.0, gotowego do bezpośredniego zatwierdzenia w repozytorium GitHub i załadowania w trybie deweloperskim przeglądarki Chrome.

### User Review & Critical Decisions

> [!IMPORTANT]
> Podsumowanie decyzji uzgodnionych z użytkownikiem w Fazie 1:

- **Numer wersji (Confirmed)**: Podbicie wersji do `0.1.0` (pierwsze stabilne wydanie produkcyjne) w `package.json` oraz `public/manifest.json`.
- **Format dystrybucji (Confirmed)**: Kompilacja i weryfikacja czystego katalogu produkcyjnego `dist/` (bez generowania dodatkowego archiwum ZIP).
- **Zakres dokumentacji (Confirmed)**: Pełna aktualizacja `README.md` oraz `CHANGELOG.md` o wszystkie nowo dodane funkcje (Web Speech TTS, Radar Obiektywizmu i Clickbaitu, analiza ostatnich 60s YouTube, Macierz Porównawcza Kart, Hybrydowy RAG ze stemmingiem PL oraz Dwujęzyczny czytnik in-page), wraz z instrukcją instalacji w Google Chrome (`chrome://extensions`).

---

### 1. Overview & Core Concept

- **Cel wydania**: Przygotowanie kompletnego, przetestowanego i przejrzystego stanu repozytorium przed zatwierdzeniem zmian i wypchnięciem do zdalnego repozytorium GitHub (`https://github.com/v3n0m3n0/Chrome-Extension.git`).
- **Odbiorcy**: Użytkownicy końcowi instalujący rozszerzenie lokalnie w Google Chrome (poprzez tryb „Załaduj rozpakowane”) oraz deweloperzy przeglądający kod źródłowy i dokumentację techniczną na GitHubie.
- **Kluczowa wartość**: Zapewnienie zerowego długu technicznego, pełnej spójności numeracji wersji, aktualnej dokumentacji z przykładami użycia oraz gwarancji, że kod źródłowy buduje się w 100% deterministycznie i bezbłędnie.

---

### 2. User Experience & Release Documentation

- **Dokumentacja instalacji w przeglądarce Chrome**:
  - Krok po kroku: wejście na `chrome://extensions/`, włączenie suwaka „Tryb programisty”, kliknięcie „Załaduj rozpakowane” i wskazanie skompilowanego folderu `dist/`.
  - Instrukcja konfiguracji klucza API z Google AI Studio oraz opcjonalnego włączenia wbudowanego modelu On-Device Gemini Nano.
- **Aktualizacja README.md**:
  - Opis wszystkich 6 nowych modułów funkcjonalnych dodanych w ramach etapów 1–3:
    1. *Odsłuch podsumowań i raportów (Web Speech API)* — bezpłatny lektor mowy offline z wyborem prędkości.
    2. *Radar Obiektywizmu i Clickbaitu (Bias & Spin Radar)* — wielowymiarowy wskaźnik neutralności, emocjonalności i clickbaitu.
    3. *YouTube Video Intelligence z analizą ostatnich 60 sekund* — interaktywna transkrypcja z wyszukiwarką i synchronizacją kadru.
    4. *Macierz Porównawcza Otwartych Kart* — konsensus, rozbieżności i unikalne tezy dla wielu kart jednocześnie.
    5. *Hybrydowy RAG Bazy Wiedzy* — Gemini Embeddings + polski stemming leksykalny z rankingiem cosinusowym.
    6. *Dwujęzyczny Czytnik In-Page* — wstrzykiwanie przetłumaczonych akapitów bezpośrednio pod oryginałem na stronach artykułów.
  - Tabela skrótów klawiszowych: `Alt+G`, `Alt+S`, `Alt+V`.
- **Aktualizacja CHANGELOG.md**:
  - Wpis dla wersji `[0.1.0]` z wyszczególnieniem sekcji `Added`, `Changed`, `Refactored` i `Security`.

---

### 3. Key Product Decisions & Trade-Offs

- **Wydanie v0.1.0 zamiast v0.0.7 / v1.0.0**:
  - *Wybrane podejście*: Numer wersji `0.1.0` w `package.json` i `public/manifest.json`.
  - *Uzasadnienie*: Projekt przeszedł gruntowny refaktoring modułowy oraz zyskał 3 pełne etapy nowych zaawansowanych możliwości AI. Wersja `0.1.0` komunikuje stabilny, dojrzały kamień milowy przed oficjalną publikacją w Chrome Web Store.
- **Wykluczenie plików tymczasowych i testowych z paczki produkcyjnej**:
  - *Wybrane podejście*: Weryfikacja `.gitignore`, aby artefakty deweloperskie, logi, `.DS_Store` i foldery tymczasowe nie trafiły do commita git.

---

### 4. Technical Architecture & Release Pipeline

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       PROCES WYDANIA PRODUKCYJNEGO v0.1.0                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 1. Synchronizacja Wersji i Manifestu                                        │
│    • package.json: version = "0.1.0"                                        │
│    • public/manifest.json: version = "0.1.0"                                │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 2. Aktualizacja Dokumentacji Repozytorium                                   │
│    • README.md: nowe funkcje, skróty klawiszowe, instrukcja instalacji       │
│    • CHANGELOG.md: rejestr zmian v0.1.0 (Added, Changed, Security)          │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 3. Weryfikacja Jakości i Regresji                                           │
│    • npm test (24/24 zestawy testów, 429+ testów jednostkowych i UI)        │
│    • TypeScript check: tsc --noEmit (0 błędów typowania)                    │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 4. Kompilacja Produkcyjna                                                   │
│    • npm run build -> generuje czysty, zoptymalizowany katalog dist/        │
│    • Manifest V3 ready do załadowania w chrome://extensions/                │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 5. Stan Repozytorium Git Gotowy do Wysłania                                 │
│    • git status czysty, gotowy do: git add . && git commit                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Harmonogram realizacji krok po kroku

1. **Aktualizacja numeru wersji**:
   - Ustawienie `"version": "0.1.0"` w `/tmp/Chrome-Extension/package.json` oraz `/tmp/Chrome-Extension/public/manifest.json`.
2. **Aktualizacja README.md i CHANGELOG.md**:
   - Wzbogacenie `README.md` o instrukcję instalacji w Chrome, tabelę skrótów klawiszowych oraz omówienie nowych modułów.
   - Dodanie sekcji `[0.1.0]` w `CHANGELOG.md`.
3. **Weryfikacja jakościowa i budowanie**:
   - Uruchomienie `npm test` dla potwierdzenia stuprocentowej zgodności regresyjnej.
   - Uruchomienie `npm run build` w celu skompilowania świeżego katalogu `dist/`.
4. **Inspekcja czystości repozytorium**:
   - Sprawdzenie `git status` oraz podsumowanie przygotowanych plików przed commitem i wysłaniem na GitHub.
