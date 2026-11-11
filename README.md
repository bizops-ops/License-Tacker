# License Tracker — Zippee BizOps

A single-file, zero-build web app for tracking regulatory licenses and permits across a network of stores (dark stores / warehouses). It tracks the full lifecycle of each license: document collection, vendor handoff, application, issuance, validity, expiry and renewal.

No backend, no build step, no `npm install` — just open `index.html`.

---

## Features

| Section | What it does |
|---|---|
| **📊 Dashboard** | Compliance % overall, per store, and per license type. Search/filter by store, city and brand. Drill into a store → license to fill in its workflow. Expiry alert banner for expired / <30-day licenses. |
| **📅 Expiry** | Spreadsheet-style grid of every store × license with Applied / Issued / Validity / Expiry / Days-left / Renew columns. Expiry is auto-calculated (`Issue Date + Validity months`). Clickable KPI filters: All, Expired, <30d, 30–90d, 90d+, No Data. |
| **📜 History** | Archive of all past renewals (cycle number, applied, issued, validity, expiry, renewed-on). Filter by brand and license type. |
| **💰 Fees** | Editable reference table of indicative fees per state and license type. Add or remove states. |
| **🏪 Master** | Store master: edit name / city / brand, toggle which licenses apply to each store, delete stores. |
| **📁 Docs** | Password-gated link out to the shared Google Drive folder of documents. |
| **➕ Add** | Add a new store and choose its applicable licenses. |

### Default license types

Trade License · FSSAI · Shop & Establishment · CLRA · 24x7 Ops · Fire NOC · PTEC

You can add more (e.g. GST Registration, Pollution NOC) from the **Add License** tile on the Dashboard — new types appear across the Dashboard, Expiry grid, Master, Fees and Add Store screens.

### Per-license workflow fields

Each store/license pair tracks 16 fields, grouped as:

- **📋 Documents** — collected, verified, verified by, Google Drive link
- **🏢 Vendor** — sent to vendor, vendor response (Awaiting / Applied / Responded / Needs More Docs / Rejected), additional docs needed
- **📝 Application** — applied on, application no., tentative date
- **💰 Fee Reference** — amount (₹)
- **✅ Status** — issue date, license received, validity (months), action taken, remarks

Compliance % for a license = filled fields ÷ total fields. A store's % is the average across its applicable licenses. Use **Confirm & Save** to lock a license's fields (click **Edit** to unlock).

### Renewals

Clicking **🔄 Renew** in the Expiry grid:
1. Archives the current Applied / Issued / Validity / Expiry into **History** as a cycle.
2. Replaces them with the new dates (new expiry is previewed live).
3. Resets the "Confirmed" lock for that license.

---

## Getting started

### Run locally

```bash
git clone <your-repo-url>
cd <your-repo>
# Option 1: just double-click index.html
# Option 2: serve it
python3 -m http.server 8000
# open http://localhost:8000
```

> The file must be saved as `index.html`. It loads React 18, ReactDOM and Babel Standalone from cdnjs and the Inter font from Google Fonts, so an internet connection is required.

### Deploy on GitHub Pages

1. Push `index.html` to your repo.
2. Go to **Settings → Pages**.
3. Set **Source** to the `main` branch, root folder.
4. Your tracker will be live at `https://<username>.github.io/<repo>/`.

---

## Tech stack

- **React 18** (UMD build via CDN)
- **Babel Standalone** — JSX is compiled in the browser
- **Plain CSS-in-JS** inline styles + a small `<style>` block
- **localStorage** for persistence
- No bundler, no dependencies to install

---

## Data & persistence

All data lives in the browser's `localStorage`, under these keys:

| Key | Contents |
|---|---|
| `lt_st` | Store list (id, name, city, brand, applicable licenses) |
| `lt_ld` | License workflow data, keyed by store → license |
| `lt_fe` | State fee reference table |
| `lt_cf` | "Confirmed / locked" flags |
| `lt_hi` | Renewal history |
| `lt_cL` | Custom license types |

**Important implications:**

- Data is **per browser, per device** — it is not shared between users.
- Clearing site data wipes everything. There is currently no export/import feature, so back up regularly (e.g. copy the `lt_*` keys from DevTools → Application → Local Storage) or consider adding an export button.
- Initial store data is seeded from the `ST` array in the source; edits are saved over it.

---

## ⚠️ Security notes — please read before publishing

This is a **client-side-only** app. Anything in `index.html` is visible to anyone who can load the page.

- **Login credentials are hardcoded** in the `US` constant (and again in the **Docs** gate). This is a UI gate, **not real security** — anyone can read them via "View Source".
- **Change the defaults** and do **not** commit real credentials to a public repo.
- The **Google Drive folder URL** (`GD` constant) is also embedded. Make sure the Drive folder's own sharing permissions are locked down — don't rely on the Docs gate to protect it.
- If this contains real compliance data or internal store names, keep the repository **private**, or move auth to a real backend (e.g. Firebase Auth, Supabase, or an SSO proxy).

---

## Customising

| To change… | Edit… |
|---|---|
| Stores shipped by default | `ST` array (`[id, name, city, brand, "lic,keys"]`) |
| Default license types, icons, colors | `DEF_L` array |
| Workflow fields | `W` array (`t`: `ck` checkbox, `tx` text, `ta` textarea, `dt` date, `nm` number, `ur` URL, `sl` select) |
| Fee reference defaults | `FI` object |
| Login users | `US` object |
| Drive folder link | `GD` constant |

Expiry thresholds (30 / 90 days) are in the `DT` component and the Expiry KPI logic.

---

## Roadmap ideas

- Export / import data as JSON or CSV
- Real authentication and a shared backend (Firebase / Supabase)
- Email or Slack alerts for upcoming expiries
- Bulk edit and bulk renew
- Per-store document uploads instead of a single Drive link

---

## Project structure

```
.
├── index.html   # the entire app (HTML + CSS + React/JSX)
└── README.md
```

## License

Add your preferred license here (e.g. MIT) — or mark as internal / proprietary.
