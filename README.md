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

### Backend tables

`wallets`, `transactions`, `loan_applications`, `financing_catalog`, `application_events`, `system_settings`

### Design System Phases

- **Phase A (2026-10-22, done)** — additive only, zero visual change on Home:
  - +11 color tokens (status success/error/info/warning + tints, disabled bg/fg, focus ring).
  - +5 shadow tokens (elevation-card/-modal, focus rings).
  - +4 border tokens (subtle-1px, focus-indigo, error-crimson, success-teal).
  - +6 typography tokens (button-primary-label, input-label-ar, input-value, input-helper-ar, badge-ar, stage-chip-ar).
  - +15 opt-in classes under `shamcash/buttons/states`, `shamcash/inputs/states`, `shamcash/badges`, `shamcash/banners`.
  - Guidelines appended: privacy mode rules, interaction states, RTL/mobile/desktop, data safety, a11y.
- **Phase B (pending approval)** — extract 4 Home modals as popups + build no-code components (Header, Bottom Nav, Action Card, Service Tile), then build `/financing/apply`, `/financing/track`, `/financing`.
