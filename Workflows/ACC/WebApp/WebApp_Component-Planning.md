---
WorkflowId: WebApp_Component-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
Scope: Generate planning for a WebApp Component (per target)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
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
    - WorkflowId: "WebApp_Component-Planning"
    - Targets: "App/Component"
    - Description: "UI/behavior constraints (no scope expansion)"
Outputs:
  - PlanningFile: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Component/{ComponentName}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Planning must be deterministic + reusable by Code/Docs workflow"
  - "Fail fast on ambiguity"
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Products.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Products.css"
...

# [0] Purpose
Given target `AppName/ComponentName`, yeh workflow **planning** generate karega:
- Component intent + Premium UI spec
- Component Type (Singular/Plural) + UTag rule
- Data contract (sirf needed keys)
- Output structure (high-level)
- Files list + acceptance checklist
- Dependencies (read-only references)

Planning ka output aage `WebApp_Component-Code` aur `WebApp_Component-Documentation` workflows reuse karenge.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

# [2] Deterministic Path Resolution
Resolve:
- AppRoot: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
- PlanningOut:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Component/{ComponentNameNormalized}.md`

Naming normalization (mandatory):
- If `ComponentName` starts with `{AppName}.` => keep
- Else if `ComponentName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{ComponentNameNormalized} = {AppName}.{ComponentName}`

PlanningOut should be saved using `ComponentNameNormalized` (not raw), so Code/Docs reuse it deterministically.

If AppRoot not found:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` first.

---

# [3] Planning Generation Steps
## Step A: Parse Inputs (Single Target Only)
- Comma not allowed here (plural orchestration handles multi-target)
- Validate target format: `AppName/ComponentName`
- Description is treated as **constraints**, not a request to expand scope.

## Step B: Discover Existing Patterns (Read-only)
- Scan (if present):
  - `{AppRoot}/User/component/`
  - `{AppRoot}/User/css/`
- If similar component exists:
  - capture style: UTag conventions, skeleton `<bl><hd><bd><ft>`, button patterns, responsive breakpoints (700/999), CSS vars usage.

## Step C: Decide Component Type (Deterministic)
ComponentType is mandatory:
- **Plural** if description implies list/loop OR it must call a child singular component.
- **Singular** if it is a leaf UI unit (no component call allowed).

Important clarification (non-negotiable):
- “Leaf UI unit” ka matlab yeh nahi ke component me sirf 1 element hoga.
- Component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.
- Leaf ka matlab sirf yeh hai ke **Singular component** ke andar **Component() calls** nahi honge.

Component type rules (non-negotiable; SSOT behavior):
- **Singular Component**: kabhi bhi kisi bhi type ka `Component()` call nahi karega.
- **Plural Component**: hamesha per-item rendering ke liye ek **Singular child component** ko call karega (plural -> singular delegation).
- **Section** me singular + plural dono components call kiye ja sakte hain (section is composition layer).

If cannot infer deterministically:
- ErrorCode: `WF_AMBIGUOUS_COMPONENT_TYPE`
- Action: user must specify "Singular" or "Plural" and (if plural) the child component name.

## Step D: Define UTag Rule
- UTag format is SSOT-driven (do not guess): `Workflows/ACC/_Refs/WebApp_UTag.md`
- Singular vs Plural UTag semantics (always true):
  - Singular UTag: does NOT end with `s`
  - Plural UTag: MUST end with `s`

Note (compatibility):
- Legacy v1 examples: singular 4 letters (e.g., `prdt`), plural 5 letters ending `s` (e.g., `prdts`).
- New work uses UTag v2 as per SSOT, but plural still ends with `s`.

Global uniqueness (non-negotiable):
- Component UTag must be globally unique across the entire WebOS ecosystem (all WebApp).
- Manual pick forbidden. Allocate via central registry:
  - SSOT: `Workflows/ACC/_Refs/WebApp_UTag.md`

Planning must include the allocated UTag.

## Step E: Define WebOS Screen Contract (Required)
Planning must explicitly state how data hydration will work:
- If rendered inside template/screen:
  - Use `if (Screen and !empty($Array)) { ... }` for backend integration
- If fetched via `/?component=Name`:
  - `Screen=false`, so component should remain safe for frontend JS hydration.

Hard template + processing constraints (must be stated in planning):
- No user-defined functions/classes inside component files.
- All conditions/calculations/normalization/escaping/merging/component calls MUST be inside BackendGuard.
- Markup/template must use ONLY `$Data[...]` (no locals like `$meta/$state/$config`).
- For plural components: per-item markup MUST be in singular child; plural MUST call child inside BackendGuard.
  - If template is `$Data`-only, capture child HTML via output buffering (`ob_start()`/`ob_get_clean()`) into `$Data['ItemsHtml']`.

Planning must also define the JS hydration contract (when applicable):
- Hydration.Api (string, non-empty)
- Hydration.ResponseKey (string, non-empty; recommended: `o`)
- Plural list: Child component UID used in `c-child="..."`
- Key placeholders to be filled by JS: `key="..."` list

## Step F: Define Outputs (Scope Lock)
Plan **exactly these two code files**:
1) `{AppRoot}/component/{ComponentName}.php`
2) `{AppRoot}/css/{ComponentName}.css`
No more.

## Step G: Premium UI Acceptance (Minimal Checklist)
- AliensStyle header/footer present in PHP component
- Mandatory skeleton: `<bl><hd><bd><ft></ft></bd></bl>`
- CSS strictly scoped to the component UTag (no bleed)
- CSS selectors policy:
  - Best: `utag .a1`
  - Max: `selector1 selector2 selector3` (space-depth)
  - Markup classes are extra-short (`a1..a9`, `b1..b9`)
- CSS formatting policy:
  - One selector per line: `selector{ prop:val; prop:val; }`
  - No blank lines
- Responsive: at least 999px + 700px breakpoint handling
- Accessibility: interactive elements keyboard-safe where relevant; aria labels if needed
- Performance-safe: no heavy loops in UI; use child component for rows if plural

Component hygiene acceptance (hard):
- Component PHP contains no `function` declarations.
- No processing/loops/array_merge/htmlspecialchars/Component() outside BackendGuard.
- HTML renders only `$Data[...]` values (or `$Data['ItemsHtml']` blocks prebuilt in guard).

## Step H: Save Planning
- Write planning to PlanningOut.
- If file exists: append a new `Revision` section (timestamp + changes).
- Never delete older revisions.

---

# [4] Output Template (Planning File Sections)
Planning file must contain:
- Overview
- Target + Paths
- ComponentType + UTag
- UI Spec (Premium)
- Data/Inputs (only required keys)
- Output Structure (high-level)
- Dependencies (read-only)
- Files to Generate (exact list)
- Acceptance Checklist
- Risks / Edge cases (only if relevant)
- Revision Log

---

# [5] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_AMBIGUOUS_COMPONENT_TYPE
- WF_PERMISSION_DENIED
