---
WorkflowId: Wiki_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Wiki
Category: Wiki Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Wiki_ACLE
Purpose: >
  Singular Planning workflow: given a single wiki article path, ek complete conversion plan banao
  jo describe kare ke article ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Wiki_ACLE-Planning", "HIEN/A/Algorithm", "Convert wiki article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Wiki/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/A/Algorithm" implies source file "/Aliens/Wiki/HIEN/A/Algorithm.md".
    Path separators: both / and \ accepted, internally normalized to /.
    Note: Range filtering ("1 to 100") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/"
    Pattern: "{ArticleSlug}.plan.md"
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
  - Source is READ-ONLY: `/Aliens/Wiki/{ArticlePath}.md`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual article structure (sections, word count, complexity) from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. HIEN/A/Algorithm → HIEN_A_Algorithm).
---

# Wiki_ACLE-Planning

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
- Extra articles add karna forbidden.
- Source Wiki repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/A/Algorithm")
- Relative to `/Aliens/Wiki/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)
- Extensions forbidden (`.md`, `.html`, `.csv`)

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
1) ArticlePath trim karo
2) Path separators normalize: `\` → `/`
3) Source file resolve karo: `/Aliens/Wiki/{ArticlePath}.md`
4) Agar file missing => fail with `WF_TARGET_MISSING`
5) Agar file exist => proceed to discovery

# [07] Discovery Phase (Evidence Pack — Mandatory)
Planning generate karne se pehle yeh discovery steps complete karo:

## [07.1] Article Structure Discovery
Read source article file:
```
/Aliens/Wiki/{ArticlePath}.md
```

Collect:
- Article title (first `# heading`)
- Total word count
- Section count (H2 headings = major sections)
- Sub-section count (H3+ headings)
- Infobox present? (table after first heading)
- Mermaid diagrams count
- Tables count (beyond infobox)
- Code blocks count
- Glossary section present?
- See also section present?
- Estimated reading time (word count / 200 wpm)
- Article complexity classification:
  - Simple: < 500 words, < 5 sections
  - Medium: 500-2000 words, 5-10 sections
  - Complex: > 2000 words, > 10 sections

Section inventory list:
- Section name, approximate word count, has mermaid?, has table?, has code block?

## [07.2] Conversion Status Discovery
For the article, check corresponding output:
```
/Aliens/Wiki_ACLE/{ArticlePath}.html           (must exist, size > 5KB)
/Aliens/Wiki_ACLE/{ArticlePath}_narrations.json (must exist)
/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/    (must exist, 3+ mp3 files inside)
```

Where `{dir}` = directory part of ArticlePath, `{ArticleName}` = filename part without extension.

Categories:
- **Already Converted**: HTML > 5KB AND audio dir with 3+ mp3s AND narrations JSON exists
- **Partially Converted**: some outputs exist but criteria not fully met
- **Pending**: no HTML or no audio dir

## [07.3] Environment Check
Verify:
- Python availability (3.10+)
- `edge-tts` package installed
- Converter scripts exist:
  - `/Aliens/Cyborg/AlienCyborg/Code/convert_article.py`
  - `/Aliens/Cyborg/AlienCyborg/Code/batch_wiki.py`
- Shared assets exist:
  - `/Aliens/ACLE/assets/css/aliens-cinematic.css`
  - `/Aliens/ACLE/assets/js/aliens-cinematic.js`
  - `/Aliens/ACLE/assets/svg/aliens-logo-defs.svg`
- Internet connectivity (edge-tts requirement)

## [07.4] Risk Assessment
Evaluate:
- Article complexity → estimated slide count (Simple: 7-8, Medium: 8-10, Complex: 10-12)
- Internet dependency risk (TTS failures possible)
- Mermaid diagram handling (need special slide treatment)
- Large table handling (need comparison slide format)
- Estimated conversion time (complexity-based: Simple ~2 min, Medium ~3 min, Complex ~5 min)

# [08] Planning Output Template (Mandatory Sections)
Planning file MUST contain these sections in order:

## Section 1: Overview
- Article name (from title)
- Article path (relative to Wiki root)
- Description (from workflow input)
- Timestamp
- Mode (New conversion)

## Section 2: Inputs
- WorkflowId: Wiki_ACLE-Planning
- Target: {ArticlePath}
- Description: {Description}
- ParentWorkflowId: Wiki_ACLE (if called from Plural)

## Section 3: Resolved Context
- Developer username (from manifest)
- Approval mode (auto/manual)
- Source path: `/Aliens/Wiki/{ArticlePath}.md`
- Output HTML path: `/Aliens/Wiki_ACLE/{ArticlePath}.html`
- Output audio path: `/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/`
- Planning output path: `/Aliens/.Alien/{Dev}/Planning/Wiki_ACLE/{ArticleSlug}.plan.md`

## Section 4: Article Structure Analysis (Discovery Results)
- Article title
- Word count
- Complexity classification (Simple/Medium/Complex)
- Section inventory table:
  ```
  | # | Section Name | Words | Mermaid | Table | Code |
  |---|---|---|---|---|---|
  | 1 | Lead section | 120 | No | No | No |
  | 2 | Core concept | 350 | Yes | No | No |
  | ... | ... | ... | ... | ... | ... |
  ```
- Total sections: N
- Total sub-sections: N
- Infobox: Yes/No
- Mermaid diagrams: N
- Tables: N
- Code blocks: N
- Glossary: Yes/No
- See also: Yes/No

## Section 5: Conversion Status
- Status: Already Converted / Partially Converted / Pending
- If already converted: HTML size, audio file count, narrations JSON present
- If partially converted: what exists, what is missing
- If pending: fresh conversion needed

## Section 6: Slide Mapping Plan
Based on article structure, plan the slide sequence:
```
| Slide # | Type | Source Section | Est Duration |
|---------|------|---------------|-------------|
| 1 | Title | Article title + lead | 10s |
| 2 | Overview | Infobox | 18s |
| 3 | Content | Section "Core concept" | 22s |
| 4 | Diagram | Mermaid from section X | 20s |
| 5 | Content | Section "Implementation" | 22s |
| 6 | Comparison | Table from section Y | 18s |
| 7 | DeepDive | Section "Advanced" | 25s |
| 8 | Glossary | Glossary section | 16s |
| 9 | Quiz | Auto-generated | 15s |
| 10 | End | Summary + See also | 10s |
```
- Estimated total slides: N (target: 7-12)
- Estimated total duration: N seconds

## Section 7: Dependencies
- Python 3.10+ (status: verified/missing)
- edge-tts package (status: verified/missing)
- Converter scripts (status: verified/missing)
- Shared assets (status: verified/missing)
- Internet connectivity (status: verified/unknown)

## Section 8: Execution Strategy
- Recommended mode: Single article (`python convert_article.py --article {ArticlePath}`)
- Estimated duration: X minutes (based on complexity)
- Skip logic: already-converted articles will be skipped automatically
- Retry strategy: edge-tts 3-retry built into converter
- SVG illustration selection: based on article topic/category

## Section 9: Step-by-Step Plan
Concrete measurable steps for the Code workflow to follow:

1. Verify environment (Python + edge-tts + scripts + assets)
2. Verify source article file exists and is non-empty
3. Verify output directory structure exists (or will be auto-created)
4. Execute conversion: `python convert_article.py --article {ArticlePath}`
5. After completion: verify output (HTML > 5KB + narrations JSON + audio 3+ mp3s)
6. Report: conversion success/failure with metrics
7. Handle failures: log error, suggest retry

## Section 10: Safety Rules
- Source READ-ONLY (no modifications to `/Aliens/Wiki/`)
- NoDelete = true
- No overwrite: skip already-converted articles
- No manual batch_progress.json edits during batch run (path: `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/batch_progress.json`)
- Internet required throughout conversion

## Section 11: Output Paths (Exact)
- Planning: `/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/{ArticleSlug}.plan.md`
- Code output HTML: `/Aliens/Wiki_ACLE/{ArticlePath}.html`
- Code output narrations: `/Aliens/Wiki_ACLE/{ArticlePath}_narrations.json`
- Code output audio: `/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/`
- Documentation: `/Aliens/Docs/Wiki_ACLE/{ArticleSlug}.md`
- Run record: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/`

## Section 12: Validation Checklist
After Code workflow completes, verify:
- [ ] HTML file exists and > 5KB
- [ ] HTML contains 7-12 cinematic animated slides
- [ ] Narrations JSON exists with per-slide narration data
- [ ] Audio directory exists with 3+ mp3 files
- [ ] No source files modified
- [ ] Article structure faithfully represented in slides
- [ ] Brand colors compliant

## Section 13: Estimated Metrics
- Article word count: {N}
- Estimated slides: {N}
- Estimated duration: {N} seconds total
- Estimated conversion time: {N} minutes
- Expected output size: ~{N} KB (HTML + JSON + audio)

# [09] Save Rules
Primary output:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/{ArticleSlug}.plan.md`
- Format: Markdown
- Naming: ArticleSlug (article path with `/` replaced by `_`), no timestamp prefix (latest plan overwrites)

Audit copy (if run system active):
- Path: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Planning.md`

# [10] Error Handling
- `WF_TARGET_MISSING` — article file not found in `/Aliens/Wiki/`
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets passed to singular workflow
- `WF_PATH_RESOLUTION_FAILED` — planning output path cannot be resolved
- `WF_CONVERTER_NOT_FOUND` — converter scripts missing (captured in plan but plan still saves)
- `WF_ENVIRONMENT_INCOMPLETE` — Python/edge-tts/assets missing (captured in plan but plan still saves)

Note: Environment check failures are recorded in the plan as warnings/blockers. Plan file still gets saved so Code workflow can read it and act accordingly.

# [11] Done Criteria
- Planning file written to output path with all 13 mandatory sections populated
- Discovery data is from actual disk scan (not guessed)
- Article structure analysis matches reality
- Slide mapping plan is deterministic and reasonable (7-12 slides)
- Conversion status accurately reflects disk state
- No scope creep beyond the single article target
- Plan is deterministic: same inputs = same plan output (modulo timestamps)
