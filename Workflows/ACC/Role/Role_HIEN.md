---
WorkflowId: Role_HIEN
WorkflowName: Aliens Role SOP Workflow (HIEN)
Type: Workflow_Plural
Domain: Role
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-27

Intent:
  Summary: >
    Aliens Company ke liye ultra detailed Role SOP (Standard Operating Procedure) documents generate karna
    Hinglish Roman me. Har document ek role ka complete guide hota hai — responsibilities, skills,
    workflows, flowcharts, career paths, KPIs, training roadmap, aur WebOS/Aliens ecosystem alignment.
    Workflow single-role mode aur CSV/planner mode dono support karta hai, lekin per-run sirf 1 role.
  WhenToUse:
    - "Workflow('Role_HIEN','Software_Engineer','...')" (single role)
    - "Workflow('Role_HIEN','Roles.csv','...')" (planner se one-role-per-run)

Inputs:
  Targets:
    Format: "RoleName | Roles.csv"
    Examples:
      - "Software_Engineer"
      - "CEO"
      - "Product_Manager"
      - "Roles.csv"
  Description:
    Format: "REQUIRED; any human language. Role context + constraints + special focus areas."
    Examples:
      - "Software Engineer ka ultra detailed SOP banao. WebOS ecosystem focus. Beginner-friendly."
      - "Roles.csv me bahut saari roles hain; har run me sirf 1 pending role likho."

Defaults:
  Language: "HIEN (Hinglish Roman)"
  OutputPreference:
    Primary: "/Aliens/Role"
    Fallback: "/Aliens/Docs/Role/HIEN"

Permissions:
  NoDelete: true
  CanCreateFiles: true
  CanEditFiles: true
  CanModifyProd: false
  CanTouchDB: false

Approval:
  Mode: "manifest"
  RequiredActions: []

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Plural.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"

Calls:
  ChildWorkflows:
    - "Role_HIEN-Planning"
    - "Role_HIEN-Documentation"

Outputs:
  RunRecord: true
  Planning: true
  Code: false
  Documentation: true

NonNegotiables:
  - NoDelete: true
  - OneRolePerRun: true
  - OneRoleOnlyInNonCsvMode: true
  - Language: "Hinglish Roman only; Hindi script not allowed"
  - SOPStyle: true
  - BeginnerFriendly: true
  - UltraDetailed: true
  - WebOSAligned: true
  - NoFactInvention: true
  - NoNetworkCalls: true
  - MustIncludeMermaid: true
  - MustIncludeTables: true
  - MustIncludeV2Sections: true
---

# Role_HIEN.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [01] Workflow ka meaning (Hinglish)
`Role_HIEN` ek **orchestrator workflow** hai.
Iska kaam:
- Targets ko validate karna
- Decide karna ke run **single-role** hai ya **CSV/planner** mode
- Per-run sirf **1 role SOP document** generate karwana
- Planning + Documentation child workflows call karna
- RunRecord outputs contract follow karna

### Kya generate hota hai?
Har role ke liye ek **ultra detailed SOP document** jo include karta hai:
- Role introduction + beginner-friendly definition
- Business impact
- Org structure placement
- Role levels (Intern/Junior se Lead/Director/C-level tak)
- Core responsibilities (daily/weekly/monthly/quarterly/annual)
- Required skills (technical + soft + domain-specific)
- Tools / technologies / frameworks
- SOP workflows (role-specific processes)
- Mermaid flowcharts (at least 2)
- Tables (at least 5)
- Deliverables checklist
- Quality standards
- Collaboration rules
- Documentation standards
- KPIs / performance metrics
- Hiring / interview evaluation matrix
- Training roadmap (0-180 days)
- Common mistakes + prevention
- Escalation matrix
- Career growth path
- Salary ranges (indicative / global reference)
- Tools cheat sheet
- Glossary
- V2.0.0 Additions: WebOS / Aliens Ecosystem alignment sections

---

## [02] Supported Run Modes (Strict)

### Mode A: Single Role
Example:
`Workflow('Role_HIEN', 'Software_Engineer', '...')`

Rules:
- Targets MUST be exactly one role name.
- Comma-separated multiple roles forbidden.
  - If comma detected => fail fast with `ACC_ERR_ARGS_INVALID`.

Execution:
- Call `Role_HIEN-Planning` with same Targets + Description
- Call `Role_HIEN-Documentation` with same Targets + Description

### Mode B: CSV / Planner
Example:
`Workflow('Role_HIEN', 'Roles.csv', '...')`

Rules:
- CSV file MUST be resolved ONLY from:
  - `/Aliens/Project/{Developer_Username}/Roles.csv`
- Per-run **sirf 1 runnable row** process hogi (global governance).

---

## [03] CSV Schema (SSOT for Role_HIEN)
`Roles.csv` MUST be a planner-style CSV (case-insensitive headers).

### Required columns
- `ID`
- `Name` (role title, e.g., "Software Engineer", "CEO", "Product Manager")
- `UTag` (output file-name slug; used for `{RoleSlug}.md`, e.g., `Software_Engineer`)
- `Workflow` (MUST be `Role_HIEN`)
- `Description` (row prompt payload; REQUIRED — role context, focus areas, constraints)
- `Status` (`0` pending, `1` success, `2` failed)
- `Assign` (MUST equal `{Developer_Username}`)

### Optional (recommended)
- `DependsOn` (ID references)
- `Priority`
- `created`, `modified`
- `Content_Path` (final SOP path override; must still be inside AllowedRoots)
- `Planning_Path` (planning path override)
- `Doc_Path` (doc path override)
- `Planning`, `Planning_Save`, `Docs`, `Doc_Save` (phase markers)
- `Department` (role ka department, e.g., "Engineering", "Product", "Executive")
- `RoleLevel` (hint: Intern/Junior/Mid/Senior/Lead/Director/VP/C-Level)

If schema mismatch / required column missing:
- Fail fast with `ACC_ERR_CSV_SCHEMA_INVALID`.

---

## [04] CSV Row Picking Rules (Deterministic)
Runnable row conditions (same philosophy as universal runner):
- `Status == 0`
- `Assign == {Developer_Username}`
- `DependsOn` empty OR all referenced rows have `Status == 1`

Pick strategy:
- File order me first runnable row pick karo.
- Random selection forbidden.

If no runnable row:
- Return `noop` or `blocked` (non-destructive).

---

## [05] CSV Dispatch Rules (Important)
For picked row:
- Validate:
  - `Workflow` == `Role_HIEN`
  - `Name` non-empty
  - `UTag` non-empty
  - `Description` non-empty
  - Missing row description => `ACC_ERR_CSV_DESCRIPTION_MISSING` and row ko mutate mat karo.

Then execute **same run** as:
- `Targets = Row.Name` (role title source)
- `EffectiveDescription = "Role: " + Row.Name + "\nUTag: " + Row.UTag + "\n\n" + OuterDescription + "\n\n---\n\n" + Row.Description`

Important:
- CSV mode me **role title** = `Name`
- CSV mode me **output file name** = `UTag` (as slug). Example: `UTag=Software_Engineer` => `Software_Engineer.md`

Child calls:
- `Role_HIEN-Planning( Targets, EffectiveDescription )`
- `Role_HIEN-Documentation( Targets, EffectiveDescription )`

Writeback semantics (strict):
- `Status` ka matlab row fully complete hai.
- Planning success => `Planning=1`, but `Status` stays `0` until docs complete.
- Docs success => `Docs=1` and `Status=1`.
- Any stage fail => mark that stage `2` and set `Status=2`.

---

## [06] Output Contract (Role SOP Document)
Article output path policy (deterministic):
- Default SOP path:
  - Preferred: `/Aliens/Role/{RoleSlug}.md`
  - Fallback: `/Aliens/Docs/Role/HIEN/{RoleSlug}.md`

`{RoleSlug}` resolution:
- CSV mode: `{RoleSlug}` MUST come from `UTag` column.
- Non-CSV mode: `{RoleSlug}` MUST be derived from `Targets`.

Slug normalization:
- Spaces -> `_` (underscore, NOT hyphen — Role repo convention)
- `/` -> `_`
- `:` -> `_`
- Multiple `_` collapse.
- Keep letters/numbers/`_`/`.`.
- Example: `Software Engineer` -> `Software_Engineer`
- Example: `Product Manager (Senior)` -> `Product_Manager_Senior`

Hard rule:
- Output path MUST be within manifest `Permissions.AllowedRoots`.
- Agar user `/Aliens/Role` chahte hain, lekin manifest AllowedRoots me nahi:
  - Fallback to `/Aliens/Docs/Role/HIEN`
  - OR fail with actionable error.

Planning path default:
- `/Aliens/.Alien/{Developer_Username}/Planning/Role/HIEN/{RoleSlug}.plan.md`

---

## [07] Role SOP Document Structure (Reference Standard)
Existing Role SOP documents follow yeh standard structure:

### Header Block (mandatory)
```
# Aliens Company SOP - {RoleTitle} (Ultra Detailed, Beginner-Friendly, Hinglish, WebOS / Aliens Ecosystem Aligned)

Version: 2.0.0
Document Type: SOP / Role Guide / Training + Execution Manual
Department: {Department}
Audience: {TargetAudience}
Language: Hinglish (easy, practical, beginner-friendly)
Use Case: Aliens Company SOP me add karne ke liye ready reference document
Aliens Ecosystem Alignment: WebOS + WebApp + WebSDK + AliensStyle + AliensGrade + AliensDocs + AI/Human dual workflow compatible
```

### Base Sections (1-35) — Every role MUST have:
1. Role ka simple introduction
2. {RoleTitle} kya hota hai (Beginner Friendly Definition)
3. Role differentiation (vs similar roles)
4. Business impact (role level results)
5. Org/branch structure me role ki position
6. Role scope types / variants
7. Core responsibilities (daily/weekly/monthly/quarterly/annual)
8. Required skills (domain-specific breakdown)
9. Languages / communication requirements
10. Technologies / tools / frameworks (detailed)
11. Role lifecycle / process flow (beginner-friendly)
12. SOP workflows (role-specific: feature, bug, review, release, etc.)
13. Flowcharts (Mermaid) — minimum 2
14. Deliverables checklist
15. Quality standards (role-specific quality dimensions)
16. Domain-specific standards (code/design/product/strategy as applicable)
17. System/architecture/design awareness (role-appropriate depth)
18. Data / database awareness (role-appropriate)
19. API / integration awareness (role-appropriate)
20. Testing / QA standards (role-appropriate)
21. Security basics (role-appropriate)
22. Performance / scalability awareness (role-appropriate)
23. Logging / monitoring / observability (role-appropriate)
24. Collaboration rules (cross-functional teams)
25. Documentation standards (/Aliens/Docs rule integrated)
26. Estimation / planning basics (role perspective)
27. KPIs / performance metrics
28. Interview / hiring evaluation matrix
29. Training roadmap (0 to 180 days)
30. Common mistakes and prevention
31. Escalation matrix
32. Career growth path
33. Salary ranges (indicative / global reference)
34. Tools cheat sheet
35. Glossary (easy terms)
36. Final SOP checklist

### V2.0.0 Additions — WebOS / Aliens Ecosystem alignment (role-specific subset):
Yeh sections role ke scope ke hisaab se include honge (typically 5-10 extra sections):
- WebOS / Aliens ecosystem alignment overview (role scope)
- WebApp vs WebSDK vs WebOS: role ka lens
- Aliens naming + planner awareness
- AliensStyle / AliensGrade governance
- AI Agent + Human same workflow model (role perspective)
- Role-specific WebOS sections (varies by role type)

---

## [08] Content Quality Rules (Non-Negotiable)
- Hinglish Roman only; Hindi script bilkul nahi.
- SOP style: structured, clear, actionable, beginner-friendly.
- Every major term explain karo simply.
- Tables min 5 per document (comparison, responsibility matrix, tools, KPIs, salary, etc.)
- Mermaid flowcharts min 2 per document (workflow flow, org placement, career path, etc.)
- Facts invent mat karo. Unconfirmed cheezen `Not confirmed` label karo.
- No network calls: external exact links verify nahi, to guess mat karo.
- Ultra detailed: 500+ lines minimum (most roles produce 700-1800+ lines).
- V2.0.0 sections mandatory for every role (WebOS/Aliens ecosystem alignment).
- Document MUST be production-ready — skeleton/placeholder/dummy sections forbidden.

---

## [09] Existing Roles Reference
Currently `/Aliens/Role/` me yeh roles exist karti hain (reference, not exhaustive):
- CEO, CFO, CDO, CMO, CTO, CVP
- Developer_BackEnd, Developer_FrontEnd
- Director, EVP, GM
- Marketer
- Product_Designer, Product_Manager, Product_Manager_Group, Product_Manager_Senior
- Project_Manager
- Software_Engineer, Software_Engineer_I, Software_Engineer_II, Software_Engineer_Intern, Software_Engineer_Junior, Software_Engineer_Senior, Software_Engineer_Trainee

New roles created through this workflow MUST follow the same quality bar aur format consistency.
Agar existing similar role ka document exist karta hai, to us document ko **reference** ke tor par read karo for format guidance — lekin copy/plagiarize mat karo.

---

## [10] Duplicate Prevention (Strict)
- Before creating any role SOP, check if `/Aliens/Role/{RoleSlug}.md` already exists.
- Agar file exists:
  - Do NOT overwrite.
  - Fail with `ACC_ERR_IO_FILE_EXISTS` and inform user.
  - User explicitly bole to overwrite allowed (Description me `overwrite=true` ya similar explicit instruction).
- Agar CSV mode me UTag ka file already exists and no overwrite instruction:
  - Mark row `Status=2` with reason "file already exists".
