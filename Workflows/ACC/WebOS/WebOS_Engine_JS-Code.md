---
WorkflowId: WebOS_Engine_JS-Code
Type: Workflow_Singular-Code
Domain: WebOS
Category: Engines
Language: JS
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflow: WebOS_Engine_JS
Purpose: >
  Generate or update a single WebOS JS engine file under WebOS unit/js, using the plan (if present)
  and strictly following AliensStyle + AliensGrade rules. Must be secure-by-default and multi-tenant ready.
  NOTE: If the target engine already exists and you are performing an enterprise update,
  use the Update pipeline (Audit → UpdatePlan → UpdateCode → UpdateDocs) to avoid needing
  a separate run via WebOS_Engine_JS_Update.
Invocation:
  Signature: Workflow("WebOS_Engine_JS-Code", "WebOS.Cache", "Human language requirement")
  TargetsMeaning: >
    Single engine name without extension. Comma list is invalid (singular workflow).
  DescriptionMeaning: >
    Constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Code:
    Path: "/Aliens/WebOS/{LatestVersion}/unit/js/"
    Pattern: "{EngineName}.js"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/AliensStyle.md
  - Workflows/ACC/_Refs/AliensGrade.md
Notes:
  - Production code directory: /Aliens/WebOS/.../unit/js/
  - ModifyOnly default: if engine file missing => error unless manifest allows create.
  - Must never delete/remove important features unless explicitly instructed.
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
require_once "Workflows/ACC/_Common/Workflow_Singular-Code.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/WebOS_Engines.md"
require_once "Workflows/ACC/_Refs/WebOS_System.md"
require_once "Workflows/ACC/_Refs/AliensStyle.md"
require_once "Workflows/ACC/_Refs/AliensGrade.md"

# [03] Strict Scope Lock
- Jo Description + Plan me define ho, sirf wohi implement karo.
- Extra helpers / extra refactors / extra exports add karna forbidden.
- Existing features remove karna forbidden (unless explicitly requested).

# [04] Inputs
Targets:
- MUST be exactly ONE engine name (example: "WebOS.Cache")
- Comma-separated list => error (WF_TARGETS_INVALID)
- Extensions forbidden (".js", ".php", etc)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"

Minimum checks:
- CanEditProdCode must be true to write under "/Aliens/WebOS/"
- Approval policy:
  - manual => approval required before writing prod code
  - auto   => proceed
If permission missing => fail fast (WF_PERMISSION_DENIED)

# [06] Resolve Latest Version + Output Path
1) Resolve latest WebOS version folder:
   - Scan: "/Aliens/WebOS/PHP.*/" pick latest
   - Fallback: "/Aliens/WebOS/PHP.26.00.00/"

2) Output path:
  - "/Aliens/WebOS/{LatestVersion}/unit/js/{EngineName}.js"

3) Directory must exist:
   - Missing directory => error (WF_PATH_RESOLUTION_FAILED)
   - ModifyOnly default => do not create directories here

# [07] Plan-Driven Generation
Plan file (optional but preferred):
- "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/JS/{EngineName}.plan.md"

Rules:
- If plan exists: implement plan exactly (no contradictions)
- If plan missing: minimal safe implementation; do NOT invent big scope

# [08] Update vs Create Rules
If engine file exists => UPDATE:
- Preserve public API contract (method names, expected shapes)
- Preserve important features
- Improve safety/performance only where Description/Plan demands

If engine file missing => CREATE:
- Default: error (ModifyOnly)
- If manifest explicitly allows CreateMissingFiles=true for this workflow:
  - Create file with AliensStyle header/footer + required exports

# [09] JS Engine Code Requirements (Non-Negotiable)
## A) Export / Namespace
Engine must be usable in browser-first WebOS runtime:
- Attach to global WebOS namespace:
  - `WebOS["Cache"]` or `WebOS.Cache` (consistent with ecosystem)
- Provide module fallback (CommonJS) when possible without breaking browser build.

## B) Standard Signatures
Methods should follow:
- (Payload, Context) signature where meaningful
- Context must support multi-app + multi-tenant

## C) Error Model
- Standard error object with:
  - ErrorCode
  - Message (safe)
  - Details (optional, debug-only)
- Never log secrets

## D) Security by Default
- Validate Payload + Context
- Guard against:
  - prototype pollution (Object.create(null), hasOwnProperty checks, safe merges)
  - XSS surfaces when rendering strings
  - unsafe eval / Function() forbidden

## E) Performance
- Avoid expensive loops on hot paths
- Prefer O(1) key access patterns
- Avoid unnecessary deep clones

## F) Observability
- Respect manifest debug flags for logging
- Prefer WebOS.System logging utilities if defined in refs

## G) AliensStyle + AliensGrade
- Header/Footer mandatory
- Clean sections
- Usage guide in footer
- No Hindi script; use Hinglish (Romanized) where needed

Hard anti-regression rule:
- Existing engines may contain legacy author names (e.g. A’nil Nayak). Do NOT copy.
- On CREATE and UPDATE, set header `Author` exactly to `AlienCyborg`.

Footer (mandatory; deterministic):
- Must include ALL of:
  - `Usage Guide`
  - `Troubleshooting`
  - `Notes` (security/logging)
  - `Change Log`

Function code-header (mandatory; deterministic):
- Every exported/public method must begin with JSDoc:
```js
/**
 * Purpose : <one line>
 * Input   : <Payload/Context shape>
 * Output  : <return shape + error envelope>
 */
```

Sections (mandatory; deterministic):
- Minimum sections expected:
  - `[SECTION] Safe Globals`
  - `[SECTION] Validation + Error Model`
  - `[SECTION] Public API`
  - `[SECTION] Helpers`
  - `[SECTION] Footer`

Logging + $echo/d rule:
- `$echo` is PHP-only. Do NOT use `$echo` in JS engines.
- `d()` is PHP/SDK logging. In JS engines, avoid `console.*` spam.
- If debug logs are needed, implement a `Debug()` helper gated by a flag (OFF by default).

Hard gates (must):
- Header `Author` must be exactly `AlienCyborg`
- Every exported/public method must start with a JSDoc code-header (Purpose/Input/Output)
- If plan exists OR Description is non-empty: minimal skeleton output is forbidden (must be enterprise-grade for the requested scope)

# [10] Reference Examples (Must Read)
Before writing, read 2-4 existing JS engines:
- "/Aliens/WebOS/{LatestVersion}/unit/js/WebOS.*.js"

Goal:
- Match ecosystem patterns, naming, and conventions.

# [11] Output Write Rules
- Write code ONLY to the resolved output path
- Do not touch other engines/files unless explicitly required
- If file is updated:
  - Keep formatting consistent
  - Keep existing comments that are meaningful

# [12] Done Criteria
- Engine file written/updated at output path
- Passes AliensStyle + AliensGrade checks
- No scope creep beyond Plan/Description
