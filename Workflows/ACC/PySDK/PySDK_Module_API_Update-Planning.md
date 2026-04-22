---
WorkflowId: PySDK_Module_API_Update-Planning
WorkflowName: PySDK Module API Update Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_API_Update
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-26
Inputs:
  - Targets: "Module/ApiName, Module/ApiName2, ..."
  - Description: "Any human language update requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.plan.md"
  - AuditDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.audit.md"
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
  - ModifyOnly: true
  - MissingTargetIsError: true
  - Theme: "_Basic"
  - DefaultVersionFallback: "Py.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Products.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/ProductNew.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product_Specifications.py"
---

# PySDK_Module_API_Update-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

**Role (Hinglish):**  
Yeh file `PySDK_Module_API_Update` ke liye **UPDATE Planning generator** hai.  
Iska kaam: target API ko locate karke (must exist), existing behavior ko read karke, aur description ke hisaab se **safe update plan** banana.

> Important: Is file me sirf PySDK API UPDATE planning ke unique rules honge.  
> Common planning rules `_Common/Workflow_Singular.md` + `_Common/Workflow_Singular-Planning.md` se aayenge.

---

## [01] Input meaning (unique for UPDATE)
Targets:
- `Module/ApiName` => PySDK module + API name (without extension)
- Comma separated targets => workflow multiple times run hoga.

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Planner MUST ensure any table names referenced/derived from SQL are singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Module-only target (Batch Update Mode):
- If target is ONLY a module name (no `/`), example:
  - `Teacher`
- Then it means: **update all existing APIs for this module**.
- In this mode workflow MUST:
  - Enumerate existing API files:
    - `{ModuleRoot}/api/*.py` (non-recursive)
  - Derive `ApiName` from filename (without `.py`)
  - Run UPDATE planning for each existing API
- Missing APIs MUST NOT be created in this workflow.

If module has no API files:
- Mark run as Completed(NoTargets) (nothing to update).

Description:
- Update requirements (bug fix, security hardening, fields add/remove, validation changes, etc.)

---

## [02] Resolve paths (unique mapping)
Using `ACC.Paths.md` resolver contract:

### PySDK version
- primary: latest `Python.{Latest}`
- fallback: `Py.26.00.00`

### Module root
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`

### API file (MUST exist for update)
- `{ApiFile} = {ModuleRoot}/api/{ApiName}.py`

### Module class (expected)
- `{ModuleClass} = {ModuleRoot}/py/index.py`

### Module SQL (expected)
- `{ModuleSql} = {ModuleRoot}/sql/index.sql`

### Planning output (update specific)
- `{PlanDoc} = /Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.plan.md`

---

## [03] Mandatory prerequisites (UPDATE strict)
Planner MUST verify:

0) Deterministic audit artifact exists
   - `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.audit.md`
   - If missing:
     - `Failed(AuditMissing)`
     - Do not write update plan doc

1) `{ApiFile}` exists  
   - If missing:
     - `Failed(TargetMissing)`
     - Do not create any file/folder
     - Do not write update plan doc (optional: write run-record with the failure only)

2) `{ModuleRoot}` exists
3) `{ModuleClass}` exists (95% case)
4) `{ModuleSql}` exists (for schema/allow-list rules)

If (2)(3)(4) missing:
- Mark `Failed(DependencyMissing)` and list missing items clearly.

---

## [04] Update planning must include (per API target)
Planner MUST compute:

### A) PlanMode
- Always: `Update` (non-negotiable)

### B) Baseline extraction (read before plan)
Planner MUST read and summarize:
- Current `{ApiFile}` behavior contract (inputs, outputs, auth gate, error style)
- Any referenced module method names (from `{ModuleClass}`)
- Any schema constraints (from `{ModuleSql}`)

Planner MUST also verify and capture these Base API baseline signals (Teachers reference):
- Routed URL pattern used in examples (if any): `https://base.software/?api=<ApiName>&...`
- Response envelope keys present: `s,t,c,o,paging,ok,code,trace`
- Logging pattern:
  - `$echo = '...'; d($echo, __LINE__, '<type>');`
  - `$echo` is internal-only; public response uses safe message field
- Aggregate mode support (if present or requested): `method=COUNT|SUM` + `methodcolumn` allowlists
- Paging semantics:
  - Aggregates must ignore paging (LIMIT/OFFSET not applied)

### C) Proposed change set (from Description)
Planner MUST convert description into explicit change items:
- Add/modify validation rules (types, required, length, allow-lists)
- Add/modify request params
- Add/modify output fields (non-breaking preferred)
- Auth/permission changes (may require approval)
- Performance changes (indexes suggestion only; no DB execute here)
- Security hardening (rate-limit hook, audit logging)

### C.2) Module Class Update Rule (Non-Negotiable)
- If any new/updated class method is required by the update:
  - MUST declare dependency on `PySDK_Module_Class_Update`.
  - Enforce **single-return** rule in touched methods:
    - exactly one return at the end
    - return is mandatory; no code after return
  - Enforce function code-header requirements:
    - Purpose + Inputs/Outputs + Dependencies + Errors/Side-effects (if applicable)

### C.1) Default improvements for UPDATE (safe, non-breaking)
Unless the plan explicitly forbids, planner should propose these default improvements (mark as NonBreaking):
- Normalize logging calls to AliensStyle `d($echo, __LINE__, '<type>')`
- Replace any direct-file execution examples with routed URL examples
- Add schema allowlists for `sort_column` and aggregate `methodcolumn`
- Add aggregate support (`COUNT`/`SUM`) only if it does not break existing clients

### D) Backward compatibility law (default)
Planner MUST assume:
- Existing clients may depend on current response shape
- Breaking changes require explicit approval signals (manifest policy)

So plan must label every change as:
- NonBreaking | Breaking | Risky

### E) Hard policy constraints (must be written into plan)
Planner MUST restate:
- API file must NOT declare any function/class
- API file must NOT execute SQL or build raw SQL queries
- Any business logic must live in existing module class (`Python/index.py`)
- No deletion allowed (files or behaviors) unless explicitly approved

### E.1) Schema Update Dependency (Non-Negotiable)
- If any **new table or column** is required:
  - MUST require `PySDK_Module_SQL_Update` for the same module.
  - Use the available `PySDK_Module_SQL_Update` workflow (do not block on availability).

### E.2) Table Naming Collision Gate (Non-Negotiable)
- New table names MUST be validated against existing schema names to avoid semantic duplicates
  (e.g., `w3_verified_contract` vs `w3_contract_verified`).
- Collision risk => fail fast and require explicit naming decision in Description.

---

## [05] Planning output format (update plan)
File:
- `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.plan.md`

Sections (mandatory):
1) Header: RunId, RunItemId, WorkflowId, Target, DateTime
2) PlanMode: Update
3) Resolved Paths
4) Baseline Summary (current behavior snapshot)
5) Desired Changes (from Description -> bullets)
6) Change Classification (NonBreaking/Breaking/Risky)
7) Contract Update
   - Inputs table (before/after)
   - Outputs (before/after)
8) Auth + Permission Update
9) Data Mapping / Schema Notes (from `{ModuleSql}`)
10) Implementation Steps (checklist for Code workflow)
11) Risks / Blockers
12) Approvals Needed (manifest-based)
13) Definition of Done

Add-on Sections (required when API is list/search or base-like):
14) Aggregates
  - Allowed methods: COUNT, SUM
  - Allowed columns (schema allowlist + numeric-only allowlist)
  - Paging behavior (aggregates ignore LIMIT/OFFSET)
15) Usage Examples
  - Must use `https://base.software/?api=<ApiName>&...` only

---

## [06] Save rules (mandatory)
Planner MUST save:
1) Update plan doc to PlanningRoot path (above)
2) RunRecord item to:
   - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md`

---

## [07] What this workflow MUST NOT do
- Code generate/update nahi karega
- Files delete/create nahi karega (update-only)
- DB migrations execute nahi karega
- Plural loop/orchestration nahi karega
