---
WorkflowId: PyOS_Engine_JS_Update
Type: Workflow_Plural-Update
Domain: PyOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
EntryPoint: true
Purpose: >
  Plural update orchestrator: given one-or-more existing PyOS JS engine names (without extension),
  perform an enterprise-grade update using 4 deterministic steps:
  (1) Audit, (2) Planning, (3) Code, (4) Documentation.
  Hard constraint: target engine MUST already exist (no create).
Invocation:
  - Workflow("PyOS_Engine_JS_Update", "PyOS.Cache", "Security hardening + backward compatibility")
  - Workflow("PyOS_Engine_JS_Update", "PyOS.Cache, PyOS.Auth", "Upgrade JS engines safely")
  - Workflow("PyOS_Engine_JS_Update", "{FILE_NAME}.csv")
TargetsSyntax:
  - Comma-separated list of engine names (without extension)
  - Example: "PyOS.Cache, PyOS.Auth"
Outputs:
  Audit:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    Pattern: "Audit.md"
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/JS/"
    Pattern: "{EngineName}.update.plan.md"
  Code:
    Path: "/Aliens/PyOS/{LatestVersion}/unit/js/"
    Pattern: "{EngineName}.js"
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engine/JS/"
    Pattern: "{EngineName}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural-Update.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - PyOS_Engine_JS_Update-Audit
  - PyOS_Engine_JS_Update-Planning
  - PyOS_Engine_JS_Update-Code
  - PyOS_Engine_JS_Update-Documentation
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only: create forbidden
  ProdWrite: true
  DbTouch: "not-required"
...

# PyOS_Engine_JS_Update

Alignment rule (hard):
- This workflow must stay aligned with the latest rules in `Workflows/ACC/PyOS/PyOS_Engine_JS.md`.

Update-only delta (hard):
- Target engine file MUST exist before any update.
- If missing: fail with `WF_TARGET_MISSING` (do not create).

Update has 4 major steps (hard):
1) Audit
2) Planning
3) Code
4) Documentation

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [00.1] Update-Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural-Update.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Preconditions (Manifest + Permissions)
1) `/Aliens/manifest.json` read + validate (mandatory).
2) Approvals follow manifest (`ApprovalMode=manual` => approval gates apply).
3) Permission minimum:
   - Planning allowed
   - Code update requires `CanEditProdCode=true`
4) Missing permission => fail fast.

# [03] Target Parsing Rules (Same as PyOS_Engine_JS)
Input Targets string:
- Comma-separated engine names (without extension)
- Example: `PyOS.Cache, PyOS.Auth`

Normalization:
- If item does not start with `PyOS.` => prefix `PyOS.`
- `.js` forbidden in normal mode targets
- Empty items => error

# [04] Update-Only Existence Gate (Hard)
For EACH EngineName:
1) Resolve latest PyOS version folder:
   - Scan: `/Aliens/PyOS/Py.*/` pick latest
   - Fallback: `/Aliens/PyOS/Py.26.00.00/`
2) Resolve file path:
   - `/Aliens/PyOS/{LatestVersion}/unit/js/{EngineName}.js`
3) If file does NOT exist:
   - Fail this engine with `WF_TARGET_MISSING`
   - Do NOT run Audit/Planning/Code/Docs
   - Do NOT create any engine file

# [05] Execution Plan (Deterministic Order)
For EACH EngineName in targets list:

### Step 1: Audit (Hard)
Call:
- Workflow("PyOS_Engine_JS_Update-Audit", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

### Step 2: Planning (Update Plan)
Call:
- Workflow("PyOS_Engine_JS_Update-Planning", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/JS/{EngineName}.update.plan.md`

### Step 3: Code (Update)
Call:
- Workflow("PyOS_Engine_JS_Update-Code", "{EngineName}", "{Description}")

Hard Rules:
- Only update the single target engine file.
- No feature loss; preserve existing public behavior unless explicitly requested.
- Fix audit-detected gaps as part of the update.
- All enterprise quality gates from `PyOS_Engine_JS` apply.

### Step 4: Documentation
Call:
- Workflow("PyOS_Engine_JS_Update-Documentation", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/Docs/PyOS/Engine/JS/{EngineName}.md`

# [06] Post-Run Verification
For EACH EngineName:
- Audit report exists.
- Planning file exists.
- Code file exists at resolved `{LatestVersion}` path.
- Documentation file exists.

If any output missing => fail with `WF_OUTPUT_MISSING` and list exact missing paths.

# [07] CSV Task Mode (Single-Row Per Run)

CSV mode mirrors `PyOS_Engine_JS` CSV rules, with one extra gate: target MUST exist.

## [07.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_JS_Update','PyOS.Cache')`
- `Workflow('PyOS_Engine_JS_Update','{FILE_NAME}.csv')`

## [07.2] CSV Schema (Update; includes Audit)
Required columns:
- `id`
- `name`
- `status`

Legacy aliases (supported for backward compatibility):
- `page_name` == `name`

Per-stage columns (required):
- `audit`
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

## [07.3] Engine Name Normalization (CSV)
From `name` (or legacy `page_name`):
- Trim
- Strip extension `.js` / `.py` if present
- Prefix `PyOS.` if missing
- Apply the same EngineShortName validation as normal mode

## [07.4] Processing Rules (No Batch / No Loop)
Workflow MUST:
1) Resolve CSV path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
2) Sort rows by `id` ascending (fallback: file order).
3) Pick EXACTLY ONE row per run where `status == 0`.

Hard rule (non-negotiable):
- CSV batching is forbidden for this workflow.
- Even if multiple rows are pending, process ONLY the first pending row and STOP.
- To process the next pending row, user must run the workflow again.

If runner/agent attempts to process more than 1 row in one run:
- fail with `WF_CSV_BATCH_FORBIDDEN`.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

Per picked row: Audit → Planning → Code → Docs, with durable stage writeback after each stage.

Stage status rule:
- `0` pending, `1` done, `2` failed.
- `status==0` rows only picked; `status==1` skip; `status==2` skip until reset.

Before calling code workflow:
- Validate engine file exists at resolved `{LatestVersion}` path.
- If missing => mark `status=2` + `code=2` and skip remaining.

Runner compatibility requirement:
- CSV execution MUST run with `MaxItems=1` (default) for this workflow.
