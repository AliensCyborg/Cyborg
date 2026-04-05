---
WorkflowId: WebApp_Section_Update-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
Scope: Generate update planning for a WebApp Section (per target)
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
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Section_Update-Planning"
    - Targets: "App/Screen_Section"
    - Description: "Update constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.Update.md"
  - AuditFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.Update.audit.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only intent: planning must describe delta edits"
  - "Missing target is error"
  - "Fail fast on ambiguity"
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Breadcrumbs.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Breadcrumbs-Section.css"
  
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Hero-Section.css"
  
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_About.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_About-Section.css"
  
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Specs.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Specs-Section.css"
  
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Offers.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Offers-Section.css"
   
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_QA.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_QA-Section.css"
   
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Related.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Related-Section.css"
  
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Reviews.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Reviews-Section.css"

  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Footer.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Footer-Section.css"
...

# [0] Purpose
Given target `AppName/SectionName`, yeh workflow **update planning** generate karega:
- Existing section + css ko read karke
- Description ke hisaab se **delta plan** banana
- Plan ko save karna:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.Update.md`

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionNameNormalized (canonical; SSOT):
  - Must contain underscore after removing optional App prefix
  - If `SectionName` starts with `{AppName}.` => keep
  - Else if `SectionName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{SectionName}`

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{SectionNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{SectionName}`

- SectionFileCandidates (pick first that exists):
  - `{AppRoot}/User/section/{SectionNameNormalized}.php`
  - `{AppRoot}/User/section/{SectionNameLegacyUnderscore}.php`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`         (SSOT / canonical)
  - `{AppRoot}/User/css/{SectionNameNormalized}.css`                 (legacy transitional)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}-Section.css`   (legacy)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}.css`           (rare transitional)

- SectionFile: first existing from SectionFileCandidates
- CssFile: first existing from CssFileCandidates
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.Update.md`

Deterministic audit artifact (required):
- AuditOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.Update.audit.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` first.

If SectionName missing underscore:
- ErrorCode: `WF_SECTION_NAME_AMBIGUOUS`
- Action: provide `Screen_Section` (example: `Deposit_Header`)

Update-only precondition:
- If SectionFile missing => `WF_SECTION_NOT_FOUND`
- If CssFile missing => `WF_SECTION_CSS_NOT_FOUND`

Audit precondition (hard):
- If AuditOut missing => `WF_AUDIT_REQUIRED`
- Action: run `WebApp_Section_Update-Audit`

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/SectionName`
- Description treated as constraints, not scope expansion

## Step B: Read Existing Section + CSS (Mandatory)
Read:
- SectionFile
- CssFile

Extract:
- Tags used (`<header>`, `<aerea>`, `<hero>`, etc.)
- Screen guards (`if(Screen){ ... }`) and how `$Array` is prepared
- Component() calls and payload keys
- CSS scoping selectors and breakpoints

Composition-only rule (Non-negotiable):
- Section ke andar koi component/design generate nahi hoga.
- Section sirf components ko jod kar update hoga.

Practical composition reminder (non-negotiable):
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Section update planning ka scope sirf **components composition** hai; component create/update alag rows/workflows me honge.

Planning MUST output:
- `RequiredComponents[]` (normalized names)
  - Includes: existing Component() calls + any new required by Description
- `ExistingComponents[]` (found in same theme)
- `MissingComponents[]` (must be created via WebApp_Component before applying section update)

Existence check (same theme):
- `{AppRoot}/User/component/{ComponentNameNormalized}.php`
- `{AppRoot}/User/css/{ComponentNameNormalized}.css`

Call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

## Step C: Define Delta Plan (Deterministic)
Planning MUST define:
1) What changes (exact)
2) Where (file + region)
3) Why (reason)
4) Expected outcome

Hard boundaries:
- Do not propose creating new components inside section.
- If component is required and missing: plan must explicitly say "Create via WebApp_Component workflow (dependency step)".
- Do not propose app-level routing/menu/register changes.

## Step D: Update Checklist (Minimal)
Checklist MUST include (as applicable):
- Naming normalization confirmed (raw vs normalized)
- Screen guard behavior preserved
- Component() contract preserved
- No destructive actions (no delete/move/rename)
- CSS remains scoped to section tags/classes

## Step E: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_SECTION_NOT_FOUND
- WF_SECTION_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_AMBIGUOUS_UPDATE_REQUIREMENT
