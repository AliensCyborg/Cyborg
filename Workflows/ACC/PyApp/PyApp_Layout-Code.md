---
WorkflowId: PyApp_Layout-Code
Type: Workflow_Singular-Code
Domain: PyApp
Scope: Generate a PyApp Layout part Python file (+ optional Layout CSS file) (create-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-23
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Header.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Nav-Layout.css"
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Layout-Code"
    - Targets: "App/LayoutPart"
    - Description: "UI/structure constraints only (no scope expansion)"
Outputs:
  - LayoutFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/layout/{LayoutNameNormalized}.py"
  - CssFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{LayoutNameNormalized}-Layout.css" 
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Create-only: if file exists -> error (use update workflow)"
...

# [0] Purpose
Given `AppName/LayoutPartName`, yeh workflow:
1) Layout part Python file create karta hai
2) Optional: Layout CSS file create karta hai (sirf jab Planning/Description me required ho)

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- LayoutNameNormalized: prefix rule per SSOT
- LayoutOut: `{AppRoot}/User/layout/{LayoutNameNormalized}.py`
- CssOut: `{AppRoot}/User/css/{LayoutNameNormalized}-Layout.css`

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

---

# [3] Create-Only Policy
- If LayoutOut exists:
  - ErrorCode: `WF_ALREADY_EXISTS`
  - Action: add/use a dedicated update workflow (not part of this create workflow)
- For CSS:
  - If CssOut exists, do not overwrite; treat as already present.

---

# [4] Planning Dependency (Hard Gate)
Load planning file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Layout/{LayoutNameNormalized}.md`

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `PyApp_Layout-Planning`

---

# [5] Code Generation Rules
## 5.1 Layout Philosophy (Non-negotiable)
- Layout part is a lightweight fragment included by `User/layout.py`.
- No business logic.
- No destructive side-effects.
- Safe include: should not depend on global mutable state beyond standard PyOS globals.

## 5.2 Required Python Patterns
- Must start with `# PyOS Access Gate`
- Must include AliensStyle Code-Header + Code-Footer
- Code-Header MUST include `Author: AlienCyborg` (exact string)
- Use `[SECTION]` blocks for readability

## 5.3 Markup Rules
- Prefer standard HTML tags.
- Markup MUST include a deterministic wrapper for optional CSS scoping:
  - If planning defines `layout-{LayoutPartSlug}` root class, use it.

## 5.4 Layout CSS Rules (Only if required)
- Filename must follow type suffix naming: `{LayoutNameNormalized}-Layout.css`.
- Scope selectors under the planned wrapper class.

---

# [6] Save Outputs
- Write LayoutOut.
- If planning says `CssRequired=true` (or Description explicitly requires layout CSS), then write CssOut.

---

# [7] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_ALREADY_EXISTS
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
