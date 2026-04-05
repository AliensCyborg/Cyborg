---
WorkflowId: WebSDK_Module_API-Code
WorkflowName: WebSDK Module API Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: WebSDK_Module_API
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-24
Inputs:
  - Targets: "Module/ApiName, Module/ApiName2, ..."
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/API/{ApiName}.plan.md"
Produces:
  - ApiFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/api/{ApiName}.php"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/WebOS_Engines.md"
    - "Workflows/ACC/_Refs/WebOS_System.md"
    - "Workflows/ACC/_Refs/WebSDK.md"
Quality:
  CodeStyle:
    - "AliensStyle (mandatory)"
    - "AliensGrade (mandatory)"
  Logging:
    - "SDK rule: use d(), NOT x()"
NonNegotiables:
  - "API file must NOT declare any function or class (0 local symbols)."
  - "API file must NOT execute SQL or build raw SQL queries."
  - "All business logic must live in Module php/index.php class methods."
  - "If module class exists, do NOT create a new class; only add methods with correct naming."
  - "Module class method updates MUST follow single-return rule: exactly one return at end, return required, no code after return."
  - "Function code-header for touched class methods MUST include Purpose + Inputs/Outputs + Dependencies."
  - NoDelete: true
  - ModifyOnlyForUpdate: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Theme: "_Basic"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Products.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Product.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/ProductNew.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Product_Specifications.php"
---

# WebSDK_Module_API-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

**Role (Hinglish):**  
Yeh file `WebSDK_Module_API` workflow ke liye **API code generator** hai.  
Iska kaam: planning ko source-of-truth maan kar **API PHP file create/update** karna, proper folder me save karna, aur run-record me exact changes + quality summary store karna.

> Important: Is file me sirf **WebSDK API generation** ke unique rules honge.  
> Common code rules `Workflow_Singular.md` + `Workflow_Singular-Code.md` se aayenge.

---

## [01] Input meaning (unique for this workflow)
### Targets interpretation
- `Module/ApiName` => WebSDK module + API file name (without extension)

### Module-only target (Batch Create Mode)
- If target is ONLY a module name (no `/`), example:
  - `Teacher`
- Then it means: **create missing APIs for this module only**.
- Workflow MUST expand this into minimum required CRUD API targets:
  - `{Module}/{Module}` (singular)
  - `{Module}/{Module}s` (default plural by `+ 's'`)
  - `{Module}/{Module}New` (create)
  - `{Module}/{Module}Update` (update)
- For each expanded target:
  - If `{ApiFile}` exists => **SKIP** (do NOT update, do NOT touch)
  - If `{ApiFile}` missing => run normal Create generation

Notes:
- Irregular plural or custom API names MUST be passed explicitly as `Module/ApiName`.
- Some modules may require more than these 4 CRUD APIs. Those MUST be passed explicitly.

For this workflow:
- Module = WebSDK Module folder name under `/Aliens/WebSDK/.../Module/`
- ApiName = PHP file name under `{Module}/api/`

Comma rule:
- Each comma-separated target => run this singular workflow again with new payload.

---

## [02] Resolve paths (unique mapping for WebSDK APIs)
Using `ACC.Paths.md` resolver contract:

### WebSDK root (version)
- Primary: latest available `PHP.{Latest}`
- Fallback: `PHP.26.00.00`

### Module root
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`

### API file path (output)
- `{ApiFile} = {ModuleRoot}/api/{ApiName}.php`

### Module class path (expected)
- `{ModuleClass} = {ModuleRoot}/php/index.php`

### Module SQL structure path (expected)
- `{ModuleSql} = {ModuleRoot}/sql/index.sql`

---

## [03] Hard dependency: PlanningDoc is mandatory
For every target, generator MUST load:

- `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/API/{ApiName}.plan.md`

If planning doc missing:
- Mark target failed: `Failed(PlanningMissing)`
- Do not create/update API file.

Planning is the single source of truth for:
- PlanMode (Create/Update)
- Data mapping (tables/columns)
- Auth/permission
- Inputs/outputs schema
- Error model mapping

---

## [04] Create vs Update rules (strict)
### A) If `{ApiFile}` exists
- Treat as **Update**
- **Modify-only**:
  - Do not delete file
  - Do not remove important behavior/features unless explicitly required by planning + approvals
  - Keep backward compatibility where possible
- Preserve existing public contract unless plan explicitly migrates it.

Batch Create override:
- If this run originated from **Module-only target (Batch Create Mode)** then:
  - If `{ApiFile}` exists => **SKIP** (no update allowed)

### B) If `{ApiFile}` does NOT exist
- Treat as **Create**
- Create folder `{ModuleRoot}/api/` if missing
- Create `{ApiFile}` with full AliensStyle header + documentation blocks

### C) For workflows with `_Update` (not this one)
- Missing target MUST be error (handled by plural or update workflow).

---

## [05] Module Types (Singular vs Plural) — code impact (SSOT)
Module type is decided by **required tables/units**, not by underscore presence in ModuleName.

If module is **PluralModule** (tables/units like `{Module}_Address`, `{Module}_Meta`):
- Generator MUST:
  - follow planning mapping for multiple tables/units
  - call module class methods that match the unit family (e.g., `{Module}_{Part}New`, `{Module}_{Part}Update`)
  - avoid assuming single-table CRUD
  - assume module class may use modular layout (`index.php` + `_Trait.php` + `{Module}_{Part}.php`) but primary entry remains `php/index.php`

If module is **SingularModule**:
- Default to single main table and standard CRUD surface (as per plan)

---

## [06] API file minimum standard (what code MUST contain)
Generator MUST produce an enterprise-grade PHP API file that includes:

1) **AliensStyle CodeHeader** (mandatory fields as per latest AliensStyle rules)
2) **Strict input parsing** + validation
3) **Auth/permission gate** (based on plan)
4) **Business logic call**:
   - Prefer calling module class methods from `{ModuleClass}`
   - Avoid embedding complex logic directly in API file unless plan says so
5) **Database safety**:
   - No raw unsafe SQL
   - Use WebOS DB engine patterns (from `WebOS_Engines.md` / `WebOS_System.md`)

### 5.1) Hard Policy (Aliens Company) — API files are glue only
For **every** WebSDK Module API file under:
- `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/api/{ApiName}.php`

These rules are mandatory:
- **No function declarations** inside API file (including helper functions).
- **No class declarations** inside API file.
- **No SQL execution** inside API file (no `SQL(...)`, no `mysqli_query(...)`, no raw query strings).

Allowed in API file:
- Input parsing via `REQUEST()`
- Auth/feature checks via `Access()`, `App[]`, `i[]`
- Calling existing module class methods (from `{ModuleClass}`)
- Returning JSON output

If any extra processing is required:
- Add/modify a **method** in `{ModuleClass}` (`/php/index.php`) inside the **existing** module class.
- Do **NOT** create a new class if one already exists.
  - Ensure the method follows **single-return** rule (one return, end of function).
  - Ensure method code-header includes **Dependencies** + **Purpose** (and Inputs/Outputs).
6) **Standard response schema**:
   - success: true/false
   - code: stable error/success code
   - message: human readable
   - data: payload
   - traceId / runId (recommended)
7) **Error model**:
   - Must map to `ACC.Errors.md`
   - Must never expose secrets
8) **Logging** (very important):
   - Since this is WebSDK (SDK/App layer), use **d()** logs, NOT x()
   - Use AliensStyle single-line $echo pattern where applicable, but routed to d()

---

## [06.1] Base API Reference (MANDATORY)

Treat this file as the baseline/reference implementation for structure + safety:

- Reference API: `/Aliens/WebSDK/PHP.26.00.00/Module/Teacher/api/Teachers.php`

All new APIs and API-fixes should match these patterns unless the planning doc explicitly overrides them.

### A) Router URL rule (NO direct file execution)
- Usage examples MUST use routed facade URL format:
  - `https://base.software/?api=<ApiName>&...`
- Never document or encourage running:
  - `/Aliens/WebSDK/.../Module/<Module>/api/<ApiName>.php?...`

Clarification:
- The above filesystem path is shown only as a **forbidden pattern**. Do NOT include it in docs, examples, readmes, or comments inside API files.

### B) Response envelope (WebSDK base)
- Response MUST include stable keys:
  - `s,t,c,o,paging,ok` and non-breaking meta: `code`, `trace`
- `c` MUST be a safe public message (no internal gate/debug leakage).

### C) Logging law (WebSDK)
- Keep `$echo` for internal narrative; log with `d($echo, __LINE__, '<type>')`.
- Never put “Step 1/2/3” strings in `$echo`.
- Do not leak `$echo` into the public response message.

### D) Filters and schema allow-listing
- Filters MUST be strictly derived from `{ModuleSql}` (`/sql/index.sql`) schema.
- Prefer explicit allow-lists for:
  - sortable columns (`sort_column`)
  - method aggregate columns (`methodcolumn`)

### E) Aggregate methods (Enterprise)
- If `method` is provided:
  - Allow only: `COUNT`, `SUM` (unless plan extends)
  - Validate `methodcolumn` as schema column name (`^[a-z0-9_]+$`) AND present in schema allowlist
  - For `SUM`, allow numeric-only columns (plan must define numeric allowlist)
  - Aggregates MUST ignore paging: force `LIMIT=''` and `OFFSET=''`

### F) API param
- Do not require validation of `api=<ApiName>` inside the API file.
- Routing/verification is handled by WebOS/Base router layer.

---

## [07] Example-driven style selection (4 example files rule)
This workflow MUST collect **4 example API files** before final generation to match ecosystem patterns.

If explicit example paths are provided by system/context, use them.  
Otherwise auto-discover with this deterministic strategy:

1) Same module, same folder:
   - Search `{ModuleRoot}/api/` for any existing `.php` files
   - Pick up to 2 most recently modified

2) Similar modules (fallback):
   - Search `/Aliens/WebSDK/{PHP.Version}/Module/*/api/*.php`
   - Pick 2 files whose names are closest to `{ApiName}` by similarity (string match)

Store these example paths into run-record so audit can see what style was followed:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/examples.json` (recommended)

**Note:** Examples are only for style/structure consistency.  
Planning remains the source for behavior/requirements.

---

## [08] What the generator MUST update in docs/run-record
For each target item, generator must write a `code.md` run note:

Path:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`

It must include:
- Resolved paths + version used
- PlanMode (Create/Update)
- Example file paths used (4)
- File operations summary:
  - CreatedFiles[]
  - UpdatedFiles[]
- Safety/approval decisions applied
- Quality checklist (AliensGrade score signals)
- Next actions for documentation workflow

---

## [09] Approval gates (unique reminders)
Approval is controlled by developer manifest + safety ref:

- If `ApprovalMode=manual` and plan requires prod/db touch:
  - generator MUST pause and create approval request
- If `ApprovalMode=auto`:
  - proceed but log “auto-approved”

For normal WebSDK API create/update, usually:
- prod code write: yes (module code)
- db touch: only if plan explicitly asks for sql changes (but actual DB run is not done here)

---

## [10] Security baseline (must never be skipped)
Generator MUST ensure:
- Input validation (type + required + length constraints)
- Auth + permission check before sensitive actions
- Rate-limit hooks if endpoint is abuse-prone (as per plan)
- Audit trace id and safe error messages

---

## [11] What this file MUST NOT do
- DB migrations execute nahi karega
- Files delete nahi karega
- Documentation generate nahi karega (next workflow ka kaam)
- Plural orchestration/loop nahi karega

---

## [12] Handoff to Documentation workflow
After API file generate/update:
- Ensure required doc signals are present in run-record so that:
  - `WebSDK_Module_API-Documentation.md` can generate docs under `/Aliens/Docs/WebSDK/...`
