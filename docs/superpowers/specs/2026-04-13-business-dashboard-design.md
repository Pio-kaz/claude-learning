# Business Dashboard — Specyfikacja Designu

**Data:** 2026-04-13  
**Projekt:** Firmowy dashboard HTML (jeden plik)  
**Status:** Zatwierdzony

---

## Cel

Profesjonalny firmowy dashboard w HTML/CSS/JS (jeden plik `index.html`) dla 5 firm. Ciemny motyw, dane w localStorage, pogoda na żywo z Open-Meteo, panel boczny do edycji.

---

## Architektura

**Jeden plik `index.html`** z trzema blokami:
- `<style>` — cały CSS (zmienne, layout, komponenty, animacje)
- `<body>` — statyczny szkielet HTML
- `<script>` — state, renderowanie, eventy, API calls

**Brak zewnętrznych zależności** oprócz:
- Google Fonts CDN (`Inter`)
- Open-Meteo API (darmowe, bez klucza)

---

## Struktura danych

Klucz localStorage: `dashboard_data`

```js
{
  companies: [
    {
      id: "A",
      name: "Firma A",
      revenue: 120000,    // przychód miesięczny PLN
      costs: 85000,       // koszty miesięczne PLN
      employees: 24,
      tasks: [
        { id: 1, text: "Audyt Q2", priority: "high", done: false },
        { id: 2, text: "Onboarding 3 osób", priority: "medium", done: false },
        { id: 3, text: "Raport zarządu", priority: "low", done: true }
      ]
    }
    // ... Firma B, C, D, E (analogicznie)
  ]
}
```

- `zysk` = `revenue - costs` — wyliczany on-the-fly, nie przechowywany
- Nowe zadanie: `id: Date.now()`
- Cykl: start → wczytaj z localStorage (lub defaults) → zmiana → `save()` → `render()`

---

## Layout

### Górny pasek (topbar)
- `position: sticky; top: 0`
- Lewa: tytuł "Business Dashboard"
- Środek: data + żywy zegar (co 1s, `Intl.DateTimeFormat` po polsku)
- Prawa: pogoda Łódź (emoji + temperatura + opis, co 10 min)

### Sekcja "Dzień dobry"
- Powitanie kontekstowe (rano/dzień/wieczór)
- 4 agregaty: łączny przychód, koszty, zysk, pracownicy
- Liczba niezakończonych zadań wszystkich firm

### Siatka kart firm
- CSS Grid: `repeat(auto-fill, minmax(340px, 1fr))`
- 5 kart (Firma A–E)

**Każda karta zawiera:**
- Nagłówek: nazwa + badge z liczbą pracowników
- 3 metryki finansowe: Przychód / Koszty / Zysk
  - Zysk zielony gdy `> 0`, czerwony gdy `< 0`
- Lista 3 zadań:
  - Badge priorytetu: `high` = czerwony, `medium` = żółty, `low` = zielony
  - Checkbox oznaczenia jako ukończone
- Przycisk "Szczegóły →" otwierający panel boczny

### Panel boczny (sidebar)
- `position: fixed; right: 0`
- Wysuwa się przez klasę `.open` → `transform: translateX(0)`, `transition: 0.3s ease`
- Overlay z przesłonięciem tła (klik na overlay zamyka panel)
- Zawiera:
  - Edytowalne pola: nazwa, przychód, koszty, pracownicy
  - Pełna lista zadań: edycja tekstu, zmiana priorytetu, toggle done, usunięcie
  - Przycisk "+ Dodaj zadanie"
- Zmiany zapisywane przy `input` (debounce 300ms)

---

## Styl wizualny

| Element | Wartość |
|---|---|
| Tło strony | `#0f1117` |
| Tło kart | `#1a1d27` |
| Kolor akcentu | `#6366f1` (indigo) |
| Font | `Inter` (Google Fonts) |
| Promień zaokrąglenia | `12px` |
| Cień kart | `0 4px 24px rgba(0,0,0,0.4)` |

---

## Interakcje

### Zegar
`setInterval` co 1000ms → `Intl.DateTimeFormat('pl-PL', { ... })`

### Pogoda
```
GET https://api.open-meteo.com/v1/forecast
  ?latitude=51.77&longitude=19.46&current_weather=true
```
Kod pogody (`weathercode`) mapowany na emoji. Fallback: `"☁️ Brak danych"` przy błędzie sieci.

### Zadania
- Checkbox `done` → tekst przekreślony + `opacity: 0.5`
- Usunięcie → `tasks.filter(t => t.id !== id)`

---

## Responsywność

| Szerokość | Kolumny kart |
|---|---|
| `> 1200px` | 3 |
| `768–1200px` | 2 |
| `< 768px` | 1 (sidebar pełna szerokość) |

---

## Obsługa błędów

- Open-Meteo niedostępne → `"☁️ Brak danych"`, brak crash'a
- localStorage niedostępne → dane tylko w pamięci sesji
- Brak zadań w firmie → komunikat "Brak zadań"
