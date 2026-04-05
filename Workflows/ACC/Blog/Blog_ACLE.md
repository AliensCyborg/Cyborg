---
WorkflowId: Blog_ACLE
Type: Workflow_Plural
Domain: Blog
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: given one-or-more original Blog article paths (ya CSV file ya source directory path),
  ACLE (Aliens Cinematic Learning Experience) cinematic video lessons generate karo by delegating
  to child workflows. Yeh workflow khud code generate nahi karta — sirf coordination +
  target resolution + safety/approval enforce karta hai, aur child workflows ko
  deterministic order me call karta hai.
  IMPORTANT: Yeh Blogs_ACLE (third-party scraped HTML blogs) se ALAG hai.
  Yeh Blog_ACLE (singular) original Aliens Blog articles (.md files from Blog_Article workflow) ke liye hai.
Invocation:
  Signature:
    - Workflow("Blog_ACLE", "HIEN.csv", "Description : 1 to 100")
    - Workflow("Blog_ACLE", "EN/AI_Vs_Human_Intelligence, HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "Convert blog articles")
    - Workflow("Blog_ACLE", "/Aliens/Blog/HIEN", "Description : 1 to 50")
    - Workflow("Blog_ACLE", "HIEN", "HIEN k saare blog articles ko convert kr do")
  TargetsMeaning: >
    4 input formats supported:
    (1) CSV filename — batch mode with per-row tracking.
    (2) Comma-separated article paths — relative to /Aliens/Blog/ without .md extension.
    (3) Directory path — source Blog directory path; articles (.md files) us path ke under scan honge.
    (4) Language name shorthand — single name without slashes/commas that matches a language folder
        inside /Aliens/Blog/ (e.g. "HIEN" → /Aliens/Blog/HIEN/). Articles scan honge.
    Range filtering Description me aata hai, Targets me nahi. Two range types supported:
    - Numeric: "1 to 100" — articles alphabetically sort karke #1 se #100 pick.
    - Alphabetical: "A to D" — article files filter by first-letter range A-D.
  DescriptionMeaning: >
    Human language constraints/requirements. Must NOT expand scope beyond Blog ACLE conversion.
    Optional range suffix supported (two types):
    (a) Numeric range: "Description : N to M" — articles alphabetically sort karke #N se #M filter.
    (b) Alphabetical range: "Description : A to D" — article files jinke naam A-D se start
        hote hain, sirf wohi include.
    Range sirf Description me valid hai, Targets me nahi.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Blog_ACLE/"
    Pattern: "{ArticleSlug}.plan.md"
  Code:
    Path: "/Aliens/Blog_ACLE/{Language}/{ArticleName}.html"
    Pattern: "*.html + *_narrations.json + audio_*/ (per article)"
  Documentation:
    Path: "/Aliens/Docs/Blog_ACLE/"
    Pattern: "{ArticleSlug}.md"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Blog_ACLE.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Blog_ACLE-Planning
  - Blog_ACLE-Code
  - Blog_ACLE-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Source Blog repo (`/Aliens/Blog/`) is strictly READ-ONLY. Koi file modify/delete nahi.
  - Output Blog_ACLE repo (`/Aliens/Blog_ACLE/`) me write hota hai.
  - edge-tts (Microsoft Azure TTS) ke liye internet connection mandatory hai.
  - Source articles .md format me hain (Blog_Article workflow se bani hain — original content).
  - Source structure: /Aliens/Blog/{Language}/{Article_Slug}.md (EN/, HIEN/, HI/)
  - Output structure: /Aliens/Blog_ACLE/{Language}/{Article_Slug}.html + narrations + audio
  - Converter scripts: `/Aliens/Cyborg/AlienCyborg/Code/convert_blog_acle.py` (single article) +
    `batch_blog_acle.py` (batch orchestrator).
  - Batch progress tracking: `/Aliens/.Alien/{Developer_Username}/State/Blog_ACLE/batch_progress.json`
  - Blog_ACLE (singular) = original Aliens Blog articles (.md source)
  - Blogs_ACLE (plural) = third-party scraped blog articles (.html source)
  - These are SEPARATE workflows for separate repos (Blog vs Blogs).
  - No letter sub-folders — Blog repo is flat: {Language}/ directly contains .md files.
  - Branding: "Aliens Blog" (not "Aliens Wiki" or "Aliens Blogs").
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. HIEN/My_Article → HIEN_My_Article).
  - Same directory structure preserved in output: source path structure Blog_ACLE me mirror hoti hai.
SpeedMode:
  Enabled: true
  Description: >
    Planning aur Documentation child workflows bypass hote hain for speed.
    Sirf Code (conversion to cinematic HTML + audio) execute hota hai.
---

# Blog_ACLE

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

# [02] Strict Scope Lock (MANDATORY)
Is workflow ka scope sirf orchestration hai:
- Manifest + Targets validation enforce karna
- Target resolution (CSV / comma-list / directory-path / language-shorthand → article paths list)
- Description parsing for range filter ("Description : N to M")
- Child workflows ko deterministic order me call karna
- Output paths sirf YAML header ke hisaab se resolve karna

Forbidden:
- Direct code generate/modify is file me (child workflow karega)
- Extra articles invent karna / unrelated scope add karna
- Description me jo nahi bola, woh add karna
- Source Blog repo (`/Aliens/Blog/`) me koi bhi modify/delete

# [03] Manifest Gate (Mandatory)
1) Read manifest: "/Aliens/manifest.json"
2) Approval policy:
   - Approval=manual => approval-gated steps par proceed se pehle approval required
   - Approval=auto   => proceed without approval
3) Permissions (minimum):
   - Planning: allowed (default)
   - Code generation (article conversion): allowed (writes to Blog_ACLE, not prod code)

# [04] Target Resolution (Deterministic)
## 4.1 — CSV Mode
If Targets ends with `.csv`:
- Resolve: `/Aliens/Project/{Developer_Username}/{Targets}`
- CSV schema: same as Wiki_ACLE.
- Per-run one row (CSV single-row rule).

## 4.2 — Comma-Separated Mode
If Targets contains `,`:
- Split by comma, trim each.
- Each = relative article path from `/Aliens/Blog/` without `.md`
- Resolve: `/Aliens/Blog/{path}.md` per item.

## 4.3 — Directory Path Mode
If Targets starts with `/` or `\` or contains `:\`:
- Interpret as absolute directory path.
- Scan for `.md` files (skip LICENSE/README/INDEX/index).
- Apply range/letter-range from Description if given.

## 4.4 — Language Shorthand Mode
If Targets is single word, no slash, no comma, no `.csv`:
- Check if `/Aliens/Blog/{Targets}/` directory exists.
- If yes: scan for `.md` files recursively.
- Apply range/letter-range from Description if given.

## 4.5 — Single Article Mode
If Targets contains `/` but no comma:
- Resolve as single article path: `/Aliens/Blog/{Targets}.md`

# [05] For Each Article:
Call child workflows in deterministic order:
1. `Blog_ACLE-Planning(ArticlePath, Description)` — [BYPASSED in SpeedMode]
2. `Blog_ACLE-Code(ArticlePath, Description)` — execute conversion
3. `Blog_ACLE-Documentation(ArticlePath, Description)` — [BYPASSED in SpeedMode]
