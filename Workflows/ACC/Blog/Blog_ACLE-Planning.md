---
WorkflowId: Blog_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Blog
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blog_ACLE
Purpose: >
  Singular Planning workflow: given a single original Blog article path, ek complete conversion plan banao
  jo describe kare ke article ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Blog_ACLE-Planning", "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "Convert blog article to cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blog/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura" implies source file
    "/Aliens/Blog/HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura.md".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Blog_ACLE/"
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
  - Source is READ-ONLY: `/Aliens/Blog/{ArticlePath}.md`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Blog_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual article structure from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - ArticleSlug = ArticlePath with `/` replaced by `_`.
---

# Blog_ACLE-Planning

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
- Sirf specified article ki planning banao.
- Extra articles add karna forbidden.
- Source Blog repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura")
- Relative to `/Aliens/Blog/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Planning output write permission (allowed by default).
- Approval policy: manual => approval before saving planning file.

# [06] Target Validation
1) ArticlePath trim karo
2) Path separators normalize: `\` → `/`
3) Source file resolve: `/Aliens/Blog/{ArticlePath}.md`
4) Agar file missing => fail with `WF_TARGET_MISSING`
5) Agar file exist => proceed to discovery

# [07] Discovery Phase (Evidence Pack — Mandatory)

## [07.1] Article Structure Discovery
Read source: `/Aliens/Blog/{ArticlePath}.md`

Collect:
- Article title (first `# heading`)
- Total word count
- Section count (H2 headings)
- Sub-section count (H3+ headings)
- Tables count
- Code blocks count
- Estimated complexity (Simple/Medium/Complex)
- Language (from path: EN/HIEN/HI)

## [07.2] Conversion Status Check
Check output: `/Aliens/Blog_ACLE/{ArticlePath}.html`
- Exists? Size? (considered converted if > 5KB)
- Narrations JSON exists?
- Audio dir exists? MP3 count?

## [07.3] Conversion Plan
Based on article structure, estimate:
- Number of slides
- Audio files needed
- Estimated conversion time

# [08] Planning Output Path
- `/Aliens/.Alien/{Developer_Username}/Planning/Blog_ACLE/{ArticleSlug}.plan.md`
- ArticleSlug = ArticlePath with `/` replaced by `_`

# [09] Acceptance Criteria
Planning success tabhi:
- Output file exists and non-empty
- Article structure discovered (word count, section count)
- Conversion status checked
- Conversion plan present with slide estimate
