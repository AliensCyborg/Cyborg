---
# [ACC.WORKFLOW] WebOS Engine Bash - Documentation
WorkflowId: WebOS_Engine_Bash-Documentation
WorkflowName: WebOS Engine Bash Documentation
WorkflowType: Workflow_Singular-Documentation
Domain: WebOS
Category: Engines
Language: Bash
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflowId: WebOS_Engine_Bash

Args:
  - Name: Targets
    Type: string
    Required: true
    Example: "WebOS.Cache"
  - Name: Description
    Type: string
    Required: false
    Example: "Focus on safety + exit codes + examples"
Outputs:
  Documentation:
    Path: "/Aliens/Docs/WebOS/Engines/Bash/"
    Pattern: "{EngineName}.md"
---

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] Refs (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/WebOS_Engines.md"
require_once "Workflows/ACC/_Refs/WebOS_System.md"

# [03] Purpose
Is workflow ka kaam: ek **single** Bash Engine ki documentation generate/update karna.
Docs always code + plan ke aligned honi chahiye.

# [03A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if anything is missing
- Security/Performance/Observability/Testing sections required

# [04] Input Rules (Singular Target)
Targets must contain exactly one EngineName (no comma).
Rules:
- Trim required.
- Extension forbidden.
- Case preserve.
Invalid -> error.

EngineName = Targets.

# [05] Sources (Read Before Writing Docs)
1) Planning (if exists):
- `/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/Bash/{EngineName}.plan.md`

2) Code (must exist for accurate docs):
- Detect latest WebOS version:
  - Prefer: `/Aliens/WebOS/PHP.*/` latest
  - Fallback: `/Aliens/WebOS/PHP.26.00.00/`
- Code path:
  `/Aliens/WebOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

If code missing:
- Write "Spec-Only (Code missing)" and stop after spec sections (no fake details).

# [06] Output Path
- `/Aliens/Docs/WebOS/Engines/Bash/{EngineName}.md`

Directory missing -> error (Modify only) unless manifest allows createDocs=true.

# [07] Documentation Coverage (Engine-Specific)
Doc MUST follow the **19 mandatory sections** defined in `Workflow_Singular-Documentation`.

Title Block (Section-1) MUST explicitly include:
- Author: AlienCyborg
- Author URI / Website: [AlienCyborg.com](https://AlienCyborg.com)

Recommended (enterprise traceability):
- Owner/Team
- Status (Draft/Stable)
- Output Doc Path
- Source paths used (script path + plan)

Additionally, ensure coverage includes:
## A) Summary
- EngineName, version, purpose
- When to use / when not to use

## B) File Location
- Exact code path (versioned)
- Related planning path
- Related workflows that generate it

## C) Usage
- Basic invocation examples (2-4)
- Required environment variables (if any)
- Expected stdout/stderr format

## D) Inputs / Outputs
- Inputs: args + env + files
- Outputs: files changed, exit codes
- Error model (exit codes + messages)

## E) Security Notes (Bash-Specific)
- Quoting, no eval, temp files, traps
- Secret handling guidelines

## F) Idempotency + Reliability
- Re-run behavior
- Locking / concurrency notes

## G) Performance Notes
- IO, time complexity hints

## H) Troubleshooting
- Common failures + fixes (no generic fluff)
- Safe debug steps (respect manifest)

## I) Changelog (Minimal)
- Date + summary + compatibility note

# [08] Scope Control (Critical)
- Description me jo focus bola hai, docs me emphasis wohi ho.
- Extra features, extra sections mat add karo unless engine needs them.

# [09] Done Criteria
- Docs updated
- Aligned with actual code + plan
- No contradictions
