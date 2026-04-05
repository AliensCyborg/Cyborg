---
WorkflowId: Workflow_Singular-Planning
Type: CommonRuleFile
Scope: ACC.Workflow.Singular.Planning
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Purpose: >
  Common rules for every Singular-Planning workflow. This file is NOT an executable workflow by itself.
  It standardizes how planning is generated, what evidence is required, where it is saved,
  and how safety/approvals/audit must be enforced while producing planning outputs.
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
  AlienCyborg: "Agent + VSCode Copilot Custom Agent + Azure AI"
Requires:
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - PlanningOutput: "/Aliens/.Alien/{Developer_Username}/Planning/"
  - RunRecordRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  - DefaultTheme: "_Basic"
  - DefaultVersionFallback: "PHP.26.00.00"
---

# Workflow_Singular-Planning.md
**Description (Hinglish):**  
Yeh file *workflow nahi hai*. Yeh **Singular-Planning** type workflows ke liye **common planning engine rules** define karti hai.  
Har workflow jo planning generate karta hai, usko:
- `Workflow_Singular.md` (base common rules)
- aur **yeh file** `Workflow_Singular-Planning.md`
`require_once` karna mandatory hai.

> Singular-Planning workflow ka kaam:  
> Given `Targets` + `Description` ke hisaab se **planning** generate karna, aur specified location par save karna, with full audit trail.

---

## [00] Enterprise Objective Prompt (MANDATORY)
Jab bhi planning ban rahi ho, yeh Objective **hamesha** apply hoga (non-negotiable).

**OBJECTIVE:**
Given file/target ke liye COMPLETE, PRODUCTION-READY, ENTERPRISE-GRADE plan banao jo WebOS ecosystem ke saath DEEPLY INTEGRATED ho.

Plan ka design is level ka ho:
- Multi-App
- Multi-Tenant
- High-Performance
- Secure by default
- Future-proof
- AI-auditable (10/10 score target)

Ecosystem assumption (hard):
- Target ko isolated/standalone mat samjho.
- Jahan possible ho, WebOS ke existing concepts/engines/context/routing/config/logging/hooks/lifecycle ko plan me map karo.

Style & Standards (mandatory):
- AliensStyle fully follow ho
- AliensGrade compliance ho
- CleanCode principles apply hon

Quality bar (planning must cover):
- Architecture, Security, Performance, Maintainability, Scalability
- Test-readiness, Observability-readiness, Backward compatibility

Usability goals:
- Minimal configuration, sensible defaults
- Extension hooks + future compatibility notes (planned)

---

## [01] Hard Rule: Common vs Unique (Very Important)
**Is common file me sirf universal rules hon.**

Allowed here:
- Planning generation contract (format, required sections, evidence pack)
- Discovery contract (kya kya dhoondhna mandatory hai, kaise record karna hai)
- Output + naming + storage rules
- Safety + approval + audit rules for planning work
- Premium UI definition reference (planning me UI requirements ka mapping)

NOT allowed here:
- Kisi specific workflow ki unique discovery logic
  - Example: “WebSDK_Module_API me kaunsi 4 example files use hongi”
- Kisi specific module/app/brand ka fixed path
- Domain-specific assumptions (wo individual workflow me rahenge)

---

## [02] Required Includes (Mandatory)
Every Singular-Planning workflow MUST include:

1) `Workflows/ACC/_Common/Workflow_Singular.md`
2) `Workflows/ACC/_Common/Workflow_Singular-Planning.md` (this file)
3) Reference knowledge (read-only):
   - `Workflows/ACC/_Refs/WebOS_Engines.md`
   - `Workflows/ACC/_Refs/WebOS_System.md`
   - `Workflows/ACC/_Refs/WebSDK.md`
4) Runtime governance:
  - `/Aliens/manifest.json`
   - ACC refs: Targets/Paths/Output/Safety/PremiumUI

---

## [03] Inputs Contract (Singular Planning)
Important: **common file arguments accept nahi karti**.  
Lekin jo singular-planning workflow is file ko include karega, us workflow ka call payload standard hona zaroori hai.

**Expected payload keys (minimum):**
- `WorkflowId`
- `Targets` (single target item recommended)
- `Description`
- `ParentWorkflowId` (agar Plural se call hua)
- `RunContext` (DeveloperUsername, Mode, ApprovalMode, Permissions, Version, Theme)
- `RunId`, `RunItemId`

**Normalization rule:**
- Comma splitting caller (Plural) karega; Singular comma ko invalid treat karega unless YAML me allow ho.

---

## [04] Evidence Pack (Mandatory)
Planning generate karne se pehle workflow ko **Evidence Pack** build karna mandatory hai:

- Target Classification (domain + type)  
- Path Resolution (manifest paths + latest version fallback)  
- Related Artifacts Lookup (class/table/existing files if update)  
- Constraints + Policies (NoDelete, Update rules, approvals, Premium UI)  
- Risk Notes (ProdWrite/DbTouch => higher risk)  

**Note:** Evidence Pack ka “how-to find” individual workflow me hoga, but *fields must exist*.

---

## [05] Planning Output Template (Mandatory Sections)
Planning file MUST contain (order fixed):

1) Overview  
2) Inputs  
3) Resolved Context  
4) Target Classification  
5) Dependencies  
6) Discovery Findings (Evidence Pack Summary)  
7) Step-by-Step Plan (7–12 measurable steps)  
8) Safety + Approvals  
9) Outputs (exact paths)  
10) Validation Checklist  
11) Rollback / Non-Destructive Rule  

### [05.B] Enterprise Grade Planning (New Mandatory Additions)
Because ACC builds **WebOS = High-Performance Operating System**, planning MUST also include:

12) Multi-App + Multi-Tenant + Multi-Brand Strategy
- Context boundaries (Tenant/App/Brand/User/Unit)
- Isolation rules (no cross-tenant leakage)
- Override layers (App/Brand/SDK/Core) impact analysis

13) Feature Coverage Matrix (No Miss)
- Core features list (must implement)
- Optional/phase features list (explicitly marked)
- Edge cases + failure modes list

14) Performance Plan (High-Performance by design)
- Cache strategy (keys, TTL, invalidation, stampede protection)
- Concurrency + locking strategy (idempotency, dedupe, retry)
- IO strategy (DB/FS/network), budgets, and safe fallbacks

15) Security + Policy Plan
- Deny-by-default gates
- Input validation rules
- Secrets policy (never in repo) + approval gates

16) Observability + Audit Plan
- Logs/metrics/events required
- Deterministic run record + audit fields

17) Output Completeness Criteria
- “No skeleton” acceptance criteria
- What must exist in the final code/docs to call it complete

---

## [06] Save Locations (Mandatory)
Primary:
- `/Aliens/.Alien/{Developer_Username}/Planning/`

Recommended name:
- `{YYYY-MM-DD}__{WorkflowId}__{TargetSafe}__Plan.md`

Audit copy:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/`

TargetSafe:
- `/` => `__`, spaces => `_`, allow only `[A-Za-z0-9_]`

---

## [07] Approval Rules (Planning)
If plan indicates ProdWrite/DbTouch:
- Manual approvals: create `/Approvals/{ApprovalId}.md` and mark plan “Pending Approval”
- Auto approvals: log “Auto-Approved by manifest” in RunRecord

Update workflows:
- Missing target => plan must output actionable error and stop (no auto-create).

---

## [08] Quality Rules
Planning MUST be:
- deterministic
- measurable steps
- ecosystem naming compliant
- Premium UI mapping included when UI is involved (`ACC.UI.Premium.md`).

---

## [09] Minimal Error Model
- ERR_DEV_NOT_RESOLVED
- ERR_MANIFEST_MISSING
- ERR_TARGET_INVALID
- ERR_TARGET_MISSING_FOR_UPDATE
- ERR_PATH_RESOLUTION_FAILED
- ERR_EVIDENCE_INSUFFICIENT

Write errors to:
- `/Runs/{RunId}/Items/{RunItemId}/errors.json`
- `/Runs/{RunId}/Items/{RunItemId}/errors.md`

---

## [10] Change Control
This file affects all Singular-Planning workflows.

### Changelog
- 26.00.00 (2025-12-24): Initial Common Rules for Singular-Planning Workflows
