---
WorkflowId: Course_New-Planning
WorkflowName: Aliens Course Program Planner
Type: Workflow_Singular-Planning
ParentWorkflowId: Course_New
Domain: Course
Category: Professional Certificate Program Creation
Language: Markdown
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-16
EntryPoint: false
Inputs:
  - Targets: "ProgramSlug (single only)"
  - Description: "REQUIRED; program topic + scope + requirements"
Produces:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md"
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
  - OneProgramOnly: true
  - StructureMustMatchExistingPatterns: true
  - HierarchyComplete: true
  - InternetRequired: false
---

# Course_New-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [01] Purpose (Hinglish)
Yeh workflow ek single Professional Certificate Program ke liye **complete planning / blueprint** banata hai.
Planning ka goal:
- Program ka poora structure define karna (courses, modules, lessons, quizzes)
- Har course ka scope + module breakdown plan karna
- Har module ke lessons plan karna (topics, types, durations)
- Certificate metadata plan karna
- File naming + folder structure plan karna (exact paths)
- Consistency ensure karna existing Course repo patterns ke saath

> Note: Planning ka output final content nahi. Final content `Course_New-Code` likhega.

---

## [02] Inputs Rules (Strict)

### Targets
- `Targets` MUST be a single program slug.
- Multiple targets (comma-separated) yahan allowed nahi.
- If multiple targets detected: fail fast with `ACC_ERR_ARGS_INVALID`.

### Description
- REQUIRED hai. Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.
- Description me program ka topic, audience, suggested course count, depth level sab aana chahiye.
- Agar outer workflow (CSV mode) ka context diya gaya ho, use as-is include karo.

---

## [03] Slug Normalization (Deterministic)

### 3.1 Program Slug
- `ProgramSlug` = Targets trimmed. Already PascalCase_With_Underscores.
- Case preserve.

### 3.2 Program Name Extraction
- If `Description` contains `Program: <value>` (case-insensitive key):
  - Use that `<value>` as human-readable `ProgramName`.
- Else:
  - Derive `ProgramName` from `ProgramSlug`: underscores → spaces.
  - Example: `Data_Science` → `Data Science`

### 3.3 Certificate Name
- `CertificateName` = `Aliens_{ProgramSlug}_Professional_Certificate`
- Display: `Aliens {ProgramName} Professional Certificate`

---

## [04] Planning Output Path (SSOT)
- Planning doc MUST be saved at:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md`

---

## [05] Research Phase (Before Planning)
Before writing the plan, agent MUST:

### 5.1 Study Existing Programs
- Read 2–3 existing program structures from `/Aliens/Course/HIEN/` to calibrate:
  - Course count patterns (typical: 5–10 courses per program)
  - Module count patterns (typical: 3–6 modules per course)
  - Lesson count patterns (typical: 5–10 lessons per module)
  - Naming conventions (C{N}_, M{N}_, L{N}_, T{N}_)
  - Content depth + style (Hinglish Roman, beginner-friendly)

### 5.2 Check Existing Certificate References
- Read `/Aliens/Certificate/` to verify naming conventions.
- If certificate for this program already exists, note it as "skip creation".

### 5.3 Check Related Programs
- Look at similar/related existing programs for consistency.
  Example: If creating "Advanced_AI", check if "AI" program exists for cross-referencing.

---

## [06] Planning Document Format (Mandatory)
Planning doc MUST contain these sections (in order):

### 1) Program Metadata
- Program name, slug, date, provider, department
- Level (Beginner / Intermediate / Advanced / Mixed)
- Estimated total duration (months)
- Target audience description
- Certificate file path

### 2) Prompt Payload Snapshot
- Full Description as-is (for traceability)

### 3) Program Overview
- What the program covers (2-3 paragraphs)
- Learning outcomes (5-10 bullet points)
- Career paths after completion (3-5 roles)
- Prerequisites (if any)

### 4) Course Structure Plan (CRITICAL SECTION)
For EACH course in the program:

```
Course {N}: {CourseName}
- Slug: C{N}_{CourseName_Slug}
- Level: Beginner / Intermediate / Advanced
- Duration: X weeks (Y hours/week)
- Modules: {count}
- Description: 2-3 lines about what this course covers
- Key Topics: bullet list of main topics
```

Minimum: 5 courses per program (unless Description explicitly says fewer).
Maximum: 12 courses per program.
Courses MUST follow a logical learning progression (foundational → intermediate → advanced → capstone).

### 5) Module Breakdown Plan (Per Course)
For EACH course, list ALL modules:

```
Course {N}: {CourseName}
  Module {M}: {ModuleName}
  - Slug: M{M}_{ModuleName_Slug}
  - Duration: ~X hours
  - Lessons: {count}
  - Key Topics: brief list
  - Quiz: Yes/No (mid-module), Final Assessment: Yes
```

Minimum: 3 modules per course.
Maximum: 6 modules per course.

### 6) Lesson Framework Plan (Per Module)
For EACH module, plan the lessons:

```
Module {M}: {ModuleName}
  Lesson {L}: {LessonTitle}
  - Slug: L{L}_{LessonTitle_Slug}
  - Type: Video / Reading / Video + Reading / Activity / Quiz
  - Duration: ~X min
  - Key Concept: 1-line summary
```

Minimum: 5 lessons per module.
Maximum: 10 lessons per module.
Last lesson MUST be "Module Review aur Practice Quiz".
Each module MUST have T_Final_Module_Assessment.md planned.
Mid-module quiz (T1_Mid_Module_Quiz.md) optional but recommended for modules with 6+ lessons.

### 7) File Structure Plan (Complete Path Map)
List EVERY file that will be created, with full relative path from `/Aliens/Course/HIEN/`:

```
{ProgramSlug}/
  C1_{CourseName}/
    INDEX.md
    M1_{ModuleName}.md
    M1_{ModuleName}/
      L1_{LessonName}.md
      L2_{LessonName}.md
      ...
      T_Final_Module_Assessment.md
    M2_{ModuleName}.md
    M2_{ModuleName}/
      ...
  C1_{CourseName}.md
  C2_{CourseName}.md
  C2_{CourseName}/
    ...
```

Total file count estimate MUST be provided.

### 8) Cross-Reference Plan
- Certificate link pattern (from course → certificate)
- Course-to-course navigation links
- Module-to-course back-links
- Lesson-to-module back-links
- INDEX.md content plan per course folder
- Master INDEX.md update plan (new program section entry)

### 9) Naming Convention Verification
- All slugs verified against naming rules:
  - No spaces in slugs (only underscores)
  - PascalCase preferred
  - C{N}_ prefix for courses, M{N}_ for modules, L{N}_ for lessons
  - Descriptive slug names (not just numbers)
  - No special characters except underscores

### 10) Content Quality Guidelines
- Language: HIEN (Hinglish Roman — romanized; Hindi/Devanagari script NOT allowed)
- Tone: Conversational, beginner-friendly (like talking to a friend/junior colleague)
- Technical terms: stay English, explained in simple Hinglish
- Every lesson MUST have: Overview, content sections, Key Takeaways, Practice Quiz
- Every module MUST have: Overview, Lessons table, Quizzes section
- Every course MUST have: Overview, Modules table, Certificate Program table

### 11) Assumptions + Scope Boundaries
- What facts need verification (mark as "assumed")
- What the program explicitly does NOT cover
- Dependencies on other programs (if any)
- Open questions for agent to decide during Code phase

### 12) Acceptance Criteria
- What "done" means for this program:
  - Certificate file created
  - All course overview files created
  - All course folders with INDEX.md created
  - All module overview files created
  - All module folders with lesson files created
  - All quizzes/assessments created
  - Master INDEX.md updated
  - File count matches plan estimate (±5%)

---

## [07] Content Rules (Non-Negotiable)
- Educational style — structured, progressive, beginner-friendly.
- Hinglish Roman only (no Devanagari, no pure Hindi script).
- Content MUST be factually accurate for technical topics.
- Agar exact fact confirm nahi, explicitly label as "Assumed" in plan.
- No plagiarism: all content must be original phrasing.
- Depth proportional to Description requirements.
- Every program MUST have a capstone/final course as the last course.

---

## [08] Acceptance Criteria (Planning)
Planning success tabhi:
- Output file exists and non-empty at correct path
- All 12 sections present
- At least 5 courses planned
- At least 3 modules per course planned
- At least 5 lessons per module planned
- Complete file structure path map present
- Naming conventions verified
- Total file count estimated
- Certificate metadata planned
