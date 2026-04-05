---
WorkflowId: Blog_Article
Type: Workflow_Plural
Domain: Blog
Category: Original Blog Article Creation (Multilingual)
Language: Markdown
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: true
Purpose: >
  Plural orchestrator workflow: original Aliens Blog articles likhna — agent (AlienCyborg) khud article likhega.
  3 languages me sequentially: EN (English) → HIEN (Hinglish Roman) → HI (Hindi Devanagari).
  Yeh workflow khud content nahi likhta — sirf coordination + target resolution + safety/approval enforce karta hai,
  aur child workflows ko deterministic order me call karta hai.
  Yeh Blogs_New (third-party blog scraping) se alag hai — yahan 100% original content creation hota hai.
Invocation:
  Signature:
    - Workflow("Blog_Article", "Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "Description")
    - Workflow("Blog_Article", "Articles.csv", "Description")
    - Workflow("Blog_Article", "Article_One, Article_Two", "Description")
  TargetsMeaning: >
    3 input formats supported:
    (1) Single article slug — single article create mode.
        Example: "Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "AI_Vs_Human_Intelligence"
    (2) CSV filename — batch mode with per-row tracking. CSV path:
        /Aliens/Project/{CyborgID}/{FILENAME}.csv
    (3) Comma-separated slugs — multiple articles, ek ek karke (same pipeline each).
        Example: "Article_One, Article_Two"
    Slug format: PascalCase_With_Underscores (spaces → underscores, meaningful short name).
  DescriptionMeaning: >
    Human language topic context + requirements. REQUIRED — empty/missing not allowed.
    Description defines WHAT the article is about, audience, tone, depth, specific points to cover.
    Must NOT expand scope beyond blog article writing.
    Optional range suffix supported (CSV mode):
    (a) Numeric range: "Description : N to M" — rows sort karke #N se #M filter.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Blog/"
    Pattern: "{Article_Slug}.plan.md"
  Code:
    EN_Path: "/Aliens/Blog/EN/{Article_Slug}.md"
    HIEN_Path: "/Aliens/Blog/HIEN/{Article_Slug}.md"
    HI_Path: "/Aliens/Blog/HI/{Article_Slug}.md"
    Pattern: "{Article_Slug}.md (per language)"
  Documentation:
    Path: "/Aliens/Docs/Blog/"
    Pattern: "{Article_Slug}.doc.md"
  RunRecord:
    Path: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    Pattern: "Blog_Article.Run.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Calls:
  - Blog_Article-Planning
  - Blog_Article-Code
  - Blog_Article-Documentation
Safety:
  NoDelete: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: false
Notes:
  - Yeh original content creation workflow hai — third-party scraping nahi (wo Blogs_New hai).
  - Agent (AlienCyborg) khud articles likhta hai based on topic knowledge + Description context.
  - Internet NOT required — agent apne training knowledge se likhta hai.
  - No Python script needed — agent directly .md files likhta hai.
  - Output repo: /Aliens/Blog/ (Git repo; language sub-folders: EN/, HIEN/, HI/).
  - Article slug consistent across all 3 languages (same filename, different content language).
  - Language execution order STRICT: EN → HIEN → HI (always; no skipping, no reordering).
  - EN finished hona MANDATORY before HIEN start; HIEN finished before HI start.
  - HIEN = Hinglish Roman (romanized Hindi-English mix; Hindi/Devanagari script NOT allowed).
  - HI = Hindi Devanagari script. English words allowed jahan Hindi equivalent nahi.
  - Blog style: conversational, engaging, opinionated, beginner-friendly yet deep.
  - NOT Wikipedia style — yeh blog hai, formal encyclopedia nahi.
  - Article ka Title article ke andar H1 se aata hai — slug sirf filename ke liye hai.
SpeedMode:
  Enabled: false
  Description: >
    SpeedMode disabled. Planning + Code + Documentation teeno mandatory hain.
    Blog articles quality-first hain — rushing nahi, sochke likhna hai.
---

# Blog_Article

## [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

## [01] Type Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Plural.md"

---

## [02] Strict Scope Lock (MANDATORY)
This workflow ONLY creates original blog articles for `/Aliens/Blog/`.
- Kisi aur repo me write nahi hogi (Blog repo only + Planning/Docs permitted paths).
- Kisi external API ya website se content nahi aayega (no scraping, no download).
- Agent apne knowledge se likh raha hai — 100% original content.
- Blog repo structure:
  - `/Aliens/Blog/EN/` — English articles
  - `/Aliens/Blog/HIEN/` — Hinglish Roman articles
  - `/Aliens/Blog/HI/` — Hindi Devanagari articles

---

## [03] Workflow Ka Meaning (Hinglish)
`Blog_Article` ek orchestrator workflow hai.
Iska kaam:
- Targets ko validate karna (article slug ya CSV)
- Decide karna ke run single-article hai ya CSV/planner mode
- Per-run sirf 1 article process karna (all 3 languages)
- Child workflows ko deterministic order me call karna: Planning → Code → Documentation
- RunRecord outputs contract follow karna

---

## [04] Supported Run Modes (Strict)

### Mode A: Single Article
Example:
`Workflow('Blog_Article', 'Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura', 'Description...')`

Rules:
- Targets = exactly one article slug (no commas, no .csv extension).
- Slug format: PascalCase_With_Underscores.

Execution:
1. Call `Blog_Article-Planning` with Targets + Description
2. Call `Blog_Article-Code` with Targets + Description
3. Call `Blog_Article-Documentation` with Targets + Description

### Mode B: CSV / Planner
Example:
`Workflow('Blog_Article', 'Articles.csv', 'Description...')`

Rules:
- CSV file MUST be resolved ONLY from:
  - `/Aliens/Project/{Developer_Username}/Articles.csv`
- Per-run sirf 1 runnable row process hogi (CSV single-row rule).

### Mode C: Comma-Separated
Example:
`Workflow('Blog_Article', 'Article_One, Article_Two', 'Description...')`

Rules:
- Split by comma, trim each.
- Iterate one by one: each goes through full pipeline (Planning → Code → Documentation).
- Order = left to right as given.

---

## [05] CSV Schema (SSOT for Blog_Article)
`Articles.csv` MUST be planner-style CSV (case-insensitive headers).

### Required columns
- `ID`
- `Name` (article title — human readable display name)
- `UTag` (article slug — used for filename: `{UTag}.md`)
- `Workflow` (MUST be `Blog_Article`)
- `Description` (row prompt payload; REQUIRED — topic context + requirements)
- `Status` (`0` pending, `1` success, `2` failed)
- `Assign` (MUST equal `{Developer_Username}`)

### Optional (recommended)
- `DependsOn` (ID references)
- `Priority`
- `created`, `modified`
- `Planning`, `Planning_Save` (phase markers)
- `Code`, `Code_Save` (phase markers)
- `Docs`, `Doc_Save` (phase markers)

If schema mismatch / required column missing:
- Fail fast with `ACC_ERR_CSV_SCHEMA_INVALID`.

---

## [06] CSV Row Picking Rules (Deterministic)
Runnable row conditions:
- `Status == 0`
- `Assign == {Developer_Username}`
- `DependsOn` empty OR all referenced rows have `Status == 1`

Pick strategy:
- File order me first runnable row pick karo.
- Random selection forbidden.

If no runnable row:
- Return `noop` or `blocked` (non-destructive).

---

## [07] CSV Dispatch Rules
For picked row:
- Validate:
  - `Workflow` == `Blog_Article`
  - `Name` non-empty
  - `UTag` non-empty
  - `Description` non-empty
  - Missing row description => `ACC_ERR_CSV_DESCRIPTION_MISSING` (row mutate mat karo).

Then execute same run as:
- `Targets = Row.UTag` (slug)
- `EffectiveDescription = "Title: " + Row.Name + "\nUTag: " + Row.UTag + "\n\n" + OuterDescription + "\n\n---\n\n" + Row.Description`

Child calls:
1. `Blog_Article-Planning(Targets, EffectiveDescription)`
2. `Blog_Article-Code(Targets, EffectiveDescription)`
3. `Blog_Article-Documentation(Targets, EffectiveDescription)`

Writeback semantics (strict):
- Planning success => `Planning=1`, `Status` stays `0`
- Code success (all 3 languages done) => `Code=1`, `Status` stays `0`
- Documentation success => `Docs=1` and `Status=1`
- Any stage fail => mark that stage `2` and set `Status=2`

---

## [08] Language Execution Order (NON-NEGOTIABLE)
1. **EN** (English) — primary content creation (original article)
2. **HIEN** (Hinglish Roman) — translated + adapted from EN
3. **HI** (Hindi Devanagari) — translated + adapted from EN

Rules:
- EN MUST be fully written AND saved to disk before HIEN starts.
- HIEN MUST be fully written AND saved to disk before HI starts.
- Each language version is a complete standalone article (not just headers/stubs).
- HIEN = romanized Hindi-English mix; Hindi/Devanagari script NOT allowed in HIEN.
- HI = Hindi Devanagari script; English technical terms allowed where no Hindi equivalent exists.
- All 3 articles share same slug (filename) — content language differs.

---

## [09] Output Contract (Blog Article)
Article output paths (deterministic):
- EN: `/Aliens/Blog/EN/{Article_Slug}.md`
- HIEN: `/Aliens/Blog/HIEN/{Article_Slug}.md`
- HI: `/Aliens/Blog/HI/{Article_Slug}.md`

`{Article_Slug}` resolution:
- CSV mode: `{Article_Slug}` = `UTag` column value.
- Non-CSV mode: `{Article_Slug}` = `Targets` (already in slug format).

Planning output:
- `/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md`

Documentation output:
- `/Aliens/Docs/Blog/{Article_Slug}.doc.md`

---

## [10] Content Quality Rules (Non-Negotiable)
- Blog style: conversational, engaging, opinionated, personal voice.
- NOT Wikipedia formal encyclopedia style.
- Beginner-friendly but technically deep.
- Each article section MUST have substance (no empty/placeholder sections).
- No fact invention — agar unsure ho to explicitly "not confirmed" mark karo.
- Enterprise-grade completeness (no skeleton/MVP articles).
- Minimum article length: meaningful deep coverage (not 200-word stubs).
- Headings (H2/H3) for clear structure.
- Code examples where relevant (blog is tech-focused).
- Author voice consistent across all sections.

---

## [11] Done Definition
Article workflow complete tabhi:
- Planning file exists at correct path and non-empty
- All 3 language files exist and non-empty:
  - `/Aliens/Blog/EN/{Slug}.md`
  - `/Aliens/Blog/HIEN/{Slug}.md`
  - `/Aliens/Blog/HI/{Slug}.md`
- Documentation file exists at correct path and non-empty
- CSV row status updated to `1` (if CSV mode)
