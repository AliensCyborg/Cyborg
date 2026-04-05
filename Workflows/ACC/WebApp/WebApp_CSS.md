---
WorkflowId: WebApp_CSS
Type: Workflow_Plural
Domain: WebApp
Scope: Generate WebApp App-Level CSS (planning + code + docs) via child workflows
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_CSS"
    - Targets: "AppName, AppName, ..."
    - Description: "Human constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Fail fast on ambiguity"
  - "App-level CSS must be reusable by Components/Sections/Screens/Layouts"
...

# [0] Purpose
Yeh workflow **app-level CSS** generate karta hai.

Is workflow ka goal:
- **CSS variables / design-tokens** (branding + colors)
- **global behavior CSS** (button feels, overlay, transitions)
- **app layout classes** (shell/layout utilities)
- **screen layout classes** (screen wrappers + responsive conventions)

Note:
- Components/Sections/Screens/Layouts ki CSS unke workflows se ban sakti hai.
- **App-level CSS** separate hoti hai jo sab jagah reuse hoti hai.

Example:
- Workflow( "WebApp_CSS", "E-Commerce, Trip", "Blue brand + premium feel; reusable layout utilities" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName`
- `AppName.Global` (runner derives `AppName` deterministically)
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case
- If any target is empty => error `WF_TARGET_INVALID`

---

# [3] Output Files (SSOT)
For each `AppName`:
- AppRoot: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
- RoleRoot: `.../User/`

This workflow creates/updates the **single global WebApp design-system CSS**:
- `{RoleRoot}/css/0.css`

Contract:
- `User/css/0.css` is the single source of truth for tokens/branding/layout utilities/behavior.
- Layout/Screen/Section/Component workflows still generate unit-level CSS files, but they must consume tokens/classes from `0.css`.

Hard rule:
- If app/theme root is missing => error `WF_APPROOT_NOT_FOUND` (run `WebApp_Structure` first).

---

# [4] Primary Execution Steps (Per Target)
For each target `AppName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve latest app version + theme root deterministically

## Step B: Planning (Child Workflow)
Call:
- Workflow( "WebApp_CSS-Planning", "{AppName}", "{Description}" )

## Step C: Code Generation (Child Workflow)
Call:
- Workflow( "WebApp_CSS-Code", "{AppName}", "{Description}" )

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "WebApp_CSS-Documentation", "{AppName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of created/updated files
