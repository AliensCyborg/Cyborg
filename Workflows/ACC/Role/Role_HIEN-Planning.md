---
WorkflowId: Role_HIEN-Planning
WorkflowName: Aliens Role SOP Planner (HIEN)
Type: Workflow_Singular-Planning
ParentWorkflowId: Role_HIEN
Domain: Role
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-27

Inputs:
  - Targets: "RoleName (single only)"
  - Description: "REQUIRED; role context + constraints + special focus areas"

Produces:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Role/HIEN/{RoleSlug}.plan.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md"

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"

NonNegotiables:
  - NoDelete: true
  - DescriptionRequired: true
  - OneRoleOnly: true
  - Language: "Hinglish (Roman). Hindi script not allowed."
  - SOPStyle: true
  - BeginnerFriendly: true
  - UltraDetailed: true
  - NoFactInvention: true
---

# Role_HIEN-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [01] Purpose (Hinglish)
Yeh workflow ek single role ke liye **SOP document ka planning / outline** banata hai.
Planning ka goal:
- Role SOP ko **deterministic structure** dena (sections + tables + diagrams)
- Beginner-friendly Hinglish language plan karna
- Tables / comparison matrices / flowcharts ka plan banana
- Role-specific sections identify karna (executive vs engineering vs product vs design)
- Department + audience + ecosystem alignment scope define karna
- Missing info ko clearly mark karna (no guessing)

> Note: Planning ka output final SOP nahi hota. Final SOP `Role_HIEN-Documentation` banayega.

---

## [02] Inputs Rules (Strict)
### Targets
- `Targets` MUST be a single RoleName.
- Comma-separated targets yahan allowed nahi.

If multiple targets detected:
- Fail fast with `ACC_ERR_ARGS_INVALID`.

### Description
- Required hai.
- Description me agar outer workflow (CSV mode) ka context diya gaya ho, to is planning me **same as-is** include karo.

---

## [03] Role Normalization (Deterministic)

### 3.1 RoleTitle
- `RoleTitle` = Targets trimmed.
- Underscores converted to spaces for display title.
- Case preserve (PascalCase preferred for title).
- Example: `Software_Engineer` => display "Software Engineer"

### 3.2 RoleSlug (for filenames)
- If `Description` contains a line like `UTag: <value>` (case-insensitive key):
  - Use that `<value>` as `RoleSlug` (after safe normalization below).
- Else:
  - Derive `RoleSlug` from `Targets`.

Normalization rules (apply in both cases):
- Spaces -> `_`
- `/` -> `_`
- `:` -> `_`
- `(` and `)` -> removed
- Multiple `_` collapse.
- Keep letters/numbers/`_`/`.`.

Examples:
- `Software Engineer` -> `Software_Engineer`
- `Product Manager (Senior)` -> `Product_Manager_Senior`
- `CEO` -> `CEO`

---

## [04] Planning Output Path (SSOT)
- Planning doc MUST be saved at:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Role/HIEN/{RoleSlug}.plan.md`

---

## [05] Existing Role Reference Read (Important)
- Before planning, check if `/Aliens/Role/{RoleSlug}.md` already exists.
- If exists:
  - Fail fast with `ACC_ERR_IO_FILE_EXISTS` (unless Description explicitly says `overwrite=true`).
- Check if **similar roles** exist in `/Aliens/Role/` (e.g., if planning "Software_Engineer_Lead", read "Software_Engineer_Senior.md" for format reference).
- Similar role docs ko **format reference** ke tor par use karo — content copy mat karo.
- Also read 1-2 existing Role docs from `/Aliens/Role/` to study the exact SOP structure, heading patterns, section depth, and table formats.

---

## [06] Role Classification (Determines Section Depth)
Before planning, role ko classify karo:

| Category | Examples | Special Sections |
|---|---|---|
| C-Level / Executive | CEO, CTO, CFO, CMO, CDO, CVP | Governance, strategy, capital, board, risk management, executive reporting |
| VP / Director | EVP, VP, Director, GM | Leadership bench, org design, budget ownership, multi-team execution |
| Management | Product Manager, Project Manager, Engineering Manager | Sprint/delivery processes, stakeholder mgmt, roadmaps, prioritization |
| Senior Individual Contributor | Senior Engineer, Staff Engineer, Principal | Architecture, mentoring, tech debt strategy, code review governance |
| Individual Contributor | Software Engineer, Designer, Marketer | Core execution, skill building, growth paths, peer collaboration |
| Entry Level | Intern, Trainee, Junior | Learning paths, mentorship, basic tasks, supervised execution |

Classification determines:
- Depth of strategy vs execution sections
- Whether governance/board sections needed
- Training roadmap detail level
- KPI complexity
- V2.0.0 section selection

---

## [07] Planning Document Format (Mandatory)
Planning doc MUST contain these sections (in order):

### 1) Title + Metadata
- RoleTitle, RoleSlug, Department, Audience, Role Category (from [06])
- Version: 2.0.0
- Language: Hinglish (Roman)

### 2) Prompt Payload Snapshot
- Copy the exact Description / prompt input

### 3) Audience + Reading Level
- Primary: who will read this SOP document?
- Secondary: who else benefits?
- Reading level: Beginner-friendly (explain every term)

### 4) SOP Document Structure Plan
- List all planned H2 sections with brief one-line description
- Base sections (35-36) + V2.0.0 additions (role-specific count)
- Total planned section count

### 5) Role Introduction Plan
- 2-3 line summary of how introduction will be framed
- Key differentiator vs similar roles
- Why this role matters in Aliens ecosystem

### 6) Responsibilities Breakdown Plan
- Daily / weekly / monthly / quarterly / annual breakdown approach
- Role-specific responsibilities (not generic)
- Min 3 categories of responsibility

### 7) Skills + Technologies Plan
- Technical skills list (planned)
- Soft skills list (planned)
- Tools / technologies / frameworks list (planned)
- Languages (programming / communication / query as applicable)

### 8) Tables Plan (at least 5)
For each planned table:
- Table name
- Columns
- Purpose
- Approximate row count

Mandatory tables:
- Business Impact table (Area vs Impact vs Result)
- Skills / competency matrix
- Tools cheat sheet table
- KPIs / metrics table
- Salary ranges table (global reference)
- Additional role-specific tables as needed

### 9) Flowcharts / Diagrams Plan (Mermaid) (at least 2)
For each planned diagram:
- Diagram name
- Type (flowchart / sequence / org chart / gantt)
- Purpose
- Key nodes/steps

Mandatory diagrams:
- Org structure / reporting placement diagram
- At least 1 SOP workflow flowchart (role-specific process)
- Career growth path diagram (optional but recommended)

### 10) SOP Workflows Plan
- List role-specific workflows that will be documented
- For each: trigger, steps (high-level), output
- Examples: feature workflow, bug fix workflow, review process, release process, incident response, etc.

### 11) Training Roadmap Plan
- Phase breakdown (0-30, 30-60, 60-90, 90-180 days)
- Key milestones per phase
- Role-specific training topics

### 12) V2.0.0 Sections Plan (WebOS / Aliens Ecosystem Alignment)
- List specific V2.0.0 sections applicable to this role
- Explain why each section is relevant
- Typical count: 5-10 extra sections

### 13) Collaboration + Cross-Functional Plan
- List all teams this role collaborates with
- Collaboration model per team (who initiates, what shared, cadence)

### 14) Terminology / Glossary Plan
- Min 15 terms planned
- Role-specific + Aliens ecosystem terms

### 15) Assumptions + Unknowns (strict)
- What assumptions are being made?
- What information is missing?
- Explicitly mark unverified items

### 16) Acceptance Criteria (what "done" means for this planning)
- Output file exists and non-empty
- All 16 sections present
- At least 5 tables planned
- At least 2 Mermaid diagrams planned
- V2.0.0 sections identified
- Assumptions section present

---

## [08] Content Rules (Non-Negotiable)
- Hinglish Roman only; Hindi script bilkul nahi.
- Beginner-friendly: explain every major term.
- Honesty: agar exact fact confirm nahi, explicitly label it as `Not confirmed` / `Needs verification`.
- No plagiarism: content original phrasing me ho.
- No copying content from existing Role docs — only format/structure reference allowed.

---

## [09] Acceptance Criteria (Planning)
Planning success tabhi:
- Output file exists and non-empty
- All 16 planning sections present
- At least 5 tables planned with column definitions
- At least 2 Mermaid diagrams planned with node descriptions
- V2.0.0 sections identified with relevance explanation
- Assumptions section present
- Role classification done (from [06])
- Total planned section count >= 35
