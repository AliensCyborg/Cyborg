---
WorkflowId: Course_ACLE-Code
Type: Workflow_Singular-Code
Domain: Course
Category: Video Lesson Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-10
EntryPoint: false
ParentWorkflow: Course_ACLE
Purpose: >
  Singular Code workflow: given a single program name, uske saare pending lessons ko
  ULTRA Premium cinematic HTML video lessons me convert karo by executing the batch
  converter script. Yeh workflow actual conversion execute karta hai — Python scripts
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Course_ACLE-Code", "AI_Developer", "Convert all lessons to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single program name without extension/path. Comma list is invalid (singular workflow).
    Example: "AI_Developer" implies source "/Aliens/Course/AI_Developer/" and output "/Aliens/Course_ACLE/AI_Developer/".
    Note: Range filtering ("1 to 10") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved program name.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Code:
    Path: "/Aliens/Course_ACLE/{ProgramName}/"
    Pattern: "*.html + *_narrations.json + audio_*/ (per lesson)"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
ExampleFiles:
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_lesson.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_all_programs.py"
  - "/Aliens/ACLE/assets/css/aliens-cinematic.css"
  - "/Aliens/ACLE/assets/js/aliens-cinematic.js"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - The "code" here is the conversion output (HTML + JSON + audio) not traditional source code.
  - converter scripts are pre-existing and must NOT be modified by this workflow.
  - Source Course repo is READ-ONLY.
  - Internet required for edge-tts (Microsoft Azure TTS API).
  - Conversion is idempotent: already-converted lessons are skipped (skip check built into scripts).
---

# Course_ACLE-Code

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Code.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Sirf specified program ke lessons convert karo
- Source Course repo (`/Aliens/Course/`) me koi bhi modification forbidden
- Converter scripts (`convert_lesson.py`, `batch_all_programs.py`) modify karna forbidden
- Shared assets (`/Aliens/ACLE/assets/`) modify karna forbidden
- `batch_progress.json` manually edit karna forbidden (script manages it)
- Koi extra program add karna forbidden
- Description me jo nahi bola, woh add karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE program name (example: "AI_Developer")
- Comma-separated list => error (`WF_TARGETS_INVALID`)
- Extensions forbidden
- Paths forbidden (sirf name)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"

Minimum checks:
- Code output write permission (writes to Course_ACLE, not prod code — generally allowed)
- Approval policy: manual => approval before starting conversion
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Plan-Driven Execution (Mandatory)
Plan file check:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/{ProgramName}.plan.md`

Rules:
- If plan exists: read plan, use discovery data (lesson counts, pending count, estimated time)
- If plan missing: conversion may still proceed with live discovery, but log warning `WF_PLANNING_MISSING`
- Plan provides: total lessons, already converted, pending, dependencies status, safety rules

Note: Unlike traditional code workflows, plan absence does NOT block execution here because
the converter scripts have built-in discovery and skip logic. But plan is strongly preferred
for accurate progress reporting.

# [07] Environment Validation (Pre-Flight Checks)
Before executing conversion, verify these prerequisites:

## [07.1] Python Check
- Python 3.10+ must be available
- Command: `python --version`
- Failure => error `WF_ENVIRONMENT_INCOMPLETE` with message "Python 3.10+ required"

## [07.2] edge-tts Package Check
- Must be installed and importable
- Command: `python -c "import edge_tts; print('OK')"`
- Failure => error `WF_ENVIRONMENT_INCOMPLETE` with message "edge-tts package missing. Run: pip install edge-tts"

## [07.3] Converter Scripts Check
- `/Aliens/Cyborg/AlienCyborg/Code/convert_lesson.py` must exist
- `/Aliens/Cyborg/AlienCyborg/Code/batch_all_programs.py` must exist
- Failure => error `WF_CONVERTER_NOT_FOUND` with exact missing file path

## [07.4] Shared Assets Check
- `/Aliens/ACLE/assets/css/aliens-cinematic.css` must exist
- `/Aliens/ACLE/assets/js/aliens-cinematic.js` must exist
- `/Aliens/ACLE/assets/svg/aliens-logo-defs.svg` must exist
- Failure => error `WF_ENVIRONMENT_INCOMPLETE` with missing asset path

## [07.5] Source Program Check
- `/Aliens/Course/{ProgramName}/` must exist and contain at least 1 lesson MD file
- Failure => error `WF_TARGET_MISSING`

## [07.6] Internet Connectivity Check
- edge-tts requires internet access to Microsoft Azure TTS servers
- Quick check: attempt a test TTS generation or network ping
- Failure => warning `WF_INTERNET_REQUIRED` (may retry during execution)

# [08] Execution Steps (Deterministic)

## Step 1: Resolve Paths
- Source root: `/Aliens/Course/{ProgramName}/`
- Output root: `/Aliens/Course_ACLE/{ProgramName}/`
- Scripts root: `/Aliens/Cyborg/AlienCyborg/Code/`
- Working directory: `/Aliens/Cyborg/AlienCyborg/Code/`

## Step 2: Pre-Conversion Inventory
Scan source program to get total lesson count:
- Walk `/Aliens/Course/{ProgramName}/` recursively
- Count all `L{N}_*.md` files
- This is `total_lessons`

Check existing conversions:
- Walk `/Aliens/Course_ACLE/{ProgramName}/` recursively
- Count lessons where HTML > 5KB AND audio dir has 3+ mp3s
- This is `already_converted`
- Pending = `total_lessons - already_converted`

Log:
```
Program: {ProgramName}
Total lessons: {total_lessons}
Already converted: {already_converted}
Pending: {pending}
```

## Step 3: Execute Batch Conversion
Run the batch converter script for this specific program:

Command:
```powershell
cd /Aliens/Cyborg/AlienCyborg/Code
$env:PYTHONIOENCODING = "utf-8"
python batch_all_programs.py --program {ProgramName}
```

Execution rules:
- Run as a foreground process (not background) so we can monitor output
- If process is expected to take very long (500+ lessons), run as background and poll
- Capture stdout/stderr for logging
- Do NOT interrupt unless critical error detected

## Step 4: Monitor Progress
During execution, periodically check:
- `batch_progress.json` for completion status
- Console output for errors/warnings
- Individual lesson output files appearing in Course_ACLE

Progress indicators:
- Converter prints per-lesson status (converting, skipping, failed)
- `batch_progress.json` updates with completed/failed lessons

## Step 5: Post-Conversion Verification
After batch script completes, verify results:

### Per-Lesson Check (sample or full scan):
For each lesson MD in source:
1. Corresponding HTML exists in Course_ACLE? Size > 5KB?
2. Audio directory exists? Contains 3+ mp3 files?
3. Narrations JSON exists?

### Aggregate Results:
- `total_lessons`: from Step 2
- `successfully_converted`: lessons passing all 3 checks
- `failed_conversions`: lessons that were attempted but failed
- `skipped_existing`: lessons that were already converted (skip check)
- `conversion_rate`: `successfully_converted / total_lessons * 100`

## Step 6: Handle Failures
If any lessons failed:
- List failed lesson paths
- Check error patterns (common: edge-tts timeout, network error, malformed MD)
- Suggest retry: "Re-run same command — converter will skip done lessons and retry failed ones"
- Do NOT mark overall program as failed unless ALL lessons failed

## Step 7: Update Progress Tracking
The batch script auto-updates `batch_progress.json`. Verify:
- Program listed in `completed_programs` (if all lessons done)
- Failed lessons listed in `failed_lessons` (if any)
- `last_updated` timestamp current

# [09] Output Specification (Per Converted Lesson)
Each successfully converted lesson produces 3 outputs:

## [09.1] Cinematic HTML File
- Path: `{OutputRoot}/C{N}_{CourseName}/M{N}_{ModuleName}/L{N}_{LessonName}.html`
- Size: typically 30KB-60KB (must be >5KB for "done" status)
- Contents: 7-12 animated slides with SVG illustrations, minimal text, gradient backgrounds
- References relative paths to shared assets: `../../../assets/css/`, `../../../assets/js/`

## [09.2] Narrations JSON
- Path: `{OutputRoot}/C{N}_{CourseName}/M{N}_{ModuleName}/L{N}_{LessonName}_narrations.json`
- Contents: per-slide narration text, presenter notes, slide titles
- Used by JS engine for audio sync + narration display

## [09.3] Audio Directory
- Path: `{OutputRoot}/C{N}_{CourseName}/M{N}_{ModuleName}/audio_L{N}_{LessonName}/`
- Contents: `slide_01.mp3`, `slide_02.mp3`, ... (one per slide)
- Each mp3: typically 100KB-800KB
- Minimum: 3 mp3 files for "done" status
- Voice: `hi-IN-MadhurNeural` (Hindi male, natural)
- Rate: `+0%` (natural speed)

# [10] Quality Gates (Non-Negotiable)
These are enforced by the converter scripts automatically, but this workflow must verify:

1. **HTML quality**: animated slides, SVG illustrations, minimal text, gradient backgrounds
2. **Audio quality**: natural Hindi narration, no silence, no corruption
3. **Completeness**: all 3 outputs per lesson (HTML + JSON + audio)
4. **Brand compliance**: colors `#22c55e`, `#0ea5e9`, `#7c6aff`, `#f59e0b`
5. **Slide types present**: Title → Overview → Objectives → Content/DeepDive → Takeaways → Quiz → End
6. **12 SVG types available**: alien head, brain network, rings, Venn, timeline, process flow, galaxy, shield, hexagonal grid, lightbulb, checklist, quiz brain
7. **Narration expansion**: detailed, natural language, heading dedup applied
8. **No source modification**: `/Aliens/Course/` untouched

# [11] Retry Strategy
Converter has built-in retry:
- edge-tts: 3 retries per TTS call with increasing wait (2.0s, 3.5s)
- Exception + asyncio.CancelledError both caught

If entire program conversion fails:
- Same command can be re-run safely (idempotent)
- Already-converted lessons will be skipped
- Only pending/failed lessons will be re-attempted

# [12] Safety Rules (Enforcement)
1. Source READ-ONLY: `/Aliens/Course/{ProgramName}/` — no create/modify/delete
2. Shared assets READ-ONLY: `/Aliens/ACLE/assets/` — no modification
3. Converter scripts READ-ONLY: do not modify `convert_lesson.py` or `batch_all_programs.py`
4. `batch_progress.json` managed by script only: no manual edits during run
5. NoDelete = true: never delete any output file (even failed/partial ones)
6. Internet required: do not start conversion without connectivity

# [13] Error Handling
ErrorCodes specific to this workflow:
- `WF_TARGET_MISSING` — program folder not found in `/Aliens/Course/`
- `WF_CONVERTER_NOT_FOUND` — converter scripts missing at expected paths
- `WF_ENVIRONMENT_INCOMPLETE` — Python/edge-tts/assets missing
- `WF_INTERNET_REQUIRED` — no internet connectivity for TTS
- `WF_CONVERSION_FAILED` — batch script returned overall failure
- `WF_PARTIAL_CONVERSION` — some lessons converted, some failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PLANNING_MISSING` — plan file not found (warning, not blocking)

# [14] Results Report (Output of this Workflow)
After execution, this workflow returns/logs:

```yaml
Program: {ProgramName}
Status: success | partial | failed
TotalLessons: {N}
Converted: {N}
Failed: {N}
Skipped: {N} (already done)
ConversionRate: {X}%
FailedLessons:
  - path/to/failed_lesson_1.md
  - path/to/failed_lesson_2.md
Duration: {X} minutes
ErrorsEncountered:
  - {error details if any}
```

# [15] Done Criteria
- Batch converter script executed for the target program
- Post-conversion verification completed
- Results report generated with accurate counts
- All pending lessons either converted or logged as failed
- Source repo untouched
- No scope creep (only this program's lessons processed)
- Progress tracking updated in `batch_progress.json`
