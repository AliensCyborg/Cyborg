---
WorkflowId: PyOS_Engine_SQL_Update
WorkflowName: PyOS Engine SQL Update (Orchestrator)
Type: Workflow_Plural-Update
Domain: PyOS
Category: Engines
Language: SQL
Scope: "Engine -> SQL (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
EntryPoint: true
Purpose: >
  Plural update orchestrator: given one-or-more existing PyOS SQL engine names (without extension),
  perform an enterprise-grade update using 4 deterministic steps:
  (1) Audit, (2) Planning, (3) Code, (4) Documentation.
  Hard constraint: target SQL engine file MUST already exist (no create).
Invocation:
  - Workflow("PyOS_Engine_SQL_Update", "PyOS.Cache", "Index hardening + safe idempotency")
  - Workflow("PyOS_Engine_SQL_Update", "PyOS.Cache, PyOS.Auth", "Upgrade SQL engines safely")
  - Workflow("PyOS_Engine_SQL_Update", "{FILE_NAME}.csv")
Inputs:
  - Targets: "Comma-separated engines. Examples: PyOS.Cache, PyOS.Auth"
  - Description: "Human language update requirement"
Outputs:
  Audit:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    Pattern: "Audit.md"
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/"
    Pattern: "{EngineBaseName}.update.plan.md"
  Code:
    Path: "/Aliens/PyOS/{Version}/unit/sql/"
    Pattern: "{EngineBaseName}.sql"
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engine/SQL/"
    Pattern: "{EngineBaseName}.md"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural-Update.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
Calls:
  - PyOS_Engine_SQL_Update-Audit
  - PyOS_Engine_SQL_Update-Planning
  - PyOS_Engine_SQL_Update-Code
  - PyOS_Engine_SQL_Update-Documentation
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only: create forbidden
  ProdWrite: true
  DbTouch: "not-required"
...

# PyOS_Engine_SQL_Update

Alignment rule (hard):
- This workflow must stay aligned with the latest rules in `Workflows/ACC/PyOS/PyOS_Engine_SQL.md`.

Update-only delta (hard):
- Target SQL engine file MUST exist before any update.
- If missing: fail with `WF_TARGET_MISSING` (do not create).

Update has 4 major steps (hard):
1) Audit
2) Planning
3) Code
4) Documentation

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural-Update.md`
3) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [01] Manifest Gate (Mandatory)
1) Read manifest:
  - `/Aliens/manifest.json`
2) Approvals follow manifest (`ApprovalMode=manual` => approval gates apply).
3) Permissions (minimum):
   - Planning allowed
   - Code update requires `CanEditProdCode=true`
4) Missing/invalid => fail fast.

---

## [02] Target Normalization (Same as PyOS_Engine_SQL)
Accepted Targets:
- `PyOS.Cache`
- `Cache` → normalize to `PyOS.Cache`

Rules:
- EngineShortName must be valid (ACC.Targets)
- EngineBaseName = `PyOS.<EngineShortName>`
- Extensions forbidden (".sql", ".py", ".js")

---

## [03] Version Resolution (PyOS)
Default:
- Use **latest** installed PyOS version under `/Aliens/PyOS/` (example: `Py.26.00.00`)
Fallback:
- If auto-detect is not possible, default `Py.26.00.00`

---

## [04] Update-Only Existence Gate (Hard)
For EACH EngineBaseName:
1) Resolve `{Version}` using version resolution rule.
2) Resolve file path:
   - `/Aliens/PyOS/{Version}/unit/sql/{EngineBaseName}.sql`
3) If file does NOT exist:
   - Fail this engine with `WF_TARGET_MISSING`
   - Do NOT run Audit/Planning/Code/Docs
   - Do NOT create any SQL file

---

## [05] Execution Plan (Deterministic Order)
For EACH EngineBaseName in targets list:

### Step 1: Audit (Hard)
Call:
- Workflow("PyOS_Engine_SQL_Update-Audit", "{EngineBaseName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

### Step 2: Planning (Update Plan)
Call:
- Workflow("PyOS_Engine_SQL_Update-Planning", "{EngineBaseName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.update.plan.md`

### Step 3: Code (Update)
Call:
- Workflow("PyOS_Engine_SQL_Update-Code", "{EngineBaseName}", "{Description}")

Hard Rules:
- Only update the single target SQL file.
- No destructive statements unless explicitly requested + approved.
- Preserve existing important behavior; keep idempotency.

### Step 4: Documentation
Call:
- Workflow("PyOS_Engine_SQL_Update-Documentation", "{EngineBaseName}", "{Description}")

Expected Output:
- `/Aliens/Docs/PyOS/Engine/SQL/{EngineBaseName}.md`

---

## [06] Post-Run Verification
For EACH EngineBaseName:
- Audit report exists.
- Planning file exists.
- SQL file exists at resolved `{Version}` path.
- Documentation file exists.

If any output missing => fail with `WF_OUTPUT_MISSING` and list exact missing paths.

---

## [07] CSV Task Mode (Single Row per Run)

CSV mode mirrors the standard PyOS CSV rules, with one extra gate: target SQL file MUST exist.

### [07.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_SQL_Update','PyOS.Cache')`
- `Workflow('PyOS_Engine_SQL_Update','{FILE_NAME}.csv')`

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
- Strip extension `.sql` / `.py` / `.js` if present
- Prefix `PyOS.` if missing
- Validate EngineShortName via ACC.Targets

### [07.4] Processing Rules (Single Row per Run)
Workflow MUST:
1) Resolve CSV path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
2) Find the FIRST row where `status == 0` (id ascending; fallback file order).
3) Process EXACTLY ONE row per workflow run.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

If the workflow processes more than one pending row in a single run => fail with `WF_CSV_BATCH_FORBIDDEN`.

For the picked row: Audit → Planning → Code → Docs, with durable stage writeback after each stage.

Before calling code workflow:
- Validate SQL file exists at resolved `{Version}` path.
- If missing => mark `status=2` + `code=2` and skip remaining.
