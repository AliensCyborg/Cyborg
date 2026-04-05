---
WorkflowId: WebOS_Engine_JS-Planning
Type: Workflow_Singular-Planning
Domain: WebOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflow: WebOS_Engine_JS
Purpose: >
  Produce a single JS engine plan for a single target engine name. The plan must be used
  by WebOS_Engine_JS-Code to generate/update the engine deterministically, without inventing scope.
  NOTE: If the target engine already exists, prefer the Update pipeline:
  - WebOS_Engine_JS_Update-Audit → WebOS_Engine_JS_Update-Planning (".update.plan.md")
  This avoids re-running both WebOS_Engine_JS and WebOS_Engine_JS_Update.
Invocation:
  Signature: Workflow("WebOS_Engine_JS-Planning", "WebOS.Cache", "Human language requirement")
  TargetsMeaning: >
    Single engine name without extension. Comma list is invalid (singular workflow).
  DescriptionMeaning: >
    Constraints, acceptance criteria, and change intent. If empty/missing -> fail fast.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/"
    Pattern: "{EngineName}.plan.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
Notes:
  - Planning is read-mostly; no production code changes here.
  - If engine file exists => plan is "Update" (no breaking changes).
  - If engine file missing => behavior depends on manifest (ModifyOnly default).
  - For existing engines, Update planning requires the deterministic audit artifact (".update.audit.md").
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Loader.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Cache.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Table.js"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/js/WebOS.Auth.js"
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
require_once "Workflows/ACC/_Refs/WebOS_Engines.md"
require_once "Workflows/ACC/_Refs/WebOS_System.md"

# [03] Strict Scope Lock
- Is workflow me sirf planning generate hoti hai.
- Description me jo bola hai, plan me sirf wohi include karo.
- Extra engines / extra features / unrelated refactors plan karna forbidden.

# [04] Inputs
Targets:
- MUST be exactly ONE engine name (example: "WebOS.Cache")
- Comma-separated list => error (WF_TARGETS_INVALID)
- Extensions forbidden (".js", ".php", etc)

Description:
- Required human requirement
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Discovery (Read-Only, Best Effort)
1) Resolve latest WebOS version:
   - Scan: "/Aliens/WebOS/PHP.*/"
   - Pick highest semantic-like version
   - Fallback: "/Aliens/WebOS/PHP.26.00.00/"

2) Resolve expected JS engine path:
   - "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"

3) Optional related references (read-only):
   - Same engine PHP counterpart (if exists):
     "/Aliens/WebOS/{LatestVersion}/Engine/{EngineName}.php"
   - Existing docs (if exists):
     "/Aliens/Docs/WebOS/Engine/JS/{EngineName}.md"
   - Similar engine examples (2-4) in "/Aliens/WebOS/{LatestVersion}/unit/js/"

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
- Export shape (global WebOS namespace + module fallback expectations)
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
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.plan.md"

Default policy:
- ModifyOnly: if plan file missing => error (unless manifest explicitly allows creating planning outputs)

# [08] Done Criteria
- Plan saved (or explicit BLOCKED reason saved if allowed)
- All required sections filled
- No scope creep beyond Description
