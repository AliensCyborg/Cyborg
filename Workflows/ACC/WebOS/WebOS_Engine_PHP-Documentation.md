---
WorkflowId: WebOS_Engine_PHP-Documentation
WorkflowName: WebOS Engine PHP Documentation
Type: Workflow_Singular-Documentation
ParentWorkflowId: WebOS_Engine_PHP
Domain: WebOS
Category: Engines
Language: PHP
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-27
Inputs:
  - Targets
  - Description
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "/Aliens/manifest.json"
Output:
  DocsRoot: "/Aliens/Docs/WebOS"
  DocOutputRelPath: "Engine/PHP/{EngineBaseName}.md"
  SaveStrategy: "create-or-update"
Safety:
  NoDelete: true
  ApprovalRequired: false
...

# WebOS_Engine_PHP-Documentation

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## [00] Purpose
Is workflow ka kaam: WebOS PHP Engine ki **documentation generate/update** karna.

## [00B] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow the enterprise documentation contract from:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if anything is missing
- Security/Performance/Observability/Testing sections required

Examples:
- `Workflow("WebOS_Engine_PHP", "WebOS.Cache", "Docs + examples" )`
- `Workflow("WebOS_Engine_PHP", "WebOS.Troubleshoot", "Docs for new engine" )`

Output:
- `/Aliens/Docs/WebOS/Engine/PHP/{EngineBaseName}.md`

---

## [01] Target Normalization
Accepted Targets:
- `WebOS.Cache`
- `Cache` (normalized to `WebOS.Cache`)

Validation:
- EngineShortName must match: `^[A-Za-z][A-Za-z0-9]*$`

---

## [02] Discovery (Best-effort, no guessing)
Docs workflow SHOULD attempt to read:
1) Engine source:
   - `/Aliens/WebOS/{Version}/Engine/{EngineBaseName}.php`
2) EngineRouter rules:
   - `/Aliens/WebOS/{Version}/Boot/WebOS.EngineRouter.php`
3) Any existing docs file (if present) to preserve changelog.

If engine source missing:
- If this run is part of "new engine" creation, docs can still be generated as **spec-first**.
- It MUST clearly mark: "Engine source not found during discovery".

---

## [03] Documentation Output Path (Hard)
Docs MUST be saved under locked root:
- `/Aliens/Docs/WebOS/`

This workflow writes:
- `/Aliens/Docs/WebOS/Engine/PHP/{EngineBaseName}.md`

---

## [04] Documentation Template (Mandatory Sections)
Docs file MUST contain:
This workflow MUST follow the **19 mandatory sections** defined in `Workflow_Singular-Documentation`.

Title Block (Section-1) MUST explicitly include:
- Author: AlienCyborg
- Author URI / Website: [AlienCyborg.com](https://AlienCyborg.com)

Recommended (enterprise traceability):
- Owner/Team
- Status (Draft/Stable)
- Output Doc Path
- Source paths used (engine code + router + plan + callsites if any)

Additionally (WebOS Engine specific):
- Router Mapping
  - `WebOS::{EngineShortName}()` → `{ClassName}::Main($Payload,$Context)`
  - `WebOS::{EngineShortName}{Action}()` → `{ClassName}::{Action}($Payload,$Context)`

Hard rule:
- Docs MUST not describe a "Main-only" skeleton as success.
- Docs MUST align with Planning (feature blueprint + return-type matrix).

Rules:
- If something is unknown, write: "Not found in repo during discovery".
- Never include secrets in examples.

---

## [04.1] Enterprise Documentation Rules (Ultra)
User expectation: docs **enterprise grade** ho, aur kuch bhi miss na ho.

Docs MUST additionally:
- Include **complete Action index**:
  - list every `public static` action present in the engine file.
  - if repo discovery indicates extra `{EngineShortName}{Action}` call patterns, list them under "Known Call Patterns".
- Include payload calling patterns:
  - `WebOS::{EngineShortName}($arg1, $arg2, ...)` → `$Payload = [$arg1,$arg2,...]` (EngineRouter style)
  - show 2-3 variants for common actions (assoc array arg vs scalar arg)
- Include "Return-Type Matrix" (must):
  - For major actions (and any action discovered in callsites), specify expected return type:
    - bool | string | array envelope
  - If backward compatibility requires multiple shapes, explicitly document the rule.
- Include edge-case notes per critical action:
  - missing args
  - wrong types
  - empty strings
- Examples MUST be exhaustive enough:
  - minimum 5 overall examples stays,
  - plus: at least 1 example per major action category (Validate/Sanitize/Allow/RateLimit etc if present).

- Include "Feature Coverage" (must):
  - a structured checklist of topic features implemented by the engine
  - mapping back to Planning sections (No Jugaad baseline + feature blueprint)
- Dependencies section MUST list:
  - WebOS engines actually used (Security/Policy/Log/Cache/etc)
  - and what each dependency is used for

---

## [05] Minimal Success Criteria
- Docs file exists at:
  - `/Aliens/Docs/WebOS/Engine/PHP/{EngineBaseName}.md`
- Contains all mandatory sections.
