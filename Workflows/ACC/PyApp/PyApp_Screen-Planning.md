---
WorkflowId: PyApp_Screen-Planning
Type: Workflow_Singular-Planning
Domain: PyApp
Scope: Generate planning for a PyApp Screen (per target)
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
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Screen-Planning"
    - Targets: "App/Screen"
    - Description: "UI/structure constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Planning must be deterministic + reusable by Code/Docs workflow"
  - "Fail fast on ambiguity"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposit.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposit-Screen.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposits.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposits-Screen.css"
...

# [0] Purpose
Given target `AppName/ScreenName`, yeh workflow **Screen planning** generate karega:
- Screen purpose + premium UI intent
- Naming normalization (App prefix; no double-prefix)
- Screen tag plan (root tag, optional scope tag)
- Sections list (exact)
- Files list + acceptance checklist

Planning ka output aage `PyApp_Screen-Code` aur `PyApp_Screen-Documentation` reuse karenge.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenNameNormalized:
  - If `ScreenName` starts with `{AppName}.` => keep
  - Else if `ScreenName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{ScreenName}`
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/ScreenName`
- Description treated as constraints, not scope expansion

## Step B: Discover Existing Patterns (Read-only)
Scan (if present):
- `{AppRoot}/User/screen/`
- `{AppRoot}/User/section/`
- `{AppRoot}/User/css/`

If a similar screen exists:
- Capture composition style:
  - root tag usage (example: `<Deposit>`)
  - wrapper/scope tag usage (example: `<sdpt>`)
  - section naming pattern (Header/Aerea/Footer etc.)

## Step C: Define Screen Structure (Deterministic)
Planning must define:
1) **ScreenRootTag**
  - Default: suffix of normalized screen name (remove `{AppName}.`)
  - Example: `AlienCyborg.Deposit` => `Deposit`
2) **ScreenScopeTag (UTag)** (required)
  - This is the screen CSS scope wrapper (example: `sdpt`).
  - Non-negotiable: it must be globally unique across the entire PyOS ecosystem.
  - Allocate via central registry (no manual pick): `Workflows/ACC/_Refs/PyApp_UTag.md`
  - If missing/ambiguous => error `WF_AMBIGUOUS_SCREEN_SCOPE_TAG`.

## Step D: Define Sections List (Exact)
Planning must list **exact** section calls in order, e.g.:
- `Section('{AppName}.{ScreenRootTag}_Header', Data_Array, return=true|false)`
- `Section('{AppName}.{ScreenRootTag}_Aerea', Data_Array, return=true|false)`
- `Section('{AppName}.{ScreenRootTag}_Footer', Data_Array, return=true|false)`

Composition-only rule (Non-negotiable):
- Screen ke andar koi section/design generate nahi hoga.
- Screen sirf sections ko jod kar banta hai.

Practical composition reminder (non-negotiable):
- Section actual me multiple components ko compose karta hai.
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Screen ka scope sirf **sections composition** hai; components/sections create/update alag rows/workflows me honge.

Planning MUST output:
- `RequiredSections[]` (normalized names)
- `ExistingSections[]` (found in same theme)
- `MissingSections[]` (must be created via PyApp_Section before screen code)

Existence check (same theme):
- `{AppRoot}/User/section/{SectionNameNormalized}.py`
- `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (canonical)
- `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy)

Call format (strict):
- `Section( 'Section_Name', Data_Array, return=true|false )`

If sections are unknown/ambiguous:
- ErrorCode: `WF_AMBIGUOUS_SECTIONS`
- Action: user must provide the intended section names.

## Step E: Define Outputs (Scope Lock)
Plan exactly:
1) `{AppRoot}/User/screen/{ScreenNameNormalized}.py`
2) `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`
No more.

## Step F: Premium UI Acceptance (Minimal Checklist)
- Uses `# PyOS Access Gate` at top
- Screen is composition-only (calls Sections)
- Naming follows `PyApp_Naming_and_Screens` SSOT
- CSS file follows type suffix naming (`{ScreenNameNormalized}-Screen.css`)
- No delete operations; no extra files
- Missing sections are created only via `PyApp_Section` workflow (not inside screen)

## Step G: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Output Template (Planning File Sections)
Planning file must contain:
- Overview
- Target + Paths
- Naming normalization (raw vs normalized)
- Screen structure (root tag + optional scope tag)
- Sections list (exact)
- Files to Generate (exact list)
- Acceptance Checklist
- Revision Log

---

# [5] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_AMBIGUOUS_SCREEN_SCOPE_TAG
- WF_AMBIGUOUS_SECTIONS
- WF_PERMISSION_DENIED
