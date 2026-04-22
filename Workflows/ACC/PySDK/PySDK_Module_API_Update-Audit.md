---
WorkflowId: PySDK_Module_API_Update-Audit
WorkflowName: PySDK Module API Update (Audit)
Type: Workflow_Singular-Audit
ParentWorkflowId: PySDK_Module_API_Update
Domain: PySDK
TargetKind: "Module/API"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Targets
  - Description
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/"
    FileName: "{ApiName}.update.audit.md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only audit; API file must exist
  ProdWrite: "no"              # audit writes only run record + deterministic artifact
  DbTouch: "not-required"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Audit.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "Workflows/ACC/_Refs/PySDK.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Products.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/ProductNew.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product_Specifications.py"
...

# PySDK_Module_API_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: **enterprise-grade audit** of an existing PySDK Module API before update.

Output (run-scoped):
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- Also write a stable copy for downstream steps:
  - `/Aliens/.Alien/{Developer}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on a run-scoped audit path.

---

## [01] Targets
Accepted targets:
- `Module/ApiName`
- Module-only batch mode: `Module`

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Any table names discovered during audit should be validated as singular (report violation).
- If violated => `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Forbidden:
- `.py` extension in Targets
- Path traversal (`..`)

---

## [02] Resolve paths (ACC.Paths compliant)
Resolve Version: latest (fallback `Py.26.00.00`).

Paths:
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{ApiFile}   = {ModuleRoot}/api/{ApiName}.py`
- `{ModuleClass} = {ModuleRoot}/py/index.py`
- `{ModuleSql}   = {ModuleRoot}/sql/index.sql`

Hard rule:
- If `{ApiFile}` missing (per ApiName target) => fail with `WF_TARGET_MISSING`.

Batch mode:
- If module-only, enumerate `{ModuleRoot}/api/*.py` (non-recursive). If none => Completed(NoTargets).

---

## [03] Audit scope (Hard)
Audit MUST cover (minimum):

### A) Update-only safety
- No create policy respected (target must exist)
- No destructive intent implied by Description unless manifest approval explicitly allows

### B) PySDK API baseline compliance
- Routed URL examples (if present) MUST be `https://base.software/?api=<ApiName>&...`
- Response envelope stability (best-effort check): `s,t,c,o,paging,ok,code,trace`
- `$echo` must never leak into public response
- Logging pattern uses `d()` (not `x()`)

### C) Glue-only policy
In `{ApiFile}`:
- No `function` declarations
- No `class` declarations
- No SQL execution or raw query strings

### D) Dependencies audit (best-effort)
- `{ModuleClass}` exists (expected)
- `{ModuleSql}` exists (schema allowlists)

If missing dependencies => report as `High` (DependencyMissing) with exact paths.

### E) AliensStyle / enterprise quality (User Reported Issues - 10)
Audit MUST explicitly report Pass/Fail for:
1) CodeHeader `Author` must be exactly: `AlienCyborg` (where applicable)
2) CodeFooter present (where applicable)
3) Function-level headers present in touched module class methods (Purpose/Input/Output)
4) SECTION dividers present for module class public methods
5) `$echo` + `d()` usage is correct and consistent (no `x()`)
6) Comments are meaningful + structured (no noise)
7) AliensStyle is applied (minimum checklist pass)
8) Output is enterprise-grade (no tiny/skeleton updates when plan requires more)
9) Single-return rule enforced in touched class methods (exactly one return at end)
10) Function code-header includes Dependencies (engines/modules/helpers)

Note:
- API files must not contain local functions/classes; function-level headers apply to module class methods when API requires logic changes.

---

## [04] Output template (Mandatory)
Audit.md MUST include (order fixed):
1) Header (Target, Version, RunId/RunItemId)
2) Resolved paths
3) Summary score (Pass/Fail + risk)
4) Findings (Critical/High/Medium/Low)
5) Required fix list (checklist)
6) Non-destructive notes (what must NOT change)
7) Approval note (if ApprovalMode=manual)

Hard rule:
- The Required Fix List MUST include `User Reported Issues (8)` with per-item Pass/Fail + fix.

---

## [05] Minimal success criteria
- Audit.md exists at run record path.
- Deterministic `{ApiName}.update.audit.md` exists at Planning path.
