---
WorkflowId: Course_ACLE
Type: Workflow_Plural
Domain: Course
Category: Video Lesson Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-10
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given one-or-more program names (ya CSV file ya source directory path),
  ACLE (Aliens Cinematic Learning Experience) video lessons generate karo by delegating
  to child workflows. Yeh workflow khud code generate nahi karta — sirf coordination +
  target resolution + safety/approval enforce karta hai, aur child workflows ko
  deterministic order me call karta hai.
Invocation:
  Signature:
    - Workflow("Course_ACLE", "Course_ACLE_Planner.csv", "Description : 1 to 10")
    - Workflow("Course_ACLE", "AI_Developer, Advanced_Data_Analytics", "Convert all lessons")
    - Workflow("Course_ACLE", "/Aliens/Course", "Description : 1 to 10")
  TargetsMeaning: >
    3 input formats supported:
    (1) CSV filename — batch mode with per-row tracking.
    (2) Comma-separated program names — direct program names (Course folder ke under folder names).
    (3) Directory path — source Course root path; programs us path ke under scan honge.
    Range filtering ("1 to 10") Description me aata hai, Targets me nahi.
  DescriptionMeaning: >
    Human language constraints/requirements. Must NOT expand scope beyond ACLE conversion.
    Optional range suffix supported: "Description : N to M" — yeh programs ko alphabetically
    sort karke #N se #M tak filter karta hai. Range sirf Description me valid hai, Targets me nahi.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/"
    Pattern: "{ProgramName}.plan.md"
  Code:
    Path: "/Aliens/Course_ACLE/{ProgramName}/"
    Pattern: "*.html + *_narrations.json + audio_*/ (per lesson)"
  Documentation:
    Path: "/Aliens/Docs/Course_ACLE/"
    Pattern: "{ProgramName}.md"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Course_ACLE.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Course_ACLE-Planning
  - Course_ACLE-Code
  - Course_ACLE-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Source Course repo (`/Aliens/Course/`) is strictly READ-ONLY. Koi file modify/delete nahi.
  - Output Course_ACLE repo (`/Aliens/Course_ACLE/`) me write hota hai.
  - edge-tts (Microsoft Azure TTS) ke liye internet connection mandatory hai.
  - Converter scripts: `/Aliens/Cyborg/AlienCyborg/Code/convert_lesson.py` (v3 ULTRA Premium) +
    `batch_all_programs.py` (multi-program orchestrator).
  - Range filtering "1 to 10" Description me aata hai (e.g. "Description : 1 to 10").
    Programs ko alphabetically sort karke #1 se #10 tak pick karo.
  - Directory path target (e.g. "/Aliens/Course") se programs us path ke under scan honge.
  - Multi-PC distribution: har PC ko alag programs assign ho, overlap forbidden.
---

# Course_ACLE

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Target resolution (CSV / comma-list / directory-path → program names list)
- Description parsing for range filter ("Description : N to M")
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct code generate/modify is file me (child workflow karega)
- Extra programs invent karna / unrelated scope add karna
- Description me jo nahi bola, woh add karna
- Source Course repo (`/Aliens/Course/`) me koi bhi modify/delete

# [03] Manifest Gate (Mandatory)
1) Read manifest:
   - "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (default)
   - Code generation (lesson conversion): allowed (writes to Course_ACLE, not prod code)
   - DB touch: NOT part of this workflow
4) Missing/invalid permission => fail fast (standard errors)

# [04] ACLE Domain Context (Hard Rules)

## [04.1] Source Structure (SSOT — READ-ONLY)
```
/Aliens/Course/
  └── {ProgramName}/                          # e.g. AI_Developer
       └── C{N}_{CourseName}/                 # e.g. C1_Introduction_to_Software_Engineering
            └── M{N}_{ModuleName}/            # e.g. M1_Intro_to_Software_Development
                 └── L{N}_{LessonName}.md     # e.g. L1_What_is_Software_Engineering.md
```

## [04.2] Output Structure (SSOT — WRITE)
```
/Aliens/Course_ACLE/
  ├── assets/                                 # Shared CSS + JS + SVG (pre-existing, do not modify)
  │    ├── css/aliens-cinematic.css
  │    ├── js/aliens-cinematic.js
  │    └── svg/aliens-logo-defs.svg
  # NOTE: Converter scripts are at /Aliens/Cyborg/AlienCyborg/Code/
  #   convert_lesson.py         — v3 ULTRA Premium converter (~1967 lines)
  #   batch_all_programs.py     — Multi-program batch orchestrator (~342 lines)
  └── {ProgramName}/                          # Mirror of Course structure
       └── C{N}_{CourseName}/
            └── M{N}_{ModuleName}/
                 ├── L{N}_{LessonName}.html              # Cinematic HTML (7-12 slides)
                 ├── L{N}_{LessonName}_narrations.json   # Narration data per slide
                 └── audio_L{N}_{LessonName}/            # Audio directory
                      ├── slide_01.mp3
                      ├── slide_02.mp3
                      └── ...
```

## [04.3] Converter Scripts (Reference Only — Do Not Modify)
- `convert_lesson.py` (v3 ULTRA Premium): single lesson MD → cinematic HTML + TTS audio
  - 12 animated SVG illustration types
  - Smart narration expansion engine (7 functions)
  - Heading deduplication
  - edge-tts 3-retry with CancelledError handling
  - Slide types: Title(10s), Overview(18s), Objectives(16s), Content(22s), DeepDive(25s), Takeaways(18s), Quiz(15s), End(10s)
  - Voice: `hi-IN-MadhurNeural` (Hindi male, natural)

- `batch_all_programs.py`: multi-program batch orchestrator
  - Flags: `--status`, `--program {name}`, `--programs {n1} {n2}...`, `--range START END`, `--list`
  - Progress tracking: `batch_progress.json` at `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json` (auto-resume, skip logic)
  - Skip check: HTML >5KB + audio dir 3+ mp3s = already done

## [04.4] Quality Bar (Non-Negotiable — Per Lesson)
- HTML file > 5KB with 7-12 cinematic animated slides
- Animated SVG illustrations (not static images)
- Minimal on-screen text (80 chars max per text block)
- Long detailed Hindi narrations — natural voice
- Per-slide TTS mp3 audio files (minimum 3)
- Brand colors: `#22c55e` (green), `#0ea5e9` (blue), `#7c6aff` (purple), `#f59e0b` (amber)
- Smooth slide transitions, gradient backgrounds
- Video feel, not webpage feel

## [04.5] Total Inventory (Current)
- Total programs: 44
- Total lessons: ~6,439
- Estimated time per lesson: 2-5 minutes (TTS dependent)
- Internet required for edge-tts (Microsoft Azure TTS API)

# [05] Targets Parsing Rules (3 Formats)
Input Targets string ko parse karo. 3 formats supported:

## [05.1] Format 1: CSV File
Example: `"Course_ACLE_Planner.csv"`

Rules:
- If targets string ends with `.csv` => CSV mode activate
- CSV path resolve karo: `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
- CSV mode me **har run me sirf 1 pending row** process karo (ACC CSV Single-Row Rule)
- Batch/loop forbidden (`WF_CSV_BATCH_FORBIDDEN`)

CSV Schema (required columns):
- `id` (integer, unique)
- `name` (program name — SSOT target)
- `status` (0=pending, 1=success, 2=failed)
- `description` (row-level prompt/context — REQUIRED, non-empty)
- `planning` (0/1/2 — stage status)
- `planning_save` (0/1/2)
- `code` (0/1/2)
- `code_save` (0/1/2)
- `doc` (0/1/2)
- `doc_save` (0/1/2)

Optional columns:
- `lessons_total` (integer — total lessons in program)
- `lessons_converted` (integer — successfully converted count)
- `lessons_failed` (integer — failed conversion count)
- `modified` (timestamp — ISO-8601)
- `created` (timestamp — ISO-8601)

CSV Processing Rules:
1) Sort rows by `id` ascending (fallback: file order)
2) Pick EXACTLY ONE row where `status == 0`
3) Validate `description` non-empty (else fail with `ACC_ERR_CSV_DESCRIPTION_MISSING`)
4) Process that single row: Planning → Code → Documentation
5) Update stage columns after each stage completes
6) Mark `status=1` on success, `status=2` on failure
7) STOP after 1 row

Hard rule:
- Agar `description` empty/missing ho, row ko "processed/failed" mark mat karo
- Row pending hi rahe jab tak prompt fix na ho

## [05.2] Format 2: Comma-Separated Program Names
Example: `"AI_Developer, Advanced_Data_Analytics"`

Rules:
- Comma se split karo
- Har item trim karo
- Empty item milay => error (`WF_TARGETS_INVALID`)
- Case preserve karo (folder names case-sensitive hain)
- Extensions forbidden (`.md`, `.html`, `.csv` etc yahan nahi)
- Har program name default source root (`/Aliens/Course/`) ke under exist karna chahiye

Validation:
- Har program folder exist check karo: `/Aliens/Course/{ProgramName}/`
- Missing folder => error per-target (`WF_TARGET_MISSING`) + skip that target

## [05.3] Format 3: Directory Path (Source Root)
Example: `"/Aliens/Course"`

Rules:
- If targets string starts with `/` or contains `:\` (absolute path) => Directory Path mode
- Given path = source root directory jahan se programs scan honge
- Directory ke under sab sub-folders = program names
- Path exist karna mandatory hai, warna error (`WF_TARGET_MISSING`)

Implementation:
1) Verify directory exists at given path
2) Scan directory for all sub-folders (each sub-folder = 1 program)
3) Sort alphabetically
4) Apply range filter from Description (if present) — see [05.5]
5) Resulting list = target programs

Note:
- Default source root is `/Aliens/Course/` — agar user yeh path de to wahi default behavior hai
- Custom path support future Course repos ke liye useful hai

## [05.4] Format Detection Logic
```
IF targets.endsWith('.csv'):
    mode = CSV
ELIF targets.startsWith('/') OR targets.contains(':\\'):
    mode = DIRECTORY_PATH
ELSE:
    mode = NAMES (comma-separated)
```

## [05.5] Description Range Filter (Optional — Applies to All Modes)
Description me optional range suffix support hai:
- Pattern: `"... : N to M"` (colon separator ke baad `N to M`)
- N aur M dono inclusive hain, 1-based
- Programs ko alphabetically sort karke #N se #M tak filter karo

Parsing:
1) Description string me last `:` ke baad ka part check karo
2) If pattern matches `{N} to {M}` (after trim):
   - Extract START = N, END = M
   - Range filter active
3) If no match: range filter inactive, process all resolved programs

Range filter logic:
1) Resolved programs list lo (from CSV rows / comma-names / directory scan)
2) Sort alphabetically
3) Slice [START-1 : END] (0-based indexing; both inclusive in 1-based)
4) Invalid range => error (`WF_RANGE_INVALID`): START < 1 or END > total programs
5) Resulting filtered list = final target programs

Examples:
- `Description: "Convert lessons : 1 to 10"` → programs #1 to #10 alphabetically
- `Description: "ULTRA Premium conversion : 11 to 20"` → programs #11 to #20
- `Description: "Convert all lessons"` → no range filter, process all

Note:
- Range filter Description me hona mandatory nahi — agar nahi hai to sab programs process honge
- CSV mode me range filter CSV ke resolved programs par lagega (sorted by name, not by id)
- Multi-PC distribution me har PC ko alag range dena preferred hai

# [06] Execution Plan (Deterministic Order)

## [06.1] Non-CSV Mode (Names / Directory Path)
For EACH ProgramName in resolved targets list (after range filter from Description, if any):

### Step 1: Validate Program Exists
- Path: `/Aliens/Course/{ProgramName}/`
- If folder missing => skip with `WF_TARGET_MISSING` for this program
- If folder exists => proceed

### Step 2: Planning
Call:
- `Workflow("Course_ACLE-Planning", "{ProgramName}", "{Description}")`

Expected Output:
- `/Aliens/.Alien/{Developer_Username}/Planning/Course_ACLE/{ProgramName}.plan.md`

Gate:
- Planning complete hona mandatory hai Code se pehle
- Planning fail => skip Code + Documentation for this program

### Step 3: Code (Lesson Conversion)
Call:
- `Workflow("Course_ACLE-Code", "{ProgramName}", "{Description}")`

Expected Output:
- `/Aliens/Course_ACLE/{ProgramName}/` me converted HTML + narrations + audio files

Gate:
- Code fail => Documentation still run ho sakta hai (partial results document karo)

### Step 4: Documentation
Call:
- `Workflow("Course_ACLE-Documentation", "{ProgramName}", "{Description}")`

Expected Output:
- `/Aliens/Docs/Course_ACLE/{ProgramName}.md`

## [06.2] CSV Mode
Per picked row (single row per run):

### Step 1: Validate
- `name` column se ProgramName resolve karo
- Validate folder exists: `/Aliens/Course/{ProgramName}/`
- Missing => mark `status=2` + skip

### Step 2: Planning
Call:
- `Workflow("Course_ACLE-Planning", "{ProgramName}", "{row.description}")`
- On success: mark `planning=1`, `planning_save=1`
- On failure: mark `planning=2`, `status=2`, skip remaining

### Step 3: Code
Call:
- `Workflow("Course_ACLE-Code", "{ProgramName}", "{row.description}")`
- On success: mark `code=1`, `code_save=1`, update `lessons_converted`/`lessons_failed` if columns exist
- On failure: mark `code=2`, `status=2`

### Step 4: Documentation
Call:
- `Workflow("Course_ACLE-Documentation", "{ProgramName}", "{row.description}")`
- On success: mark `doc=1`, `doc_save=1`, mark `status=1`
- On failure: mark `doc=2`, `status=2`

After each stage: **durable writeback** to CSV (save immediately after stage, not at the end).

# [07] Error Handling (Fail Fast per Target)
Default policy: continue to next target on failure (individual target fails, others proceed).

ErrorCodes:
- `WF_MANIFEST_MISSING` — `/Aliens/manifest.json` not found
- `WF_MANIFEST_INVALID` — manifest parse/validation failed
- `WF_TARGETS_INVALID` — targets string could not be parsed (empty, bad format)
- `WF_TARGET_MISSING` — program folder not found in `/Aliens/Course/`
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `WF_CHILD_FAILED` — child workflow returned failure
- `WF_PATH_RESOLUTION_FAILED` — output path cannot be resolved
- `WF_CSV_BATCH_FORBIDDEN` — attempted to process multiple CSV rows in one run
- `ACC_ERR_CSV_DESCRIPTION_MISSING` — CSV row `description` column empty/missing
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description parameter empty/missing (non-CSV mode)
- `WF_RANGE_INVALID` — Description range filter invalid (START < 1 or END > total)
- `WF_SOURCE_NOT_FOUND` — source directory not found (default `/Aliens/Course/` or given path)
- `WF_OUTPUT_ROOT_NOT_FOUND` — `/Aliens/Course_ACLE/` directory not found
- `WF_INTERNET_REQUIRED` — edge-tts requires internet, connection check failed
- `WF_CONVERTER_NOT_FOUND` — `convert_lesson.py` or `batch_all_programs.py` not found

Action guidance:
- Missing manifest => exact path create karne ko bolo
- Target missing => list available programs (`--list` command use karo)
- CSV description missing => user ko row me description fill karne ko bolo
- Child failed => child workflow id + program name + retry instructions do
- Internet required => network connectivity check karo, retry

# [08] Minimal Success Criteria
Per target program ke liye 3 child workflow outputs generated hon:
1) Planning artifact exists
2) Converted lessons exist (HTML + audio in Course_ACLE)
3) Documentation artifact exists

Per-lesson success check (delegated to Code child workflow):
- HTML file > 5KB
- Audio directory with 3+ mp3 files
- Narrations JSON exists

# [09] Multi-PC Distribution Rules (Non-Negotiable)

Jab multiple PCs par parallel conversion ho rahi ho:
1. **No program overlap**: har PC ko completely different programs assign hon
2. **Same repo structure**: `/Aliens/Course/` aur `/Aliens/Course_ACLE/` dono repos har PC pe same paths pe hon
3. **Independent progress**: har PC apna alag `batch_progress.json` maintain karega (`/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`)
4. **Merge via file copy**: sab PCs ka kaam hone ke baad, program folders ek main PC me copy/merge karo
5. **Range mode preferred**: `--range` flag se PCs me divide karo (easiest)

Distribution example (5 PCs, 44 programs):
- PC-1: `Workflow("Course_ACLE", "/Aliens/Course", "Convert lessons : 1 to 10")`
- PC-2: `Workflow("Course_ACLE", "/Aliens/Course", "Convert lessons : 11 to 20")`
- PC-3: `Workflow("Course_ACLE", "/Aliens/Course", "Convert lessons : 21 to 30")`
- PC-4: `Workflow("Course_ACLE", "/Aliens/Course", "Convert lessons : 31 to 40")`
- PC-5: `Workflow("Course_ACLE", "/Aliens/Course", "Convert lessons : 41 to 44")`

# [10] Safety Contract (Enforcement)
1. Source Course repo (`/Aliens/Course/`) = **READ-ONLY**. Koi file create/modify/delete nahi.
2. Shared assets (`/Aliens/Course_ACLE/assets/`) = **DO NOT MODIFY** via this workflow.
3. Converter scripts = **DO NOT MODIFY** via this workflow (scope outside).
4. `batch_progress.json` = **DO NOT MANUALLY EDIT** jab batch running ho. (Path: `/Aliens/.Alien/{Developer_Username}/State/Course_ACLE/batch_progress.json`)
5. NoDelete = true globally.
6. Internet required for TTS — check before starting.
7. No partial deployment: lesson "done" tab hai jab HTML + narrations JSON + audio sab complete.

# [11] CSV Task Mode Summary

## [11.1] Invocation
Allowed:
- `Workflow('Course_ACLE', 'Course_ACLE_Planner.csv', 'Description : 1 to 10')`
- `Workflow('Course_ACLE', 'AI_Developer, Advanced_Data_Analytics', 'Convert all lessons')`
- `Workflow('Course_ACLE', '/Aliens/Course', 'Description : 1 to 10')`

## [11.2] Hard Rules
- CSV mode: exactly 1 row per run (Single-Row Rule)
- Non-CSV mode: multiple programs allowed (loop per target)
- `description` required in both modes (CSV: per-row, non-CSV: workflow parameter)
- Range filter (`N to M`) sirf Description me valid hai (colon ke baad), Targets me nahi

# [12] Run Record (Post-Execution)
After all targets processed, save run summary:
- Path: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Course_ACLE.Run.md`
- Contents:
  - Run timestamp
  - Targets input (raw)
  - Resolved programs list
  - Per-program status (success/failed/partial)
  - Per-program lesson counts (total/converted/failed/skipped)
  - Child workflow call log
  - Errors encountered
  - Approvals pending (if any)
  - Total duration

# [13] Done Criteria
Workflow successful jab:
- Har target program ke liye Planning + Code + Documentation child workflows called
- Run record saved
- CSV mode: picked row status updated (1 or 2)
- Non-CSV mode: per-program status logged
- No StrictScopeOnly violations
