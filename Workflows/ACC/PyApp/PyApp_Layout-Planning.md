---
WorkflowId: PyApp_Layout-Planning
Type: Workflow_Singular-Planning
Domain: PyApp
Scope: Generate planning for a PyApp Layout part (per target)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-23
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Header.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout.py"
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Layout-Planning"
    - Targets: "App/LayoutPart"
    - Description: "UI/structure constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Layout/{LayoutNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Planning must be deterministic + reusable by Code/Docs workflow"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Given target `AppName/LayoutPartName`, yeh workflow **Layout part planning** generate karega:
- Layout part purpose (exact)
- Naming normalization (App prefix; no double-prefix)
- Layout-part structure rules (lightweight fragment)
- CSS intent (optional) + deterministic scope strategy
- Files list + acceptance checklist

Planning ka output aage `PyApp_Layout-Code` aur `PyApp_Layout-Documentation` reuse karenge.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- LayoutNameNormalized:
  - If `LayoutPartName` starts with `{AppName}.` => keep
  - Else if `LayoutPartName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{LayoutPartName}`
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Layout/{LayoutNameNormalized}.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/LayoutPartName`
- Description treated as constraints, not scope expansion

## Step B: Discover Existing Patterns (Read-only)
Scan (if present):
- `{AppRoot}/User/layout/`
- `{AppRoot}/User/css/`

If a similar layout part exists:
- Capture markup style (wrapper conventions, class naming)

## Step C: Define Layout Part Contract (Deterministic)
Planning MUST define:
1) **LayoutPartObjective** (1-2 lines; exact responsibility)
2) **Include/Usage Contract**
   - Layout parts are included by `User/layout.py`.
   - Layout part itself should be safe to include multiple times (idempotent output; no side-effects).
3) **Markup Contract**
   - Use standard HTML tags (div/nav/header/footer/ul/li/a/button/etc.).
   - Keep fragment lightweight; no heavy loops or business logic.
4) **CSS Contract (Optional)**
   - If CSS is required, define a deterministic root selector.
   - Default root selector rule (deterministic):
     - Root element MUST include class: `layout-{LayoutPartSlug}`
     - `{LayoutPartSlug}` = sanitized suffix of LayoutNameNormalized (remove `{AppName}.` and keep `[A-Za-z0-9_-]` only)
   - If CSS is NOT required, explicitly mark `CssRequired=false`.

## Step D: Define Outputs (Scope Lock)
Plan exact outputs:
1) `{AppRoot}/User/layout/{LayoutNameNormalized}.py`
2) Optional: `{AppRoot}/User/css/{LayoutNameNormalized}-Layout.css`

No other files.

## Step E: Acceptance Checklist (Minimal)
- File naming follows `PyApp_Naming_and_Screens` SSOT
- Layout part is a small fragment only (no business logic)
- No delete operations; no extra files
- If CSS is created, filename uses type suffix `-Layout.css`

## Step F: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Output Template (Planning File Sections)
Planning file must contain:
- Overview
- Target + Paths
- Naming normalization (raw vs normalized)
- Layout contract (objective, usage, markup)
- CSS contract (required?, root selector)
- Files to Generate (exact list)
- Acceptance Checklist
- Revision Log

---

# [5] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_PERMISSION_DENIED
