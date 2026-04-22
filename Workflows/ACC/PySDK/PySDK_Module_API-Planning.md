---
WorkflowId: PySDK_Module_API-Planning
WorkflowName: PySDK Module API Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_API
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-02
Inputs:
  - Targets: "Module/ApiName, Module/ApiName2, ..."
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.plan.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/PyOS_Engines.md"
    - "Workflows/ACC/_Refs/PyOS_System.md"
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - NoDelete: true
  - Theme: "_Basic"
  - DefaultVersionFallback: "Py.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Products.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/ProductNew.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product_Specifications.py"
---

# PySDK_Module_API-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

**Role (Hinglish):**  
Yeh file `PySDK_Module_API` workflow ke liye **Planning generator** hai.  
Iska kaam: targets ke hisaab se **har API ka exact plan** banana, context resolve karna (paths, version, module type), dependencies verify karna, aur planning ko developer ke `.Alien` workspace me save karna.

> Important: Is file me sirf **unique planning rules** honge jo *PySDK Module APIs* ke liye specific hain.  
> Common planning rules `Workflow_Singular.md` + `Workflow_Singular-Planning.md` se aayenge.

---

## [01] Input meaning (unique for this workflow)
### Targets examples
- `User/UserNew`
- `Account/AccountUpdate`
- `Web3/Web3_Accounts`

### Module-only target (Batch Create Mode)
- If target is ONLY a module name (no `/`), example:
  - `Teacher`
- Then it means: **create missing APIs for this module only**.
- In this mode, workflow MUST expand the module into a minimum required CRUD API target set:
  - `{Module}/{Module}` (singular)
  - `{Module}/{Module}s` (default plural by `+ 's'`)
  - `{Module}/{Module}New` (create)
  - `{Module}/{Module}Update` (update)

### Preset Expansion Profiles (Deterministic; no guessing)
Sometimes enterprise modules have a known, explicit API surface beyond the 4-CRUD minimum.

Rule:
- Presets are allowed ONLY when the preset list is explicitly declared in SSOT (this file) so it remains deterministic.

Preset: `User` module (explicit surface; dedupe duplicates)
- If target is module-only AND `Module == 'User'` then expand into these API targets (if missing only):
  - `User/User`
  - `User/Users`
  - `User/UserNew`
  - `User/UserUpdate`
  - `User/UserStatus`
  - `User/User_Address`
  - `User/User_Addresses`
  - `User/User_AddressNew`
  - `User/User_AddressUpdate`
  - `User/User_AddressStatus`
  - `User/User_Meta`
  - `User/User_Metas`
  - `User/User_MetaNew`
  - `User/User_MetaUpdate`
  - `User/User_MetaStatus`

Notes:
- If the preset list contains duplicates in future edits, planner MUST dedupe them (same `{ApiName}`) before execution.
- Underscore-containing API names are valid for this workflow.
- Module type (Singular vs Plural) is decided by required DB tables + unit family (see [03]), not by underscore presence.
- For each expanded target:
  - If `{ApiFile}` already exists => **SKIP** (do NOT update, do NOT touch)
  - If `{ApiFile}` missing => proceed with normal planning/code/docs create pipeline

Notes:
- Irregular plural or custom API names MUST be passed explicitly as `Module/ApiName`.
- Some modules may require more than these 4 CRUD APIs (example: `Status`, `Delete`, domain-specific list endpoints). Those MUST be passed explicitly.

**Interpretation for this workflow:**
- Left side (before `/`) = **PySDK Module name**
- Right side (after `/`) = **API file name** (without extension)

**Comma rule:**
- `User/UserNew, User/Users, Web3/Web3_Accounts`
  => same workflow 3 bar run hoga, 3 alag payloads ke saath.

### Description
- Developer ya user ki custom requirement (any human language).  
- Planning me isko **constraints** me convert karna mandatory hai (UI, validation, security, performance, fields, flows).

---

## [02] Resolve module + version + base paths (unique resolution)
This workflow MUST resolve these paths (using `ACC.Paths.md` contract):

### PySDK root (default latest)
- `/Aliens/PySDK/Python.{Latest}/Module/{Module}/`

If auto latest decide na ho paye:
- default: `Py.26.00.00`

### Target module folder
- `{ModuleRoot} = /Aliens/PySDK/Py.26.00.00/Module/{Module}/`

### Target API file path
- `{ApiFile} = {ModuleRoot}/api/{ApiName}.py`

### Related module class path (95% case)
- `{ModuleClass} = {ModuleRoot}/py/index.py`

### Related module sql path (structure / dummy)
- `{ModuleSql} = {ModuleRoot}/sql/index.sql`

---

## [03] Module Type rule (Singular vs Plural) — planning impact (SSOT)
Module type is decided by **required DB tables + unit family**, not by underscore presence in ModuleName.

**Singular Module**
- Exactly 1 required table: `{Module}`
- Planning defaults to:
  - single main table (`sql/index.sql`)
  - primary entry class in `Python/index.py` (modular layout optional; enabled if large or requested)

**Plural Module**
- Multiple required tables/units prefixed by `{Module}_` (example: `User_Address`, `User_Meta`)
- Planning MUST:
  - list all required tables touched by the API (base + part tables)
  - enforce base table requirement: `{Module}` table is mandatory in every module
    - if `{Module}_*` part tables are involved but base `{Module}` table is missing => add blocker `BLOCKER_BASE_TABLE_MISSING` and require `PySDK_Module_SQL_Update`
  - explicitly map which module-class method(s) handle which unit
  - assume modular layout exists/should exist: `index.py` + `Python/_Trait.py` + `Python/{Module}_{Part}.py`

---

## [04] What the planner MUST discover (per API target)
For each `{Module}/{ApiName}` target, planner must compute:

### A) Existence + action mode
- If `{ApiFile}` exists:
  - PlanMode = `Update`
  - Changes must be **modify-only** (no delete, no breaking changes unless approved)
- If `{ApiFile}` does NOT exist:
  - PlanMode = `Create`
  - API file will be created in Code workflow

### B) Required dependencies (must verify)
- Module folder exists?
- `Python/index.py` exists?
- `sql/index.sql` exists?
- If missing:
  - Add to plan: “Blocker: missing dependency”
  - Set target status = `Failed(DependencyMissing)`
  - (Plural workflow should skip code/docs for this target)

### C) API intent extraction from Description
Planner must extract into explicit constraints:
- What does API do? (create/update/get/list/delete/status/etc.)
- Input fields (name/type/required)
- Output schema (success/fail payload)
- Auth requirements (guest/user/admin/superadmin)
- Rate limit / abuse guard (if needed)
- Logging requirements (audit level)
- Error codes (must map to ACC.Errors)

### C.1) Base API patterns (must be planned)
Planner MUST align the plan to the current “Base API” reference unless explicitly overridden:
- Reference API: `/Aliens/PySDK/Py.26.00.00/Module/Teacher/api/Teachers.py`

Planner MUST explicitly write these decisions into every plan:
- Router URL examples use `https://base.software/?api=<ApiName>&...` (no direct file execution URLs)
- Response envelope keys: `s,t,c,o,paging,ok,code,trace`
- `$echo` is internal-only; public message uses separate variable (no leakage)

### D) Data mapping (table + columns)
Planner must:
- read/parse `{ModuleSql}` (even if approximate)
- identify main table(s) and primary keys
- list fields that API reads/writes
- define constraints: unique keys, indexing, status delete rule (soft delete)

### D.1) Schema Update Dependency (Non-Negotiable)
- If any **new table or column** is required by the API or class method changes:
  - MUST require `PySDK_Module_SQL_Update` for the same module.
  - Use the available `PySDK_Module_SQL_Update` workflow (do not block on availability).

### D.2) Table Naming Collision Gate (Non-Negotiable)
- New table names MUST be validated against existing schema names to avoid semantic duplicates
  (e.g., `w3_verified_contract` vs `w3_contract_verified`).
- Collision risk => fail fast and require explicit naming decision in Description.

### E) Integration points
Planner must list:
- Which PyOS engines are likely needed (Auth, DB, Cache, Validate, RateLimit, Log, etc.)
- Which module class methods to call (existing or new method required)
- Whether this API impacts other APIs / cron / webhook

### E.1) Module Class Update Rule (Non-Negotiable)
- If planner decides any new/updated class method is required:
  - MUST declare dependency on `PySDK_Module_Class_Update`.
  - Enforce **single-return** rule for touched methods:
    - Function must use **exactly one return**, only at the end.
    - Return is **mandatory**; no code allowed after return.
  - Enforce **Function Code Header** requirements:
    - Purpose (clear)
    - Inputs/Outputs
    - Dependencies (engines/modules/helpers)
    - Errors/Side-effects (if applicable)

### F) Security baseline
Planner must include:
- input validation strategy
- auth/permission strategy
- no direct SQL injection risk (use PyOS DB engine patterns)
- request tracing id + audit log requirement

### F.1) Aggregate methods (Enterprise baseline)
If the API is a listing/search endpoint OR intended as a base endpoint, planner MUST consider aggregates:
- Supported methods: `COUNT`, `SUM` (default)
- `methodcolumn` policy:
  - `COUNT`: any schema column (from `{ModuleSql}` allowlist)
  - `SUM`: numeric-only allowlist (planner must define numeric columns)
- Aggregates MUST ignore paging (LIMIT/OFFSET not applied)
- Plan must state how output should be returned:
  - `o` should return scalar aggregate (not list)
  - `paging` may still return input limit/offset but must not affect aggregate

### G) Hard Policy (Aliens Company) — API constraints (must be written in plan)
Planner MUST include these constraints explicitly in every plan:
- API file must NOT declare any `function` or `class`.
- API file must NOT execute SQL or build raw SQL queries.
- Any business logic must be implemented as methods inside the existing module class at `{ModuleClass}`.
- If module class already exists, planner must mark: “Do not create new class; only add methods if needed”.

Naming alignment (recommended; enforce unless explicitly overridden in Description):
- API `{ApiName}.py` should call module class method `{ApiName}()` (same identifier) to keep deterministic cross-layer naming.
- If method missing in module class, planner MUST add it as a dependency (create via `PySDK_Module_Class` / add method via class code workflow), not implement logic inside API.

---

## [05] Planning Output Format (per target)
Planner MUST generate one planning markdown per API target using this structure:

### File
`/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.plan.md`

### Sections (mandatory)
1. **Header**: RunId, RunItemId, WorkflowId, Target, DateTime
2. **PlanMode**: Create/Update + reason
3. **Resolved Paths**: ModuleRoot, ApiFile, ModuleClass, ModuleSql
4. **Goal (human)**: cleaned description in 2–5 bullet points
5. **API Contract**:
   - Endpoint name (logical)
   - Inputs (fields table)
   - Outputs (success/fail schema)
6. **Auth + Permission**
7. **Data Mapping**:
   - Tables involved
   - Columns touched
   - Constraints/index notes
8. **Implementation Steps** (stepwise checklist)
9. **Risks / Blockers**
10. **Approvals Needed** (based on manifest + safety ref)
11. **Definition of Done** (test checklist + lint + docs)

### Add-on Sections (required for Base/List APIs)
12. **Aggregates**:
  - Allowed methods + allowed columns (COUNT/SUM)
  - Whether aggregates ignore paging (default yes)
13. **Usage Examples**:
  - Must use `https://base.software/?api=<ApiName>&...` only

---

## [06] Save rules (mandatory)
Planner MUST save:
1) PlanningDoc (per API target) to:
   - `/Aliens/.Alien/{Developer_Username}/Planning/...`

2) A copy in RunRecord item:
   - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md`

Planner MUST also append `RunSummary` signals (as per `ACC.Output.md`):
- `PlanningCreated=true|false`
- `Blockers=[...]`
- `NextActions=[...]`

---

## [06.1] Memory Context (Scoped; Optional)
Memory can be used as **supporting context** only. It must NEVER override:
- current `Description`
- workflow SSOT docs
- disk reality

Runner behavior (if enabled in manifest + feature flags):
- For each run-item, the runner auto-attaches scoped memory into item artifacts:
  - `Memory.Context.json`
  - `Memory.Context.md`

Planner usage guidance:
- Use memory to recover prior decisions/constraints/pitfalls for the same scope.
- Never apply cross-scope memory.

Manual read (if needed):
- `Workflow("Memory-Read", "PySDK.Module.{Module}", "limit=10")`

---

## [07] Examples (interpretation only)
### Example 1
`Workflow("PySDK_Module_API", "User/UserNew", "New user create api with validation and proper errors")`
Planning must:
- resolve `/Aliens/PySDK/Py.26.00.00/Module/User/api/UserNew.py`
- determine create/update
- map table from `User/sql/index.sql`
- define input fields and auth strategy

### Example 2
`Workflow("PySDK_Module_API", "Web3/Web3_Accounts", "List accounts with pagination, secure by default")`
Planning must:
- include pagination parameters
- include rate limit + auth requirement (if any)
- define output list schema

---

## [08] What this file MUST NOT do
- Code generate nahi karega
- Files delete nahi karega
- DB migrations run nahi karega
- Unique code-style rules define nahi karega (Code workflow ka kaam hai)

---

## [09] Handoff to next workflow
Planner output must be sufficient so that:
- `PySDK_Module_API-Code.md` can generate correct API file(s)
- `PySDK_Module_API-Documentation.md` can generate docs in `/Aliens/Docs/PySDK/...`

