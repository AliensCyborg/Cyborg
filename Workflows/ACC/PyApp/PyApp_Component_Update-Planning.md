---
WorkflowId: PyApp_Component_Update-Planning
Type: Workflow_Singular-Planning
Domain: PyApp
Scope: Generate update planning for a PyApp Component (per target)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Component_Update-Planning"
    - Targets: "App/Component"
    - Description: "Update constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.md"
  - AuditFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.audit.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only intent: planning must describe delta edits"
  - "Missing target is error"
  - "Fail fast on ambiguity"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Products.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Products.css"
...

# [0] Purpose
Given target `AppName/ComponentName`, yeh workflow **update planning** generate karega:
- Existing component + css ko read karke
- Description ke hisaab se **delta plan** banana
- Plan ko save karna:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.md`

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ComponentNameNormalized (canonical; SSOT):
  - If `ComponentName` starts with `{AppName}.` => keep
  - Else if `ComponentName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{ComponentName}`

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ComponentNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ComponentName}`

- ComponentFileCandidates (pick first that exists):
  - `{AppRoot}/User/component/{ComponentNameNormalized}.py`
  - `{AppRoot}/User/component/{ComponentNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - `{AppRoot}/User/css/{ComponentNameLegacyUnderscore}.css`

- ComponentFile: first existing from ComponentFileCandidates
- CssFile: first existing from CssFileCandidates
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.md`

Deterministic audit artifact (required):
- AuditOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.audit.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

Update-only precondition:
- If ComponentFile missing => `WF_COMPONENT_NOT_FOUND`
- If CssFile missing => `WF_COMPONENT_CSS_NOT_FOUND`

Audit precondition (hard):
- If AuditOut missing => `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Component_Update-Audit`

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/ComponentName`
- Description treated as constraints, not scope expansion

## Step B: Read Existing Component + CSS (Mandatory)
Read:
- ComponentFile
- CssFile

Extract:
- Root UTag (example: `depo`)
- Data contract keys actually used
- Any risky patterns (inline Python islands vs heredoc)
- CSS scoping selectors and breakpoints

Also extract + flag (hard constraints):
- Any `function ...` declarations inside component (forbidden)
- Any processing outside BackendGuard (forbidden)
- Any markup usage of non-`$Data[...]` variables (forbidden)
- If plural: whether it follows plural->singular child rendering; if `$Data`-only template is required, confirm buffered child rendering into `$Data['ItemsHtml']`

## Step C: Define Delta Plan (Deterministic)
Planning MUST define:
1) What changes (exact)
2) Where (file + section)
3) Why (reason)
4) Expected outcome

Hard boundaries:
- Do not propose creating new components/sections/screens/templates unless Description explicitly requires.
- Do not propose app-level routing/menu/register changes.

## Step D: Update Checklist (Minimal)
Checklist MUST include (as applicable):
- Naming normalization confirmed (raw vs normalized)
- Component contract preserved (public usage)
- No destructive actions (no delete/move/rename)
- CSS remains scoped to UTag selectors
- Responsive behavior preserved/improved

Component hygiene (mandatory):
- No functions/classes/closures in component file
- All conditions/calculations/normalization/escaping/merging/Component() calls stay inside BackendGuard
- Template markup uses ONLY `$Data[...]` (or `$Data['ItemsHtml']` prepared in guard)

## Step E: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Output Template (Planning File Sections)
Planning file must contain:
- Overview
- Target + Paths
- Naming normalization (raw vs normalized)
- Existing snapshot (UTag, data keys, css scope)
- Delta steps (numbered)
- Acceptance checklist
- Revision log

---

# [5] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_COMPONENT_NOT_FOUND
- WF_COMPONENT_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_AMBIGUOUS_UPDATE_REQUIREMENT
