---
WorkflowId: WebOS_Engine_JS
Type: Workflow_Plural
Domain: WebOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given one-or-more WebOS engine names (without extension),
  generate (1) Planning, (2) Code, and (3) Documentation by delegating to child workflows.
  This workflow itself does NOT generate code directly; it only coordinates child workflows
  in a safe, deterministic order.
Invocation:
  Signature: Workflow("WebOS_Engine_JS", "WebOS.Cache, WebOS.Auth", "Human language requirement")
  TargetsMeaning: >
    Targets are engine names WITHOUT extension. Example "WebOS.Cache" implies engine file "WebOS.Cache.js".
  DescriptionMeaning: >
    Human language constraints. Must NOT expand scope beyond what this workflow defines.
Outputs:
  Audit:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    Pattern: "Audit.md"
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/"
    Pattern: "{EngineName}.plan.md | {EngineName}.update.plan.md (if updating existing engine)"
  Code:
    Path: "/Aliens/WebOS/{LatestVersion}/unit/js/"
    Pattern: "{EngineName}.js"
  Documentation:
    Path: "/Aliens/Docs/WebOS/Engine/JS/"
    Pattern: "{EngineName}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
Calls:
  - WebOS_Engine_JS-Planning
  - WebOS_Engine_JS-Code
  - WebOS_Engine_JS-Reaudit
  - WebOS_Engine_JS-Documentation
  - WebOS_Engine_JS_Update-Audit
  - WebOS_Engine_JS_Update-Planning
  - WebOS_Engine_JS_Update-Code
  - WebOS_Engine_JS_Update-Reaudit
  - WebOS_Engine_JS_Update-Documentation
Notes:
  - Legacy misspelling support: runner normalizes older prompts to canonical "Documentation".
  - JS engines are versioned under "/Aliens/WebOS/{LatestVersion}/unit/js/" (fallback PHP.26.00.00).
  - Auto mode: if the target engine file already exists, this workflow MUST run the Update pipeline (Audit→UpdatePlan→UpdateCode→UpdateDocs).
...

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct code generate/modify is file me (child workflow karega)
- Extra engines invent karna / unrelated refactors
- Inventory generation (use AlienCyborg_* workflows)
- Description me jo nahi bola, woh add karna

# [03] Manifest Gate (Mandatory)
1) Read manifest:
  - "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (default)
   - Code generation: requires CanEditProdCode=true
   - DB touch: NOT part of this workflow; agar child workflow explicitly require kare tabhi consider
4) Missing/invalid permission => fail fast (standard errors)

# [04] Targets Parsing Rules
Input Targets string:
- Example: "WebOS.Cache, WebOS.Auth"

Rules:
- Comma-separated items split karo
- Har item trim karo
- Empty item milay => error (targets invalid)
- Case preserve karo (naming conventions strict)
- Extensions forbidden (".js" / ".php" / etc)

# [05] Execution Plan (Deterministic Order)
For EACH EngineName in Targets list:

### Step 0: Resolve Mode (Create vs Update)
1) Resolve latest WebOS version:
  - Scan: "/Aliens/WebOS/PHP.*/" pick latest
  - Fallback: "/Aliens/WebOS/PHP.26.00.00/"
2) Resolve engine path:
  - "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"
3) If engine file exists => Mode=UPDATE
4) If engine file missing => Mode=CREATE (subject to manifest)

Hard rule:
- Mode=UPDATE must use the Update child workflows (below). Do NOT run the non-update children for an existing engine.

### Mode=CREATE (engine missing)

#### Step 1: Planning
Call:
- Workflow("WebOS_Engine_JS-Planning", "{EngineName}", "{Description}")

Output:
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.plan.md"

#### Step 2: Code
Call:
- Workflow("WebOS_Engine_JS-Code", "{EngineName}", "{Description}")

Output:
- "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"

#### Step 3: Reaudit (Post-Code Verification, Read-only)
Call:
- Workflow("WebOS_Engine_JS-Reaudit", "{EngineName}", "{Description}")

Output:
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.reaudit.md"

Loop Rule (Non-Negotiable):
- `compliant`    => proceed to Step 4 (Documentation).
- `noncompliant` => loop back to Step 1 (Planning) -> Step 2 (Code) -> Step 3 (Reaudit) until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.
- See `_Common/Workflow_Plural.md` [05A] for full semantics.

#### Step 4: Documentation
Call:
- Workflow("WebOS_Engine_JS-Documentation", "{EngineName}", "{Description}")

Output:
- "/Aliens/Docs/WebOS/Engine/JS/{EngineName}.md"

### Mode=UPDATE (engine exists)

#### Step 1: Audit (Hard)
Call:
- Workflow("WebOS_Engine_JS_Update-Audit", "{EngineName}", "{Description}")

Expected outputs:
- Run record: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Audit.md"
- Deterministic artifact: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.update.audit.md"

#### Step 2: Planning (Update Plan)
Call:
- Workflow("WebOS_Engine_JS_Update-Planning", "{EngineName}", "{Description}")

Output:
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.update.plan.md"

#### Step 3: Code (Update)
Call:
- Workflow("WebOS_Engine_JS_Update-Code", "{EngineName}", "{Description}")

Output:
- "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"

#### Step 4: Reaudit (Update — Post-Code Verification, Read-only)
Call:
- Workflow("WebOS_Engine_JS_Update-Reaudit", "{EngineName}", "{Description}")

Output:
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.update.reaudit.md"

Loop Rule:
- `noncompliant` => loop back to Update Planning -> Update Code -> Update Reaudit until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.

#### Step 5: Documentation
Call:
- Workflow("WebOS_Engine_JS_Update-Documentation", "{EngineName}", "{Description}")

Output:
- "/Aliens/Docs/WebOS/Engine/JS/{EngineName}.md"

# [06] Error Handling (Fail Fast)
Default policy: stop on first failure (audit-grade).

Recommended ErrorCodes:
- WF_MANIFEST_MISSING
- WF_MANIFEST_INVALID
- WF_TARGETS_INVALID
- WF_PERMISSION_DENIED
- WF_CHILD_FAILED
- WF_PATH_RESOLUTION_FAILED

Action guidance:
- Missing manifest -> exact path create karne ko bolo
- Permission missing -> manifest me required permission enable karne ko bolo
- Targets invalid -> exact valid example show karo
- Child failed -> child workflow id + engine name + retry instructions do

# [07] Minimal Success Criteria
Har target engine ke liye 3 outputs generate hon:
1) Planning
2) Code
3) Documentation

Mode=UPDATE additional success criteria:
- Audit.md exists (run record)
- Deterministic audit artifact exists (planning path)
- Update plan exists (".update.plan.md")

StrictScopeOnly violation => automatic failure.

# [08] CSV Task Mode (Single-Row Per Run)

Allowed:
- `Workflow('WebOS_Engine_JS','WebOS.Cache')`
- `Workflow('WebOS_Engine_JS','{FILE_NAME}.csv')`

## [08.1] CSV Schema
Required columns:
- `id`
- `name`
- `status`
- `audit` (required; CREATE-mode will auto-mark as done)
- `planning`
- `planning_save`
- `code`
- `code_save`
- `doc`
- `doc_save`

Legacy aliases (supported for backward compatibility):
- `page_name` == `name`
- `code_generate` == `code`
- `docs_generate` == `doc`
- `docs_dsave` == `doc_save`

Required prompt column:
- `description` *(must be non-empty; this is the row prompt/context)*

Progress/metrics columns (must be maintained when present in CSV):
- `code_lines`  (integer; last process me AI ne jitni code lines add/update ki)
- `modified`    (timestamp; primary code file modified time; ISO-8601 recommended)
- `created`     (timestamp; primary code file created time; ISO-8601 recommended)

Status values:
- `0` pending, `1` success, `2` failed

Hard rule:
- Process EXACTLY ONE `status==0` row per run, then STOP.
- Batch/loop forbidden (WF_CSV_BATCH_FORBIDDEN).

Prompt rule (hard):
- Picked row ka `description` empty/missing ho to fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`.
- CSV prompt missing validation failure me row ko “processed/failed” mark mat karo: `status`, `created/modified` ya timestamps mutate mat karo (row pending hi rahe jab tak prompt fix na ho).
