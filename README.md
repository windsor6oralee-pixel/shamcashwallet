# ShamCash Wallet

Local workspace for the ShamCash Wallet app. The live product is built in WeWeb; this folder is the Cursor root for MCP-driven work, notes, and any local files.

## WeWeb

- Workspace: Dallah driving school's workspace (`81769363-74be-43f5-9955-491d5d61f976`)
- Project: My project (`218ecab6-4825-4e99-a5ed-657c480051f3`)
- Editor: https://patterned-plot-editor.weweb.io
- Backend: WeWeb Database, Auth, and Storage are installed

### Pages

| Page | Path |
| --- | --- |
| Home | `/home` (home page) |
| Login | `/login` |
| Signup | `/signup` |
| Forgot password | `/forgot-password` |
| 404 | `/404` |
| Financing (hub) | `/financing` |
| Apply Financing | `/financing/apply` |
| Track Financing | `/financing/track` |

### Backend tables

`wallets`, `transactions`, `loan_applications`, `financing_catalog`, `application_events`, `system_settings`

### Table views

| View | uid | Access |
| --- | --- | --- |
| published_financing_catalog | `b50a7af2-77a4-4dbf-9a57-6f8b909106c3` | public |
| financing_lifecycle_events | `69894fc0-e404-4ddd-9713-a28c6b883508` | public |
| financing_application_timeline | `76335532-c045-492c-9586-9d40d4949604` | public, **requires `reference_code`** (filter always applied) |
| system_settings_view | `19cbf492-56cd-4361-a2ac-84bbaa0daed9` | — |
| recent_transactions | `86373b0a-8100-4500-84ad-25bcfdb634f6` | — |
| all_wallets | `a5f6a66b-44aa-4587-aaba-972b52b3b507` | — |
| financing_applications_view | `52123c2d-1895-4461-ac24-171ab57e00a5` | authenticated (admin) |

### Design System Phases

- **Phase A (2026-10-22, done)** — additive only, zero visual change on Home:
  - +11 color tokens (status success/error/info/warning + tints, disabled bg/fg, focus ring).
  - +5 shadow tokens (elevation-card/-modal, focus rings).
  - +4 border tokens (subtle-1px, focus-indigo, error-crimson, success-teal).
  - +6 typography tokens (button-primary-label, input-label-ar, input-value, input-helper-ar, badge-ar, stage-chip-ar).
  - +15 opt-in classes under `shamcash/buttons/states`, `shamcash/inputs/states`, `shamcash/badges`, `shamcash/banners`.
  - Guidelines appended: privacy mode rules, interaction states, RTL/mobile/desktop, data safety, a11y.
- **Phase B (2026-10-22, done)** — 8 no-code components extracted from Home (Top Currency Bar, Bottom Navigation, Track Financing Modal, Eligibility Lifecycle Modal, Dual QR Modal, Apply Financing Modal, Service Tile, Action Card) + 20 component workflows. The 4 original Home modals and their backdrop are untouched.
- **Phase C (2026-10-22, done)** — the financing journey:
  - 2 new no-code components (Page Header, Step Indicator) → 10 components in total.
  - 3 pages published: `/financing` (hub), `/financing/apply` (4-step wizard), `/financing/track` (status + timeline).
  - 15 frontend workflows, 1 new public table view (`financing_application_timeline`, reference-code scoped), 20 project globals (15 `apply_*`, 5 `track_*`), 2 global formulas (`applyCatalogTiers`, `applyCatalogRow`).
  - +2 typography tokens (card-title, body-strong) and +13 classes → 39 classes.
  - Guidelines appended with the Phase B and Phase C sections (never replaced).

### Local reference

`.weweb-components.md` is the authoritative log of every component, page, variable, formula, table view and workflow UID.

