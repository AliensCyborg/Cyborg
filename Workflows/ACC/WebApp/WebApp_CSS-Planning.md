---
WorkflowId: WebApp_CSS-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
ParentWorkflowId: WebApp_CSS
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
Inputs:
  Target:
    - Format: "AppName"
  Description: "REQUIRED"
Outputs:
  - "CSS contract for app-level tokens + utilities"
NonNegotiables:
  - "DescriptionRequired"
  - "No deletes"
...

# [0] Purpose
App-level CSS ka planning contract generate karna:
- Branding colors / tokens
- Layout + utility classes
- Reusable patterns (popup/flyout/overlay)

---

# [1] Deterministic Root (SSOT)
Theme Root:
- `AppRoot = /Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
- CSS Output: `{AppRoot}/User/css/0.css`

Precondition:
- `User/css/0.css` must exist (Structure workflow responsibility).

---

# [2] Token Strategy (SSOT)
This workflow defines the global token system in `User/css/0.css`.

Hard rules:
- **Do not rename base keys** (`--a`, `--aa`, `--b`, `--c`, etc.)
- Prefer alias tokens for app-level readability:
  - `--brand-primary: var(--a)`
  - `--brand-secondary: var(--aa)`
  - `--surface: var(--b)`
  - `--surface-2: var(--bt)`
  - `--text: var(--c)`
  - `--text-muted: var(--c0)`

---

# [3] Layout Strategy (Reusable)
App-level layout + utilities must be generic so that Components/Sections/Screens reuse them.

Minimum required categories:
- App shell + containers: `.app`, `.container`, `.stack`, `.row`, `.grid`
- Spacing + radius utilities: `.p-11`, `.p-22`, `.r-11`, `.r-22`
- Text helpers: `.muted`, `.nowrap`, `.clamp-2`
- Buttons: `.btn1` behavior baseline
- Overlays: `popups` + `Flyouts` baseline

---

# [4] Plan Output Contract (JSON in markdown)
Plan file MUST include a JSON contract with:
- Workflow
- AppName
- Description
- ThemeMode: `light|dark|auto`
- Brand:
  - Primary
  - Secondary
  - Background
  - Text
- Utilities:
  - Enabled blocks list

If Description doesn’t specify colors:
- pick safe defaults (light theme).

---

# [5] Description Required
Empty => `ACC_ERR_DESCRIPTION_REQUIRED`
