---
WorkflowId: Skill_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Skill
Category: Skill Topic Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Skill_ACLE
Purpose: >
  Singular Documentation workflow: given a single skill lesson path, uski ACLE conversion ka
  enterprise-grade documentation generate karo — covering lesson overview, conversion results,
  quality metrics, troubleshooting, aur usage guide. Documentation file
  Docs/Skill_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Skill_ACLE-Documentation", "HIEN/Python/01_Python_Introduction", "Document the ACLE Skill conversion results")
  TargetsMeaning: >
    Single lesson path relative to /Aliens/Skills/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Python/01_Python_Introduction" implies conversion results at "/Aliens/Skill_ACLE/HIEN/Python/01_Python_Introduction.html".
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved lesson path.
  DescriptionMeaning: >
    Human language constraints/focus. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Skill_ACLE/"
    Pattern: "{LessonSlug}.md"
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
  - Source Skills repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - Documentation language follows ACC SSOT: Hinglish (Roman Hindi).
  - English sirf technical terms ke liye. Devanagari Hindi script allowed nahi.
  - LessonSlug = LessonPath with `/` replaced by `_` (e.g. HIEN/Python/01_Python_Introduction → HIEN_Python_01_Python_Introduction).
---

# Skill_ACLE-Documentation

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
- Sirf specified lesson ki documentation banao
- Extra lessons add karna forbidden
- Source Skills repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden
- Is workflow me code execute karna forbidden (sirf documentation karo)

# [04] Inputs
Targets:
- MUST be exactly ONE lesson path (example: "HIEN/Python/01_Python_Introduction")
- Relative to `/Aliens/Skills/` without `.md` extension
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

## [06.1] Source Lesson Discovery
Read: `/Aliens/Skills/{LessonPath}.md`
Extract:
- Lesson title (first `#` heading)
- Language (from path)
- Skill name (from path)
- Lesson number (from filename prefix)
- Total headings/sections count
- Tables/code blocks count
- Word count
- Mermaid/diagram blocks count

## [06.2] Conversion Output Discovery
Check `/Aliens/Skill_ACLE/{LessonPath}.html`:
- Exists? File size?
- Count slides (search for `class="slide"` or similar patterns)
- Has brand colors? Has SVG illustrations?

Check `/Aliens/Skill_ACLE/{LessonPath}_narrations.json`:
- Exists? Valid JSON?
- Count entries (slides)
- Total narration character count

Check `/Aliens/Skill_ACLE/{dir}/audio_{LessonFilename}/`:
- Exists? Count mp3 files?
- Total audio size?

## [06.3] Quality Assessment
Based on discovery data:
- HTML status: Pass (>5KB + slides) / Fail
- Audio status: Pass (3+ mp3s) / Fail
- Narration status: Pass (JSON valid) / Fail
- Overall: success / partial / failed / not-converted

## [06.4] Skill Context
- Total lessons in skill folder
- How many already converted
- How many pending
- This lesson's position (N of M)

# [07] Documentation Generation (Output)
Generate doc file at: `/Aliens/Docs/Skill_ACLE/{LessonSlug}.md`

Documentation structure:
```markdown
# ACLE Skill Conversion Report: {LessonPath}

## Summary
| Field | Value |
|-------|-------|
| Source | /Aliens/Skills/{LessonPath}.md |
| Output | /Aliens/Skill_ACLE/{LessonPath}.html |
| Language | {Language} |
| Skill | {SkillName} |
| Lesson | #{Number} of {Total} |
| Status | {success/partial/failed/not-converted} |
| Generated | {timestamp} |

## Source Analysis
- Word count: {N}
- Sections: {N}
- Tables: {N}
- Code blocks: {N}
- Complexity: {Simple/Medium/Complex}

## Conversion Results
### HTML Output
- File: {path}
- Size: {N}KB
- Slides: {N}
- Brand colors: {present/missing}
- SVG illustrations: {present/missing}
- Branding: Aliens School

### Audio Output
- Directory: {path}
- Files: {N} mp3 files
- Total size: {N}KB

### Narrations
- File: {path}
- Entries: {N}
- Total narration chars: {N}

## Quality Gates
- [x/✗] HTML > 5KB
- [x/✗] 7-12 slides
- [x/✗] Audio 3+ mp3 files
- [x/✗] Narrations JSON valid
- [x/✗] Brand colors present
- [x/✗] "Aliens School" branding

## Skill Progress
- Total in skill: {N} lessons
- Converted: {N}
- Pending: {N}
- Conversion rate: {N}%

## Troubleshooting
{If failed: describe errors, common fixes, retry commands}
{If success: "No issues detected."}

## Usage
- View: Open {output_html} in browser
- Audio: Check {audio_dir} for slide mp3s
- Retry: `python convert_skill.py --lesson "{LessonPath}"`
```

# [08] Safety Rules
1. Source READ-ONLY: all Skills and Skill_ACLE files — read only for documentation
2. Documentation output WRITE: `/Aliens/Docs/Skill_ACLE/`
3. NoDelete = true: never delete any existing documentation
4. Evidence-based only: never invent/guess data — use disk discovery

# [09] Error Handling
ErrorCodes:
- `WF_TARGET_MISSING` — lesson source file not found
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `WF_DOC_WRITE_FAILED` — could not write documentation file
