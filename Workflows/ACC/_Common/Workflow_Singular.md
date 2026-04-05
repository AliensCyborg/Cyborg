---
WorkflowId: Workflow_Singular
Type: CommonRuleFile
Scope: ACC.Workflow.Singular
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Purpose: >
  Common rules for every Singular workflow. This file is NOT an executable workflow by itself.
  It defines the single-run contract, validation, approvals, run-records, output rules, and safety rules
  that MUST be shared across 10,000+ Singular workflows (Planning/Code/Documentation variants included).
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
  - SingularDoesNotOrchestrate: true
---

# Workflow_Singular.md
**Description (Hinglish):**  
Yeh file *workflow nahi hai*. Yeh Singular type workflows ke liye ek **common rule-engine** hai.  
Har Singular workflow ko is file ko `require_once` karna mandatory hai, taaki safety, approvals, output contract, logs, aur audit ek jaise rahein.

> **Singular Workflow ka meaning:**  
> Aisa workflow jo **ek single target item** par focused hota hai, aur by default **kisi dusre workflow ko orchestrate/call nahi karta**.  
> (Plural orchestration ka kaam `Workflow_Plural.md` cover karta hai.)

---

## [01] Hard Rule: Common vs Unique (Very Important)
**Is file me sirf woh rules hon jo har Singular workflow ko chahiye.**

Allowed (common rules):
- Single-target input contract + normalization
- Approval/safety/no-delete policy
- RunRecord + audit + attribution standard
- Output contract + folder naming rules
- Standard error model + update workflow policy
- Minimal quality gates (where applicable)

NOT allowed (unique logic):
- Kisi specific workflow ki “kaise generate karna hai” wali steps
- Specific prompts/steps jo sirf ek workflow par apply hon
- Kisi single module/app/brand ka hardcoded structure

**Reason:**  
Is file me change ka impact 10,000+ workflows par ek sath padega.

---

## [02] Required Structure: Singular Workflow File Template
Har Singular workflow file ka minimum structure:

1) YAML header (workflow metadata)  
2) `require_once Workflows/ACC/_Common/Workflow_Singular.md`  
3) (Optional) subtype common require:
   - Planning: `Workflow_Singular-Planning.md`
   - Code: `Workflow_Singular-Code.md`
   - Documentation: `Workflow_Singular-Documentation.md`
4) Input accept: `Targets` (single item) + `Description`
5) Validate, Execute, Save outputs, Write RunRecord

**IMPORTANT:**  
Singular workflow ka input `Targets` ek single item hona chahiye. Plural loop yahan nahi chalega.

---

## [03] Inputs Contract (Mandatory)
Singular workflows MUST accept:

- `WorkflowId` (string)  
- `Targets` (string; SINGLE target item)  
- `Description` (string; any human language)  
- `ParentWorkflowId` (optional, when called by plural)  
- `RunContext` (optional, when called by plural)  
- `RunItemId` (optional, when called by plural)

### Targets parsing rules (high level)
Detailed rules: `Workflows/ACC/_Refs/ACC.Targets.md`

Common enforcement:
- `Targets` me comma `,` allowed nahi (Singular me).  
  - Agar comma aa jaye => error: “Singular workflow received multi-target. Use plural workflow.”
- Whitespace trimming mandatory.
- Empty target => error.
- `Parent/Child` syntax valid.
- `WebOS.Cache` base-name valid (extension workflow decide karega).

Description rule (hard):
- `Description` empty/missing ho to fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [04] Developer Manifest Contract (Mandatory)
Singular workflow MUST read developer-scoped manifest:

Path:
- `/Aliens/manifest.json`

Developer_Username resolution policy (recommended):
1) If ENV `ALIEN_DEV` exists => use it
2) Else if current git user/email maps to folder => use it
3) Else fail with actionable error

Manifest MUST define (minimum):
- `Mode`: `employee | assistant`
- `ApprovalMode`: `auto | manual`
- `DefaultVersion`: `latest` (fallback allowed)
- `DefaultTheme`: `_Basic`
- `Paths` (AliensRoot, WebOSRoot, WebAppsRoot, WebBrandRoot, DocsRoot)
- `Permissions` (ProdWrite, DbTouch, NoDelete=true)

---

## [05] Execution Contract (Mandatory)
Singular workflow execution always follows this canonical pipeline:

1) **Load Context**
   - Read developer manifest
   - Load refs (Targets/Paths/Output/Safety)
   - Merge RunContext:
     - If provided (by Plural) => child must respect it
     - Else build from manifest defaults

2) **Normalize Target**
   - Validate target grammar
   - Resolve domain intent using WorkflowId
     - Example: `WebSDK_Module_API-Code` => Module/API context
   - Resolve version:
     - prefer latest, fallback PHP.26.00.00

3) **Safety + Approval Gate**
   - Evaluate requested actions (prod write / db touch etc.)
   - If manual approval required => create approval request and stop

4) **Execute Unique Logic**
   - Singular workflow’s unique steps run here (defined inside the workflow file itself)
   - This common file does NOT define those steps

5) **Save Outputs**
   - Save generated artifacts according to Output contract
   - No delete. Only create/update.

6) **Finalize**
   - Write RunRecord + Summary
   - Return structured result object (conceptually)

---

## [06] Orchestration Rule (Mandatory)
Singular workflow MUST NOT orchestrate other workflows by default.

Allowed:
- Reading refs/common files
- Using ecosystem engines (WebOS/System/WebSDK) as tools (conceptually)
- Writing docs/logs/artifacts

Not allowed (default):
- Calling other workflow ids like a mini-plural
- Looping over multiple targets

Exception policy (optional):
- If YAML header explicitly sets `AllowNestedCalls=true`, then:
  - Only allowed for utility workflows (future: e.g., “ResolvePaths”, “ValidateTargets”)
  - Must still remain single-target in effect

---

## [07] Failure Strategy (Mandatory)
Singular workflow must always return one of:
- `success`
- `failed`
- `partial` (rare; only if allowed by workflow’s own spec)

Rules:
- Missing target:
  - If workflow id contains `_Update` => fail (modify-only policy)
  - Otherwise:
    - If singular is a “create” workflow => may create (depends on that workflow’s unique spec)
    - If spec says “Modify only” => fail

Error format (recommended fields):
- `ErrorCode`
- `Message`
- `ActionableFix`
- `Target`
- `WorkflowId`
- `RunItemId`

---

## [08] Output Contract (Mandatory)
Detailed output schema: `Workflows/ACC/_Refs/ACC.Output.md`

Common requirements:
- Every singular run MUST create a RunRecord folder:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/`
- If called from plural (has RunItemId), MUST write inside the parent item folder too:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`

Docs base paths (locked):
- `/Aliens/Docs/WebOS/`
- `/Aliens/Docs/WebApps/`
- `/Aliens/Docs/WebBrand/`
- `/Aliens/Docs/WebSDK/`

Planning outputs:
- Default: `/Aliens/.Alien/{Developer}/Planning/`

---

## [09] Approval Contract (Mandatory)
Approval rules depend on:
- manifest `ApprovalMode`
- workflow YAML requested permissions
- safety policy (`ACC.Safety.md`)

If `ApprovalMode=manual`:
- Create approval request:
  - `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
- Stop execution until approved.

If `ApprovalMode=auto`:
- Proceed, but log “auto-approved” decision in RunRecord.

---

## [10] Logging + Audit (Mandatory)
Singular workflows MUST produce:
- Human-readable log: `.md`
- Machine-readable log: `.json` (recommended)
- Attribution: developer + time + workflow id + target

Suggested folders:
- `/Aliens/.Alien/{Developer}/Runs/`
- `/Aliens/.Alien/{Developer}/Reports/`
- `/Aliens/.Alien/{Developer}/State/`

---

## [11] Security + Safety (Mandatory)
Non-negotiables:
- **NoDelete = true**
- Never write secrets into repo (use `Secrets/` gitignored)
- DB touch allowed only if:
  - logged + artifacted (SQL file / migration note)
  - approvals respected (auto/manual)

---

## [12] Quality Gate (Mandatory)
Singular workflows must:
- Respect “Premium UI” definition when the workflow is UI-related (via refs)
- Respect AliensStyle/AliensGrade when the workflow is code-related (enforced by subtype common in `Workflow_Singular-Code.md`)

This file enforces only the shared gates (not unique generation steps).

---

## [13] Change Control
Because this file affects 10,000+ workflows, any change MUST:
- Be backward compatible where possible
- Be documented in changelog
- Add migration note if behavior changes

### Changelog
- 26.00.00 (2025-12-24): Initial Common Rules for Singular Workflows
