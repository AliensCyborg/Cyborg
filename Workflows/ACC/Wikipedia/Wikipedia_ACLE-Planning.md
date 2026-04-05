---
WorkflowId: Wikipedia_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Wikipedia
Category: Wikipedia Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Wikipedia_ACLE
Purpose: >
  Singular Planning workflow: given a single Wikipedia article path, ek complete conversion plan banao
  jo describe kare ke article ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Wikipedia_ACLE-Planning", "HIEN/A0001/Albert_Einstein", "Convert Wikipedia article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Wikipedia/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/A0001/Albert_Einstein" implies source file "/Aliens/Wikipedia/HIEN/A0001/Albert_Einstein.md".
    Path separators: both / and \ accepted, internally normalized to /.
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Wikipedia_ACLE/"
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
  - Source is READ-ONLY: `/Aliens/Wikipedia/{ArticlePath}.md`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Wikipedia_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual article structure (sections, word count, complexity) from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. HIEN/A0001/Albert_Einstein → HIEN_A0001_Albert_Einstein).
---

# Wikipedia_ACLE-Planning

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
- Source Wikipedia repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/A0001/Albert_Einstein")
- Relative to `/Aliens/Wikipedia/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

# [05] Planning Steps
1) Read source article: `/Aliens/Wikipedia/{ArticlePath}.md`
2) Discover structure:
   - Count sections (H2/H3 headings)
   - Count total words
   - Identify tables, code blocks, diagrams
   - List key topics / sub-headings
3) Check conversion status:
   - Does HTML already exist at `/Aliens/Wikipedia_ACLE/{ArticlePath}.html` ?
   - Does audio directory exist at `/Aliens/Wikipedia_ACLE/{ArticlePath_Parent}/audio_{ArticleName}/` ?
   - If already converted and valid (>5KB HTML + 3+ mp3s): note as "already converted"
4) Estimate slide count (7-12 based on section count + content density)
5) Identify risks:
   - Very large article (>50KB) → may produce 12+ slides (truncation risk)
   - Missing sections / very short article → may produce <7 slides
   - Special content (math, code, tables) → extra slide types

# [06] Output
Save planning document to:
- `/Aliens/.Alien/{Developer_Username}/Planning/Wikipedia_ACLE/{ArticleSlug}.plan.md`
- ArticleSlug = ArticlePath with `/` replaced by `_`

Plan file format:
```markdown
# Wikipedia_ACLE Planning — {ArticleName}

## Source
- Path: /Aliens/Wikipedia/{ArticlePath}.md
- Size: {bytes} bytes
- Words: {count}
- Sections: {count}

## Structure
{list of H2/H3 headings}

## Conversion Status
- Already converted: {yes/no}
- HTML exists: {yes/no} ({size} bytes)
- Audio exists: {yes/no} ({count} mp3 files)

## Estimated Output
- Slides: {7-12}
- Audio files: {estimated}
- Special content: {tables/code/diagrams}

## Risks
{any identified risks}

## Execution Plan
1. Run convert_wikipedia.py with article path
2. Verify HTML output > 5KB
3. Verify audio directory with 3+ mp3 files
4. Post-process: verify branding "Aliens Wikipedia"
```
