---
WorkflowId: WebOS_Engine_SQL
WorkflowName: WebOS Engine SQL (Orchestrator)
Type: Workflow_Plural
Domain: WebOS
Category: Engines
Language: SQL
Scope: "Engine -> SQL"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: true
Inputs:
  - Targets: "Comma-separated engines. Examples: WebOS.Cache, WebOS.Auth"
  - Description: "Human language requirement (required)"
Outputs:
  - Planning: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md"
  - Code: "/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql"
  - Documentation: "/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
ChildWorkflows:
  - WebOS_Engine_SQL-Planning
  - WebOS_Engine_SQL-Code
  - WebOS_Engine_SQL-Documentation
...

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [00] Purpose
Is workflow ka kaam: ek ya multiple WebOS SQL engines ke liye **Planning + Code + Documentation** generate/update karwana,
strictly WebOS ecosystem rules ke hisaab se.

Ye **Workflow_Plural** hai:
- Iske andar chhote workflows loop me run honge (Planning, Code, Documentation).

## [01] Invocation
Signature:
- `Workflow("WebOS_Engine_SQL", "WebOS.Cache, WebOS.Auth", "Aese kr do wese kr do")`

Notes:
- Targets me comma ka matlab: same workflow ko multiple times run karna (har item alag run-item).
- Description mandatory hai; empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

## [02] Target Normalization (Engines)
Accepted Target formats:
- `WebOS.Cache`
- `Cache`  → normalize to `WebOS.Cache`

Rules:
- EngineBaseName = `WebOS.<EngineShortName>`
- EngineShortName must be non-empty and must match allowed naming conventions from `ACC.Targets`.

If any target is invalid:
- **Stop** and return a clear error (no partial writes).

## [03] Version Resolution (WebOS)
Default:
- Use **latest** installed WebOS version under `/Aliens/WebOS/` (example: `PHP.26.00.00`)

Fallback:
- If auto-detect is not possible, default to `PHP.26.00.00` (as per project rule).

If `/Aliens/WebOS/{Version}` is missing:
- Error (do not create WebOS installation folders in this workflow).

## [04] Execution Flow (Deterministic)
For each Target (in given order):

1) Call Planning:
- `Workflow("WebOS_Engine_SQL-Planning", "<Target>", "<Description>")`

2) Call Code:
- `Workflow("WebOS_Engine_SQL-Code", "<Target>", "<Description>")`

3) Call Documentation:
- `Workflow("WebOS_Engine_SQL-Documentation", "<Target>", "<Description>")`

Hard rules:
- No re-ordering.
- No parallel execution unless Workflow_Universal explicitly allows it (default: sequential).

## [05] Manifest & Mode
This workflow MUST respect developer’s `.Alien` manifest (location and fields defined in `ACC.Manifest`).

Typical controls:
- AssistantMode vs EmployeeMode
- Auto vs Manual approvals
- Allowed write scopes (WebOS/WebApps/WebBrand)
- Allowed operations (Create/Update, DB touch, prod edits)

If manifest is missing:
- **Error** (do not guess defaults).

## [06] Output Paths (Single Source of Truth)
Code output:
- `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`

Plan output:
- `/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/{EngineBaseName}.plan.md`

Docs output:
- `/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md`

No other paths are allowed in this workflow.

## [07] Safety / No-Scope-Creep
Follow Workflow_Universal:
- Jitna bola jaye, utna hi.
- Apni taraf se extra features, extra files, extra changes forbidden.

Also:
- Never delete any file.
- Never modify any other engine file besides the resolved target SQL file.

## [08] Run Records (Recommended)
If Workflow_Universal defines Run logging:
- Create RunId
- Create per-target RunItemId
- Record:
  - input Targets/Description
  - resolved Version
  - resolved output paths
  - success/error summary

## [09] Done Criteria
- For each target:
  - Planning saved
  - SQL file created/updated
  - Documentation saved
- All writes are within allowed paths and within manifest permissions
- No side effects beyond declared outputs

---

## [10] CSV Task Mode (Single Row per Run)

CSV mode mirrors the standard WebOS CSV rules for create/update (Planning → Code → Documentation).

### [10.1] Invocation
Allowed:
- `Workflow('WebOS_Engine_SQL','WebOS.Cache')`
- `Workflow('WebOS_Engine_SQL','{FILE_NAME}.csv')`

### [10.2] CSV Schema (Create/Update; no Audit stage)
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

### [10.3] Engine Name Normalization (CSV)
From `name` (or legacy `page_name`):
- Trim
- Strip extension `.sql` / `.php` / `.js` if present
- Prefix `WebOS.` if missing
- Validate EngineShortName via ACC.Targets

### [10.4] Processing Rules (Single Row per Run)
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
