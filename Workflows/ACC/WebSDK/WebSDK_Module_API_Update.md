---
WorkflowId: WebSDK_Module_API_Update
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module/API"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-26
Summary: >
  Plural UPDATE-only workflow that updates one or multiple WebSDK Module APIs based on Targets.
  This workflow MUST NOT create missing APIs. For each target, it orchestrates:
  Audit (Read-only) -> Planning (Update) -> Code (Modify-only) -> Documentation (Update notes).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_API_Update-Audit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API_Update-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API_Update-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API_Update-Reaudit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_API_Update-Doc.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item must be ModuleName/ApiName."
  - Name: Description
    Type: string
    Required: false
    Notes: "Any human language. Update requirements, constraints, and intent."
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
  ModifyOnly: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: true
  DbTouch: "optional (only if workflow decides migration is required)"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Audit", "Planning", "Code", "Reaudit", "Documentation"]
  MaxReauditIterations: 0
UpdatePolicy:
  MissingTarget: "ERROR (do not create)"
  ExistingTarget: "Modify-only (no delete, no destructive changes unless approved)"
FailureStrategy:
  FailFast: false
  OnTargetMissing: "skip Audit+Planning+Code+Doc for that target and record Failed(TargetMissing)"
  OnAuditFail: "skip Planning+Code+Doc for that target"
  OnPlanningFail: "skip Code+Doc for that target"
  OnCodeFail: "Doc may run but must clearly mark code update failed"
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_API_Update.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_API_Update` ek **Plural** workflow hai jo 1 ya multiple **existing** WebSDK Module APIs ko update karwata hai.

Example:
- `Workflow("WebSDK_Module_API_Update", "User/UserNew, Account/AccountUpdate", "Existing APIs me validation improve karo")`

Iska matlab:
- 2 targets => same workflow **2 baar** run hoga (per target item).
- Har target item ke liye yeh 4 child workflows call karega:
  1) `WebSDK_Module_API_Update-Audit`
  2) `WebSDK_Module_API_Update-Planning`
  3) `WebSDK_Module_API_Update-Code`
  4) `WebSDK_Module_API_Update-Doc`

---

## Target Grammar (Mandatory)
Each target item must be:

- `ModuleName/ApiName`

Examples:
- `User/UserNew`
- `Account/AccountUpdate`
- `Web3/Web3_Accounts`

**Important:** Actual type (API) is decided by this workflow id (`WebSDK_Module_API_Update`).

---

## UPDATE-only Contract (Non-Negotiable)
For each target item:

1) Resolve target -> `{ApiFile}` path.
2) If `{ApiFile}` does NOT exist:
   - This is an UPDATE workflow, so:
   - MUST record `Failed(TargetMissing)`
   - MUST NOT create file/folder
   - MUST NOT call child workflows for this target
3) If `{ApiFile}` exists:
   - Call child workflows in order:
  - Audit (read-only)
     - Planning (update plan)
     - Code (modify-only update)
     - Docs (update notes + final docs)

---

## Orchestration Contract (per target item)

### Step 1 — Build RunContext
- Resolve developer manifest: `/Aliens/manifest.json`
- Resolve mode: `employee | assistant`
- Resolve approval: `auto | manual`
- Resolve versions:
  - Preferred: latest available
  - Fallback: `PHP.26.00.00`

### Step 2 — Resolve target into structured payload
From `ModuleName/ApiName` create payload:

- Domain: `WebSDK`
- ModuleName: `<ModuleName>`
- ApiName: `<ApiName>`
- TargetType: `api`
- WebSDK module base path:
  - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/`
- API output path (must already exist):
  - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/api/{ApiName}.php`

### Step 3 — Call child workflows in order
Child calls MUST pass at least:
- `Targets`: single target item (NOT full list)
- `Description`: inherited
- `ParentWorkflowId`: `WebSDK_Module_API_Update`
- `RunContext`: developer + mode + approval + permissions + version
- `RunItemId`: stable id for this item

Calls:
1) `Workflow("WebSDK_Module_API_Update-Audit", "<ModuleName/ApiName>", "<Description>")`
1) `Workflow("WebSDK_Module_API_Update-Planning", "<ModuleName/ApiName>", "<Description>")`
2) `Workflow("WebSDK_Module_API_Update-Code", "<ModuleName/ApiName>", "<Description>")`
3) `Workflow("WebSDK_Module_API_Update-Doc", "<ModuleName/ApiName>", "<Description>")`

---

## What this workflow MUST NOT do
- Is workflow me unique planning/code/doc rules detail me nahi likhne.
- Is workflow me direct code/templates generate nahi karne.
- Is workflow me filesystem/db actions implement nahi karne.
- Sirf orchestration + payload rules + validation + run-summary.

---

## Cross-Workflow Dependency Rules (Non-Negotiable)
Update run me agar API/class change se **schema change** ya **class method change** require ho to yeh parent workflow MUST enforce:

1) **Module Class Update**
   - Required when any class method is added/modified for the API:
     - `WebSDK_Module_Class_Update` MUST be called for that module.

2) **Module SQL Update (Schema change required)**
   - If new table/columns required:
     - MUST call `WebSDK_Module_SQL_Update`.
   - Note:
     - `WebSDK_Module_SQL_Update` is available and should be used instead of blocking.
   - How to call (same module):
     - `Workflow("WebSDK_Module_SQL_Update", "<ModuleName>", "<Description>")`
   - Target (what it updates):
     - `/Aliens/WebSDK/{Version}/Module/{ModuleName}/sql/index.sql`
   - Guidance:
     - Call it ONLY when schema/table/column changes are required.

3) **Table Naming Collision Gate**
   - New table names must be validated to avoid semantic duplicates/aliases.
   - Collision risk => fail fast and require explicit naming decision.

---

## Validation Rules (Minimum)
For each target item:
1) `ModuleName` empty na ho
2) `ApiName` empty na ho
3) Only allowed chars policy:
   - `A-Z a-z 0-9 _` recommended
   - spaces not allowed

### Singular Entity Name Rule (SSOT) (Non-negotiable)
- ModuleName MUST be singular (never plural).
- Any table names referenced/derived as part of the update MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

---

## Success Criteria
This workflow is considered successful when:
- Every target item produced a RunItem record
- For existing targets: child workflows called in correct order
- For missing targets: `Failed(TargetMissing)` recorded without any creation
- RunSummary generated with:
  - succeeded / failed / partial counts
  - updated file list
  - approvals pending list (if any)
