---
WorkflowId: PyApp_Section_Update-Code
Type: Workflow_Singular-Code
Domain: PyApp
Scope: Update a PyApp Section + its CSS file (modify-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
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
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Section_Update-Code"
    - Targets: "App/Screen_Section"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - SectionFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/section/{SectionNameNormalized}.py"
  - CssFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{SectionNameNormalized}-Section.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only: update existing files only"
  - "Missing target is error"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Breadcrumbs.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Breadcrumbs-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Hero-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_About.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_About-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Specs.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Specs-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Offers.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Offers-Section.css"
   
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_QA.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_QA-Section.css"
   
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Related.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Related-Section.css"
  
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Reviews.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Reviews-Section.css"

  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Footer.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product_Footer-Section.css"
...

# [0] Purpose
Given `AppName/SectionName`, yeh workflow existing section ko **modify-only update** karta hai:
1) Section Python file
2) Section CSS file

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionNameNormalized (canonical; SSOT): prefix rule per `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md` (and must contain underscore)

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{SectionNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{SectionName}`

- SectionFileCandidates (pick first that exists):
  - `{AppRoot}/User/section/{SectionNameNormalized}.py`
  - `{AppRoot}/User/section/{SectionNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`         (SSOT / canonical)
  - `{AppRoot}/User/css/{SectionNameNormalized}.css`                (legacy transitional)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}-Section.css`  (legacy)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}.css`          (legacy)

- SectionOut: first existing from SectionFileCandidates
- CssOut: first existing from CssFileCandidates

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

If SectionName missing underscore:
- ErrorCode: `WF_SECTION_NAME_AMBIGUOUS`
- Action: provide `Screen_Section` (example: `Deposit_Header`)

Update-only precondition:
- If SectionOut missing => `WF_SECTION_NOT_FOUND`
- If CssOut missing => `WF_SECTION_CSS_NOT_FOUND`

---

# [3] Planning Dependency (Hard Gate)
Load update planning file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Section/{SectionNameNormalized}.Update.md`

Deterministic audit artifact (hard gate):
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Section/{SectionNameNormalized}.Update.audit.md`

If audit not found:
- ErrorCode: `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Section_Update-Audit`

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `PyApp_Section_Update-Planning`

---

# [4] Update Rules
## 4.1 Section Philosophy (Preserve Theme)
- Section = composition/layout layer.
- Inline Python islands are allowed in sections (matches existing theme style).
- Preserve Screen guard behavior and Component() contracts.

## 4.1.1 Composition-Only Rule (Non-negotiable)
- Section ke andar **koi component ya design generate nahi hoga**.
- Section ka kaam sirf existing components ko wire/call karna hai.

Practical hierarchy reminder (non-negotiable):
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Section component ka internal markup/logic replicate nahi karega; sirf `Component(...)` calls + minimal wrappers.

Component call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

Dependency resolution (allowed, but only via workflow):
- Agar planning me required component missing ho:
  - Direct component code section file me add mat karna.
  - `PyApp_Component` workflow run karke missing components create karo (same theme), phir section update apply karo.

## 4.2 Modify-only Contract (Non-negotiable)
- No file delete.
- No rename/move.
- No overwrite from scratch.
- Preserve existing semantics unless planning explicitly changes it.

Note:
- Yeh workflow section files ko modify-only patch karega.
- Components create karna sirf dependency step ke tor par `PyApp_Component` workflow se allowed hai.

## 4.3 Allowed Change Categories
Only apply changes that appear in update planning:
- Layout refinements inside the section markup
- Screen guard refinements (safe)
- Component() calls ordering/parameters (only if required)
- CSS scope + responsive fixes inside the resolved Section CSS file (`CssOut`)

## 4.4 CSS Rules (Update)
- Preserve existing Section CSS filename (no rename/move). SSOT prefers `{SectionNameNormalized}-Section.css` for new work.
- Scope selectors to screen wrapper + semantic section tag (example: `prod header ...`).
- Do NOT depend on `.{{AppName}}_*` / section-name classes in selectors.
- Selector depth (spaces) strict:
  - Best: `selector1 selector2`
  - Max: `selector1 selector2 selector3`
  - If more depth needed: add a short class in markup and target it
- Markup classes must be extra-short (`a1..a9`, `b1..b9`).
- CSS formatting (strict): one selector per line `selector{ prop:val; prop:val; }` and no blank lines.

Forbidden unless Description explicitly requests:
- Creating new components/sections/screens/templates
- App-level routing/menu/register changes

---

# [5] Save Outputs
- Patch SectionOut + CssOut (modify-only)
- Return minimal output summary: updated file paths only

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_SECTION_NOT_FOUND
- WF_SECTION_CSS_NOT_FOUND
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
