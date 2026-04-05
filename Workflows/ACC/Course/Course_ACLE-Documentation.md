---
WorkflowId: Course_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Course
Category: Video Lesson Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-10
EntryPoint: false
ParentWorkflow: Course_ACLE
Purpose: >
  Singular Documentation workflow: given a single program name, uski ACLE conversion ka
  enterprise-grade documentation generate karo — covering program overview, conversion results,
  lesson inventory, quality metrics, troubleshooting, aur usage guide. Documentation file
  Docs/Course_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Course_ACLE-Documentation", "AI_Developer", "Document the ACLE conversion results")
  TargetsMeaning: >
    Single program name without extension/path. Comma list is invalid (singular workflow).
    Example: "AI_Developer" implies conversion results at "/Aliens/Course_ACLE/AI_Developer/".
    Note: Range filtering ("1 to 10") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved program name.
  DescriptionMeaning: >
    Human language constraints/focus. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Course_ACLE/"
    Pattern: "{ProgramName}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Notes:
  - Documentation is generated AFTER conversion (Code workflow) completes.
  - Documentation reflects actual conversion results from disk (not guessed).
  - Source Course repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - Documentation language follows ACC SSOT: Hinglish (Roman Hindi).
  - English sirf technical terms ke liye. Devanagari Hindi script allowed nahi.
---

# Course_ACLE-Documentation

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Sirf specified program ki documentation banao
- Extra programs add karna forbidden
- Source Course repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden
- Is workflow me code execute karna forbidden (sirf documentation karo)

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
- Documentation output write permission (allowed by default; writes to `/Aliens/Docs/`)
- Approval policy: manual => approval before saving documentation
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Discovery Phase (Mandatory — Data from Disk)
Documentation MUST be evidence-based. Agar information disk par nahi milti, to honestly "unknown/not available" likho — invent mat karo.

## [06.1] Source Program Discovery
Scan: `/Aliens/Course/{ProgramName}/`
- Total courses count
- Total modules count (per course)
- Total lessons count (per module, per course, grand total)
- Course names list
- Module names list per course

## [06.2] Conversion Results Discovery
Scan: `/Aliens/Course_ACLE/{ProgramName}/`
For each source lesson MD file:
- HTML exists? Size?
- Audio dir exists? mp3 count?
- Narrations JSON exists?

Categorize:
- **Fully Converted**: HTML > 5KB + audio dir 3+ mp3s + narrations JSON
- **Partially Converted**: some outputs missing or undersized
- **Not Converted**: no output found

Aggregate:
- Total converted count
- Total partial count
- Total pending count
- Conversion rate percentage

## [06.3] Planning Artifact Discovery
Check: `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/{ProgramName}.plan.md`
- If exists: reference plan data in documentation
- If missing: note "Planning artifact not found"

## [06.4] Batch Progress Discovery
Check: `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`
- If program in `completed_programs`: note as batch-completed
- If lessons in `failed_lessons`: list them
- If not found: note "Progress tracking not available"

# [07] Documentation Content Template (Mandatory Sections)
Following the ACC `Workflow_Singular-Documentation.md` common contract, with ACLE-specific content:

## [07.1] Language + Audience Contract
- Language: Hinglish (Roman Hindi)
- Devanagari Hindi script: forbidden
- English: sirf technical terms ke liye
- Audience: beginner to intermediate — simple language, practical examples
- Depth: 3000-8000+ words (enterprise-grade, no shortcuts)

## [07.2] Mandatory Document Structure

### Section 1: Title Block
```markdown
---
Document: ACLE Program Documentation
Program: {ProgramName}
Domain: Course (Aliens Cinematic Learning Experience)
Version: 26.03.00
Author: AlienCyborg
Generated: {timestamp}
WorkflowId: Course_ACLE-Documentation
---
```

### Section 2: Executive Summary (Beginner-Friendly)
- Program name aur purpose
- ACLE conversion ka scope: MD text lessons → cinematic HTML video lessons
- Total lessons count, conversion rate
- Quality bar: animated SVG, TTS audio, minimal text, video-like experience
- 3-4 lines me overall status summary

### Section 3: Quick Start (Lesson Viewing Guide)
- Kaise ek converted lesson open/play karna hai
- Browser requirements (modern browser, autoplay support)
- Audio controls: play/pause, next/prev slide, narration toggle
- File paths: kahan se HTML file open karna hai
- 10-minute setup equivalent

### Section 4: Glossary
- ACLE: Aliens Cinematic Learning Experience
- TTS: Text-to-Speech
- SVG: Scalable Vector Graphics
- Narration: AI-generated Hindi audio commentary per slide
- Slide types: Title, Overview, Objectives, Content, DeepDive, Takeaways, Quiz, End
- edge-tts: Microsoft Azure Text-to-Speech engine
- Converter: Python script jo MD → HTML conversion karta hai

### Section 5: Program Structure Overview
- Course list (C1, C2, C3...) with names
- Module list per course (M1, M2...)
- Lesson count per module
- Total inventory table:
  ```
  | Course | Modules | Lessons |
  |--------|---------|---------|
  | C1_... | 5       | 23      |
  | C2_... | 4       | 18      |
  | Total  | 9       | 41      |
  ```

### Section 6: Conversion Results (Detailed)
- Overall conversion rate: X/Y lessons (Z%)
- Per-course breakdown table:
  ```
  | Course | Total | Converted | Failed | Pending |
  |--------|-------|-----------|--------|---------|
  | C1_... | 23    | 23        | 0      | 0       |
  ```
- Failed lessons list (if any) with likely reasons
- Partial conversions list (if any)

### Section 7: Output File Structure
- Directory tree of generated files
- Per-lesson output description:
  - HTML file: what it contains, size range
  - Narrations JSON: what it contains, format
  - Audio directory: mp3 files, naming convention, voice details
- Shared assets: CSS, JS, SVG (pre-existing, not generated)

### Section 8: Quality Metrics
- HTML file size statistics (min, max, average)
- Audio file count statistics (min, max per lesson)
- Slide type coverage (Title→End sequence)
- SVG illustration types used
- Brand color compliance
- Narration expansion quality indicators

### Section 9: Technical Architecture
- Conversion pipeline: MD → Parse → Build HTML → Generate Narrations → TTS Audio
- Converter components: `convert_lesson.py` (v3 ULTRA Premium, ~1967 lines)
- Batch orchestrator: `batch_all_programs.py` (~342 lines)
- TTS engine: edge-tts + `hi-IN-MadhurNeural` voice
- Slide engine: aliens-cinematic.js (client-side player)
- Styling: aliens-cinematic.css (cinematic slide styling)
- Skip logic: idempotent design (already-done = skip)

### Section 10: Integration with Aliens Ecosystem
- Course repo relationship (`/Aliens/Course/` → source SSOT)
- Course_ACLE repo relationship (`/Aliens/Course_ACLE/` → output)
- Shared assets architecture (CSS/JS/SVG reuse across programs)
- Multi-PC distribution compatibility
- Progress tracking system (`batch_progress.json` at `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/`)

### Section 11: Safety + Security
- Source repo READ-ONLY (no modifications ever)
- NoDelete policy (never delete output files)
- No manual `batch_progress.json` edits during batch (path: `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/`)
- No overwrite of already-converted lessons (skip check)
- Internet dependency for TTS (Azure servers)
- No secrets/credentials involved in conversion

### Section 12: Troubleshooting Guide
Common issues table:
- edge-tts timeout/failure → check internet, retry (3-retry built in)
- HTML file too small (< 5KB) → lesson MD may be too short, check source
- Audio directory empty → TTS failed all retries, check network
- Slides not rendering → shared assets missing, verify assets/ folder
- Narration sounds robotic → expected behavior for TTS, quality is "natural" level
- Batch process stopped → re-run same command, auto-resume from progress.json
- Missing SVG illustrations → converter selects from 12 types intelligently

### Section 13: FAQ (Minimum 15 Q/A)
At least 15 questions covering:
- Kaise ek lesson ko manually re-convert karna hai?
- Skip check kaise disable karna hai?
- Different voice kaise use karna hai?
- Multi-PC me progress kaise merge karna hai?
- Lesson kaise verify karna hai ke sahi convert hua?
- Agar internet disconnect ho jaye to kya hoga?
- Kya converted lessons offline kaam karengi? (Ha — HTML + audio local hai)
- batch_progress.json corrupt ho jaye to?
- New lessons add ho jaye source me to?
- Conversion speed kaise badhaye?
- Kya lessons ko re-convert kar sakte hain? (Ha — delete HTML/audio, re-run)
- Quality check kaise karna hai?
- Kya custom SVG illustrations add kar sakte hain?
- Disk space kitna chahiye?
- Multiple programs ek saath kaise convert karna hai?

### Section 14: Change Log / Versioning
- Converter version: v3 ULTRA Premium (current)
- Workflow version: 26.03.00
- Known limitations
- Planned improvements (if any from Description)

### Section 15: Extension + Future Hooks
- New slide types addition strategy
- New SVG illustration types strategy
- Voice customization hooks
- Batch script flag extensibility
- Multi-language narration (future)
- Progress dashboard (future)

### Section 16: Appendix
- Full program inventory (all courses/modules/lessons)
- Sample narration JSON structure
- Sample HTML slide structure (snippet)
- Command reference (all batch script flags)

# [08] Save Rules
Primary output:
- Path: `/Aliens/Docs/Course_ACLE/{ProgramName}.md`
- Format: Markdown
- Naming: exact program name, no timestamp prefix

Audit copy (if run system active):
- Path: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Documentation.md`

# [09] Honesty Contract (From Common Rules — Enforced)
- Agar koi info code/repo discovery me directly available nahi hai:
  - **Assumption** clearly label karo
  - **TODO (Team)** list me capture karo
  - Fake details invent karna forbidden
- Conversion counts MUST match actual disk scan (no guessing)
- If scan impossible (folder permissions, etc), honestly state it

# [10] Error Handling
- `WF_TARGET_MISSING` — program folder not found (source or output)
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PATH_RESOLUTION_FAILED` — documentation output path cannot be resolved
- `WF_DISCOVERY_INCOMPLETE` — could not scan source/output folders fully

Note: If conversion output folder is missing/empty (Code workflow did not run or failed completely),
documentation should still be generated with honest status: "Conversion not performed / 0% complete".

# [11] Done Criteria
- Documentation file written to `/Aliens/Docs/Course_ACLE/{ProgramName}.md`
- All 16 mandatory sections present and populated
- Conversion data sourced from actual disk scan
- Language is Hinglish (no Devanagari)
- Depth is enterprise-grade (3000+ words)
- FAQ has minimum 15 entries
- Troubleshooting covers at least 7 common issues
- No scope creep beyond the single program target
- No fake/guessed conversion data
