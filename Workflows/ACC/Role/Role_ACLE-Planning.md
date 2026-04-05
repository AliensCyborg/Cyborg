---
WorkflowId: Role_ACLE-Planning
Type: Workflow_Singular-Planning
Domain: Role
Category: Role SOP Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Role_ACLE
Purpose: >
  Singular Planning workflow: given a single Role SOP document path, ek complete conversion plan banao
  jo describe kare ke document ka structure kya hai (sections, word count, complexity), kya already
  converted hai, kya risks/dependencies hain, aur execution steps kya hain.
  Plan file save karo specified planning path par.
Invocation:
  Signature: Workflow("Role_ACLE-Planning", "Software_Engineer", "Convert role SOP to cinematic video")
  TargetsMeaning: >
    Single role filename stem from /Aliens/Role/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "Software_Engineer" implies source file "/Aliens/Role/Software_Engineer.md".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Planning:
    Path: "/Aliens/.Alien/{Developer_Username}/Planning/Role_ACLE/"
    Pattern: "{RoleSlug}.plan.md"
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
  - Source is READ-ONLY: `/Aliens/Role/{RoleSlug}.md`
  - Output planning is WRITE: `/Aliens/.Alien/{Developer_Username}/Planning/Role_ACLE/`
  - This workflow does NOT run conversion. It only creates a plan document.
  - Plan must discover actual document structure from disk.
  - Plan must check existing conversion status (already converted vs pending).
  - RoleSlug = filename stem (e.g., Software_Engineer, CEO, Product_Manager).
---

# Role_ACLE-Planning

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
- Sirf specified role ki planning banao.
- Extra roles add karna forbidden.
- Source Role repo modify karna forbidden.
- Converter scripts modify karna forbidden.
- Is workflow me code execute karna forbidden (sirf planning karo).

# [04] Inputs
Targets:
- MUST be exactly ONE role filename stem (example: "Software_Engineer")
- Filename stem from `/Aliens/Role/` without `.md` extension
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Planning output write permission (allowed by default).
- Approval policy: manual => approval before saving planning file.

# [06] Target Validation
1) RoleSlug = Targets trimmed
2) Source file resolve: `/Aliens/Role/{RoleSlug}.md`
3) Agar file missing => fail with `WF_TARGET_MISSING`
4) Agar file exist => proceed to discovery

# [07] Discovery Phase (Evidence Pack — Mandatory)

## [07.1] Document Structure Discovery
Read source: `/Aliens/Role/{RoleSlug}.md`

Collect:
- Document title (first `# heading`)
- Role name (extracted from title)
- Version (from header metadata)
- Department (from header metadata)
- Total word count
- Total line count
- Section count (H2 headings)
- Sub-section count (H3+ headings)
- Tables count
- Mermaid diagrams count
- Code blocks count
- Estimated complexity (Simple/Medium/Complex/Ultra — based on line count: <500=Simple, 500-1000=Medium, 1000-1500=Complex, 1500+=Ultra)

## [07.2] Conversion Status Check
Check output: `/Aliens/Role_ACLE/{RoleSlug}.html`
- Exists? Size? (considered converted if > 5KB)
- Narrations JSON exists?
- Audio dir exists? MP3 count?

## [07.3] Conversion Plan
Based on document structure, estimate:
- Number of slides (approximately: section count * 2-3 + intro + outro)
- Audio files needed
- Estimated conversion time
- Special considerations for large files (1000+ lines)

# [08] Planning Output Path
- `/Aliens/.Alien/{Developer_Username}/Planning/Role_ACLE/{RoleSlug}.plan.md`

# [09] Acceptance Criteria
Planning success tabhi:
- Output file exists and non-empty
- Document structure discovered (word count, line count, section count)
- Conversion status checked
- Conversion plan present with slide estimate
