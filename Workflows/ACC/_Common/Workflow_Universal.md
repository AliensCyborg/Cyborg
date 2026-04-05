---
WorkflowId: Workflow_Universal
Type: CommonRuleFile
Scope: ACC.Workflow.Universal
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-21
Purpose: >
  Global common rules for EVERY workflow type (Plural, Singular, Planning, Code, Documentation,
  Inventory, Training, etc.). This file is NOT an executable workflow by itself.
  It defines the universal execution contract and hard boundaries so that 10,000+ workflows
  remain predictable, safe, and audit-friendly.
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
  AlienCyborg: "Agent + VSCode Copilot Custom Agent + Azure AI"
NonNegotiables:
  - StrictScopeOnly: true
  - NoExtraWork: true
  - NoDelete: true
  - NoSilentChanges: true
  - ExplicitOutputsOnly: true
  - FailOnAmbiguity: true
  - ManifestRequired: true
  - EnterpriseGradeOnly: true
  - QualityOverSpeed: true
  - NoCheapOrAverageOutput: true
  - NoPlaceholdersOrIncomplete: true
Requires:
  - /Aliens/manifest.json
...

## [01] Hard Rule: Strict Scope Only (No Extra Work)
**Universal rule:** jitna bola jaye / jitna workflow me defined ho, utna hi karna hai.  
Agent apni taraf se "faltu me" koi extra kaam, extra files, extra features, extra refactor, ya extra output nahi karega.

---

## [01A] Hard Rule: Enterprise-Grade Quality (Quality > Speed)
**Universal rule:** kisi bhi workflow run me cheap/average/adhura output acceptable nahi.

Constraints (must-haves):
- Output **enterprise-grade**, complete, aur satisfying ho (within the workflow scope).
- Enterprise-grade output ka matlab aksar **long / detailed** hota hai; shortness ke liye quality sacrifice karna forbidden hai.
- “Placeholder”, “TODO”, “lorem ipsum”, half-finished stubs, ya vague pseudo-code provide mat karna.
- Agar kisi cheez ke bina complete/enterprise-grade output possible nahi (missing requirements / missing inputs / ambiguity):
  - **fail fast** with a clear error (do not guess).
  - Tell exactly what is missing and what to provide.

Important:
- Yeh rule **scope expand** karne ki permission nahi deta.
- Quality improve karne ka matlab sirf itna hai: jo scope defined hai usko best possible enterprise-grade quality me deliver karna.

**Scope sources (priority order):**
1) The workflow file itself (steps + constraints)
2) The `Workflow(WorkflowId, Targets, Description)` invocation payload
3) Developer manifest permissions + mode

**Forbidden (examples):**
- “Nice-to-have” improvements jo user ne nahi bole
- Extra modules/components/apis create karna
- Code formatting refactor / rename / restructure without request
- Additional docs, diagrams, inventories, logs if workflow/manifest does not require them
- Extra dependencies add karna, extra configs change karna, extra folders create karna

**Allowed (only if REQUIRED for correctness and already within workflow scope):**
- Minimal corrections to satisfy the workflow contract (e.g., required imports, required headers)
- Only those files that the workflow explicitly lists as outputs
- Only those edits that are necessary to meet the stated acceptance criteria

**If scope is unclear:** fail fast with a clear error explaining what is missing/ambiguous.  
No guessing, no implicit expansion.

---

## [02] Universal Invocation Contract (ACC.Workflow())
All workflows are invoked as:

`Workflow( WorkflowId, Targets, Description )`

### 2.1 WorkflowId
- Must map to an existing workflow file by the same ID (repo-driven).
- If not found: error (do not auto-invent a new workflow unless the workflow request is explicitly “create workflow”).

### 2.2 Targets
- A comma-separated list. Each item is one independent run of the same workflow with different payload.
  - Example: `User/UserNew, User/Users, Web3/Web3_Accounts`
- Trim spaces. Preserve casing as-is (naming conventions are strict).
- Slash indicates parent/child binding:
  - Parent can be a Module/App/Brand/Engine (workflow decides)
  - Child can be API/Cron/Webhook/Component/Template (workflow decides)
- If target contains `_` in parent name, treat as “Cluster” semantics when the workflow requires module/table resolution rules.

### 2.3 Description
- Human language instructions.
- Must be treated as *constraints* and *requirements*, not as license to expand scope.
- If Description conflicts with workflow rules: workflow rules win (or fail with conflict error if hard).

Hard rule:
- `Description` empty/missing ho to fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Manifest Contract (Mandatory)
Every workflow MUST read developer-scoped manifest first:

Path:
- `/Aliens/manifest.json`

**Minimum required fields (global):**
- `Mode`: `assistant` | `employee`
- `Approval`: `auto` | `manual`
- `Permissions`:
  - `CanEditProdCode`: true/false
  - `CanTouchDatabase`: true/false
  - `CanDeleteFiles`: must be false (universal policy)
- `Paths`:
  - `AliensRoot`: default `/Aliens`
  - `DeveloperRoot`: default `/Aliens/.Alien/{Developer_Username}`

**Hard rules:**
- If manifest is missing: fail with actionable error.
- If required fields are missing: fail with actionable error.
- If `Approval=manual`, any step marked “needs approval” must stop and ask for approval (workflow decides what is “approval-gated”).
- If `Approval=auto`, proceed without waiting.

**Mode meaning (high-level):**
- `assistant`: do nothing unless explicitly invoked by ACC/Workflow run.
- `employee`: after tasks finish, can keep watching the Task folder only if manifest enables it. Otherwise stop.

---

## [04] Universal Change Policy (No Delete + Explicit Outputs)
### 4.1 No Delete (Non-Negotiable)
- No file deletion. No folder deletion. No “cleanup” operations.

### 4.2 Modify vs Create rule
- If workflow action is **Modify Only** and a required target file is missing: **error** (do not create).
- If workflow action is **Create/Generate** and file already exists:
  - **NEVER overwrite via the non-update workflow.**
  - MUST route to the corresponding `*_Update` workflow family.
  - If no corresponding update workflow exists, FAIL with actionable error.

#### 4.2.1 Update Routing Rule (SSOT)
If a workflow is invoked and the primary `TargetFilePath` already exists:

- If `WorkflowId` already contains `_Update`: continue (modify-only rules apply).
- Else: the runner/orchestrator MUST switch to the matching `_Update` workflow.

Deterministic mapping rule (generic):
- If `WorkflowId` ends with a suffix like `-Planning`, `-Code`, `-Documentation`, `-Audit`:
  - Insert `_Update` before the suffix.
  - Example: `WebApp_Component-Code` => `WebApp_Component_Update-Code`
- Else:
  - Append `_Update`.
  - Example: `WebSDK_Module_API` => `WebSDK_Module_API_Update`

Fail-fast error when update workflow is missing:
- `ErrorCode`: `WF_TARGET_EXISTS_USE_UPDATE`
- `Action`: "Run the corresponding *_Update workflow for this target."

### 4.3 Minimal Touch Principle
- Touch only the files required by the workflow outputs.
- Keep changes minimal and localized.

---

## [05] Output Contract (Paths + Naming)
- Every workflow MUST explicitly define:
  - Output path(s)
  - Output file name(s)
  - Output format (md/php/js/sql/sh)
- If output path cannot be resolved with certainty: fail with “Path resolution failed” error.

---

## [06] Standard Error Model (for AI Audits)
When failing, use this structured format in response/logging:

- `ErrorCode`: short stable code (e.g., `WF_MANIFEST_MISSING`)
- `Message`: one-line summary
- `Details`: bullet list of what was expected vs what was found
- `Action`: exact next step the user/dev should do

---

## [07] Include Order (Mandatory)
Every workflow file (any type) MUST include this file first:

1) `require_once Workflows/ACC/_Common/Workflow_Universal.md`
2) Then type-specific common file(s), e.g.:
   - `Workflow_Plural.md`
   - `Workflow_Singular.md`
   - `Workflow_Singular-Planning.md`
   - `Workflow_Singular-Code.md`
   - `Workflow_Singular-Documentation.md`
   - (future) `Workflow_Singular-Inventory.md`, `Workflow_Singular-Training.md`

Reason: universal safety + scope rules must apply before any specialization.

---

## [08] Quick Acceptance Checklist (Universal)
A workflow run is “valid” only if:
- Manifest read + validated
- Targets parsed deterministically
- Outputs are explicitly known (paths + names)
- No extra work was done beyond scope
- No deletes performed
- Any ambiguity produced a fail-fast error (not a guess)
- Output is enterprise-grade (no placeholders / no incomplete stubs)
