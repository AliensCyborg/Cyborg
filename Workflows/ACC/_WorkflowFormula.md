---
ACC_GLOBAL:
  ID: ACC_Workflow_Formula
  Name: "ACC Workflow Formula (Global Ruleset)"
  Version: 26.00.00
  UpdatedAt: 2025-12-24
  Timezone: Asia/Kolkata
  Owner: "Aliens Company (Core Team)"
  Agent: AlienCyborg
  AppliesTo:
    - "All ACC Workflows (.md)"
  CanonicalRepoPath: "Workflows/ACC/_WorkflowFormula.md"
  CanonicalDocsPath: "/Aliens/Docs/ACC/ACC.WorkflowFormula.md"
  RelatedCommonFiles:
    - "Workflows/ACC/_Common/Workflow_Plural.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  CoreRefs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
  DeveloperManifest:
    Required: true
    Path: "/Aliens/manifest.json"
    ResolveDeveloperPolicy:
      - "ENV: ALIEN_DEV (preferred)"
      - "Repo mapping (git user/email => folder)"
      - "Fail with actionable message if unresolved"
  GlobalGuardrails:
    PlanningRequired: true
    PlanningBeforeEveryFileWrite: true
    PlanningMustBeEnterpriseGrade: true
    CodeMustFollowApprovedPlan: true
    CodeMustBeFeatureComplete_NoSkeleton: true
    DocsMandatoryAfterCode: true
    NoDeletePolicy: true
    ModifyOnlyPolicy: true
    MissingTargetIsErrorOnUpdate: true
    NoWeakeningOverrides: true
    DeterminismRequired: true
    SecretsNeverInRepo: true
    AutoVsManualApprovalFromManifest: true
    DefaultTheme: "_Basic"
    DefaultVersionFallback: "PHP.26.00.00"
  OutputDirs:
    PlanningDir: "/Aliens/.Alien/{Developer_Username}/Planning"
    RunsDir: "/Aliens/.Alien/{Developer_Username}/Runs"
    MemoryDir: "/Aliens/.Alien/{Developer_Username}/Memory"
    ApprovalsDir: "/Aliens/.Alien/{Developer_Username}/Approvals"
    DocsDir: "/Aliens/Docs"
  Determinism:
    RunStamp: "YYYYMMDD-HHMMSS-<8char hash of (WorkflowID|Targets|Description)>"
    SameInputsSamePlan: true
    SamePlanSameCode: true
---

# ACC Workflow Formula (Global Ruleset)

Ye file **global rules** ke liye hai. Isko har ACC workflow me include karna mandatory hai, taaki:
- rules repeat na ho,
- safety/approval/output/audit sab workflows me same rahein,
- future me global changes ek hi jagah se apply ho jaayen.

> Important: Individual workflow ka unique logic yahan nahi aayega.  
> Yahan sirf woh rules hain jo **har workflow** ko chahiye.

---

## 1) Mandatory: Har Workflow me Include Kaise Karein

Har workflow file ke YAML header me ye keys mandatory hain:

```yaml
WorkflowId: "WebSDK_Module_API"
Type: "Workflow_Plural"  # ya Workflow_Singular-Planning / Workflow_Singular-Code / Workflow_Singular-Documentation
ACC:
  Includes:
    - "Workflows/ACC/_WorkflowFormula.md"
  RequiresCommon:
    - "Workflows/ACC/_Common/Workflow_Plural.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
```

**Rule:** Includes/Refs/Common me se koi file missing/unreadable ho ⇒ workflow **STOP** (no planning, no code).

---

## 2) Workflow Types + Common Files Mapping (Mandatory)

### A) Plural Workflow
- Type: `Workflow_Plural`
- Must require:
  - `Workflows/ACC/_Common/Workflow_Plural.md`

Plural ka job:
- Targets list parse karke loop chalana
- Child workflows call karna (Planning → Code → Documentation)
- RunRecord + Summary produce karna

### B) Singular Workflows
All singular types must require:
- `Workflows/ACC/_Common/Workflow_Singular.md`

And additionally (as per type):

- `Workflow_Singular-Planning`
  - `Workflows/ACC/_Common/Workflow_Singular-Planning.md`
- `Workflow_Singular-Code`
  - `Workflows/ACC/_Common/Workflow_Singular-Code.md`
- `Workflow_Singular-Documentation`
  - `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## 3) Inputs Contract (Global)

ACC Workflow runtime input signature:

`Workflow( WorkflowId, Targets, Description )`

### Targets (Global Parsing Rules)
- Comma `,` ⇒ same workflow **repeat** per target item
- `Parent/Child` ⇒ relationship (actual type workflow id decide karega)
- `WebOS.Cache` ⇒ basename only (extension workflow decide karega)
- Underscore rule:
  - `User` ⇒ Standard module (mostly one table)
  - `User_` / `User_Accounts` ⇒ Cluster module (multiple tables, same prefix)

Detailed grammar: `Workflows/ACC/_Refs/ACC.Targets.md`

---

## 4) Developer Manifest Contract (Global)

Every run MUST load:
- `/Aliens/manifest.json`

Manifest decides:
- `Mode`: `employee | assistant`
- `ApprovalMode`: `auto | manual`
- `Permissions`: prod write, db touch, etc.
- Default version/theme
- Paths roots (`/Aliens/WebOS`, `/Aliens/WebApp`, `/Aliens/WebBrand`, `/Aliens/WebSDK`, `/Aliens/Docs`)

If manifest missing ⇒ STOP with error and fix steps.

---

## 5) Global Execution Formula (All steps required)

### Step 1: Goal extraction + scope lock
- Description se: goal, constraints, safety sensitivity, required quality
- Scope lock: exact domain (WebOS/WebApp/WebBrand/WebSDK), exact targets list

### Step 2: Read global refs + common files
- Formula + Common + Refs read mandatory
- Missing ⇒ STOP

### Step 3: Resolve paths + targets normalization
- `ACC.Paths` rules apply
- Every target normalized to absolute intended output path(s)

### Step 4: Safety + approval decision
- `ACC.Safety` + manifest `ApprovalMode`
- If manual required ⇒ Approval file generate, execution gate

### Step 5: Planning-first enforcement
This is a **hard gate** (non-negotiable):

1) **Per TargetItem / Per File rule**
- Har ek target item ke liye (aur jis file ko touch karna ho), sabse pehle **Planning** generate + save mandatory hai.
- Planning ke baghair koi code write / patch / save allowed nahi.

2) **Enterprise-grade planning requirement**
- Planning must be **feature-complete** and designed for:
  - Multi-App + Multi-Tenant + Multi-Brand compatibility (Context-scoped)
  - High-performance patterns (cache-first, concurrency, idempotency)
  - Security + Safety (deny-by-default, policy gates, secrets never in repo)
  - Auditability + determinism (stable run records, stable outputs)
  - Extensibility hooks (future engines / overrides / providers)

3) **Planning output must be saved**
- Planning output MUST be saved in developer planning dir **and** linked/copied in RunRecord item.
- If planning save fails, workflow must STOP (no code, no docs).

4) **Approval enforcement**
- If manifest requires manual approvals for the planned actions, create approval request and STOP before code writes.

### Step 6: Code generation (Modify-only)
- NoDelete always
- Update workflows (_Update) me: missing target ⇒ error
- Existing business logic ko blindly overwrite forbidden (workflow-specific rules decide)

**Hard rule (quality + completeness):**
- Code must follow the approved plan and must be **enterprise-grade**.
- Stubs / placeholders / “skeleton-only” outputs forbidden.
- “File size large” ka matlab: feature coverage large. Agar planning comprehensive hai to file naturally comprehensive hogi.
- If output looks incomplete (missing required sections/actions/contracts), treat as **quality gate failure** and STOP.

### Step 7: Documentation generation
- `/Aliens/Docs/...` me mandatory save
- Planning + code changes mapping + usage examples mandatory

**Traceability rule:**
- Documentation must map back to:
  - planning artifact(s)
  - final code artifact(s)
  - feature list implemented

### Step 8: Quality gates
- “No stub” (placeholders) forbidden
- Safety + path lock + allowlist checks
- Fail ⇒ safe stop (no destructive writes)

**New mandatory quality gate:**
- Planning→Code→Docs order violation => FAIL.
- Missing planning artifact for any touched file => FAIL.
- Empty or near-empty output files (0 bytes / trivial skeleton) => FAIL.

### Step 9: RunRecord + Audit output
- Runs folder me per run + per item logs
- Human readable `.md` + optional machine `.json`

### Step 10: Deterministic summary
- RunStamp + exact output paths + counts + warnings + next actions

---

## 6) Output Contract (Global)

Every run must create:
- Run folder:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/`
- Items:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`
- Planning (required):
  - `/Aliens/.Alien/{Developer}/Planning/...`
- Docs (required):
  - `/Aliens/Docs/...`

Exact schema: `Workflows/ACC/_Refs/ACC.Output.md`

---

## 7) Safety Rules (Global)
Non-negotiable:
- **NoDeletePolicy = true**
- **ModifyOnlyPolicy = true**
- Secrets never in repo (use `.Alien/{Developer}/Secrets` and gitignore)
- DB touch allowed only with explicit manifest permission + (manual approval if configured)

Details: `Workflows/ACC/_Refs/ACC.Safety.md`

---

## 8) Overrides Policy (Strict)
Workflows can add extra strict rules locally, but:
- Global rules **weaken** karna forbidden.

Allowed:
```yaml
ACC:
  Override:
    AddStrictRules:
      - "Extra security gate"
      - "Extra tests"
```

Forbidden:
- NoDeletePolicy ko false karna
- ApprovalMode bypass
- Update workflow me missing-target ko ignore karna

---

## 9) Change Control (Global File)
Because this file affects 10,000+ workflows:
- Changes must be backward compatible (where possible)
- Add changelog + migration note if behavior changes

### Changelog
- 26.00.00 (2025-12-24): Updated to new ACC architecture ( _Common + _Refs + per-workflow 4-file model )
- 26.00.01 (2025-12-27): Enforced enterprise-grade per-file Planning→Code→Docs gating + no-skeleton outputs.
- 1.1.0 (2025-12-20): Legacy global ruleset (deprecated)
