---
WorkflowId: WebOS_Engine_Bash_Update-Code
WorkflowName: WebOS Engine Bash Update (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: WebOS_Engine_Bash_Update
Domain: WebOS
Scope: "Engine -> Bash (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Targets: "Single Engine (update). Example: WebOS.Cache"
  - Description: "Human update requirement"
Outputs:
  - Code: "/Aliens/WebOS/{LatestVersion}/Unit/bash/{EngineName}.sh"
  - RunRecord: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "/Aliens/manifest.json"
Quality:
  MustFollow:
    - AliensStyle
    - Shell safety rules (no eval by default, strict quoting)
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only
  ProdWrite: true
  DbTouch: "not-required"
Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "ProdWrite && manifest.ApprovalMode == manual"
    - "Audit reported Critical gaps"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Boot.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Doctor.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Format.PHP.sh"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/bash/WebOS.Security.Scan.sh"
---

# WebOS_Engine_Bash_Update-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Purpose
Is workflow ka kaam: existing WebOS Bash engine file ko **enterprise-grade update** karna.

Hard constraints:
- Target engine file MUST already exist.
- Only the single target `.sh` engine file may be modified.
- No feature loss; backward compatibility preserved.

---

## [01] Inputs & Evidence
Before writing, workflow MUST read (best-effort):
- Existing engine file
- Update plan: `{EngineName}.update.plan.md`
- Deterministic Audit Artifact: `{EngineName}.update.audit.md`

If plan/audit missing:
- Fail fast (update process integrity).

---

## [02] Update Strategy (Hard)
The update MUST:
1) Fix audit-detected AliensStyle gaps (Header/Footer/Sectioning/logging guard)
2) Fix shell safety gaps (quoting, mktemp+trap, no eval, PATH/cd safety)
3) Improve idempotency + determinism (locks, stable outputs)
4) Preserve existing flags/options and output contracts
5) Implement requested changes (bug/perf/security)

Allowed:
- Large diffs inside the single file, if needed for enterprise completeness.

Forbidden:
- Removing existing public flags/options (unless explicitly requested)
- Writing to other engine files
- Writing to shared core files

---

## [03] Verification Gates (Must)
- Engine file exists at `/Aliens/WebOS/{LatestVersion}/Unit/bash/{EngineName}.sh`
- Header/Footer blocks present
- No secrets in logs/examples
- Explicit exit-code model present and consistent
- Idempotency notes or behavior implemented

Additional hard gates (must):
- Header `Author` must be exactly `AlienCyborg`
- Footer must include usage + troubleshooting + update notes
- Major functions must have comment code-headers (Purpose/Input/Output)
- Sections must be consistent and readable
- Logging/output must be deterministic and debug-flag driven (no noisy default)
- Comments must be meaningful (why/contract) and AliensStyle-compliant
- Script must be enterprise-grade when plan/description requires it (locking/idempotency + safety helpers + stable exit-code model)

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
# Update Notes:
#   - <what changed + why>
# Changelog:
#   - <date> <summary>
```

Additional hard gates (must):
- Script must contain >= 3 section markers using this exact pattern: `# [SECTION] <NAME>`
  - Minimum recommended: `# [SECTION] CONFIG`, `# [SECTION] FUNCTIONS`, `# [SECTION] MAIN`
- Forbidden cross-language tokens:
  - `$echo` must not appear anywhere (PHP leakage)
  - `d()` must not appear anywhere (debug leakage)

---

## [04] Minimal Success Criteria
- Target engine file updated.
- Audit items addressed (or explicitly deferred with justification in docs/plan).
- No unrelated files modified.
