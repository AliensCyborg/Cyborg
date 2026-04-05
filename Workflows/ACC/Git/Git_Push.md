---
WorkflowId: Git_Push
Type: Workflow_Utility
Domain: Git
Category: Version Control — Push
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-17
EntryPoint: true
Purpose: >
  Safe Git Push workflow: given a repo name (folder under /Aliens/), local commits
  ko remote par push karo — lekin pehle pull + conflict resolution + clean state
  verification mandatory hai. Data loss ya history corruption kabhi nahi honi chahiye.
  Force push permanently forbidden hai.
Invocation:
  Signature:
    - Workflow("Git_Push", "Course_ACLE", "Push latest ACLE conversion output")
    - Workflow("Git_Push", "WebOS", "Push engine updates")
    - Workflow("Git_Push", "WebSDK, WebOS", "Push both repos")
  TargetsMeaning: >
    Repo folder name(s) under /Aliens/. Comma-separated for multiple repos.
    Example: "Course_ACLE" means repo at /Aliens/Course_ACLE/.
    Must be a valid Git repo (has .git/ folder).
  DescriptionMeaning: >
    Human language context: kya push ho raha hai, kyun push ho raha hai.
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
  - Force push (`--force`, `--force-with-lease`) permanently forbidden.
  - Rebase (`git rebase`) permanently forbidden in this workflow.
  - `git reset --hard` permanently forbidden.
  - History rewrite commands permanently forbidden.
  - Sab repos /Aliens/ ke direct sub-folders hain.
  - Push se pehle pull mandatory hai — always pull-before-push.
  - Agar pull me conflicts aaye, push STOP. Conflicts pehle resolve karo.
  - Partial push (kuch repos success kuch fail) allowed — per-repo independent status.
---

# Git_Push

# [00] Philosophy (NON-NEGOTIABLE)
```
SAFETY > SPEED > CONVENIENCE
```
- Data loss hone se behtar hai ke push fail ho jaye.
- History corrupt hone se behtar hai ke workflow STOP ho jaye.
- Har step verify karo pehle, action baad me.
- Jab doubt ho: STOP. User se poochho.

# [01] Strict Scope Lock
Yeh workflow sirf yeh karta hai:
- Local commits ko remote par push karna (safe way me)
- Push se pehle pull + merge karna (fast-forward preferred)
- Status report dena

Forbidden:
- `git push --force` ya `--force-with-lease` (PERMANENTLY BANNED)
- `git rebase` (any form)
- `git reset --hard` (any form)
- `git commit --amend` on already-pushed commits
- Branch delete (local ya remote)
- Tag delete
- History rewrite (filter-branch, BFG, etc.)
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
3) `NoDelete: true` enforce (no branch/tag deletion as safety)
4) Internet required for push — verify connectivity

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
- Agar `origin` nahi hai => warning (use first available remote)

### Step 4: Verify Branch
- Command: `git -C /Aliens/{RepoName} branch --show-current`
- Current branch note karo (push isi branch par hoga)
- Agar detached HEAD => `GIT_ERR_DETACHED_HEAD` (push forbidden)

# [05] Pre-Push Safety Sequence (Per Repo — MANDATORY)
Yeh sequence skip/reorder nahi ho sakta. Har step fail par STOP.

## Step 5.1: Check Working Tree Status
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Agar output empty => clean state, proceed
- Agar uncommitted/untracked changes hain:
  - **Auto Stage:** `git -C /Aliens/{RepoName} add -A`
  - **Auto Commit:** `git -C /Aliens/{RepoName} commit -m "Auto-commit: push all pending changes"`
    - Commit message me changed files ka short summary include karo (agar feasible ho)
  - Report: "Dirty working tree detected — auto staged + committed."
  - Proceed to Step 5.2 (NO STOP, NO question, NO skip)
  - User se koi confirmation nahi leni — Git_Push ka purpose hi safe + automatic push hai
  - Agar `git add` ya `git commit` fail ho => tab `GIT_ERR_DIRTY_WORKING_TREE` + STOP

## Step 5.2: Check Unpushed Commits Exist
```bash
git -C /Aliens/{RepoName} log @{u}..HEAD --oneline
```
- Agar output empty => koi push karne ko nahi hai
  - Report: "No unpushed commits. Nothing to push."
  - Skip this repo (not an error, just informational)
- Agar commits hain => note count, proceed

## Step 5.3: Fetch Remote (Safe — No Merge)
```bash
git -C /Aliens/{RepoName} fetch origin
```
- Sirf remote state fetch karta hai, local kuch change nahi
- Network failure => `GIT_ERR_NETWORK_FAILED`

## Step 5.4: Check for Divergence
```bash
git -C /Aliens/{RepoName} status -sb
```
Aur:
```bash
git -C /Aliens/{RepoName} rev-list HEAD..@{u} --count
```
3 scenarios:

### Scenario A: No Divergence (ahead only)
- Local ahead hai, remote pe kuch naya nahi
- SAFE to push directly
- Proceed to Step 6

### Scenario B: Behind Only
- Remote me naye commits hain, local me koi naya commit nahi
- Pull karo (Step 5.5), then check again
- Agar pull ke baad bhi kuch push karne ko nahi => skip

### Scenario C: Diverged (both ahead and behind)
- Dono sides me naye commits hain
- Pull with merge karo (Step 5.5)
- Agar merge conflict aaye => STOP (Step 5.6)

## Step 5.5: Safe Pull (Before Push)
```bash
git -C /Aliens/{RepoName} pull --no-rebase origin {branch}
```
Important flags:
- `--no-rebase` MANDATORY (rebase banned)
- Yeh merge commit create kar sakta hai — that's OK and expected

Pull result check:
- Success (clean merge ya fast-forward) => proceed to Step 6
- Conflict => go to Step 5.6

## Step 5.6: Conflict Handling (STOP — No Auto-Resolve)
Agar merge conflict aaye:
- **DO NOT auto-resolve conflicts**
- **DO NOT accept --theirs or --ours blindly**
- Report conflict details:
  ```
  CONFLICT detected in {RepoName}:
  - Conflicted files: {list}
  - Branch: {branch}
  - Merge was: local {branch} + remote origin/{branch}
  ```
- Abort the merge safely:
  ```bash
  git -C /Aliens/{RepoName} merge --abort
  ```
- Mark repo as `GIT_ERR_MERGE_CONFLICT`
- Push is repo ke liye SKIP
- User ko batao: "Conflicts manually resolve karo, commit karo, phir dobara Git_Push run karo."

# [06] Push Execution (Per Repo)
Jab Step 5 cleanly pass ho jaye:

```bash
git -C /Aliens/{RepoName} push origin {branch}
```

Important:
- NO `--force` (banned)
- NO `--force-with-lease` (banned)
- NO `--set-upstream` unless branch has no upstream yet

### Push Result Handling:
- **Success**: Mark repo as `PUSH_SUCCESS`
- **Rejected (non-fast-forward)**: 
  - Yeh tab hota hai jab fetch ke baad bhi kisi ne push kar diya (race condition)
  - Action: Re-fetch + re-pull + retry push (max 1 retry)
  - Agar retry bhi fail => `GIT_ERR_PUSH_REJECTED`, STOP
- **Authentication failure**: `GIT_ERR_AUTH_FAILED`
- **Network failure**: `GIT_ERR_NETWORK_FAILED`

# [07] Post-Push Verification (Per Repo)
Push ke baad verify karo:

### Step 7.1: Verify Local = Remote
```bash
git -C /Aliens/{RepoName} fetch origin
git -C /Aliens/{RepoName} rev-list HEAD..@{u} --count
git -C /Aliens/{RepoName} rev-list @{u}..HEAD --count
```
- Dono counts 0 hone chahiye (in sync)
- Agar nahi => warning report (but not a failure)

### Step 7.2: Clean State Verify
```bash
git -C /Aliens/{RepoName} status --porcelain
```
- Should be clean (same as before push)

# [08] Multi-Repo Handling
Jab multiple repos comma-separated diye jaye:

Rules:
- Har repo independently process karo
- Ek repo fail hone se dusri repos SKIP nahi hongi
- Per-repo status maintain karo
- Final report me sab repos ka individual status do

Processing order:
- Targets list me jo order hai, wahi order follow karo
- Parallel execution forbidden (ek time pe ek repo)

# [09] Error Codes

| Code | Meaning | Action |
|---|---|---|
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty/missing | Fail fast |
| `GIT_ERR_REPO_NOT_FOUND` | Folder not found under /Aliens/ | Skip repo |
| `GIT_ERR_NOT_A_REPO` | No .git/ folder | Skip repo |
| `GIT_ERR_NO_REMOTE` | No remote configured | Skip repo |
| `GIT_ERR_DETACHED_HEAD` | HEAD not on a branch | Skip repo |
| `GIT_ERR_DIRTY_WORKING_TREE` | Uncommitted changes | Skip repo, ask user to commit/stash |
| `GIT_ERR_NETWORK_FAILED` | Fetch/push network error | Skip repo, retry later |
| `GIT_ERR_MERGE_CONFLICT` | Pull merge conflict | Abort merge, skip, manual resolve needed |
| `GIT_ERR_PUSH_REJECTED` | Non-fast-forward after retry | Skip repo |
| `GIT_ERR_AUTH_FAILED` | Authentication failed | Skip repo, check credentials |
| `PUSH_SUCCESS` | Push completed successfully | Done |
| `PUSH_NOTHING` | No unpushed commits | Skip (informational) |

# [10] Permanently Forbidden Commands
Yeh commands is workflow me kabhi, kisi bhi condition me, kisi bhi reason se use nahi honge:

```
git push --force
git push --force-with-lease
git push -f
git rebase
git rebase --interactive
git rebase -i
git reset --hard
git reset --mixed (on pushed commits)
git commit --amend (on pushed commits)
git filter-branch
git reflog expire
git gc --prune=now (dangerous context)
git branch -D (force delete)
git push origin --delete
git tag -d + git push origin :refs/tags/
```

# [11] Status Report (Per Run)
Har run ke baad summary report:

```
=== Git_Push Report ===
Timestamp: {ISO-8601}
Description: {Description}

| Repo | Branch | Status | Commits Pushed | Notes |
|------|--------|--------|----------------|-------|
| Course_ACLE | main | SUCCESS | 5 | Clean push after pull |
| WebOS | main | SKIPPED | 0 | Dirty working tree |
| WebSDK | main | FAILED | 0 | Merge conflict |

Total: 3 repos | 1 success | 1 skipped | 1 failed
```

# [12] Safety Checklist (Agent Must Verify Before Each Push)
- [ ] Working tree clean (no uncommitted changes)
- [ ] Current branch is not detached HEAD
- [ ] Remote is configured and reachable
- [ ] Fetch completed successfully
- [ ] Pull completed without conflicts (or was not needed)
- [ ] No force flags in push command
- [ ] Push command uses explicit branch name
- [ ] Post-push verification shows local = remote

# [13] Done Criteria
- Har target repo processed (success/skip/fail individually)
- No data loss occurred
- No history corruption occurred
- No force push executed
- No rebase executed
- Status report generated with per-repo breakdown
- Conflicts (if any) cleanly aborted, not silently resolved
