# Instructions for AI Coding Agents

## What This Repo Is

A **documentation-only repository** for **WarungHwa**, a FastAPI + Jinja2 POS (Point of Sale) web app for warung owners in Indonesia. There is no source code here — only specs, design docs, and conventions that the implementation project reads from.

`flows/navigation-flow.md` is the canonical route list. Do not invent routes.

---

## Reading Order Before Any Work

Always read in this order; later steps depend on earlier ones:

1. `product/overview.md` — product summary and terminology
2. `product/scope.md` — in-scope features and priorities
3. `technical/architecture.md`, `technical/project-structure.md` — tech stack, folder structure, uv setup, data flow, auth model
4. `technical/frontend-conventions.md` — component, form, API, and testing rules
5. `design/design-system.md`, `design/color-palette.md`, `design/typography.md` — design tokens
6. The specific page spec in `pages/` for the page being worked on
7. Related user flows in `flows/`

---

## Key Architecture Facts

- **Stack:** FastAPI + Python 3.11+ + MySQL 8.0+ + SQLModel (ORM) + Alembic (migrations) + Jinja2 (SSR templates) + TailwindCSS 3.x + uv. Full SSR — no React/Vue/SPA.
- **Multi-tenant:** Every model (products, inventory, transactions) has a FK to `Warung`. Every route handler **must** filter by the logged-in user's warung to prevent data leakage between tenants.
- **Auth:** Session-based via signed cookie (`itsdangerous`). All protected routes use a FastAPI dependency (`get_current_user`) that reads and verifies the cookie; on failure it redirects to `/login`. No self-register — accounts are created manually by the vendor.
- **CSRF:** `starlette-csrf` middleware applied to all form POSTs.
- **Form parsing:** HTML forms use `application/x-www-form-urlencoded`; requires `python-multipart`. Validation via Pydantic schemas.
- **Icons:** Heroicons via CDN SVG inline — copy-paste langsung ke template Jinja2. Jangan pakai icon library lain.
- **Export (PDF/Excel):** Client-side only. Server returns JSON from a dedicated endpoint; `jsPDF` and `SheetJS` generate the file in the browser via CDN. Do not add server-side export logic.
- **Receipt printing:** `window.print()` in browser, optional for the user.
- **Deployment:** Undecided — scope is a local-run final project. Do not add deployment config unless asked.

---

## Approved Routes

The full method + route table lives in `flows/navigation-flow.md` — that file is the source of truth. Summary of page routes:

| Route | Page | Auth |
|---|---|---|
| `/login` | Login | Public |
| `/profile` | User profile (view + edit) | Authenticated |
| `/` | Kasir (POS) | Authenticated |
| `/products` | Product + inventory management | Authenticated |
| `/products/:id` | Product detail + edit | Authenticated |
| `/products/:id/stock` | Stock history for one product | Authenticated |
| `/stock/history` | Stock history, all products | Authenticated |
| `/discounts` | Discount management | Authenticated |
| `/discounts/:id` | Discount detail + edit | Authenticated |
| `/transactions` | Transaction history | Authenticated |
| `/transactions/:id` | Transaction detail | Authenticated |
| `/api/products/export` | JSON for product/stock export | Authenticated |
| `/api/transactions/export` | JSON for transaction export | Authenticated |

Conventions: routes are **English, lowercase, no trailing slash**. Delete is a `POST` to `:id/delete` (HTML forms cannot send `DELETE`). New/edit forms open in a **modal** on the list page — there are no `/new` routes. Stock is only changed through a mutation record (`POST /products/:id/stock` or a sale) — never written directly. In FastAPI code, path params are written `{id}`, not `:id`.

---

## Page Specs

All page specs live in `pages/`, grouped in one folder per feature:

| File | Route |
|---|---|
| `app-layout.md` | Shared authenticated shell |
| `account/login-page.md` | `/login` |
| `account/profile-page.md` | `/profile` |
| `kasir/kasir-page.md` | `/` |
| `products/products-page.md` | `/products` |
| `products/product-detail-page.md` | `/products/:id` |
| `products/product-stock-history-page.md` | `/products/:id/stock` |
| `inventory/stock-history-page.md` | `/stock/history` |
| `discounts/discounts-page.md` | `/discounts` |
| `discounts/discount-detail-page.md` | `/discounts/:id` |
| `transactions/transactions-page.md` | `/transactions` |
| `transactions/transaction-detail-page.md` | `/transactions/:id` |

Folder convention: one folder per feature; filename is `<page>-page.md`; only `app-layout.md`, `README.md`, and `_page-template.md` stay at the `pages/` root. Cross-cutting docs stay outside `pages/` (`flows/`, `technical/`, `design/`, `product/`).

Shared UI patterns (list pages): table on desktop, cards on mobile; server-side search + pagination (10/25/50); create/edit in a **modal**; delete via confirmation dialog → `POST :id/delete`; money is integer Rupiah.

---

## Page Status Workflow

`Draft` → `Ready for Design` → `Ready for Development` → `In Progress` → `In Review` → `Done`

Use `Blocked` for any page with an unresolved open decision that blocks work.

---

## Writing and Editing Documentation

- **One file per page.** Copy `pages/_page-template.md` for every new page spec.
- `pages/account/login-page.md` is the reference example of a fully completed spec.
- `pages/app-layout.md` documents the shared authenticated shell (sidebar + topbar).
- Separate settled decisions from open ones. Use the `Open Decisions` table in each file.
- Do not invent endpoints, request/response schemas, or business rules. Mark unknowns as `TBD` and add an open decision row.
- Do not repeat global conventions in page specs — reference `technical/frontend-conventions.md` instead.

---

## Open Decisions to Know About

- **Deployment** (`technical/architecture.md`): local only, no decision yet.
- **WhatsApp number for "Laporkan Masalah"** (`pages/app-layout.md`): medium is settled (WhatsApp via `wa.me`), the vendor number is not.
- **Profile page fields** (`pages/account/profile-page.md`): spec is Draft — editable fields not yet decided.

## Settled Decisions (do not reopen)

- Icons: **Heroicons** outline 24px, SVG inline. Sidebar mapping: `shopping-cart` Kasir, `cube` Produk, `tag` Diskon, `document-text` Transaksi, `arrows-right-left` Riwayat Stok.
- Sidebar has **5 menu items** (Kasir, Produk, Diskon, Transaksi, Riwayat Stok).
- There is **no `/reports` page** — stock is viewed on `/products`, sales history on `/transactions`. Export lives on those two pages.
- Intended destination: only internal relative paths (`/…`, not `//…`); otherwise redirect to `/`.
- Product type is a **multi-tenant web app**, not SaaS.

---

## Before Completing Any Documentation Task

1. Check the `Open Decisions` section of every file touched.
2. Verify acceptance criteria are testable and unambiguous.
3. Confirm no global rule is duplicated into a page spec.
4. Update the page `Status` field if the spec moved to a new stage.
5. Never mark a page `Done` with unmet acceptance criteria.

---

## Reporting Format

- Summary
- Files changed
- Decisions/assumptions made
- Open decisions raised or resolved
- Acceptance criteria status
- Remaining issues
