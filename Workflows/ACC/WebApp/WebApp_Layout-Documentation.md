---
WorkflowId: WebApp_Layout-Documentation
Type: Workflow_Singular-Documentation
Domain: WebApp
Scope: Generate documentation for a WebApp Layout part
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-23
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
ExampleFiles:
  - "/Aliens/Docs/WebApp/AlienCyborg/Screens/AlienCyborg.Deposit.md"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Nav-Layout.css"
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Layout-Documentation"
    - Targets: "App/LayoutPart"
    - Description: "Doc constraints (no scope expansion)"
Outputs:
  - DocFile: "/Aliens/Docs/WebApp/{AppName}/Layouts/{LayoutNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
...

# [0] Purpose
Given `AppName/LayoutPartName`, yeh workflow layout-part ki **documentation** generate karta hai taaki:
- include/use contract clear ho
- naming rules consistent rahein
- audit + maintenance easy ho

# [0A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- LayoutFile: `{AppRoot}/User/layout/{LayoutNameNormalized}.php`
- CssFileCandidates (pick first that exists; no rename/move):
  - `{AppRoot}/User/css/{LayoutNameNormalized}-Layout.css` (SSOT / canonical)
  - `{AppRoot}/User/css/{LayoutNameNormalized}.css` (legacy transitional)
- CssFile: first existing from CssFileCandidates (or blank if none exists)
- DocOut: `/Aliens/Docs/WebApp/{AppName}/Layouts/{LayoutNameNormalized}.md`

If LayoutFile missing:
- ErrorCode: `WF_DEPENDENCY_MISSING`
- Action: run `WebApp_Layout-Code` first.

---

# [3] Documentation Inputs (Read-only)
- Planning file:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Layout/{LayoutNameNormalized}.md`
- Layout part source file (+ optional CSS file)
- Description constraints

---

# [4] Documentation Content Rules
Doc must include ONLY what is required for this layout part.

Minimum required sections:
1) Overview
2) Paths (layout + optional css)
3) Naming normalization (raw vs normalized)
4) Include/usage contract (how layout.php includes it)
5) Markup contract + key selectors/classes
6) CSS scoping notes (if CSS exists)
7) Premium checklist (minimal)
8) Changelog

Forbidden:
- App-wide docs
- Screen/Section/Component implementation docs
- Extra suggestions not requested

---

# [5] Save Strategy
- If DocOut exists: update in-place
- Maintain a changelog (append per run)
- Do not delete older docs

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_DEPENDENCY_MISSING
- WF_PERMISSION_DENIED
