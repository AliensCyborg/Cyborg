---
WorkflowId: PySDK_Module_Class-Planning
WorkflowName: PySDK Module Class Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_Class
Domain: PySDK
Scope: "Module -> Class"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-02
Inputs:
  Required:
    - Targets
  Optional:
    - Description
Outputs:
  Planning:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/Class/"
    Format: "md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"
  ProdWrite: "allowed (depends on manifest/approval) - planning only does not write prod"
  DbTouch: "allowed (depends on manifest/approval) - planning only does not execute"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PySDK.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Student/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Doctor/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Book/py/index.py"
---

# PySDK_Module_Class-Planning.md

## [00] Purpose
Yeh workflow **sirf planning generate** karta hai for:  
`Workflow("PySDK_Module_Class", "User, Account", "...")`

Meaning:
- Targets me jo bhi items hain (User, Account, Web3, etc.) unke liye **module ki main class** (`Python/index.py`) ko analyze + plan banana.
- Output plan ko `.Alien` ke Planning folder me save karna, taaki Code workflow same plan follow karke enterprise-grade class generate/update kar sake.

> Important: Yeh workflow **module create nahi** karta.  
> Agar module folder hi missing ho, to **error**. Module create karne ke liye `PySDK_Module` workflow use hoga.

---

## [01] Mandatory Includes (Non-negotiable)
Is workflow file ke top execution section me (runtime engine me) yeh includes mandatory hain:

1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

 Yeh common files:
- Targets parsing contract
- Manifest context
- Safety/approval rules
- Output conventions
- Audit/run-record conventions
provide karti hain.

---

## [02] Input Contract
### Targets
- Comma-separated list of modules: `User, Account`
- Har target -> ek module
- **File-scope (Part Create):** `Module/FileBase` (example: `W3/W3_Token_Wallet`)

### Description
- Human language me custom requirement
- Agar blank ho, to default assumption: “standard enterprise module class”.

---

## [03] Target Resolution Rules (PySDK Module)
For each Target (ModuleName):

### Step 0: Singular Entity Name Gate (SSOT)
- ModuleName MUST be singular (never plural).
- Planning MUST ensure extracted table names are singular.
- If the ModuleName (or any table name detected from SQL) is plural => fail with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

### Step A: Resolve Version + Base Paths
- Version: **latest** (fallback `Py.26.00.00` if auto decide possible nahi)
- PySDK Root:
  - `/Aliens/PySDK/{Version}/Module/`

### Step B: Resolve Module Folder
- ModuleDir:
  - `/Aliens/PySDK/{Version}/Module/{ModuleName}/`

If ModuleDir missing:
- Fail this target with error:
  - `MODULE_NOT_FOUND`

### Step C: Resolve Expected Files
- Class file (primary):
  - `/Aliens/PySDK/{Version}/Module/{ModuleName}/py/index.py`
- SQL structure (preferred source of truth):
  - `/Aliens/PySDK/{Version}/Module/{ModuleName}/sql/index.sql`
- Optional directories (future use):
  - `/api/`, `/cron/`, `/webhook/`, `/component/`, `/template/`, `/css/`

If `Python/index.py` missing:
- Do NOT fail for create workflow.
- Mark as `CreateScenario = true` in plan and proceed.
- Code workflow will create a new class skeleton at this path.

Note:
- “Modify-only, missing ho to error” rule applies to `PySDK_Module_Class_Update` workflow, not to this create workflow.

---

## [04] Module Type Detection (Singular vs Plural) (SSOT)
Your module types are defined by **required DB tables + prefix family**, not by underscore presence in `ModuleName`.

Definitions:
- `SingularModule`:
  - Exactly 1 required table: `{ModuleName}`
  - Example: Module=`User` => Table=`User`
- `PluralModule`:
  - Multiple required tables and units prefixed by `{ModuleName}_`
  - Example: Module=`User` => Tables/Classes like `User_Address`, `User_Meta`

Detection (preferred evidence order):
1) Parse `/sql/index.sql` (preferred; deterministic)
   - Extract all `CREATE TABLE` names (normalize to BaseTableName as per DB prefix law)
   - If there exists any table matching `^{ModuleName}_[A-Za-z0-9]+$` (besides base `{ModuleName}`) => `ModuleType = PluralModule`
   - Else => `ModuleType = SingularModule`
2) If SQL missing: inspect module Python folder
   - If any file matches `{ModuleName}_*.py` => `ModuleType = PluralModule`
3) If still unclear: inspect existing method names in `Python/index.py`
   - If any public method matches `^{ModuleName}_[A-Za-z0-9]+` => `ModuleType = PluralModule`

If SQL file missing:
- Plan MUST include warning: `SQL_STRUCTURE_MISSING`.
- Plan MUST still enforce: “Har module ke liye DB table required hai”; therefore add a blocker note: “Define/Update tables via PySDK_Module_SQL_Update before finalizing code behavior.”

---

## [05] Table Discovery Strategy (Preferred Order)
For each ModuleName:

1) Parse `/sql/index.sql`:
   - Identify `CREATE TABLE` statements
   - Extract table names list
2) If SQL missing:
  - Heuristic:
    - Assume at least base `{ModuleName}` table exists (required).
    - If any `{ModuleName}_*` part units are detected from Python/methods => include those part tables in `Tables[]` as *required-by-contract* and mark as `TABLE_REFERENCE_INFERRED` (must be confirmed via SQL update).
3) If both unavailable:
   - Error in plan: `TABLES_UNRESOLVED`

Output plan must include:
- Tables[] (final list)
- PrimaryTable (if determinable)
- KeyColumns (id, uid, status, created_at etc. if present)

### 5.1 Hard Gate: Base table is mandatory (SSOT)
Rule (non-negotiable): every module MUST have its base table named exactly `{ModuleName}`.

If `ModuleType = PluralModule`:
- Planning MUST ensure `Tables[]` includes base `{ModuleName}` table in addition to any `{ModuleName}_*` part tables.
- If SQL/inspection detects `{ModuleName}_*` tables but base `{ModuleName}` is missing:
  - Mark plan as blocked with deterministic blocker: `BLOCKER_BASE_TABLE_MISSING`.
  - Include fix instruction: “Add base table `{ModuleName}` via `PySDK_Module_SQL_Update` before proceeding with code changes.”

Database prefix law (planning-level):
- Planning MUST store **base table names** for PySDK module code generation (no hardcoded prefix).
- If SQL/index.sql contains prefixed names (e.g., `Coinpods_teacher_attendance`), planning should record:
  - `RawTableName` (as detected)
  - `BaseTableName` (prefix removed where determinable)
- If prefix cannot be determined safely, keep `BaseTableName = RawTableName` but emit warning: `TABLE_PREFIX_UNRESOLVED`.

---

## [06] Existing Class Analysis (Index.py)
Planning must scan current `Python/index.py` to decide:

- ClassName (existing or expected)
- Methods present (CRUD + custom)
- Dependencies used (DB engine, Auth engine, Cache, etc.)
- Style compliance signals (AliensStyle header? logging? naming?)

Planning output should produce:
- `CurrentState` summary
- `GapAnalysis` (what is missing vs expected)
- `UpgradePlan` (what to add/update)

---

## [06A] Modular Class Plan (Large-file Prevention) (SSOT)
Goal: module class ko **tukdo me split** karna without breaking old code.

### A) File Map (deterministic)
If modularization is enabled (default = enabled when file is large OR Description asks), planner MUST output this file map:
- Main class (entrypoint; backward compatible):
  - `{MainClassFile} = /Aliens/PySDK/{Version}/Module/{ModuleName}/py/index.py`
- Facade traits file (wrapper methods):
  - `{TraitFile} = /Aliens/PySDK/{Version}/Module/{ModuleName}/py/_Trait.py`
- Partition classes (feature chunks):
  - `{PartFilePattern} = /Aliens/PySDK/{Version}/Module/{ModuleName}/py/{ModuleName}_{Part}.py`

Examples for `User`:
- `Python/index.py`
- `Python/_Trait.py`
- `Python/User_Address.py` (class `User_Address`)
- `Python/User_Meta.py` (class `User_Meta`)

### B) Enable/Disable rule
Planner MUST decide `PartitionPlan.Enabled`:
- If `ModuleType = PluralModule`:
  - `PartitionPlan.Enabled` MUST be `true` (structural requirement; plural units must live in separate part files).
- Else if `ModuleType = SingularModule`:
  - Enable if ANY of these is true:
    - Existing `Python/index.py` is large (recommended heuristic): `> 1500 lines` OR `> 80 public methods` OR `> 200 KB`
    - Description contains: `split|modular|partition|tukdo|facade` (case-insensitive)
  - Else: keep disabled (default).

### C) Partition discovery (deterministic)
Planner MUST propose `Parts[]` using this order:
1) From existing public method names:
   - Group methods matching pattern: `^{ModuleName}_[A-Za-z0-9]+` by `{Part}` token.
  - Example: `User_AddressNew` => Part=`Address`
  - Naming rule (NON-NEGOTIABLE): Part class/file name is **exactly** `{ModuleName}_{Part}`.
    - Correct: `W3_Account` (file: `W3_Account.py`, import: `import('W3/W3_Account')`)
    - Forbidden: `W3_AccountPart` / `{ModuleName}_{Part}Part`
2) From SQL tables (if present):
  - For plural modules, prefer parts that match `{ModuleName}_{Part}` table names.
3) If still unclear:
   - Do NOT invent parts; keep plan non-modular and write warning `WARN_PARTS_UNRESOLVED`.

### D) Backward compatibility guarantee (non-negotiable)
Planner MUST ensure the final design preserves:
- Existing construction style: `$Obj = new {ModuleName}();`
- Existing calls: `$Obj->{ModuleName}_{Part}*()` must still work.

Implementation constraint to enforce in plan:
- Public method names MUST NOT be removed or renamed.
- The main class keeps wrapper methods via facades and delegates to part classes.

### E) Method placement rules (must be listed in plan)
Planner MUST output:
- `MainClassMethods[]`: module core methods (bootstrap, main CRUD, module lifecycle)
- `PartMethods[{Part}][]`: methods whose names start with `{ModuleName}_{Part}`

If migrating an existing method body:
- Replace body on main class with a thin delegation wrapper (or move wrapper into `*_Facade` trait).
- Move full implementation into `{ModuleName}_{Part}` class.

---

## [07] Expected Class Capabilities (Planning Checklist)
Planning must decide which of these should exist:

### A) Core CRUD (minimum)
- RowById / Row / Get
- Insert / New
- Update
- StatusUpdate (if `status` column exists)
- Filter / List (criteria-based)

### B) Validations
- Required fields checks
- Data type normalization
- Safe defaults

### C) Security-by-default
- Input sanitization strategy (consistent)
- Auth/permission hooks (if module used by APIs)
- Prevent unsafe SQL patterns

### D) Performance
- Index suggestions (from SQL / common queries)
- Caching candidate methods (read-heavy)
- Pagination strategy for list methods

### E) Extensibility
- Hooks points: Before/After actions (if your PyOS pattern supports)
- Meta strategy (if module uses meta tables)

### F) Cross-Layer Naming Alignment (SSOT; deterministic)
When a module is used end-to-end (PySDK Class + PySDK API + PyApp UI):
- Prefer **same identifiers** across layers for clarity and traceability.
  - Module class method: `{Name}()`
  - API file: `{Name}.py`
  - (If UI is also aligned) Screen/Component seeds: `{Name}` (UI naming rules still apply: AppName dot-prefix + normalization)

Hard boundary reminder:
- API file contains routing/validation envelope only; business logic MUST live in module class methods.
- PyApp UI artifacts compose/render only; business logic still lives in PySDK.

Explicit preset: `User` module surface (no guessing; dedupe duplicates)
- If `ModuleName == 'User'`, planning MUST consider these expected public methods (add only when needed by requirements / APIs):
  - `User()`
  - `Users()`
  - `UserNew()`
  - `UserUpdate()`
  - `UserStatus()`
  - `User_Address()`
  - `User_Addresses()`
  - `User_AddressNew()`
  - `UserAddressStatus()`
  - `User_Meta()`
  - `User_Metas()`
  - `User_MetaNew()`
  - `User_MetaStatus()`

> Note: Exact implementation rules Code workflow me honge.  
> Planning ka kaam: **clear blueprint**.

---

## [08] Output Plan Format (Per Target)
Planning file per module target must be saved as:

- Module scope: `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/Class/{ModuleName}.md`
- File scope: `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/Class/{ModuleName}.{FileBase}.md`

Plan template (minimum sections):
1) Overview (ModuleName, ModuleType, Version)
2) Resolved Paths (ModuleDir, ClassFile, SqlFile)
3) Tables & Columns summary
4) Current Class Snapshot (high-level)
5) Required Methods list (CRUD + custom)
6) Security plan
7) Performance plan
8) Testing plan (smoke tests)
9) Risks & decisions
10) Next step: Code workflow instructions

Additionally (Mandatory):
- Clearly state whether this target is `CreateScenario` (index.py missing) or `UpdateScenario` (index.py exists).
- Include an explicit checklist for AliensStyle rules to enforce in code generation:
  - SECTION divider before each public method
  - Internal `/* [CODEHEADER] */` inside each method body
  - `$echo` + `d()` entry/branch logging
  - `$Python['{Module}']['function'][]` manifest completeness
  - No `function_exists()` guards for core primitives in PySDK modules
  - DB prefix law (no hardcoded prefixed names)
  - English-only developer comments/log strings

---

## [09] Error & Warning Codes (for this workflow)
Errors:
- `MODULE_NOT_FOUND`
- `TABLES_UNRESOLVED`

Warnings:
- `SQL_STRUCTURE_MISSING`
- `STYLE_NON_COMPLIANT` (if headers/logging missing)
- `UNKNOWN_DEPENDENCIES` (if file uses unexpected includes)

---

## [10] Human Instruction Notes (Premium Quality)
Agar Description me “premium / enterprise” mention ho:
- Plan me extra sections add hon:
  - API contract alignment notes
  - Standard logging/audit coverage
  - Documentation requirements mapping (Docs workflow ke liye)

---

## [11] Final Output Rule
Is workflow ka output:
- Sirf planning files generate karega (in `.Alien/Planning/...`)
- Production code me koi change nahi karega
- RunRecord me per-target status + summary store karega (common rules ke through)

