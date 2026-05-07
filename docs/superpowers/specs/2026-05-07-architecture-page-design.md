# Architecture Page — Design Spec

**Date:** 2026-05-07
**Status:** Approved

## Goal

A single, self-contained HTML page that documents the Factory Inventory Management System's architecture for new contributors and workshop participants. Visual-first, comprehensive, openable directly in any browser without a server.

## Output

- **File:** `docs/architecture.html`
- **Format:** Single self-contained HTML file with inline CSS and inline SVG — no external assets, no JavaScript, no build step
- **Audience:** Developers and workshop participants exploring the codebase for the first time

## Style

Match the app's existing design system:

- **Palette:** Slate/gray (`#0f172a` primary, `#64748b` secondary, `#e2e8f0` borders, `#f8fafc` page background)
- **Status accents:** green / blue / yellow / red (consistent with app)
- **Typography:** System font stack (`-apple-system, BlinkMacSystemFont, "Segoe UI", ...`)
- **Layout:** CSS Grid for section layout
- **Diagrams:** Custom inline SVG (consistent with "Custom SVG, CSS Grid for layouts" project convention)
- **No emojis** (per project design system rule)

## Page Sections

In order, top to bottom:

### 1. Header
- Title: "Factory Inventory Management — Architecture"
- One-line description from README
- Tech stack badges: Vue 3, Vite, FastAPI, Python, JSON

### 2. System Architecture Diagram
SVG showing the 3-tier system, left to right or top to bottom:
- **Browser tier** — Vue 3 SPA on port 3000
- **API tier** — FastAPI server on port 8001
- **Data tier** — JSON files in `server/data/` loaded into memory

Show arrows for HTTP requests (filter query params) and JSON file reads.

### 3. Tech Stack
Three cards (CSS Grid, 3 columns):

- **Frontend:** Vue 3 (Composition API), Vite, JavaScript, custom SVG charts
- **Backend:** Python 3, FastAPI, Pydantic, uvicorn
- **Data:** JSON files, in-memory loading via `mock_data.py`, no database

### 4. Data Flow Diagram
SVG showing one round trip end-to-end:

`User selects filter` → `Vue updates ref` → `client/src/api.js builds query params` → `FastAPI route` → `mock_data filter function` → `Pydantic model validation` → `JSON response` → `Vue ref updates` → `computed property derives view data` → `template re-renders`

### 5. Frontend Structure
Table or list with all assets:

- **Views (7):** Dashboard, Inventory, Orders, Demand, Backlog, Spending, Reports — one-line purpose each
- **Components (9):** FilterBar, BacklogDetailModal, CostDetailModal, InventoryDetailModal, ProductDetailModal, ProfileDetailsModal, ProfileMenu, LanguageSwitcher, TasksModal — one-line purpose each
- **Other:** `api.js` (HTTP client), `App.vue` (root + global styles), `composables/`, `utils/`, `locales/`, `main.js`

### 6. Backend Structure
Brief list:

- `main.py` — FastAPI app, all route definitions, CORS
- `mock_data.py` — Loads JSON, applies filters
- `generate_data.py` — Seed/regenerate JSON
- `data/*.json` — 7 data files: `inventory`, `orders`, `demand_forecasts`, `backlog_items`, `purchase_orders`, `spending`, `transactions`

### 7. API Endpoints
Table: Method | Path | Filters supported | Returns (verified against `server/main.py`)

**Inventory & Orders:**
- `GET /api/inventory` — warehouse, category — list of inventory items
- `GET /api/inventory/{item_id}` — none — single inventory item (404 if missing)
- `GET /api/orders` — warehouse, category, status, month — list of orders
- `GET /api/orders/{order_id}` — none — single order (404 if missing)

**Dashboard & Operations:**
- `GET /api/dashboard/summary` — warehouse, category, status, month — summary stats
- `GET /api/demand` — none — demand forecasts
- `GET /api/backlog` — none — backlog items (with `has_purchase_order` flag)

**Spending:**
- `GET /api/spending/summary` — none — spending summary
- `GET /api/spending/monthly` — none — monthly spending breakdown
- `GET /api/spending/categories` — none — spending by category
- `GET /api/spending/transactions` — none — recent transactions

**Reports:**
- `GET /api/reports/quarterly` — none — quarterly performance (calculated from orders)
- `GET /api/reports/monthly-trends` — none — month-over-month trends

**Meta:**
- `GET /` — none — API metadata (name, version)

### 8. Filter System
Explain the 4 cross-cutting filters and which endpoints honor each:

- **Time Period (`month`)** — orders, dashboard summary
- **Warehouse** — inventory, orders, dashboard summary
- **Category** — inventory, orders, dashboard summary
- **Order Status** — orders, dashboard summary

Note: inventory has no time dimension (per CLAUDE.md "Common Issues #4").

## Out of Scope

- Interactive elements (no JS)
- External CSS/JS dependencies
- Embedded screenshots beyond what's already in `docs/`
- Live API calls or runtime data
- Auto-generation from code (this is a hand-maintained doc snapshot)

## Trade-offs

**Self-contained single file** vs split CSS/JS: chose self-contained because it's a one-off documentation artifact, not a maintained site. Easier to share, version, and view offline.

**Comprehensive reference** vs minimal one-pager: chose comprehensive (per user) so workshop participants can use it as a real reference document.

## Final Step

After file is created, open it in the user's default browser via `xdg-open` (Linux).
