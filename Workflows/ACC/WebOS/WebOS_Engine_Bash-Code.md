---
# [ACC.WORKFLOW] WebOS Engine Bash - Code
WorkflowId: WebOS_Engine_Bash-Code
WorkflowName: WebOS Engine Bash Code
WorkflowType: Workflow_Singular-Code
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
    Example: "Add safe locking + idempotent behavior; keep existing flags"
Outputs:
  Code:
    Path: "/Aliens/WebOS/{LatestVersion}/Unit/bash/"
    Pattern: "{EngineName}.sh"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Boot.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Doctor.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Format.PHP.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Security.Scan.sh"
---

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Code.md"

# [02] Refs (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"

require_once "Workflows/ACC/_Refs/WebOS_Engines.md"
require_once "Workflows/ACC/_Refs/WebOS_System.md"
require_once "Workflows/ACC/_Refs/AliensStyle.md"
require_once "Workflows/ACC/_Refs/AliensGrade.md"

# [03] Purpose
Is workflow ka kaam: ek **single** Bash Engine ki `.sh` file generate/update karna,
WebOS ecosystem standards ke hisaab se.

This workflow MUST:
- Planning ko follow kare (agar plan file exist karti ho)
- Backward compatible rahe (agar existing engine update ho raha ho)
- Secure-by-default + idempotent + deterministic output de

# [04] Input Rules (Singular Target)
Targets must contain exactly one EngineName (no comma).
Rules:
- Trim required.
- Extension forbidden.
- Case preserve.
Invalid -> error.

EngineName = Targets.

# [05] Version + Output Path Resolution
Detect latest WebOS version:
- Prefer: `/Aliens/WebOS/PHP.*/` latest
- Fallback: `/Aliens/WebOS/PHP.26.00.00/`

Output file:
- `/Aliens/WebOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

Directory missing -> error (Modify only; do not create folders) unless manifest explicitly allows.

# [06] Inputs (Source of Truth)
Before writing code:
1) Planning file (if exists):
- `/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/Bash/{EngineName}.plan.md`

2) Existing `.sh` engine file (if exists):
- `/Aliens/WebOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

3) Optional reference examples (2-4):
- Similar Bash engines in `/Aliens/WebOS/{LatestVersion}/Unit/bash/`

If plan is missing:
- Generate minimal safe skeleton only.
- Do not add extra features beyond Description.

# [07] Code Output Requirements (Bash Standards)
## A) AliensStyle Header/Footer (Mandatory)
- File header must include:
  - EngineName, version, purpose, usage, safety highlights
- File footer must include:
  - Usage guide + troubleshooting pointers
(Exact template defined by AliensStyle ref.)

Mandatory template (must follow; do not improvise Author):

Header (top of file):
```
#!/usr/bin/env bash

# [ENGINE] {EngineName}
# Author: AlienCyborg
# WebOS: {LatestVersion}
# Purpose: <1-2 lines from Description/Plan>
# Safety: <no eval by default; strict quoting; secret-safe logs>
```

Footer (bottom of file):
```
# [FOOTER]
# Usage:
#   - <2-4 examples>
# Troubleshooting:
#   - <common failures + fixes>
# Changelog:
#   - <date> <summary>
```

Hard gates (must):
- Header `Author` must be exactly `AlienCyborg`
- Major functions must have comment code-headers (Purpose/Input/Output)
- If plan exists OR Description is non-empty: minimal skeleton output is forbidden (must be enterprise-grade for the requested scope)

Additional hard gates (must):
- Script must contain >= 3 section markers using this exact pattern: `# [SECTION] <NAME>`
  - Minimum recommended: `# [SECTION] CONFIG`, `# [SECTION] FUNCTIONS`, `# [SECTION] MAIN`
- Forbidden cross-language tokens:
  - `$echo` must not appear anywhere (PHP leakage)
  - `d()` must not appear anywhere (debug leakage)

## B) Security (Non-Negotiable)
- Never use `eval` unless Description explicitly demands (default: forbidden).
- Always quote variables.
- Avoid untrusted input execution.
- Never print secrets; mask sensitive values.
- Use safe temp files (mktemp) and cleanup via trap.
- Use predictable PATH handling; avoid implicit `cd` without checks.

## C) Reliability + Idempotency
- Re-run safe: running script multiple times should not break state.
- Use locks for concurrency if applicable (flock / lockfile pattern).
- Explicit exit codes; consistent error messages.

## D) Deterministic Logs
- If debug enabled via manifest, logs can be verbose.
- If not, logs minimal.
- Include RunId if available.

## E) Performance
- Avoid expensive file scans / network calls unless required.
- Use batch operations where possible.

# [08] Update vs New Behavior
If file exists:
- Preserve important behavior + public flags.
- Only change what Description and/or plan explicitly requires.
- No breaking change unless Description explicitly says and manifest allows.

If file missing:
- Create new engine with minimal stable interface.
- Include safe defaults; no extra features.

# [09] Approval Gate
Use manifest:
- Manual mode: stop before writing code and request approval.
- Auto mode: proceed with full validation + logs.

# [10] Post-Write Record (Required)
Write run log:
- `/Aliens/.Alien/{Developer_Username}/Task/Logs/ACC/WebOS_Engine_Bash-Code/{RunId}.log.md`

Include:
- changedFiles, checksum, outcome, any approvals.

# [11] Scope Control (Critical)
- Description me jo bola hai, **sirf utna** implement karna.
- Extra helpers, extra files, extra refactors mat karna.
