---
WorkflowId: PyApp_Screen_Update-Planning
Type: Workflow_Singular-Planning
Domain: PyApp
Scope: Generate update planning for a PyApp Screen (per target)
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
    - WorkflowId: "PyApp_Screen_Update-Planning"
    - Targets: "App/Screen"
    - Description: "Update constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.md"
  - AuditFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.audit.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only intent: planning must describe delta edits"
  - "Missing target is error"
  - "Fail fast on ambiguity"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposit.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposit-Screen.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposits.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposits-Screen.css"
...

# [0] Purpose
Given target `AppName/ScreenName`, yeh workflow **update planning** generate karega:
- Existing screen + css ko read karke
- Description ke hisaab se **delta plan** banana
- Plan ko save karna:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.md`

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenNameNormalized (canonical; SSOT):
  - If `ScreenName` starts with `{AppName}.` => keep
  - Else if `ScreenName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{ScreenName}`

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ScreenNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ScreenName}`

- ScreenFileCandidates (pick first that exists):
  - `{AppRoot}/User/screen/{ScreenNameNormalized}.py`
  - `{AppRoot}/User/screen/{ScreenNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`         (SSOT / canonical)
  - `{AppRoot}/User/css/{ScreenNameNormalized}.css`                (legacy transitional)
  - `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}-Screen.css`   (legacy)
  - `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}.css`          (rare transitional)

- ScreenFile: first existing from ScreenFileCandidates
- CssFile: first existing from CssFileCandidates
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.md`

Deterministic audit artifact (required):
- AuditOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.audit.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

Update-only precondition:
- If ScreenFile missing => `WF_SCREEN_NOT_FOUND`
- If CssFile missing => `WF_SCREEN_CSS_NOT_FOUND`

Audit precondition (hard):
- If AuditOut missing => `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Screen_Update-Audit`

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/ScreenName`
- Description treated as constraints, not scope expansion

## Step B: Read Existing Screen + CSS (Mandatory)
Read:
- ScreenFile
- CssFile

Extract:
- Root tag usage (example: `<Deposit>`)
- Optional scope/wrapper tag usage
- Section call list + order (`Section('...')`)
- Any hydration guards (example: `global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`)

Composition-only rule (Non-negotiable):
- Screen ke andar koi section/design generate nahi hoga.
- Screen sirf sections ko jod kar update hoga.

Practical composition reminder (non-negotiable):
- Section actual me multiple components ko compose karta hai.
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Screen update planning ka scope sirf **sections composition** hai; components/sections create/update alag rows/workflows me honge.

Planning MUST output:
- `RequiredSections[]` (normalized names)
  - Includes: existing Section() calls + any new required by Description
- `ExistingSections[]` (found in same theme)
- `MissingSections[]` (must be created via PyApp_Section before applying screen update)

Existence check (same theme):
- `{AppRoot}/User/section/{SectionNameNormalized}.py`
- `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (canonical)
- OR `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy)

Call format (strict):
- `Section( 'Section_Name', Data_Array, return=true|false )`

## Step C: Define Delta Plan (Deterministic)
Planning MUST define:
1) What changes (exact)
2) Where (file + section)
3) Why (reason)
4) Expected outcome

Hard boundaries:
- Do not propose creating new sections inside screen.
- If section is required and missing: plan must explicitly say "Create via PyApp_Section workflow (dependency step)".
- Do not propose app-level routing/menu/register changes.

## Step D: Update Checklist (Minimal)
Checklist MUST include (as applicable):
- Naming normalization confirmed (raw vs normalized)
- Section order changes (if requested)
- Screen tag/scoping changes (only if deterministically inferred)
- CSS scope safety (prefer wrapper scoping)
- No destructive actions (no delete/move/rename)

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
- Existing snapshot (root tag, scope tag, sections list)
- Delta steps (numbered)
- Acceptance checklist
- Revision log

---

# [5] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_SCREEN_NOT_FOUND
- WF_SCREEN_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_AMBIGUOUS_UPDATE_REQUIREMENT
