---
WorkflowId: WebApp_Structure-Code
Type: Workflow_Singular-Code
Domain: WebApp
ParentWorkflowId: WebApp_Structure
Status: Draft
Version: 26.00.03
LastUpdated: 2026-01-19
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
NonNegotiables:
  - "Create placeholders only"
  - "Update planner path columns when resolvable"
  - "No deletes"
...

# [0] Purpose
Filesystem scaffold create karna (folders + empty files).

---

# [1] Placeholder Rules
- Create placeholders only by default.
- Exception (NON-NEGOTIABLE): create minimal bootstrapping content for required entry/aggregator files:
  - `User/index.php`
  - `User/layout.php`
  - `User/css.php`
  - `User/js.php`
  - `User/component.php`
  - `User/css/0.css`

Minimum content rules:
- All required PHP entry/aggregator files must start with `<?php Access(); ?>`.
- `index.php` must include `layout.php` and call `AppHead(...)` (no business logic).
- `css.php` must only aggregate folder CSS assets.
- `js.php` is required for compatibility but must NOT aggregate/include local JS (custom JS in WebApp is forbidden).
- `component.php` must only aggregate component templates and optionally serialize JSON (no business logic).

Base CSS rules:
- `User/css/0.css` must be created with real base CSS (not empty): `:root{...}` variables + shared global styles reused across the app.
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

Meaning contract:
- When generating `User/css/0.css`, keep the variable keys and intended meanings consistent with `WebApp_Structure-Planning` CSS variable dictionary (only values change per theme).

---

# [1.1] Naming Enforcement (Dot Prefix) — NON-NEGOTIABLE
When Structure creates any Screen/Section/Component/CSS placeholders:
- Basename MUST be `AppName.<Name>`.
- If basename already has `AppName.` prefix => do not add again.
- Legacy `AppName_` inputs must be normalized to `AppName.`.
- Never create files with double prefix (example: `B-Commerce.B-Commerce.Home.php`).

---

# [2] Planner Columns
When executed from planner runner, update these columns deterministically when resolvable:
- `Planning_Path`
- `Code_Path`
- `Doc_Path`
- `Content_Path`

## [2.1] Module JS Rows (WebSDK_Module_JS)
Non-negotiable:
- WebApp ke andar custom JS files create/update karna forbidden hai.
- If a planner row `Worklflow` is `WebSDK_Module_JS` (or its `-Planning/-Code/-Documentation` variants), Structure workflow must:
  - NOT create any WebApp-local JS placeholders
  - Only fill path columns deterministically to the WebSDK module locations:
    - `Code_Path = /Aliens/WebSDK/PHP.26.00.00/Module/{Module}/js/index.js`
    - `Planning_Path = /Aliens/.Alien/{Developer}/Planning/WebSDK/Module/{Module}/JS/index.plan.md`
    - `Doc_Path = /Aliens/Docs/WebSDK/Module/{Module}/JS/index.md`
  - Keep `Content_Path` blank for module JS rows
