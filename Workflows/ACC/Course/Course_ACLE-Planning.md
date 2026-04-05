---
WorkflowId: Course_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Course
Category: Video Lesson Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-10
EntryPoint: false
ParentWorkflow: Course_ACLE
Purpose: >
  Singular Planning workflow: given a single program name, ek complete conversion plan banao
  jo describe kare ke us program ke kitne courses, modules, aur lessons hain, kaunse already
  converted hain, kaunse pending hain, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Course_ACLE-Planning", "AI_Developer", "Convert all lessons to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single program name without extension/path. Comma list is invalid (singular workflow).
    Example: "AI_Developer" implies source folder "/Aliens/Course/AI_Developer/".
    Note: Range filtering ("1 to 10") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved program name.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/"
    Pattern: "{ProgramName}.plan.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Notes:
  - Source is READ-ONLY: `/Aliens/Course/{ProgramName}/`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual program structure (courses/modules/lessons) from disk.
  - Plan must check existing conversion status (already converted vs pending).
---

# Course_ACLE-Planning

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Planning.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Jo Description + Parent workflow me define ho, sirf wohi plan me include karo.
- Extra programs add karna forbidden.
- Source Course repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE program name (example: "AI_Developer")
- Comma-separated list => error (`WF_TARGETS_INVALID`)
- Extensions forbidden (`.md`, `.html`, `.csv`)
- Paths forbidden (sirf name, no slashes)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"

Minimum checks:
- Planning output write permission (allowed by default)
- Approval policy: manual => approval before saving planning file
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Target Validation
1) ProgramName trim karo
2) Source folder resolve karo: `/Aliens/Course/{ProgramName}/`
3) Agar folder missing => fail with `WF_TARGET_MISSING`
4) Agar folder exist => proceed to discovery

# [07] Discovery Phase (Evidence Pack — Mandatory)
Planning generate karne se pehle yeh discovery steps complete karo:

## [07.1] Program Structure Discovery
Scan source folder recursively:
```
/Aliens/Course/{ProgramName}/
  └── C{N}_{CourseName}/
       └── M{N}_{ModuleName}/
            └── L{N}_{LessonName}.md
```

Collect:
- Total courses count
- Total modules count (per course aur grand total)
- Total lessons count (per module, per course, aur grand total)
- Lesson file paths list (full inventory)

## [07.2] Conversion Status Discovery
For each lesson MD file, check corresponding output:
```
/Aliens/Course_ACLE/{ProgramName}/C{N}_{CourseName}/M{N}_{ModuleName}/
  ├── L{N}_{LessonName}.html (must exist, size > 5KB)
  └── audio_L{N}_{LessonName}/ (must exist, 3+ mp3 files inside)
```

Categories:
- **Already Converted**: HTML > 5KB AND audio dir with 3+ mp3s
- **Partially Converted**: HTML exists but < 5KB, OR audio dir exists but < 3 mp3s
- **Pending**: no HTML or no audio dir

Collect counts:
- Already converted lessons
- Partially converted lessons (need re-conversion)
- Pending lessons (fresh conversion needed)

## [07.3] Environment Check
Verify:
- Python availability (3.10+)
- `edge-tts` package installed
- Converter scripts exist:
  - `/Aliens/Cyborg/AlienCyborg/Code/convert_lesson.py`
  - `/Aliens/Cyborg/AlienCyborg/Code/batch_all_programs.py`
- Shared assets exist:
  - `/Aliens/Course_ACLE/assets/css/aliens-cinematic.css`
  - `/Aliens/Course_ACLE/assets/js/aliens-cinematic.js`
  - `/Aliens/Course_ACLE/assets/svg/aliens-logo-defs.svg`
- Internet connectivity (edge-tts requirement)

## [07.4] Risk Assessment
Evaluate:
- Estimated conversion time (lessons × 2-5 min avg)
- Internet dependency risk (TTS failures possible)
- Disk space requirement estimate (avg ~500KB per lesson output)
- Large program warning (500+ lessons = significant batch time)

# [08] Planning Output Template (Mandatory Sections)
Planning file MUST contain these sections in order:

## Section 1: Overview
- Program name
- Description (from workflow input)
- Timestamp
- Mode (New conversion)

## Section 2: Inputs
- WorkflowId: Course_ACLE-Planning
- Target: {ProgramName}
- Description: {Description}
- ParentWorkflowId: Course_ACLE (if called from Plural)

## Section 3: Resolved Context
- Developer username (from manifest)
- Approval mode (auto/manual)
- Source path: `/Aliens/Course/{ProgramName}/`
- Output path: `/Aliens/Course_ACLE/{ProgramName}/`
- Planning output path

## Section 4: Program Structure (Discovery Results)
- Course list with lesson counts
- Module list per course
- Total inventory: X courses, Y modules, Z lessons

## Section 5: Conversion Status Analysis
- Already converted: N lessons (X%)
- Partially converted: N lessons (X%) — list files
- Pending conversion: N lessons (X%) — list first 50, then summary
- Overall progress: X% complete

## Section 6: Dependencies
- Python 3.10+ (status: verified/missing)
- edge-tts package (status: verified/missing)
- Converter scripts (status: verified/missing)
- Shared assets (status: verified/missing)
- Internet connectivity (status: verified/unknown)

## Section 7: Execution Strategy
- Recommended mode: Single program (`--program {ProgramName}`)
- Estimated duration: X hours (based on pending lessons × avg time)
- Skip logic: already-converted lessons will be skipped automatically
- Retry strategy: edge-tts 3-retry built into converter
- Auto-resume: if process stops, same command resumes from where it left off

## Section 8: Step-by-Step Plan
Concrete measurable steps for the Code workflow to follow:

1. Verify environment (Python + edge-tts + scripts + assets)
2. Verify source program folder exists and has lesson files
3. Verify output directory structure exists (or will be auto-created)
4. Execute batch conversion: `python batch_all_programs.py --program {ProgramName}`
5. Monitor progress via `batch_progress.json` (`/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`)
6. After completion: verify per-lesson outputs (HTML + audio)
7. Report: total converted, total failed, total skipped
8. Handle failures: log failed lessons, suggest retry

## Section 9: Safety Rules
- Source READ-ONLY (no modifications to `/Aliens/Course/`)
- NoDelete = true
- No overwrite: skip already-converted lessons
- No manual batch_progress.json edits during batch run (path: `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`)
- Internet required throughout conversion

## Section 10: Output Paths (Exact)
- Planning: `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/{ProgramName}.plan.md`
- Code output root: `/Aliens/Course_ACLE/{ProgramName}/`
- Documentation: `/Aliens/Docs/Course_ACLE/{ProgramName}.md`
- Run record: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/`

## Section 11: Validation Checklist
After Code workflow completes, verify:
- [ ] All pending lessons converted (HTML > 5KB + audio 3+ mp3s)
- [ ] No source files modified
- [ ] batch_progress.json updated with program completion (`/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`)
- [ ] Failed lessons logged with error details
- [ ] Narrations JSON exists for each converted lesson

## Section 12: Estimated Metrics
- Total lessons to convert: {pending_count}
- Already done (skip): {already_count}
- Estimated time: {pending_count × 3 min avg} = ~X hours
- Expected output size: {pending_count × 500KB avg} = ~X MB

# [09] Save Rules
Primary output:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/{ProgramName}.plan.md`
- Format: Markdown
- Naming: exact program name, no timestamp prefix (latest plan overwrites)

Audit copy (if run system active):
- Path: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Planning.md`

# [10] Error Handling
- `WF_TARGET_MISSING` — program folder not found in `/Aliens/Course/`
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets passed to singular workflow
- `WF_PATH_RESOLUTION_FAILED` — planning output path cannot be resolved
- `WF_CONVERTER_NOT_FOUND` — converter scripts missing (captured in plan but plan still saves)
- `WF_ENVIRONMENT_INCOMPLETE` — Python/edge-tts/assets missing (captured in plan but plan still saves)

Note: Environment check failures are recorded in the plan as warnings/blockers. Plan file still gets saved so Code workflow can read it and act accordingly.

# [11] Done Criteria
- Planning file written to output path with all 12 mandatory sections populated
- Discovery data is from actual disk scan (not guessed)
- Conversion status counts match reality
- No scope creep beyond the single program target
- Plan is deterministic: same inputs = same plan output (modulo timestamps)
