---
WorkflowId: WebSDK_Module_SQL_Update-Audit
WorkflowName: WebSDK Module SQL Update (Audit)
Type: Workflow_Singular-Audit
ParentWorkflowId: WebSDK_Module_SQL_Update
Domain: WebSDK
TargetKind: "Module/SQL"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-31
Inputs:
  - Targets
  - Description
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/"
    FileName: "index.update.audit.md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only audit; sql/index.sql must exist
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
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.sql"
---

# WebSDK_Module_SQL_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: **enterprise-grade audit** of an existing WebSDK module SQL file (`sql/index.sql`) before update.

Output (run-scoped):
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- Also write a stable copy for downstream steps:
  - `/Aliens/.Alien/{Developer}/Planning/WebSDK/Module/{Module}/SQL/index.update.audit.md`

Reason (hard):
- Child workflows have different `{RunId}/{RunItemId}`; Planning/Code/Docs must not rely on a run-scoped audit path.

---

## [01] Targets
Accepted targets:
- `ModuleName` (single module per singular call)

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Any table names discovered during audit should be validated as singular (report violation).
- If violated => `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Forbidden:
- paths (`/`), extensions, path traversal (`..`)

---

## [02] Resolve paths (ACC.Paths compliant)
Resolve Version: latest (fallback `PHP.26.00.00`).

Paths:
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- `{SqlFile}    = {ModuleRoot}/sql/index.sql`

Hard rule:
- If `{SqlFile}` missing => fail with `WF_TARGET_MISSING` (update-only).

---

## [03] Audit scope (Hard)
Audit MUST cover (minimum):

### A) Update-only safety
- No-create policy respected (sql/index.sql must exist)
- No destructive intent implied by Description unless manifest approval explicitly allows

### B) SQL baseline hygiene (best-effort)
- File is not empty
- Contains deterministic header/sectioning if project enforces it (best-effort)
- No obviously unsafe patterns intended for runtime execution (note: file is a bundle, not executed here)

### C) Table naming + collisions
- Extract current table names from `CREATE TABLE` statements (best-effort)
- Enforce singular naming rule (report violations)
- Collision/alias risk detection:
  - If Description introduces new tables, check name tokens against existing table names
  - If potential semantic duplicate detected (token reorder like `w3_verified_contract` vs `w3_contract_verified`):
    - mark as `Critical` and block planning unless explicit naming decision is provided

### D) Dependencies (best-effort)
- ModuleRoot exists
- SqlFile exists

---

## [04] Output template (Mandatory)
Audit.md MUST include (order fixed):
1) Header (ModuleName, Version, RunId/RunItemId)
2) Resolved paths
3) Summary score (Pass/Fail + risk)
4) Findings (Critical/High/Medium/Low)
5) Required fix list (checklist)
6) Non-destructive notes (what must NOT change)
7) Approval note (if ApprovalMode=manual)

Hard rule:
- If collision gate risk exists, Required fix list MUST include:
  - explicit naming decision requirement
  - list of conflicting/near-duplicate names

---

## [05] Minimal success criteria
- Audit.md exists at run record path.
- Deterministic `index.update.audit.md` exists at Planning path.
