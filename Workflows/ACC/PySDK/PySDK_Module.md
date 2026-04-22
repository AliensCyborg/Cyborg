---
WorkflowId: PySDK_Module
Type: Workflow_Plural
Domain: PySDK
TargetKind: "Module"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that produces a complete PySDK Module by orchestrating multiple child workflows.
  Current phase (v26 initial): Class + API only.
  Future phase: SQL, Cron, Webhook, Component, Screen, JS.
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
CallsNow:
  - "Workflows/ACC/PySDK/PySDK_Module_Class.md"
  - "Workflows/ACC/PySDK/PySDK_Module_API.md"
CallsLater:
  - "Workflows/ACC/PySDK/PySDK_Module_SQL.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Cron.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Webhook.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Component.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Screen.md"
  - "Workflows/ACC/PySDK/PySDK_Module_JS.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated list. Each item must be a ModuleName (no slash)."
  - Name: Description
    Type: string
    Required: false
    Notes: "Any human language. Overall module requirements and constraints."
Defaults:
  PySDKVersion: "latest"
  VersionFallback: "Py.26.00.00"
  Theme: "_Basic"
  NoDelete: true
  ApprovalMode: "from /Aliens/manifest.json"
Execution:
  Mode: "orchestrator"
  PerTargetOrderNow:
    - "PySDK_Module_Class"
    - "PySDK_Module_API"
Idempotency:
  IfModuleExists: "Proceed (child workflows decide create/modify), but record ExistingModule=true"
  IfModuleMissing: "Create module root folder (non-destructive) and proceed"
FailureStrategy:
  FailFast: false
  OnTargetInvalid: "skip that target and record Failed(TargetInvalid)"
  OnChildFail: "continue next child where safe; record Partial for that module"
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/PySDK/"
---

# PySDK_Module.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`PySDK_Module` ek **Plural** workflow hai jo ek PySDK **Module** (example: `Teacher`) ko complete banata hai — by calling multiple child workflows.

Example:
- `Workflow("PySDK_Module", "Teacher", "Teacher module complete banao")`

Agar multiple modules:
- `Workflow("PySDK_Module", "Teacher,User,Account", "In modules ko setup karo")`
  - 3 targets => workflow 3 bar run hoga (per module).

---

## Target Grammar (Mandatory)
Each target item must be:

- `ModuleName` (NO slash)

Valid:
- `Teacher`
- `User`
- `Account`
- `Web3`

Invalid:
- `Teacher/TeacherNew`  (yeh API/Cron/Webhook target format hai, module ka nahi)

---

## What happens NOW (current phase)
Abhi ke time, per module target, yeh workflow sirf 2 child workflows run karta hai:

1) `Workflow("PySDK_Module_Class", "<ModuleName>", "<Description>")`
2) `Workflow("PySDK_Module_API", "<ModuleName>", "<Description>")`

In dono ke complete hote hi module usable state me aa jana chahiye (class + APIs ready).

### Important (Module-only contract)
- `PySDK_Module` MUST call `PySDK_Module_API` with **module-only target** (NO slash).
  - This intentionally activates `PySDK_Module_API` module-only mode (batch create) which expands to minimum CRUD endpoints.
  - The CRUD minimum is governed by `PySDK_Module_API-*` workflows (not duplicated here).
- `PySDK_Module` MUST call `PySDK_Module_Class` with **module-only target** (NO slash).
  - Class workflow is responsible for creating/updating `/py/index.py`.

---

## What will happen LATER (future phase)
Future me per module target, yeh additional workflows bhi run karega (order stable rakho):

3) `PySDK_Module_SQL`
4) `PySDK_Module_Cron`
5) `PySDK_Module_Webhook`
6) `PySDK_Module_Component`
7) `PySDK_Module_Screen`
8) `PySDK_Module_JS`

Note:
- Abhi yeh calls **disabled** hain. Is file me sirf roadmap ke liye list hai.

---

## Orchestration Contract (per module target)

### Step 1 — Load Developer Manifest
Read:
- `/Aliens/manifest.json`

Resolve:
- Mode: `employee | assistant`
- Approval: `auto | manual`
- Permissions: prod modify allowed, db touch allowed, etc.

### Step 2 — Resolve PySDK version
- Use latest available PySDK version automatically
- If auto decide possible nahi:
  - fallback: `Py.26.00.00`

### Step 3 — Resolve module root path
For `{ModuleName}`:
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{ModuleName}/`

Idempotency rule:
- If `{ModuleRoot}` missing:
  - create folder (non-destructive)
- If `{ModuleRoot}` exists:
  - proceed, but record `ExistingModule=true`

Also ensure minimum folder structure (non-destructive, create if missing):
- `{ModuleRoot}/py/`
- `{ModuleRoot}/api/`
- `{ModuleRoot}/sql/` (if missing, do NOT create schema; folder is allowed)

Notes:
- Do NOT delete/rename any existing file or folder.
- Do NOT create future-phase folders (cron/webhook/component/screen/js) in current phase.

### Step 4 — Call child workflows (current phase order)
Pass payload:
- `Targets`: single module name (NOT full list)
- `Description`: inherited
- `ParentWorkflowId`: `PySDK_Module`
- `RunContext`: manifest-derived context
- `RunItemId`: stable id for this module

Calls:
1) `PySDK_Module_Class`
2) `PySDK_Module_API`

### Step 5 — Run Summary
Record per module:
- Child status
- Files created/modified summary (as reported by children)
- Any approvals pending (if manual approval mode)

---

## Validation Rules (Minimum)
For each module target:
1) ModuleName empty na ho
2) Recommended allowed chars:
   - `A-Z a-z 0-9 _`
3) ModuleName must NOT contain:
   - `/` (slash), spaces, dot extension

### Singular Entity Name Rule (SSOT) (Non-Negotiable)
- Module name MUST be singular (never plural).
- This rule is inherited by all child workflows: module class name and SQL table names must also remain singular.
- If the name is detected as plural, fail this target with: `ACC_ERR_NAME_MUST_BE_SINGULAR`.

If module target is valid but contains leading/trailing spaces:
- Trim it before processing.

If invalid:
- Record `Failed(TargetInvalid)`
- Skip child calls for that target.

---

## Non-Negotiable Safety (inheritance reminder)
This workflow MUST follow the universal rule:
- **Jitna bola jaye / jitna workflow me defined ho, utna hi karna hai.**
- Extra files, extra changes, extra scope — allowed nahi.

(Universal rules must come from `Workflows/ACC/_Common/Workflow_Universal.md`.)

---

## Success Criteria
For each module target:
- Both child workflows called in correct order (current phase)
- Run records written for the target
- If something fails:
  - workflow still produces an honest run summary with Partial/Failed

---

## What this workflow MUST NOT do
- Is file me unique rules of Class/API workflow repeat nahi karne
- Is file me direct code generation nahi
- Is file me API/Class/sql implement nahi
- Sirf orchestration + validation + stable call-order
