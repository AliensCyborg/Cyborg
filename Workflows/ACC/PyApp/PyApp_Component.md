---
WorkflowId: PyApp_Component
Type: Workflow_Plural
Domain: PyApp
Scope: Generate PyApp Components (planning + code + docs) via child workflows
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
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
    - WorkflowId: "PyApp_Component"
    - Targets: "App/Component, App/Component, ..."
    - Description: "Human constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Explicit outputs only"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **1 ya multiple PyApp Components** generate karwata hai.
Is file ka kaam sirf orchestration hai: har Target par:
1) Planning
2) Code
3) Documentation

Example:
- Workflow( "PyApp_Component", "Exchange/Deposit_Transactions, Social/Post", "UI premium hona zarori hai" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/ComponentName`
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case (naming conventions strict)
- If any target is empty => error `WF_TARGET_INVALID`

## 2.3 Resolution Defaults
- Default Version: latest numeric PyApp version folder (example: `26.00.00`)
  - Runner must pick latest deterministically (no hardcoded version)
- Default Theme: `{AppName}_Basic` (current rule)
- Component files are stored under App Theme:
  - Component: `User/component/{ComponentNameNormalized}.py`
  - CSS: `User/css/{ComponentNameNormalized}.css`

## 2.3.1 JS Template-System Hydration Contract (Mandatory)
Component HTML must remain compatible with PyOS JS hydration (Unit/js/001.js):
- Note: yeh **HTML marker contract** hai (attributes + placeholders) — PyApp ke andar custom JS likhna allowed nahi.
- Hydration triggers only when the rendered HTML contains a non-empty `c-api` attribute
- Required attributes for hydration (on a single wrapper element):
  - `c-api="<ApiName>"`
  - `c-response="o"` (non-empty; used as a gate)
- Data injection placeholders:
  - use `key="<FieldName>"` on elements that should be filled by API output
  - special keys handled by JS: `img` (sets `src`), `href` (sets `href`), `status` (sets `status` attribute)
- Plural list hydration:
  - list container must include `c-dynamic="true" c-child="<ChildComponentUid>"`
  - include a removable placeholder element `uid="No"` inside the container (JS removes it)

Enterprise rule for dual-mode:
- When `Screen && !empty($Array)` (backend integration), component must NOT output `c-api` (avoid unwanted client re-hydration).
- When backend payload is missing (template fetch / empty state), component MAY output `c-api` + `key="..."` placeholders.

## 2.4 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

Normalize component basename:
- If `ComponentName` already starts with `{AppName}.` => keep
- Else if it starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{AppName}.{ComponentName}`

Important:
- Do not double-prefix.

Output rule (must match normalization):
- If user passes `AlienCyborg/WithdrawNew`, then **generated filenames** must be:
  - `.../User/component/AlienCyborg.WithdrawNew.py`
  - `.../User/css/AlienCyborg.WithdrawNew.css`

## 2.5 ComponentName Sanitization (Mandatory)
- If user mistakenly passes punctuation in `ComponentName` (e.g. `Withdraw's`), runner must sanitize it into a safe basename:
  - keep only `[A-Za-z0-9_]` characters
  - example: `Withdraw's` => `Withdraws`

## 2.6 Variants (Deterministic)
- Component variant is inferred from `ComponentName + Description` (no extra questions).
- Withdraw:
  - `withdraw` => single transaction card (UTag preferred: `wdra`)
  - `withdraw_list` => multiple transactions list (UTag preferred: `wdrs`)

---

# [2.7] Plural Loop Rendering Rule (Mandatory)
Jab kisi plural component me same UI row ko loop me print karwana hota hai:
- Loop ke andar heavy/large HTML markup **kabhi** mat likho.
- Hamesha ek **Singular Component** banao (agar already exist karta ho to reuse) aur loop me call karo:

  `Component( 'Singular_Component_Name', $DataArray, true )`

Notes:
- If singular component missing ho, is workflow family me **auto** singular generate karna allowed hai.
  - Condition: plural/list component ka item renderer `Component('App_Singular', $Item, true)` depend karta ho aur singular exists nahi karta.
  - Action: runner **without extra approval** missing singular component ko generate kar sakta hai (Planning + Code + Docs).
  - Reason: plural component ki runtime stability + enterprise loop rule compliance.
- Plural component sirf list container + minimal wrappers handle kare; item markup singular component ke andar rahe.

Additional strict rules (plural components):
- Loop + item-rendering `Component('App_Singular', $Item, true)` **sirf** BackendGuard ke andar.
- BackendGuard ke bahar:
  - koi loop nahi
  - koi `Component()` call nahi
  - koi `$Array` merge/formatting/sanitization nahi
  - sirf minimal empty/skeleton UI allowed

Dummy data rule (enterprise):
- Component me hardcoded demo dataset by default **forbidden**.
- Dummy/default dataset sirf **BackendGuard** ke andar allowed hai.
- Explicit dummy: caller `$Array['dummy']=1`.
- Default dataset: jab component no payload ke sath Screen render me call ho (PyOS `Component()` global `$Screen=true` set karta hai).

---

# [2.8] HTML Standardization Rule (Mandatory)
Component markup me har jagah custom tags use **mat** karo.

Allowed custom structural tags (fixed skeleton):
- `<{UTAG}>` root
- `<bl>`
- `<hd>`
- `<bd>`
- `<ft>`

Inside `hd/bd/ft`:
- Prefer **standard HTML**: `div`, `span`, `p`, `ul`, `li`, `button`, `time`, `h1-h6`, etc.
- Use **classes** freely for styling.
- If ecosystem me kuch existing minimal custom tags already used ho (legacy compatibility), keep them minimal and do not expand.

---

# [2.9] BackendGuard Processing Rule (Mandatory)
Enterprise performance discipline:

- Kisi bhi component me zyada se zyada Python processing **sirf** is condition ke andar honi chahiye:

  `global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`

- Is guard ke andar hi:
  - `$Data = array_merge($Default, $Array)`
  - loops / list rendering
  - heavy sanitization / formatting
  - singular component calls (plural components)

- Guard fail ho (Screen false ya `$Array` empty) to:
  - component **minimal UI** render kare
  - `$Array` ko merge/loop **na** kare (even if front default data pass kar raha ho)
  - goal: backend integration na ho to Python load minimum rahe

Notes:
- Front-end preview/default UI ke liye caller `Component('X')` (no args) use kar sakta hai; component guard ke andar **default dataset** render kar sakta hai.
- Preview ke liye BackendGuard ko bypass mat karo; dummy/default dataset handling hamesha guard ke andar hi rahe.

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/ComponentName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
  - ComponentOut: `{AppRoot}/User/component/{ComponentNameNormalized}.py`
  - CssOut: `{AppRoot}/User/css/{ComponentNameNormalized}.css`

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
Call:
- Workflow( "PyApp_Component-Planning", "{AppName}/{ComponentName}", "{Description}" )

## Step C: Code Generation (Child Workflow)
Call:
- Workflow( "PyApp_Component-Code", "{AppName}/{ComponentName}", "{Description}" )

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "PyApp_Component-Documentation", "{AppName}/{ComponentName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of created/updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/PyApp/...`
- If manifest says `Approval=auto`: proceed.

Note: Exact gating is implemented in child workflows; yeh orchestration file sirf intent define karta hai.

---

# [5] Hard Boundaries (No Extra Work)
- Scope **sirf**: given targets ke components + CSS + docs.
- App-level routing/layout/menu/register changes **mat** karna.
- Template wiring **mat** karna.
- Extra components/sections/templates **mat** banana.

Note:
- CSS selector + formatting SSOT: `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

If user wants extra:
- Run a different workflow (e.g., `PyApp_Template`, `PyApp_Structure_Update`, etc.)

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED

---

# [7] CSV Mode (Optional)
If workflow is invoked via Tasks CSV:
- Process exactly ONE pending row (`status=0`) per run.
- If more than one pending row exists, hard-fail with `WF_CSV_BATCH_FORBIDDEN`.
- After processing, runner must write back row status deterministically.
