---
WorkflowId: PySDK_Module_API_Update-Doc
WorkflowName: PySDK Module API Update Documentation
Type: Workflow_Singular-Documentation
ParentWorkflowId: PySDK_Module_API_Update
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-26
Inputs:
  - Targets: "Module/ApiName, Module/ApiName2, ..."
  - Description: "Any human language update requirement"
Consumes:
  - AuditDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.audit.md"
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/API/{ApiName}.update.plan.md"
  - ApiFile: "/Aliens/PySDK/{Python.Version}/Module/{Module}/api/{ApiName}.py"
Produces:
  - Documentation: "/Aliens/Docs/PySDK/Module/{Module}/API/{ApiName}.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/PyOS_Engines.md"
    - "Workflows/ACC/_Refs/PyOS_System.md"
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - DocsLocationFixed: "/Aliens/Docs/PySDK"
  - NoDelete: true
  - DefaultVersionFallback: "Py.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
---

# PySDK_Module_API_Update-Doc.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

**Role (Hinglish):**  
Yeh file `PySDK_Module_API_Update` ke liye **documentation updater** hai.  
Iska kaam: update planning + updated API file ko read karke **enterprise-grade docs update** karna aur `/Aliens/Docs/PySDK/` me save karna.

> Important: Is file me sirf PySDK Module API UPDATE docs ke unique rules honge.  
> Common docs rules `_Common/Workflow_Singular.md` + `_Common/Workflow_Singular-Documentation.md` se aayenge.

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
- Any local section list below is additive coverage; it MUST be mapped into the 19-section structure (must not shorten/override SSOT).

---

## [01] Mandatory prerequisites (UPDATE docs)
Documentation updater MUST verify:

0) Deterministic audit artifact exists

1) Update PlanningDoc exists
2) ApiFile exists (updated file)

If any missing:
- Mark target failed:
  - `Failed(AuditMissing)` or `Failed(PlanningMissing)` or `Failed(ApiFileMissing)`
- Do not generate docs

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Any table names documented/referenced MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Module-only target (Batch Update Mode):
- If target is ONLY a module name (no `/`), example:
  - `Teacher`
- Then it means: **update docs for all existing APIs of this module** (update-only).
- In this mode workflow MUST:
  - Enumerate existing API files:
    - `{ModuleRoot}/api/*.py` (non-recursive)
  - For each existing `ApiName`:
    - Require update planning doc exists
    - Update the docs file
- Missing APIs MUST NOT be created.

---

## [02] Resolve paths (unique mapping)
- `{DocFile} = /Aliens/Docs/PySDK/Module/{Module}/API/{ApiName}.md`
- Create folders if missing:
  - `/Aliens/Docs/PySDK/Module/{Module}/API/`
No delete allowed.

---

## [03] Docs must include Update-specific sections
Along with standard API docs sections, UPDATE docs MUST include:

### A) Update Summary
- Why update was needed (from Description cleaned)
- Change classification: NonBreaking/Breaking/Risky (from plan)

### B) What changed (high-level)
- Inputs changed?
- Outputs changed?
- Auth/permission changed?
- Validation changed?
- Any module class method changes?
 - Any SQL/schema changes? (if `PySDK_Module_SQL_Update` was required)
 - Table naming decision notes (if new tables/columns introduced)

### C) Compatibility Notes
- Any breaking changes must be explicitly highlighted
- If approval required, mention approval status

### E) Base API Compliance (Teachers Reference)
Docs MUST align to the current Base API reference unless plan overrides:
- Reference: `/Aliens/PySDK/Py.26.00.00/Module/Teacher/api/Teachers.py`
- All example URLs MUST be routed (no direct file execution):
  - `https://base.software/?api=<ApiName>&...`
- If aggregates are supported/added:
  - Document `method=COUNT|SUM` and `methodcolumn` rules
  - Mention that aggregates ignore paging (LIMIT/OFFSET)

### D) Run linkage
- Include run record paths:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/planning.md`
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/code.md`
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/documentation.md`

---

## [04] Sources of truth
Docs MUST be built from:
1) Update PlanningDoc (primary)
2) Current ApiFile (secondary, for reality verification)

If mismatch:
- Mention “Mismatch detected”
- State which parts follow planning vs code.

---

## [05] Run record output (mandatory)
Write:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md`

Must include:
- Doc path
- Sources used
- Warnings/mismatches
- Next actions (if any)

---

## [06] What this workflow MUST NOT do
- Code generate/update nahi karega
- Planning generate nahi karega
- Files delete nahi karega
- Missing API create nahi karega (update-only)
