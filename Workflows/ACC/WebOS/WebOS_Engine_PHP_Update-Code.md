---
WorkflowId: WebOS_Engine_PHP_Update-Code
WorkflowName: WebOS Engine PHP Update (Code)
Type: Workflow_Singular-Code
ParentWorkflowId: WebOS_Engine_PHP_Update
Domain: WebOS
Scope: "Engine -> PHP (Update)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Targets: "Single Engine (update). Example: WebOS.Cache"
  - Description: "Human update requirement"
Outputs:
  - Code: "/Aliens/WebOS/PHP.{Latest}/Engine/{EngineBaseName}.php"
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
    - EngineRouter v7.0 compliance
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
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Account.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Agent.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.API.php"
  - "/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Cache.php"
...

# WebOS_Engine_PHP_Update-Code

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [00] Purpose
Is workflow ka kaam: existing WebOS PHP engine file ko **enterprise-grade update** karna.

Hard constraints:
- Target engine file MUST already exist.
- Only the single target engine file may be modified.
- No feature loss; backward compatibility preserved.

---

## [01] Inputs & Evidence
Before writing, workflow MUST read (best-effort):
- Existing engine file
- Update plan: `{EngineBaseName}.update.plan.md`
- Deterministic Audit Artifact: `{EngineBaseName}.update.audit.md`

If plan/audit missing:
- Fail fast (update process integrity).

---

## [02] Update Strategy (Hard)
The update MUST:
1) Fix audit-detected AliensStyle gaps (CodeHeader/Footer/Sections/logging guard)
2) Ensure EngineRouter compliance (static Main + action signatures)
3) Close coverage gaps discovered from repo call patterns
4) Implement requested changes (bug/perf/security)

Allowed:
- Large diffs inside the single file, if needed for enterprise completeness.

Forbidden:
- Main-only/skeleton changes
- Removing existing public actions (unless explicitly requested)
- Editing Router/Boot core files

---

## [03] Verification Gates (Must)
- Engine file exists at `/Aliens/WebOS/{Version}/Engine/{EngineBaseName}.php`
- `php -l` passes
- AliensStyle checklist satisfied (Dependencies + Footer present)
- Public methods have section headers + internal code-headers
- No secrets in logs/examples

Additional hard gates (must):
- File CodeHeader `Author` must be exactly `AlienCyborg`
- File CodeFooter must exist and include update notes + compatibility notes
- Every public method must start with internal code-header (Purpose/Input/Output)
- `$echo` + `x(...)` must be used per WebOS convention AND guarded (`function_exists`) with fallback (no fatal assumptions)
- Comments must be meaningful (no noise) and AliensStyle-compliant
- Engine must be enterprise-grade: include required helpers + coverage inventory implementation (no missing actions discovered in planning)

---

## [04] Minimal Success Criteria
- Target engine file updated.
- Audit items addressed (or explicitly deferred with justification in docs/plan).
- No unrelated files modified.
