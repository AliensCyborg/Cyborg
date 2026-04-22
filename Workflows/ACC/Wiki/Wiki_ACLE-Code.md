---
WorkflowId: Wiki_ACLE-Code
Type: Workflow_Singular-Code
Domain: Wiki
Category: Wiki Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Wiki_ACLE
Purpose: >
  Singular Code workflow: given a single wiki article path, us article ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Wiki_ACLE-Code", "HIEN/A/Algorithm", "Convert wiki article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Wiki/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/A/Algorithm" implies source "/Aliens/Wiki/HIEN/A/Algorithm.md"
    and output "/Aliens/Wiki_ACLE/HIEN/A/Algorithm.html".
    Note: Range filtering ("1 to 100") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Code:
    Path: "/Aliens/Wiki_ACLE/{ArticlePath}.html"
    Pattern: "*.html + *_narrations.json + audio_*/ (per article)"
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
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_article.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_wiki.py"
  - "/Aliens/ACLE/assets/css/aliens-cinematic.css"
  - "/Aliens/ACLE/assets/js/aliens-cinematic.js"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - The "code" here is the conversion output (HTML + JSON + audio) not traditional source code.
  - Converter scripts are pre-existing and must NOT be modified by this workflow.
  - Source Wiki repo is READ-ONLY.
  - Internet required for edge-tts (Microsoft Azure TTS API).
  - Conversion is idempotent: already-converted articles are skipped (skip check built into scripts).
  - Same directory structure preserved: output mirrors source path in Wiki_ACLE.
---

# Wiki_ACLE-Code

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
- Sirf specified article ko convert karo
- Source Wiki repo (`/Aliens/Wiki/`) me koi bhi modification forbidden
- Converter scripts (`convert_article.py`, `batch_wiki.py`) modify karna forbidden
- Shared assets (`/Aliens/ACLE/assets/`) modify karna forbidden
- `batch_progress.json` manually edit karna forbidden (script manages it; located at `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/batch_progress.json`)
- Koi extra article add karna forbidden
- Description me jo nahi bola, woh add karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/A/Algorithm")
- Relative to `/Aliens/Wiki/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)
- Extensions forbidden

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"

Minimum checks:
- Code output write permission (writes to Wiki_ACLE, not prod code — generally allowed)
- Approval policy: manual => approval before starting conversion
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Plan-Driven Execution (Mandatory)
Plan file check:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/{ArticleSlug}.plan.md`
  (ArticleSlug = ArticlePath with `/` replaced by `_`)

Rules:
- If plan exists: read plan, use discovery data (article structure, complexity, estimated slides)
- If plan missing: conversion may still proceed with live discovery, but log warning `WF_PLANNING_MISSING`
- Plan provides: article structure analysis, conversion status, slide mapping plan, dependency status

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
- `/Aliens/Cyborg/AlienCyborg/Code/convert_article.py` must exist
- `/Aliens/Cyborg/AlienCyborg/Code/batch_wiki.py` must exist
- Failure => error `WF_CONVERTER_NOT_FOUND` with exact missing file path

## [07.4] Shared Assets Check
- `/Aliens/ACLE/assets/css/aliens-cinematic.css` must exist
- `/Aliens/ACLE/assets/js/aliens-cinematic.js` must exist
- `/Aliens/ACLE/assets/svg/aliens-logo-defs.svg` must exist
- Failure => error `WF_ENVIRONMENT_INCOMPLETE` with missing asset path

## [07.5] Source Article Check
- `/Aliens/Wiki/{ArticlePath}.md` must exist and be non-empty
- Failure => error `WF_TARGET_MISSING`

## [07.6] Internet Connectivity Check
- edge-tts requires internet access to Microsoft Azure TTS servers
- Quick check: attempt a test TTS generation or network ping
- Failure => warning `WF_INTERNET_REQUIRED` (may retry during execution)

# [08] Execution Steps (Deterministic)

## Step 1: Resolve Paths
- Source file: `/Aliens/Wiki/{ArticlePath}.md`
- Output directory: `/Aliens/Wiki_ACLE/{dir}/` (where {dir} = directory part of ArticlePath)
- Output HTML: `/Aliens/Wiki_ACLE/{ArticlePath}.html`
- Output narrations: `/Aliens/Wiki_ACLE/{ArticlePath}_narrations.json`
- Output audio: `/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/`
  (where {ArticleName} = filename part of ArticlePath)
- Scripts root: `/Aliens/Cyborg/AlienCyborg/Code/`

## Step 2: Pre-Conversion Status Check
Check if article is already converted:
- HTML exists at output path? Size > 5KB?
- Narrations JSON exists?
- Audio directory exists with 3+ mp3 files?

If already fully converted:
- Log: "Article {ArticlePath} already converted. Skipping."
- Return success (idempotent)

If partially converted or pending:
- Proceed to conversion

Log:
```
Article: {ArticlePath}
Status: Already Converted / Partially Converted / Pending
HTML exists: Yes/No (size: {N}KB)
Audio dir: Yes/No ({N} mp3 files)
Narrations JSON: Yes/No
```

## Step 3: Execute Article Conversion
Run the converter script for this specific article:

Command:
```powershell
cd /Aliens/Cyborg/AlienCyborg/Code
$env:PYTHONIOENCODING = "utf-8"
python convert_article.py --article "{ArticlePath}"
```

Execution rules:
- Run as a foreground process (not background) — individual article conversion is short
- Capture stdout/stderr for logging
- Do NOT interrupt unless critical error detected

Alternative batch mode (if convert_article.py supports):
```powershell
python batch_wiki.py --article "{ArticlePath}"
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

### Per-Article Check:
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

### Results Summary:
```
Article: {ArticlePath}
HTML: {size}KB ({slide_count} slides)
Audio: {mp3_count} files in audio_{ArticleName}/
Narrations: {entries} entries
Status: success / partial / failed
```

## Step 6: Handle Failures
If conversion failed:
- Check error patterns (common: edge-tts timeout, network error, malformed MD, empty source)
- Log failure details
- Suggest retry: "Re-run same command — converter will retry failed article"
- Do NOT mark overall article as permanently failed unless repeated failures

## Step 7: Directory Structure Verification
Verify output preserves same directory structure as source:
- Source parent dir: `/Aliens/Wiki/{dir}/`
- Output parent dir: `/Aliens/Wiki_ACLE/{dir}/`
- Both should have matching sub-folder hierarchy

# [09] Output Specification (Per Converted Article)
Each successfully converted article produces 3 outputs:

## [09.1] Cinematic HTML File
- Path: `/Aliens/Wiki_ACLE/{ArticlePath}.html`
- Size: typically 20KB-60KB (must be >5KB for "done" status)
- Contents: 7-12 animated slides with SVG illustrations, minimal text, gradient backgrounds
- Slide sequence derived from article structure:
  - Title slide: article name + lead section summary
  - Overview slide: infobox data (if present)
  - Content slides: per major section (1-3 slides depending on length)
  - Diagram slides: mermaid diagrams → animated SVG representation
  - Comparison slides: tables → visual comparison format
  - Glossary slide: key terms from glossary section
  - Quiz slide: auto-generated questions from content
  - End slide: summary + see also references
- References relative paths to shared assets: `../../assets/css/`, `../../assets/js/`
  (depth depends on article path nesting level)

## [09.2] Narrations JSON
- Path: `/Aliens/Wiki_ACLE/{ArticlePath}_narrations.json`
- Contents: per-slide narration text, presenter notes, slide titles
- Used by JS engine for audio sync + narration display
- Format:
  ```json
  {
    "article": "{ArticlePath}",
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
- Path: `/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/`
- Contents: `slide_01.mp3`, `slide_02.mp3`, ... (one per slide)
- Each mp3: typically 100KB-800KB
- Minimum: 3 mp3 files for "done" status
- Voice: `hi-IN-MadhurNeural` (Hindi male, natural)
- Rate: `+0%` (natural speed)

# [10] Quality Gates (Non-Negotiable)
These are enforced by the converter script automatically, but this workflow must verify:

1. **HTML quality**: animated slides, SVG illustrations, minimal text, gradient backgrounds
2. **Audio quality**: natural Hindi narration, no silence, no corruption
3. **Completeness**: all 3 outputs per article (HTML + JSON + audio)
4. **Brand compliance**: colors `#22c55e`, `#0ea5e9`, `#7c6aff`, `#f59e0b`
5. **Slide types present**: Title → Overview → Content → [Diagram/Comparison] → Glossary → Quiz → End
6. **SVG types available**: alien head, brain network, rings, Venn, timeline, process flow,
   galaxy, shield, hexagonal grid, lightbulb, checklist, quiz brain (12 types)
7. **Narration expansion**: detailed, natural language, heading dedup applied
8. **No source modification**: `/Aliens/Wiki/` untouched
9. **Directory structure preserved**: output path mirrors source path hierarchy

# [11] Retry Strategy
Converter has built-in retry:
- edge-tts: 3 retries per TTS call with increasing wait (2.0s, 3.5s)
- Exception + asyncio.CancelledError both caught

If article conversion fails:
- Same command can be re-run safely (idempotent)
- Already-generated slides/audio will be detected by skip check
- Only pending/failed components will be re-attempted

# [12] Safety Rules (Enforcement)
1. Source READ-ONLY: `/Aliens/Wiki/{ArticlePath}.md` — no create/modify/delete
2. Shared assets READ-ONLY: `/Aliens/ACLE/assets/` — no modification
3. Converter scripts READ-ONLY: do not modify `convert_article.py` or `batch_wiki.py`
4. `batch_progress.json` managed by script only: no manual edits during run (path: `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/batch_progress.json`)
5. NoDelete = true: never delete any output file (even failed/partial ones)
6. Internet required: do not start conversion without connectivity
7. Directory structure: output MUST preserve same relative path as source

# [13] Error Handling
ErrorCodes specific to this workflow:
- `WF_TARGET_MISSING` — article file not found in `/Aliens/Wiki/`
- `WF_CONVERTER_NOT_FOUND` — converter scripts missing at expected paths
- `WF_ENVIRONMENT_INCOMPLETE` — Python/edge-tts/assets missing
- `WF_INTERNET_REQUIRED` — no internet connectivity for TTS
- `WF_CONVERSION_FAILED` — converter script returned overall failure
- `WF_PARTIAL_CONVERSION` — some outputs generated, some failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PLANNING_MISSING` — plan file not found (warning, not blocking)

# [14] Results Report (Output of this Workflow)
After execution, this workflow returns/logs:

```yaml
Article: {ArticlePath}
Status: success | partial | failed
HTMLSize: {N}KB
SlideCount: {N}
AudioFiles: {N}
NarrationsEntries: {N}
ConversionTime: {N}s
SkipReason: already_converted | null
ErrorDetails: {if failed, details here}
```
