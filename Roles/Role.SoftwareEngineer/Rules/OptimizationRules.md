# OptimizationRules — Software Engineer Role (SSOT)

This file expands:
- `Roles/Role.SoftwareEngineer/Rules.md` → Section 5 (Optimization Rules)

Goal: Optimize **only after correctness**, and do it in ways that remain WebOS-aligned and maintainable.

---

## 1) Optimization Priorities (in order)
1. Correctness & security
2. Database efficiency (largest wins)
3. Caching strategy
4. Reducing redundant work (includes/includes/IO)
5. UI performance (paint, layout, payload)

---

## 2) Database Optimization Rules
- Avoid `SELECT *`
- Use paging and limits
- Prefer indexed filters:
  - `uid`, `ecosystem_id`, `tenant_id`, `status`, `created_at`
- Keep joins small (max 3 unless explicitly required)
- Partition heavy tables using MDBMS rules

---

## 3) Caching Rules (WebOS-first)
- Use WebOS cache engine for expensive calls.
- Cache keys must include:
  - ecosystem
  - tenant
  - app/context
- Invalidation must be explicit and safe.

---

## 4) PHP Optimization Rules
- Avoid repeated heavy includes in loops.
- Avoid repeated DB calls inside loops.
- Prefer bulk reads then in-memory mapping.

---

## 5) JS + UI Optimization Rules
- Avoid forced reflow loops.
- Batch DOM writes.
- Provide lightweight skeleton/loading states.

---

## 6) What NOT to Optimize
- Do not micro-optimize readable code.
- Do not remove validations for speed.
- Do not bypass Row engine.

---

## 7) Verification Checklist
- [ ] No correctness regressions
- [ ] Query count reduced or query plan improved
- [ ] Cache invalidation defined
- [ ] No security regression

---

# END
