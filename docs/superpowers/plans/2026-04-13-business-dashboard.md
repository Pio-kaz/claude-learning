# Business Dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Zbudować profesjonalny firmowy dashboard w jednym pliku `index.html` dla 5 firm z ciemnym motywem, pogodą Open-Meteo, localStorage i panelem bocznym do edycji.

**Architecture:** Jeden plik HTML z blokami `<style>`, `<body>`, `<script>`. State trzymany w obiekcie JS i persistowany do `localStorage` pod kluczem `dashboard_data`. Renderowanie przez funkcje JS modyfikujące `innerHTML` kontenerów.

**Tech Stack:** HTML5, CSS3 (Grid, Flexbox, CSS Variables, transitions), Vanilla JS (ES6+), Open-Meteo API, localStorage, Google Fonts (Inter CDN)

---

## Plik

- Tworzy: `index.html` (root repozytorium)

---

### Task 1: HTML skeleton + CSS foundation

**Files:**
- Create: `index.html`

- [ ] **Step 1: Utwórz plik `index.html` z podstawowym szkieletem**

```html
<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Business Dashboard</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <style>
    /* === CSS VARIABLES === */
    :root {
      --bg: #0f1117;
      --surface: #1a1d27;
      --surface2: #222537;
      --accent: #6366f1;
      --accent-hover: #818cf8;
      --text: #e2e8f0;
      --text-muted: #64748b;
      --text-dim: #94a3b8;
      --green: #10b981;
      --red: #ef4444;
      --yellow: #f59e0b;
      --border: #2d3148;
      --radius: 12px;
      --radius-sm: 6px;
      --shadow: 0 4px 24px rgba(0,0,0,0.4);
      --transition: 0.2s ease;
    }

    /* === RESET === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Inter', sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      line-height: 1.5;
    }
    button {
      cursor: pointer;
      border: none;
      background: none;
      font-family: inherit;
      font-size: inherit;
      color: inherit;
    }
    input, select, textarea {
      font-family: inherit;
      font-size: inherit;
    }
  </style>
</head>
<body>

  <script>
    // placeholder — będzie rozbudowywany
    console.log('Dashboard loading...');
  </script>
</body>
</html>
```

- [ ] **Step 2: Zweryfikuj w przeglądarce**

Otwórz `index.html` w przeglądarce. Oczekiwane: ciemne tło `#0f1117`, brak błędów w konsoli, font Inter załadowany (sprawdź w DevTools → Network).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: dodaj szkielet HTML i CSS variables"
```

---

### Task 2: Topbar HTML + CSS

**Files:**
- Modify: `index.html` — dodaj `<header>` i jego style

- [ ] **Step 1: Dodaj HTML topbara w `<body>` przed `<script>`**

```html
  <!-- TOPBAR -->
  <header class="topbar">
    <div class="topbar__brand">
      <svg width="28" height="28" viewBox="0 0 28 28" fill="none">
        <rect width="28" height="28" rx="6" fill="#6366f1"/>
        <path d="M7 20L11 13L15 17L19 10L21 13" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
      <span>Business Dashboard</span>
    </div>
    <div class="topbar__clock">
      <span id="clock-time">00:00:00</span>
      <span id="clock-date">poniedziałek, 1 stycznia 2026</span>
    </div>
    <div class="topbar__weather" id="weather">
      <span class="weather-icon">⏳</span>
      <span class="weather-text">Łódź — ładowanie...</span>
    </div>
  </header>
```

- [ ] **Step 2: Dodaj CSS topbara w `<style>`**

```css
    /* === TOPBAR === */
    .topbar {
      position: sticky;
      top: 0;
      z-index: 100;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 32px;
      height: 64px;
      background: rgba(15, 17, 23, 0.95);
      backdrop-filter: blur(12px);
      border-bottom: 1px solid var(--border);
    }
    .topbar__brand {
      display: flex;
      align-items: center;
      gap: 10px;
      font-weight: 700;
      font-size: 1.05rem;
      letter-spacing: -0.3px;
    }
    .topbar__clock {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 1px;
    }
    #clock-time {
      font-size: 1.4rem;
      font-weight: 600;
      letter-spacing: 0.5px;
      font-variant-numeric: tabular-nums;
    }
    #clock-date {
      font-size: 0.75rem;
      color: var(--text-muted);
      text-transform: capitalize;
    }
    .topbar__weather {
      display: flex;
      align-items: center;
      gap: 8px;
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 8px;
      padding: 6px 14px;
      font-size: 0.85rem;
    }
    .weather-icon { font-size: 1.2rem; }
    .weather-text { color: var(--text-dim); }
```

- [ ] **Step 3: Zweryfikuj w przeglądarce**

Otwórz `index.html`. Oczekiwane: topbar przyklejony do góry, trzy sekcje widoczne (logo+tekst, miejsce na zegar, placeholder pogody), ciemne tło z subtelną dolną linią.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: dodaj topbar z layoutem"
```

---

### Task 3: Żywy zegar i data

**Files:**
- Modify: `index.html` — dodaj JS zegara w `<script>`

- [ ] **Step 1: Zastąp placeholder w `<script>` kodem zegara**

```js
    // === CLOCK ===
    function updateClock() {
      const now = new Date();
      document.getElementById('clock-time').textContent =
        now.toLocaleTimeString('pl-PL', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
      document.getElementById('clock-date').textContent =
        now.toLocaleDateString('pl-PL', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
    }
    updateClock();
    setInterval(updateClock, 1000);
```

- [ ] **Step 2: Zweryfikuj w przeglądarce**

Otwórz `index.html`. Oczekiwane: zegar tyka co sekundę, data wyświetla się po polsku np. "poniedziałek, 13 kwietnia 2026".

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: dodaj żywy zegar i datę po polsku"
```

---

### Task 4: State — domyślne dane + save/load

**Files:**
- Modify: `index.html` — dodaj state management w `<script>`

- [ ] **Step 1: Dodaj domyślne dane i funkcje save/load po kodzie zegara**

```js
    // === STATE ===
    const DEFAULT_DATA = {
      companies: [
        {
          id: "A", name: "Firma Alpha", revenue: 320000, costs: 215000, employees: 34,
          tasks: [
            { id: 1, text: "Audyt finansowy Q2", priority: "high", done: false },
            { id: 2, text: "Onboarding 3 nowych inżynierów", priority: "medium", done: false },
            { id: 3, text: "Raport zarządu — czerwiec", priority: "low", done: true }
          ]
        },
        {
          id: "B", name: "Firma Beta", revenue: 187000, costs: 143000, employees: 18,
          tasks: [
            { id: 4, text: "Migracja bazy danych", priority: "high", done: false },
            { id: 5, text: "Kampania marketingowa", priority: "medium", done: false },
            { id: 6, text: "Aktualizacja regulaminu", priority: "low", done: false }
          ]
        },
        {
          id: "C", name: "Firma Gamma", revenue: 540000, costs: 490000, employees: 61,
          tasks: [
            { id: 7, text: "Negocjacje z dostawcą", priority: "high", done: false },
            { id: 8, text: "Szkolenie BHP", priority: "medium", done: true },
            { id: 9, text: "Przegląd umów", priority: "low", done: false }
          ]
        },
        {
          id: "D", name: "Firma Delta", revenue: 95000, costs: 112000, employees: 11,
          tasks: [
            { id: 10, text: "Pozyskanie inwestora", priority: "high", done: false },
            { id: 11, text: "Optymalizacja kosztów", priority: "high", done: false },
            { id: 12, text: "Redesign strony", priority: "medium", done: false }
          ]
        },
        {
          id: "E", name: "Firma Epsilon", revenue: 760000, costs: 420000, employees: 92,
          tasks: [
            { id: 13, text: "Ekspansja na rynek DE", priority: "high", done: false },
            { id: 14, text: "Rekrutacja 5 seniorów", priority: "medium", done: false },
            { id: 15, text: "Certyfikacja ISO 27001", priority: "medium", done: true }
          ]
        }
      ]
    };

    const LS_KEY = 'dashboard_data';

    function loadData() {
      try {
        const raw = localStorage.getItem(LS_KEY);
        return raw ? JSON.parse(raw) : JSON.parse(JSON.stringify(DEFAULT_DATA));
      } catch {
        return JSON.parse(JSON.stringify(DEFAULT_DATA));
      }
    }

    function saveData() {
      try {
        localStorage.setItem(LS_KEY, JSON.stringify(state));
      } catch {
        // localStorage niedostępne — dane żyją tylko w pamięci sesji
      }
    }

    let state = loadData();
```

- [ ] **Step 2: Zweryfikuj w konsoli przeglądarki**

Otwórz `index.html` → DevTools Console → wpisz `state`. Oczekiwane: obiekt z tablicą `companies` zawierającą 5 firm. Wpisz `saveData()` → sprawdź DevTools → Application → localStorage → klucz `dashboard_data`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: dodaj state management z localStorage"
```

---

### Task 5: Sekcja "Dzień dobry" — HTML + CSS + renderowanie

**Files:**
- Modify: `index.html` — HTML, CSS i funkcja `renderSummary()`

- [ ] **Step 1: Dodaj HTML sekcji summary po `<header>`**

```html
  <!-- SUMMARY -->
  <section class="summary" id="summary">
    <!-- wypełniane przez renderSummary() -->
  </section>
```

- [ ] **Step 2: Dodaj CSS sekcji summary w `<style>`**

```css
    /* === SUMMARY === */
    .summary {
      padding: 32px 32px 0;
    }
    .summary__greeting {
      font-size: 1.6rem;
      font-weight: 700;
      margin-bottom: 6px;
      letter-spacing: -0.5px;
    }
    .summary__subtitle {
      color: var(--text-muted);
      font-size: 0.9rem;
      margin-bottom: 24px;
    }
    .summary__stats {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
      gap: 12px;
    }
    .stat-card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 16px 20px;
    }
    .stat-card__label {
      font-size: 0.75rem;
      text-transform: uppercase;
      letter-spacing: 0.8px;
      color: var(--text-muted);
      margin-bottom: 6px;
    }
    .stat-card__value {
      font-size: 1.4rem;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
    }
    .stat-card__value.green { color: var(--green); }
    .stat-card__value.red   { color: var(--red); }
    .stat-card__value.accent { color: var(--accent-hover); }
```

- [ ] **Step 3: Dodaj funkcję `renderSummary()` w `<script>`**

```js
    // === HELPERS ===
    function formatPLN(n) {
      return new Intl.NumberFormat('pl-PL', { style: 'currency', currency: 'PLN', maximumFractionDigits: 0 }).format(n);
    }

    function greeting() {
      const h = new Date().getHours();
      if (h < 12) return 'Dzień dobry';
      if (h < 18) return 'Dzień dobry';
      return 'Dobry wieczór';
    }

    // === RENDER SUMMARY ===
    function renderSummary() {
      const totalRevenue  = state.companies.reduce((s, c) => s + c.revenue, 0);
      const totalCosts    = state.companies.reduce((s, c) => s + c.costs, 0);
      const totalProfit   = totalRevenue - totalCosts;
      const totalEmployees = state.companies.reduce((s, c) => s + c.employees, 0);
      const pendingTasks  = state.companies.reduce((s, c) => s + c.tasks.filter(t => !t.done).length, 0);

      document.getElementById('summary').innerHTML = `
        <div class="summary__greeting">${greeting()}, witaj w dashboardzie 👋</div>
        <div class="summary__subtitle">
          Zarządzasz ${state.companies.length} firmami · ${pendingTasks} niezakończonych zadań
        </div>
        <div class="summary__stats">
          <div class="stat-card">
            <div class="stat-card__label">Łączny przychód</div>
            <div class="stat-card__value accent">${formatPLN(totalRevenue)}</div>
          </div>
          <div class="stat-card">
            <div class="stat-card__label">Łączne koszty</div>
            <div class="stat-card__value">${formatPLN(totalCosts)}</div>
          </div>
          <div class="stat-card">
            <div class="stat-card__label">Łączny zysk</div>
            <div class="stat-card__value ${totalProfit >= 0 ? 'green' : 'red'}">${formatPLN(totalProfit)}</div>
          </div>
          <div class="stat-card">
            <div class="stat-card__label">Pracownicy</div>
            <div class="stat-card__value">${totalEmployees}</div>
          </div>
          <div class="stat-card">
            <div class="stat-card__label">Zadania w toku</div>
            <div class="stat-card__value accent">${pendingTasks}</div>
          </div>
        </div>
      `;
    }
```

- [ ] **Step 4: Wywołaj `renderSummary()` na końcu `<script>`**

```js
    // === INIT ===
    renderSummary();
```

- [ ] **Step 5: Zweryfikuj w przeglądarce**

Otwórz `index.html`. Oczekiwane: powitanie "Dzień dobry", 5 kart statystyk z wartościami PLN, łączny zysk zielony (sumując przykładowe dane: 320k+187k+540k+95k+760k - 215k+143k+490k+112k+420k = 1 902 000 - 1 380 000 = 522 000 PLN).

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: dodaj sekcję podsumowania z agregatami firm"
```

---

### Task 6: Karty firm — HTML + CSS + renderowanie

**Files:**
- Modify: `index.html` — HTML grid, CSS kart, funkcja `renderCards()`

- [ ] **Step 1: Dodaj HTML gridu kart po sekcji summary**

```html
  <!-- COMPANY CARDS -->
  <main class="cards-grid" id="cards-grid">
    <!-- wypełniane przez renderCards() -->
  </main>
```

- [ ] **Step 2: Dodaj CSS gridu i kart w `<style>`**

```css
    /* === CARDS GRID === */
    .cards-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(340px, 1fr));
      gap: 20px;
      padding: 24px 32px 40px;
    }

    /* === COMPANY CARD === */
    .company-card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      padding: 24px;
      display: flex;
      flex-direction: column;
      gap: 20px;
      transition: border-color var(--transition), box-shadow var(--transition);
    }
    .company-card:hover {
      border-color: var(--accent);
      box-shadow: 0 4px 32px rgba(99,102,241,0.15);
    }
    .card__header {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .card__name {
      font-size: 1.1rem;
      font-weight: 700;
      letter-spacing: -0.3px;
    }
    .badge {
      display: inline-flex;
      align-items: center;
      gap: 4px;
      padding: 3px 10px;
      border-radius: 20px;
      font-size: 0.75rem;
      font-weight: 500;
    }
    .badge--employees {
      background: rgba(99,102,241,0.15);
      color: var(--accent-hover);
      border: 1px solid rgba(99,102,241,0.25);
    }
    .badge--high   { background: rgba(239,68,68,0.15);   color: #fca5a5; border: 1px solid rgba(239,68,68,0.25); }
    .badge--medium { background: rgba(245,158,11,0.15);  color: #fcd34d; border: 1px solid rgba(245,158,11,0.25); }
    .badge--low    { background: rgba(16,185,129,0.15);  color: #6ee7b7; border: 1px solid rgba(16,185,129,0.25); }

    /* === METRICS === */
    .card__metrics {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
    }
    .metric {
      background: var(--surface2);
      border-radius: var(--radius-sm);
      padding: 10px 12px;
    }
    .metric__label {
      font-size: 0.68rem;
      text-transform: uppercase;
      letter-spacing: 0.6px;
      color: var(--text-muted);
      margin-bottom: 4px;
    }
    .metric__value {
      font-size: 0.9rem;
      font-weight: 600;
      font-variant-numeric: tabular-nums;
    }
    .metric__value.green { color: var(--green); }
    .metric__value.red   { color: var(--red); }

    /* === TASKS === */
    .card__tasks-title {
      font-size: 0.75rem;
      text-transform: uppercase;
      letter-spacing: 0.6px;
      color: var(--text-muted);
      margin-bottom: 8px;
    }
    .task-item {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 7px 0;
      border-bottom: 1px solid var(--border);
    }
    .task-item:last-child { border-bottom: none; }
    .task-item input[type="checkbox"] {
      accent-color: var(--accent);
      width: 15px;
      height: 15px;
      cursor: pointer;
      flex-shrink: 0;
    }
    .task-item__text {
      flex: 1;
      font-size: 0.85rem;
    }
    .task-item__text.done {
      text-decoration: line-through;
      opacity: 0.45;
    }
    .tasks-empty {
      font-size: 0.8rem;
      color: var(--text-muted);
      padding: 8px 0;
    }

    /* === CARD FOOTER === */
    .card__footer {
      margin-top: auto;
    }
    .btn-details {
      width: 100%;
      padding: 9px;
      background: rgba(99,102,241,0.1);
      border: 1px solid rgba(99,102,241,0.25);
      border-radius: var(--radius-sm);
      color: var(--accent-hover);
      font-size: 0.85rem;
      font-weight: 500;
      transition: background var(--transition), border-color var(--transition);
    }
    .btn-details:hover {
      background: rgba(99,102,241,0.2);
      border-color: var(--accent);
    }
```

- [ ] **Step 3: Dodaj funkcję `renderCards()` w `<script>` przed sekcją INIT**

```js
    // === RENDER CARDS ===
    function renderCards() {
      document.getElementById('cards-grid').innerHTML = state.companies.map(company => {
        const profit = company.revenue - company.costs;
        const tasks = company.tasks.slice(0, 3);
        return `
          <div class="company-card">
            <div class="card__header">
              <div class="card__name">${company.name}</div>
              <span class="badge badge--employees">👥 ${company.employees}</span>
            </div>
            <div class="card__metrics">
              <div class="metric">
                <div class="metric__label">Przychód</div>
                <div class="metric__value">${formatPLN(company.revenue)}</div>
              </div>
              <div class="metric">
                <div class="metric__label">Koszty</div>
                <div class="metric__value">${formatPLN(company.costs)}</div>
              </div>
              <div class="metric">
                <div class="metric__label">Zysk</div>
                <div class="metric__value ${profit >= 0 ? 'green' : 'red'}">${formatPLN(profit)}</div>
              </div>
            </div>
            <div class="card__tasks">
              <div class="card__tasks-title">Top zadania</div>
              ${tasks.length === 0
                ? '<div class="tasks-empty">Brak zadań</div>'
                : tasks.map(t => `
                  <div class="task-item">
                    <input type="checkbox" ${t.done ? 'checked' : ''}
                      onchange="toggleTaskDone('${company.id}', ${t.id})">
                    <span class="task-item__text ${t.done ? 'done' : ''}">${t.text}</span>
                    <span class="badge badge--${t.priority}">${t.priority}</span>
                  </div>
                `).join('')
              }
            </div>
            <div class="card__footer">
              <button class="btn-details" onclick="openSidebar('${company.id}')">
                Szczegóły →
              </button>
            </div>
          </div>
        `;
      }).join('');
    }
```

- [ ] **Step 4: Dodaj stub `toggleTaskDone` i `openSidebar` (zaślepki na teraz) oraz wywołaj `renderCards()` w sekcji INIT**

```js
    // === STUBS (do implementacji w kolejnych taskach) ===
    function toggleTaskDone(companyId, taskId) { /* Task 9 */ }
    function openSidebar(companyId) { /* Task 7 */ }

    // === INIT (zastąp istniejące) ===
    renderSummary();
    renderCards();
```

- [ ] **Step 5: Zweryfikuj w przeglądarce**

Otwórz `index.html`. Oczekiwane: 5 kart firm w siatce, każda z metrykami finansowymi, 3 zadaniami z kolorowymi badge'ami, zysk Firmy Delta czerwony (przychód 95k < koszty 112k), przycisk "Szczegóły →" widoczny.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: dodaj karty firm z metrykami i listą zadań"
```

---

### Task 7: Panel boczny (sidebar) — HTML + CSS + open/close

**Files:**
- Modify: `index.html` — HTML sidebara, CSS, funkcje `openSidebar`/`closeSidebar`

- [ ] **Step 1: Dodaj HTML sidebara i overlay po `<main>` (przed `<script>`)**

```html
  <!-- OVERLAY -->
  <div class="overlay" id="overlay" onclick="closeSidebar()"></div>

  <!-- SIDEBAR -->
  <aside class="sidebar" id="sidebar">
    <div class="sidebar__header">
      <h2 class="sidebar__title" id="sidebar-title">Firma</h2>
      <button class="sidebar__close" onclick="closeSidebar()">✕</button>
    </div>
    <div class="sidebar__content" id="sidebar-content">
      <!-- wypełniane przez openSidebar() -->
    </div>
  </aside>
```

- [ ] **Step 2: Dodaj CSS sidebara i overlay w `<style>`**

```css
    /* === OVERLAY === */
    .overlay {
      display: none;
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.6);
      z-index: 200;
      backdrop-filter: blur(2px);
    }
    .overlay.open { display: block; }

    /* === SIDEBAR === */
    .sidebar {
      position: fixed;
      top: 0;
      right: 0;
      height: 100vh;
      width: 420px;
      background: var(--surface);
      border-left: 1px solid var(--border);
      z-index: 300;
      display: flex;
      flex-direction: column;
      transform: translateX(100%);
      transition: transform 0.3s ease;
      overflow: hidden;
    }
    .sidebar.open { transform: translateX(0); }

    .sidebar__header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 20px 24px;
      border-bottom: 1px solid var(--border);
      flex-shrink: 0;
    }
    .sidebar__title {
      font-size: 1.15rem;
      font-weight: 700;
    }
    .sidebar__close {
      width: 32px;
      height: 32px;
      border-radius: 8px;
      background: var(--surface2);
      color: var(--text-muted);
      font-size: 0.85rem;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: color var(--transition), background var(--transition);
    }
    .sidebar__close:hover {
      color: var(--text);
      background: var(--border);
    }
    .sidebar__content {
      flex: 1;
      overflow-y: auto;
      padding: 24px;
    }
    .sidebar__content::-webkit-scrollbar { width: 4px; }
    .sidebar__content::-webkit-scrollbar-track { background: transparent; }
    .sidebar__content::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }
```

- [ ] **Step 3: Zastąp stuba `openSidebar` i dodaj `closeSidebar` w `<script>`**

```js
    // === SIDEBAR OPEN/CLOSE ===
    let activeSidebarId = null;

    function openSidebar(companyId) {
      activeSidebarId = companyId;
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      document.getElementById('sidebar-title').textContent = company.name;
      renderSidebarContent(company);
      document.getElementById('sidebar').classList.add('open');
      document.getElementById('overlay').classList.add('open');
    }

    function closeSidebar() {
      document.getElementById('sidebar').classList.remove('open');
      document.getElementById('overlay').classList.remove('open');
      activeSidebarId = null;
    }

    function renderSidebarContent(company) {
      document.getElementById('sidebar-content').innerHTML = `<p style="color:var(--text-muted)">Zawartość — Task 8</p>`;
    }
```

- [ ] **Step 4: Zweryfikuj w przeglądarce**

Kliknij "Szczegóły →" na dowolnej karcie. Oczekiwane: panel wysuwa się płynnie z prawej, overlay przyciemnia tło. Kliknięcie overlay lub ✕ zamyka panel z animacją powrotu.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: dodaj panel boczny z animacją open/close"
```

---

### Task 8: Sidebar — edycja danych firmy

**Files:**
- Modify: `index.html` — `renderSidebarContent()` z formularzem firmy + debounce save

- [ ] **Step 1: Dodaj CSS formularza sidebara w `<style>`**

```css
    /* === SIDEBAR FORM === */
    .sidebar-section { margin-bottom: 28px; }
    .sidebar-section__title {
      font-size: 0.7rem;
      text-transform: uppercase;
      letter-spacing: 0.8px;
      color: var(--text-muted);
      margin-bottom: 12px;
    }
    .form-group { margin-bottom: 12px; }
    .form-group label {
      display: block;
      font-size: 0.8rem;
      color: var(--text-dim);
      margin-bottom: 4px;
    }
    .form-input {
      width: 100%;
      background: var(--surface2);
      border: 1px solid var(--border);
      border-radius: var(--radius-sm);
      padding: 8px 12px;
      color: var(--text);
      outline: none;
      transition: border-color var(--transition);
    }
    .form-input:focus { border-color: var(--accent); }
```

- [ ] **Step 2: Zastąp `renderSidebarContent()` pełną implementacją**

```js
    // === DEBOUNCE ===
    function debounce(fn, ms) {
      let timer;
      return (...args) => { clearTimeout(timer); timer = setTimeout(() => fn(...args), ms); };
    }

    const debouncedSave = debounce(() => {
      saveData();
      renderSummary();
      renderCards();
    }, 300);

    function renderSidebarContent(company) {
      document.getElementById('sidebar-content').innerHTML = `
        <div class="sidebar-section">
          <div class="sidebar-section__title">Dane firmy</div>
          <div class="form-group">
            <label>Nazwa firmy</label>
            <input class="form-input" type="text" value="${company.name}"
              oninput="updateCompanyField('${company.id}', 'name', this.value)">
          </div>
          <div class="form-group">
            <label>Przychód miesięczny (PLN)</label>
            <input class="form-input" type="number" value="${company.revenue}"
              oninput="updateCompanyField('${company.id}', 'revenue', +this.value)">
          </div>
          <div class="form-group">
            <label>Koszty miesięczne (PLN)</label>
            <input class="form-input" type="number" value="${company.costs}"
              oninput="updateCompanyField('${company.id}', 'costs', +this.value)">
          </div>
          <div class="form-group">
            <label>Liczba pracowników</label>
            <input class="form-input" type="number" value="${company.employees}"
              oninput="updateCompanyField('${company.id}', 'employees', +this.value)">
          </div>
        </div>
        <div class="sidebar-section">
          <div class="sidebar-section__title">Zadania</div>
          <div id="sidebar-tasks"></div>
          <button class="btn-add-task" onclick="addTask('${company.id}')">+ Dodaj zadanie</button>
        </div>
      `;
      renderSidebarTasks(company);
    }

    function updateCompanyField(companyId, field, value) {
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      company[field] = value;
      if (field === 'name') document.getElementById('sidebar-title').textContent = value;
      debouncedSave();
    }
```

- [ ] **Step 3: Dodaj stub `renderSidebarTasks`, `addTask` i `btn-add-task` CSS**

```css
    .btn-add-task {
      width: 100%;
      padding: 9px;
      background: transparent;
      border: 1px dashed var(--border);
      border-radius: var(--radius-sm);
      color: var(--text-muted);
      font-size: 0.85rem;
      margin-top: 12px;
      transition: color var(--transition), border-color var(--transition);
    }
    .btn-add-task:hover {
      color: var(--accent-hover);
      border-color: var(--accent);
    }
```

```js
    function renderSidebarTasks(company) {
      document.getElementById('sidebar-tasks').innerHTML =
        '<p style="color:var(--text-muted);font-size:0.8rem">Zadania — Task 9</p>';
    }
    function addTask(companyId) { /* Task 9 */ }
```

- [ ] **Step 4: Zweryfikuj w przeglądarce**

Otwórz sidebar Firmy Alpha. Oczekiwane: 4 pola edytowalne z danymi. Zmień nazwę firmy → tytuł sidebara aktualizuje się na bieżąco, karta firmy aktualizuje się po 300ms, localStorage zapisany (sprawdź DevTools → Application).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: dodaj edycję danych firmy w sidebarze z debounce"
```

---

### Task 9: Zarządzanie zadaniami w sidebarze

**Files:**
- Modify: `index.html` — `renderSidebarTasks()`, `addTask()`, `updateTask()`, `deleteTask()`

- [ ] **Step 1: Dodaj CSS zadań w sidebarze w `<style>`**

```css
    /* === SIDEBAR TASKS === */
    .sidebar-task {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 8px 0;
      border-bottom: 1px solid var(--border);
    }
    .sidebar-task:last-child { border-bottom: none; }
    .sidebar-task input[type="checkbox"] {
      accent-color: var(--accent);
      width: 15px;
      height: 15px;
      flex-shrink: 0;
      cursor: pointer;
    }
    .sidebar-task__text {
      flex: 1;
      background: transparent;
      border: none;
      border-bottom: 1px solid transparent;
      color: var(--text);
      padding: 2px 4px;
      outline: none;
      font-size: 0.85rem;
      transition: border-color var(--transition);
    }
    .sidebar-task__text:focus { border-bottom-color: var(--accent); }
    .sidebar-task__text.done {
      text-decoration: line-through;
      opacity: 0.45;
    }
    .sidebar-task__priority {
      background: var(--surface2);
      border: 1px solid var(--border);
      border-radius: 4px;
      color: var(--text-dim);
      font-size: 0.75rem;
      padding: 2px 6px;
      outline: none;
    }
    .btn-delete-task {
      color: var(--text-muted);
      font-size: 0.9rem;
      padding: 2px 6px;
      border-radius: 4px;
      transition: color var(--transition), background var(--transition);
    }
    .btn-delete-task:hover { color: var(--red); background: rgba(239,68,68,0.1); }
```

- [ ] **Step 2: Zastąp `renderSidebarTasks()`, `addTask()` i dodaj `updateTask()`, `deleteTask()`**

```js
    function renderSidebarTasks(company) {
      const container = document.getElementById('sidebar-tasks');
      if (!company.tasks.length) {
        container.innerHTML = '<p class="tasks-empty">Brak zadań. Dodaj pierwsze!</p>';
        return;
      }
      container.innerHTML = company.tasks.map(t => `
        <div class="sidebar-task">
          <input type="checkbox" ${t.done ? 'checked' : ''}
            onchange="updateTask('${company.id}', ${t.id}, 'done', this.checked)">
          <input class="sidebar-task__text ${t.done ? 'done' : ''}" type="text" value="${t.text}"
            oninput="updateTask('${company.id}', ${t.id}, 'text', this.value)"
            onfocus="this.parentElement.style.background='rgba(99,102,241,0.05)'"
            onblur="this.parentElement.style.background=''">
          <select class="sidebar-task__priority"
            onchange="updateTask('${company.id}', ${t.id}, 'priority', this.value)">
            <option value="high"   ${t.priority==='high'   ? 'selected' : ''}>🔴 high</option>
            <option value="medium" ${t.priority==='medium' ? 'selected' : ''}>🟡 medium</option>
            <option value="low"    ${t.priority==='low'    ? 'selected' : ''}>🟢 low</option>
          </select>
          <button class="btn-delete-task" onclick="deleteTask('${company.id}', ${t.id})">✕</button>
        </div>
      `).join('');
    }

    function updateTask(companyId, taskId, field, value) {
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      const task = company.tasks.find(t => t.id === taskId);
      if (!task) return;
      task[field] = value;
      if (field === 'done') {
        // aktualizuj klasę done w sidebarze bez pełnego re-renderu
        const input = event.target;
        const textInput = input.parentElement.querySelector('.sidebar-task__text');
        textInput.classList.toggle('done', value);
      }
      debouncedSave();
    }

    function deleteTask(companyId, taskId) {
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      company.tasks = company.tasks.filter(t => t.id !== taskId);
      renderSidebarTasks(company);
      debouncedSave();
    }

    function addTask(companyId) {
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      company.tasks.push({ id: Date.now(), text: 'Nowe zadanie', priority: 'medium', done: false });
      renderSidebarTasks(company);
      debouncedSave();
    }
```

- [ ] **Step 3: Zastąp stub `toggleTaskDone` pełną implementacją**

```js
    function toggleTaskDone(companyId, taskId) {
      const company = state.companies.find(c => c.id === companyId);
      if (!company) return;
      const task = company.tasks.find(t => t.id === taskId);
      if (!task) return;
      task.done = !task.done;
      saveData();
      renderSummary();
      renderCards();
    }
```

- [ ] **Step 4: Zweryfikuj w przeglądarce**

Otwórz sidebar → kliknij "+ Dodaj zadanie" → widać nowe zadanie "Nowe zadanie". Edytuj jego tekst → zmiany widoczne. Zmień priorytet → badge zmienia kolor po zamknięciu i ponownym otwarciu (dane w localStorage). Kliknij ✕ → zadanie znika. Checkbox done → tekst przekreślony.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: dodaj pełne zarządzanie zadaniami w sidebarze"
```

---

### Task 10: Pogoda Open-Meteo

**Files:**
- Modify: `index.html` — funkcja `fetchWeather()` w `<script>`

- [ ] **Step 1: Dodaj mapę kodów pogody i funkcję `fetchWeather()` w `<script>`**

```js
    // === WEATHER ===
    const WEATHER_CODES = {
      0: ['☀️', 'Bezchmurnie'], 1: ['🌤️', 'Przeważnie słonecznie'],
      2: ['⛅', 'Częściowe zachmurzenie'], 3: ['☁️', 'Pochmurno'],
      45: ['🌫️', 'Mgła'], 48: ['🌫️', 'Mgła z szronem'],
      51: ['🌦️', 'Mżawka'], 53: ['🌦️', 'Mżawka'], 55: ['🌧️', 'Ulewna mżawka'],
      61: ['🌧️', 'Deszcz'], 63: ['🌧️', 'Umiarkowany deszcz'], 65: ['🌧️', 'Silny deszcz'],
      71: ['🌨️', 'Śnieg'], 73: ['🌨️', 'Umiarkowany śnieg'], 75: ['❄️', 'Silny śnieg'],
      80: ['🌦️', 'Przelotne opady'], 81: ['🌧️', 'Opady'], 82: ['⛈️', 'Gwałtowne opady'],
      95: ['⛈️', 'Burza'], 96: ['⛈️', 'Burza z gradem'], 99: ['⛈️', 'Ciężka burza']
    };

    async function fetchWeather() {
      const el = document.getElementById('weather');
      try {
        const res = await fetch(
          'https://api.open-meteo.com/v1/forecast?latitude=51.77&longitude=19.46&current_weather=true'
        );
        if (!res.ok) throw new Error('HTTP ' + res.status);
        const data = await res.json();
        const cw = data.current_weather;
        const [icon, desc] = WEATHER_CODES[cw.weathercode] || ['🌡️', 'Nieznana pogoda'];
        el.innerHTML = `
          <span class="weather-icon">${icon}</span>
          <span class="weather-text">Łódź &nbsp;${Math.round(cw.temperature)}°C &nbsp;·&nbsp; ${desc}</span>
        `;
      } catch {
        el.innerHTML = `<span class="weather-icon">☁️</span><span class="weather-text">Brak danych pogodowych</span>`;
      }
    }

    fetchWeather();
    setInterval(fetchWeather, 10 * 60 * 1000); // co 10 minut
```

- [ ] **Step 2: Zweryfikuj w przeglądarce**

Otwórz `index.html`. Oczekiwane: topbar po prawej pokazuje emoji + temperaturę + opis np. "Łódź  14°C · Częściowe zachmurzenie". Sprawdź DevTools → Network → request do `api.open-meteo.com` ze statusem 200.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: dodaj pogodę Łódź z Open-Meteo API"
```

---

### Task 11: Responsywność

**Files:**
- Modify: `index.html` — media queries w `<style>`

- [ ] **Step 1: Dodaj media queries na końcu sekcji `<style>`**

```css
    /* === RESPONSIVE === */
    @media (max-width: 1200px) {
      .cards-grid { grid-template-columns: repeat(2, 1fr); }
    }

    @media (max-width: 768px) {
      .topbar {
        padding: 0 16px;
        height: auto;
        flex-wrap: wrap;
        gap: 8px;
        padding-top: 12px;
        padding-bottom: 12px;
      }
      .topbar__clock { order: 3; width: 100%; text-align: center; }
      .topbar__weather { order: 2; }
      .cards-grid {
        grid-template-columns: 1fr;
        padding: 16px;
      }
      .summary { padding: 20px 16px 0; }
      .sidebar { width: 100vw; }
      .summary__stats {
        grid-template-columns: repeat(2, 1fr);
      }
    }

    @media (max-width: 400px) {
      #clock-time { font-size: 1.1rem; }
      .summary__greeting { font-size: 1.3rem; }
      .card__metrics { grid-template-columns: repeat(2, 1fr); }
    }
```

- [ ] **Step 2: Zweryfikuj w przeglądarce**

DevTools → Toggle Device Toolbar (Ctrl+Shift+M). Sprawdź przy 1400px (3 kolumny), 900px (2 kolumny), 375px (1 kolumna, sidebar pełna szerokość, topbar wrapia się).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: dodaj responsywny layout (mobile/tablet/desktop)"
```

---

### Task 12: Polish UI — animacje, scrollbar, finishing touches

**Files:**
- Modify: `index.html` — drobne poprawki CSS i UX

- [ ] **Step 1: Dodaj animację wejścia kart i loading state pogody w `<style>`**

```css
    /* === ANIMATIONS === */
    @keyframes fadeInUp {
      from { opacity: 0; transform: translateY(16px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    .company-card {
      animation: fadeInUp 0.3s ease both;
    }
    .company-card:nth-child(1) { animation-delay: 0.05s; }
    .company-card:nth-child(2) { animation-delay: 0.10s; }
    .company-card:nth-child(3) { animation-delay: 0.15s; }
    .company-card:nth-child(4) { animation-delay: 0.20s; }
    .company-card:nth-child(5) { animation-delay: 0.25s; }

    /* scrollbar global */
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: transparent; }
    ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }
    ::-webkit-scrollbar-thumb:hover { background: var(--text-muted); }

    /* footer info */
    .page-footer {
      text-align: center;
      padding: 20px;
      color: var(--text-muted);
      font-size: 0.75rem;
      border-top: 1px solid var(--border);
    }
```

- [ ] **Step 2: Dodaj `<footer>` po `<aside>`**

```html
  <footer class="page-footer">
    Business Dashboard · dane zapisywane lokalnie · pogoda: Open-Meteo
  </footer>
```

- [ ] **Step 3: Zweryfikuj w przeglądarce**

Przeładuj stronę. Oczekiwane: karty wjeżdżają po kolei z lekkim opóźnieniem, niestandardowy scrollbar w sidebarze, footer na dole strony.

- [ ] **Step 4: Commit finalny**

```bash
git add index.html
git commit -m "feat: dodaj animacje wejścia kart i finishing touches UI"
```

---

## Weryfikacja końcowa (manualna)

- [ ] Zegar tyka co sekundę, data po polsku
- [ ] Pogoda Łódź ładuje się z Open-Meteo (sprawdź Network tab)
- [ ] Sekcja "Dzień dobry" pokazuje poprawne sumy
- [ ] 5 kart firm widocznych, Firma Delta ma czerwony zysk
- [ ] Checkbox done na karcie → tekst przekreślony + `renderSummary()` aktualizuje liczbę zadań
- [ ] Przycisk "Szczegóły →" otwiera sidebar z animacją
- [ ] Edycja nazwy firmy w sidebarze → tytuł sidebara + karta aktualizują się
- [ ] Zmiana przychodów/kosztów → zysk na karcie przelicza się, summary zaktualizowane
- [ ] Dodaj zadanie → pojawia się w liście
- [ ] Usuń zadanie → znika
- [ ] Odśwież stronę → wszystkie zmiany zachowane (localStorage)
- [ ] Sprawdź na szerokości 375px (mobile)
