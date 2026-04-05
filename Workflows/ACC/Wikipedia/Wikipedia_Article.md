---
WorkflowId: Wikipedia_Article
Type: Workflow_Plural
Domain: Wikipedia
Category: Wikipedia Article Download + Translation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: true
Purpose: >
  Wikipedia raw articles ko download karo, EN me save karo,
  phir Hinglish (HIEN) aur Hindi (HI) me translate karke save karo.
  Mission: Wikipedia ka saara knowledge Hindi/Hinglish me lana —
  same content, same images, same structure, bas language change.
Invocation:
  Signature:
    - Workflow("Wikipedia_Article", "A", "Description")
    - Workflow("Wikipedia_Article", "Wikipedia_A.csv", "Description")
    - Workflow("Wikipedia_Article", "Albert_Einstein", "Ek article download aur translate karo")
  TargetsMeaning: >
    3 input formats supported:
    (1) Single letter (A-Z) — Wikipedia API se us letter ke saare articles enumerate karo,
        Planner CSV banaao, phir ek ek article process karo.
    (2) CSV filename — batch mode with per-row tracking. CSV path:
        /Aliens/Project/{CyborgID}/Wikipedia_{RunID}.csv
    (3) Article title — single article download + translate.
    Range filter Description me aata hai (numeric ya alphabetical).
  DescriptionMeaning: >
    Human language constraints/requirements.
    Optional range suffix:
    (a) Numeric range: "N to M" — articles alphabetically sort karke #N se #M filter.
    (b) Alphabetical sub-range: "Aa to Ad" — sirf matching prefix wale articles.
    SpeedMode default ON: Planning + Documentation bypass.
SpeedMode:
  Enabled: true
  Description: >
    Planning aur Documentation child workflows bypass hote hain for speed.
    Sirf Code (download + translate + save) execute hota hai.
    Workflow files exist karte hain (reference ke liye), but execution skip.
Outputs:
  EN:
    Path: "/Aliens/Wikipedia/EN/{Letter}{Seq}/{Slug}.md"
    Description: "Raw wikitext downloaded from English Wikipedia"
  HIEN:
    Path: "/Aliens/Wikipedia/HIEN/{Letter}{Seq}/{Slug}.md"
    Description: "Hinglish (romanized Hindi + English mix) translated article"
  HI:
    Path: "/Aliens/Wikipedia/HI/{Letter}{Seq}/{Slug}.md"
    Description: "Hindi (Devanagari script) translated article"
  PlannerCSV:
    Path: "/Aliens/Project/{CyborgID}/Wikipedia_{Letter}.csv"
    Description: "Auto-generated Planner CSV from Wikipedia API enumeration"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Wikipedia_Article.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Wikipedia_Article-Planning (BYPASSED in SpeedMode)
  - Wikipedia_Article-Code
  - Wikipedia_Article-Documentation (BYPASSED in SpeedMode)
Safety:
  NoDelete: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Internet MANDATORY: Wikipedia API for article download + OpenAI API for translation.
  - Wikipedia raw URL pattern: https://en.wikipedia.org/w/index.php?title={Title}&action=raw
  - Wikipedia sitemap: https://en.wikipedia.org/w/rest.php/site/v1/sitemap/0
  - MediaWiki allpages API for article enumeration by letter prefix.
  - Folder structure: /Aliens/Wikipedia/{Language}/{Letter}{Seq:04d}/{Slug}.md
  - Max 1000 files per folder. After 1000 → next sequence folder (A0001 → A0002 → ...).
  - Same folder numbering across EN/HIEN/HI for consistency.
  - Translation uses LLM (OpenAI gpt-4o-mini default). API key via OPENAI_API_KEY env var.
  - Wikitext markup 100% preserved during translation (templates, links, images, refs, categories).
  - SpeedMode = Planning + Documentation bypass for maximum throughput.
  - Article slug = Wikipedia title with spaces → underscores (Albert Einstein → Albert_Einstein).
  - Converter scripts: /Aliens/Cyborg/AlienCyborg/Code/wikipedia_article.py (core) +
    batch_wikipedia.py (batch orchestrator + CSV planner).
  - Progress: /Aliens/.Alien/{Developer_Username}/State/Wikipedia_Article/batch_progress.json
---

# Wikipedia_Article

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope:
- Wikipedia articles download karna (raw wikitext)
- 3 languages me save karna (EN, HIEN, HI)
- Planner CSV generation from Wikipedia API
- Child workflows ko deterministic order me call karna

Forbidden:
- Articles ka content modify karna (beyond translation)
- Wikipedia pe kuch upload/edit karna
- Source Wikipedia articles ko alter karna
- Unrelated repos/folders me likhna

# [03] Manifest Gate (Mandatory)
1) Read manifest: "/Aliens/manifest.json"
2) Approval policy: per manifest (SpeedMode me lightened)
3) Permissions:
   - Internet access: REQUIRED (Wikipedia API + Translation API)
   - File write: /Aliens/Wikipedia/ (all 3 language dirs)
   - CSV write: /Aliens/Project/{CyborgID}/
4) Missing/invalid => fail fast

# [04] Wikipedia Domain Context (Hard Rules)

## [04.1] Source: English Wikipedia (READ via API)
- Raw article URL: `https://en.wikipedia.org/w/index.php?title={Title}&action=raw`
- Allpages API: `https://en.wikipedia.org/w/api.php?action=query&list=allpages&apprefix={Prefix}&aplimit=500&format=json`
- Sitemap index: `https://en.wikipedia.org/w/rest.php/site/v1/sitemap/0`
- Article format: raw MediaWiki wikitext (.md extension for storage)

## [04.2] Output Structure (SSOT — WRITE)
```
/Aliens/Wikipedia/
  ├── EN/                          # English (raw wikitext from Wikipedia)
  │    ├── A0001/                  # First 1000 articles starting with A
  │    │    ├── Albert_Einstein.md
  │    │    ├── Algorithm.md
  │    │    └── ... (up to 1000 files)
  │    ├── A0002/                  # Next 1000 articles starting with A
  │    │    └── ...
  │    ├── B0001/
  │    │    └── ...
  │    └── .../
  ├── HIEN/                        # Hinglish translated (same structure)
  │    ├── A0001/
  │    │    ├── Albert_Einstein.md
  │    │    └── ...
  │    └── .../
  └── HI/                          # Hindi translated (same structure)
       ├── A0001/
       │    ├── Albert_Einstein.md
       │    └── ...
       └── .../
```

Key rules:
- **Max 1000 files per folder**. Count sirf `.md` files.
- Folder naming: `{Letter}{Seq:04d}` — A0001, A0002, B0001, C0001, etc.
- **Same folder assignment across all 3 languages**. Agar EN me Albert_Einstein A0001 me hai,
  to HIEN aur HI me bhi A0001 me save hoga.
- Slug = article title with spaces → underscores: `Albert Einstein` → `Albert_Einstein`
- File extension: `.md` (raw wikitext stored as markdown)

## [04.3] Folder Assignment Logic
```
function get_target_folder(language, letter):
    base = /Aliens/Wikipedia/{language}/
    # Find all existing folders for this letter
    folders = sorted(dirs matching {letter}????)
    if no folders:
        create {letter}0001/
        return {letter}0001/
    last_folder = folders[-1]
    if count_md_files(last_folder) >= 1000:
        next_seq = int(last_folder.name[1:]) + 1
        create {letter}{next_seq:04d}/
        return {letter}{next_seq:04d}/
    return last_folder
```

Cross-language sync:
- Process article → determine folder from EN first
- Use SAME folder name for HIEN and HI (create if needed)

## [04.4] Translation Rules (Non-Negotiable)
Translation backend: LLM (OpenAI gpt-4o-mini default, configurable)

### Hinglish (HIEN) Translation:
- Target: Hindi written in Roman/Latin script mixed with English
- Preserve ALL wikitext markup: `{{templates}}`, `[[links]]`, `[[File:...]]`,
  `<!-- comments -->`, `<ref>...</ref>`, categories, infoboxes
- Only translate readable prose/text content
- Keep proper nouns, technical terms, filenames, URLs, references in English AS-IS
- Keep numbers, dates, mathematical expressions AS-IS
- Write in natural conversational Hinglish (jaise educated Indians bolte hain)
- Do NOT transliterate English technical terms to Hindi script
- Output must have EXACTLY same structure and line count as input

### Hindi (HI) Translation:
- Target: Pure Hindi in Devanagari script (नागरी लिपि)
- Same markup preservation rules as Hinglish
- Technical terms can have English in parentheses: विशेष सापेक्षता (Special Relativity)
- Keep proper nouns in English
- Everything else in Devanagari

### Section-by-Section Translation:
- Split article by `\n== ` (level-2 headings)
- Translate each section separately (stays within token limits)
- Lead section (before first `== `) is translated separately
- Reassemble sections after translation
- Each section retried up to 3 times on failure

## [04.5] Planner CSV Schema
Path: `/Aliens/Project/{CyborgID}/Wikipedia_{Letter}.csv`

Required columns:
- `id` (integer, unique, sequential)
- `name` (article title/slug — e.g. `Albert_Einstein`)
- `letter` (first letter — e.g. `A`)
- `folder` (assigned folder — e.g. `A0001`)
- `status` (0=pending, 1=success, 2=failed)
- `description` (auto-filled: "Download and translate {Title}")
- `en_saved` (0/1 — English raw download saved)
- `hien_saved` (0/1 — Hinglish translation saved)
- `hi_saved` (0/1 — Hindi translation saved)
- `modified` (timestamp ISO-8601)
- `created` (timestamp ISO-8601)
- `error` (error message if failed, empty otherwise)

CSV Processing (per ACC rules):
1) Sort by `id` ascending
2) Pick EXACTLY ONE row where `status == 0`
3) Process: download EN → translate HIEN → translate HI
4) Update columns after each step
5) Mark `status=1` on full success, `status=2` on failure
6) STOP after 1 row

## [04.6] Converter Scripts (Reference)
Located at `/Aliens/Cyborg/AlienCyborg/Code/`:

- `wikipedia_article.py` — Core module:
  - `fetch_raw_article(title)` — download raw wikitext from Wikipedia
  - `save_article(title, content, language, folder_name)` — save to correct path
  - `translate_article(content, target_lang)` — LLM-based translation
  - `process_article(title)` — full pipeline (download → save EN → translate → save HIEN/HI)
  - CLI: `--title {Title}`, `--download-only`, `--translate-only`, `--no-hi`

- `batch_wikipedia.py` — Batch orchestrator:
  - `build_csv(letter)` — enumerate articles from Wikipedia API → create planner CSV
  - `process_next(letter)` — process one pending row from CSV
  - CLI: `--build-csv A`, `--run A`, `--status A`, `--list A`, `--range 1 100`
  - Progress: `/Aliens/.Alien/{CyborgID}/State/Wikipedia_Article/batch_progress.json`

# [05] Targets Parsing Rules

## [05.1] Format 1: Single Letter (A-Z)
Example: `"A"` or `"B"`

Rules:
- If targets is a single uppercase letter (A-Z) => Letter mode
- Step 1: Check if Planner CSV exists: `/Aliens/Project/{CyborgID}/Wikipedia_{Letter}.csv`
- Step 2: If CSV not exists → create it using Wikipedia allpages API
- Step 3: Process CSV mode (one row per run)

Implementation:
1) Parse targets as single letter
2) Check/create Planner CSV
3) Switch to CSV mode with created CSV

## [05.2] Format 2: CSV Filename
Example: `"Wikipedia_A.csv"`

Rules:
- If targets ends with `.csv` => CSV mode
- CSV path: `/Aliens/Project/{CyborgID}/{filename}`
- Per ACC CSV rules: one row per run, status tracking, fail-fast

## [05.3] Format 3: Article Title
Example: `"Albert_Einstein"` or `"Theory_of_relativity"`

Rules:
- If targets is not single letter and not CSV => treat as article title
- Direct single-article processing (no CSV needed)
- Download + translate + save immediately

## [05.4] Format Detection Logic
```
IF targets.length == 1 AND targets[0] in A-Z:
    mode = LETTER
ELIF targets.endsWith('.csv'):
    mode = CSV
ELSE:
    mode = SINGLE_ARTICLE
```

## [05.5] Description Range Filter (Optional)
Same as ACLE_Wiki range system:
- Numeric: "N to M" — CSV rows sorted by name, pick #N to #M
- Prefix: "Aa to Ad" — articles starting with specific prefix

# [06] Execution Plan

## [06.1] Letter Mode
Input: `Workflow("Wikipedia_Article", "A", "Description")`

### Step 1: Build or Load Planner CSV
- CSV path: `/Aliens/Project/{CyborgID}/Wikipedia_A.csv`
- If CSV not exists: call `batch_wikipedia.py --build-csv A`
  - Enumerates ALL articles starting with 'A' from Wikipedia API
  - Saves to CSV with status=0 for all rows
  - This is a one-time operation (can take minutes for large letters)
- If CSV exists: load it

### Step 2: Apply Range Filter
- If Description has range (e.g. "1 to 1000"):
  - Filter CSV rows by range
- If no range: process all pending rows

### Step 3: Process Next Pending Article
- Pick first row where `status == 0`
- Call Code child workflow (or direct script execution in SpeedMode)

### Per Article Steps (SpeedMode — Planning + Docs Bypassed):
1) **Download EN**: fetch raw wikitext from Wikipedia API
   - URL: `https://en.wikipedia.org/w/index.php?title={name}&action=raw`
   - Save to: `/Aliens/Wikipedia/EN/{folder}/{name}.md`
   - Mark `en_saved=1`

2) **Translate HIEN**: translate wikitext to Hinglish via LLM
   - Section-by-section translation
   - Save to: `/Aliens/Wikipedia/HIEN/{folder}/{name}.md`
   - Mark `hien_saved=1`

3) **Translate HI**: translate wikitext to Hindi via LLM
   - Section-by-section translation
   - Save to: `/Aliens/Wikipedia/HI/{folder}/{name}.md`
   - Mark `hi_saved=1`

4) **Update CSV**: mark `status=1`, update `modified` timestamp

### Step 4: STOP (one row per run, per ACC rules)

## [06.2] CSV Mode
Same as Letter Mode Step 3-4, but CSV already exists.

## [06.3] Single Article Mode
Direct processing without CSV:
1) Download EN
2) Translate HIEN
3) Translate HI
4) Save all 3

# [07] Error Handling
ErrorCodes:
- `WF_MANIFEST_MISSING` — manifest not found
- `WF_INTERNET_REQUIRED` — no internet connection
- `WF_WIKIPEDIA_API_ERROR` — Wikipedia API returned error
- `WF_ARTICLE_NOT_FOUND` — Wikipedia article does not exist (404)
- `WF_TRANSLATION_FAILED` — LLM translation API failed
- `WF_API_KEY_MISSING` — OPENAI_API_KEY not set
- `WF_FOLDER_FULL` — folder has 1000 files (should auto-create next)
- `WF_CSV_BUILD_FAILED` — Wikipedia API enumeration failed
- `WF_ARTICLE_TOO_LARGE` — article exceeds safe translation size
- `ACC_ERR_CSV_DESCRIPTION_MISSING` — CSV row description empty
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description parameter empty

Recovery:
- API rate limits: exponential backoff (1s, 2s, 4s, 8s, max 30s)
- Translation failure per section: retry 3 times, then skip section with [TRANSLATION_FAILED] marker
- Download failure: retry 3 times, then mark row as failed
- Network error: retry with backoff, then fail-fast

# [08] Success Criteria
Per article:
1) EN raw file exists and size > 0
2) HIEN translated file exists and size > 0
3) HI translated file exists and size > 0
4) All 3 files in correct folder structure
5) Wikitext markup preserved in translations

# [09] Multi-PC Distribution
- Har PC ko alag letter ya alag range assign karo
- Example: PC1 = A (1-50000), PC2 = A (50001-100000), PC3 = B
- CSV row overlap forbidden
- Each PC maintains independent progress
