---
WorkflowId: Course_New
WorkflowName: Aliens Course Program Creator (HIEN)
Type: Workflow_Plural
Domain: Course
Category: Professional Certificate Program Creation
Language: Markdown
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-16
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: Aliens School ke liye Professional Certificate Programs create karna.
  Agent (AlienCyborg) khud poora program structure likhta hai — Certificate, Courses, Modules, Lessons, Quizzes.
  Yeh workflow khud content nahi likhta — sirf coordination + target resolution + safety/approval enforce karta hai,
  aur child workflows ko deterministic order me call karta hai.
  Ek program = 5–12 courses, har course = 3–6 modules, har module = 5–10 lessons + quizzes.
  Output language: HIEN (Hinglish Roman).
Invocation:
  Signature:
    - Workflow("Course_New", "AI", "AI Professional Certificate program banao with 7 courses")
    - Workflow("Course_New", "Programs.csv", "Description : 1 to 5")
    - Workflow("Course_New", "Data_Science, Machine_Learning", "Create these two programs")
  TargetsMeaning: >
    3 input formats supported:
    (1) Single program slug — single program create mode.
        Example: "AI", "Data_Science", "Cybersecurity"
        Slug = Program folder name inside /Aliens/Course/HIEN/
    (2) CSV filename — batch mode with per-row tracking. CSV path:
        /Aliens/Project/{Developer_Username}/{FILENAME}.csv
    (3) Comma-separated slugs — multiple programs, ek ek karke (same pipeline each).
        Example: "AI, Data_Science"
    Slug format: PascalCase_With_Underscores (spaces → underscores).
  DescriptionMeaning: >
    Human language program context + requirements. REQUIRED — empty/missing not allowed.
    Description defines: program ka topic, target audience, number of courses (suggested),
    depth level, any specific courses to include, aur special requirements.
    Optional range suffix supported (CSV mode):
    (a) Numeric range: "Description : N to M" — rows sort karke #N se #M filter.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Course/"
    Pattern: "{ProgramSlug}.plan.md"
  Code:
    CertificatePath: "/Aliens/Certificate/Aliens_{ProgramName}_Professional_Certificate.md"
    ProgramPath: "/Aliens/Course/HIEN/{ProgramSlug}/"
    Pattern: "C{N}_{CourseName}.md + folders + modules + lessons"
  Documentation:
    Path: "/Aliens/Docs/Course/"
    Pattern: "{ProgramSlug}.doc.md"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Course_New.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Course_New-Planning
  - Course_New-Code
  - Course_New-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: false
Notes:
  - Agent (AlienCyborg) khud program content likhta hai based on topic knowledge + Description context.
  - Internet NOT required — agent apne training knowledge se likhta hai.
  - No Python script needed — agent directly .md files likhta hai.
  - Output repos:
    - Course content: /Aliens/Course/HIEN/{ProgramSlug}/ (Git repo)
    - Certificate: /Aliens/Certificate/ (Git repo)
    - Master INDEX: /Aliens/Course/INDEX.md (must be updated with new program entry)
  - Language: HIEN (Hinglish Roman) only. Hindi/Devanagari script NOT allowed in lessons.
  - Program slug = Certificate name mapping convention:
    - Slug "AI" → Certificate "Aliens_AI_Professional_Certificate.md"
    - Slug "Data_Science" → Certificate "Aliens_Data_Science_Professional_Certificate.md"
  - Hierarchy: Program → Courses (C{N}_) → Modules (M{N}_) → Lessons (L{N}_) + Quizzes (T{N}_, T_Final_)
  - Every course folder has INDEX.md listing all modules.
  - Every module listing exists both as overview .md AND as folder with lessons.
  - Naming prefix convention STRICT:
    - Courses: C{N}_ (C1_, C2_, ..., C12_)
    - Modules: M{N}_ (M1_, M2_, ..., M6_)
    - Lessons: L{N}_ (L1_, L2_, ..., L10_)
    - Quizzes: T{N}_ for mid-module, T_Final_ for final assessment
  - Provider: "Aliens Learning Foundation"
  - Department: "Aliens School"
  - Branding: "Aliens" (all courses branded as Aliens courses)
SpeedMode:
  Enabled: false
  Description: >
    SpeedMode disabled — Course programs generate massive content hierarchy.
    Planning is critical for structure consistency. Documentation is mandatory for tracking.
---

# Course_New

## [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

## [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

## [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Target resolution (CSV / comma-list / single slug → program slugs list)
- Description parsing for range filter ("Description : N to M")
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct content generate/modify is file me (child workflows karenge)
- Extra programs invent karna / unrelated scope add karna
- Description me jo nahi bola, woh add karna
- Existing Course files delete karna / overwrite karna (NoDelete=true)

## [03] Manifest Gate (Mandatory)
1) Read manifest: "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (default)
   - Code generation (course content): allowed (writes to Course repo, not prod code)

## [04] Target Resolution (Deterministic)

### 4.1 — CSV Mode
If Targets ends with `.csv`:
- Resolve: `/Aliens/Project/{Developer_Username}/{Targets}`
- CSV schema: same as standard planner CSV.
- Per-run one row (CSV single-row rule).

### 4.2 — Comma-Separated Mode
If Targets contains `,`:
- Split by comma, trim each.
- Each = Program slug.
- Process sequentially, one per child-call cycle.

### 4.3 — Single Program Mode
If Targets is single word (no comma, no `.csv`):
- Interpret as Program slug.
- Example: "AI" → process single program.

## [05] Slug Normalization
- `ProgramSlug` = Targets trimmed, PascalCase_With_Underscores.
- Slug se derived names:
  - `ProgramName` = ProgramSlug with underscores → spaces (for display).
    Example: "Data_Science" → "Data Science"
  - `CertificateName` = "Aliens_{ProgramSlug}_Professional_Certificate"
    Example: "AI" → "Aliens_AI_Professional_Certificate"

## [06] Pre-Execution Checks (Mandatory)
Before calling child workflows:
1. Check if program folder already exists: `/Aliens/Course/HIEN/{ProgramSlug}/`
   - If exists AND has content files: WARN but continue (additive mode, koi file delete nahi).
   - If exists but empty: proceed normally.
   - If not exists: create folder.
2. Check if certificate file exists: `/Aliens/Certificate/{CertificateName}.md`
   - If exists: skip certificate creation (it was created in prior run or manually).
   - If not exists: create it.

## [07] For Each Program:
Call child workflows in deterministic order:
1. `Course_New-Planning(ProgramSlug, Description)` — plan the full program structure
2. `Course_New-Code(ProgramSlug, Description)` — generate all content files
3. `Course_New-Documentation(ProgramSlug, Description)` — document what was created

## [08] CSV Schema (SSOT for Course_New)
`Programs.csv` MUST be a planner-style CSV.

### Required Columns
- `ID`
- `Name` (program display name, e.g., "AI", "Data Science")
- `UTag` (program slug for folder/file naming, e.g., "AI", "Data_Science")
- `Workflow` (MUST be `Course_New`)
- `Description` (row prompt payload; REQUIRED — program topic, scope, depth)
- `Status` (`0` pending, `1` success, `2` failed)
- `Assign` (MUST equal `{Developer_Username}`)

### Optional
- `DependsOn`
- `Priority`
- `created`, `modified`
- `CourseCount` (suggested number of courses; Planning decides actual)
- `Level` (Beginner / Intermediate / Advanced / Mixed)
- `Duration` (suggested total program duration)
- `Planning`, `Planning_Save`, `Code`, `Code_Save`, `Docs`, `Doc_Save`

If schema mismatch / required column missing:
- Fail fast with `ACC_ERR_CSV_SCHEMA_INVALID`.

## [09] CSV Row Picking Rules (Deterministic)
Runnable row conditions:
- `Status == 0`
- `Assign == {Developer_Username}`
- `DependsOn` empty OR all referenced rows have `Status == 1`

Pick strategy:
- File order me first runnable row pick karo.
- Random selection forbidden.

If no runnable row:
- Return `noop` or `blocked`.

## [10] CSV Dispatch Rules
For picked row:
- Validate:
  - `Workflow` == `Course_New`
  - `Name` non-empty
  - `UTag` non-empty
  - `Description` non-empty (else `ACC_ERR_CSV_DESCRIPTION_MISSING`)
- Execute with:
  - `Targets = Row.UTag` (slug)
  - `EffectiveDescription = "Program: " + Row.Name + "\nSlug: " + Row.UTag + "\n\n" + OuterDescription + "\n\n---\n\n" + Row.Description`

Writeback:
- Planning success => `Planning=1`, `Status` stays `0`
- Code success => `Code=1`, `Status` stays `0`
- Docs success => `Docs=1` and `Status=1`
- Any stage fail => mark that stage `2` and set `Status=2`

## [11] Output Contract (Course Program)
Program output path policy (deterministic):

### Certificate
- Path: `/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md`

### Course Content
- Program folder: `/Aliens/Course/HIEN/{ProgramSlug}/`
- Course file: `C{N}_{CourseName}.md`
- Course folder: `C{N}_{CourseName}/`
  - Index: `INDEX.md`
  - Module file: `M{N}_{ModuleName}.md`
  - Module folder: `M{N}_{ModuleName}/`
    - Lesson file: `L{N}_{LessonName}.md`
    - Mid-quiz: `T{N}_Mid_Module_Quiz.md` (optional, 1 per module max)
    - Final: `T_Final_Module_Assessment.md` (1 per module)

### Master INDEX Update
- Update `/Aliens/Course/INDEX.md` with new program entry (additive, no existing content deleted).

### Planning
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md`

### Documentation
- Path: `/Aliens/Docs/Course/{ProgramSlug}.doc.md`
