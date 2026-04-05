---
WorkflowId: Blogs_New
Type: Workflow_Plural
Domain: Blogs
Category: Blog Article Scraping + Translation Pipeline
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given a blog domain (ya CSV file),
  us blog ka sitemap extract karo, planner CSV banao, phir har article ko
  fetch karke humary custom HTML format me refactor karo (EN), aur
  Hinglish (HIEN) + Hindi (HI) me translate karke save karo.
  Yeh workflow khud code generate nahi karta — sirf coordination +
  target resolution + safety/approval enforce karta hai, aur child workflows ko
  deterministic order me call karta hai.
Invocation:
  Signature:
    - Workflow("Blogs_New", "healthline.com", "Description")
    - Workflow("Blogs_New", "healthline.csv", "Description")
  TargetsMeaning: >
    2 input formats supported:
    (1) Blog domain name — sitemap extract karke planner CSV banao, phir articles process karo.
        Example: "healthline.com", "webmd.com", "mayoclinic.org"
    (2) CSV filename — batch mode with per-row tracking. CSV path:
        /Aliens/Project/{CyborgID}/{FILENAME}.csv
    Range filtering Description me aata hai, Targets me nahi.
  DescriptionMeaning: >
    Human language constraints/requirements. Must NOT expand scope beyond blog article processing.
    Optional range suffix supported:
    (a) Numeric range: "Description : N to M" — articles sort karke #N se #M filter.
    Optional flags:
    - "download-only" — sirf EN (original) save karo, translation skip.
    - "translate-only" — sirf translation karo (EN must already exist).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Blogs_New/"
    Pattern: "{BlogSlug}_sitemap.plan.md"
  Code:
    EN_Path: "/Aliens/Blogs/{BlogName}/EN/{Article_Slug}.html"
    HIEN_Path: "/Aliens/Blogs/{BlogName}/HIEN/{Article_Slug}.html"
    HI_Path: "/Aliens/Blogs/{BlogName}/HI/{Article_Slug}.html"
    Pattern: "*.html (per article, per language)"
  Documentation:
    Path: "/Aliens/Docs/Blogs_New/"
    Pattern: "{BlogSlug}.md"
  Planner:
    Path: "/Aliens/Project/{Developer_Username}/{BlogName}.csv"
    Pattern: "{BlogName}.csv"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Blogs_New.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Blogs_New-Planning
  - Blogs_New-Code
  - Blogs_New-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Source blog websites are external (internet required).
  - Output Blogs repo (`/Aliens/Blogs/`) me write hota hai.
  - LLM translation (OpenAI gpt-4o-mini) ke liye OPENAI_API_KEY env var mandatory hai.
  - Converter scripts: `/Aliens/Cyborg/AlienCyborg/Code/blogs_new.py` (core module) +
    `batch_blogs.py` (batch orchestrator).
  - Batch progress tracking: `/Aliens/.Alien/{Developer_Username}/State/Blogs_New/batch_progress.json`
  - Planner CSV: `/Aliens/Project/{Developer_Username}/{BlogName}.csv`
  - Range filtering "1 to 100" Description me aata hai.
  - BlogName = domain ka first part capitalized (healthline.com → Healthline).
  - 1000-file limit per folder: jab EN/ me 1000 .html files ho jayein, overflow
    sub-folder `0002/`, `0003/` etc. create hoga. Same for HIEN/ and HI/.
  - Cross-language folder consistency: agar EN me article sub-folder me gaya,
    HIEN/HI me bhi usi sub-folder me jayega.
SpeedMode:
  Enabled: true
  Description: >
    Planning aur Documentation child workflows bypass hote hain for speed.
    Sirf Code (fetch + refactor + translate + save) execute hota hai.
    Planning phase sirf sitemap extraction + CSV creation karta hai (agar domain mode ho).
---

# Blogs_New

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Target resolution (Domain → sitemap → CSV / CSV file → article list)
- Description parsing for range filter (numeric "N to M")
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct code generate/modify is file me (child workflow karega)
- Extra articles invent karna / unrelated scope add karna
- Description me jo nahi bola, woh add karna
- Other blogs process karna jo target me nahi hain

# [03] Manifest Gate (Mandatory)
1) Read manifest:
   - "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (sitemap extraction + CSV creation)
   - Code generation (article processing): allowed (writes to /Aliens/Blogs/, not prod code)
   - DB touch: NOT part of this workflow
4) Missing/invalid permission => fail fast (standard errors)

# [04] Blogs Domain Context (Hard Rules)

## [04.1] Blog Name Derivation
- Domain: `healthline.com` → BlogName: `Healthline`
- Domain: `webmd.com` → BlogName: `Webmd`
- Domain: `mayoclinic.org` → BlogName: `Mayoclinic`
- Rule: domain ka first segment (before first dot), first letter capitalize.

## [04.2] Output Structure (SSOT — WRITE)
```
/Aliens/Blogs/
  └── {BlogName}/                              # e.g. Healthline/
       ├── EN/                                 # English (original refactored)
       │    ├── {Article_Slug}.html            # First 1000 articles
       │    ├── {Article_Slug}.html
       │    ├── ...
       │    ├── 0002/                          # Overflow sub-folder (when EN/ hits 1000)
       │    │    ├── {Article_Slug}.html
       │    │    └── ...
       │    └── 0003/                          # Next overflow
       │         └── ...
       ├── HIEN/                               # Hinglish (translated)
       │    ├── {Article_Slug}.html
       │    ├── 0002/
       │    │    └── ...
       │    └── ...
       └── HI/                                 # Hindi (translated)
            ├── {Article_Slug}.html
            ├── 0002/
            │    └── ...
            └── ...
```

Key rules:
- **Same directory structure**: agar EN me article `0002/` me gaya, HIEN/HI me bhi `0002/` me jayega.
- **1000-file limit**: jab ek folder me 1000 .html files ho jayein, next folder create hota hai.
- **Sub-folder naming**: `0002`, `0003`, `0004`... (root folder = implicit `0001`).
- **Article_Slug**: URL se derived, underscored, title-cased (e.g. `What_Is_Diabetes`).

## [04.3] Planner CSV Structure
```
/Aliens/Project/{CyborgID}/{BlogName}.csv
```

CSV Schema (required columns):
- `id` (integer, unique, auto-increment)
- `url` (full article URL — SSOT target)
- `slug` (Article_Slug — derived from URL)
- `title` (article title — extracted from page)
- `status` (0=pending, 1=success, 2=failed)
- `description` (row-level prompt/context)
- `en_saved` (0/1/2 — EN processing status)
- `hien_saved` (0/1/2 — HIEN translation status)
- `hi_saved` (0/1/2 — HI translation status)
- `folder` (sub-folder name if overflow, empty if root)
- `modified` (timestamp — ISO-8601)
- `created` (timestamp — ISO-8601)
- `error` (error message if failed)

## [04.4] Sitemap Extraction
- Primary: `https://{domain}/sitemap.xml`
- Fallback 1: `https://{domain}/sitemap_index.xml`
- Fallback 2: `https://{domain}/robots.txt` (look for `Sitemap:` directive)
- Fallback 3: `https://{domain}/wp-sitemap.xml` (WordPress)
- Handle sitemap index (nested sitemaps — fetch each child sitemap)
- Handle gzip-compressed sitemaps (.xml.gz)
- Extract all `<loc>` URLs from sitemap XML
- Filter: only article/post URLs (skip category pages, tag pages, author pages)

## [04.5] HTML Template (Aliens Blog Format)
Custom branded HTML with:
- Clean semantic HTML5 structure
- Inline CSS (standalone files, no external dependencies)
- Aliens brand colors (#22c55e green, #0ea5e9 blue, #7c6aff purple)
- Article content: headings, paragraphs, lists, tables, images, blockquotes
- Meta tags: source URL, blog name, language, generation date
- Header: "Aliens Blogs" brand + source attribution
- Footer: source link + copyright

## [04.6] Content Extraction Rules
From fetched blog HTML:
- Remove: scripts, styles, navigation, sidebars, ads, social widgets, comments
- Keep: headings (h1-h6), paragraphs, lists (ul/ol), tables, images, blockquotes, code blocks
- Extract: article title from `<h1>` or `<title>`
- Extract: main content from `<article>`, `<main>`, or largest content area
- Images: preserve `src` and `alt` attributes (absolute URLs)

## [04.7] Translation Rules
- EN → HIEN: Hinglish (Roman Hindi + English mix). Natural conversational tone.
  Keep proper nouns in English. Keep technical terms in English.
- EN → HI: Hindi (Devanagari script). Formal encyclopedic tone.
  Technical terms: Hindi first, English in parentheses.
- Translation is HTML-aware: preserve all HTML tags, only translate text content.
- Section-by-section translation for reliability (split by `<h2>` headings).

## [04.8] Quality Bar (Per Article)
- HTML file well-formed and valid
- Content extracted faithfully (no missing sections)
- Translations natural and readable
- All 3 language versions present (EN, HIEN, HI)
- Source URL preserved in meta tags
- Images have absolute URLs

# [05] Targets Parsing Rules (2 Formats)

## [05.1] Format 1: Blog Domain
Example: `"healthline.com"`

Rules:
- If targets string does NOT end with `.csv` => Domain mode
- Domain validated (format check: contains at least one dot, no spaces)
- Step 1: Extract sitemap → get article URLs
- Step 2: Build Planner CSV at `/Aliens/Project/{CyborgID}/{BlogName}.csv`
- Step 3: Process articles from CSV (one per run in CSV mode, or batch in SpeedMode)

## [05.2] Format 2: CSV File
Example: `"healthline.csv"`

Rules:
- If targets string ends with `.csv` => CSV mode
- CSV path resolve karo: `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`
- CSV mode me **har run me sirf 1 pending row** process karo (ACC CSV Single-Row Rule)
- Batch/loop forbidden (`WF_CSV_BATCH_FORBIDDEN`)

## [05.3] Format Detection Logic
```
IF targets.endsWith('.csv'):
    mode = CSV
ELSE:
    mode = DOMAIN
```

# [06] Execution Plan (Deterministic Order)

## [06.1] Domain Mode
### Step 1: Sitemap Extraction (Planning Phase)
- Run: `python blogs_new.py --sitemap {domain}`
- Output: Planner CSV at `/Aliens/Project/{CyborgID}/{BlogName}.csv`
- Articles listed with URLs, slugs, status=0

### Step 2: Process Articles (Code Phase)
- Run: `python batch_blogs.py --run {BlogName}`
- Picks one pending row (status=0) from CSV
- For that article:
  a) Fetch HTML from URL
  b) Extract content (title + body)
  c) Refactor to Aliens HTML format → save EN
  d) Translate body to Hinglish → save HIEN
  e) Translate body to Hindi → save HI
  f) Update CSV row: en_saved=1, hien_saved=1, hi_saved=1, status=1
- Repeat for next pending rows (one per run)

## [06.2] CSV Mode
- Same as Domain Mode Step 2 (articles already in CSV)
- Pick one pending row, process, update

# [07] Error Handling (Fail Fast per Target)
Default policy: continue to next article on failure.

ErrorCodes:
- `WF_MANIFEST_MISSING` — `/Aliens/manifest.json` not found
- `WF_MANIFEST_INVALID` — manifest parse/validation failed
- `WF_TARGETS_INVALID` — targets string could not be parsed
- `WF_DOMAIN_INVALID` — blog domain format invalid
- `WF_SITEMAP_NOT_FOUND` — no sitemap found for domain
- `WF_SITEMAP_EMPTY` — sitemap found but no article URLs
- `WF_ARTICLE_FETCH_FAILED` — could not fetch article HTML
- `WF_CONTENT_EXTRACTION_FAILED` — could not extract article content
- `WF_TRANSLATION_FAILED` — LLM translation failed
- `WF_API_KEY_MISSING` — OPENAI_API_KEY env var not set
- `WF_CSV_BATCH_FORBIDDEN` — attempted to process multiple CSV rows in one run
- `ACC_ERR_CSV_DESCRIPTION_MISSING` — CSV row description empty/missing
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description parameter empty/missing
- `WF_INTERNET_REQUIRED` — internet connection required

# [08] Minimal Success Criteria
Per article:
1) EN HTML saved (content extracted + refactored)
2) HIEN HTML saved (translated to Hinglish)
3) HI HTML saved (translated to Hindi)
4) CSV row updated with status=1

# [09] Converter Scripts (Reference)
- `blogs_new.py` (core module): sitemap extraction, content fetch, HTML refactoring, translation
  - `--sitemap {domain}` → extract sitemap + build CSV
  - `--article {url}` → process single article
  - `--no-translate` → skip translation (EN only)
- `batch_blogs.py` (batch orchestrator): CSV-driven batch processing
  - `--sitemap {domain}` → extract sitemap + build CSV
  - `--run {BlogName}` → process next pending article from CSV
  - `--status {BlogName}` → progress report
  - `--list {BlogName}` → numbered list of articles
  - `--range START END` → filter by row range
