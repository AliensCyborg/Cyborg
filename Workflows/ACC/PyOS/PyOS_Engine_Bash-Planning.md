---
# [ACC.WORKFLOW] PyOS Engine Bash - Planning
WorkflowId: PyOS_Engine_Bash-Planning
WorkflowName: PyOS Engine Bash Planning
WorkflowType: Workflow_Singular-Planning
Domain: PyOS
Category: Engines
Language: Bash
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
ParentWorkflowId: PyOS_Engine_Bash

Args:
  - Name: Targets
    Type: string
    Required: true
    Example: "PyOS.Cache"
  - Name: Description
    Type: string
    Required: false
    Example: "Idempotent deploy helper; no secrets in logs; safe reruns"
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/"
    Pattern: "{EngineName}.plan.md"
ExampleFiles:
  - "/Aliens/PyOS/Py.26.01.00/Unit/bash/PyOS.Boot.sh"
  - "/Aliens/PyOS/Py.26.01.00/Unit/bash/PyOS.Doctor.sh"
  - "/Aliens/PyOS/Py.26.01.00/Unit/bash/PyOS.Format.py.sh"
  - "/Aliens/PyOS/Py.26.01.00/Unit/bash/PyOS.Security.Scan.sh"
---

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Planning.md"

# [02] Refs (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/PyOS_Engines.md"
require_once "Workflows/ACC/_Refs/PyOS_System.md"

# [03] Purpose
Is workflow ka kaam: ek **single** Bash Engine ke liye planning banana, taaki Code workflow
ko exact direction mil sake (scope, boundaries, IO, security, reliability).

This workflow is planning-only:
- No code writes in PyOS engine locations
- No docs writes
- Only plan file write/update to Planning folder (manifest rules apply)

# [04] Input Normalization (Singular Target)
Targets must contain exactly one EngineName (no comma).
Rules:
- Trim required.
- Extension forbidden (".sh" not allowed).
- Case preserve (strict naming).
- If Targets invalid -> error.

EngineName = Targets (as-is, after trim).

# [05] Discovery (Read-Only, Best Effort)
Read and infer only; do not write in discovery phase.

1) Detect latest PyOS version:
- Prefer: `/Aliens/PyOS/Py.*/` latest
- Fallback: `/Aliens/PyOS/Py.26.00.00/`

2) Check existing Bash engine file:
- `/Aliens/PyOS/{LatestVersion}/Unit/bash/{EngineName}.sh`

3) Check related engine references (optional):
- Related Python engine (if exists):
  `/Aliens/PyOS/{LatestVersion}/Engine/{EngineName}.py`
- Existing docs (if exists):
  `/Aliens/Docs/PyOS/Engines/Bash/{EngineName}.md`
- Existing plan (if exists):
  `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/{EngineName}.plan.md`

Outcome classification:
- If `.sh` exists => UPDATE plan (backward compatible, minimal risk)
- If `.sh` missing => NEW plan (skeleton + safe defaults)

# [06] Plan Template (Must Produce)
Plan file MUST include these sections (filled, not empty):

## A) Overview
- EngineName
- What problem it solves
- Responsibilities (MUST DO) / Non-goals (MUST NOT DO)

## B) Entry + Invocation
- How it is called (from which workflow / which PyOS automation)
- Expected command-line usage pattern
- Required environment variables (if any) and defaults

## C) Inputs / Outputs
- Inputs: args, env vars, files
- Outputs: files changed, stdout/stderr contract, exit codes
- Error model: standard exit codes + message format

## D) Safety Defaults (Shell-Specific)
- No `eval` / no unsafe dynamic execution
- Quote all variables
- Strict mode policy (when to use `set -euo pipefail`)
- `IFS` safety
- Temp file handling (mktemp) and cleanup traps
- Secret handling: never echo tokens/keys; mask if needed

## E) Idempotency + Determinism
- Re-run safe behavior
- Avoid non-deterministic outputs
- Locking strategy (if concurrent runs possible)

## F) Performance + IO
- Minimize disk + network IO
- Avoid expensive recursive searches unless explicitly required

## G) Observability
- Logging levels tied to manifest/debug flags
- What to log (and what not to log)
- RunId correlation

## H) Compatibility
- Backward compatibility notes (if updating existing engine)
- Constraints: keep existing flags/outputs stable unless Description explicitly says change

## I) Files to Write
- Code output path: `/Aliens/PyOS/{LatestVersion}/Unit/bash/{EngineName}.sh`
- Docs output path: `/Aliens/Docs/PyOS/Engines/Bash/{EngineName}.md`
- Plan output path: `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/{EngineName}.plan.md`

# [07] Plan Save Rule
Write/update plan file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/Bash/{EngineName}.plan.md`

Modify-only policy:
- If plan folder/file missing -> follow manifest:
  - If manifest allows createPlanning=true -> create
  - Else -> error (do not invent structure)

# [08] Scope Control (Critical)
- Description me jo bola hai, sirf wohi plan me include karna.
- Extra features, extra files, extra refactors mat add karna.

# [09] Done Criteria
- Plan saved
- Clear NEW vs UPDATE decision
- Contains explicit boundaries + safety defaults + exit-code model
