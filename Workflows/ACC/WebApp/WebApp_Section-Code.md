---
WorkflowId: WebApp_Section-Code
Type: Workflow_Singular-Code
Domain: WebApp
Scope: Generate a WebApp Section + its CSS file (create-only)
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
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Section-Code"
    - Targets: "App/Screen_Section"
    - Description: "UI constraints only (no scope expansion)"
Outputs:
  - SectionFile: "/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/section/{SectionNameNormalized}.php"
  - CssFile: "/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{SectionNameNormalized}-Section.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Create-only: if file exists -> error (use update workflow)"
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
Given `AppName/SectionName`, yeh workflow **2 files create** karta hai:
1) Section PHP file
2) Section CSS file

Scope strictly in 2 files tak limited hai.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionNameNormalized:
  - Must contain underscore after removing optional App prefix
  - If `SectionName` starts with `{AppName}.` => keep
  - Else if `SectionName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
  - Else => `{AppName}.{SectionName}`
- SectionOut: `{AppRoot}/User/section/{SectionNameNormalized}.php`
- CssOut: `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` first.

If SectionName missing underscore:
- ErrorCode: `WF_SECTION_NAME_AMBIGUOUS`
- Action: provide `Screen_Section` (example: `Deposit_Header`)

---

# [3] Create-Only Policy
- If SectionOut exists OR CssOut exists:
  - ErrorCode: `WF_ALREADY_EXISTS`
  - Action: run `WebApp_Section_Update` workflow
- Do not overwrite silently.

---

# [4] Planning Dependency
Before generating code:
- Try to load planning file:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.md`

If planning not found:
- Proceed only if requirements are deterministic
- Otherwise fail with:
  - ErrorCode: `WF_PLANNING_REQUIRED`
  - Action: run `WebApp_Section-Planning`

---

# [5] Code Generation Rules
## 5.1 Section PHP file rules
- Must start with `<?php Access(); ?>`
- Must include AliensStyle Code-Header + Code-Footer
- Code-Header MUST include `Author: AlienCyborg` (exact string)
- Use `[SECTION]` blocks for readability (audit-friendly)
- Keep it simple: section = **layout-only composition**.
- Inline PHP islands are allowed (sections in current theme use them), but PHP should be near-zero.
- If Screen/Array guard needed, follow existing style; avoid doing any loops/formatting in section.

Enterprise quality gates (non-negotiable):
- Do not output a tiny skeleton if Description/Planning implies more.
- Function-level headers required for any non-trivial PHP functions (Purpose / Inputs / Outputs).
- Comments must be meaningful and structured; no filler comments.
- `$echo` + `d()` must be used correctly when logging exists; do not use `x()`.

## 5.1.1 Composition-Only Rule (Non-negotiable)
- Section ke andar **koi UI design** generate nahi hoga.
- Section ka kaam sirf layout wrappers + existing components ko wire/call karna hai.
- Section me loops/conditions/formatting ("PHP ka zyada kaam") **avoid** karo; heavy work component ke BackendGuard me hi ho.

Practical hierarchy reminder (non-negotiable):
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Section ka role sirf in components ko arrange/compose karna hai; section component ka internal markup/logic replicate nahi karega.

Component call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

Dependency gate (mandatory):
- Planning file se `RequiredComponents[]` load karo.
- Har required component ke liye ensure:
  - `{AppRoot}/User/component/{ComponentNameNormalized}.php` exists
  - `{AppRoot}/User/css/{ComponentNameNormalized}.css` exists
- Agar missing hai:
  - ErrorCode: `WF_COMPONENT_MISSING`
  - Action: run `WebApp_Component` for missing components (via plural orchestrator), then re-run `WebApp_Section`.

Dummy/preview rule:
- Section kabhi hardcoded dummy items generate nahi karega.
- Preview/default UI ke liye Section normally sirf `Component('X')` call kare (no args), taa-ke component apna default dataset guard ke andar render kar sake.
- Optional: agar component explicit dummy support karta ho to Section `array('dummy'=>1)` pass kar sakta hai; lekin dummy/default dataset handling hamesha component ke BackendGuard ke andar hi hoga.

## 5.2 CSS file rules
- File name must follow type suffix naming: `{SectionNameNormalized}-Section.css`
- Keep selectors scoped to the screen wrapper + semantic section tag (example: `prod header ...`).
- Do NOT depend on section-name classes in selectors (example: `.AlienCyborg_Products_Header`).
- Prefer short-class targeting:
  - Best: `selector1 selector2` (example: `prod header` + `.a1`)
  - Max allowed: `selector1 selector2 selector3` (space-depth)
  - If more depth needed: add a short class in markup and target it
- Markup classes must be extra-short (`a1..a9`, `b1..b9`).
- Use theme variables only.
- CSS formatting (strict): one selector per line `selector{ prop:val; prop:val; }` and no blank lines.

---

# [6] Save Outputs
- Write SectionOut + CssOut
- Return minimal output summary: created file paths only

---

# [7] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_ALREADY_EXISTS
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
