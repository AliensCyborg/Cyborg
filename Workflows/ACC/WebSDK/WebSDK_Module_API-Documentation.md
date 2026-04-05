---
WorkflowId: WebSDK_Module_API-Documentation
WorkflowName: WebSDK Module API Documentation Generator
Type: Workflow_Singular-Documentation
ParentWorkflowId: WebSDK_Module_API
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-24
Inputs:
  - Targets: "Module/ApiName, Module/ApiName2, ..."
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/API/{ApiName}.plan.md"
  - ApiFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/api/{ApiName}.php"
Produces:
  - Documentation: "/Aliens/Docs/WebSDK/Module/{Module}/API/{ApiName}.md"
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
    - "Workflows/ACC/_Refs/WebOS_Engines.md"
    - "Workflows/ACC/_Refs/WebOS_System.md"
    - "Workflows/ACC/_Refs/WebSDK.md"
NonNegotiables:
  - DocsLocationFixed: "/Aliens/Docs/WebSDK"
  - NoDelete: true
  - DefaultVersionFallback: "PHP.26.00.00"
   - Approval: "Follow /Aliens/manifest.json"
---

# WebSDK_Module_API-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

**Role (Hinglish):**  
Yeh file `WebSDK_Module_API` workflow ke liye **documentation generator** hai.  
Iska kaam: planning + actual generated API file ko read karke **enterprise-grade docs** banana aur `/Aliens/Docs/WebSDK/` me save karna.

> Important: Is file me sirf **WebSDK Module APIs documentation** ke unique rules honge.  
> Common documentation rules `Workflow_Singular.md` + `Workflow_Singular-Documentation.md` se aayenge.

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

## [01] Inputs interpretation (unique)
Targets:
- `Module/ApiName` => WebSDK module + API name

Module-only target (Batch Create Mode):
- If target is ONLY a module name (no `/`), example:
   - `Teacher`
- Then it means: **create missing APIs for this module only** and generate docs for those created targets.
- Workflow MUST expand this into minimum required CRUD API targets:
   - `{Module}/{Module}` (singular)
   - `{Module}/{Module}s` (default plural by `+ 's'`)
   - `{Module}/{Module}New` (create)
   - `{Module}/{Module}Update` (update)
- For each expanded target:
   - If `{ApiFile}` exists => **SKIP** (do NOT touch; docs are not regenerated here)
   - If `{ApiFile}` missing but created in this run => generate docs normally

Notes:
- Some modules may require more than these 4 CRUD APIs. Those MUST be passed explicitly as `Module/ApiName`.

Comma-separated targets => workflow multiple times run hoga.

Description:
- extra requirements; docs me “Notes / Constraints” me include hoga.

---

## [02] Resolve paths (unique mapping)
Using `ACC.Paths.md` resolver contract:

### WebSDK version
- primary: latest `PHP.{Latest}`
- fallback: `PHP.26.00.00`

### Module root
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`

### API file
- `{ApiFile} = {ModuleRoot}/api/{ApiName}.php`

### Planning doc (mandatory)
- `{PlanDoc} = /Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/API/{ApiName}.plan.md`

### Docs output path (fixed)
- `{DocFile} = /Aliens/Docs/WebSDK/Module/{Module}/API/{ApiName}.md`

---

## [03] Mandatory prerequisites
Documentation generator MUST verify:

1) PlanningDoc exists  
2) ApiFile exists

If any missing:
- Mark target failed:
  - `Failed(PlanningMissing)` or `Failed(ApiFileMissing)`
- Do not generate docs

---

## [04] What documentation MUST include (per API)
Generator MUST produce docs with these sections:

1) **Overview**
   - What this API does (from planning goal)
2) **Location**
   - Full path of API file
3) **PlanMode**
   - Create/Update + reason
4) **Auth & Permission**
   - Roles, tokens, restrictions (from plan)
5) **Request Contract**
   - Inputs table (field, type, required, constraints, example)
6) **Response Contract**
   - success payload example
   - error payload example (mapped to ACC.Errors)
7) **Data Mapping**
   - Tables involved
   - Columns touched
   - Soft delete / status rule if relevant
   - If new tables/columns were introduced, mention required SQL update workflow and naming decision
8) **Side Effects**
   - Cache changes, logs, downstream triggers
9) **Error Codes**
   - Stable error codes list (ACC.Errors mapping)
10) **Examples**
   - Example request payload(s)
   - Example response(s)
11) **Security Notes**
   - Validation, rate limit, audit logging, sensitive data rules
12) **Change Log**
   - What changed (if update)
   - Link to run record item paths (RunId/RunItemId)
13) **Definition of Done**
   - Tests checklist
   - Lint/format checklist
   - Manual QA checklist

---

## [05] How to derive content (source of truth)
Docs MUST be built from:

### Primary source: PlanningDoc
- Behavior, mapping, contract, auth, constraints

### Secondary source: Actual ApiFile
- Confirm file path, class/function names, final endpoints naming (if present)
- If mismatch between planning and code:
  - Doc must state: “Mismatch detected”
  - Prefer planning for intent, but mention code reality

---

## [06] Folder create rules
If docs folders missing, generator MUST create:

- `/Aliens/Docs/WebSDK/Module/{Module}/API/`

No delete operations allowed.

---

## [07] Run record output (mandatory)
For each target, generator MUST write:

- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md`

It must include:
- Doc path
- Sources used (plan file + api file)
- Summary of sections generated
- Any mismatches / warnings
- Next actions (if any)

---

## [08] What this workflow MUST NOT do
- Code generate/update nahi karega
- Planning generate nahi karega
- Files delete nahi karega
- DB execute nahi karega

---

## [09] Example
`Workflow("WebSDK_Module_API", "User/UserNew", "Docs must be premium and enterprise readable")`
Docs should appear at:
- `/Aliens/Docs/WebSDK/Module/User/API/UserNew.md`

---
