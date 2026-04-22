---
WorkflowId: PyOS_Engine_Bash_Update
Type: Workflow_Plural-Update
Domain: PyOS
Category: Engines
Language: Bash
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
EntryPoint: true
Purpose: >
  Plural update orchestrator: given one-or-more existing PyOS Bash engine names (without extension),
  perform an enterprise-grade update using 4 deterministic steps:
  (1) Audit, (2) Planning, (3) Code, (4) Documentation.
  Hard constraint: target engine MUST already exist (no create).
Invocation:
  - Workflow("PyOS_Engine_Bash_Update", "PyOS.Cache", "Idempotency + security hardening; backward compatibility maintained")
  - Workflow("PyOS_Engine_Bash_Update", "PyOS.Cache, PyOS.Auth", "Upgrade bash engines safely")
  - Workflow("PyOS_Engine_Bash_Update", "{FILE_NAME}.csv")
TargetsSyntax:
  - Comma-separated list of engine names (without extension)
  - Example: "PyOS.Cache, PyOS.Auth"
Outputs:
  Audit:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    Pattern: "Audit.md"
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/"
    Pattern: "{EngineName}.update.plan.md"
  Code:
    Path: "/Aliens/PyOS/{LatestVersion}/Unit/bash/"
    Pattern: "{EngineName}.sh"
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engines/Bash/"
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
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
Calls:
  - PyOS_Engine_Bash_Update-Audit
  - PyOS_Engine_Bash_Update-Planning
  - PyOS_Engine_Bash_Update-Code
  - PyOS_Engine_Bash_Update-Documentation
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only: create forbidden
  ProdWrite: true
  DbTouch: "not-required"
...

# PyOS_Engine_Bash_Update

Alignment rule (hard):
- This workflow must stay aligned with the latest rules in `Workflows/ACC/PyOS/PyOS_Engine_Bash.md`.

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

# [03] Target Parsing Rules (Same as PyOS_Engine_Bash)
Input Targets string:
- Comma-separated engine names (without extension)
- Example: `PyOS.Cache, PyOS.Auth`

Normalization:
- If item does not start with `PyOS.` => prefix `PyOS.`
- `.sh` forbidden in normal mode targets
- Empty items => error

# [04] Update-Only Existence Gate (Hard)
For EACH EngineName:
1) Resolve PyOS version using ACC.Paths rules (latest; fallback `Py.26.00.00`).
2) Resolve file path:
  - `/Aliens/PyOS/{LatestVersion}/Unit/bash/{EngineName}.sh`
3) If file does NOT exist:
   - Fail this engine with `WF_TARGET_MISSING`
   - Do NOT run Audit/Planning/Code/Docs
   - Do NOT create any engine file

# [05] Execution Plan (Deterministic Order)
For EACH EngineName in targets list:

### Step 1: Audit (Hard)
Call:
- Workflow("PyOS_Engine_Bash_Update-Audit", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Audit hard requirement:
- Audit MUST detect AliensStyle/Header/Footer, shell safety (quoting, no eval), exit-code model,
  idempotency/locking gaps, and docs mismatch; list concrete fixes.

### Step 2: Planning (Hard)
Call:
- Workflow("PyOS_Engine_Bash_Update-Planning", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/{EngineName}.update.plan.md`

Planning hard requirement:
- Plan MUST include audit findings + explicit update steps + compatibility notes.

### Step 3: Code (Hard)
Call:
- Workflow("PyOS_Engine_Bash_Update-Code", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/PyOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

Code hard requirement:
- Modify only the target `.sh` file.
- No feature loss; preserve existing flags/behavior unless explicitly requested.

### Step 4: Documentation
Call:
- Workflow("PyOS_Engine_Bash_Update-Documentation", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/Docs/PyOS/Engines/Bash/{EngineName}.md`

# [06] Post-Run Verification
For EACH EngineName:
- Audit exists at RunRecord path.
- Update plan exists and matches required schema.
- Code file exists at expected engine path.
- Documentation file exists at expected docs path.

If any output missing:
- Fail with `WF_OUTPUT_MISSING`
- Details me exact missing paths list karo
- Action me child workflow ka step re-run suggestion do

---

## [07] CSV Task Mode (Single Row per Run)

CSV mode mirrors the standard PyOS CSV rules, with one extra gate: target engine file MUST exist.

### [07.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_Bash_Update','PyOS.Cache')`
- `Workflow('PyOS_Engine_Bash_Update','{FILE_NAME}.csv')`

### [07.2] CSV Schema (Update; includes Audit)
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

### [07.3] Engine Name Normalization (CSV)
From `name` (or legacy `page_name`):
- Trim
- Strip extension `.sh` / `.sql` / `.py` / `.js` if present
- Prefix `PyOS.` if missing
- Validate EngineShortName via ACC.Targets

### [07.4] Processing Rules (Single Row per Run)
Workflow MUST:
1) Resolve CSV path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
2) Find the FIRST row where `status == 0` (id ascending; fallback file order).
3) Process EXACTLY ONE row per workflow run.

If the workflow processes more than one pending row in a single run => fail with `WF_CSV_BATCH_FORBIDDEN`.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

For the picked row: Audit → Planning → Code → Docs, with durable stage writeback after each stage.

Before calling code workflow:
- Validate engine file exists at resolved `{LatestVersion}` path.
- If missing => mark `status=2` + `code=2` and skip remaining.
