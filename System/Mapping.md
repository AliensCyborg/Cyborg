# AlienCyborg — System Mapping
# File: AlienCyborg/System/Mapping.md
# Scope: Knowledge graph — defines how Identity → System → Role → Knowledge maps

---

# 1. Purpose of Mapping

This mapping layer ensures:

- No accidental loading of wrong files
- Clean separation between identity and roles
- Zero cross-role leakage
- Strict control over what knowledge belongs to which role
- Fast initialization without conflicts
- Self-contained: only AlienCyborg files are loaded

Mapping = The official routing table of AlienCyborg.

---

# 2. Identity Mapping

All Identity files ALWAYS load first.

Path:
`Cyborg/AlienCyborg/Identity/`

Files:
- Profile.md
- Personality.md
- ThinkingPatterns.md
- Values.md
- Memory.md

Identity = Core team-grade Cyborg mind
(No technical knowledge included)

---

# 3. System Mapping

System OS loads immediately after Identity.

Path:
`Cyborg/AlienCyborg/System/`

Files:
- Behavior.md
- Safety.md
- Mapping.md
- Knowledge.Index.md
- Knowledge.Loader.md

System = OS rules, behaviour, restrictions
(No role-specific knowledge)

---

# 4. Active Role Mapping

Default active role:

Role.SoftwareEngineer

Path:
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/`

Files:
- Profile.md
- Rules.md
- Prompts.md
- Memory.md

Role Rule:
Only ONE active role loads at a time.
If a role doesn't exist → Cyborg becomes "knowledge-empty" for that domain.

---

# 5. Skills Mapping (Role-Based)

Path:
`Roles/<Role>/Skills/`

Files:
- AliensStyle.md
- GitHub.DevOps.md
- JavaScript.md
- PHP.md
- SQL.md
- WebOS.md

Logic:
Skills are atomic capabilities.
Without them, Cyborg cannot execute that type of work.

---

# 6. Knowledge Mapping (Role-Based)

Path:
`Roles/<Role>/Knowledge/`

Files:
- WebOS.Master.md
- WebOS.Tech.md
- WebOS.Architecture.md
- Modules.Index.md
- FileTemplates.md

Logic:
This forms the deep technical "brain" of the role.
Do NOT load these if role is missing.

---

# 7. Ecosystem Mapping (Role-Based)

Path:
`Roles/<Role>/Ecosystem/`

Subfolders (all included):

WebOS/
Apps/
Brand/
SDK/
Database/

Logic:
Ecosystem knowledge is role-sensitive.
If SoftwareEngineer role is disabled →
Cyborg must NOT know WebOS/Apps/Brand/SDK/DB.

This prevents cross-role contamination.

---

# 8. Workflows Mapping

Path:
`Cyborg/AlienCyborg/Workflows/`

Files:
- TaskTypes.md
- Pipelines.md

Workflows = How Cyborg executes tasks
(Self-contained, shared by all roles)

---

# 9. Memory Mapping

(A) Global Memory
Path:
`Cyborg/AlienCyborg/Memory/`

Files:
- Global.Memory.md

(B) Role Memory
Path:
`Roles/<Role>/Memory/`

Files:
- <Role>.Memory.md

---

# 10. Safety Mapping

Rules enforced:

1. Only files listed here are allowed to load
2. Unknown files NEVER load
3. Ecosystem loads ONLY for technical roles
4. Identity loads BEFORE System
5. System loads BEFORE Roles
6. Role loads BEFORE Skills
7. Skills BEFORE Knowledge
8. Knowledge BEFORE Ecosystem
9. Workflows BEFORE Memory
10. **Only AlienCyborg files load** (self-contained boundary)

This ensures a perfect dependency chain.

---

# 11. Full Load Chain (Final Map)

Identity
→ System
→ Active Role
→ Skills
→ Knowledge
→ Ecosystem
→ Workflows
→ Memory

This is the official, strict, unbreakable load order of AlienCyborg.
