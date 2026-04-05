---
WorkflowId: BugFix_FromLogs
WorkflowName: Ultra-Safe Bug Fix (From Logs)
Type: Workflow_Plural
Domain: Common
Category: BugFix
Intent: "Given only error logs in Description, triage + produce Audit/Plan + apply ONLY mechanical safe patches (no business-logic change), then write docs"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Example: "Auto" 
  - Name: Description
    Type: string
    Required: true
    Example: "<paste full logs here>"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "BugFix_FromLogs-Audit"
  - "BugFix_FromLogs-Planning"
  - "BugFix_FromLogs-Code"
  - "BugFix_FromLogs-Documentation"
DefaultOrder: ["Audit", "Planning", "Code", "Documentation"]
PermissionsRequested:
  NoDelete: true
  ProdWrite: true
  DbTouch: false
  Network: false
Approval:
  Default: "inherit developer manifest (auto/manual)"
  Notes:
    - "This workflow is EXTRA-SENSITIVE: it must NOT change business logic."
    - "Code changes are allowed ONLY when they are mechanical and lint-validated, otherwise it must stop with a plan."
Outputs:
  Planning:
    Location: "/Aliens/.Alien/{Developer_Username}/Planning/BugFix_FromLogs/{RunId}/BugFix.Plan.md"
  Code:
    SaveStrategy: "modify-only (mechanical safe patches only)"
  Documentation:
    Location: "/Aliens/Docs/BugFix_FromLogs/{RunId}.md"
    SaveStrategy: "create-or-update"
Safety:
  NoDelete: true
  ModifyOnly: true
  MissingTargetIsError: false
---

# BugFix_FromLogs.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [01] Purpose
Is workflow ka goal: aap **sirf logs paste** kar do (Description me), aur system:
- Audit kare (logs + extracted file paths + lint status)
- Planning kare (safe deterministic checklist)
- Code me **sirf mechanical safe fixes** apply kare (agar possible ho)
- Docs generate kare (kya issue tha, kya fix hua, kya verify hua)

Yeh workflow intentionally **ultra-sensitive** hai:
- Feature loss / business logic change **forbidden**
- Risky refactors forbidden
- Agar logs ambiguous hon ya fix mechanical na ho, workflow **plan de kar stop** karega.

---

## [02] Input Contract
- Targets:
  - Recommended: `Auto`
  - (Future extensibility) you may pass a hint like `Auto:WebSDK` but runner must still rely on logs.
- Description:
  - Mandatory
  - Paste full logs (stack trace + error line + environment snippet)

Blank Description => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Log Parsing Rules (SSOT)
Workflow MUST attempt to extract at least one file path from Description using these patterns:
- Python: `File \"...\", line N`
- PHP: `in ...\.php on line N` (Windows/Linux paths)
- JS/Node: `(path:line:col)`

Only accept paths that can be resolved inside workspace root (`/Aliens`).
If no path extracted => `ERR_LOGS_NO_FILE_PATH_DETECTED`.

---

## [04] Safety Model (Non-negotiable)
Default mode = **No prod-code change**.
Prod-code change allowed ONLY when all are true:
1) Fix is *mechanical* (examples: remove UTF-8 BOM causing syntax error, neutralize duplicate PHP open/close tags)
2) Modify-only + no deletions
3) Before/After snapshots written
4) Lint passes after change (php -l / python compile) 
5) If lint fails => rollback

Business logic change examples (FORBIDDEN):
- Changing conditions, return values, query logic, auth logic
- Renaming/removing public methods
- Changing DB table mappings / schema assumptions

---

## [05] Child Workflows
Per run, execute:
0) `BugFix_FromLogs-Audit`
1) `BugFix_FromLogs-Planning`
2) `BugFix_FromLogs-Code`
3) `BugFix_FromLogs-Documentation`

---

## [06] Acceptance Criteria
Success tabhi:
- Audit + Plan artifacts exist
- If code change happened: lint must pass and rollback rules satisfied
- Docs created/updated
- RunRecord + Item record exist (ACC.Output)

---

## [07] Example
`Workflow("BugFix_FromLogs", "Auto", "<paste logs>")`
