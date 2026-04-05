---
WorkflowId: Blogs_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Blogs
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blogs_ACLE
Purpose: >
  Singular Planning workflow: given a single Blog article path, ek complete conversion plan banao
  jo describe kare ke article ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Blogs_ACLE-Planning", "Healthline/EN/What_Is_Diabetes", "Convert blog article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blogs/ without .html extension.
    Comma list is invalid (singular workflow).
    Example: "Healthline/EN/What_Is_Diabetes" implies source file "/Aliens/Blogs/Healthline/EN/What_Is_Diabetes.html".
    Path separators: both / and \ accepted, internally normalized to /.
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Blogs_ACLE/"
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
  - Source is READ-ONLY: `/Aliens/Blogs/{ArticlePath}.html`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Blogs_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual article structure (sections, word count, complexity) from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - Source is HTML (not .md like Wiki/Wikipedia). Parser must handle HTML content.
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. Healthline/EN/What_Is_Diabetes → Healthline_EN_What_Is_Diabetes).
---

# Blogs_ACLE-Planning

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
- Source Blogs repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "Healthline/EN/What_Is_Diabetes")
- Relative to `/Aliens/Blogs/` without `.html` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

# [05] Planning Steps
1) Read source article: `/Aliens/Blogs/{ArticlePath}.html`
   - Source is HTML (Aliens Blog format). Parse HTML content for structure analysis.
2) Discover structure:
   - Count sections (h2/h3 headings in article body)
   - Count total text words (strip HTML tags first)
   - Identify images, tables, code blocks, blockquotes
   - List key topics / sub-headings
3) Check conversion status:
   - Does ACLE HTML already exist at `/Aliens/Blogs_ACLE/{ArticlePath}.html` ?
   - Does audio directory exist at `/Aliens/Blogs_ACLE/{ArticlePath_Parent}/audio_{ArticleName}/` ?
   - If already converted and valid (>5KB HTML + 3+ mp3s): note as "already converted"
4) Estimate slide count (7-12 based on section count + content density)
5) Identify risks:
   - Very large article (>50KB HTML) → may produce 12+ slides (truncation risk)
   - Missing sections / very short article → may produce <7 slides
   - Translated content (HIEN/HI) → narration language must match

# [06] Output
Save planning document to:
- `/Aliens/.Alien/{Developer_Username}/Planning/Blogs_ACLE/{ArticleSlug}.plan.md`
- ArticleSlug = ArticlePath with `/` replaced by `_`

Plan file format:
```markdown
# Blogs_ACLE Planning — {ArticleName}

## Source
- Path: /Aliens/Blogs/{ArticlePath}.html
- Size: {bytes} bytes
- Words: {count}
- Sections: {count}
- Language: {EN/HIEN/HI}

## Structure
{list of h2/h3 headings from article}

## Conversion Status
- Already converted: {yes/no}
- HTML exists: {yes/no} ({size} bytes)
- Audio exists: {yes/no} ({count} mp3 files)

## Risks
{identified risks}

## Execution Plan
1. Parse source HTML article content
2. Extract sections + text for slides
3. Generate cinematic HTML ({N} slides)
4. Generate narration scripts ({N} narrations)
5. Generate TTS audio ({N} mp3 files)
6. Verify output files
```
