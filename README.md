# Spendlens — Multi-currency Expense Dashboard

A single-page expense dashboard that converts 20 sample expenses across 10 currencies into USD and summarises them for a monthly board report. It shows category totals, an overall total, top merchants, a sortable/filterable expense table, an add-expense form, and a "what-if" EUR rate slider.

**Live URL:** https://mayank08929.github.io/Dozee/

## Run it locally

It's a static site with **no build step and no runtime dependencies**. React is pre-compiled and inlined directly into `index.html`, so you can simply **double-click `index.html`** to open it — or serve the folder if you prefer:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```

Nothing is fetched from a CDN at runtime (only the web fonts, which fall back to system fonts if unavailable), so the page renders even fully offline.

## File structure

| Path | What it is |
|------|------------|
| `index.html` | The entire app — styles, dataset, currency logic, and all React components, pre-compiled to plain JS and inlined. Self-contained: no runtime CDN, no build. |
| `data.js` | The same `RATES` + `EXPENSES` exported as an ES module, for anyone who wants to import the dataset cleanly. |
| `docs/ceo-brief.md` | Plain-English one-pager for the CEO (also rendered in-app under Documentation). |
| `docs/edge-cases.md` | Adversarial failure-mode analysis (also rendered in-app). |
| `README.md` | This file. |

## How the currency logic works

Rates are quoted as **units of currency per 1 USD** (base USD), so the conversion is `USD = amount / rate[currency]`. All conversion goes through one pure function, `toUSD(amount, currency, rates)`, that every view shares — so the table, the category summary, the merchant ranking, and the what-if slider can never drift apart. Figures are rounded to 2 decimals at display/total boundaries only, never on intermediate sums, so category totals reconcile to the overall total.

## Known limitations · what I'd fix with 4 more hours

- **No persistence** — added expenses live in memory and reset on reload. I'd add `localStorage` first (cheap), then a real backend.
- **No build pipeline.** React is inlined as pre-compiled JS, which keeps deployment trivial but means no code-splitting or tree-shaking. I'd move to a Vite build before this grows past a few views.
- **Money is stored as floats.** For production I'd switch to integer minor units (cents).
- **Mobile** uses a slightly cramped table; a card-per-expense layout would be better on the smallest screens.

## Assumptions for the next developer

- Rates are "units per 1 USD" (base USD), confirmed by the snapshot (e.g. INR 83.47, EUR 0.9201).
- The 2026-05-01 snapshot is the single source of truth; no live rates are fetched, per the brief.
- Each seed merchant is unique, but merchant totals still sum defensively in case of duplicates.

## Reference figures (base rates)

- Overall total: **$1,992.15**
- Category totals: Travel $1,590.95 · Software $204.49 · Food $129.66 · Entertainment $67.05
- Top 3 merchants: Emirates flight $503.74 · Qantas $339.60 · JR Rail Pass $325.06
