---
WorkflowId: WebApp_Screen_Update
Type: Workflow_Plural
Domain: WebApp
Scope: Update existing WebApp Screens (planning + code + docs) via child workflows (modify-only)
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
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Screen_Update"
    - Targets: "App/Screen, App/Screen, ..."
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Update-only: missing targets => error"
  - "Modify-only: do not overwrite from scratch"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **existing WebApp Screens** ko update karwata hai.
Is file ka kaam orchestration hai: per Target:
0) Audit (read-only)
1) Planning (update plan)
2) Code (modify-only update)
3) Documentation (create-or-update docs)

Example:
- Workflow( "WebApp_Screen_Update", "AlienCyborg/Deposit, AlienCyborg/Home", "Layout refine + sections reorder" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/ScreenName`
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case
- If any target is empty => error `WF_TARGET_INVALID`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Normalize Screen basename:
- If `ScreenName` starts with `{AppName}.` => keep
- Else if `ScreenName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{AppName}.{ScreenName}`

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/ScreenName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
  - ScreenNameNormalized: canonical (SSOT)
  - Update-only lookup must resolve the *existing* files (dot or legacy underscore / legacy css suffix) without rename/move.
  - ScreenOut: resolved existing screen file
  - CssOut: resolved existing screen css file

Update-only resolution rule:
- Missing screen/css target => error (`WF_SCREEN_NOT_FOUND` / `WF_SCREEN_CSS_NOT_FOUND`)

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
## Step A1: Audit (Child Workflow)
Call:
- Workflow( "WebApp_Screen_Update-Audit", "{AppName}/{ScreenName}", "{Description}" )

## Step B: Planning (Child Workflow)
Call:
- Workflow( "WebApp_Screen_Update-Planning", "{AppName}/{ScreenName}", "{Description}" )

## Step B1: Section Dependency Resolution (Mandatory)
Rule (Screen = composition only):
- Screen ke andar koi naya section/design generate nahi hoga.
- Screen sirf existing Sections ko call karega.

Dependency policy:
- Planning se `RequiredSections[]` nikalo (existing + new required by description).
- For each required section:
  - Normalize name via App prefix rule (`{AppName}.`), avoid double-prefix.
  - Check existence in same theme:
    - `{AppRoot}/User/section/{SectionNameNormalized}.php`
    - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (canonical)
    - OR `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy)
  - If exists: DO NOT create, sirf call.
  - If missing: pehle `WebApp_Section` workflow run karo (loop / per section), phir screen update apply karo.

Call format (strict):
- `Section( 'Section_Name', Data_Array, return=true|false )`

If any required section cannot be resolved/created:
- ErrorCode: `WF_SECTION_DEPENDENCY_FAILED`
- Action: run `WebApp_Section` for missing sections, then re-run screen update.

## Step C: Code Update (Child Workflow)
Call:
- Workflow( "WebApp_Screen_Update-Code", "{AppName}/{ScreenName}", "{Description}" )

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "WebApp_Screen_Update-Documentation", "{AppName}/{ScreenName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/WebApp/...`
- If manifest says `Approval=auto`: proceed.

---

# [5] Hard Boundaries (No Extra Work)
- Scope sirf: given targets ke existing screen + its screen CSS + docs.
- Screen ke andar section/design generate mat karna.
- Allowed dependency creation: sirf missing sections ko `WebApp_Section` workflow se create karna (same theme), phir screen me call karna.
- Components auto-create mat karna (components are handled inside Section via existing rules).
- App-level routing/menu/register changes mat karna.

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_SCREEN_NOT_FOUND
- WF_SCREEN_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED
