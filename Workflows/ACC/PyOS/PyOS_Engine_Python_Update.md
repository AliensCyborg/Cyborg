---
WorkflowId: PyOS_Engine_Python_Update
Type: Workflow_Plural-Update
Domain: PyOS
Category: Engines
Language: Python
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
EntryPoint: true
Purpose: >
  Plural update orchestrator: given one-or-more existing PyOS Python engine names (without extension),
  perform an enterprise-grade update using 4 deterministic steps:
  (1) Audit, (2) Planning, (3) Code, (4) Documentation.
  Hard constraint: target engine MUST already exist (no create).
Invocation:
  - Workflow("PyOS_Engine_Python_Update", "PyOS.Cache", "Performance + security hardening; backward compatibility maintained")
  - Workflow("PyOS_Engine_Python_Update", "PyOS.Cache, PyOS.Auth", "Upgrade engines safely")
  - Wrokflow("PyOS_Engine_Python_Update", "{FILE_NAME}.csv")
TargetsSyntax:
  - Comma-separated list of engine names (without extension)
  - Example: "PyOS.Cache, PyOS.Auth"
Outputs:
  Audit:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    Pattern: "Audit.md"
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/"
    Pattern: "{EngineName}.update.plan.md"
  Code:
    Path: "/Aliens/PyOS/Python.{Latest}/Engine/"
    Pattern: "{EngineName}.py"
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engine/py/"
    Pattern: "{EngineName}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - PyOS_Engine_Python_Update-Audit
  - PyOS_Engine_Python_Update-Planning
  - PyOS_Engine_Python_Update-Code
  - PyOS_Engine_Python_Update-Documentation
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only: create forbidden
  ProdWrite: true
  DbTouch: "not-required"
...

# PyOS_Engine_Python_Update

Alignment rule (hard):
- This workflow must stay aligned with the latest rules in `Workflows/ACC/PyOS/PyOS_Engine_Python.md`.

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
4) Missing permission => fail fast (standard ACC error model).

# [03] Target Parsing Rules (Same as PyOS_Engine_Python)
Input Targets string:
- Comma-separated engine names (without extension)
- Example: `PyOS.Cache, PyOS.Auth`

Normalization:
- If item does not start with `PyOS.` => prefix `PyOS.`
- `.py` forbidden in normal mode targets
- Empty items => error

# [04] Update-Only Existence Gate (Hard)
For EACH EngineName:
1) Resolve engine version path using ACC.Paths rules (latest; fallback `Py.26.00.00`).
2) Resolve file path:
   - `/Aliens/PyOS/{Version}/Engine/{EngineName}.py`
3) If file does NOT exist:
   - Fail this engine with `WF_TARGET_MISSING`
  - Do NOT run Audit/Planning/Code/Docs
  - Do NOT create any engine file

# [05] Execution Plan (Deterministic Order)
For EACH EngineName in targets list:

### Step 1: Audit (Hard)
Call:
- Workflow("PyOS_Engine_Python_Update-Audit", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/{EngineName}.update.audit.md`

Audit hard requirement:
- Audit MUST detect AliensStyle / CodeHeader / CodeFooter / Sectioning / Router contract gaps and list them explicitly.

### Step 2: Planning (Update Plan)
Call:
- Workflow("PyOS_Engine_Python_Update-Planning", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/{EngineName}.update.plan.md`

### Step 3: Code (Update)
Call:
- Workflow("PyOS_Engine_Python_Update-Code", "{EngineName}", "{Description}")

Hard Rules:
- Only update the single target engine file.
- No feature loss; preserve existing public behavior unless explicitly requested.
- Fix audit-detected gaps as part of the update.
- All enterprise quality gates from `PyOS_Engine_Python` apply (no skeleton outputs).

### Step 4: Documentation
Call:
- Workflow("PyOS_Engine_Python_Update-Documentation", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/Docs/PyOS/Engine/py/{EngineName}.md`

# [06] Post-Run Verification
For EACH EngineName:
- Audit report exists.
- Planning file exists.
- Code file exists at resolved `{Version}` path.
- Documentation file exists.

If any output missing => fail with `WF_OUTPUT_MISSING` and list exact missing paths.

# [07] CSV Task Mode (Batch Update)

CSV mode mirrors `PyOS_Engine_Python` CSV rules, with one extra gate: target MUST exist.

## [07.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_Python_Update','PyOS.Cache')`
- `Wrokflow('PyOS_Engine_Python_Update','{FILE_NAME}.csv')`  *(legacy typo supported)*

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
- Strip extension `.py` / `.js` if present
- Prefix `PyOS.` if missing
- Apply the same EngineShortName validation as normal mode

## [07.4] Processing Rules (Loop)
Workflow MUST:
1) Resolve CSV path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
2) Iterate rows in order (id ascending; fallback file order).
3) Pick ONLY rows where `status == 0`.

Hard guarantee (Single Row per Run):
- CSV me jitne bhi pending tasks hon (10/1000/10000), workflow ko **sirf 1 status=0 row** process karni hai per run.
- Next run me next pending row process hogi.
- Batch/loop me saari rows ek saath process karna forbidden hai.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

Enforcement:
- Agar implementation 1 se zyada rows process kare to hard fail with:
  - `WF_CSV_BATCH_FORBIDDEN`
- Speed hacks forbidden; durable progress mandatory.

Per picked row: Audit → Planning → Code → Docs, with durable stage writeback after each stage.

Stage status rule:
- `0` pending, `1` done, `2` failed.
- `status==0` rows only picked; `status==1` skip; `status==2` skip until reset.

### Stage A: Audit
- If `audit != 1` then run `PyOS_Engine_Python_Update-Audit`.
- On success => `audit=1`.
- On fail => `audit=2`, `status=2`, skip remaining.

### Stage B: Planning
- If `planning != 1` then run `PyOS_Engine_Python_Update-Planning`.
- On success => `planning=1`.
- On fail => `planning=2`, `status=2`, skip remaining.

### Stage C: Code (Update)
Before calling code workflow:
- Validate engine file exists at resolved `{Version}` path.
- If missing => set `code=2`, `status=2` and skip remaining (create forbidden).

If exists and `code != 1` then run `PyOS_Engine_Python_Update-Code`.
- On success => `code=1`.
- On fail => `code=2`, `status=2`, skip remaining.

### Stage D: Code Save / Verify
- Same verification rules as `PyOS_Engine_Python` (file exists, lint passes, header/footer/baseline present).

### Stage E/F: Docs + Docs Verify
- Same rules as `PyOS_Engine_Python`.

### Final Row Status
- If all stages are `1` => `status=1`
- Else if any stage is `2` => `status=2`
- Else keep `status=0`

## [07.5] CSV Writeback (Must)
- After each stage status change, write CSV back (durable).
- Never delete rows.
- Keep unknown columns intact.
- Also keep `code_lines`, `modified`, `created` updated for the picked row.

## [07.6] Approvals + RunRecords
- Manifest approvals apply in CSV mode too.
- RunRecords MUST be created per picked row/engine using ACC.Output rules.
