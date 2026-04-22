---
WorkflowId: Workflow_Plural
Type: CommonRuleFile
Scope: ACC.Workflow.Plural
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-21
Purpose: >
  Common rules for every Plural workflow. This file is NOT an executable workflow by itself.
  It defines the orchestration contract, validation, approvals, run-records, and safety rules
  that MUST be shared across 10,000+ Plural workflows.
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
  AlienCyborg: "Agent + VSCode Copilot Custom Agent + Azure AI"
Requires:
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - UpdateWorkflows: "modify-only (missing target => error)"
  - DefaultTheme: "_Basic"
  - DefaultVersionFallback: "PHP.26.00.00"
---

# Workflow_Plural.md
**Description (Hinglish):**  
Yeh file *workflow nahi hai*. Yeh Plural type workflows ke liye ek **common rule-engine** hai.  
Har Plural workflow ko is file ko `require_once` karna mandatory hai, taaki orchestration, safety, approvals, logs, output contract aur audit ek jaise rahein.

> **Plural Workflow ka meaning:**  
> Aisa workflow jo 1 ya zyada workflows ko call kar sakta hai (Plural ya Singular), aur targets ki list par loop chala kar same work ko repeat/run karta hai.

---

## [01] Hard Rule: Common vs Unique (Very Important)
**Is file me sirf woh rules hon jo har Plural workflow ko chahiye.**

Allowed in this common file:
- Input parsing + target normalization contract
- Approval / safety / no-delete policy
- Execution orchestration contract (loop, fail strategy, summary)
- Run-record + audit + attribution
- Standard error model

NOT allowed in this common file:
- Kisi specific workflow ka unique logic (e.g., “WebSDK_Module_API me planning kaise banani hai”)
- Specific file paths jo sirf ek workflow ko apply hon
- Specific prompts/steps jo sirf ek workflow ko apply hon

**Reason:**  
Is file me change ka impact 10,000+ workflows par ek sath padega.

---

## [02] Required Structure: Plural Workflow File Template
Har Plural workflow file ka minimum structure:

1) YAML header (workflow metadata)  
2) `require_once Workflow_Plural.md`  
3) `Inputs`: `Targets`, `Description` (mandatory)  
4) `Calls`: ek list of child workflows (usually Planning/Code/Documentation)  
5) Orchestration: targets ke upar loop + call execution  

Example skeleton (conceptual):

- File: `Workflows/ACC/WebSDK/WebSDK_Module_API.md`
  - Type: `Workflow_Plural`
  - Requires: `Workflows/ACC/_Common/Workflow_Plural.md`
  - Calls:
    - `WebSDK_Module_API-Planning`
    - `WebSDK_Module_API-Code`
    - `WebSDK_Module_API-Documentation`

---

## [03] Inputs Contract (Mandatory)
Plural workflows MUST accept exactly:

- `WorkflowId` (string)  
- `Targets` (string)  
- `Description` (string; any human language)

### Targets parsing rules (high level)
Detailed rules: `Workflows/ACC/_Refs/ACC.Targets.md`

Common rules enforced here:
- Comma `,` means **repeat run** per target item.
- Whitespace trimming mandatory.
- Empty entries default: ignored + warning logged.
  - Note: some strict workflows may override and treat empty item as error.
- Duplicate targets default: allowed (because comma implies explicit re-run intent).
  - Optional: allow `Deduplicate=true` in workflow YAML to override.
- `Parent/Child` syntax means relationship; actual type resolved by workflow id.
- `WebOS.Cache` syntax means base-name without extension; extension decided by workflow id.

Additional clarity:
- `Targets` are always **resource identifiers** (names), not behavior modes/keywords.
- `Description` is mandatory and must be non-empty. Empty/missing => fail with `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [04] Developer Manifest Contract (Mandatory)
Plural orchestration MUST read developer-scoped manifest:

Path:
- `/Aliens/manifest.json`

Developer_Username resolution policy (recommended):
1) If ENV `ALIEN_DEV` exists => use it
2) Else if current git user/email maps to folder => use it
3) Else fail with actionable error: “Developer_Username not resolved; create folder or set ALIEN_DEV.”

Manifest MUST define (minimum):
- `Mode`: `employee | assistant`
- `ApprovalMode`: `auto | manual`
- `DefaultVersion`: `latest` (fallback allowed)
- `DefaultTheme`: `_Basic`
- `Paths` (AliensRoot, WebOSRoot, WebAppsRoot, WebBrandRoot, DocsRoot)
- `Permissions` (ProdWrite, DbTouch, NoDelete=true)
- `Worker` (poll interval etc.)

---

## [05] Orchestration Contract (Mandatory)
Plural workflow execution always follows this canonical pipeline:

1) **Load Context**
   - Read developer manifest
   - Load ACC refs (Targets/Paths/Output/Safety)
   - Build execution context: developer, mode, approval, permissions

2) **Normalize Targets**
   - Convert Targets string -> TargetList[] (ordered)
   - Validate each target against grammar (refs)
   - Build Payload per target

3) **Run Loop**
   For each target in TargetList:
   - Build `RunItemId`
   - Call child workflows in configured order
   - Collect outputs, warnings, errors

4) **Finalize**
   - Save RunRecord (audit)
   - Save Summary (human readable)
   - If EmployeeMode: continue polling Task/Inbox etc. (runtime behavior)

Default execution order:
- Planning => Code => Documentation
- For code-producing workflows, the **recommended** order is:
  Planning => Code => **Reaudit** => Documentation
(Plural workflow may override order ONLY if explicitly required in its YAML header.)

### [05A] Reaudit Step (Universal; Opt-in via Calls list)
For any code-producing Plural workflow whose `Calls` list includes a child of
type `Workflow_Singular-Reaudit` (typically named `<Parent>-Reaudit`):
- Run that Reaudit child **after** Code and **before** Documentation.
- Reaudit is **read-only on Code** (Non-Negotiable).
- Reaudit MUST consume Planning's `## Required Items` block + `## Out-of-Scope`
  block + the freshly written code state.
- Reaudit produces a Compliance Matrix + verdict (`compliant` | `noncompliant`).

Loop semantics (Quality > Speed; Non-Negotiable):
- Verdict `compliant`     => proceed to Documentation.
- Verdict `noncompliant`  => orchestrator MUST loop back to Planning (NOT Code).
  Planning refines Required Items / Out-of-Scope based on Reaudit findings.
  Then Code re-runs, then Reaudit re-runs (Iteration N+1).
- No iteration limit by default (`MaxReauditIterations: 0`). A positive bound
  is advisory only; reaching it MUST raise `ACC_ERR_REAUDIT_MAX_ITERATIONS`
  and stop, never auto-promote `noncompliant` to `compliant`.
- Documentation MUST NOT run while verdict is `noncompliant`.

Backward compatibility:
- Reaudit is **opt-in**. Workflows whose Calls list does NOT include a Reaudit
  child continue to work as Planning => Code => Documentation.
- New code-producing workflows SHOULD adopt Reaudit. Existing workflows MAY
  adopt Reaudit incrementally.

### [05B] Universal Strict Prompt Compliance (Cross-Reference)
Every Plural workflow inherits, via its Singular Planning + Code children:
- `Workflow_Singular-Planning.md` Section 18 — `## Required Items` block (mandatory)
- `Workflow_Singular-Planning.md` Section 19 — `## Out-of-Scope` block (mandatory)
- `Workflow_Singular-Code.md` Section [06A] — Strict Scope Compliance

Plural orchestrator MUST surface in the final RunSummary:
- Number of Required Items (RI-NNN) declared by Planning
- Reaudit verdict (when Reaudit child ran)
- Out-of-scope violations count (when Reaudit child ran)

### 5.1 Target Exists => Route to Update (SSOT)
If a Plural workflow is invoked and its primary `TargetFilePath` already exists:

- The system MUST run the corresponding `*_Update` Plural workflow family instead of continuing the non-update workflow.
- Non-update workflows must NOT behave like “update in-place” when the target already exists.

Reason:
- Determinism + safety (no accidental overwrite / mixed create+update semantics).
- Update workflows are explicitly modify-only (missing target => error), so the intent stays clean.

Mapping rule (generic; same as `Workflow_Universal.md`):
- Insert `_Update` before `-Planning|-Code|-Documentation|-Audit` suffix when present.
- Otherwise append `_Update`.

---

## [06] Calling Child Workflows (Mandatory)
Plural workflows MUST call child workflows using a single consistent pattern.

**Call signature (conceptual):**
- `CallWorkflow(ChildWorkflowId, Payload)`

**Payload minimal fields:**
- `Targets`: single target item (not the full list)
- `Description`: inherited description (pass-through; do NOT rewrite)
- `ParentWorkflowId`: current plural workflow id
- `RunContext`: developer + permissions + mode + version + theme
- `RunItemId`: stable id for tracing

Child workflow must NEVER assume global context; it must use payload/context.

Hard rule:
- `Description` me enterprise objective + hard constraints embedded ho sakte hain.
- Plural workflow ko `Description` ko shorten/summarize/modify nahi karna.

Nested workflow calling allowed:
- Plural can call Plural
- Plural can call Singular
- Singular should not call Plural (recommended) unless explicitly allowed by policy

---

## [07] Failure Strategy (Mandatory)
Plural workflows MUST declare a failure strategy. Default is safe:

- Default: `FailFast=false` (continue other targets)
- For each target:
  - If Planning fails: skip Code+Docs for that target
  - If Code fails: Docs may still run (optional) but should label “code generation failed”
  - If Docs fails: target still marked “partial success”

**Update workflows rule:**
- If workflow id contains `_Update` then missing target MUST be an error and target marked failed.

---

## [08] Output Contract (Mandatory)
Detailed output schema: `Workflows/ACC/_Refs/ACC.Output.md`

Common requirements:
- Every run MUST create a RunRecord folder:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/`
- Every target item MUST create a per-item record:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`
- Planning outputs (if any):
  - `/Aliens/.Alien/{Developer}/Planning/` (or within Runs/Items with a copy)
- Documentation outputs must be saved under:
  - `/Aliens/Docs/{Domain}/...` (Domain resolved by workflow id)

**Run Summary must include:**
- TotalTargets, Succeeded, Failed, Partial
- CreatedFiles[], UpdatedFiles[]
- Warnings[], Errors[]
- NextActions (if manual approval pending)

---

## [09] Approval Contract (Mandatory)
Approval rules depend on:
- Developer manifest `ApprovalMode`
- Workflow YAML `Permissions` request
- Safety policy (`ACC.Safety.md`)

If `ApprovalMode=manual`:
- Create approval request file:
  - `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
- MUST block dangerous actions until approved.

If `ApprovalMode=auto`:
- Proceed without approval (as per manifest), but still log “auto-approved” decision in RunRecord.

Dangerous actions (examples; exact list in Safety ref):
- prod code write
- database touch/migration
- mass file modifications across modules/apps
- security-sensitive config changes

---

## [10] Logging + Audit (Mandatory)
Plural workflows MUST produce:
- Human-readable log: `.md`
- Machine-readable log: `.json` (recommended)
- Attribution data: developer, time, workflow id, targets

Suggested folders:
- `/Aliens/.Alien/{Developer}/Runs/`
- `/Aliens/.Alien/{Developer}/Reports/`
- `/Aliens/.Alien/{Developer}/State/`

If EmployeeMode:
- Update heartbeat in `State/worker.json` (recommended) so team can see it is alive.

---

## [11] Security + Safety (Mandatory)
Non-negotiables:
- **NoDelete = true** (never delete files; only create/update)
- Secrets must never be written into repo:
  - Use `.Alien/{Developer}/Secrets/` (gitignored)
- If a workflow needs DB touch:
  - Must create/append SQL artifact + log the change
  - Must never run destructive SQL unless explicitly approved by policy

---

## [12] Quality Gate (Mandatory)
Plural workflows MUST enforce:
- The child Code workflow must follow your ecosystem standards (AliensStyle/AliensGrade) if applicable.
- The child Documentation workflow must store docs in `/Aliens/Docs/...`.

Plural workflow itself is orchestration-only, so:
- It must ensure the Code workflow declares which standards it applied.
- It must surface quality summary in the final RunSummary.

---

## [13] Quick Examples (Conceptual)
### Example A: Big workflow
`Workflow("WebApp", "Exchange, Swap", "Binance jaisa app chahiye complete")`
- Targets: 2
- Calls (mandatory pipeline):
  - WebApp-Research => WebApp-Planning => WebApp-Planner => WebApp-Structure
  - WebApp-Development => WebApp-Audit => WebApp-Refactor
- Quality rule: Quality > Speed (time-unbounded is allowed)

### Example B: Small workflow
`Workflow("WebApp_Component", "Exchange/DepositNew, Exchange/Withdraws", "UI premium hona zaroori hai")`
- Targets: 2
- Calls: WebApp_Component-Planning => WebApp_Component-Code => WebApp_Component-Documentation

---

## [14] Change Control
Because this file affects 10,000+ workflows, any change MUST:
- Be backward compatible where possible
- Be documented in a changelog section
- Add migration note if behavior changes

### Changelog
- 26.00.00 (2025-12-24): Initial Common Rules for Plural Workflows
- 26.01.00 (2026-04-18): Added [05A] Reaudit step (universal opt-in) +
  [05B] Strict Prompt Compliance cross-reference. Backward compatible.
  Author: AlienCyborg.
