---
WorkflowId: Course_New-Code
WorkflowName: Aliens Course Program Content Generator
Type: Workflow_Singular-Code
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
  - Description: "REQUIRED; program topic + requirements"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md"
Produces:
  - CertificateFile: "/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md"
  - ProgramFolder: "/Aliens/Course/HIEN/{ProgramSlug}/"
  - CourseFiles: "/Aliens/Course/HIEN/{ProgramSlug}/C{N}_{CourseName}.md"
  - CourseFolders: "/Aliens/Course/HIEN/{ProgramSlug}/C{N}_{CourseName}/"
  - ModuleFiles: ".../{CourseFolder}/M{N}_{ModuleName}.md"
  - ModuleFolders: ".../{CourseFolder}/M{N}_{ModuleName}/"
  - LessonFiles: ".../{ModuleFolder}/L{N}_{LessonName}.md"
  - QuizFiles: ".../{ModuleFolder}/T{N}_Mid_Module_Quiz.md, T_Final_Module_Assessment.md"
  - IndexFiles: ".../{CourseFolder}/INDEX.md"
  - MasterIndexUpdate: "/Aliens/Course/INDEX.md (additive update)"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
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
  - PlanningDependency: true
  - InternetRequired: false
  - LanguageHIEN: true
  - HierarchyComplete: true
  - NamingConventionStrict: true
  - ExistingContentPreserved: true
---

# Course_New-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [01] Purpose (Hinglish)
Is workflow ka kaam: ek single Professional Certificate Program ka **poora content generate karna**.
Agent (AlienCyborg) khud HIEN me content likhta hai — koi script, koi API, koi scraping nahi.
Planning doc ko follow karke enterprise-grade, educational, structured course content create karna.
Yeh workflow potentially 100+ files generate karta hai (Certificate + Courses + Modules + Lessons + Quizzes + INDEXes).

---

## [02] Inputs Rules (Strict)

### Targets
- Single program slug only.
- Multiple targets (comma) => `ACC_ERR_ARGS_INVALID`.

### Slug
- `ProgramSlug` = Targets trimmed. Already PascalCase_With_Underscores.

### Program Name
- If `Description` contains `Program: <value>`: use that as `ProgramName`.
- Else: derive from slug (underscores → spaces).
  Example: `Data_Science` → `Data Science`

### Certificate Name
- `CertificateName` = `Aliens_{ProgramSlug}_Professional_Certificate`

### Description
- Required. Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Planning Dependency (Mandatory)
- Planning file read karo:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md`

If missing:
- Fail fast with `ACC_ERR_PLANNING_GENERATION_FAILED`.

Planning doc ko SSOT treat karo for:
- Course list (names, slugs, order)
- Module breakdown per course
- Lesson plan per module
- File structure map
- Naming conventions
- Content quality guidelines

---

## [04] Execution Order (STRICT — Top-Down)
Files MUST be generated in this EXACT order:

### Phase 1: Certificate
1. Create Certificate file: `/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md`
   - Skip if already exists.

### Phase 2: Course Overview Files (all courses first)
2. For each course (C1 → C{N}):
   - Create course overview: `/Aliens/Course/HIEN/{ProgramSlug}/C{N}_{CourseName}.md`

### Phase 3: Course Folders + INDEX + Modules + Lessons (per course)
3. For each course (C1 → C{N}):
   a. Create course folder: `/Aliens/Course/HIEN/{ProgramSlug}/C{N}_{CourseName}/`
   b. For each module (M1 → M{M}):
      - Create module overview: `C{N}_{CourseName}/M{M}_{ModuleName}.md`
      - Create module folder: `C{N}_{CourseName}/M{M}_{ModuleName}/`
      - For each lesson (L1 → L{L}):
        - Create lesson file: `M{M}_{ModuleName}/L{L}_{LessonTitle}.md`
      - Create mid-module quiz (if planned): `M{M}_{ModuleName}/T1_Mid_Module_Quiz.md`
      - Create final assessment: `M{M}_{ModuleName}/T_Final_Module_Assessment.md`
   c. Create course INDEX.md: `C{N}_{CourseName}/INDEX.md`

### Phase 4: Master INDEX Update
4. Update `/Aliens/Course/INDEX.md` — add new program section (additive, no existing content deleted).

---

## [05] Certificate File Format (Template)
Certificate .md file MUST follow this exact structure:

```markdown
# Aliens {ProgramName} Professional Certificate

> **Certificate Provider:** Aliens Learning Foundation
> **Department:** Aliens School
> **Level:** {Level}
> **Duration:** Lagbhag {Duration} ({StudyHours} recommended)
> **Courses in Program:** {CourseCount}

---

## Iske Baare Me

{2-3 paragraphs describing the program, what learners will achieve, career outcomes}

---

## Kya Seekhenge

{10-15 bullet points of learning outcomes}

---

## Program Ka Syllabus

### Course 1: [{CourseTitle}](../Course/HIEN/{ProgramSlug}/C1_{CourseSlug}.md)
{4-6 bullet points of topics}

### Course 2: ...
...

---

## Skills Jo Seekhenge

{Grid/list of practical skills — tools, concepts, frameworks}

---

## Kis Ke Liye Hai

{Target audience description — experience level, background, goals}

---

## Career Opportunities

{3-5 roles with brief descriptions that this certificate prepares for}

---

*© Aliens Learning Foundation — Aliens School*
```

---

## [06] Course Overview File Format (C{N}_.md)
Each course overview MUST follow this structure:

```markdown
# {CourseTitle}

> **Course {N} of {Total}** in the [Aliens {ProgramName} Professional Certificate](../../Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md)
> **Provider:** Aliens Learning Foundation
> **Department:** Aliens School
> **Level:** {Level}
> **Duration:** Lagbhag {Weeks} hafte (hafta me {Hours} ghante)

---

## Iske Baare Me

{1-2 paragraphs about what this course covers and why it matters}

---

## Kya Seekhenge

{5-8 bullet points of learning outcomes}

---

## Modules

| Module | Naam | Dekho |
|---|---|---|
| Module 1 | {ModuleName} | [Dekho](C{N}_{CourseSlug}/M1_{ModuleSlug}.md) |
| Module 2 | ... | ... |
...

> 📂 Detailed modules aur lessons ke liye [C{N}_{CourseSlug}/](C{N}_{CourseSlug}/) folder dekho.

---

## Certificate Program

| Course | Name |
|---|---|
| **→ Course {N}** | **{CourseTitle}** (yeh course) |
| Course X | [{OtherCourseTitle}]({OtherCourseFile}.md) |
...

---

*© Aliens Learning Foundation — Aliens School*
```

---

## [07] Course INDEX.md Format
Each course folder's INDEX.md MUST follow this structure:

```markdown
# {CourseTitle} — Modules

> **Course:** [{CourseTitle}](../C{N}_{CourseSlug}.md)
> **Course {N} of {Total}** in the [Aliens {ProgramName} Professional Certificate](../../../Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md)
> **Provider:** Aliens Learning Foundation — Aliens School

---

## About

Yeh folder "{CourseTitle}" course ke detailed modules aur lessons contain karta hai.

---

## Modules

| Module | Naam | Dekho |
|---|---|---|
| Module 1 | {ModuleName} | [Dekho](M1_{ModuleSlug}.md) |
| Module 2 | ... | ... |
...

---

## Kya Seekhoge

{4-6 key learning outcomes}

---

## Certificate Program

{Same course navigation table as in course overview}

---

*© Aliens Learning Foundation — Aliens School*
```

---

## [08] Module Overview File Format (M{M}_.md)
Each module overview MUST follow this structure:

```markdown
# Module {M}: {ModuleTitle}

> **Course:** [{CourseTitle}](../C{N}_{CourseSlug}.md)
> **Module {M} of {TotalModules}** · Approx. {Hours} hours
> **Provider:** Aliens Learning Foundation — Aliens School

---

## Iske Baare Me

{1-2 paragraphs about what this module covers}

---

## Lessons

### Lesson 1: {LessonTitle}
- **Type:** {Video / Reading / Video + Reading / Activity}
- **Duration:** ~{Minutes} min

{2-4 lines describing what this lesson covers — specific topics, concepts, examples}

---

### Lesson 2: ...
...

---

### Lesson {Last}: Module {M} Review aur Practice Quiz
- **Type:** Quiz
- **Duration:** ~15 min

Module {M} ka review — {topics summary}. Quiz me matching, multiple choice, aur scenario-based questions honge.

---

## Quizzes & Assessments

| Assessment | Type | Topics Covered |
|---|---|---|
| {QuizName} | Graded Quiz | {Topics} |
| {ActivityName} | Practice Activity | {Topics} |

---

*© Aliens Learning Foundation — Aliens School*
```

---

## [09] Lesson File Format (L{L}_.md)
Each lesson MUST follow this structure:

```markdown
# Lesson {L}: {LessonTitle}

> **Module:** {ModuleTitle}
> **Course:** {CourseTitle}
> **Program:** {ProgramSlug}
> **Type:** {Video / Reading / Video + Reading / Activity} | **Duration:** ~{Minutes} min
> **Provider:** Aliens Learning Foundation — Aliens School

---

## Overview

Is lesson me hum {LessonTitle} ke baare me detail se seekhenge. Topics include: {brief list of what this lesson covers}. Yeh sab concepts {ModuleTitle} module ke core building blocks hain.

---

## Seekhne Ka Maqsad

Is lesson complete karne ke baad tum:
- **{Learning outcome 1}** ko detail me samajh paoge aur apply kar sakoge
- **{Learning outcome 2}** ke concepts clearly samajh aayenge
- {More outcomes as needed}

---

## {Content Type} Content

### {Topic 1 Heading}

{Detailed explanation — 3-5 paragraphs covering the concept.
Use examples, analogies, real-world scenarios.
Technical terms stay English, explanations in Hinglish.
Be conversational — like teaching a friend.}

### {Topic 2 Heading}

{Same depth and style}

### {More topics as needed}

---

## Key Takeaways

- **{Takeaway 1}** — {brief explanation}
- **{Takeaway 2}** — {brief explanation}
- {3-5 total takeaways}

---

## Practice Quiz

**Q1:** {Question about lesson content}
- a) {Option}
- b) {Option} ✓
- c) {Option}
- d) {Option}

**Q2:** {Another question}
- a) {Option}
- b) {Option} ✓
- c) {Option}
- d) {Option}

---

> **Next →:** Lesson {L+1}
> **Module:** [{ModuleTitle}](../M{M}_{ModuleSlug}.md)

---

*© Aliens Learning Foundation — Aliens School*
```

### Lesson Content Depth Guidelines
- Video type lessons: 3-5 detailed subsections, each 2-4 paragraphs.
- Reading type lessons: 4-6 subsections, each 2-3 paragraphs.
- Activity type lessons: scenario description + step-by-step instructions.
- Quiz/Review lessons: comprehensive review + 5-7 quiz questions.

---

## [10] Quiz File Formats

### Mid-Module Quiz (T1_Mid_Module_Quiz.md)
```markdown
# Mid-Module Quiz: {ModuleTitle}

> **Module:** {ModuleTitle}
> **Course:** {CourseTitle}
> **Program:** {ProgramSlug}
> **Type:** Graded Quiz | **Duration:** ~15 min
> **Provider:** Aliens Learning Foundation — Aliens School

---

## Quiz Overview

Yeh quiz {ModuleTitle} ke pehle lessons ka assessment hai. Is quiz me un topics ko test kiya jayega jo tum ne abhi tak seekhe hain. Apni understanding verify karo aur weak areas identify karo.

---

## Instructions

- Sab questions attempt karo
- Har question me sirf ek correct answer hai
- 70% ya usse zyada score pass hone ke liye zaruri hai
- Agar pass nahi hue to relevant lessons review karo aur dobara try karo

---

*© Aliens Learning Foundation — Aliens School*
```

### Final Module Assessment (T_Final_Module_Assessment.md)
```markdown
# Final Module Assessment: {ModuleTitle}

> **Module:** {ModuleTitle}
> **Course:** {CourseTitle}
> **Program:** {ProgramSlug}
> **Type:** Graded Assessment | **Duration:** ~30 min
> **Provider:** Aliens Learning Foundation — Aliens School

---

## Assessment Overview

Yeh {ModuleTitle} module ka final assessment hai. Is me poore module ke concepts, practical applications, aur critical thinking test hogi. Isko pass karna next module me jaane ke liye zaruri hai.

---

## Instructions

- Sab questions attempt karo
- Mix of MCQ, short answer, aur scenario-based questions
- 80% ya usse zyada score pass hone ke liye zaruri hai
- 2 attempts allowed; best score count hoga
- Agar pass nahi hue to poora module review karo

---

*© Aliens Learning Foundation — Aliens School*
```

---

## [11] Content Quality Rules (Non-Negotiable)

### Language
- HIEN (Hinglish Roman) — Romanized Hindi-English mix.
- Hindi/Devanagari script STRICTLY NOT allowed anywhere.
- Technical terms stay English: "algorithm", "function", "database", "API", etc.
- Non-technical explanations in Hinglish: "samjhna", "seekhna", "karna", etc.

### Tone
- Educational but conversational — like a knowledgeable friend teaching.
- Beginner-friendly: assume no prior knowledge (unless course level says otherwise).
- Use examples, analogies, real-world scenarios to explain concepts.
- No dry academic language — keep it engaging.

### Structure
- Progressive difficulty: each lesson builds on previous.
- No forward references (don't explain concept X using concept Y that comes later).
- Every lesson self-contained enough to make sense on its own.
- Cross-references to related lessons where helpful.

### Accuracy
- Factual accuracy critical for technical content.
- If unsure about specific detail: omit rather than invent.
- Version-specific info (software versions, dates): use latest known accurate info.

### Completeness
- Every lesson MUST have: Overview, Seekhne Ka Maqsad, Content, Key Takeaways, Practice Quiz.
- Every module MUST have: Overview, Lessons list with descriptions, Quizzes section.
- Every course MUST have: Overview, Modules table, Certificate Program navigation.
- No placeholder/skeleton content — every file must have real, substantial content.

---

## [12] File Creation Rules (Safety)

### Existing Files
- If a file already exists at target path: DO NOT overwrite.
- Skip that file and note it in run record as "skipped — already exists".
- NoDelete applies: never remove existing files.

### Directory Creation
- Create directories as needed (course folders, module folders).
- Do not create empty directories without files.

### Verification (Per File)
- After creating each file, verify:
  - File exists on disk
  - File has substantial content (not just headers/empty template)
  - Markdown is well-formed (headings, lists, tables, links)

---

## [13] Master INDEX.md Update Rules
- Read current `/Aliens/Course/INDEX.md`.
- Find appropriate insertion point (programs are grouped in Series sections).
- Add new program section:
  - Program heading with course count
  - Table of all courses (same format as existing entries)
  - Certificate link
- ADDITIVE only: do not modify or delete any existing content.
- If program section already exists in INDEX.md: skip update, note as "already indexed".

---

## [14] Acceptance Criteria (Code)
Code success tabhi:
- Certificate file created (or verified already exists)
- All course overview files created (C{N}_.md)
- All course folders created with INDEX.md
- All module overview files created (M{M}_.md)
- All module folders created with lesson files (L{L}_.md)
- All quiz/assessment files created (T{N}_, T_Final_)
- Every file has substantial content (not skeleton/placeholder)
- Master INDEX.md updated with new program entry
- File count matches planning estimate (±5%)
- No Devanagari script anywhere in any file
- All cross-reference links valid (relative paths)
