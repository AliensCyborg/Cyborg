---
WorkflowId: Course_New-Documentation
WorkflowName: Aliens Course Program Documentation
Type: Workflow_Singular-Documentation
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
  - Description: "REQUIRED; program topic context"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Course/{ProgramSlug}.plan.md"
  - CertificateFile: "/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md"
  - ProgramFolder: "/Aliens/Course/HIEN/{ProgramSlug}/"
Produces:
  - DocumentationDoc: "/Aliens/Docs/Course/{ProgramSlug}.doc.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
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
  - AllCoursesMustExist: true
---

# Course_New-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`

---

## [01] Purpose (Hinglish)
Is workflow ka kaam: Course program creation ka **documentation record** banana.
Yeh content generate hone ke baad chalta hai — verify karta hai ke poora program hierarchy
correctly create hua hai aur ek summary doc create karta hai for future reference + auditing.

---

## [02] Inputs Rules (Strict)

### Targets
- Single program slug only.
- Multiple targets => `ACC_ERR_ARGS_INVALID`.

### Description
- Required. Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Documentation Output Path (SSOT)
- Documentation MUST be saved at:
  - `/Aliens/Docs/Course/{ProgramSlug}.doc.md`

---

## [04] Pre-Documentation Verification (Mandatory)
Before writing documentation, verify all expected artifacts exist:

### 4.1 Certificate Check
- Verify `/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md` exists.
- Record: file size, exists/not.

### 4.2 Program Structure Check
- Scan `/Aliens/Course/HIEN/{ProgramSlug}/` recursively.
- Count: course files, course folders, module files, module folders, lesson files, quiz files, INDEX files.
- Record totals.

### 4.3 Planning File Check
- Verify planning doc exists.
- Compare planned file count vs actual file count.

### 4.4 Master INDEX Check
- Verify program entry exists in `/Aliens/Course/INDEX.md`.

---

## [05] Documentation Format (Mandatory)
Documentation file MUST contain these sections:

### 1) Header + Metadata
```markdown
# Course Program Documentation: {ProgramName}

> **Program:** {ProgramName}
> **Slug:** {ProgramSlug}
> **Created:** {Date}
> **Workflow:** Course_New
> **Author:** AlienCyborg / Aliens Learning Foundation
```

### 2) Program Summary
- Program name, level, duration, course count
- Target audience
- 2-3 line summary of what the program covers

### 3) Generation Results
- Total files generated (by type):
  - Certificate files: X
  - Course overview files: X
  - Course INDEX files: X
  - Module overview files: X
  - Lesson files: X
  - Quiz/Assessment files: X
  - **Total: X files**
- Planned vs actual comparison
- Any files skipped (already existed)
- Any files failed

### 4) Program Structure Map
Complete tree of generated files:
```
/Aliens/Certificate/Aliens_{ProgramSlug}_Professional_Certificate.md
/Aliens/Course/HIEN/{ProgramSlug}/
  C1_{CourseName}.md
  C1_{CourseName}/
    INDEX.md
    M1_{ModuleName}.md
    M1_{ModuleName}/
      L1_{LessonName}.md
      ...
      T_Final_Module_Assessment.md
    ...
  ...
```

### 5) Course Summary Table
| # | Course | Modules | Lessons | Quizzes | Status |
|---|---|---|---|---|---|
| 1 | {CourseName} | {M} | {L} | {Q} | Created |
| 2 | ... | ... | ... | ... | ... |

### 6) Quality Metrics
- Average lessons per module
- Average modules per course
- Total content depth score (based on lesson content length)
- Link integrity: all cross-references valid (checked/not-checked)
- Language compliance: HIEN only (verified/not-verified)

### 7) Cross-Reference Verification
- Certificate → Courses: links valid (Y/N per course)
- Courses → Certificate: back-links valid (Y/N per course)
- Courses → Modules: links valid
- Modules → Lessons: links valid
- Lessons → Module back-links: valid
- Master INDEX → Program entry: valid

### 8) Known Issues / Warnings
- Any deviations from plan
- Any content that needs review
- Any links that could not be verified
- Any assumptions made during generation

### 9) Prompt Payload Snapshot
- Original Description (for traceability)

---

## [06] Acceptance Criteria (Documentation)
Documentation success tabhi:
- Doc file exists and non-empty at correct path
- All 9 sections present
- File count summary accurate
- Structure map complete
- Course summary table complete
- At least basic quality metrics reported
