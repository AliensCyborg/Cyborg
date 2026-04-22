---
WorkflowId: PySDK_Module_Class
WorkflowName: PySDK Module Class
Type: Workflow_Plural
Domain: PySDK
Category: Module
Intent: "Ensure/Generate a module primary class (index.py) for one or multiple modules"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Example: "User, Account, Web3"
  - Name: Description
    Type: string
    Required: false
    Example: "Class ko enterprise grade banana hai, extra safe by default"
TargetRules:
  TargetKind: "ModuleName | ModuleName/FileBase"
  Separator: ","
  Meaning:
    - "Comma => same workflow repeat per target item"
    - "No slash => Target is a Module"
    - "One slash => create a Module Part file (Python/{FileBase}.py) and wire it into index.py"
  ModuleTypeRule:
    - "Singular Module => exactly 1 required DB table: {ModuleName}"
    - "Plural Module => multiple required DB tables + units prefixed by {ModuleName}_ (e.g., {ModuleName}, {ModuleName}_Address, {ModuleName}_Meta)"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "PySDK_Module_Class-Planning"
  - "PySDK_Module_Class-Code"
  - "PySDK_Module_Class-Documentation"
DefaultOrder: ["Planning", "Code", "Documentation"]
PermissionsRequested:
  NoDelete: true
  ProdWrite: true
  DbTouch: false
  Network: false
Approval:
  Default: "inherit developer manifest (auto/manual)"
  Notes:
    - "No destructive actions; mostly file create/update."
Outputs:
  Planning:
    Location: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{ModuleName}/Class.md"
  Code:
    PrimaryFile: "/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/index.py"
  Documentation:
    Location: "/Aliens/Docs/PySDK/Module/{ModuleName}/Class.md"
---

# PySDK_Module_Class.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

**Purpose (Hinglish):**  
Yeh workflow PySDK ke kisi bhi Module ka **primary class** (usually `Python/index.py`) ko **generate/update** karne ke liye hai.  
Is workflow ka focus: **module class ka skeleton, core methods, CRUD/utility integration, aur enterprise-grade structure** ko consistent banana.

> Yeh file **Plural** hai: 1 ya zyada modules par loop karke child workflows run karegi.

---

## [01] What this workflow DOES
For each Module in Targets list:
1) Module ko locate/validate karega (module folder + version decide).
2) Planning workflow se plan banwayega (context + existing code inspection).
3) Code workflow se `Python/index.py` generate/update karwayega.
4) Documentation workflow se docs generate karwayega (module class overview + usage).

---

## [02] What this workflow DOES NOT do
- Module folder create karna (wo `PySDK_Module` ka kaam hai).
- API/Cron/Webhook files create karna (unke alag workflows hain).
- Database schema create/migrate execute karna (read-only referencing allowed; DB touch false by default).

---

## [03] Target interpretation (STRICT)
Targets examples:
- `Workflow("PySDK_Module_Class", "User, Account", "…")`
  - `User` module
  - `Account` module

Module Types (SSOT):
- **Singular Module**: exactly 1 required DB table named `{ModuleName}` (example: module `User` => table `User`).
- **Plural Module**: multiple required tables and part units prefixed by `{ModuleName}_` (example: module `User` => tables/classes like `User_Address`, `User_Meta`).

Important:
- Module type is decided by **required tables + prefix family**, not by underscore presence in ModuleName.
- Target for this workflow is always the **ModuleName** (e.g., `User`), even when the module has plural units.

If a target contains slash (e.g., `W3/W3_Token_Wallet`):
- Treated as **Part Create** under the same module.
- It will create `/py/W3_Token_Wallet.py` (class `W3_Token_Wallet`) and wire it into `Python/index.py` so the main module object can access it.
- If you want to update only an existing part file, prefer `PySDK_Module_Class_Update` with `Module/FileBase`.

---

## [04] Preflight validations (Mandatory)
Per target:
1) Resolve LatestVersion:
   - Prefer “latest” policy (fallback Py.26.00.00 if auto decide not possible).
2) Resolve module root:
   - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/`

3) Singular Entity Name Rule (SSOT):
  - ModuleName MUST be singular (never plural).
  - ClassName derived from ModuleName MUST be singular.
  - Table names referenced/derived for the module MUST be singular.
  - If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.
3) Must exist:
   - If missing => error: “Module not found. Run PySDK_Module first or create module structure.”
4) Identify existing assets (optional but recommended):
   - `Python/index.py` (existing class)
   - `sql/index.sql` (table structure reference)
   - `api/`, `cron/`, `webhook/` (for context only)

---

## [05] Orchestration (How this file calls child workflows)
For each module target item, build payload:
- Targets: single module name (e.g., `User`)
- Description: inherited from parent call
- ParentWorkflowId: `PySDK_Module_Class`
- RunContext: developer manifest + version + permissions + theme `_Basic`
- Hints:
  - `ModuleType`: Singular|Plural (based on planning detection)
  - `ModuleRoot`: resolved absolute path

Then call in order:
1) `Workflow("PySDK_Module_Class-Planning", "{ModuleName}", "{Description}")`
2) `Workflow("PySDK_Module_Class-Code", "{ModuleName}", "{Description}")`
3) `Workflow("PySDK_Module_Class-Documentation", "{ModuleName}", "{Description}")`

> Child workflows MUST follow their own Singular common rules and must write outputs to declared locations.

---

## [06] Code generation expectations (High-level)
Actual unique code rules live in `PySDK_Module_Class-Code.md`, but parent workflow MUST enforce:
- Update policy: **no delete**
- Preserve existing important logic (no destructive overwrites)
- Enterprise conventions: consistent structure, readable sections, predictable naming
- Security by default: validate inputs, avoid unsafe eval/exec, avoid secret leakage
- Performance by default: avoid heavy loops/queries without caching strategy (where relevant)
- **Database prefix law**: never hardcode full table names (e.g., `Coinpods_...`) in PySDK modules; use base names only (see `Workflows/ACC/_Refs/PySDK.md`).

### [06A] Modular class layout (Large-file prevention)
When module class is becoming too large, workflows MAY (and Update workflows SHOULD, when recommended by Audit/Planning) use modular layout:
- Primary entry point remains: `/py/index.py`
- Facade traits file: `/py/_Trait.py`
- Partition/part files: `/py/{ModuleName}_{Part}.py`

Hard requirement: backward compatibility MUST be preserved.
- Existing public methods MUST remain callable on `{ModuleName}` (via traits/wrappers/delegation).
- No rename/remove of public methods unless explicitly approved.

SSOT details:
- Planning: `PySDK_Module_Class-Planning.md` (`PartitionPlan.Enabled`)
- Code: `PySDK_Module_Class-Code.md` (require/use/delegation rules)
- Update: `PySDK_Module_Class_Update-*` (audit recommendation + migration plan + non-destructive move rules)

If existing `Python/index.py` found:
- Code workflow must do safe update:
  - add missing sections/methods
  - keep existing custom methods untouched unless explicitly required
  - avoid breaking public API

If missing:
- Create new `Python/index.py` with full required structure.

Create vs Update clarity (Strict):
- This workflow (`PySDK_Module_Class`) is allowed to create a missing `Python/index.py`.
- Update-only workflow (`PySDK_Module_Class_Update`) MUST error if `Python/index.py` is missing.

---

## [07] Documentation expectations (High-level)
Docs workflow output must include:
- Module summary + purpose
- Standard vs Cluster explanation (based on underscore)
- File map of module folder (Python/api/cron/webhook/sql)
- Public methods list (from class)
- Examples: how other engines/modules should call it

Docs location:
- `/Aliens/Docs/PySDK/Module/{ModuleName}/Class.md`

---

## [08] Failure handling
Per module target:
- If Planning fails => skip Code + Docs for that module.
- If Code fails => Docs may run but must label “code generation failed”.
- If Docs fails => mark module as “partial success”.

Overall run summary must aggregate:
- Success/Failed/Partial counts
- CreatedFiles/UpdatedFiles per module
- Errors/Warnings list

---

## [09] Quality Gate (Parent responsibility)
Parent workflow MUST require:
- Child workflows produce:
  - RunRecord item logs
  - clear outputs
  - no hidden destructive actions
- If manual approval enabled:
  - approval request must be created before any ProdWrite change

Mandatory rule inheritance (applies to all child workflows):
- AliensStyle (SECTION + CODEHEADER + logging)
- DB prefix law (never hardcode prefixed table names)
- English-only developer comments/log strings
- No `function_exists()` guards inside PySDK modules for core primitives
- `$Python['{Module}']['function'][]` list must cover every public entry point

---

## [10] Quick usage examples
1) Two modules:
`Workflow("PySDK_Module_Class", "User, Account", "Class ko enterprise grade banana hai")`

2) Plural module (multi-table + units):
`Workflow("PySDK_Module_Class", "User", "Plural module hai (User + User_Address + User_Meta). Modular layout enforce karo")`

---

## [11] Notes for future (Extensibility)
Recommended future flags (optional, can be added later via YAML):
- `DeduplicateTargets: true|false`
- `StrictUpdateOnly: true|false`
- `EnforceMethods: [List]` (required methods for the class)
- `AutoDiscoverTables: true|false` (via sql/index.sql or DB introspection if enabled)
