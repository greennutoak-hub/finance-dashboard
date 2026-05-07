# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

No build step. Open `index.html` directly in a browser, or serve it:

```
python -m http.server 8000
```

There are no npm scripts, tests, or linting tools.

## Architecture

Single-file vanilla HTML/CSS/JS application (`index.html`). All logic, styles, and markup live in one file. No framework, no bundler.

**External dependencies (CDN only):**
- Chart.js v4.4.1 — bar and doughnut charts on the Overview tab
- Anthropic API (`claude-sonnet-4-20250514`) — receipt/slip OCR via base64 image upload
- Yahoo Finance via `allorigins` CORS proxy — Thai (`.BK`) and US stock prices
- CoinGecko public API — crypto prices

## State & Persistence

All state lives in a single global `state` object and is persisted entirely to `localStorage` under the key `financeData`. There is no backend.

```js
let state = {
  transactions: [],           // {id, type, cat, desc, amount, date}
  stocks: {th: [], us: [], crypto: []},
  assets: [],                 // {id, type, name, value}
  prices: {},                 // symbol -> price, symbol_chg -> % change
  lastPriceUpdate: null
};
```

Call `save()` after any mutation; `load()` restores from localStorage on startup.

## Key Functions

| Function | Purpose |
|---|---|
| `showPage(name)` | Tab switching; triggers the relevant render function |
| `renderOverview()` | Dashboard: net worth, charts, recent transactions |
| `renderTxTable()` | Filtered transaction list |
| `renderPortfolio()` / `renderStockTable()` | Portfolio views |
| `renderAssets()` | Net-worth asset allocation bars |
| `refreshPrices()` | Fetches Yahoo Finance + CoinGecko, updates `state.prices` |
| `handleReceiptImages(e)` | Converts images to base64, calls Anthropic API for OCR |
| `handleCSV(e)` | Parses bank statement CSV into transactions locally |

## UI & Localization

- All UI text is in **Thai**
- Primary currency is **THB**; US stocks and crypto display in **USD**
- Dark theme defined via CSS variables (`--bg`, `--text`, `--green`, `--red`, `--blue`, `--amber`, `--purple`)

## API Key Handling

The Anthropic API key is entered by the user in the browser and used directly in client-side `fetch` calls. There is no server to proxy requests. Do not hard-code any API key into the file.
