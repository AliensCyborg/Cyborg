---
WorkflowId: Role_HIEN-Documentation
WorkflowName: Aliens Role SOP Writer (HIEN)
Type: Workflow_Singular-Documentation
ParentWorkflowId: Role_HIEN
Domain: Role
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-27

Inputs:
  - Targets: "RoleName (single only)"
  - Description: "REQUIRED; role context + constraints + special focus areas"

Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Role/HIEN/{RoleSlug}.plan.md"

Produces:
  - RoleSOP: "/Aliens/Role/{RoleSlug}.md (preferred) OR /Aliens/Docs/Role/HIEN/{RoleSlug}.md (fallback)"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"

Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"

NonNegotiables:
  - NoDelete: true
  - DescriptionRequired: true
  - OneRoleOnly: true
  - Language: "Hinglish (Roman). Hindi script not allowed."
  - SOPStyle: true
  - BeginnerFriendly: true
  - UltraDetailed: true
  - WebOSAligned: true
  - MustUseHeadings: true
  - MustUseTables: true
  - MustUseMermaid: true
  - NoFactInvention: true
  - NoNetworkCalls: true
  - NoCopyFromExisting: true
  - ProductionReady: true
---

# Role_HIEN-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`

> Note: Hum yahan `Workflow_Singular-Documentation.md` intentionally include nahi kar rahe.
> Reason: us common file me DocsRoot locked sirf WebOS/WebApps/WebBrand/WebSDK hai.
> Role SOP documents ka output `/Aliens/Role/` ya `/Aliens/Docs/Role/HIEN/` hai,
> is liye yeh workflow apna doc-output contract khud define karta hai.

---

## [01] Purpose (Hinglish)
Is workflow ka kaam: ek single role ke liye **ultra detailed SOP (Standard Operating Procedure) document** generate karna.
Output document ka level: **production-ready**, enterprise-grade, beginner-friendly, Hinglish Roman.
Yeh document Aliens Company SOP me directly add hone ke liye ready hota hai.

---

## [02] Inputs Rules (Strict)

### Targets
- Single role name only.
- Multiple targets (comma) => `ACC_ERR_ARGS_INVALID`.

### Title vs Slug (important)
- `RoleTitle` MUST come from `Targets` (underscores to spaces for display).
- `RoleSlug` MUST be derived from `Targets` **unless** CSV mode provided an override:
  - If `Description` contains `UTag: <value>` (case-insensitive key), then use `<value>` as `RoleSlug`.

### Description
- Required.
- Description ko constraints treat karo.

---

## [03] Planning Dependency (Mandatory)
- Planning file read karo:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Role/HIEN/{RoleSlug}.plan.md`

If missing:
- Fail fast with `ACC_ERR_PLANNING_GENERATION_FAILED`.

Planning doc se extract karo:
- Section outline
- Tables plan (names, columns, purposes)
- Mermaid diagrams plan
- Role classification
- V2.0.0 section list
- Skills / tools / technologies plan
- Training roadmap phases
- Glossary terms

---

## [04] Output Path Policy (Deterministic + Safe)
Preferred output:
- `/Aliens/Role/{RoleSlug}.md`

Fallback output (jab manifest AllowedRoots me `/Aliens/Role` allowed nahi ho):
- `/Aliens/Docs/Role/HIEN/{RoleSlug}.md`

Hard rule:
- Output path MUST be within manifest `Permissions.AllowedRoots`.
- Agar dono roots allowed nahi => fail with `ACC_ERR_IO_WRITE_FAILED`.

Duplicate check:
- Agar target file already exists => fail with `ACC_ERR_IO_FILE_EXISTS` (unless `overwrite=true` in Description).

---

## [05] SOP Document Format (Mandatory — Full Specification)

### 5.1 File Header (required — top of document)
```markdown
# Aliens Company SOP - {RoleTitle} (Ultra Detailed, Beginner-Friendly, Hinglish, WebOS / Aliens Ecosystem Aligned)

Version: 2.0.0  
Document Type: SOP / Role Guide / Training + Execution Manual  
Department: {Department}  
Audience: {PrimaryAudience}  
Language: Hinglish (easy, practical, beginner-friendly)  
Use Case: Aliens Company SOP me add karne ke liye ready reference document  
Aliens Ecosystem Alignment: WebOS + WebApp + WebSDK + AliensStyle + AliensGrade + AliensDocs + AI/Human dual workflow compatible
```

### 5.2 Table of Contents (required)
- Numbered list of ALL sections (base + V2.0.0 additions)
- Base sections numbered 1-35/36
- V2.0.0 additions numbered sequentially after base sections
- Must include a `### V2.0.0 Additions` sub-header

### 5.3 Base Sections (Mandatory — adapt per role category)

**Section 1: Role ka simple introduction**
- 3-5 paragraphs ka beginner-friendly introduction
- Why this role matters in general
- Why this role matters in Aliens Company / WebOS context specifically
- Simple language me, no jargon without explanation

**Section 2: {RoleTitle} kya hota hai (Beginner Friendly Definition)**
- Simple 2-line definition
- What this role "builds" / "produces" / "ensures"
- What this role does NOT do (clear scope boundaries)

**Section 3: Role differentiation (vs similar roles)**
- Comparison table (| Role | Scope | Focus | Typical ownership |)
- Clear differentiation from adjacent roles
- Example: Software Engineer vs Developer vs Architect

**Section 4: Business impact (role level results)**
- Business impact areas table (| Area | Impact | Business result |)
- Minimum 5-7 rows
- Concrete example at the end

**Section 5: Org/branch structure me position**
- Typical reporting structure (bullet hierarchy)
- Cross-functional collaboration list
- Aliens Company specific collaboration notes

**Section 6: Role scope types / variants**
- Different flavors/scopes of this role
- Table or bullet list
- Example: for PM = "Platform PM / Growth PM / Internal Tools PM"

**Section 7: Core responsibilities (daily/weekly/monthly/quarterly/annual)**
- Detailed breakdown by cadence
- Each cadence as sub-section
- Role-specific, NOT generic

**Section 8: Required skills**
- Technical skills (detailed list with explanations)
- Soft skills (with role-specific context)
- Domain-specific skills
- Skills comparison table: Beginner vs Intermediate vs Senior

**Section 9: Languages**
- Programming languages (if applicable)
- Query languages
- Communication languages
- Tool-specific languages (markup, scripting, etc.)

**Section 10: Technologies / tools / frameworks**
- Detailed list categorized by: Core, Supporting, Ecosystem-specific
- Each tool with 1-line description of usage
- Table format recommended

**Section 11: Role lifecycle / process flow**
- Step-by-step typical workflow (beginner-friendly)
- How a typical request/task flows through this role
- From input to output

**Section 12: SOP workflows**
- Role-specific process workflows (at least 3)
- Each workflow: Trigger → Steps → Output → Quality gate
- Examples: Feature workflow, Bug fix workflow, Review process, Release process, etc.

**Section 13: Flowcharts (Mermaid)**
- Minimum 2 Mermaid diagrams
- At least 1 workflow flowchart
- At least 1 organizational/structure diagram
- Use ```mermaid code blocks

**Section 14: Deliverables checklist**
- Table or checklist of what this role produces
- Frequency (per task / weekly / monthly)
- Quality standard per deliverable

**Section 15: Quality standards**
- Role-specific quality dimensions
- What "good" looks like for this role
- AliensGrade alignment where applicable

**Section 16-23: Domain-specific standards**
- Sections 16-23 cover: code standards, system design, database, API, testing, security, performance, logging/monitoring
- Depth varies by role category (engineer gets full depth, PM gets awareness level)
- Each section MUST exist but depth is role-appropriate

**Section 24: Collaboration rules**
- Detailed cross-functional collaboration
- Per-team collaboration model
- Cadence and communication norms

**Section 25: Documentation standards**
- `/Aliens/Docs` rule
- What documentation this role is responsible for
- Documentation quality bar

**Section 26: Estimation / planning basics**
- How this role participates in estimation
- Planning frameworks relevant to this role

**Section 27: KPIs / performance metrics**
- Table of KPIs (| KPI | Definition | Target | Assessment frequency |)
- Minimum 5-8 KPIs
- Role-specific, not generic

**Section 28: Interview / hiring evaluation matrix**
- Table (| Area | What to assess | Evaluation criteria |)
- Role-specific interview questions approach
- Scoring framework

**Section 29: Training roadmap (0 to 180 days)**
- Phase-wise breakdown:
  - Day 0-30: Onboarding + basics
  - Day 30-60: Independent execution starts
  - Day 60-90: Ownership + deeper skills
  - Day 90-180: Full productivity + growth
- Milestones per phase
- WebOS / Aliens ecosystem training items included

**Section 30: Common mistakes and prevention**
- Table or list of min 8-10 common mistakes
- For each: mistake, impact, prevention

**Section 31: Escalation matrix**
- When to escalate, to whom, how
- Table format (| Scenario | Escalate to | Method | SLA |)

**Section 32: Career growth path**
- Linear progression steps
- Alternative paths
- Skills needed per level transition

**Section 33: Salary ranges**
- Global reference ranges (USD/INR)
- Table by level
- Disclaimer: indicative only

**Section 34: Tools cheat sheet**
- Quick reference table (| Tool | Category | Use case | Link/Note |)
- Min 8-10 tools

**Section 35: Glossary**
- Min 20 terms
- Each term: simple 1-line Hindi-English explanation
- Alphabetical order

**Section 36: Final SOP checklist**
- Comprehensive checklist of everything this role should follow

### 5.4 V2.0.0 Additions (WebOS / Aliens Ecosystem Alignment)
These sections come AFTER the base sections. Numbering continues from base section count.

Mandatory V2.0.0 sections (include at least 5, select role-appropriate ones):

1. **WebOS / Aliens ecosystem alignment overview (role scope)**
   - How this role fits into WebOS architecture
   - What WebOS layers this role touches

2. **WebApp vs WebSDK vs WebOS: role ka lens**
   - How this role interacts with each layer
   - Scope boundaries per layer

3. **Aliens naming + planner governance**
   - Role's responsibility in naming conventions
   - Planner awareness requirements

4. **AliensStyle / AliensGrade governance**
   - Quality expectations from this role
   - Audit mindset

5. **AI Agent + Human same workflow model**
   - How this role interacts with AI agents (AlienCyborg etc.)
   - Dual workflow understanding

Additional V2.0.0 sections — select based on role category:
- WebOS shared engine governance (technical roles)
- Multi-branch scaling playbook (leadership roles)
- Executive risk register (C-level roles)
- Release management alignment (engineering roles)
- Incident communication SOP (management roles)
- Onboarding upgrade track (all roles, WebOS focus)
- Specialization paths (engineering roles)

---

## [06] Content Quality Rules (Non-Negotiable)
- Hinglish Roman only; Hindi script bilkul nahi.
- SOP style: structured, clear, actionable, beginner-friendly.
- Ultra detailed: minimum 500 lines (most roles should produce 700-1800+ lines).
- Skeleton/placeholder/dummy sections **absolutely forbidden**.
- Every section must have substantive content (not just headers with 2 lines).
- Tables minimum 5 per document (use planning doc's table plan).
- Mermaid flowcharts minimum 2 per document.
- Every major term explain karo in simple Hinglish.
- Facts invent mat karo. Unconfirmed cheezen `Not confirmed` label karo.
- No network calls: external links verify nahi, to guess mat karo.
- No copying content from existing Role docs — only format reference allowed.
- Original content required: phrasing original honi chahiye, even if similar role docs exist.
- Production-ready: document directly Aliens Company SOP me add ho sake.

---

## [07] Existing Role Documents — Format Reference Only
- Agent MAY read 1-2 existing Role docs from `/Aliens/Role/` to study format patterns.
- Purpose: understand heading style, table formats, section depth, Hinglish tone.
- MUST NOT copy-paste any content from existing docs.
- MUST NOT modify any existing Role docs.

---

## [08] Mermaid Diagram Rules (Strict)
- Use valid Mermaid JS syntax inside ```mermaid code blocks.
- Diagram types allowed: flowchart TD/LR, sequenceDiagram, graph TD/LR.
- No abbreviations in node labels — use human-readable text.
- Test mentally: would this render correctly?
- Minimum 5 nodes per flowchart.
- Do NOT use generic/placeholder diagrams. Each diagram must be role-specific.

---

## [09] Table Rules (Strict)
- Standard Markdown table syntax.
- Minimum 5 tables per document.
- Each table must have:
  - Header row
  - Alignment row (|---|)
  - Minimum 3 data rows (most tables should have 5-10+)
- Tables should match Planning doc's table plan.
- Tables that MUST exist in every Role SOP:
  1. Business Impact table
  2. Skills / competency matrix
  3. KPIs / metrics table
  4. Salary ranges table
  5. Tools cheat sheet table

---

## [10] Writing Style Guide (Hinglish SOP Tone)
- Tone: professional but beginner-friendly
- Sentence structure: short, clear, direct
- Hindi words use: naturally integrate Hindi/Urdu words where they make the text more accessible
- Technical terms: always explain in parentheses or next line
- Example pattern:
  - "Software Engineer ka main kaam **code likhna** + **system design** + **testing** hai."
  - "Agar koi bug aaye to pehle reproduce karo, phir root cause find karo, phir fix karo."
- Avoid: overly formal language, academic English, complex sentence structures
- Use: bold for emphasis, bullet points for lists, tables for structured data

---

## [11] Section Depth Guide (by Role Category)

| Role Category | Technical Sections Depth | Strategy Sections Depth | Management Sections Depth | Min Lines |
|---|---|---|---|---|
| C-Level / Executive | Awareness (light) | Deep + comprehensive | Deep (governance) | 700+ |
| VP / Director | Moderate | Deep | Deep | 600+ |
| Management (PM, PjM) | Moderate | Moderate-Deep | Deep | 700+ |
| Senior IC (Sr Eng, Staff) | Deep + comprehensive | Moderate | Light-Moderate | 800+ |
| IC (Engineer, Designer) | Deep | Light | Light | 700+ |
| Entry Level (Intern, Junior) | Guided/Learning | None | None | 500+ |

---

## [12] Acceptance Criteria (Documentation)
Documentation success tabhi:
- Output file exists at correct path and non-empty
- File header matches format from [05.1]
- Table of Contents present with all sections numbered
- All base sections (35-36) present with substantive content
- V2.0.0 sections present (minimum 5)
- Minimum 5 tables present with proper formatting
- Minimum 2 Mermaid diagrams present and syntactically plausible
- Glossary has minimum 20 terms
- Minimum line count met (per role category from [11])
- No Hindi script detected (Roman only)
- No placeholder/skeleton/TODO sections
- Content is original (not copy-pasted from existing Role docs)
- Planning doc was read and followed
