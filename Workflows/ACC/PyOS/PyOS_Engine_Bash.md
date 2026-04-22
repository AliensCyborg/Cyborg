---
# [ACC.WORKFLOW] PyOS Engine Bash (Orchestrator)
WorkflowId: PyOS_Engine_Bash
WorkflowName: PyOS Engine Bash
WorkflowType: Workflow_Plural
Domain: PyOS
Category: Engines
Language: Bash
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: true

# [INPUT CONTRACT]
Args:
  - Name: Targets
    Type: string
    Required: true
    Example: "PyOS.Cache, PyOS.Auth"
  - Name: Description
    Type: string
    Required: false
    Example: "Safe-by-default, idempotent, enterprise-grade; keep backward compatibility"

# [OUTPUT CONTRACT]
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/"
    Pattern: "{EngineName}.plan.md"
  Code:
    Path: "/Aliens/PyOS/{LatestVersion}/Unit/bash/"
    Pattern: "{EngineName}.sh"
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engines/Bash/"
    Pattern: "{EngineName}.md"

# [RUNTIME]
ExecutionModel: "Loop per target, deterministic order"
StopOnError: true
ApprovalGate:
  ManualMode: "If manifest says Manual -> require approval before Code write"
  AutoMode: "If manifest says Auto -> proceed, but validation + logs mandatory"
---

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Refs (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"

# [03] Purpose
Plural orchestrator workflow: given one-or-more PyOS engine names (without extension),
generate (1) Planning, (2) Code, and (3) Documentation for Bash engines by delegating
to child workflows. This workflow itself does NOT write code directly; it only coordinates
child workflows in a safe, deterministic order.

Invocation:
- Workflow("PyOS_Engine_Bash", "PyOS.Cache, PyOS.Auth", "Aese kr do wese kr do")

Notes:
- Legacy misspelling support: runner normalizes older prompts to canonical "Documentation".
- Target names never include extension; workflow will decide ".sh" output.

# [04] What this workflow DOES
This workflow:
- Validates manifest + permissions for this run (before any writes)
- Parses Targets into a deterministic list
- For each EngineName:
  1) calls Planning child workflow
  2) calls Code child workflow
  3) calls Documentation child workflow
- Stops/fails fast on ambiguity or invalid inputs (no guessing)

# [05] What this workflow does NOT do
- Directly write `.sh` code (child workflow does)
- Create extra engines not requested in Targets
- Modify unrelated files (no extra work)
- Delete files/folders (never)

# [06] Target Parsing Rules
Input Targets string:
- Example: "PyOS.Cache, PyOS.Auth"

Rules:
- Comma-separated items ko split karo.
- Har item trim karo.
- Empty item mile -> error (targets invalid).
- Case preserve (strict naming).
- Extension include mat karo (".sh" forbidden in targets).
- Duplicate targets:
  - Default behavior: allow duplicates and execute per occurrence (deterministic).
  - (Future) Agar manifest ya safety rules me "DedupeTargets=true" ho, tab dedupe.

# [07] Deterministic Execution Steps (Per EngineName)
For each EngineName in Targets list:

## Step A: Planning
Call:
- Workflow("PyOS_Engine_Bash-Planning", "{EngineName}", "{Description}")

Expected Planning Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/{EngineName}.plan.md`

## Step B: Code
Call:
- Workflow("PyOS_Engine_Bash-Code", "{EngineName}", "{Description}")

Expected Code Output:
- `/Aliens/PyOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

## Step C: Documentation
Call:
- Workflow("PyOS_Engine_Bash-Documentation", "{EngineName}", "{Description}")

Expected Documentation Output:
- `/Aliens/Docs/PyOS/Engines/Bash/{EngineName}.md`

# [08] Permissions & Safety Gate (Manifest Driven)
Before any child calls:
1) Read `/Aliens/manifest.json`
2) Validate:
   - `Permissions.CanEditProdCode` must be true (writing PyOS engine files)
   - `Permissions.CanDeleteFiles` must be false
3) Approval:
   - If `Approval=manual` and this workflow is approval-gated in policy -> stop and request approval
   - If `Approval=auto` -> proceed
4) If permission missing -> fail fast with standard error model

# [09] Run Logging / Audit (Required)
For each run write a log file:
- `/Aliens/.Alien/{Developer_Username}/Task/Logs/ACC/PyOS_Engine_Bash/{RunId}.log.md`

Minimum fields:
- timestamp, workflowId, targets, mode (manual/auto), approvals (if any),
  outputs (paths), changedFiles list, outcome (success/error)

# [10] Acceptance Criteria
A run is successful if, for every EngineName:
- Planning file exists at expected path and matches naming
- `.sh` engine file exists at expected path
- Documentation file exists at expected path
- No extra files were created beyond defined outputs
- No deletes were performed
- Errors are returned in standard error model format

---

# [11] CSV Task Mode (Single Row per Run)

CSV mode mirrors the standard PyOS CSV rules (Planning → Code → Documentation).

## [11.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_Bash','PyOS.Cache')`
- `Workflow('PyOS_Engine_Bash','{FILE_NAME}.csv')`

## [11.2] CSV Schema (Create/Update; no Audit stage)
Required columns:
- `id`
- `name`
- `status`

Legacy aliases (supported for backward compatibility):
- `page_name` == `name`

Per-stage columns (required):
- `planning`
- `planning_save`
- `code`
- `code_save`
- `doc`
- `doc_save`

Legacy aliases (supported for backward compatibility):
- `code_generate` == `code`
- `docs_generate` == `doc`
- `docs_dsave` == `doc_save`

Required prompt column:
- `description` *(must be non-empty; this is the row prompt/context)*

Optional columns:
- `url`

Progress/metrics columns (must be maintained when present in CSV):
- `code_lines`  (integer; last process me AI ne jitni code lines add/update ki)
- `modified`    (timestamp; primary code file modified time; ISO-8601 recommended)
- `created`     (timestamp; primary code file created time; ISO-8601 recommended)

Status values:
- `0` pending, `1` success, `2` failed

## [11.3] Engine Name Normalization (CSV)
From `name` (or legacy `page_name`):
- Trim
- Strip extension `.sh` / `.sql` / `.py` / `.js` if present
- Prefix `PyOS.` if missing
- Validate EngineShortName via ACC.Targets

## [11.4] Processing Rules (Single Row per Run)
Workflow MUST:
1) Resolve CSV path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
2) Find the FIRST row where `status == 0` (id ascending; fallback file order).
3) Process EXACTLY ONE row per workflow run.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

If the workflow processes more than one pending row in a single run => fail with `WF_CSV_BATCH_FORBIDDEN`.

For the picked row: Planning → Code → Docs, with durable stage writeback after each stage.
