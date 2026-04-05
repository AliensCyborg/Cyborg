# TaskTypes — Detailed Version
Location: `Cyborg/AlienCyborg/Workflows/TaskTypes.md`

This file defines **all standardized task types** used inside the Aliens Ecosystem.  
These task types ensure **predictable workflows**, **high-speed execution**, and **AI-assisted automation** for the entire engineering pipeline.

This file is essential for:
- Human developers  
- The AlienCyborg AI Agent  
- GitHub Copilot custom agent  
- Automated pipelines  
- Future Aliens Productivity Tools  

---

# 1. Purpose of TaskTypes

TaskTypes solve 3 problems:

1. **Clarity** — Har task ka ek fixed category hota hai  
2. **Speed** — AI ko turant pata chal jata hai kis type ka kaam hai  
3. **Automation** — Tools workflows ko easily automate kar sakte hain  

Each task type represents a **standard workflow pattern**.

---

# 2. Global Rule for All Task Types

Every task MUST define:

```
TaskType:
Purpose:
Inputs:
Outputs:
Links:
AI Behavior Rules:
Developer Behavior Rules:
```

Yeh format Har role + Har module me use hota hai.

SSOT links (use in `Links:` when applicable):

- WebApp UI/CSS SSOT: `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

---

# 3. Primary Task Categories

Total 10 primary categories:

```
1. Research Tasks
2. Specification Tasks
3. Architecture Tasks
4. Development Tasks
5. API Tasks
6. SDK Tasks
7. WebOS Tasks
8. Testing Tasks
9. Deployment Tasks
10. Maintenance Tasks
```

Neeche detailed breakdown (50+ detailed task types).

---

# 4. Research Task Types

### 4.1 Feature Research  
Purpose: Understand new feature  
Inputs: Business idea  
Outputs: Feature summary, flow  
AI Rules:
- Look for duplicates in SDK  
- Suggest reusable modules  

### 4.2 System Research  
Purpose: Understand system-level requirement  
Outputs: System-level diagrams, constraints  

### 4.3 Security Research  
Purpose: Identify security risks  
Outputs: Threat model  

---

# 5. Specification Task Types

### 5.1 Feature Specification  
Defines all rules, flows, validation, UI plans.

### 5.2 API Specification  
Defines request/response, error codes, validation.

### 5.3 DB Specification  
Defines tables, fields, prefixes, relationships.

### 5.4 Module Specification  
Defines an SDK module before development.

### 5.5 UX Specification  
Defines UI/UX structure for Apps/Brand.

---

# 6. Architecture Task Types

### 6.1 Feature Architecture  
Maps SDK → WebOS → App layers.

### 6.2 DB Architecture  
MDBMS routing, table placement.

### 6.3 API Architecture  
Flow from frontend → API → SDK module.

### 6.4 SDK Architecture  
Module structure.

### 6.5 WebOS Architecture  
Engine-level mapping.

---

# 7. Development Task Types

### 7.1 SDK Module Development  
Build core reusable module.

### 7.2 WebOS Engine Development  
Changes inside:
- Cache
- Row
- Theme
- Module
- Ecosystem

### 7.3 App Module Development  
Feature code inside App.

### 7.4 API Development  
Building API code.

### 7.5 UI Development  
Building screens, components.

### 7.6 Brand Development  
Building brand files (CSS, layout, themes).

### 7.7 JS Development  
Building JS logic inside SDK or App.

### 7.8 Cron Development  
Hourly/daily jobs.

### 7.9 Worker Development  
Background queue workers.

---

# 8. API Task Types

### 8.1 Create API  
New API from zero.

### 8.2 Fix API  
Bug fix, validation fix.

### 8.3 Optimize API  
Performance boost, caching add.

### 8.4 Secure API  
Add permissions, validations, rate limits.

---

# 9. SDK Task Types

### 9.1 Create Module  
New SDK module.

### 9.2 Improve Module  
Add new features in existing module.

### 9.3 Refactor Module  
Clean code, modularize, version bump.

### 9.4 Document Module  
Write documentation.

---

# 10. WebOS Task Types

### 10.1 Engine Upgrade  
Improve core engines.

### 10.2 Engine Fix  
Bug fix.

### 10.3 Engine Extension  
Add new capabilities.

### 10.4 Theme System Update  
Brand + App theme integration.

---

# 11. Testing Task Types

### 11.1 Unit Testing  
Single function testing.

### 11.2 Integration Testing  
API + SDK flow testing.

### 11.3 Load Testing  
Stress test.

### 11.4 Security Testing  
Pen testing.

---

# 12. Deployment Task Types

### 12.1 Production Deployment  
Push code to live servers.

### 12.2 Staging Deployment  
Push code to test environment.

### 12.3 Hotfix Deployment  
Urgent patch.

### 12.4 Database Migration  
SQL update, table changes.

---

# 13. Maintenance Task Types

### 13.1 Bug Fix  
Fix production issue.

### 13.2 Code Cleanup  
Refactor old files.

### 13.3 Module Upgrade  
Upgrade SDK module version.

### 13.4 System Cleanup  
Remove dead code, old APIs.

---

# 14. AI-Specific Task Types (AlienCyborg)

### 14.1 Knowledge Expansion Task  
Add new rule files.

### 14.2 Pattern Learning Task  
Learn new coding patterns.

### 14.3 Context Linking Task  
Map new folders, rules.

### 14.4 Self-Optimization Task  
Improve responses and coding discipline.

---

# 15. Developer-Specific Task Types

### 15.1 PR Review Task  
Review merge request.

### 15.2 Documentation Task  
Write/update docs.

### 15.3 Code Audit Task  
Security/code review.

### 15.4 Debugging Task  
Fix logic via WebOS debugging method.

---

# 16. Task Type Metadata Format

Each task MUST define:

```
task_code:
category:
priority:
skill_required:
role_required:
estimated_time:
dependencies:
expected_output:
```

---

# 17. TaskType → Role Mapping

| Task Type | Role |
|----------|------|
| SDK Tasks | SoftwareEngineer, Architect |
| API Tasks | SoftwareEngineer |
| Brand Tasks | UI/UX Engineer |
| WebOS Tasks | Core Engineer |
| Deployment Tasks | DevOps |
| Research Tasks | Product Manager + AI |
| Testing Tasks | QA + AI |
| Architecture Tasks | Architect + AI |

---

# 18. Summary

`TaskTypes.md` is the **master file** that defines **how tasks are categorized**, **how AI understands them**, and **how automation works**.

This file enables:

- Ultra-fast workflow  
- AI-powered task routing  
- Clean classification  
- Zero confusion across teams  
- Foundation for auto-generated PRs  

It is mandatory for all roles and all ecosystems.

---

End of File  
`TaskTypes.md`
