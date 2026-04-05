---
WorkflowId: Skill_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Skill
Category: Skill Topic Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Skill_ACLE
Purpose: >
  Singular Planning workflow: given a single skill lesson path, ek complete conversion plan banao
  jo describe kare ke lesson ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Skill_ACLE-Planning", "HIEN/Python/01_Python_Introduction", "Convert skill lesson to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single lesson path relative to /Aliens/Skills/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Python/01_Python_Introduction" implies source file "/Aliens/Skills/HIEN/Python/01_Python_Introduction.md".
    Path separators: both / and \ accepted, internally normalized to /.
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved lesson path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Skill_ACLE/"
    Pattern: "{LessonSlug}.plan.md"
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
  - Source is READ-ONLY: `/Aliens/Skills/{LessonPath}.md`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Skill_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual lesson structure (sections, word count, complexity) from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - LessonSlug = LessonPath with `/` replaced by `_` (e.g. HIEN/Python/01_Python_Introduction → HIEN_Python_01_Python_Introduction).
---

# Skill_ACLE-Planning

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
- Extra lessons add karna forbidden.
- Source Skills repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE lesson path (example: "HIEN/Python/01_Python_Introduction")
- Relative to `/Aliens/Skills/` without `.md` extension
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
1) LessonPath trim karo
2) Path separators normalize: `\` → `/`
3) Source file resolve karo: `/Aliens/Skills/{LessonPath}.md`
4) Agar file missing => fail with `WF_TARGET_MISSING`
5) Agar file exist => proceed to discovery

# [07] Discovery Phase (Evidence Pack — Mandatory)
Planning generate karne se pehle yeh discovery steps complete karo:

## [07.1] Lesson Structure Discovery
Read source file: `/Aliens/Skills/{LessonPath}.md`
Extract:
- Total headings (##, ###)
- Total sections (## level)
- Total subsections (### level)
- Tables count
- Code blocks count
- Word count (approximate)
- Mermaid/diagram blocks count
- Estimated complexity: Simple (<500 words, <5 sections) / Medium / Complex (>2000 words, >10 sections)

## [07.2] Lesson Context Discovery
Extract from the lesson path:
- Language: first path component (e.g., "HIEN")
- Skill name: second path component (e.g., "Python")
- Lesson number: from filename prefix (e.g., 01 from "01_Python_Introduction")
- Lesson name: from filename (e.g., "Python_Introduction")

Check skill folder for sibling context:
- Total lessons in same skill folder (count *.md excluding index files)
- Lesson position (N of M)

## [07.3] Conversion Status Check
Check if already converted:
- HTML: `/Aliens/Skill_ACLE/{LessonPath}.html` exists? Size > 5KB?
- Audio: `/Aliens/Skill_ACLE/{dir}/audio_{LessonFilename}/` exists? 3+ mp3 files?
- Narrations: `/Aliens/Skill_ACLE/{LessonPath}_narrations.json` exists?

Status:
- "Already Converted" (all 3 present and valid)
- "Partially Converted" (some present)
- "Pending" (none present)

## [07.4] Dependency Check
- Python 3.10+ available?
- edge-tts installed?
- Converter script exists: `/Aliens/Cyborg/AlienCyborg/Code/convert_skill.py`?
- Shared assets exist: `/Aliens/Skill_ACLE/assets/css/aliens-cinematic.css`?
- Internet connectivity (for TTS)?

# [08] Plan Generation (Output)
Generate plan file at: `/Aliens/.Alien/{Developer_Username}/Planning/Skill_ACLE/{LessonSlug}.plan.md`

Plan file structure:
```markdown
# ACLE Skill Conversion Plan: {LessonPath}

## Lesson Info
- Source: /Aliens/Skills/{LessonPath}.md
- Language: {Language}
- Skill: {SkillName}
- Lesson: #{LessonNumber} of {TotalInSkill}
- Word count: {WordCount}
- Sections: {SectionCount}
- Complexity: {Simple/Medium/Complex}

## Conversion Status
- HTML: {Exists/Missing} ({SizeKB}KB)
- Audio: {Exists/Missing} ({Mp3Count} files)
- Narrations: {Exists/Missing}
- Overall: {Already Converted / Partially Converted / Pending}

## Dependencies
- Python: {OK/Missing}
- edge-tts: {OK/Missing}
- Converter: {OK/Missing}
- Assets: {OK/Missing}
- Internet: {OK/Unknown}

## Estimated Slides
- Title: 1
- Overview: 1
- Content: {N} (based on section count)
- DeepDive: {N} (based on complexity)
- Quiz: 1
- End: 1
- Total estimated: {N}

## Execution Plan
1. Run convert_skill.py --lesson "{LessonPath}"
2. Verify HTML output > 5KB
3. Verify audio directory has 3+ mp3 files
4. Verify narrations JSON exists
```

# [09] Safety Rules
1. Source READ-ONLY: `/Aliens/Skills/{LessonPath}.md` — no modify/delete
2. Planning output WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Skill_ACLE/`
3. NoDelete = true: never delete any existing plan
4. Converter scripts READ-ONLY: do not modify

# [10] Error Handling
ErrorCodes:
- `WF_TARGET_MISSING` — lesson file not found
- `ACC_ERR_DESCRIPTION_REQUIRED` — Description empty/missing
- `WF_TARGETS_INVALID` — multiple targets or invalid format
- `WF_PERMISSION_DENIED` — manifest permission check failed
- `WF_PLANNING_WRITE_FAILED` — could not write plan file
