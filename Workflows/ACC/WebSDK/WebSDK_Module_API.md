---
WorkflowId: WebSDK_Module_API
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module/API"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-24
Summary: >
  Plural workflow that generates one or multiple WebSDK Module APIs based on Targets.
  It orchestrates Planning -> Code -> Documentation per target item.
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_API-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API-Reaudit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item must be ModuleName/ApiName."
  - Name: Description
    Type: string
    Required: false
    Notes: "Any human language. Requirements and constraints."
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: true
  DbTouch: "optional (only if workflow decides migration is required)"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Reaudit", "Documentation"]
  MaxReauditIterations: 0
FailureStrategy:
  FailFast: false
  OnPlanningFail: "skip Code+Documentation for that target"
  OnCodeFail: "Documentation may run but must clearly mark code generation failed"
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_API.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_API` ek **Plural** workflow hai jo 1 ya multiple **WebSDK Module APIs** generate karwata hai.

Example:
- `Workflow("WebSDK_Module_API", "User/UserNew, Account/AccountUpdate", "Aese kr do wese kr do")`

Iska matlab:
- 2 targets => same workflow **2 baar** run hoga (per target item).
- Har target item ke liye yeh 3 child workflows call karega:
  1) `WebSDK_Module_API-Planning`
  2) `WebSDK_Module_API-Code`
  3) `WebSDK_Module_API-Documentation`

---

## Target Grammar (Mandatory)
Each target item must be:

- `ModuleName/ApiName`

Examples:
- `User/UserNew`
- `Account/AccountUpdate`
- `Web3/Web3_Accounts`

### Notes (Module Types: Singular vs Plural) (SSOT)
- **Singular Module**: exactly 1 required DB table `{ModuleName}` (example: `User` => table `User`).
- **Plural Module**: multiple required DB tables + units prefixed by `{ModuleName}_` (example: `User` module may include tables/classes like `User_Address`, `User_Meta`).

Important:
- Module type is decided by required tables/units, not by underscore presence in `ModuleName`.

**Important:** Target grammar only tells parent/child relation. Actual type (API) is decided by this workflow id (`WebSDK_Module_API`).

---

## What this workflow MUST do (Orchestration Contract)
For each target item:

### Step 1 — Build RunContext
- Resolve developer: `/Aliens/manifest.json`
- Resolve mode: `employee | assistant`
- Resolve approval: `auto | manual`
- Resolve versions:
  - Preferred: latest available
  - Fallback: `PHP.26.00.00`

### Step 2 — Resolve target into structured payload
From `ModuleName/ApiName` create a payload:

- Domain: `WebSDK`
- ModuleName: `<ModuleName>`
- ApiName: `<ApiName>`
- TargetType: `api`
- WebSDK module base path:
  - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/`
- API output path:
  - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/api/{ApiName}.php`

### Step 3 — Call child workflows in order
Child workflow calls MUST pass at least:

- `Targets`: single target item (NOT full list)
- `Description`: inherited
- `ParentWorkflowId`: `WebSDK_Module_API`
- `RunContext`: developer + mode + approval + permissions + version
- `RunItemId`: stable id for this item

Calls:
1) `Workflow("WebSDK_Module_API-Planning", "<ModuleName/ApiName>", "<Description>")`
2) `Workflow("WebSDK_Module_API-Code", "<ModuleName/ApiName>", "<Description>")`
3) `Workflow("WebSDK_Module_API-Reaudit", "<ModuleName/ApiName>", "<Description>")`
4) `Workflow("WebSDK_Module_API-Documentation", "<ModuleName/ApiName>", "<Description>")`

Reaudit loop (Non-Negotiable):
- `compliant`    => proceed to Documentation.
- `noncompliant` => loop back to Planning -> Code -> Reaudit until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.
- See `_Common/Workflow_Plural.md` [05A] for full semantics.

---

## What this workflow MUST NOT do
- Is workflow me **unique planning/code/doc rules** detail me nahi likhne.
- Is workflow me direct code/templates generate nahi karne.
- Is workflow me filesystem/db actions implement nahi karne.
- Sirf orchestration + payload rules + validation + run-summary.

---

## Cross-Workflow Dependency Rules (Non-Negotiable)
When API create/update requires **module class changes** or **schema changes**, this parent workflow MUST enforce the correct dependency workflows:

1) **Module Class Update**
   - If API requires new or modified class method(s), this workflow MUST call:
     - `WebSDK_Module_Class_Update` (per target module)
   - Class update MUST follow the single-return rule and function header dependency rules (see child workflows).

2) **Module SQL Update (Schema change required)**
   - If any new table/column is needed by the API or class update:
     - MUST call `WebSDK_Module_SQL_Update` for the same module.
   - Use the available `WebSDK_Module_SQL_Update` workflow (do not block on availability).
   - How to call (same module):
     - `Workflow("WebSDK_Module_SQL_Update", "<ModuleName>", "<Description>")`
   - Target (what it updates):
     - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/sql/index.sql`
   - Guidance:
     - Call it ONLY when schema/table/column changes are required.

3) **Table Naming Collision Gate**
   - Any new table name MUST be validated against existing schema to avoid semantic duplicates
     (e.g., `w3_verified_contract` vs `w3_contract_verified`).
   - If collision risk found: fail fast and require explicit naming decision in Description.

---

## Validation Rules (Minimum)
For each target item:
1) `ModuleName` empty na ho
2) `ApiName` empty na ho
3) Only allowed characters policy:
   - `A-Z a-z 0-9 _` recommended
   - Spaces not allowed
4) If module folder missing:
   - Default behavior: planning should decide whether to create module scaffold or raise a clear error
   - If this becomes strict later, handle it in common refs/policy (not here)

---

## Update vs Create Policy (This workflow)
`WebSDK_Module_API` is a **create-or-update** generator by default:

- If API file does not exist => generate new
- If API file exists => update allowed (modify-only), but:
  - MUST preserve critical behavior
  - MUST follow AliensStyle / ecosystem standards (in Code workflow)
  - MUST log changed files in RunRecord

> Strict “missing target => error” policy applies to `_Update` workflows, not this one.

---

## Documentation Output Policy (High level)
Documentation workflow MUST save docs under:

- `/Aliens/Docs/WebSDK/`

Recommended doc placement per API:
- `/Aliens/Docs/WebSDK/Module/{ModuleName}/api/{ApiName}.md`

Exact structure will be enforced in:
- `Workflows/ACC/_Refs/ACC.Output.md`
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## Examples
### Example 1
`Workflow("WebSDK_Module_API", "User/UserNew", "Simple create API with secure validation")`

### Example 2 (multiple runs)
`Workflow("WebSDK_Module_API", "User/UserNew,User/Users,Web3/Web3_Accounts", "")`

This must run 3 times:
1) User/UserNew
2) User/Users
3) Web3/Web3_Accounts

---

## Success Criteria (for this Plural workflow)
This workflow is considered successful when:
- Every target item produced a RunItem record
- Child workflows were called in correct order
- RunSummary generated with:
  - succeeded / failed / partial counts
  - created/updated file list
  - approvals pending list (if any)

---

## Notes for Future Expansion
- Optional per-workflow YAML flags:
  - `DeduplicateTargets: true|false`
  - `DocsOnly: true` (skip code generation)
  - `PlanOnly: true` (skip code + docs)
- Hooks:
  - Before/After hooks should be supported by the ACC runtime (not defined here).
