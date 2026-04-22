---
WorkflowId: PyOS_Engine_JS-Planning
Type: Workflow_Singular-Planning
Domain: PyOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflow: PyOS_Engine_JS
Purpose: >
  Produce a single JS engine plan for a single target engine name. The plan must be used
  by PyOS_Engine_JS-Code to generate/update the engine deterministically, without inventing scope.
  NOTE: If the target engine already exists, prefer the Update pipeline:
  - PyOS_Engine_JS_Update-Audit → PyOS_Engine_JS_Update-Planning (".update.plan.md")
  This avoids re-running both PyOS_Engine_JS and PyOS_Engine_JS_Update.
Invocation:
  Signature: Workflow("PyOS_Engine_JS-Planning", "PyOS.Cache", "Human language requirement")
  TargetsMeaning: >
    Single engine name without extension. Comma list is invalid (singular workflow).
  DescriptionMeaning: >
    Constraints, acceptance criteria, and change intent. If empty/missing -> fail fast.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/JS/"
    Pattern: "{EngineName}.plan.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
Notes:
  - Planning is read-mostly; no production code changes here.
  - If engine file exists => plan is "Update" (no breaking changes).
  - If engine file missing => behavior depends on manifest (ModifyOnly default).
  - For existing engines, Update planning requires the deterministic audit artifact (".update.audit.md").
ExampleFiles:
  - "/Aliens/PyOS/Py.26.01.00/Unit/js/PyOS.Loader.js"
  - "/Aliens/PyOS/Py.26.01.00/Unit/js/PyOS.Cache.js"
  - "/Aliens/PyOS/Py.26.01.00/Unit/js/PyOS.Table.js"
  - "/Aliens/PyOS/Py.26.01.00/Unit/js/PyOS.Auth.js"
...

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Planning.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/PyOS_Engines.md"
require_once "Workflows/ACC/_Refs/PyOS_System.md"

# [03] Strict Scope Lock
- Is workflow me sirf planning generate hoti hai.
- Description me jo bola hai, plan me sirf wohi include karo.
- Extra engines / extra features / unrelated refactors plan karna forbidden.

# [04] Inputs
Targets:
- MUST be exactly ONE engine name (example: "PyOS.Cache")
- Comma-separated list => error (WF_TARGETS_INVALID)
- Extensions forbidden (".js", ".py", etc)

Description:
- Required human requirement
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Discovery (Read-Only, Best Effort)
1) Resolve latest PyOS version:
   - Scan: "/Aliens/PyOS/Py.*/"
   - Pick highest semantic-like version
   - Fallback: "/Aliens/PyOS/Py.26.00.00/"

2) Resolve expected JS engine path:
   - "/Aliens/PyOS/{LatestVersion}/unit/js/{EngineName}.js"

3) Optional related references (read-only):
   - Same engine Python counterpart (if exists):
     "/Aliens/PyOS/{LatestVersion}/Engine/{EngineName}.py"
   - Existing docs (if exists):
     "/Aliens/Docs/PyOS/Engine/JS/{EngineName}.md"
   - Similar engine examples (2-4) in "/Aliens/PyOS/{LatestVersion}/unit/js/"

4) Mode decision:
   - If engine file exists => Mode=UPDATE (no breaking changes)
   - If missing:
     - If manifest allows CreateMissingFiles for this workflow => Mode=CREATE
     - Else => Mode=BLOCKED (ModifyOnly); output plan should clearly state "Blocked: file missing"

# [06] Planning Output Template (Must Fill)
The plan file MUST contain these sections (with concrete bullets):

## A) Identity
- EngineName: {EngineName}
- Language: JS
- Mode: CREATE | UPDATE | BLOCKED
- Target Path: exact resolved path
- Related files (if any)

## B) Purpose & Boundaries
- Why this engine exists (system reason)
- Must Do / Must Not Do (hard boundaries)

## C) Public API Contract
- Export shape (global PyOS namespace + module fallback expectations)
- Method list (names + responsibility)
- Input/Output conventions:
  - Signature: (Payload, Context) where applicable
  - Error model: standardized error object

## D) Context & Multi-Tenant Rules
- Required context keys (Tenant/App/Brand/User as needed)
- Keying rules if storage/caching used
- How to avoid cross-tenant leakage

## E) Security Requirements
- Validation rules (Payload + Context)
- No secrets in logs
- Browser safety notes (prototype pollution, XSS surfaces)
- Safe defaults

## F) Performance Requirements
- Hot paths
- Sync vs async decisions
- Memory constraints and large payload behavior
- Batching patterns (if needed)

## G) Observability
- Logs/metrics/traces rules (what to emit, when)
- Debug toggles tied to manifest

## H) Acceptance Criteria
- Concrete pass/fail checks aligned with Description
- Backward compatibility statement (for UPDATE)

## I) Next Step Contract
- Code workflow must implement EXACTLY this plan
- Documentation workflow must reflect final code + plan (no contradictions)

# [07] Output Save Rule
Planning file path:
- "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/JS/{EngineName}.plan.md"

Default policy:
- ModifyOnly: if plan file missing => error (unless manifest explicitly allows creating planning outputs)

# [08] Done Criteria
- Plan saved (or explicit BLOCKED reason saved if allowed)
- All required sections filled
- No scope creep beyond Description
