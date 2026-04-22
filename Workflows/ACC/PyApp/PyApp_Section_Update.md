---
WorkflowId: PyApp_Section_Update
Type: Workflow_Plural
Domain: PyApp
Scope: Update existing PyApp Sections (planning + code + docs) via child workflows (modify-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Section_Update"
    - Targets: "App/Screen_Section, App/Screen_Section, ..."
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Update-only: missing targets => error"
  - "Modify-only: update existing files only"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **existing PyApp Sections** ko update karwata hai.
Is file ka kaam orchestration hai: per Target:
0) Audit (read-only)
1) Planning (update plan)
2) Code (modify-only update)
3) Documentation (create-or-update docs)

Example:
- Workflow( "PyApp_Section_Update", "AlienCyborg/Deposit_Header, AlienCyborg/Home_Hero", "Layout refine" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/SectionName`
- Comma-separated list = multiple runs

## 2.2 SectionName Meaning (Deterministic)
`SectionName` must include ScreenName + SectionPart separated by underscore:
- `ScreenName_SectionPart`
Examples:
- `Deposit_Header`
- `Home_Hero`

If user passes only `Header` (no underscore):
- Error `WF_SECTION_NAME_AMBIGUOUS`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

Normalize Section basename:
- If `SectionName` starts with `{AppName}.` => keep
- Else => `{AppName}.{SectionName}`

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/SectionName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
  - SectionNameNormalized: `{AppName}.{SectionName}` (unless already prefixed)
  - SectionOut: `{AppRoot}/User/section/{SectionNameNormalized}.py`
    - CssOut: resolved existing section css file (candidate lookup; no rename/move)

Update-only resolution rule:
- SectionOut missing => error `WF_SECTION_NOT_FOUND`
- CssOut missing => error `WF_SECTION_CSS_NOT_FOUND`

Legacy CSS candidates (update-only compatibility; do not rename/move):
- `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (SSOT / canonical)
- `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy transitional)
- `{AppRoot}/User/css/{SectionNameLegacyUnderscore}-Section.css` (legacy)
- `{AppRoot}/User/css/{SectionNameLegacyUnderscore}.css` (rare transitional)

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
## Step A1: Audit (Child Workflow)
Call:
- Workflow( "PyApp_Section_Update-Audit", "{AppName}/{SectionName}", "{Description}" )

## Step B: Planning (Child Workflow)
Call:
- Workflow( "PyApp_Section_Update-Planning", "{AppName}/{SectionName}", "{Description}" )

## Step B1: Component Dependency Resolution (Mandatory)
Rule (Section = composition only):
- Section ke andar koi naya component/design generate nahi hoga.
- Section sirf existing Components ko call karega.

Dependency policy:
- Planning se `RequiredComponents[]` nikalo (existing + new required by description).
- For each required component:
  - Normalize name via App prefix rule (`{AppName}_`), avoid double-prefix.
  - Check existence in same theme:
    - `{AppRoot}/User/component/{ComponentNameNormalized}.py`
    - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - If exists: DO NOT create, sirf call.
  - If missing: pehle `PyApp_Component` workflow run karo (loop / per component), phir section update apply karo.

Call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

If any required component cannot be resolved/created:
- ErrorCode: `WF_COMPONENT_DEPENDENCY_FAILED`
- Action: run `PyApp_Component` for missing components, then re-run section update.

## Step C: Code Update (Child Workflow)
Call:
- Workflow( "PyApp_Section_Update-Code", "{AppName}/{SectionName}", "{Description}" )

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "PyApp_Section_Update-Documentation", "{AppName}/{SectionName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/PyApp/...`
- If manifest says `Approval=auto`: proceed.

---

# [5] Hard Boundaries (No Extra Work)
- Scope sirf: given targets ke existing section + its CSS + docs.
- Section ke andar component/design generate mat karna.
- Allowed dependency creation: sirf missing components ko `PyApp_Component` workflow se create karna (same theme), phir section me call karna.
- App-level routing/menu/register changes mat karna.

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_SECTION_NOT_FOUND
- WF_SECTION_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED
