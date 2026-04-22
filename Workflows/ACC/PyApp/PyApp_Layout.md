---
WorkflowId: PyApp_Layout
Type: Workflow_Plural
Domain: PyApp
Scope: Generate PyApp Layout parts (planning + code + docs) via child workflows
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-23
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Header.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Footer.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py"
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Layout"
    - Targets: "App/LayoutPart, App/LayoutPart, ..."
    - Description: "Human constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Explicit outputs only"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **1 ya multiple PyApp Layout parts** generate karwata hai.
Layout parts are small Python fragments included by `User/layout.py`.

Is file ka kaam orchestration hai (per target):
1) Planning
2) Code
3) Documentation

Example:
- Workflow( "PyApp_Layout", "B-Commerce/Nav, B-Commerce/Footer", "Premium layout UX; no business logic" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/LayoutPartName`
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case (naming conventions strict)
- If any target is empty => error `WF_TARGET_INVALID`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

Normalize Layout basename:
- If `LayoutPartName` already starts with `{AppName}.` => keep
- Else if it starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{AppName}.{LayoutPartName}`

Do not double-prefix.

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/LayoutPartName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
  - LayoutNameNormalized: `{AppName}.{LayoutPartName}` (unless already prefixed)
  - LayoutOut: `{AppRoot}/User/layout/{LayoutNameNormalized}.py`
  - CssOut (optional): `{AppRoot}/User/css/{LayoutNameNormalized}-Layout.css`

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
Call:
- Workflow( "PyApp_Layout-Planning", "{AppName}/{LayoutPartName}", "{Description}" )

## Step C: Code Generation (Child Workflow)
Call:
- Workflow( "PyApp_Layout-Code", "{AppName}/{LayoutPartName}", "{Description}" )

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "PyApp_Layout-Documentation", "{AppName}/{LayoutPartName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of created/updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/PyApp/...`
- If manifest says `Approval=auto`: proceed

---

# [5] Hard Boundaries (No Extra Work)
- Scope **sirf**: given targets ke layout-part Python + optional layout CSS + docs.
- `User/layout.py` (aggregator) ko edit/replace mat karo.
- App routing/menu changes mat karo.
- Business logic mat add karo.

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED
