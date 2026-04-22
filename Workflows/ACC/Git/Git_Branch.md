---
WorkflowId: Git_Branch
Type: Workflow_Utility
Domain: Git
Category: Git — Per-Cyborg Branch Bootstrap (Idempotent)
Status: Draft
Version: 26.04.00
LastUpdated: 2026-04-19
EntryPoint: true
Purpose: >
  Idempotent branch provisioning: ensure `{CyborgID}` branch exists locally and
  on remote for every team repo. Used standalone OR as a child step of the
  `Cyborg` setup workflow (Phase 4) and first-run recovery before `Check-in`.
  Never touches `Cyborg` or `.github` (those always stay on `main`).

  Idempotent: if the branch already exists (local or remote), it is re-used,
  never recreated. Safe to re-run any time.
Invocation:
  Signature:
    - Workflow("Git_Branch", "C1071", "Initial branch setup on new machine")
    - Workflow("Git_Branch", "{CyborgID}", "{context}")
  TargetsMeaning: >
    Cyborg ID to bootstrap branches for. Exactly one.
    Empty/missing => `ACC_ERR_TARGETS_REQUIRED`.
  DescriptionMeaning: >
    Free-text context (reason for bootstrap). Required.
    Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.
ChildWorkflows: []
Safety:
  NoDelete: true
  NoForcePush: true
  NoRebase: true
  NoResetHard: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
---

# Git_Branch

# [00] Philosophy (NON-NEGOTIABLE)
```
ONE-TIME, IDEMPOTENT, ADDITIVE-ONLY.
```
- Yeh workflow sirf **create** karta hai — kabhi delete/rename/reset nahi.
- Re-run safe: agar branch pehle se hai, skip (no error).
- Sirf team repos (21) ko touch karta hai. `Cyborg` + `.github` untouched.

# [01] Strict Scope Lock
Allowed per repo (when branch needs creation):
- `git fetch origin`
- `git branch --show-current` (read)
- `git ls-remote --heads origin {CyborgID}` (read)
- `git checkout -b {CyborgID}` (from current HEAD of default branch)
- `git push -u origin {CyborgID}`
- `git checkout {default_branch}` to restore state after bootstrap

Forbidden:
- Delete / rename any branch
- Force-push anywhere
- Touch `Cyborg` or `.github` repos
- Touch any repo not in §03 list
- Any filesystem writes outside git operations

# [02] Inputs

## Targets
- Exactly one Cyborg ID
- Must match manifest `Developer.CyborgID` (integrity gate) OR be an explicit
  override with `Description` containing the phrase `admin-override`.
- Empty/missing => `ACC_ERR_TARGETS_REQUIRED`

## Description
- Required, non-empty. Captures the reason.
- Empty => `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Target Repo List (same 21 team repos as Check-in)

```
.Alien, ACLE, Attendance, Blog, Course, Course_ACLE, Course_ATLE,
Docs, Feedback, Project, Report, Skills, Skill_ACLE, Skill_ATLE,
WebApp, WebBrand, WebDB, WebSDK, Wiki, Wiki_ACLE, Wiki_ATLE
```

- **Excluded:** `Cyborg`, `.github`, `WebOS`.
- Source of truth: `Workflows/ACC/Session/Check-in.md` §4.2 — keep in sync.

# [04] Manifest + Identity Gate
1) Read `/Aliens/manifest.json`
2) `AllowShellCommands: true` required
3) `Developer.CyborgID` must exist AND equal Targets (unless admin-override)
   - Mismatch => `GIT_BRANCH_ERR_IDENTITY_MISMATCH`
4) `Developer.GitAccount` — informational log only (not enforced; different
   machines may have different git credentials)

# [05] Per-Repo Execution (sequential, isolated)

For each `Repo` in §03 list:

## 5.1 Pre-checks
- If `/Aliens/{Repo}/` missing OR `/Aliens/{Repo}/.git/` missing
  → skip with `GIT_BRANCH_SKIP_REPO_MISSING` (no clone)
- If working tree dirty → stash with `--include-untracked` (Git_Pull contract)

## 5.2 Save current branch
- `original_branch = git -C /Aliens/{Repo} branch --show-current`
- If detached HEAD → skip with `GIT_BRANCH_ERR_DETACHED_HEAD`

## 5.3 Determine default branch
- `default_branch` = `git -C /Aliens/{Repo} symbolic-ref --short refs/remotes/origin/HEAD | sed 's@^origin/@@'`
- Fallback: `main`
- If default_branch resolution fails → skip with `GIT_BRANCH_ERR_NO_DEFAULT`

## 5.4 Fetch remote
- `git -C /Aliens/{Repo} fetch origin`
- Network failure => mark `GIT_BRANCH_ERR_NETWORK` and continue to next repo

## 5.5 Check existing state
- `local_exists`  = `git show-ref --verify --quiet refs/heads/{CyborgID}`
- `remote_exists` = `git ls-remote --heads origin {CyborgID}` non-empty

## 5.6 Four-case handling

### Case A: local + remote both exist
- Action: no-op
- Status: `GIT_BRANCH_OK_EXISTS`

### Case B: remote exists, local missing
- `git -C /Aliens/{Repo} checkout -b {CyborgID} origin/{CyborgID}`
- `git -C /Aliens/{Repo} checkout {original_branch or default_branch}`
- Status: `GIT_BRANCH_OK_PULLED_REMOTE`

### Case C: local exists, remote missing
- `git -C /Aliens/{Repo} push -u origin {CyborgID}`
- Status: `GIT_BRANCH_OK_PUSHED_LOCAL`

### Case D: neither exists (the common "brand new cyborg" path)
- Ensure on `default_branch`:
  - If `original_branch != default_branch`: `git checkout {default_branch}`
- `git -C /Aliens/{Repo} checkout -b {CyborgID}`   # branch from default HEAD
- `git -C /Aliens/{Repo} push -u origin {CyborgID}`
- `git -C /Aliens/{Repo} checkout {original_branch or default_branch}`
- Status: `GIT_BRANCH_OK_CREATED`

## 5.7 Restore state
- If we stashed in 5.1: `git stash pop` (Git_Pull stash contract)
- If we switched branches: restore `original_branch` (best effort)
- Stash pop conflict → warn `GIT_BRANCH_WARN_STASH_POP`, never auto-resolve

# [06] Report

```
=== Git_Branch Report ===
Cyborg:    {CyborgID}
GitAccount: {github_account}  (local credentials in use)
Reason:    {description}
Started:   {ts_start_iso}
Finished:  {ts_end_iso}

| Repo        | On disk | Action               | Status                 |
|-------------|---------|----------------------|------------------------|
| .Alien      |   yes   | create+push          | GIT_BRANCH_OK_CREATED   |
| ACLE        |   yes   | exists (no-op)       | GIT_BRANCH_OK_EXISTS    |
| Attendance  |   yes   | pulled from remote   | GIT_BRANCH_OK_PULLED_REMOTE |
| Feedback    |    no   | skipped              | GIT_BRANCH_SKIP_REPO_MISSING |
...

Total repos: 21
Bootstrapped: N
Already OK:   M
Skipped:      K
Failed:       F

Overall: OK | PARTIAL | FAIL
```

# [07] Error / Warning Codes

| Code | Meaning | Severity |
|---|---|---|
| `ACC_ERR_TARGETS_REQUIRED`        | CyborgID missing                          | HARD |
| `ACC_ERR_DESCRIPTION_REQUIRED`    | Reason missing                            | HARD |
| `GIT_BRANCH_ERR_IDENTITY_MISMATCH` | Target != manifest CyborgID (no override) | HARD |
| `GIT_BRANCH_SKIP_REPO_MISSING`     | Repo not on disk / not a git repo         | INFO |
| `GIT_BRANCH_ERR_DETACHED_HEAD`     | Repo on detached HEAD                     | WARN |
| `GIT_BRANCH_ERR_NO_DEFAULT`        | Could not resolve default branch          | WARN |
| `GIT_BRANCH_ERR_NETWORK`           | Fetch/push network failure                | WARN |
| `GIT_BRANCH_OK_EXISTS`             | Branch already OK local+remote            | OK   |
| `GIT_BRANCH_OK_CREATED`            | Branch newly created + pushed             | OK   |
| `GIT_BRANCH_OK_PUSHED_LOCAL`       | Local branch pushed to remote             | OK   |
| `GIT_BRANCH_OK_PULLED_REMOTE`      | Remote branch checked out locally         | OK   |
| `GIT_BRANCH_WARN_STASH_POP`        | Stash pop had a conflict (user's work safe) | WARN |

# [08] Permanently Forbidden
- Deleting any branch
- Force-pushing any branch
- Touching `Cyborg`, `.github`, `WebOS`
- Cloning any repo
- Creating files outside git operations
- `git reset --hard`, `git clean -fd`, `git stash clear`, `git branch -D`

# [09] Recommended Invocation
Run **once per cyborg per machine** the first time they set up their laptop:

```
Workflow("Git_Branch", "C1071", "Initial branch setup on new machine")
```

After this, `Workflow("Check-in", "C1071", "IST")` will find all branches and
stop warning about `CHECKIN_WARN_BRANCH_MISSING`.

# [10] Done Criteria
- All 21 team repos individually processed (ok / skip / fail)
- No branch deleted, no force-push, no history rewritten
- Working tree restored to original state on each repo
- Aggregate report generated
