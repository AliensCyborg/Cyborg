---
WorkflowId: WebApp_Screen-Code
Type: Workflow_Singular-Code
Domain: WebApp
Scope: Generate a WebApp Screen + its Screen CSS file (create-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Screen-Code"
    - Targets: "App/Screen"
    - Description: "UI/structure constraints only (no scope expansion)"
Outputs:
  - ScreenFile: "/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/screen/{ScreenNameNormalized}.php"
  - CssFile: "/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{ScreenNameNormalized}-Screen.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Create-only: if file exists -> error (use update workflow)"
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposit.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposit-Screen.css"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposits.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposits-Screen.css"
...

# [0] Purpose
Given `AppName/ScreenName`, yeh workflow **2 files create** karta hai:
1) Screen PHP file
2) Screen CSS file (same basename as Screen)

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenNameNormalized: prefix rule per SSOT
- ScreenOut: `{AppRoot}/User/screen/{ScreenNameNormalized}.php`
- CssOut: `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` first.

---

# [3] Create-Only Policy
- If ScreenOut exists OR CssOut exists:
  - ErrorCode: `WF_ALREADY_EXISTS`
  - Action: run `WebApp_Screen_Update` workflow
- Do not overwrite silently.

---

# [4] Planning Dependency (Hard Gate)
Load planning file:
- `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Screen/{ScreenNameNormalized}.md`

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `WebApp_Screen-Planning`

---

# [5] Code Generation Rules
## 5.1 Screen Philosophy

## 5.2 Required Screen PHP Patterns
- Must start with `<?php Access(); ?>`
- Must include AliensStyle Code-Header + Code-Footer
- Code-Header MUST include `Author: AlienCyborg` (exact string)
- Use `[SECTION]` blocks for readability (audit-friendly)
- Screen is composition-only: it wires Sections; it does not implement UI design or heavy logic.
- Use Section calls as per plan:
  - `Section( 'Section_Name', Data_Array, return=true|false )`

Inline PHP islands are allowed in Screen files for calling Sections (matches existing theme style).

## 5.2.1 Composition-Only Rule (Non-negotiable)

Hard rule:
- Screen ka scope **sirf** sections ko compose/wire karna hai.
- Screen ke andar **Component()** direct call mat karo; Screen sirf `Section(...)` calls karega.
- Section actual me multiple components ko compose karta hai; component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.

Meaning:
- Screen workflow kabhi bhi section/component ka markup “invent” nahi karega.
- Agar UI me new section/component chahiye ho, to separate rows/workflows use hon (`WebApp_Section`, `WebApp_Component`) and screen rerun ho.

Dependency gate (mandatory):
  - `{AppRoot}/User/section/{SectionNameNormalized}.php` exists
  - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` exists (canonical)
  - OR `{AppRoot}/User/css/{SectionNameNormalized}.css` exists (legacy)
  - ErrorCode: `WF_SECTION_MISSING`
  - Action: run `WebApp_Section` for missing sections (via plural orchestrator), then re-run `WebApp_Screen`.

Enterprise quality gates (non-negotiable):
- Do not output a tiny skeleton if Description/Planning implies more.
- Function-level headers required for non-trivial functions (Purpose / Inputs / Outputs).
- Comments must be meaningful and structured; no filler comments.
- `$echo` + `d()` must be used correctly when logging exists; do not use `x()`.
## 5.3 Screen CSS Rules
- Filename must follow type suffix naming: `{ScreenNameNormalized}-Screen.css`.
---
# [6] Save Outputs
- Write ScreenOut + CssOut
---
# [7] Errors
- WF_MANIFEST_MISSING

