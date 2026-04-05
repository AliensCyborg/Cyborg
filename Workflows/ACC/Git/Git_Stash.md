---
WorkflowId: Git_Stash
Type: Workflow_Utility
Domain: Git
Category: Version Control — Stash
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-17
EntryPoint: true
Purpose: >
  Safe Git Stash workflow: given a repo name aur action (push/pop/apply/list/show),
  uncommitted changes ko safely stash me save/restore karo. Data loss kabhi nahi
  honi chahiye. Stash clear/drop blindly karna forbidden hai.
Invocation:
  Signature:
    - Workflow("Git_Stash", "Course_ACLE", "Stash current changes before switching task")
    - Workflow("Git_Stash", "WebOS", "Pop latest stash — restore my work")
    - Workflow("Git_Stash", "WebSDK", "Apply stash@{2} without dropping — preview only")
    - Workflow("Git_Stash", "Course_ACLE", "List all stashes")
    - Workflow("Git_Stash", "WebOS", "Show stash@{0} contents")
    - Workflow("Git_Stash", "Course_ACLE, WebOS", "Stash changes in both repos")
  TargetsMeaning: >
    Repo folder name(s) under /Aliens/. Comma-separated for multiple repos.
    Example: "Course_ACLE" means repo at /Aliens/Course_ACLE/.
    Must be a valid Git repo (has .git/ folder).
  DescriptionMeaning: >
    Human language instruction that determines the stash action.
    Required — empty/missing => fail fast.
    Description determines the MODE:
    - Contains "push" / "save" / "stash current" / "stash changes" => PUSH mode
    - Contains "pop" / "restore" / "unstash" => POP mode
    - Contains "apply" / "preview" => APPLY mode (non-destructive pop)
    - Contains "list" / "show all" / "stash list" => LIST mode
    - Contains "show" + stash ref => SHOW mode (view stash contents)
    - Contains "drop" + specific stash ref => DROP mode (single stash remove — GUARDED)
    If mode cannot be determined => default to PUSH (safest).
Safety:
  NoDelete: true
  NoStashClear: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: false
Notes:
  - `git stash clear` permanently forbidden (deletes ALL stashes — catastrophic data loss).
  - `git stash drop` allowed ONLY for specific stash ref after explicit confirmation.
  - Stash push hamesha `--include-untracked` ke saath hoga (naye files bhi preserve).
  - Stash push hamesha descriptive message ke saath hoga.
  - Pop fail par stash preserved rehta hai (Git default — data safe).
  - Internet NOT required for stash operations (pure local).
---

# Git_Stash

# [00] Philosophy (NON-NEGOTIABLE)
```
SAFETY > SPEED > CONVENIENCE
```
- Stash = user ke uncommitted kaam ka backup. Treat with extreme care.
- Stash data lose karna = user ka hours/days ka kaam lose karna.
- Har stash operation verify karo: before aur after.
- Jab doubt ho: LIST first, then decide.

# [01] Strict Scope Lock
Yeh workflow sirf yeh karta hai:
- Stash push (save changes)
- Stash pop (restore + remove from stash)
- Stash apply (restore but keep in stash)
- Stash list (show all stashes)
- Stash show (view specific stash contents)
- Stash drop (remove specific stash — guarded)

Forbidden:
- `git stash clear` (PERMANENTLY BANNED — deletes ALL stashes)
- `git reset --hard` (any form)
- `git checkout -- .` (discard all changes)
- `git clean -fd` (remove untracked)
- Koi bhi commit/push/pull/rebase in this workflow
- Scope se bahar koi kaam

# [02] Inputs

## Targets
- One or more repo folder names under /Aliens/
- Comma-separated for multiple: `"WebOS, WebSDK"`
- Each target = folder name
- Must be valid Git repo

## Description
- Required (non-empty)
- Determines the stash MODE (see Mode Detection)
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Mode Detection (From Description)
Description parse karke mode determine karo. Case-insensitive matching.

### Priority Order (first match wins):
1. **LIST mode**: Description contains `list` / `show all` / `all stashes` / `stash list`
2. **SHOW mode**: Description contains `show` + optional stash ref (e.g., `show stash@{0}`)
3. **DROP mode**: Description contains `drop` + stash ref (e.g., `drop stash@{2}`)
4. **POP mode**: Description contains `pop` / `restore` / `unstash` / `get back`
5. **APPLY mode**: Description contains `apply` / `preview` / `without dropping`
6. **PUSH mode**: Description contains `push` / `save` / `stash current` / `stash changes` / default fallback

### Stash Ref Extraction
Agar Description me specific stash ref hai (e.g., `stash@{2}`, `stash@{0}`):
- Extract using regex: `stash@\{\d+\}`
- Use this ref for POP/APPLY/SHOW/DROP
- Agar ref nahi diya: `stash@{0}` (latest) default use hoga (POP/APPLY/SHOW)
- DROP ke liye explicit ref MANDATORY (blind drop forbidden)

# [04] Target Validation (Same as Git_Pull/Git_Push)
For each target repo name:

### Step 1: Resolve Path
- Path: `/Aliens/{RepoName}/`
- Agar folder nahi milta => `GIT_ERR_REPO_NOT_FOUND`

### Step 2: Verify Git Repo
- Check: `/Aliens/{RepoName}/.git/` exists
- Agar `.git/` nahi hai => `GIT_ERR_NOT_A_REPO`

# [05] PUSH Mode — Save Changes to Stash

### Step 5.1: Check Working Tree Status
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Agar clean (empty output):
  - Report: "Working tree clean. Stash me save karne ko kuch nahi."
  - Mark: `STASH_NOTHING`
  - Skip this repo (not an error)

### Step 5.2: Count What Will Be Stashed
Before stash, inventory lo:
```bash
git -C /Aliens/{RepoName} diff --stat
git -C /Aliens/{RepoName} diff --cached --stat
git -C /Aliens/{RepoName} ls-files --others --exclude-standard
```
Collect:
- Unstaged modified files count
- Staged files count
- Untracked files count
- Total files that will be stashed

### Step 5.3: Execute Stash Push
```bash
git -C /Aliens/{RepoName} stash push -m "{message}" --include-untracked
```

Message format:
- Pattern: `"Git_Stash | {Description_short} | {timestamp_ISO8601}"`
- Example: `"Git_Stash | Stash before switching task | 2026-03-17T14:30:00"`
- Max 100 chars (truncate Description if needed)

Important flags:
- `-m` MANDATORY (always descriptive message)
- `--include-untracked` MANDATORY (preserve new files)

### Step 5.4: Verify Stash Created
```bash
git -C /Aliens/{RepoName} stash list --max-count=1
```
- Latest stash message should match our push message
- Agar mismatch => `GIT_ERR_STASH_VERIFY_FAILED`

```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Working tree should now be clean
- Agar still dirty => `GIT_ERR_STASH_INCOMPLETE`

Mark: `STASH_PUSH_SUCCESS`
Report: "Stashed {N} files (M modified, S staged, U untracked) as stash@{0}"

# [06] POP Mode — Restore + Remove from Stash

### Step 6.1: Verify Stash Exists
```bash
git -C /Aliens/{RepoName} stash list
```
- Agar empty => `GIT_ERR_NO_STASH`
  - Report: "No stashes found. Nothing to pop."
  - Skip repo

### Step 6.2: Show What Will Be Popped
```bash
git -C /Aliens/{RepoName} stash show {stash_ref} --stat
```
- Display stash contents summary to confirm correct stash

### Step 6.3: Check Working Tree Clean (Recommended)
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Agar dirty:
  - **WARN** (not block): "Warning: Working tree me already changes hain. Stash pop se merge ho jayengi.
    Agar conflict aaye to stash preserved rahega."
  - Proceed cautiously

### Step 6.4: Execute Stash Pop
```bash
git -C /Aliens/{RepoName} stash pop {stash_ref}
```

### Pop Result:

### Case A: Clean Pop
- Changes restored successfully
- Stash entry automatically removed by Git
- Mark: `STASH_POP_SUCCESS`
- Report: "Stash {stash_ref} successfully popped. Changes restored."

### Case B: Pop Conflict
- Merge conflict between stash changes and current working tree
- **IMPORTANT**: Git does NOT drop the stash on conflict (data safe)
- Report:
  ```
  STASH POP CONFLICT in {RepoName}:
  - Stash {stash_ref} pop me conflict aaya
  - Your stash is SAFE (not dropped) — data preserved
  - Conflicted files: {list from git diff --name-only --diff-filter=U}
  - Manual resolve needed: conflicts fix karo, stage karo, then `git stash drop {stash_ref}`
  ```
- Mark: `GIT_WARN_STASH_POP_CONFLICT`

### Case C: Pop Error
- Agar other error aaye (rare)
- Stash typically preserved
- Report error details
- Mark: `GIT_ERR_STASH_POP_FAILED`

# [07] APPLY Mode — Restore But Keep Stash (Non-Destructive)

### Step 7.1: Same as POP Step 6.1 — verify stash exists
### Step 7.2: Same as POP Step 6.2 — show contents

### Step 7.3: Execute Stash Apply
```bash
git -C /Aliens/{RepoName} stash apply {stash_ref}
```

- Apply = pop minus the drop. Stash entry hamesha preserved rehti hai.
- Safest restore option — user dekh sakta hai, phir manually drop kar sakta hai.

### Apply Result:

### Case A: Clean Apply
- Changes restored, stash preserved
- Mark: `STASH_APPLY_SUCCESS`
- Report: "Stash {stash_ref} applied. Stash preserved (not dropped).
  Agar satisfied ho to manually `git stash drop {stash_ref}` run karo."

### Case B: Apply Conflict
- Same as Pop conflict — but stash always preserved (apply never drops)
- Report conflict details
- Mark: `GIT_WARN_STASH_APPLY_CONFLICT`

# [08] LIST Mode — Show All Stashes

```bash
git -C /Aliens/{RepoName} stash list
```

### Output Format:
```
=== Stash List for {RepoName} ===
stash@{0}: On main: Git_Stash | Before task switch | 2026-03-17T14:30:00
stash@{1}: On develop: Git_Stash | WIP save | 2026-03-16T10:00:00
stash@{2}: On main: Manual stash before merge

Total: 3 stashes
```

- Agar empty: "No stashes found for {RepoName}."
- Mark: `STASH_LIST_SUCCESS`

# [09] SHOW Mode — View Specific Stash Contents

```bash
git -C /Aliens/{RepoName} stash show {stash_ref} --stat
```

For detail view:
```bash
git -C /Aliens/{RepoName} stash show {stash_ref} -p
```
(Only show summary by default. Full diff sirf agar Description me "detail" / "full" / "diff" ho)

### Output Format:
```
=== Stash Contents: {stash_ref} ===
Message: Git_Stash | Before task switch | 2026-03-17T14:30:00
Files changed: 5
Insertions: 120
Deletions: 30

Files:
  M src/engine.php (+80, -20)
  M src/config.php (+30, -5)
  A src/new_file.php (+10, -0)
  ...
```

- Mark: `STASH_SHOW_SUCCESS`

# [10] DROP Mode — Remove Specific Stash (GUARDED)

### Critical Safety Rules:
1. `git stash clear` is PERMANENTLY FORBIDDEN (all stashes deleted)
2. DROP requires EXPLICIT stash ref (e.g., `stash@{2}`)
3. No ref / wildcard / "all" => `GIT_ERR_DROP_REF_REQUIRED`
4. Before dropping: SHOW the stash contents for confirmation

### Step 10.1: Validate Stash Ref
- Must be exact format: `stash@{N}` where N is a number
- Ref must exist:
  ```bash
  git -C /Aliens/{RepoName} stash show {stash_ref}
  ```
  - Agar error => `GIT_ERR_STASH_NOT_FOUND`

### Step 10.2: Show Before Drop (Informational)
```bash
git -C /Aliens/{RepoName} stash show {stash_ref} --stat
```
- Display what will be permanently deleted

### Step 10.3: Execute Drop
```bash
git -C /Aliens/{RepoName} stash drop {stash_ref}
```

### Step 10.4: Verify Drop
```bash
git -C /Aliens/{RepoName} stash list
```
- Specified stash should no longer appear

Mark: `STASH_DROP_SUCCESS`
Report: "Stash {stash_ref} dropped permanently."

# [11] Multi-Repo Handling
Jab multiple repos comma-separated diye jaye:

Rules:
- Same MODE apply hota hai sab repos par
- Har repo independently process karo
- Per-repo status maintain karo
- Processing order: targets list me jo order hai, wahi
- Parallel execution forbidden

# [12] Error Codes

| Code | Meaning | Action |
|---|---|---|
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | Fail fast |
| `GIT_ERR_REPO_NOT_FOUND` | Folder not found under /Aliens/ | Skip repo |
| `GIT_ERR_NOT_A_REPO` | No .git/ folder | Skip repo |
| `GIT_ERR_NO_STASH` | No stashes exist (pop/apply/show/drop) | Skip repo |
| `GIT_ERR_STASH_VERIFY_FAILED` | Stash push verification failed | Warning |
| `GIT_ERR_STASH_INCOMPLETE` | Tree still dirty after stash push | Warning |
| `GIT_ERR_STASH_NOT_FOUND` | Specific stash ref not found | Skip operation |
| `GIT_ERR_STASH_POP_FAILED` | Pop error (non-conflict) | Stash preserved |
| `GIT_ERR_DROP_REF_REQUIRED` | Drop without specific ref | Block (no blind drop) |
| `GIT_WARN_STASH_POP_CONFLICT` | Pop/apply merge conflict | Stash preserved, manual resolve |
| `GIT_WARN_STASH_APPLY_CONFLICT` | Apply merge conflict | Stash preserved, manual resolve |
| `STASH_PUSH_SUCCESS` | Stash saved successfully | Done |
| `STASH_POP_SUCCESS` | Stash popped successfully | Done |
| `STASH_APPLY_SUCCESS` | Stash applied (preserved) | Done |
| `STASH_LIST_SUCCESS` | Stash list displayed | Done |
| `STASH_SHOW_SUCCESS` | Stash contents displayed | Done |
| `STASH_DROP_SUCCESS` | Stash dropped | Done |
| `STASH_NOTHING` | Nothing to stash (clean tree) | Skip (info) |

# [13] Permanently Forbidden Commands

```
git stash clear              (deletes ALL stashes — catastrophic)
git stash drop               (without explicit ref — blind drop)
git reset --hard
git checkout -- .
git clean -fd
```

# [14] Data Safety Contract

1. **Stash Push**: hamesha `--include-untracked` + descriptive message
2. **Stash Pop conflict**: Git automatically retains stash entry — data SAFE
3. **Stash Apply**: never drops stash — always safe to use for preview
4. **Stash Drop**: only with explicit ref + pre-show
5. **Stash Clear**: BANNED — no bulk delete ever
6. **Working tree check**: before every operation
7. **Verification**: after every operation (stash list + working tree status)

# [15] Status Report (Per Run)

```
=== Git_Stash Report ===
Timestamp: {ISO-8601}
Mode: {PUSH/POP/APPLY/LIST/SHOW/DROP}
Description: {Description}

| Repo | Branch | Status | Details |
|------|--------|--------|---------|
| Course_ACLE | main | PUSH_SUCCESS | 5 files stashed (3M, 1S, 1U) |
| WebOS | main | STASH_NOTHING | Working tree already clean |

Total: 2 repos | 1 success | 1 skipped
```

# [16] Done Criteria
- Har target repo processed (success/skip/fail individually)
- No data loss occurred
- Stash operations verified (before + after state)
- No forbidden commands executed
- Status report generated with per-repo breakdown
- Conflicts (if any) properly reported with stash preserved
