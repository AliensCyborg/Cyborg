---
WorkflowId: Skill_ACLE-Code
Type: Workflow_Singular-Code
Domain: Skill
Category: Skill Topic Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Skill_ACLE
Purpose: >
  Singular Code workflow: given a single skill lesson path, us lesson ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Skill_ACLE-Code", "HIEN/Python/01_Python_Introduction", "Convert skill lesson to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single lesson path relative to /Aliens/Skills/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Python/01_Python_Introduction" implies source "/Aliens/Skills/HIEN/Python/01_Python_Introduction.md"
    and output "/Aliens/Skill_ACLE/HIEN/Python/01_Python_Introduction.html".
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved lesson path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Code:
    Path: "/Aliens/Skill_ACLE/{LessonPath}.html"
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
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_skill.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_skill.py"
  - "/Aliens/Skill_ACLE/assets/css/aliens-cinematic.css"
  - "/Aliens/Skill_ACLE/assets/js/aliens-cinematic.js"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - The "code" here is the conversion output (HTML + JSON + audio) not traditional source code.
  - Converter scripts are pre-existing and must NOT be modified by this workflow.
  - Source Skills repo is READ-ONLY.
  - Internet required for edge-tts (Microsoft Azure TTS API).
  - Conversion is idempotent: already-converted lessons are skipped (skip check built into scripts).
  - Same directory structure preserved: output mirrors source path in Skill_ACLE.
  - Branding: "Aliens School" (not "Aliens Wiki").
---

# Skill_ACLE-Code

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
- Sirf specified lesson ko convert karo
- Source Skills repo (`/Aliens/Skills/`) me koi bhi modification forbidden
- Converter scripts (`convert_skill.py`, `batch_skill.py`) modify karna forbidden
- Shared assets (`/Aliens/Skill_ACLE/assets/`) modify karna forbidden
- `batch_progress.json` manually edit karna forbidden (script manages it; located at `/Aliens/.Alien/{Developer_Username}/State/Skill_ACLE/batch_progress.json`)
- Koi extra lesson add karna forbidden
- Description me jo nahi bola, woh add karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE lesson path (example: "HIEN/Python/01_Python_Introduction")
- Relative to `/Aliens/Skills/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)
- Extensions forbidden

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"

Minimum checks:
- Code output write permission (writes to Skill_ACLE, not prod code — generally allowed)
- Approval policy: manual => approval before starting conversion
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Plan-Driven Execution (Mandatory)
Plan file check:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Skill_ACLE/{LessonSlug}.plan.md`
  (LessonSlug = LessonPath with `/` replaced by `_`)

Rules:
- If plan exists: read plan, use discovery data (lesson structure, complexity, estimated slides)
- If plan missing: conversion may still proceed with live discovery, but log warning `WF_PLANNING_MISSING`
- Plan provides: lesson structure analysis, conversion status, slide mapping plan, dependency status

Note: Unlike traditional code workflows, plan absence does NOT block execution here because
the converter script has built-in discovery and skip logic. But plan is strongly preferred
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
- `/Aliens/Cyborg/AlienCyborg/Code/convert_skill.py` must exist
- `/Aliens/Cyborg/AlienCyborg/Code/batch_skill.py` must exist
- Failure => error `WF_CONVERTER_NOT_FOUND` with exact missing file path

## [07.4] Shared Assets Check
- `/Aliens/Skill_ACLE/assets/css/aliens-cinematic.css` must exist
- `/Aliens/Skill_ACLE/assets/js/aliens-cinematic.js` must exist
- `/Aliens/Skill_ACLE/assets/svg/aliens-logo-defs.svg` must exist
- Failure => error `WF_ENVIRONMENT_INCOMPLETE` with missing asset path

## [07.5] Source Lesson Check
- `/Aliens/Skills/{LessonPath}.md` must exist and be non-empty
- Failure => error `WF_TARGET_MISSING`

## [07.6] Internet Connectivity Check
- edge-tts requires internet access to Microsoft Azure TTS servers
- Quick check: attempt a test TTS generation or network ping
- Failure => warning `WF_INTERNET_REQUIRED` (may retry during execution)

# [08] Execution Steps (Deterministic)

## Step 1: Resolve Paths
- Source file: `/Aliens/Skills/{LessonPath}.md`
- Output directory: `/Aliens/Skill_ACLE/{dir}/` (where {dir} = directory part of LessonPath)
- Output HTML: `/Aliens/Skill_ACLE/{LessonPath}.html`
- Output narrations: `/Aliens/Skill_ACLE/{LessonPath}_narrations.json`
- Output audio: `/Aliens/Skill_ACLE/{dir}/audio_{LessonFilename}/`
  (where {LessonFilename} = filename part of LessonPath without extension)
- Scripts root: `/Aliens/Cyborg/AlienCyborg/Code/`

## Step 2: Pre-Conversion Status Check
Check if lesson is already converted:
- HTML exists at output path? Size > 5KB?
- Narrations JSON exists?
- Audio directory exists with 3+ mp3 files?

If already fully converted:
- Log: "Lesson {LessonPath} already converted. Skipping."
- Return success (idempotent)

If partially converted or pending:
- Proceed to conversion

Log:
```
Lesson: {LessonPath}
Status: Already Converted / Partially Converted / Pending
HTML exists: Yes/No (size: {N}KB)
Audio dir: Yes/No ({N} mp3 files)
Narrations JSON: Yes/No
```

## Step 3: Execute Lesson Conversion
Run the converter script for this specific lesson:

Command:
```powershell
cd /Aliens/Cyborg/AlienCyborg/Code
$env:PYTHONIOENCODING = "utf-8"
python convert_skill.py --lesson "{LessonPath}"
```

Execution rules:
- Run as a foreground process (not background) — individual lesson conversion is short
- Capture stdout/stderr for logging
- Do NOT interrupt unless critical error detected

Alternative batch mode:
```powershell
python batch_skill.py --lesson "{LessonPath}"
```

## Step 4: Monitor Progress
During execution, monitor:
- Console output for slide generation progress
- Console output for TTS audio generation progress
- Console output for errors/warnings

Progress indicators:
- Converter prints per-slide status (generating, TTS processing, saving)
- HTML file size growing during generation
- Audio mp3 files appearing in output directory

## Step 5: Post-Conversion Verification
After converter script completes, verify results:

### Per-Lesson Check:
1. HTML file exists at expected path? Size > 5KB?
2. HTML contains 7-12 cinematic animated slides?
3. Narrations JSON exists? Valid JSON?
4. Audio directory exists? Contains 3+ mp3 files?
5. All mp3 files are non-empty (> 0 bytes)?

### Quality Spot-Check:
- HTML file has brand colors (`#22c55e`, `#0ea5e9`, `#7c6aff`, `#f59e0b`)
- HTML references shared assets (relative paths to `../assets/` or similar)
- Narrations JSON has entries matching slide count
- Audio files named slide_01.mp3, slide_02.mp3, etc.
- Branding says "Aliens School" (not "Aliens Wiki")

### Results Summary:
```
Lesson: {LessonPath}
HTML: {size}KB ({slide_count} slides)
Audio: {mp3_count} files in audio_{LessonFilename}/
Narrations: {entries} entries
Status: success / partial / failed
```

## Step 6: Handle Failures
If conversion failed:
- Check error patterns (common: edge-tts timeout, network error, malformed MD, empty source)
- Log failure details
- Suggest retry: "Re-run same command — converter will retry failed lesson"
- Do NOT mark overall lesson as permanently failed unless repeated failures

## Step 7: Directory Structure Verification
Verify output preserves same directory structure as source:
- Source parent dir: `/Aliens/Skills/{dir}/`
- Output parent dir: `/Aliens/Skill_ACLE/{dir}/`
- Both should have matching sub-folder hierarchy

# [09] Output Specification (Per Converted Lesson)
Each successfully converted lesson produces 3 outputs:

## [09.1] Cinematic HTML File
- Path: `/Aliens/Skill_ACLE/{LessonPath}.html`
- Size: typically 20KB-60KB (must be >5KB for "done" status)
- Contents: 7-12 animated slides with SVG illustrations, minimal text, gradient backgrounds
- Slide sequence derived from lesson structure:
  - Title slide: skill topic name + lesson intro
  - Overview slide: topic summary/objectives
  - Content slides: per major section (1-3 slides depending on length)
  - DeepDive slides: complex concepts, code examples, tables
  - Diagram slides: mermaid/visual content (if present)
  - Comparison slides: tables → visual comparison format
  - Quiz slide: auto-generated questions from content
  - End slide: summary + related topics
- References relative paths to shared assets: `../../assets/css/`, `../../assets/js/`
  (depth depends on lesson path nesting level)
- Branding: "Aliens School" throughout

## [09.2] Narrations JSON
- Path: `/Aliens/Skill_ACLE/{LessonPath}_narrations.json`
- Contents: per-slide narration text, presenter notes, slide titles
- Used by JS engine for audio sync + narration display
- Format:
  ```json
  {
    "lesson": "{LessonPath}",
    "slides": [
      {
        "slide_num": 1,
        "type": "Title",
        "title": "...",
        "narration": "...",
        "duration_seconds": 10
      },
      ...
    ]
  }
  ```

## [09.3] Audio Directory
- Path: `/Aliens/Skill_ACLE/{dir}/audio_{LessonFilename}/`
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
5. **Slide types present**: Title → Overview → Content/DeepDive → Quiz → End
6. **12 SVG types available**: alien head, brain network, rings, Venn, timeline, process flow, galaxy, shield, hexagonal grid, lightbulb, checklist, quiz brain
7. **Narration expansion**: detailed, natural language, heading dedup applied
8. **No source modification**: `/Aliens/Skills/` untouched
9. **Branding**: "Aliens School" (not "Aliens Wiki")

# [11] Retry Strategy
Converter has built-in retry:
- edge-tts: 3 retries per TTS call with increasing wait (2.0s, 3.5s)
- Exception + asyncio.CancelledError both caught

If lesson conversion fails:
- Same command can be re-run safely (idempotent)
- Already-converted lessons will be skipped
- Only pending/failed lessons will be re-attempted

# [12] Safety Rules (Enforcement)
1. Source READ-ONLY: `/Aliens/Skills/{LessonPath}.md` — no create/modify/delete
2. Shared assets READ-ONLY: `/Aliens/Skill_ACLE/assets/` — no modification
3. Converter scripts READ-ONLY: do not modify `convert_skill.py` or `batch_skill.py`
4. `batch_progress.json` managed by script only: no manual edits during run
5. NoDelete = true: never delete any output file (even failed/partial ones)
6. Internet required: do not start conversion without connectivity

# [13] Error Handling
ErrorCodes specific to this workflow:
- `WF_TARGET_MISSING` — lesson file not found in `/Aliens/Skills/`
- `WF_CONVERTER_NOT_FOUND` — converter scripts missing at expected paths
- `WF_ENVIRONMENT_INCOMPLETE` — Python/edge-tts/assets missing
- `WF_INTERNET_REQUIRED` — no internet connectivity for TTS
- `WF_CONVERSION_FAILED` — converter script returned overall failure
- `WF_PARTIAL_CONVERSION` — some parts converted, some failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PLANNING_MISSING` — plan file not found (warning, not blocking)

# [14] Results Report (Output of this Workflow)
After execution, this workflow returns/logs:

```yaml
Lesson: {LessonPath}
Status: success | partial | failed
HTML: {size}KB
Slides: {count}
AudioFiles: {count}
NarrationChars: {count}
Duration: {seconds}s
```
