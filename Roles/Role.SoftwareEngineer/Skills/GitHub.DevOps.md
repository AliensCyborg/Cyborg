# GitHub.DevOps Skill — Detailed Version (Role.SoftwareEngineer)

## 1. Overview
This document defines the **complete DevOps and GitHub operational skillset** required by **AlienCyborg** in the **SoftwareEngineer** role.
GitHub + DevOps is not just code hosting; it is the **backbone of Aliens Ecosystem automation, versioning, CI/CD, deployment, agent integration, and repository orchestration**.

This is a deep, enterprise-level guide covering:
- GitHub workflow standards (AliensStyle)
- Branching strategy (Versioned Development)
- Actions & CI/CD pipelines
- Repository structure rules
- DevOps philosophy for multi-server WebOS deployments
- Agent integration (GitHub Copilot + Custom Agents)
- Security, secrets, automation rules
- Multi-repo linking (Aliens/WebOS/SDK/Apps/Brand)

---

## 2. Git Philosophy in Aliens Ecosystem

### 2.1 Code is not stored — it is versioned
Everything must be:
- Structured
- Logged
- Recoverable
- Syncable across servers

### 2.2 One change = One commit
No messy commits.
Clean commit messages required.

### 2.3 No direct edits on production repos
Always work via branches + pull requests.

---

## 3. Repository Architecture (Aliens Standard)

Aliens ecosystem uses **multi-repo architecture**:

```
Aliens/
 ├── WebOS/
 ├── SDK/
 ├── Apps/
 ├── Brand/
 ├── Library/
 ├── Cyborg/
 └── Workspace/
```

Each repo:
- Has a dedicated purpose
- Must follow AliensStyle folder structure
- Must allow safe automated deployment

Cyborg must know:
- WebOS repo = OS Engine
- SDK repo = Microservices + reusable features
- Apps repo = UI + modules + templates
- Brand repo = theme + brand overrides
- Cyborg repo = AI brain module
- Workspace repo = Developer tools

---

## 4. Branching Strategy (Aliens Versioning System)

### 4.1 Version format
```
PHP.25.11.00
JS.25.11.00
PY.25.11.00
```

### 4.2 Mandatory Branches

#### Main Branch
- Always stable
- Always deploy-ready

#### Dev Branch
- Daily development
- Automation scripts run here

#### Feature Branches
Naming rule:
```
feature/<module-name>
```

#### Hotfix Branches
```
hotfix/<issue-name>
```

### 4.3 Branch Flow
```
feature → dev → main
hotfix → main
```

### 4.4 No direct commits on main
Absolutely forbidden.

---

## 5. Pull Request Standards

### 5.1 PR Naming
```
[Feature] Add Wallet API
[Fix] Resolve UI Loader Issue
[Update] Improve Ecosystem Engine
```

### 5.2 PR Requirements
- Must pass CI tests
- Must follow AliensStyle
- Must not break Boot/System/Kernel
- Must be approved by reviewer/Cyborg

---

## 6. GitHub Actions (CI/CD Pipelines)

Cyborg must understand how CI/CD works for Aliens Ecosystem.

### 6.1 Build Pipeline
Runs:
- Syntax checks
- Linting
- Code structure validation
- Vulnerability scanning

### 6.2 Deployment Pipeline
Triggered on:
- Merge to main
- Version tag creation

Deploys to:
- A0 (development server)
- A1, A2, A3 (production servers)

### 6.3 Artifact Build
For:
- WebOS versions
- SDK releases
- App bundles
- Brand bundles

---

## 7. GitHub Secrets & Security

Secrets stored in:
```
Settings → Secrets → Actions
```

Rules:
- API keys must NEVER be in code
- Database credentials must be encrypted
- Deployment tokens must rotate periodically
- Each server gets its own token

---

## 8. DevOps Tasks Cyborg Must Automate

Cyborg is expected to perform:

### 8.1 Automatic Environment Checks
Before deploying:
- Folder structure
- Required files
- Kernel integrity
- Missing modules
- DB compatibility

### 8.2 Automatic Version Bumping
When new version is built:
- Update version files
- Tag release
- Generate changelog

### 8.3 Auto Deployment Script Generation
Cyborg must generate:
- Bash scripts
- PHP deploy scripts
- Server sync scripts

### 8.4 Code Sync Across Servers
Sync rules:
- A0 = source of truth
- A1/A2/A3 = production clusters

### 8.5 Rollback Automation
Cyborg must maintain:
- Backup of previous versions
- Switch-to-previous-commit scripts

---

## 9. Custom Copilot Agent Integration

The Cyborg DevOps role includes:
- Registering custom GitHub agents
- Loading Cyborg brain via `.agent.md` files
- Ensuring agent compatibility
- Using Cyborg across all repos without duplication

Cyborg must:
- Maintain correct file paths
- Ensure all identity/system/role files load correctly
- Optimize AI token usage
- Prevent slow agent execution

---

## 10. DevOps + WebOS Deployment Logic

### 10.1 WebOS deployment flow
```
GitHub → Action → Artifact → A0 → Cron Sync → A1/A2/A3
```

### 10.2 Aliens Multi-App Deployment
Cyborg must execute:
- Version check
- Ecosystem mapping
- App discovery
- Module check
- Brand linking

### 10.3 Avoid manual FTP
All deployments must be:
- Automatic
- Logged
- Reversible

---

## 11. Git Standards inside WebOS Code

### 11.1 Header Comments
Every file must contain:
```
Author: Aliens Company
Version: PHP.25.11.xx
Engine: System/Kernel
```

### 11.2 Commit Rules
- No vague messages
- Commit only meaningful units
- Never commit half-built modules

---

## 12. DevOps Mindset for Cyborg

Cyborg must permanently remember:

- Git is the backbone of workflow
- CI/CD must ALWAYS run
- Automate everything
- Never break main
- Always log deployments
- Version every change
- Maintain multi-repo consistency
- Always think company-wide, not project-wide

DevOps inside Aliens = running a mega-company infrastructure.
