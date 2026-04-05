---
RefId: ACC.Safety
Type: ACC.Reference
Scope: SafetyPolicy
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-21
AppliesTo:
  - "Workflows/ACC/_Common/Workflow_Plural.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  - "All Individual Workflow Files"
NonNegotiables:
  - NoDelete: true
  - ModifyOnly: true
  - MissingTargetOnUpdateWorkflow: "error"
  - SecretsNeverInRepo: true
  - AuditAlwaysOn: true
SourcesOfTruth:
  - "/Aliens/manifest.json"
---

# ACC.Safety.md
**Purpose (Hinglish):**  
Yeh file ACC/AlienCyborg ke liye **Safety + Approvals + Guardrails** define karti hai.  
Iska goal: AI ko powerful rakhna, but uncontrolled/dangerous changes se ecosystem ko bachana.

> IMPORTANT: Is policy me change ka impact 10,000+ workflows par padega.  
> Isliye is file me sirf global safety rules hone chahiye.

---

## 1) Core Principles (Non-Negotiable)
1) **NoDelete = true**  
   - AI/Workflow **kisi bhi file ko delete nahi karega** (repo ho ya prod).  
   - Agar cleanup chahiye, to AI sirf “proposal + list” banayega, delete nahi karega.

2) **ModifyOnly = true**  
   - Existing target file milti hai: modify allowed (policy + approval ke hisaab se)  
   - Existing target file missing hai: behavior workflow type se decide hoga:
     - Normal (create allowed): create + logs
     - Update workflows (`*_Update`): **missing = error** (create nahi hoga)

3) **AuditAlwaysOn = true**  
   - Har run ka record mandatory:
     - `/Aliens/.Alien/{Developer}/Runs/{RunId}/...`

4) **SecretsNeverInRepo = true**  
   - API keys, tokens, passwords, connection strings **repo me kabhi nahi**.  
   - Allowed locations:
     - `/Aliens/.Alien/{Developer}/Secrets/` (gitignored)
     - Environment variables (preferred)
   - Logs me secrets redact honge.

---

## 2) Safety Context (Manifest is mandatory)
Safety decisions always read from:
- `/Aliens/manifest.json`

### Minimum required manifest keys (safety related)
- `Mode`: `employee | assistant`
- `ApprovalMode`: `auto | manual`
- `Permissions`:
  - `ProdWrite`: true/false
  - `DbTouch`: true/false
  - `NoDelete`: true (must)
  - `ModifyOnly`: true (must)
- `Paths`: roots of Aliens ecosystem (WebOS/WebApps/WebBrand/WebSDK/Docs)
- `Identity`: developer/team info (for audit attribution)

If manifest missing/invalid:
- Workflow must fail with clear error and “how to fix”.

---

## 3) Action Classification (Risk Levels)
Har workflow action ko is table ke hisaab se classify karna mandatory hai:

### Level 0 — Safe
No approval needed (auto or manual dono me):
- Planning generation
- Documentation generation
- Read-only analysis
- Creating *non-prod* drafts inside `.Alien/{Developer}/Drafts/` or `Runs/`

### Level 1 — Low
Auto mode: allowed  
Manual mode: optional approval (recommendation only, not block)
- Creating new files in allowed target areas (non-critical)  
- Minor refactor that does not change runtime behavior (formatting, comments, docs)

### Level 2 — Medium
Auto mode: allowed (but strict audit + diff + rollback notes)  
Manual mode: **approval required** (block until approved)
- Creating new API/Cron/Webhook files in WebSDK modules
- Creating new Component/Template files in WebApps/WebBrand theme
- Editing existing module class/index.php with behavioral changes

### Level 3 — High / Dangerous
Auto mode: allowed **only if manifest explicitly allows** + strict safeguards  
Manual mode: **approval required** (always)
- Editing WebOS Engine files
- Editing prod runtime code paths (anything under `/Aliens/WebOS/` or core loaders/routers)
- Security/auth/session/crypto/payment related modifications
- Mass changes across multiple modules/apps

### Level 4 — Blocked (never)
Auto mode: blocked  
Manual mode: blocked (unless policy is extended later)
- Delete operations
- Writing secrets into repo
- Destructive DB operations without explicit SQL artifact + approval

---

## 4) Approval Rules (Auto vs Manual)
### If `ApprovalMode = auto`
- Workflow proceeds as per manifest permissions
- Still must create:
  - RunRecord
  - Change summary
  - Risk classification note: “Auto-approved by manifest”

### If `ApprovalMode = manual`
- Workflow must STOP before executing Medium/High actions.
- It must create an approval request:
  - `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
- Approval file must include:
  - WorkflowId, Targets, Description
  - Risk level (0-4)
  - List of files to create/update
  - DB touch? (yes/no)
  - Prod write? (yes/no)
  - Rollback plan (minimal)

Only after explicit approval signal (mechanism defined in manifest) workflow continues.

---

## 5) ProdWrite Policy
User decision: **prod code modify allowed** (manifest controlled).  
So enforcement:

- If action touches `/Aliens/WebOS/...` OR other prod runtime code:
  - Require `Permissions.ProdWrite=true`
  - Risk level at least **High**
  - Manual mode => approval mandatory

Additionally:
- Must write a “diff summary” in RunRecord
- Must not remove “important code/features” unless explicitly instructed

---

## 6) DbTouch Policy
User decision: **database touch allowed** (manifest controlled).  
So enforcement:

- Require `Permissions.DbTouch=true`
- Any DB touch must produce an artifact:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Artifacts/sql/{ArtifactName}.sql`
- SQL rules:
  - Never run destructive SQL silently
  - For destructive SQL (DROP/TRUNCATE/ALTER risky):
    - Risk level High
    - Manual mode => approval mandatory
- DB connection secrets:
  - only from `.Alien/Secrets` or ENV

---

## 7) Target Existence Rules (Create vs Update)
- For normal workflows (non `_Update`):
  - Create new file allowed (if path is valid)
- For `_Update` workflows:
  - **Target missing => error**  
  - Workflow must not “invent” a new file silently
  - It may suggest “use non-update workflow” as guidance

### 7.1 Target Exists Routing Rule (SSOT)
If a workflow is invoked and the primary target file already exists:
- Non-update workflows MUST NOT overwrite/modify that file.
- The run MUST switch to the corresponding `*_Update` workflow family.
- If no update workflow exists: fail fast with `WF_TARGET_EXISTS_USE_UPDATE`.

Reason:
- Prevents accidental overwrites.
- Keeps create workflows “create-only” and update workflows “modify-only”.

---

## 8) Allowed Roots (Hard Fence)
Workflows may only write within these allowed roots (from manifest):
- `/Aliens/WebOS/`
- `/Aliens/WebApp/`
- `/Aliens/WebBrand/`
- `/Aliens/WebSDK/`
- `/Aliens/Docs/`
- `/Aliens/.Alien/{Developer}/...` (workspace)

Any attempt to write outside allowed roots:
- Block + error

---

## 9) Concurrency + Locking (Prevent Conflicts)
To avoid two workflows editing same target:
- Before write, create lock file:
  - `/Aliens/.Alien/{Developer}/Locks/{TargetHash}.lock`
- If lock exists and is fresh:
  - stop or retry (strategy from manifest)
- Always release lock at end (even on failure)

---

## 10) Rollback Minimum Standard
For Medium/High actions:
- Save “before” snapshot of changed files in RunRecord:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Artifacts/before/`
- Save “after” snapshot (or diff summary):
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Artifacts/after/`

Rollback intent:
- Quick revert possible even if human intervenes.

---

## 11) Security Hygiene
- Never expose secrets in logs
- Redact patterns: token, api_key, secret, password, connection string
- Do not weaken auth rules
- If workflow touches Auth/Payment/Crypto:
  - force High classification
  - force security checklist in approval request

---

## 12) Human Required Areas
If workflow YAML marks:
- `HumanRequired: true` OR category says “Graphics & Web Design Team”
Then:
- Workflow can generate drafts + guidelines
- Final asset production remains human-reviewed (unless manifest says otherwise)

---

## 12.1) Default Execution Discipline (Reduce Re-Instruction)
These defaults exist to keep ACC behavior consistent on new installs and avoid repeated human instructions.

### A) CSV Task Runs: One Item Per Run (Assistant Mode)
If a workflow is invoked with a CSV task file (TaskQueue mode), then by default:
- In `AssistantMode`: process **exactly 1** pending row per run.
- Repeat runs to process the next pending row.

CSV prompt rule (non-negotiable):
- Picked row ka `Description` (planner style) ya `description` (WebOS engine style) **read karna mandatory** hai.
- Isi description ko **prompt/context** ke taur par use karna mandatory hai (child call me pass karo, aur planning/code/docs me as primary instruction treat karo).
- Agar description column missing ho ya value empty ho:
  - Row ko process mat karo.
  - Standard error model se fail karo: `ACC_ERR_CSV_DESCRIPTION_MISSING`.
  - CSV writeback me `status=2` (failed) mark karo (jab workflow CSV writeback control karta ho).

CSV progress tracking (mandatory when columns exist in the CSV):
- `code_lines`: last processed run me AI ne **sirf primary code artifact** (targeted file) me kitni lines create/update ki (integer)
  - Planning artifacts, docs, approvals, RunRecord, ya any other non-target files ki lines include karna forbidden.
  - Deterministic guidance:
    - If file created in this run => `code_lines = total lines in that created file`.
    - If file updated => `code_lines = diff-based count` (added + modified lines; runner must implement a consistent method).
    - If no code artifact written => `code_lines = 0`.
- `modified`: jis primary code file ko update/modify kiya uska timestamp (ISO-8601 recommended, UTC recommended)
- `created`: jis primary code file ko create kiya uska timestamp (ISO-8601 recommended, UTC recommended)
  - Semantics:
    - Create par `created` set karo (set-once; overwrite forbidden).
    - Update par `modified` set karo (each update par refresh allowed).
    - Create run me `modified` set karna optional hai; default recommended: blank.

Rationale:
- Keeps audit + approvals clean and deterministic.
- Prevents accidental mass changes when a user only intended to “start the workflow”.

Exception:
- A workflow may explicitly declare and document safe multi-item concurrency.

### B) Manual Approval: File-Based Source of Truth
If `ApprovalMode = manual`, then approval is only considered valid when it is recorded in the generated approval request file under:
- `/Aliens/.Alien/{Developer}/Approvals/`

Standard decision block (append-only):
```
## Decision
- Status: APPROVED | REJECTED
- ApprovedBy: {Developer}
- ApprovedAt: YYYY-MM-DD
```

If the user approves in chat (e.g. "APPROVED"), the workflow runner MUST transcribe that decision into the approval file to maintain an auditable record.

### C) No Ad-hoc Execution (“No Jugaad”)
When a user invokes `Workflow(WorkflowId, Targets|Csv, Description)`, execution MUST happen via the official ACC workflow engine/runner.

Forbidden patterns:
- Writing a one-off Python/PS1 script to interpret workflow intent
- Bypassing workflow phases (Planning → Code → Docs) outside the workflow system

Allowed:
- Improving the official workflow engine/runner so that the workflow rules are implemented once, correctly, and reused everywhere.

### D) Approval Idempotency (No Spam)
In `ApprovalMode = manual`, the approval request must be stable and reusable:
- Use a deterministic ApprovalId based on `{WorkflowId + Target}` (not per-run random).
- If an approval file already exists for the same `{WorkflowId + Target}`:
  - Do NOT create a new file.
  - Reuse and append-only update the existing file.

Execution gating:
- If approval file has `Status: APPROVED` in the Decision block => workflow may proceed.
- If approval is missing/pending => workflow must stop before prod writes.

---

## 13) Standard Error Codes (Summary)
(Full mapping in `ACC.Errors.md`)

- `SAFETY_MANIFEST_MISSING`
- `SAFETY_PERMISSION_DENIED`
- `SAFETY_DELETE_FORBIDDEN`
- `SAFETY_OUTSIDE_ALLOWED_ROOT`
- `SAFETY_APPROVAL_REQUIRED`
- `SAFETY_DB_DESTRUCTIVE_BLOCKED`
- `SAFETY_TARGET_MISSING_UPDATE_WORKFLOW`

---

## 14) Changelog
- 26.00.00 (2025-12-24): Initial Safety + Approvals + Guardrails policy for ACC workflows
