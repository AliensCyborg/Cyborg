# RefactoringRules — Software Engineer Role (SSOT)

This file expands:
- `Roles/Role.SoftwareEngineer/Rules.md` → Section 7 (Refactoring Rules)

Goal: Improve structure without breaking WebOS flows, APIs, or compatibility.

---

## 1) When Refactoring is Allowed
- Duplicate logic exists across multiple files.
- Complexity blocks new features.
- Bugs are recurring because structure is unclear.

Refactoring is **not** allowed when it changes behavior unintentionally.

---

## 2) Safe Refactoring Sequence
1. Identify repeated pattern
2. Define a target abstraction (Engine/Service/Helper)
3. Keep old API stable (adapter layer if needed)
4. Move code in small steps
5. Verify each step (search + runtime checks)

---

## 3) Hard Boundaries (Do Not Touch)
Unless explicitly instructed, do not refactor:
- Boot
- System
- Kernel
- Row engine
- Authentication core

---

## 4) Compatibility Rules
- Preserve file paths and naming conventions.
- If a rename is required, update all references.
- Avoid breaking changes across versions.

---

## 5) Refactoring Outcomes
A refactor is considered successful only if:
- Behavior remains correct
- Code is simpler to extend
- Duplication is reduced
- No SSOT paths or conventions are violated

---

# END
