# Hollow Creek ’86: Do północy

Interaktywna gra tekstowa z narratorem AI (Google Gemini, z automatycznym przełączeniem na Groq). Masz 15 tur (od 16:30 do północy), żeby odnaleźć zaginionego przyjaciela i zamknąć Szczelinę. Narrator reaguje na wszystko, co napiszesz. Bez zależności npm (Node.js 18+).

## Jak działa mechanika
- Gracz może wybierać z propozycji albo pisać własne działania.
- **Stan gry pilnuje serwer**, a nie model: zdrowie (0 = śmierć), zagrożenie (10 = Cień Cię dopada), zaufanie dr Vance i szeryfa, przedmioty oraz dwa cele (Tomek odnaleziony, Szczelina zamknięta). Model opowiada historię i proponuje zmiany stanu, serwer je ogranicza i zatwierdza.
- Zła gra ma skutki: brawura, napaści, kłamstwa i marnowanie czasu podnoszą zagrożenie, odbierają zdrowie, psują relacje albo kończą grę natychmiast.
- Zakończenia: **WYGRANA** (Tomek + Szczelina), **CZĘŚCIOWY SUKCES** (tylko Szczelina), **GORZKI KONIEC** (tylko Tomek), **PORAŻKA** (śmierć, schwytanie, zagrożenie 10, północ bez sukcesu).
- Tomka da się odnaleźć dopiero od tury 8, a Szczelinę zamknąć od tury 10. Samym tekstem („wygrywam”) nie da się oszukać gry.

## Automatyczne przełączanie Gemini → Groq
Przy ustawionych obu kluczach serwer pyta najpierw dostawcę z `LLM_PROVIDER` (domyślnie Gemini), a przy błędzie (timeout, 429, 5xx, zły klucz lub model) od razu powtarza zapytanie u drugiego. Dostawca, który zawiódł, jest omijany przez 60 s. `/health` pokazuje stan obu dostawców. `LLM_FALLBACK=off` wyłącza przełączanie. Modele: `GEMINI_MODEL`, `GROQ_MODEL`. Limit czasu: `LLM_TIMEOUT_MS` (domyślnie 40000).

## Uruchomienie lokalne
macOS/Linux:

    export GEMINI_API_KEY="..." GROQ_API_KEY="..."
    node server.js             # http://localhost:3000

Windows (PowerShell):

    $env:GEMINI_API_KEY="..."; $env:GROQ_API_KEY="..."
    node server.js

## GitHub i hosting (Render)
1. Wgraj pliki na GitHub (repo np. `hollow-creek`): `server.js`, `package.json`, `render.yaml`, `README.md` oraz folder `public` z `index.html`.
2. render.com → **New → Blueprint** → wybierz repo → wpisz `GEMINI_API_KEY` i `GROQ_API_KEY` → **Apply**.
3. Po deployu otwórz adres `https://hollow-creek-xxxx.onrender.com`. Sprawdź `/health`.

Darmowy plan usypia serwis po ok. 15 min bez ruchu (pierwsze wejście ~1 min), a trwające gry są w pamięci serwera: po restarcie gracz zobaczy komunikat o wygasłej sesji i zacznie od nowa.

## Struktura
- `server.js` – API gry, stan i zasady, przełączanie dostawców LLM
- `public/index.html` – interfejs gry
- `render.yaml`, `.github/workflows/ci.yml`, `.env.example`
