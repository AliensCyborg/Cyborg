---
RefId: ACC.Errors
Type: ACC.Ref
Scope: ACC.Workflow.ErrorModel
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Purpose: >
  Standard error model for ACC workflows (Plural + Singular). Defines codes, structure,
  severity, phases, and remediation rules so AlienCyborg can produce predictable outputs
  and humans can debug fast.
NonNegotiables:
  - NoDelete: true
  - UpdateWorkflows: "modify-only (missing target => error)"
  - HumanReadable: true
  - MachineReadable: true
---

# ACC.Errors.md

## 0) One-line Summary
Is ref ka kaam: ACC ke har workflow me **same error format, same codes, same severity, same fixes** enforce karna.

- Human ke liye: clear, actionable message
- AI ke liye: machine-stable code + structured fields
- Enterprise ke liye: audit + repeatable debugging

---

## 1) Error Object (Standard Shape)
Har error MUST is structure me represent ho (logs/RunRecord/Reports me).

### 1.1 Required Fields
- `error_id` : unique id (per error instance)
- `code` : stable error code (from this ref)
- `severity` : `fatal | error | failed | warning | info`
- `phase` : execution phase (see section 2)
- `workflow_id` : current workflow id (e.g., `WebSDK_Module_API-Code`)
- `parent_workflow_id` : agar nested call hai to parent id, warna empty
- `target` : current target item (single) e.g., `User/UserNew` ya `WebOS.Cache`
- `message` : short human message (Hinglish)
- `details` : technical details (safe; no secrets)
- `hint` : 1-3 bullets me “ab kya karo”
- `action` : `retry | skip_target | stop_run | request_approval | fix_manifest | fix_target | not_supported`
- `timestamp` : ISO datetime
- `actor` : `AlienCyborg | Developer | System`
- `location` : file/path or step name (best-effort)

### 1.2 Optional Fields (Recommended)
- `files_intended` : create/update list (before action)
- `files_touched` : actual touched list (after action)
- `approval_id` : agar approval needed
- `run_id` : current run id
- `run_item_id` : per-target item id
- `retry_after_sec` : suggested cooldown for retry
- `related_refs` : e.g., `ACC.Targets`, `ACC.Paths`, `ACC.Safety`
- `stack` : internal trace (safe scrubbed)
- `caused_by` : nested error list (for cascade failures)

---

## 2) Phase Enum (Execution Stages)
`phase` field MUST in me se ek ho:

1) `BOOT` : workflow start / context init
2) `MANIFEST` : developer manifest load/validate
3) `INPUT_PARSE` : Workflow(...) args parsing
4) `TARGETS_NORMALIZE` : comma split, trim, dedupe policy
5) `TARGETS_VALIDATE` : grammar validation (slash, dot, underscore rules)
6) `PATH_RESOLVE` : ecosystem paths resolve (WebOS/WebApps/WebBrand/WebSDK)
7) `DISCOVER` : related files/tables/classes/examples discovery
8) `SAFETY` : safety checks (NoDelete, prod/db, secrets, approvals)
8.1) `PRE_EXECUTION_GATE` : hard gates before execution (assignment/dependsOn/required inputs)
9) `APPROVAL` : approval request / wait / decision
10) `PLANNING` : plan generate + save
11) `CODEGEN` : code generate + save
12) `DOCGEN` : docs generate + save
13) `IO_WRITE` : file write, folder create, permissions
14) `AUDIT` : RunRecord/log generation
15) `FINALIZE` : summary / exit state

---

## 3) Severity Meanings (Strict)
- `fatal`  : run cannot continue at all (context broken / security violation)
- `error`  : current target cannot be completed; run may continue for other targets (plural default)
- `failed` : a declared quality/validation gate failed (treated similar to error but with “quality gate” semantics)
- `warning`: non-blocking issue, output produced but needs human review
- `info`   : informational event (not an error; still structured)

---

## 4) Core Policies (Linked to Errors)
### 4.1 NoDelete
Delete attempt => always `fatal` + `ACC_ERR_NO_DELETE_POLICY`

### 4.2 Update Workflow (Modify-only)
If workflow id contains `_Update`:
- Missing target => `error` + `ACC_ERR_UPDATE_TARGET_MISSING`
- Create-new behavior is not allowed unless workflow explicitly declares allow-create (future extension)

### 4.3 Approval Mode
If `ApprovalMode=manual` and action is risky:
- create approval request file
- return `ACC_ERR_APPROVAL_REQUIRED` with `action=request_approval`

If `ApprovalMode=auto`:
- proceed but still log an `info` record: `ACC_INFO_AUTO_APPROVED`

---

## 5) Error Code Namespace
Format:
- `ACC_ERR_*`  => errors/failures
- `ACC_WARN_*` => warnings
- `ACC_INFO_*` => info events

Rules:
- Codes are stable; never rename without a migration note.
- New codes must be appended, not repurposed.
- Each code must define: severity default, phase default, and remediation.

---

## 6) Error Codes Catalog

### 6.1 BOOT / MANIFEST
#### ACC_ERR_MANIFEST_NOT_FOUND
- Default severity: `fatal`
- Default phase: `MANIFEST`
- When: `/Aliens/manifest.json` missing
- Hint:
  - `/Aliens/manifest.json` create/restore karo
  - `Paths`, `Mode`, `ApprovalMode`, `Permissions` fill karo
- Action: `fix_manifest`

#### ACC_ERR_MANIFEST_INVALID_JSON
- Default severity: `fatal`
- Default phase: `MANIFEST`
- When: manifest parse fail
- Hint:
  - JSON validate karo
  - Trailing commas remove karo
- Action: `fix_manifest`

#### ACC_ERR_MANIFEST_SCHEMA_INVALID
- Default severity: `fatal`
- Default phase: `MANIFEST`
- When: required keys missing / invalid enums
- Hint:
  - `Mode` in: employee|assistant
  - `ApprovalMode` in: auto|manual
  - `NoDelete` true confirm
- Action: `fix_manifest`

#### ACC_ERR_DEV_USERNAME_UNRESOLVED
- Default severity: `fatal`
- Default phase: `MANIFEST`
- When: Developer_Username resolve nahi hua
- Hint:
  - ENV `ALIEN_DEV` set karo, ya folder mapping fix karo
- Action: `fix_manifest`

---

### 6.2 INPUT / TARGETS
#### ACC_ERR_ARGS_INVALID
- Default severity: `fatal`
- Default phase: `INPUT_PARSE`
- When: Workflow(...) args count/types invalid
- Hint:
  - Workflow(WorkflowId, Targets, Description)
- Action: `stop_run`

#### ACC_ERR_CSV_DESCRIPTION_MISSING
- Default severity: `failed`
- Default phase: `INPUT_PARSE`
- When: CSV task mode me picked row ka prompt missing/empty ho
  - Planner style: `Description` column missing/empty
  - WebOS engine style: `description` column missing/empty
- Hint:
  - CSV row me description add karo (actionable prompt)
  - Example: "Create WebOS.Cache bash engine with logs + error handling"
- Action: `stop_run`

#### ACC_ERR_DESCRIPTION_REQUIRED
- Default severity: `fatal`
- Default phase: `INPUT_PARSE`
- When: Normal workflow invocation me `Description` missing/empty ho
  - `Workflow(WorkflowId, Targets, "")` or Description omitted
- Hint:
  - Description ko clear prompt banao (requirements, constraints, quality)
  - Example: "Premium UI + responsive + security checks"
- Action: `stop_run`

#### ACC_ERR_WORKFLOWID_UNKNOWN
- Default severity: `fatal`
- Default phase: `INPUT_PARSE`
- When: WorkflowId registry/index me nahi
- Hint:
  - `ACC.Workflows.Index.*` update/refresh
- Action: `stop_run`

#### ACC_ERR_TARGETS_EMPTY
- Default severity: `fatal`
- Default phase: `TARGETS_NORMALIZE`
- When: empty targets after trimming
- Hint:
  - atleast 1 target pass karo
- Action: `stop_run`

#### ACC_ERR_TARGET_FORMAT_INVALID
- Default severity: `error`
- Default phase: `TARGETS_VALIDATE`
- When: target grammar break (illegal chars, empty parent/child, etc.)
- Hint:
  - `ACC.Targets` ref follow karo
- Action: `fix_target`

#### ACC_ERR_NAME_MUST_BE_SINGULAR
- Default severity: `failed`
- Default phase: `TARGETS_VALIDATE`
- When: Module/Class/Table name plural detect ho (SSOT: entity names must always be singular)
- Hint:
  - Module/Class/Table ka naam singular rakho (example: `Teacher`, not `Teachers`)
  - Cluster tokens bhi singular hon (example: `User_Profile`, not `Users_Profiles`)
  - Plural sirf list method names me allowed hai (example: `Teachers()`)
- Action: `fix_target`

#### ACC_ERR_TARGET_PARENT_CHILD_AMBIGUOUS
- Default severity: `error`
- Default phase: `TARGETS_VALIDATE`
- When: `Parent/Child` but workflow cannot decide type
- Hint:
  - WorkflowId correct choose karo (e.g., WebSDK_Module_API vs WebApp_Component)
- Action: `fix_target`

#### ACC_WARN_TARGET_DUPLICATE
- Default severity: `warning`
- Default phase: `TARGETS_NORMALIZE`
- When: duplicates found (only warn if Deduplicate=true)
- Hint:
  - comma ka meaning repeat-run hai; intentional ho to ignore
- Action: `retry`

---

### 6.3 PATH RESOLUTION / DISCOVERY
#### ACC_ERR_PATHS_REF_MISSING
- Default severity: `fatal`
- Default phase: `PATH_RESOLVE`
- When: `Workflows/ACC/_Refs/ACC.Paths.md` missing
- Hint:
  - refs restore karo
- Action: `stop_run`

#### ACC_ERR_PATH_RESOLVE_FAILED
- Default severity: `fatal`
- Default phase: `PATH_RESOLVE`
- When: Aliens root or domain roots resolve nahi hue
- Hint:
  - manifest Paths correct karo
- Action: `fix_manifest`

#### ACC_ERR_TARGET_NOT_FOUND
- Default severity: `error`
- Default phase: `DISCOVER`
- When: target file/module/app/brand not found (and workflow requires existing)
- Hint:
  - create-workflow use karo, ya name correct karo
- Action: `fix_target`

#### ACC_ERR_EXAMPLES_NOT_FOUND
- Default severity: `warning`
- Default phase: `DISCOVER`
- When: Code workflow ne example references maange but file(s) missing
- Hint:
  - example paths add karo, ya workflow me optional mark karo
- Action: `skip_target`

#### ACC_ERR_TABLE_STRUCTURE_NOT_FOUND
- Default severity: `warning`
- Default phase: `DISCOVER`
- When: planning/code/docs needs related sql structure but missing
- Hint:
  - module sql/index.sql ensure karo
- Action: `skip_target`

---

### 6.4 SAFETY / APPROVAL
#### ACC_ERR_NO_DELETE_POLICY
- Default severity: `fatal`
- Default phase: `SAFETY`
- When: workflow attempted delete/rename destructive action
- Hint:
  - “modify-only” follow karo
- Action: `stop_run`

#### ACC_ERR_APPROVAL_REQUIRED
- Default severity: `failed`
- Default phase: `APPROVAL`
- When: manual approval required for prod/db/security action
- Hint:
  - approval file check karo: `.Alien/{Dev}/Approvals/{ApprovalId}.md`
- Action: `request_approval`

#### ACC_ERR_APPROVAL_DENIED
- Default severity: `failed`
- Default phase: `APPROVAL`
- When: human denied approval
- Hint:
  - change request revise karo
- Action: `stop_run`

#### ACC_ERR_PROD_WRITE_NOT_ALLOWED
- Default severity: `fatal`
- Default phase: `SAFETY`
- When: manifest Permissions me ProdWrite false, but workflow tried prod modify
- Hint:
  - permission update ya safe mode use karo
- Action: `fix_manifest`

#### ACC_ERR_DB_TOUCH_NOT_ALLOWED
- Default severity: `fatal`
- Default phase: `SAFETY`
- When: manifest Permissions me DbTouch false, but workflow tried DB changes
- Hint:
  - permission update ya dry-run planning only use karo
- Action: `fix_manifest`

#### ACC_ERR_SECRET_LEAK_RISK
- Default severity: `fatal`
- Default phase: `SAFETY`
- When: content suggests writing secrets into repo
- Hint:
  - `.Alien/{Dev}/Secrets/` use karo; repo me secrets never
- Action: `stop_run`

---

### 6.5 PLANNING / CODEGEN / DOCGEN
#### ACC_ERR_PLANNING_GENERATION_FAILED
- Default severity: `error`
- Default phase: `PLANNING`
- When: planning fail for a target
- Hint:
  - discovery refs check karo (class/table/examples)
- Action: `skip_target`

#### ACC_ERR_CODEGEN_FAILED
- Default severity: `error`
- Default phase: `CODEGEN`
- When: code output invalid/incomplete
- Hint remember:
  - required standards apply (AliensStyle/AliensGrade) if this workflow declares them
- Action: `retry`

#### ACC_FAILED_QUALITY_GATE
- Default severity: `failed`
- Default phase: `CODEGEN`
- When: declared quality gate did not pass (AI-auditable score, lint, structure, etc.)
- Hint:
  - gate report dekho (Runs/Reports)
- Action: `retry`

#### ACC_ERR_DOCGEN_FAILED
- Default severity: `warning`
- Default phase: `DOCGEN`
- When: docs fail but code exists
- Hint:
  - docs folder permissions check karo; domain path check karo
- Action: `retry`

---

### 6.6 IO / AUDIT
#### ACC_ERR_IO_WRITE_FAILED
- Default severity: `fatal`
- Default phase: `IO_WRITE`
- When: file write denied / disk error / path invalid
- Hint:
  - permissions + path resolve check karo
- Action: `stop_run`

#### ACC_ERR_RUNRECORD_WRITE_FAILED
- Default severity: `warning`
- Default phase: `AUDIT`
- When: run logs cannot be written
- Hint:
  - `.Alien/{Dev}/Runs/` permissions check
- Action: `retry`

#### ACC_INFO_AUTO_APPROVED
- Severity: `info`
- Phase: `APPROVAL`
- When: ApprovalMode=auto and risky action proceeds
- Action: `info`

---

### 6.7 INPUT / CSV
#### ACC_ERR_CSV_SCHEMA_INVALID
- Default severity: `fatal`
- Default phase: `INPUT_PARSE`
- When: CSV task file schema mismatch / required columns missing / required field blank
  - Example: `utag` column missing, ya Screen/Section/Component row me `utag` empty
  - Example: required columns order mismatch breaks deterministic parsing
- Hint:
  - CSV columns ko SSOT schema ke mutabiq align karo
  - Required columns (WebApp Planner): `ID, Name, utag, Worklflow, Parent, Parent_Type, Description, Prority, ...`
  - Ensure `Description` always populated, and Screen/Section/Component rows ke liye `utag` populated
- Action: `stop_run`

#### ACC_ERR_TASK_ASSIGNMENT_REQUIRED
- Default severity: `fatal`
- Default phase: `INPUT_PARSE`
- When: planner CSV me `Assign` column missing ho, ya task row (`Status=0`) me `Assign` blank ho.
- Hint:
  - Planner schema update karo: `Assign` column add karo
  - Har actionable row (Status=0) me `Assign` set karo (example: `AnilNayak`)
- Action: `stop_run`

#### ACC_ERR_TASK_NOT_ASSIGNED
- Default severity: `fatal`
- Default phase: `PRE_EXECUTION_GATE`
- When: current developer username (from `ACC.Manifest` -> `Developer.Username`) `Assign` se match nahi karta.
- Hint:
  - Ya to task ka `Assign` sahi developer par set karo
  - Ya correct developer account/manifest ke sath workflow run karo
- Action: `stop_run`

#### ACC_ERR_TASK_DEPENDENCY_NOT_MET
- Default severity: `fatal`
- Default phase: `PRE_EXECUTION_GATE`
- When: planner CSV me task run se pehle `DependsOn` gate pass nahi hota.
  - Example: `DependsOn` me referenced row ID exist nahi karta
  - Example: dependency row ka `Status != 1` (pending/failed)
- Hint:
  - Pehle dependencies ko complete karo (Status=1)
  - `DependsOn` IDs ko verify karo (existing row IDs only)
  - Cycles remove karo (dependency graph acyclic hona chahiye)
- Action: `stop_run`

---

## 7) Standard Remediation Rules (Auto Decisions)
Plural workflow default behavior (recommended):
- `fatal`  => stop whole run immediately
- `error`  => mark target failed, continue other targets
- `failed` => mark target failed, continue other targets (unless FailFast=true)
- `warning`=> continue, but add to Summary.Warnings
- `info`   => continue

Singular workflow behavior:
- `fatal`/`error`/`failed` => return to caller with code; do not proceed further steps inside singular

---

## 8) Error Message Style Guide (Hinglish, actionable)
- Avoid blame words
- Always include “kya hua”, “kahan hua”, “ab kya karo”
- Never expose secrets/tokens/keys
- Prefer exact path names in `details`

---

## 9) Add New Error Codes (Change Control)
New error add karne ka rule:
1) `ACC_ERR_` / `ACC_WARN_` / `ACC_INFO_` prefix
2) Define: severity + phase + when + hint + action
3) Never repurpose old code
4) Update:
   - `ACC.Workflows.Index.md/json` (if needed)
   - Changelog section below

### Changelog
- 26.00.00 (2025-12-24): Initial ACC error model + codes catalog
