---
WorkflowId: WebApp_Section-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
Scope: Generate planning for a WebApp Section (per target)
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
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Section-Planning"
    - Targets: "App/Screen_Section"
    - Description: "UI/behavior constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Planning must be deterministic + reusable by Code/Docs workflow"
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
Given target `AppName/SectionName`, yeh workflow **planning** generate karega:
- Section intent + Premium UI spec
- Section structure (minimal + predictable)
- Component() calls (allowed) as composition only
- Output structure (high-level)
- Files list + acceptance checklist

Planning ka output aage `WebApp_Section-Code` aur `WebApp_Section-Documentation` workflows reuse karenge.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionName rules:
  - Must contain underscore: `ScreenName_SectionPart`
  - If `SectionName` starts with `{AppName}.` => keep
  - Else if `SectionName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{SectionNameNormalized} = {AppName}.{SectionName}`
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.md`

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` first.

If SectionName missing underscore:
- ErrorCode: `WF_SECTION_NAME_AMBIGUOUS`
- Action: provide `Screen_Section` (example: `Deposit_Header`)

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here
- Validate target format: `AppName/SectionName`
- Description is treated as **constraints**, not scope expansion.

## Step B: Discover Existing Patterns (Read-only)
- Scan (if present):
  - `{AppRoot}/User/section/`
  - `{AppRoot}/User/css/`
- If similar section exists:
  - capture style: tags used (`<header>`, `<aerea>`, `<hero>`), indentation, Screen guards, Component() usage.

## Step C: Define Section Structure (Deterministic)
Planning must decide:
- SectionScopeTag (UTag) (required): a globally unique wrapper tag for section scope
  - Allocate via central registry: `Workflows/ACC/_Refs/WebApp_UTag.md`
- Section root semantic tag (inside wrapper): usually `<header>`, `<aerea>`, `<hero>`
- Whether it needs `if(Screen){ ... }` guards
- Which Component() calls are used (if any)

Composition-only rule (Non-negotiable):
- Section ke andar koi component/design generate nahi hoga.
- Section sirf components ko jod kar banega.

Practical composition reminder (non-negotiable):
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Section ka scope sirf **components composition** hai; component create/update alag rows/workflows me honge.

Planning MUST output:
- `RequiredComponents[]` (normalized names)
- `ExistingComponents[]` (found in same theme)
- `MissingComponents[]` (must be created via WebApp_Component before section code)

Existence check (same theme):
- `{AppRoot}/User/component/{ComponentNameNormalized}.php`
- `{AppRoot}/User/css/{ComponentNameNormalized}.css`

Call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

If cannot infer deterministically:
- ErrorCode: `WF_AMBIGUOUS_REQUIREMENT`
- Action: user must specify section purpose + whether it will call components.

## Step D: Define Outputs (Scope Lock)
Plan exactly these two files:
1) `{AppRoot}/User/section/{SectionNameNormalized}.php`
2) `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`

## Step E: Premium UI Acceptance (Minimal Checklist)
- Section file has `<?php Access(); ?>`
- No destructive ops
- No app-level wiring
- CSS selectors do not bleed beyond intended tags
- CSS selectors policy (strict):
  - Best: `selector1 selector2`
  - Max: `selector1 selector2 selector3` (space-depth)
  - Markup classes are extra-short (`a1..a9`, `b1..b9`)
  - Do NOT depend on `.{{AppName}}_*` / section-name classes in selectors
- CSS formatting policy:
  - One selector per line: `selector{ prop:val; prop:val; }`
  - No blank lines
- Section is composition-only (no component/design creation inside section)
- Missing components are created only via `WebApp_Component` workflow (not inside section)

## Step F: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_AMBIGUOUS_REQUIREMENT
