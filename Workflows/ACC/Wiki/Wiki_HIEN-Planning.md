---
WorkflowId: Wiki_HIEN-Planning
WorkflowName: Aliens Wiki Article Planner (HIEN)
Type: Workflow_Singular-Planning
ParentWorkflowId: Wiki_HIEN
Domain: Wiki
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-27
Inputs:
  - Targets: "TopicName (single only)"
  - Description: "REQUIRED; topic context + constraints"
Produces:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Wiki/HIEN/{TopicSlug}.plan.md"
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
  - OneTopicOnly: true
  - Language: "Hinglish (Roman). Hindi script not allowed."
  - WikipediaStyle: true
  - NoFactInvention: true
---

# Wiki_HIEN-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [01] Purpose (Hinglish)
Yeh workflow ek single wiki topic ke liye **article planning / outline** banata hai.
Planning ka goal:
- Article ko Wikipedia style me **deterministic structure** dena (sections + tables + diagrams)
- Beginner-friendly Hinglish language plan karna
- Images/tables/flowcharts ka plan banana
- Assumptions aur missing info ko clearly mark karna (no guessing)

> Note: Planning ka output final article nahi hota. Final article `Wiki_HIEN-Documentation` banayega.

---

## [02] Inputs Rules (Strict)
### Targets
- `Targets` MUST be a single TopicName.
- Comma-separated targets yahan allowed nahi.

If multiple targets detected:
- Fail fast with `ACC_ERR_ARGS_INVALID`.

### Description
- Required hai.
- Description me agar outer workflow (CSV mode) ka context diya gaya ho, to is planning me **same as-is** include karo.

---

## [03] Topic Normalization (Deterministic)
### 3.1 Title
- `TopicTitle` = Targets trimmed.
- Case preserve.

### 3.2 TopicSlug (for filenames)
- If `Description` contains a line like `UTag: <value>` (case-insensitive key):
  - Use that `<value>` as `TopicSlug` (after safe normalization below).
- Else:
  - Derive `TopicSlug` from `TopicTitle`.

Normalization rules (apply in both cases):
- Spaces -> `-`
- `/` -> `-`
- `:` -> `-`
- Multiple `-` collapse.
- Keep letters/numbers/`-`/`_`/`.`.

Example:
- `Web Browser` -> `Web-Browser`
- `Browser` -> `Browser`

---

## [04] Planning Output Path (SSOT)
- Planning doc MUST be saved at:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Wiki/HIEN/{TopicSlug}.plan.md`

---

## [05] Planning Document Format (Mandatory)
Planning doc MUST contain these sections (in order):

1) Title + Metadata
2) Prompt Payload Snapshot
3) Audience + Reading Level
4) Wikipedia-style Structure Plan
5) Infobox Plan (fields list)
6) Section Outline (H2/H3)
7) Tables Plan (at least 2)
8) Diagrams Plan (Mermaid) (at least 1)
9) Images Plan (Wikimedia Commons only)
10) Terminology Plan (Glossary list: 15+ terms)
11) Internal Link Plan (related Aliens Wiki topics)
12) Assumptions + Unknowns (strict)
13) Acceptance Criteria (what “done” means)

---

## [06] Content Rules (Non-Negotiable)
- Hinglish Roman only; Hindi script bilkul nahi.
- Beginner-friendly: explain every major term.
- Honesty: agar exact fact confirm nahi, explicitly label it as `Not confirmed` / `Needs verification`.
- No plagiarism: content original phrasing me ho.

---

## [07] Acceptance Criteria (Planning)
Planning success tabhi:
- Output file exists and non-empty
- All 13 sections present
- Infobox fields list present
- At least 2 tables + 1 mermaid diagram planned
- Assumptions section present
