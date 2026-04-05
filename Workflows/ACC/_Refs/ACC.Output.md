---
RefId: ACC.Output
Type: ACC.Ref
Scope: ACC.Workflow.OutputContract
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Purpose: >
  Defines the mandatory output format for every ACC Workflow run (Plural/Singular),
  including RunRecords, per-item artifacts, planning/docs save locations, and audit logs.
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
  WebSDK: ">=PHP.26.00.00"
  WebApps: ">=PHP.26.00.00"
  WebBrand: ">=PHP.26.00.00"
Requires:
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "/Aliens/manifest.json"
NonNegotiables:
  - NoDelete: true
  - MustCreateRunRecord: true
  - MustWriteHumanSummary: true
  - MustWriteMachineSummary: true
  - MustRecordApprovals: true
---

# ACC.Output.md
**Description (Hinglish):**  
Yeh file batati hai ke **har ACC Workflow run** ke baad **kya output generate hona chahiye**, **kahan save hona chahiye**, aur **audit/logging ka format kya hoga**.  
Is output contract ko follow karna mandatory hai, warna debugging, approvals, employee-mode tracking, aur “who did what” trace possible nahi rahega.

---

## [01] Output Philosophy (Core Goals)
1) **Repeatable**: same workflow same input => same predictable output structure  
2) **Auditable**: har change ka trace mile (developer, workflow, target, time, approvals)  
3) **Human + Machine**: `.md` summary + `.json` structured logs dono mandatory  
4) **NoDelete**: koi file delete nahi; sirf create/update + record  
5) **Multi-target ready**: comma-separated targets par per-item folder alag  
6) **Works in VS Code + Backend**: same folders, same schema

---

## [02] Base Paths (Source of Truth)
**Developer-scoped runtime folder (mandatory):**
- `/Aliens/.Alien/{Developer_Username}/`

**Run records base folder (mandatory):**
- `/Aliens/.Alien/{Developer_Username}/Runs/`

**Approvals folder (mandatory):**
- `/Aliens/.Alien/{Developer_Username}/Approvals/`

**Planning output folder (mandatory):**
- `/Aliens/.Alien/{Developer_Username}/Planning/`

**Docs output root (mandatory):**
- `/Aliens/Docs/`

Exact path resolve rules: `Workflows/ACC/_Refs/ACC.Paths.md`

---

## [03] RunId + RunItemId (Naming Rules)
### RunId (unique per workflow run)
Recommended format (stable + readable):
- `YYYYMMDD-HHMMSS-<WorkflowId>-<ShortRand>`

Example:
- `20251224-094812-WebSDK_Module_API-9F3A`

### RunItemId (unique per target item in a run)
Recommended deterministic-ish format:
- `<Index>-<TargetSlug>-<ShortHash>`

Example:
- `01-User-UserNew-6C1B`
- `02-Account-AccountUpdate-0A9D`

**TargetSlug** rules:
- `/` -> `-`
- spaces trim
- commas already split in Targets parser

---

## [04] Mandatory RunRecord Folder Structure
For every workflow run, you MUST create:

`/Aliens/.Alien/{Developer}/Runs/{RunId}/`

Inside it, minimum required files:

1) `Run.md`  *(human readable full log)*  
2) `Run.json` *(machine readable run summary)*  
3) `Inputs.json` *(raw inputs + resolved context)*  
4) `Outputs.json` *(created/updated files list + artifacts list)*  
5) `Errors.json` *(if any; else empty array allowed)*  
6) `Warnings.json` *(if any; else empty array allowed)*  
7) `Approvals.json` *(approval decisions, even if auto)*  
8) `Timeline.json` *(steps + timestamps)*

And per target item folder:

`/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`

Inside each item folder, minimum required files:

1) `Item.md`  
2) `Item.json`  
3) `Payload.json` *(what was passed into child workflow)*  
4) `Artifacts/` *(any generated intermediate outputs)*

---

## [05] Minimal JSON Schemas (Must Follow)
### Run.json (minimum required)
```json
{
  "RunId": "20251224-094812-WebSDK_Module_API-9F3A",
  "WorkflowId": "WebSDK_Module_API",
  "WorkflowType": "Plural|Singular",
  "Developer": "Anil",
  "Mode": "employee|assistant",
  "ApprovalMode": "auto|manual",
  "StartedAt": "2025-12-24T09:48:12+05:30",
  "FinishedAt": "2025-12-24T09:49:30+05:30",
  "TargetsRaw": "User/UserNew, Account/AccountUpdate",
  "TargetsCount": 2,
  "Result": "success|partial|failed",
  "Counts": {
    "Succeeded": 1,
    "Failed": 0,
    "Partial": 1
  },
  "CreatedFiles": [],
  "UpdatedFiles": [],
  "DocsCreated": [],
  "PlanningCreated": [],
  "Warnings": [],
  "Errors": [],
  "Approvals": []
}
```

### Item.json (minimum required)
```json
{
  "RunId": "20251224-094812-WebSDK_Module_API-9F3A",
  "RunItemId": "01-User-UserNew-6C1B",
  "WorkflowId": "WebSDK_Module_API-Code",
  "ParentWorkflowId": "WebSDK_Module_API",
  "Target": "User/UserNew",
  "ResolvedDomain": "WebSDK",
  "ResolvedVersion": "PHP.26.00.00",
  "Result": "success|partial|failed",
  "CreatedFiles": [],
  "UpdatedFiles": [],
  "Artifacts": [],
  "Warnings": [],
  "Errors": []
}
```

### Inputs.json (minimum required)
Inputs.json ka goal: raw invocation + resolved context ko preserve karna (audit/debug).

Minimum required shape (extra fields allowed):
```json
{
  "RunId": "20251224-094812-WebSDK_Module_API-9F3A",
  "WorkflowId": "WebSDK_Module_API",
  "TargetsRaw": "User/UserNew, Account/AccountUpdate",
  "DescriptionRaw": "...",
  "InvocationMode": "normal|csv",
  "Csv": {
    "File": "{optional: /Aliens/Project/{Developer_Username}/...csv}",
    "PickedRow": {
      "Id": "{optional}",
      "Name": "{optional}",
      "Description": "{required when InvocationMode=csv}"
    },
    "PromptComposed": "{optional; sanitized snapshot of what was actually used as prompt}"
  }
}
```

> Note: JSON me extra fields add karna allowed hai, lekin minimum fields missing nahi hone chahiye.

---

## [06] Human Logs (Markdown) Requirements
### Run.md (must include)
- RunId, WorkflowId, Developer, time window
- Targets list (ordered)
- For each target: status + created/updated files
- Approval decision (auto/manual) + reason
- Any warnings/errors with error codes
- Final summary + next actions

### Item.md (must include)
- Target
- Child workflow step (Planning/Code/Docs)
- What was generated
- What path was written
- Any constraints applied (premium UI etc.)

---

## [07] Planning Output Contract
Planning file location:
- `/Aliens/.Alien/{Developer}/Planning/`

Planning file naming:
- `{RunId}__{RunItemId}__{WorkflowId}.md`

Example:
- `20251224-094812-WebSDK_Module_API-9F3A__01-User-UserNew-6C1B__WebSDK_Module_API-Planning.md`

Planning must be copy-linked in RunRecord:
- also store a pointer (relative path) inside `Run.json` -> `PlanningCreated[]`

---

## [08] Documentation Output Contract
Docs save rule:
- Docs go to `/Aliens/Docs/` with domain folder.

Recommended domain mapping:
- WebOS workflows => `/Aliens/Docs/WebOS/`
- WebSDK workflows => `/Aliens/Docs/WebSDK/`
- WebApps workflows => `/Aliens/Docs/WebApps/`
- WebBrand workflows => `/Aliens/Docs/WebBrand/`

Documentation naming:
- `{TargetSlug}__{WorkflowId}.md` OR `{ModuleOrApp}/{Thing}.md` (workflow decides)
But RunRecord MUST record exact path in `DocsCreated[]`.

---

## [09] Generated Code Output Contract (Created/Updated Files)
Code output goes to actual ecosystem paths (WebOS/WebApps/WebSDK/WebBrand) as resolved by workflow.

Rules:
- If target file missing and it is **create workflow** => create allowed
- If target file missing and it is **_Update workflow** => error (no create)
- No delete, no rename, no move (unless workflow explicitly supports “migrate” and safety allows)

RunRecord MUST list all code writes in:
- `CreatedFiles[]`
- `UpdatedFiles[]`

Each entry recommended format:
```json
{
  "Path": "/Aliens/WebSDK/PHP.26.00.00/Module/User/api/UserNew.php",
  "Action": "created|updated",
  "Reason": "Workflow WebSDK_Module_API-Code",
  "Diff": {
    "Available": false,
    "Note": "Optional: store patch/diff in Artifacts if enabled"
  }
}
```

---

## [10] Artifacts Contract (Intermediate files)
Artifacts folder per item:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Artifacts/`

Allowed artifacts examples:
- prompt snapshots (sanitized)
- extracted references
- diff patches
- validations
- performance metrics

Never store secrets in artifacts.

---

## [11] Approvals Output Contract
If ApprovalMode=manual OR safety triggers approval:
- Create approval request markdown:
  - `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`

ApprovalId recommended:
- `{RunId}__{RunItemId}__{ActionType}`

Also MUST write:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Approvals.json`

Approvals.json entry minimal:
```json
{
  "ApprovalId": "20251224-094812-WebSDK_Module_API-9F3A__01-User-UserNew-6C1B__ProdWrite",
  "Required": true,
  "Mode": "manual|auto",
  "Decision": "approved|rejected|pending",
  "Reason": "prod code write + db touch",
  "DecidedBy": "human|system",
  "DecidedAt": "ISO-8601 timestamp"
}
```

---

## [12] Error + Warning Output Contract
Errors must follow ACC.Errors codes (ref):
- `Workflows/ACC/_Refs/ACC.Errors.md`

Errors.json minimal entry:
```json
{
  "Code": "ACC_TARGET_INVALID",
  "Message": "Target grammar invalid: 'User//UserNew'",
  "Scope": "TargetsParser",
  "WorkflowId": "WebSDK_Module_API",
  "Target": "User//UserNew",
  "Action": "skipped|failed",
  "Hint": "Use Parent/Child like User/UserNew"
}
```

Warnings.json same structure but Action usually “continued”.

---

## [13] Retention + Clean-up (NoDelete Compatible)
Because NoDelete=true:
- Runs folder can grow large.
Recommended policy:
- Keep last N runs in “hot” index
- Older runs can be archived (zip) but not deleted by workflow automatically (unless explicitly allowed in policy)

If archive allowed, archive output must be written as artifact and logged.

---

## [14] Quick Example: Run Tree (Visual)
Example for:
`Workflow("WebSDK_Module_API","User/UserNew,Account/AccountUpdate","...")`

- `/Aliens/.Alien/Anil/Runs/20251224-094812-WebSDK_Module_API-9F3A/`
  - `Run.md`
  - `Run.json`
  - `Inputs.json`
  - `Outputs.json`
  - `Approvals.json`
  - `Errors.json`
  - `Warnings.json`
  - `Timeline.json`
  - `Items/`
    - `01-User-UserNew-6C1B/`
      - `Item.md`
      - `Item.json`
      - `Payload.json`
      - `Artifacts/`
    - `02-Account-AccountUpdate-0A9D/`
      - `Item.md`
      - `Item.json`
      - `Payload.json`
      - `Artifacts/`

---

## [15] Checklist (Workflow Writer Must Ensure)
- [ ] Run folder created
- [ ] Run.md + Run.json written
- [ ] Items folders created for each target
- [ ] Planning saved to Planning folder (if planning workflow executed)
- [ ] Docs saved to /Aliens/Docs/... (if docs workflow executed)
- [ ] Created/Updated files recorded
- [ ] Approval recorded (auto or manual)
- [ ] Errors/Warnings arrays written (even if empty)

---

## [16] Change Log
- 26.00.00 (2025-12-24): Initial ACC Output Contract
