---
WorkflowId: WebApp_Structure-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
ParentWorkflowId: WebApp_Structure
Status: Draft
Version: 26.00.03
LastUpdated: 2026-01-19
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Target:
    - Format: "AppName"
  Description: "REQUIRED"
Outputs:
  - "Resolved AppRoot + required folders/files list"
NonNegotiables:
  - "DescriptionRequired"
...

# [0] Purpose
App root resolve + scaffold plan.

---

# [1] Deterministic Root (SSOT)
- Version: `26.00.00`
- Theme: `{AppName}_Basic`

Root:
- `AppRoot = /Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`

Minimum folders:
- `{AppRoot}/User/`
- `{AppRoot}/User/event/`
- `{AppRoot}/User/layout/`
- `{AppRoot}/User/screen/`
- `{AppRoot}/User/section/`
- `{AppRoot}/User/component/`
- `{AppRoot}/User/css/`
- `{AppRoot}/User/js/` (reserved; local JS files forbidden)

Minimum required files (User root):
- `{AppRoot}/User/index.php` (WebApp entry)
- `{AppRoot}/User/app.php` (WebApp aggregator)
- `{AppRoot}/User/layout.php` (layout aggregator)
- `{AppRoot}/User/css.php` (CSS aggregator)
- `{AppRoot}/User/js.php` (JS aggregator)
- `{AppRoot}/User/component.php` (component registry endpoint)

Minimum required files (User/css):
- `{AppRoot}/User/css/0.css` (base variables + shared global css; loads first)

---

# [1.1] Naming Normalization (Dot Prefix) — NON-NEGOTIABLE
All WebApp artifacts created by Structure must follow `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`.

Rules:
- Required basename prefix is `AppName.` (dot prefix).
- If the provided basename already starts with `AppName.` => keep as-is (NO double prefix).
- If it starts with legacy `AppName_` => normalize to `AppName.` (only the first separator).
- If no prefix present => normalize to `AppName.<RawName>`.

Forbidden (double basename):
- `AppName.AppName.X.php`
- `AppName.AppName.X.css`

Entry file invariants (structure-only, no business logic):
- All required files start with `<?php Access(); ?>`.
- `index.php` must call `AppHead(...)` and include `layout.php` (entry must be runnable).
- `css.php` must aggregate folder css (e.g. `RequireAll(__DIR__.'/css','css')`).
- `js.php` must exist but must NOT aggregate/include any local JS (no `RequireAll(__DIR__.'/js','js')`).
  - WebApp JS policy: custom JS in WebApp is forbidden.
  - If app needs JS features, implement them in WebSDK Module JS or WebOS Engine, and load via `AppHead(... WebOS_manifest ...)` (`modules` + `libraries`).
- `layout.php` must include layout parts from `User/layout/`.
  - Layout part naming SSOT: `{AppName}.<LayoutPartName>.php` (example: `{AppName}.Nav.php`).
- `component.php` must aggregate component templates from `User/componentt/` and support optional JSON export (e.g. `?encode=json`).

CSS invariants (structure-only, no business logic):
- `User/css/0.css` must define `:root{...}` CSS variables (colors + shared theme variables) and contain reusable global CSS used across the app (baseline layout/screen styles, common utilities).
- Variable names are SSOT and must not be renamed. Required variable keys (minimum set):
  - `--0`
  - `--a`, `--aa`, `--aat`, `--aaa`, `--lgaa`, `--at`
  - `--b`, `--bt`, `--b-t`, `--tb`
  - `--c`, `--c0`, `--c1`
  - `--bc`, `--bg`, `--hover`
  - `--b-t22`, `--b-t44`, `--b-t77`, `--b-t88`, `--b-t95`
  - `--bor`
  - `--btn`, `--btn1`, `--btn2`, `--btn3`, `--btn4`
  - `--bt-bx`
  - `--bs`, `--bss`, `--bs-b`, `--bs-bb`, `--bs-bs`, `--bs-bt`, `--bs-bl`, `--bs-br`
  - `--lg`, `--lg1`, `--lg-bl`, `--lg-b`, `--lg-b1`

CSS variable dictionary (what to change vs what to keep):
- Keys are stable across all apps; only values change per theme.
- `--0`: fully transparent (utility/placeholder value).
- `--a`: primary accent (links, highlighted text, important accents).
- `--aa`: strong accent (button/nav backgrounds, active indicators).
- `--aat`: accent tint (hover/selected background where `--aa` would be too strong).
- `--aaa`: accent gradient (used for toggles/primary gradient surfaces).
- `--lgaa`: alias/secondary name for accent gradient.
- `--at`: accent translucent overlay (soft highlight/overlay use-cases).
- `--b`: app base background (body/page background).
- `--bt`: card/panel background (containers, tiles).
- `--b-t`: alias for card/panel background (legacy compatibility).
- `--tb`: top-bar/background gradient layer (toolbar/top overlays).
- `--c`: primary text + icon color (default foreground).
- `--c0`: muted/secondary text (labels, low-priority text).
- `--c1`: secondary highlight/accent text (special indicators/rare accents).
- `--bc`: base border/soft separator + light backgrounds (hr, input bg, separators).
- `--bg`: soft backdrop/background layer (menus, overlays, blur-panels).
- `--hover`: generic hover surface gradient.
- `--b-t22/44/77/88/95`: transparent white layers (UI layering system).
- `--bor`: border/outline/overlay dim color (borders, modals backdrops).
- `--btn`, `--btn1..4`: button gradients (kept in sync with accent theme).
- `--bt-bx`: subtle shadow preset (light elevation).
- `--bs`, `--bss`, `--bs-*`: shadow presets (cards, overlays, focus states).
- `--lg`, `--lg1`, `--lg-bl`, `--lg-b`, `--lg-b1`: decorative/utility gradients (subtle overlays, special surfaces).

---

# [2] Description Required
Empty => `ACC_ERR_DESCRIPTION_REQUIRED`
