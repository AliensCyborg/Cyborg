---
WorkflowId: WebSDK_Module_SQL_Update-Documentation
WorkflowName: WebSDK Module SQL Update Documentation
Type: Workflow_Singular-Documentation
ParentWorkflowId: WebSDK_Module_SQL_Update
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-31
Inputs:
  - Targets: "ModuleName"
  - Description: "Any human language requirement"
Consumes:
  - AuditArtifact: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.update.audit.md"
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md"
  - SqlFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/sql/index.sql"
Produces:
  - Documentation: "/Aliens/Docs/WebSDK/Module/{Module}/SQL/index.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/WebSDK.md"
NonNegotiables:
  - DocsLocationFixed: "/Aliens/Docs/WebSDK"
  - NoDelete: true
  - ModifyOnly: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
---

# WebSDK_Module_SQL_Update-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## Role
Per module SQL docs update/generate karo, based on AuditArtifact + PlanningDoc + actual `sql/index.sql`.

## [00B] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if anything is missing
- Security/Performance/Observability/Testing sections required

Rule:
- Any local “minimum” list is additive; it MUST be mapped into the 19-section structure (must not shorten/override SSOT).

---

## [01] Preconditions (Update-only)
MUST exist:
- AuditArtifact
- PlanningDoc
- SqlFile

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Documented SQL table names MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

If missing:
- fail with `Failed(AuditMissing)` / `Failed(PlanningMissing)` / `Failed(SqlFileMissing)`

---

## [02] Docs content
Doc MUST follow the **19 mandatory sections** defined in `Workflow_Singular-Documentation`.

Additionally (SQL Update specific), ensure coverage includes:
- Version + resolved paths
- Table list + purpose
- Keys/indexes overview
- What changed (Update Summary):
  - changed tables
  - changed columns
  - changed indexes
- Migration/rollout notes (ordered steps)
- Table naming collision checks (if any new tables were introduced)
- Definition of Done checklist

---

## [03] Save Outputs
- `/Aliens/Docs/WebSDK/Module/{Module}/SQL/index.md`
- RunRecordItem: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md`
