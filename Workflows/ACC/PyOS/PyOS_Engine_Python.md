---
WorkflowId: PyOS_Engine_Python
Type: Workflow_Plural
Domain: PyOS
Category: Engines
Language: Python
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-27
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given one-or-more PyOS engine names (without extension),
  generate (1) Planning, (2) Code, and (3) Documentation by delegating to child workflows.
  This workflow itself does NOT generate code directly; it only coordinates child workflows
  in a safe, deterministic order.
Invocation:
  - Workflow("PyOS_Engine_Python", "PyOS.Troubleshoot", "What you want in this engine (required)")
  - Workflow("PyOS_Engine_Python", "PyOS.Troubleshoot, PyOS.Cache, PyOS.Auth, PyOS.Account")
  - Workflow("PyOS_Engine_Python", "PyOS.Cache, PyOS.Auth", "Aese kr do wese kr do")
TargetsSyntax:
  - Comma-separated list of engine names (without extension)
  - Example: "PyOS.Cache, PyOS.Auth"
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/"
    Pattern: "{EngineName}.plan.md"
  Code:
    Path: "/Aliens/PyOS/{Version}/Engine/"  # Version resolves to latest Py.* (fallback Py.26.00.00)
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
Calls:
  - PyOS_Engine_Python-Planning
  - PyOS_Engine_Python-Code
  - PyOS_Engine_Python-Documentation
  - PyOS_Engine_Python_Update
Notes:
  - Legacy misspelling support: runner normalizes older prompts to canonical "Documentation".
...

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Scope Lock
- StrictScopeOnly rule apply hogi (Workflow_Universal ke mutabiq).
- Is workflow ka scope sirf orchestration hai:
  - targets parse karo
  - child workflows call karo
  - outputs verify karo
- Agent apni taraf se extra improvements / refactor / extra files create nahi karega.

# [03] Preconditions (Manifest + Permissions)
1) `/Aliens/manifest.json` read + validate (mandatory).
2) Agar `Approval=manual`:
   - Jo steps “approval-gated” hain, un par proceed se pehle approval required.
3) Permissions checks (minimum):
   - Planning: allowed (default)
   - Code generation: requires `CanEditProdCode=true`
   - Any DB touch: is workflow me default **not required**; agar child workflow explicitly require kare tabhi.
4) Agar required permission missing ho -> fail fast with standard error model.

# [04] Target Parsing Rules
Input Targets string:
- Example: "PyOS.Cache, PyOS.Auth"

Example (new engine name):
- "PyOS.Troubleshoot"

Rules:
- Comma-separated items ko split karo.
- Har item trim karo (leading/trailing spaces remove).
- Empty items ignore mat karo; agar empty item milay -> error (targets invalid).
- Case preserve karo (naming conventions strict).
- Extension kabhi include mat karo (".py" forbidden in targets).

Normalization (PyOS Engine convenience):
- Agar item `PyOS.` se start nahi hota (e.g., `Cache`) to normalize: `PyOS.{Item}`.

Notes:
- `PyOS.Troubleshoot` is a normal engine target name (not a special mode/keyword).
- `Description` mandatory hai; agar empty/missing ho to fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

Normalization:
- Multiple spaces -> single space (only around commas)
- "PyOS.Cache" valid; "PyOS Cache" invalid unless workflow explicitly supports aliasing.

# [05] Execution Plan (Deterministic Order)
For EACH EngineName in Targets list:

## [05.0] Auto-Route: Create vs Update (Hard)
Before running child steps for a target, resolve whether the engine already exists.

Rule:
- If the resolved engine file already exists => run `PyOS_Engine_Python_Update` for that target.
- If it does not exist => run the normal create flow: Planning → Code → Documentation.

Why:
- User requirement: running `PyOS_Engine_Python` should automatically switch to update-mode when the target file already exists.

Implementation Notes (spec-level):
- Resolve `{Version}` using ACC.Paths policy (latest; fallback `Py.26.00.00`).
- Existence check path:
  - `/Aliens/PyOS/{Version}/Engine/{EngineName}.py`
- When update-mode triggers:
  - Call: `Workflow('PyOS_Engine_Python_Update','{EngineName}','{Description}')`
  - Then continue to next target (do NOT re-run Planning/Code/Docs in this parent workflow).

### Step 1: Planning
Call:
- Workflow("PyOS_Engine_Python-Planning", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/py/{EngineName}.plan.md`

Approval:
- Default: no approval
- If manifest says manual and planning marked gated by org policy -> require approval

### Step 2: Code
Call:
- Workflow("PyOS_Engine_Python-Code", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/PyOS/{Version}/Engine/{EngineName}.py`

Hard Rules:
- Only create/update the single target engine file.
- Do NOT modify other engines or shared core files unless child workflow explicitly lists them as required outputs.

### Step 3: Documentation
Call:
- Workflow("PyOS_Engine_Python-Documentation", "{EngineName}", "{Description}")

Expected Output:
- `/Aliens/Docs/PyOS/Engine/py/{EngineName}.md`

Legacy Note:
- Agar kahin Documentation step ki misspelling milay, treat as alias of "-Documentation".
- Canonical naming always "-Documentation" use karo.

# [06] Post-Run Verification
For EACH EngineName:
- Planning file exists and matches required schema (if schema ref available).
- Code file exists at expected engine path.
- Documentation file exists at expected docs path.

If any output missing:
- Fail with `WF_OUTPUT_MISSING`
- Details me exact missing paths list karo
- Action me child workflow ka step re-run suggestion do

# [07] Non-Goals (Explicit)
- New engine invent karna (not requested) -> forbidden
- Extra helper libs, extra refactors -> forbidden
- Bulk reformatting of unrelated files -> forbidden
- Inventory generation -> this workflow ka scope nahi (use AlienCyborg_Inventory)

# [08] Minimal Success Criteria
- Har target engine ke liye 3 outputs generate ho jayein:
  1) Planning
  2) Code
  3) Documentation
- StrictScopeOnly violations = automatic failure (audit-grade).

# [09] Enterprise / Ultra Output Expectations (User Rules)

> Ye section user expectation ko **hard rule** bana raha hai.
> Iska matlab: output "minimal" nahi hoga. Agent ko enterprise-grade, exhaustive, large implementation generate karni hai
> taake repo me kahin bhi koi call/method missing na reh jaye.

## [09.1] Core Philosophy
- **No Missing Coverage**: engine generate/update karte waqt repo discovery se jitne `{EngineShortName}{Action}` call patterns milen,
  un sab actions ka **public static** implementation (ya safe stub + explicit TODO + safe return) present ho.
- **Enterprise-grade by default**: validation, error model, logging, safe defaults, edge-cases handling.
- **Large file allowed / required**: agar engine foundational ho (Security/Cache/API/Auth/etc) to file intentionally comprehensive banani hai.

## [09.2] Scope Clarification (Important)
- StrictScopeOnly ka matlab ye nahi ke engine ke andar features limit kar do.
- StrictScopeOnly ka matlab:
  - **unrelated files** modify/create nahi honge,
  - lekin **target engine file ke andar** enterprise-level completeness add karna allowed/expected hai.

## [09.3] Non-Negotiables (Must)
- Router contract 100% follow:
  - `public static function Main(array $Payload, array $Context)`
  - actions also: `public static function Action(array $Payload, array $Context)`
- Payload handling MUST support EngineRouter style where call args are passed as numeric `$Payload`.
- Backward compatibility:
  - Existing public behaviors/methods remove nahi karne.
  - If unsure: preserve, deprecate via docs, do not break.

## [09.4] Quality Gates (Workflow-level)
- Planning output MUST include a "Coverage" section: discovered actions list + which are implemented/stubbed.
- Code output MUST include:
  - deterministic structured result format (success/engine/error) for Main and major actions
  - defensive validation and safe failure returns (no fatal errors)
  - logging markers consistent with existing PyOS engines
- Documentation output MUST include:
  - complete Public Methods Index (all actions)
  - examples covering common + edge cases

## [09.7] NO Jugaad / NO Main-Only Engines (Hard Fail)
User requirement (hard rule): "system ko samjho" aur enterprise-grade topic-based engine banani hai.

Forbidden outputs (automatic failure):
- Engine file that only contains `Main()` and nothing else.
- "Skeleton" engines that do not implement the planned feature-set.
- Bypassing Planning step and directly writing code.

Minimum baseline for ANY newly created engine (must be present in the target engine file):
- Enterprise CodeHeader including explicit **Dependencies** list (real PyOS engines used)
- CodeFooter
- Guarded logger helper (no assumptions about `x()`/`d()`)
- Payload arg extraction helper (EngineRouter numeric payload support)
- Structured Ok/Fail result helpers (consistent envelope + error codes)

Enterprise integration rule:
- Engine must use existing PyOS ecosystem (e.g., `PyOS::Log()`, `PyOS::Security*()`, `PyOS::Policy*()`, `PyOS::Cache()` where relevant).
- Do not reinvent parallel systems inside the same engine.

## [09.5] Run Records + Manual Approval Artifacts (Must)
- Is workflow system ka audit-grade output mandatory hai.
- If `/Aliens/manifest.json` me `ApprovalMode=manual` ho:
  - code generation se related step ke liye **approval record** produce/ensure ho.
  - RunRecord folder produce/ensure ho:
    - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Run.md`
    - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Run.json`
    - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Item.md`
    - `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Item.json`
- Post-Run Verification me in artifacts ko bhi validate karo.

## [09.6] Return-Type Compatibility (Critical)
- Repo me same engine action different contexts me use hota hai (bool vs array vs string).
- Code+Docs rules MUST enforce:
  - Planning me "Return-Type Expectations" explicitly list ho.
  - Docs me "Return-Type Compatibility Notes / Matrix" include ho.
  - Where possible: break actions into clear contracts; where legacy requires: maintain backward compatible returns.


# [10] CSV Task Mode (Batch Engine Generation)

> New capability: `PyOS_Engine_Python` ab CSV task list se bhi run ho sakta hai.
> Existing single-target / comma-target rules same rahenge; yeh rule **additional** hai.

## [10.1] Invocation
Allowed:
- `Workflow('PyOS_Engine_Python','PyOS.Security')`  *(normal mode)*
- `Wrokflow('PyOS_Engine_Python','{{FILE_NAME}}.csv')` *(CSV mode; legacy typo supported)*

CSV mode me `Targets` engine list nahi hota; yeh **task file name** hota hai.

## [10.1.1] CSV Mode = Same Quality, Same Process (Hard)
CSV mode is ONLY a batch orchestrator. It must follow the exact same process and quality gates as single-engine mode:
- Per-row: Planning → Code → Documentation
- Same enterprise completeness (large files allowed)
- Same AliensStyle/AliensGrade/CleanCode requirements
- Same approvals + run records

If CSV mode generates "Main-only" engines or skips planning, mark the row failed (`status=2`).

## [10.2] Task File Resolution (Hard)
When `Targets` ends with `.csv`:
1) Resolve developer username from manifest.
2) Resolve path:
  - `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`

If file missing:
- Fail with `WF_TASKS_CSV_MISSING` and include expected path.

## [10.3] CSV Schema (Expected Columns)
Required columns:
- `id`
- `name`  (target engine name; may include extension)
- `status`     (overall)

Legacy aliases (supported for backward compatibility):
- `page_name` == `name`

Per-stage columns (required for stage tracking):
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
- `0` = pending
- `1` = success
- `2` = failed

## [10.4] Engine Name Normalization (CSV)
From `name` (or legacy `page_name`):
- Trim
- If ends with `.py` or `.js` → strip extension (task files can be file-like)
- If does not start with `PyOS.` → prefix `PyOS.`
- Then apply the same EngineShortName validation as normal mode.

## [10.5] Processing Rules (Loop)
Workflow MUST:
1) Read CSV.
2) Iterate rows in order (`id` ascending; fallback file order).
3) Pick ONLY rows where `status == 0`.

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).

Hard guarantee (Single Row per Run):
- CSV me 10, 1000, ya 10000 tasks hon, workflow ko **sirf 1 pending (status=0) row** process karni hai per run.
- Next run me next pending row process hogi.
- Batch/loop me saari rows ek saath process karna forbidden hai.

Enforcement:
- Agar runner me `max-items` / batch option aaye bhi, effective behavior **always 1** hona chahiye.
- Agar koi implementation 1 se zyada rows process kare to hard fail with:
  - `WF_CSV_BATCH_FORBIDDEN`

For each picked row (safe order):
- If target engine already exists => run `PyOS_Engine_Python_Update` and mark stages.
- Else => Planning → Code → Docs.

### CSV Auto-Update Shortcut (Hard)
Before Stage A (Planning), resolve `{EngineName}` and check if the engine file exists at:
- `/Aliens/PyOS/{Version}/Engine/{EngineName}.py`

If exists:
- Run: `Workflow('PyOS_Engine_Python_Update','{EngineName}','{RowDescription}')`
- On success: set all stage columns to `1`:
  - `planning=1, planning_save=1, code=1, code_save=1, doc=1, doc_save=1`, and set `status=1`
- On fail: set `status=2` (and at least one stage to `2`, recommended: `code=2`), then continue to next row.

If not exists:
- Proceed with Stage A → E rules below.

Stage status rule:
- `0` means pending (run it)
- `1` means already done (skip)
- `2` means failed (do not auto-pick unless user resets to 0)

Long-run policy:
- CSV may take hours/days. This is allowed.
- Progress must remain durable and auditable; do not batch-write at the end.

### Stage A: Planning
- If `planning != 1` then run:
  - `Workflow('PyOS_Engine_Python-Planning', '{EngineName}', '{RowDescription}')`
- On success: set `planning=1`
- On fail: set `planning=2`, set `status=2`, skip remaining stages for that row.

### Stage B: Code
- If `code != 1` then run:
  - `Workflow('PyOS_Engine_Python-Code', '{EngineName}', '{RowDescription}')`
- On success: set `code=1`
- On fail: set `code=2`, set `status=2`, skip remaining.

Hard rule for Stage B success:
- "Success" means the engine was generated as per planning (enterprise-grade), not a minimal skeleton.
- If code is detected as "Main-only" or missing baseline helpers/header/footer → treat as failure.

### Stage C: Code Save / Verify
- If `code_save != 1` then verify:
  - engine file exists at `/Aliens/PyOS/{Version}/Engine/{EngineName}.py`
  - `python -m py_compile` passes
- On success: set `code_save=1`
- On fail: set `code_save=2`, set `status=2`, skip remaining.

Additional verification (must):
- Engine file contains CodeHeader + CodeFooter.
- Engine file contains at least the minimum baseline helpers defined in [09.7].

### Stage D: Docs
- If `doc != 1` then run:
  - `Workflow('PyOS_Engine_Python-Documentation', '{EngineName}', '{RowDescription}')`
- On success: set `doc=1`
- On fail: set `doc=2`, set `status=2`, skip remaining.

### Stage E: Docs Save / Verify
- If `doc_save != 1` then verify docs exists:
  - `/Aliens/Docs/PyOS/Engine/py/{EngineName}.md`
- On success: set `doc_save=1`
- On fail: set `doc_save=2`, set `status=2`.

### Final Row Status
- If all stages are `1` → set `status=1`
- Else if any stage is `2` → set `status=2`
- Else keep `status=0`

## [10.6] CSV Writeback (Must)
- After each stage status change, write CSV back to the same file (durable progress).
- Never delete rows.
- Keep unknown columns intact.
- Also keep `code_lines`, `modified`, `created` updated for the picked row.

## [10.7] Approvals + RunRecords
- CSV mode me bhi manifest approvals apply hoti hain (manual approvals still required).
- RunRecords MUST be created per picked row/engine using standard RunId/RunItemId rules.

