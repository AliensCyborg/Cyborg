---
WorkflowId: PyOS_Engine_JS-Documentation
Type: Workflow_Singular-Documentation
Domain: PyOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflow: PyOS_Engine_JS
Purpose: >
  Generate/update documentation for a single PyOS JS engine based on the final code (source of truth)
  and the plan (if present). Documentation must be consistent, accurate, and scoped to the request.
  NOTE: For existing-engine enterprise updates, use PyOS_Engine_JS_Update-Documentation so docs reflect
  the audit findings + update plan, without requiring a separate top-level workflow run.
Invocation:
  Signature: Workflow("PyOS_Engine_JS-Documentation", "PyOS.Cache", "Human language requirement")
  TargetsMeaning: >
    Single engine name without extension. Comma list is invalid (singular workflow).
  DescriptionMeaning: >
    Emphasis/constraints for docs only. Must not expand scope.
Outputs:
  Documentation:
    Path: "/Aliens/Docs/PyOS/Engine/JS/"
    Pattern: "{EngineName}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
Notes:
  - Docs must reflect real code; no fake APIs.
  - ModifyOnly default: if docs file missing => error unless manifest allows create.
...

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/PyOS_Engines.md"
require_once "Workflows/ACC/_Refs/PyOS_System.md"

# [03] Strict Scope Lock
- Docs me sirf wohi cover karo jo actual code me exist karta hai.
- Description me jo emphasis bola hai, docs ka focus wohi ho.
- Extra features / imaginary APIs add karna forbidden.

# [03A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if code/plan info missing
- Security/Performance/Observability/Testing sections required

# [04] Inputs
Targets:
- MUST be exactly ONE engine name (example: "PyOS.Cache")
- Comma-separated list => error (WF_TARGETS_INVALID)
- Extensions forbidden

Description:
- Required emphasis (e.g., "explain TTL + multi-tenant keys")
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Source of Truth (Read First)
1) Resolve latest PyOS version:
   - Scan "/Aliens/PyOS/Py.*/" pick latest
   - Fallback "/Aliens/PyOS/Py.26.00.00/"

2) Read code file (primary):
   - "/Aliens/PyOS/{LatestVersion}/unit/js/{EngineName}.js"
If missing:
- Generate "Spec-Only" doc:
  - Mention code missing
  - Include only intended contract from plan (if exists)
  - Then STOP (no API details invented)

3) Optional plan file:
   - "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/JS/{EngineName}.plan.md"

4) Optional previous docs (if exists):
  - "/Aliens/Docs/PyOS/Engine/JS/{EngineName}.md"

# [06] Documentation Structure (Must)
Docs file MUST include:

## 0) Title Block (Hard)
Title Block (Section-1) MUST explicitly include:
- Author: AlienCyborg
- Author URI / Website: [AlienCyborg.com](https://AlienCyborg.com)

Recommended (enterprise traceability):
- Owner/Team
- Status (Draft/Stable)
- Output Doc Path
- Source paths used (engine code + plan)

## 1) Summary
- EngineName
- Purpose (1-3 lines)
- Where it lives (exact path)

## 2) Integration
- How PyOS loads/uses this engine (high-level)
- Global namespace / module export behavior

## 3) Public API
- Method list (exact names from code)
- Signatures (Payload, Context) where applicable
- Return shapes
- Error model (ErrorCode list if present)

## 4) Context & Multi-Tenancy
- Required context keys
- Keying rules if storage/caching used
- Data isolation guarantees

## 5) Security Notes
- Validation requirements
- Sensitive data handling / logging rules

## 6) Performance Notes
- Hot paths
- Complexity notes
- Any caching/TTL behavior

## 7) Examples
- 2-4 practical examples:
  - ACC invocation example (Workflow call that generates/updates this engine)
  - Runtime usage example (how app/module would call PyOS engine)
Rules:
- Examples must match actual code methods (no fake)

## 8) Change Log (Minimal)
- LastUpdated date
- "CREATE" vs "UPDATE" (from plan or diff summary)
- Backward compatibility note (if UPDATE)

# [07] Output Path + Save Rules
Docs output path:
- "/Aliens/Docs/PyOS/Engine/JS/{EngineName}.md"

Default policy:
- ModifyOnly: if docs file missing => error
- If manifest allows CreateDocs=true => create file

Directory missing => error (do not create dirs here).

# [08] Done Criteria
- Doc saved/updated at output path
- No contradictions with code
- No scope creep beyond Description
