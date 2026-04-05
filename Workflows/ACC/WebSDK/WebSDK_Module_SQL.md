---
WorkflowId: WebSDK_Module_SQL
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module"
Scope: Generate/update module SQL bundle (index.sql)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates the SQL schema bundle for one or multiple WebSDK modules.
  This workflow orchestrates Planning -> Code -> Documentation per Module target.
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_SQL-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_SQL-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_SQL-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated list. Each item must be a ModuleName (NO slash)."
  - Name: Description
    Type: string
    Required: false
    Notes: "Any human language. Schema requirements and constraints."
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: false
  DbTouch: "no (file generation only)"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Documentation"]
FailureStrategy:
  FailFast: false
  OnPlanningFail: "skip Code+Documentation for that target"
  OnCodeFail: "Documentation may run but must clearly mark code generation failed"
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_SQL.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_SQL` ek **Plural** workflow hai jo WebSDK module ke SQL artifacts generate/update karta hai.

Example:
- `Workflow("WebSDK_Module_SQL", "Teacher", "Teacher module ka SQL schema bundle banado")`
- `Workflow("WebSDK_Module_SQL", "Teacher,User,Account", "In modules ka SQL update karo")`

---

## Target Grammar (Strict)
Each target item must be:
- `ModuleName` (NO slash)

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Table names produced in `sql/index.sql` MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Table Naming Collision Gate (Non-Negotiable):
- New table names MUST be checked against existing schema to avoid semantic duplicates
  (e.g., `w3_verified_contract` vs `w3_contract_verified`).
- Collision risk => fail fast and require explicit naming decision.

Valid:
- `Teacher`
- `User`

Invalid:
- `Teacher/TableNew` (yeh module-level SQL workflow nahi)

---

## Orchestration Contract (per target)
For each module target:
1) Validate ModuleName
2) Resolve version (latest, fallback `PHP.26.00.00`)
3) Resolve paths:
   - ModuleRoot: `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
   - SqlFile: `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/sql/index.sql`
4) Call child workflows in order:
   - `Workflow("WebSDK_Module_SQL-Planning", "<Module>", "<Description>")`
   - `Workflow("WebSDK_Module_SQL-Code", "<Module>", "<Description>")`
   - `Workflow("WebSDK_Module_SQL-Documentation", "<Module>", "<Description>")`

---

## Policies
- NoDelete: true
- This workflow is create-or-update for `sql/index.sql`.
- Missing module folder is an error by default.
  - If user wants to bootstrap a module, run `WebSDK_Module` first.

---

## Success Criteria
- Planning doc created per module
- `sql/index.sql` created or updated per plan
- Docs created under `/Aliens/Docs/WebSDK/Module/{Module}/SQL/`
- Run summary includes created/updated files list
