---
WorkflowId: Wiki_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Wiki
Category: Wiki Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Wiki_ACLE
Purpose: >
  Singular Documentation workflow: given a single wiki article path, uski ACLE conversion ka
  enterprise-grade documentation generate karo — covering article overview, conversion results,
  quality metrics, troubleshooting, aur usage guide. Documentation file
  Docs/Wiki_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Wiki_ACLE-Documentation", "HIEN/A/Algorithm", "Document the ACLE Wiki conversion results")
  TargetsMeaning: >
    Single article path relative to /Aliens/Wiki/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/A/Algorithm" implies conversion results at "/Aliens/Wiki_ACLE/HIEN/A/Algorithm.html".
    Note: Range filtering ("1 to 100") is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints/focus. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent ("... : N to M") but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Wiki_ACLE/"
    Pattern: "{ArticleSlug}.md"
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
  - Source Wiki repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - Documentation language follows ACC SSOT: Hinglish (Roman Hindi).
  - English sirf technical terms ke liye. Devanagari Hindi script allowed nahi.
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. HIEN/A/Algorithm → HIEN_A_Algorithm).
---

# Wiki_ACLE-Documentation

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
- Sirf specified article ki documentation banao
- Extra articles add karna forbidden
- Source Wiki repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden
- Is workflow me code execute karna forbidden (sirf documentation karo)

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
- Documentation output write permission (allowed by default; writes to `/Aliens/Docs/`)
- Approval policy: manual => approval before saving documentation
If permission missing => fail fast (`WF_PERMISSION_DENIED`)

# [06] Discovery Phase (Mandatory — Data from Disk)
Documentation MUST be evidence-based. Agar information disk par nahi milti, to honestly "unknown/not available" likho — invent mat karo.

## [06.1] Source Article Discovery
Read: `/Aliens/Wiki/{ArticlePath}.md`
- Article title
- Word count
- Section count (H2 headings)
- Sub-section count (H3+ headings)
- Infobox present?
- Mermaid diagrams count
- Tables count
- Code blocks count
- Glossary section present?
- See also section present?
- Article complexity (Simple/Medium/Complex)

## [06.2] Conversion Results Discovery
Scan output files:

### HTML Check:
- Path: `/Aliens/Wiki_ACLE/{ArticlePath}.html`
- Exists? Size? (must be > 5KB)
- Slide count (parse HTML for slide markers)

### Narrations JSON Check:
- Path: `/Aliens/Wiki_ACLE/{ArticlePath}_narrations.json`
- Exists? Valid JSON?
- Entry count (should match slide count)

### Audio Check:
- Path: `/Aliens/Wiki_ACLE/{dir}/audio_{ArticleName}/`
- Exists? mp3 count? (must be 3+)
- Total audio size

Categorize:
- **Fully Converted**: HTML > 5KB + audio dir 3+ mp3s + narrations JSON
- **Partially Converted**: some outputs missing or undersized
- **Not Converted**: no output found

## [06.3] Planning Artifact Discovery
Check: `/Aliens/.Alien/{Developer_Username}/Planning/Wiki_ACLE/{ArticleSlug}.plan.md`
- If exists: reference plan data in documentation
- If missing: note "Planning artifact not found"

## [06.4] Batch Progress Discovery
Check: `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/batch_progress.json`
- If article marked as completed: note
- If article in failed list: note failure details
- If not found: note "Progress tracking not available"

# [07] Documentation Content Template (Mandatory Sections)
Following the ACC `Workflow_Singular-Documentation.md` common contract, with ACLE Wiki-specific content:

## [07.1] Language + Audience Contract
- Language: Hinglish (Roman Hindi)
- Devanagari Hindi script: forbidden
- English: sirf technical terms ke liye
- Audience: beginner to intermediate — simple language, practical examples
- Depth: 2000-5000+ words (enterprise-grade, no shortcuts)

## [07.2] Mandatory Document Structure

### Section 1: Title Block
```markdown
---
Document: ACLE Wiki Article Documentation
Article: {ArticlePath}
ArticleTitle: {title from source}
Domain: Wiki (Aliens Cinematic Learning Experience)
Version: 26.03.00
Author: AlienCyborg
Generated: {timestamp}
WorkflowId: Wiki_ACLE-Documentation
---
```

### Section 2: Executive Summary (Beginner-Friendly)
- Article name aur topic
- ACLE conversion ka scope: Wiki MD article → cinematic HTML video article
- Conversion status (converted/partial/pending)
- Quality bar summary: animated SVG, TTS audio, minimal text, video-like experience
- 3-4 lines me overall status summary

### Section 3: Quick Start (Article Viewing Guide)
- Kaise converted article open/play karna hai
- Browser requirements (modern browser, autoplay support)
- Audio controls: play/pause, next/prev slide, narration toggle
- File path: kahan se HTML file open karna hai
- 5-minute setup equivalent

### Section 4: Glossary
- ACLE: Aliens Cinematic Learning Experience
- TTS: Text-to-Speech
- SVG: Scalable Vector Graphics
- Narration: AI-generated Hindi audio commentary per slide
- Slide types: Title, Overview, Content, Diagram, Comparison, Glossary, Quiz, End
- edge-tts: Microsoft Azure Text-to-Speech engine
- Converter: Python script jo Wiki MD → HTML conversion karta hai
- ArticleSlug: article path with `/` replaced by `_`

### Section 5: Source Article Overview
- Article title
- Article path (relative to Wiki root)
- Language folder: {Language}
- Word count
- Complexity classification
- Section list with word counts
- Special elements: infobox, mermaid diagrams, tables, code blocks, glossary

### Section 6: Conversion Results (Detailed)
- Overall status: Fully Converted / Partially Converted / Not Converted
- HTML file:
  - Path, size, slide count
  - Slide type sequence (Title → Overview → Content → ...)
- Narrations JSON:
  - Path, entry count, valid JSON?
- Audio directory:
  - Path, mp3 count, total audio size
- Quality compliance:
  - HTML > 5KB? Yes/No
  - Audio 3+ mp3s? Yes/No
  - Brand colors present? Yes/No
  - SVG illustrations present? Yes/No

### Section 7: Output File Structure
- Directory tree of generated files for this article:
  ```
  /Aliens/Wiki_ACLE/{dir}/
    ├── {ArticleName}.html              (cinematic HTML)
    ├── {ArticleName}_narrations.json   (narration data)
    └── audio_{ArticleName}/            (audio directory)
         ├── slide_01.mp3
         ├── slide_02.mp3
         └── ...
  ```
- Per-output description: what it contains, format, size range
- Shared assets reference: CSS, JS, SVG (pre-existing, not generated)

### Section 8: Quality Metrics
- HTML file size
- Slide count (target: 7-12)
- Audio file count per slide
- Narration word count per slide (richness indicator)
- SVG illustration types used in this article
- Brand color compliance
- Slide type coverage (which types present, which missing)
- Overall quality score: Pass / Partial / Fail

### Section 9: Technical Architecture
- Conversion pipeline: Wiki MD → Parse Sections → Build HTML Slides → Generate Narrations → TTS Audio
- Article section → Slide type mapping logic
- Converter: `convert_article.py` (wiki-specific converter)
- TTS engine: edge-tts + `hi-IN-MadhurNeural` voice
- Slide engine: aliens-cinematic.js (client-side player)
- Styling: aliens-cinematic.css (cinematic slide styling)
- Skip logic: idempotent (already-done = skip)

### Section 10: Integration with Aliens Ecosystem
- Wiki repo relationship (`/Aliens/Wiki/` → source SSOT, READ-ONLY)
- Wiki_ACLE repo relationship (`/Aliens/Wiki_ACLE/` → output)
- Same directory structure preservation principle
- Shared assets architecture (CSS/JS/SVG reuse across articles)
- Multi-PC distribution compatibility
- Progress tracking system (`batch_progress.json` at `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/`)
- Relationship with Course ACLE (shared concepts, separate repos)

### Section 11: Safety + Security
- Source repo READ-ONLY (no modifications ever)
- NoDelete policy (never delete output files)
- No manual `batch_progress.json` edits during conversion (path: `/Aliens/.Alien/{Developer_Username}/State/Wiki_ACLE/`)
- No overwrite of already-converted articles (skip check)
- Internet dependency for TTS (Azure servers)
- No secrets/credentials involved in conversion

### Section 12: Troubleshooting Guide
Common issues table:
- edge-tts timeout/failure → check internet, retry (3-retry built in)
- HTML file too small (< 5KB) → article source may be too short, check source MD
- Audio directory empty → TTS failed all retries, check network
- Slides not rendering → shared assets missing, verify assets/ folder
- Narration sounds robotic → expected behavior for TTS, quality is "natural" level
- Converter crash → check error log, common causes: malformed MD, encoding issues
- Missing SVG illustrations → converter selects from 12 types intelligently
- Mermaid diagram not represented → check converter support for diagram type
- Directory structure mismatch → verify source path matches output path hierarchy

### Section 13: FAQ (Minimum 10 Q/A)
At least 10 questions covering:
- Kaise ek article ko manually re-convert karna hai?
- Skip check kaise disable karna hai?
- Different voice kaise use karna hai?
- Agar internet disconnect ho jaye to kya hoga?
- Kya converted articles offline kaam karengi? (Ha — HTML + audio local hai)
- batch_progress.json corrupt ho jaye to?
- New articles add ho jaye source me to? (Simply run workflow for new articles)
- Quality check kaise karna hai?
- Kya custom SVG illustrations add kar sakte hain?
- Multiple articles ek saath kaise convert karna hai? (CSV mode or directory path mode)

### Section 14: Change Log / Versioning
- Converter version (current)
- Workflow version: 26.03.00
- Known limitations
- Planned improvements (if any from Description)

### Section 15: Extension + Future Hooks
- New slide types addition strategy
- New SVG illustration types strategy
- Voice customization hooks
- Multi-language narration (future — EN, HI articles)
- Language-specific voice selection (future)
- Progress dashboard (future)
- Batch optimization hooks

### Section 16: Appendix
- Source article section list
- Slide mapping detail (which section → which slide)
- Sample narration JSON snippet
- Sample HTML slide structure snippet
- Command reference (converter script flags)

# [08] Save Rules
Primary output:
- Path: `/Aliens/Docs/Wiki_ACLE/{ArticleSlug}.md`
- Format: Markdown
- Naming: ArticleSlug (article path with `/` replaced by `_`), no timestamp prefix

Audit copy (if run system active):
- Path: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/Documentation.md`

# [09] Error Handling
- `WF_TARGET_MISSING` — article source file not found in `/Aliens/Wiki/`
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets passed to singular workflow
- `WF_PATH_RESOLUTION_FAILED` — documentation output path cannot be resolved
- `WF_OUTPUT_ROOT_NOT_FOUND` — `/Aliens/Docs/` directory not found

# [10] Done Criteria
- Documentation file written to output path with all 16 mandatory sections populated
- Discovery data is from actual disk scan (not guessed)
- Conversion results accurately reflect disk state
- Quality metrics are evidence-based
- Article structure in documentation matches actual source article
- No scope creep beyond the single article target
- Documentation is human-readable, clean Hinglish, enterprise-grade
