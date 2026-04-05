---
WorkflowId: WorkflowNew
WorkflowName: Workflow New (Create Human Workflow Doc)
Type: AI-Executable Workflow (ACC)
Domain: Workflow
Category: Authoring
PrimaryLanguage: Markdown
Source: Aliens Company ACC
Version: 1.0.0
Status: Active
Owner: Aliens Company
Author: AlienCyborg
AuthorURI: https://AlienCyborg.com
LastUpdated: 2026-03-01
Purpose: >
  Yeh workflow Aliens Company ke HUMAN workflows ke liye naya workflow document
  (SOP/Recipe-style) create karta hai. Output sirf /Aliens/Workflow/ folder me
  human-readable .md workflow file hota hai. AI-agent workflows ko touch karna,
  link banana, ya unme ched-chad strictly forbidden hai.
---

# WorkflowNew

## 1) Title Block
**Workflow:** WorkflowNew  
**Scope:** Create a NEW Human Workflow document in `/Aliens/Workflow/`  
**Version:** 26.03.01  
**Owner:** Aliens Company  
**Author:** AlienCyborg  
**Author URI:** https://AlienCyborg.com  

---

## 2) Executive Summary (Super Easy)
Tum `WorkflowNew` tab use karte ho jab tumhe **naya workflow** banana ho (human team ke liye).

Aliens me “workflow” ka matlab simple 3-step list nahi hota.
Yahan workflow ek **SOP-grade execution recipe** hota hai: objective + scope + rules + steps + decision branches + quality gates + troubleshooting.

`WorkflowNew` ka output:
- **1 new file**: `/Aliens/Workflow/{DomainFolder}/{WorkflowId}.md`

Hard boundary:
- Yeh workflow **sirf HUMAN workflows** generate karta hai.
- AI-agent workflows ka folder alag hai. Unko read/modify/link karna **allowed nahi**.

---

## 3) Is Workflow Ka Kaam Kya Hoga (SSOT)
`WorkflowNew` ka kaam:
1) Tumhare diye hue `WorkflowId` ko validate/normalize karna
2) Domain infer karna (WebSDK/WebOS/WebApp/WebBrand/Workflow)
3) Existing human workflows ko reference ke tor par read karna (style/sections)
4) New human workflow `.md` file generate karna with:
	- YAML metadata header
	- Title Block
	- Inputs/Preconditions
	- Step-by-step recipe (create/update routing rules if applicable)
	- Decision points + quality gates
	- Common errors + troubleshooting
	- Mermaid flowchart(s)
5) Acceptance evidence produce karna: output file exists + structure complete

Non-goals:
- Existing human workflow ko update/overwrite nahi karega.
- AI-agent workflows ke saath koi binding/link nahi karega.

---

## 4) Invocation (Kaise Run Hota Hai)
Standard format:

`Workflow("WorkflowNew", "Targets", "Description")`

### Mode A: Single WorkflowId (1 workflow doc create)
Example:

`Workflow('WorkflowNew', 'WebSDK_Module_Class', 'Is workflow ka purpose...')`

Meaning:
- Target = `WebSDK_Module_Class` (workflow id)
- Description = human workflow ka actual intent/spec

### Mode B: CSV-driven (multiple workflows queue me)
Example:

`Workflow('WorkflowNew', 'WorkflowNew.csv', 'Har workflow ka description CSV ke Description column me hai')`

Meaning:
- Target = a CSV file name
- Runner CSV ko read karke **per run exactly 1 pending row** (`status=0`) process karega
- Row ke `Description` column ka content hi **actual workflow prompt** mana jayega

Important:
- New conversation start pe CSV auto-pick/auto-resume forbidden.
- CSV run sirf tab start hoga jab user current conversation me explicit target CSV de.

---

## 4.1) Universal ACC Planner (CSV) Schema (SSOT)
Yeh section **planner-style** ACC CSV (Tasks/Planner) ka canonical schema define karta hai.

SSOT location rule:
- Planner CSVs sirf yahan allowed hain: `/Aliens/Project/{Developer_Username}/`  (ref: `ACC.Paths` + runner strict guard)

Compatibility rule (NON-NEGOTIABLE):
- CSV headers **case-insensitive** treat hote hain.
- Unknown/extra columns ko runner **drop nahi** karta (preserve).

### 4.1.1 Minimum Required Columns (runner compliance)
Runner preflight/audit ke mutabiq yeh columns **required** hain:

1) `ID`
- Unique row identifier (string recommended numeric, e.g., `1`, `2`)
- `DependsOn` isi ID ko reference karta hai

2) `Name`
- Workflow ka primary target/name (no file extension)
- WebApp UI artifacts me dot-prefix rule apply hota hai (e.g., `AppName.Home`)

3) `Worklflow`
- **Intentional legacy header spelling**: `Worklflow` (NOT `Workflow`)
- Is value ko runner normalize karta hai (typos like `Documention` -> `Documentation`)
- Value must exist in workflow registry index and should be supported by runner

4) `Description`
- **Prompt payload** (mandatory)
- Empty/missing => fail fast (`ACC_ERR_CSV_DESCRIPTION_MISSING`)
- Recommended: `[TARGET]` block row fields se consistent ho

5) `Status`
- `0` = pending (runnable candidates)
- `1` = completed (terminal)
- `2` = failed (auto-retry forbidden; user reset to `0` if retry)

6) `Assign`
- Task assignment gate: runnable tabhi hai jab `Assign == Developer.Username`
- Missing/blank => `ACC_ERR_TASK_ASSIGNMENT_REQUIRED`

### 4.1.2 Recommended Columns (planner governance + determinism)
In columns ka hona strongly recommended hai:

- `DependsOn`
  - Comma-separated row IDs: `1,2, 3`
  - Gate: referenced rows exist AND unka `Status == 1` ho (else `ACC_ERR_TASK_DEPENDENCY_NOT_MET`)

- `Parent`, `Parent_Type`
  - Hierarchy modeling (especially WebApp rows)
  - Example (WebApp):
    - Screen row: `Parent_Type=WebApp_Structure`, `Parent={AppName}`
    - Section row: `Parent_Type=WebApp_Screen`, `Parent={AppName}.{ScreenName}`
    - Component row: `Parent_Type=WebApp_Section`, `Parent={AppName}.{SectionName}`

- `Planning_Path`, `Code_Path`, `Doc_Path`, `Content_Path`
  - Deterministic derived paths; runner inko prefill/fix kar sakta hai
  - Note: `Content_Path` intentionally optional ho sakta hai (workflow/design dependent)

### 4.1.3 Progress Tracking Columns (when present)
Yeh columns present hon to runner ko writeback karna mandatory hai:

- `created`
  - Create run me set-once (overwrite forbidden)

- `modified`
  - Update/processing run me refresh allowed

- `code_lines`
  - Sirf **primary code artifact** ke lines count (planning/docs not included)

### 4.1.4 Optional Phase Markers (Workflows runner)
Planner me yeh columns present hon to `Workflows` runner phase-wise fill karta hai:

- `Planning`, `Planning_Save`
- `Code`, `Code_Save`
- `Docs`, `Doc_Save`

Compatibility aliases (common planners):
- `Doc_Save` == `doc_save`
- `Docs` == `doc`

### 4.1.5 WebApp UI-only Column
- `utag`
  - Sirf UI rows: `WebApp_Screen`, `WebApp_Section`, `WebApp_Component`
  - Missing/empty => schema invalid (example: `ACC_ERR_CSV_SCHEMA_INVALID`)

### 4.1.6 Example Header (superset; safe)
Yeh header ek safe superset hai; planners isko use karke sab workflows support kar sakte hain:

`ID,Name,Worklflow,Parent,Parent_Type,Description,Status,Assign,DependsOn,Planning_Path,Code_Path,Doc_Path,Content_Path,utag,Planning,Planning_Save,Code,Code_Save,Docs,Doc_Save,created,modified,code_lines`

---

## 5) Inputs Contract

### 5.1 Mandatory
1) `WorkflowId`: `WorkflowNew`
2) `Targets`: either
	- single workflow id (e.g., `WebOS_Engine_Go`) OR
	- CSV filename (e.g., `WorkflowNew.csv`)
3) `Description`:
	- Mode A: workflow ka spec
	- Mode B: CSV meta note (actual per-row Description in CSV)

### 5.2 Valid Target Grammar (Mode A)
Allowed pattern examples:
- `WebSDK_Module_X`
- `WebOS_Engine_X`
- `WebApp_Screen_X`

Forbidden:
- file extensions: `.md`, `.csv` (Mode A me)
- spaces: `WebSDK Module Class`
- path traversal: `../Something`

---

## 6) Output Contract (Exactly Kya Create Hoga)

### 6.1 Output root (Human workflows)
Human workflows folder SSOT:
- `/Aliens/Workflow/`

### 6.2 Domain mapping (default)
WorkflowId prefix se domain choose karo:
- `WebSDK_` => `/Aliens/Workflow/WebSDK/{WorkflowId}.md`
- `WebOS_`  => `/Aliens/Workflow/WebOS/{WorkflowId}.md`
- `WebApp_` => `/Aliens/Workflow/WebApp/{WorkflowId}.md`
- `WebBrand_` => `/Aliens/Workflow/WebBrand/{WorkflowId}.md`

Agar prefix match nahi hota:
- STOP (fail-fast)
- user se domain clarify mangna (guessing forbidden)

### 6.3 Create-only rule
If output file already exists:
- STOP
- Report: "Already exists — update workflow alag banega" (overwrite forbidden)

---

## 7) Reference (Human Workflow Folder se Read-Only Examples)
Yeh files reference ke liye use karo (style + sections):
- `/Aliens/Workflow/WebSDK/WebSDK_Module_Class.md`
- `/Aliens/Workflow/WebOS/WebOS_Engine_SQL.md`
- `/Aliens/Workflow/WebApp/WebApp_Screen.md`
- `/Aliens/Workflow/WebApp/WebApp_Screen_Update.md`
- `/Aliens/Workflow/README.md`

Note:
- Reference read-only.
- In references ko `WorkflowNew` me "Design inspiration" ke tor par follow karo.

---

## 8) High-Level Flow (Mermaid)
```mermaid
flowchart TD
  A[Start: WorkflowNew] --> B[Validate Target + Description]
  B --> C{Mode?}
  C -->|Target ends with .csv| D[CSV Mode]
  C -->|Else| E[Single WorkflowId Mode]
  D --> D1[Load CSV]
  D1 --> D2[Pick exactly 1 status=0 row]
  D2 --> D3[Use row.Description as actual prompt]
  D3 --> E
  E --> F[Infer Domain from WorkflowId prefix]
  F --> G[Resolve output path under /Aliens/Workflow]
  G --> H{File exists?}
  H -->|Yes| X[STOP: Create-only, no overwrite]
  H -->|No| I[Generate human workflow doc (.md)]
  I --> J[Validate: metadata + title block + sections]
  J --> K[Save file]
  K --> L[Return summary + evidence]
```

---

## 9) Step-by-Step Recipe (Ultra Practical)

### Step 1: Target interpret
- If target ends with `.csv` => CSV mode
- Else => Single workflow id mode

### Step 2: Normalize workflow id
- Trim spaces
- Reject invalid chars
- Ensure it matches allowed prefix patterns

### Step 3: Domain infer
- Prefix mapping (Section 6.2)
- If ambiguous => STOP

### Step 4: Draft workflow content (structure)
New human workflow `.md` MUST include:
1) YAML metadata header
2) `# {WorkflowId}` heading
3) Title Block with Author + Author URI
4) Executive Summary
5) Why it exists
6) Audience matrix (optional but recommended)
7) Input contract + target grammar
8) Paths + outputs
9) Flowchart(s)
10) Stage-by-stage steps
11) Quality gates
12) Troubleshooting
13) Completion criteria

### Step 5: Safety/Policy rules
- No delete/rename of existing workflows
- No edits outside `/Aliens/Workflow/`
- No touching AI-agent workflow repo/folders
- If something missing/unclear => ask 1–3 clear questions (no assumptions)

### Step 6: Save + evidence
- Output file exists at expected path
- Headers present
- At least 1 mermaid flow

---

## 10) Quality Gates (Mandatory)
QG-1: Correct output folder (domain mapping ok)
QG-2: Create-only respected (no overwrite)
QG-3: Title Block has Author + Author URI
QG-4: Clear Input Contract + Outputs defined
QG-5: Decision branches included where needed (create vs update if workflow type requires)
QG-6: Troubleshooting section exists

---

## 11) Common Failures + Fix

### Failure: Prefix unknown
Symptom: target `TeacherWorkflow` (no WebSDK_/WebOS_/WebApp_)
Fix: Provide correct WorkflowId naming OR explicitly define Domain.

### Failure: File already exists
Symptom: output path exists
Fix: Create separate update workflow OR do manual update with audit.

### Failure: Description too vague
Symptom: workflow me objective unclear
Fix: Provide Description with:
- OBJECTIVE
- SCOPE
- OUTPUTS
- RULES

---

## 12) Completion Criteria (Kab complete mana jayega)
Done tab mana jayega jab:
- New human workflow `.md` file created under `/Aliens/Workflow/{Domain}/`
- Workflow doc me required sections present
- Mermaid flowchart present and renders
- No AI-agent workflow files touched

---

## 13) Extension Hooks (Future)
- `WorkflowUpdate` sibling workflow ban sakta hai (audit + safe edit)
- Domain-specific templates add ho sakte hain (WebSDK/WebOS/WebApp)

