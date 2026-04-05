---
WorkflowId: Git_Pull
Type: Workflow_Utility
Domain: Git
Category: Version Control — Pull
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-17
EntryPoint: true
Purpose: >
  Safe Git Pull workflow: given a repo name (folder under /Aliens/), remote changes
  ko local me lao — lekin pehle dirty state handle karo (auto-stash), conflicts
  gracefully handle karo, aur data loss kabhi nahi honi chahiye.
  Rebase banned. Merge strategy only.
Invocation:
  Signature:
    - Workflow("Git_Pull", "Course_ACLE", "Pull latest changes")
    - Workflow("Git_Pull", "WebOS, WebSDK", "Sync both repos from remote")
  TargetsMeaning: >
    Repo folder name(s) under /Aliens/. Comma-separated for multiple repos.
    Example: "Course_ACLE" means repo at /Aliens/Course_ACLE/.
    Must be a valid Git repo (has .git/ folder).
  DescriptionMeaning: >
    Human language context: kyun pull ho raha hai.
    Required — empty/missing => fail fast. Pure informational; scope expand nahi karta.
Safety:
  NoDelete: true
  NoForcePush: true
  NoRebase: true
  NoResetHard: true
  SourceReadOnly: false
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
Notes:
  - Rebase permanently forbidden (`--rebase` flag banned).
  - `git reset --hard` permanently forbidden.
  - Agar dirty working tree hai, workflow pehle stash karega (safe).
  - Agar merge conflict aaye: merge abort, stash restore, user ko manual resolve batao.
  - Data loss se bachne ke liye har step pe verify karo.
---

# Git_Pull

# [00] Philosophy (NON-NEGOTIABLE)
```
SAFETY > SPEED > CONVENIENCE
```
- Data loss hone se behtar hai ke pull fail ho jaye.
- Conflict silently resolve karne se behtar hai ke workflow STOP ho jaye.
- Dirty working tree ka kaam kabhi lose nahi hona chahiye.
- Agar kuch bhi suspicious lage: STOP. User se poochho.

# [01] Strict Scope Lock
Yeh workflow sirf yeh karta hai:
- Remote changes ko local me merge karna (fast-forward preferred)
- Dirty working tree ko pehle safe stash karna
- Conflict hone par cleanly abort + restore

Forbidden:
- `git pull --rebase` (PERMANENTLY BANNED)
- `git rebase` (any form)
- `git reset --hard` (any form)
- `git checkout -- .` (discard all changes — BANNED)
- `git clean -fd` (remove untracked — BANNED)
- Branch delete (local ya remote)
- Force push (unrelated but ensure no accidental trigger)
- Koi bhi file create/modify/delete jo Git operation nahi hai
- Scope se bahar koi kaam

# [02] Inputs

## Targets
- One or more repo folder names under /Aliens/
- Comma-separated for multiple: `"WebOS, WebSDK, Course_ACLE"`
- Each target = folder name (no path prefix, no trailing slash)
- Must be valid Git repo (`.git/` folder exist karna chahiye)

## Description
- Required (non-empty)
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Manifest Gate
1) Read `/Aliens/manifest.json`
2) Verify `AllowShellCommands: true`
3) `NoDelete: true` enforce
4) Internet required for pull — verify connectivity

# [04] Target Validation
For each target repo name:

### Step 1: Resolve Path
- Path: `/Aliens/{RepoName}/`
- Agar folder nahi milta => `GIT_ERR_REPO_NOT_FOUND`

### Step 2: Verify Git Repo
- Check: `/Aliens/{RepoName}/.git/` exists
- Agar `.git/` nahi hai => `GIT_ERR_NOT_A_REPO`

### Step 3: Verify Remote Configured
- Command: `git -C /Aliens/{RepoName} remote -v`
- Agar koi remote nahi => `GIT_ERR_NO_REMOTE`

### Step 4: Verify Branch
- Command: `git -C /Aliens/{RepoName} branch --show-current`
- Current branch note karo
- Agar detached HEAD => `GIT_ERR_DETACHED_HEAD` (pull forbidden)

# [05] Pre-Pull Sequence (Per Repo — MANDATORY)

## Step 5.1: Check Working Tree Status
```bash
git -C /Aliens/{RepoName} status --porcelain
```

### Case A: Clean Working Tree (output empty)
- No stash needed
- Set flag: `stash_created = false`
- Proceed directly to Step 5.3

### Case B: Dirty Working Tree (uncommitted changes)
- Proceed to Step 5.2 (auto-stash)

## Step 5.2: Auto-Stash Dirty Changes (Safe Preservation)
```bash
git -C /Aliens/{RepoName} stash push -m "Git_Pull auto-stash {timestamp}" --include-untracked
```

Important flags:
- `-m` with descriptive message (easy to find later if needed)
- `--include-untracked` to preserve new files too
- This saves: staged changes + unstaged changes + untracked files

Verify stash created:
```bash
git -C /Aliens/{RepoName} stash list --max-count=1
```
- Agar stash message match kare => `stash_created = true`
- Agar stash fail ho jaye:
  - **DO NOT proceed with pull** (dirty tree me pull = risk)
  - Report: `GIT_ERR_STASH_FAILED`
  - Skip this repo

Post-stash verify:
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- MUST be clean now. Agar still dirty => `GIT_ERR_STASH_INCOMPLETE`, skip repo

## Step 5.3: Fetch Remote First (Safe — No Merge)
```bash
git -C /Aliens/{RepoName} fetch origin
```
- Just fetch metadata + objects, local kuch change nahi
- Network failure => `GIT_ERR_NETWORK_FAILED`
  - Agar `stash_created = true`: stash pop karo (restore user's work)
  - Then skip repo

## Step 5.4: Check What Will Change
```bash
git -C /Aliens/{RepoName} rev-list HEAD..@{u} --count
```
3 scenarios:

### Scenario A: Nothing to Pull (count = 0)
- Remote me kuch naya nahi
- Report: "Already up to date."
- Agar `stash_created = true`: stash pop karo (Step 5.7)
- Skip this repo (not an error)

### Scenario B: Fast-Forward Possible
```bash
git -C /Aliens/{RepoName} merge-base --is-ancestor HEAD @{u}
```
- If exit code 0: fast-forward possible (safest)
- Proceed to Step 6

### Scenario C: Diverged (local has commits too)
```bash
git -C /Aliens/{RepoName} rev-list @{u}..HEAD --count
```
- If count > 0: local bhi ahead hai
- Merge required (not fast-forward)
- Proceed to Step 6 (merge mode)

# [06] Pull Execution (Per Repo)

```bash
git -C /Aliens/{RepoName} pull --no-rebase origin {branch}
```

Important flags:
- `--no-rebase` MANDATORY (rebase permanently banned)
- This does a `fetch + merge`

### Pull Result Handling:

### Case A: Success (Clean Merge or Fast-Forward)
- Mark: `PULL_SUCCESS`
- Proceed to Step 5.7 (stash pop if needed)
- Then Step 7 (post-pull verification)

### Case B: Merge Conflict
- **DO NOT auto-resolve**
- **DO NOT accept --theirs or --ours**
- Report conflict details:
  ```
  CONFLICT detected in {RepoName}:
  - Conflicted files: {list from git diff --name-only --diff-filter=U}
  - Branch: {branch}
  - Merge was: local {branch} + remote origin/{branch}
  ```
- Abort the merge cleanly:
  ```bash
  git -C /Aliens/{RepoName} merge --abort
  ```
- Verify working tree is back to pre-pull state:
  ```bash
  git -C /Aliens/{RepoName} status --porcelain
  ```
  - Should be clean (since we stashed before)
- Agar `stash_created = true`: stash pop karo (Step 5.7) — user ka kaam restore
- Mark: `GIT_ERR_MERGE_CONFLICT`
- Report: "Pull me conflict aaya. Merge abort kiya aur stash restore kiya. 
  Conflicts manually resolve karo: `git pull --no-rebase` manually run karo, 
  conflicts fix karo, commit karo."

### Case C: Other Error
- Agar `stash_created = true`: stash pop karo (restore user's work)
- Report error details
- Mark: `GIT_ERR_PULL_FAILED`

## Step 5.7: Stash Pop (Restore User's Work — If Stashed)
Only run if `stash_created = true`:

```bash
git -C /Aliens/{RepoName} stash pop
```

### Pop Result:

### Case A: Clean Pop (no conflict)
- User ke changes restore ho gaye on top of pulled changes
- Perfect scenario

### Case B: Pop Conflict
- Yeh tab hota hai jab pulled changes aur stashed changes same files me hain
- **DO NOT auto-resolve**
- Report:
  ```
  STASH POP CONFLICT in {RepoName}:
  - Pull successful tha, lekin stash pop me conflict aaya
  - Your stashed changes are still in stash (not lost)
  - Conflicted files: {list}
  - Manual resolve needed: conflicts fix karo, then `git stash drop` se stash clean karo
  ```
- IMPORTANT: Stash pop conflict me stash automatically retained hota hai
  (stash drop nahi hota jab conflict aaye). So user ka kaam safe hai.
- Mark: `GIT_WARN_STASH_POP_CONFLICT`

# [07] Post-Pull Verification (Per Repo)

### Step 7.1: Verify Branch Intact
```bash
git -C /Aliens/{RepoName} branch --show-current
```
- Same branch as before pull
- Agar different => something went wrong

### Step 7.2: Verify Sync Status
```bash
git -C /Aliens/{RepoName} rev-list HEAD..@{u} --count
```
- Should be 0 (we just pulled everything)

### Step 7.3: Check Working Tree
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Agar stash pop hua: may show unstaged changes (user's work) — that's expected
- Agar stash nahi hua: should be clean

# [08] Multi-Repo Handling
Jab multiple repos comma-separated diye jaye:

Rules:
- Har repo independently process karo
- Ek repo fail hone se dusri repos SKIP nahi hongi
- Per-repo status maintain karo
- Final report me sab repos ka individual status do
- Processing order: targets list me jo order hai, wahi
- Parallel execution forbidden (ek time pe ek repo)

# [09] Error Codes

| Code | Meaning | Action |
|---|---|---|
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | Fail fast |
| `GIT_ERR_REPO_NOT_FOUND` | Folder not found under /Aliens/ | Skip repo |
| `GIT_ERR_NOT_A_REPO` | No .git/ folder | Skip repo |
| `GIT_ERR_NO_REMOTE` | No remote configured | Skip repo |
| `GIT_ERR_DETACHED_HEAD` | HEAD not on a branch | Skip repo |
| `GIT_ERR_STASH_FAILED` | Could not stash dirty changes | Skip repo (changes preserved in tree) |
| `GIT_ERR_STASH_INCOMPLETE` | Stash ran but tree still dirty | Skip repo |
| `GIT_ERR_NETWORK_FAILED` | Fetch/pull network error | Pop stash if needed, skip repo |
| `GIT_ERR_MERGE_CONFLICT` | Pull merge conflict | Abort merge, pop stash, skip |
| `GIT_ERR_PULL_FAILED` | Pull failed (other reason) | Pop stash if needed, skip |
| `GIT_WARN_STASH_POP_CONFLICT` | Stash pop conflicted | Stash preserved, manual resolve |
| `PULL_SUCCESS` | Pull completed successfully | Done |
| `PULL_NOTHING` | Already up to date | Skip (informational) |

# [10] Permanently Forbidden Commands
Yeh commands is workflow me kabhi use nahi honge:

```
git pull --rebase
git rebase
git rebase --interactive
git rebase -i
git reset --hard
git reset --mixed (on important refs)
git checkout -- .  (discard all changes)
git clean -fd  (remove untracked files)
git stash drop  (only after verified clean pop)
git stash clear  (permanently forbidden — all stashes gone)
git branch -D
git push --force
git filter-branch
```

# [11] Stash Safety Contract (Critical)
Stash is user ke uncommitted kaam ka backup. Is liye:

1. **Stash push hamesha `--include-untracked` ke saath hoga** — naye files bhi preserve hon
2. **Stash push hamesha descriptive `-m` message ke saath hoga** — trace kar sako
3. **Stash pop fail hone par stash automatically retain hota hai** — Git ka default behavior. Data safe hai.
4. **`git stash drop` sirf tab allowed hai jab pop cleanly success ho** — workflow me auto-drop hota hai pop success pe
5. **`git stash clear` permanently forbidden** — multiple stashes ka data loss
6. **Agar workflow kisi bhi point par fail ho, pehle check karo: stash_created = true?** Agar haan, to stash pop karo ya at minimum user ko batao ke stash me unka kaam hai.

# [12] Data Loss Prevention Checklist (Agent MUST verify)
Before pull:
- [ ] If dirty tree: stash created with --include-untracked
- [ ] Stash verified (stash list shows our entry)
- [ ] Working tree clean after stash

During pull:
- [ ] --no-rebase flag present
- [ ] No force flags
- [ ] Conflict detected => merge --abort (not resolved blindly)

After pull:
- [ ] If stash was created: stash pop attempted
- [ ] If stash pop conflicted: user informed, stash preserved
- [ ] Branch same as before
- [ ] No files lost (tracked or untracked)

# [13] Status Report (Per Run)

```
=== Git_Pull Report ===
Timestamp: {ISO-8601}
Description: {Description}

| Repo | Branch | Status | Commits Pulled | Stash Used | Notes |
|------|--------|--------|----------------|------------|-------|
| Course_ACLE | main | SUCCESS | 12 | Yes (clean pop) | Fast-forward merge |
| WebOS | main | UP-TO-DATE | 0 | No | Nothing to pull |
| WebSDK | develop | CONFLICT | 0 | Yes (restored) | 3 files conflicted |

Total: 3 repos | 1 success | 1 up-to-date | 1 conflict
```

# [14] Done Criteria
- Har target repo processed (success/skip/fail individually)
- No data loss occurred (uncommitted work preserved via stash)
- No history corruption occurred
- No rebase executed
- No force operations
- Stash properly handled (created/popped/preserved as needed)
- Status report generated with per-repo breakdown
