# ACC.Version.md
# AlienCyborg Codes (ACC)
# ============================================================
# Copyright (c) A|iens. All rights reserved.
#
# Author: AlienCyborg
# Author URI: https://aliens.com
#
# Purpose:
#   This document defines the complete versioning philosophy,
#   lifecycle, compatibility rules, and upgrade strategy for
#   ACC (AlienCyborg Codes).
#
# ============================================================

## 1. What is ACC Versioning?

ACC versioning is **not just a language version**.
It represents the **capability level of the AlienCyborg Agent**
and the **guaranteed behavior of Workflows executed through ACC**.

Every ACC version answers three questions:
1. What can ACC understand?
2. What can ACC execute safely?
3. What guarantees does ACC give to developers and systems?

---

## 2. Version Format

ACC follows a **Semantic + Capability Hybrid Versioning Model**.

Format:
```
ACC.MAJOR.MINOR.PATCH
```

### 2.1 MAJOR
- Breaking changes in:
  - Workflow execution logic
  - Manifest rules
  - Permission system
  - Runtime behavior
- Old workflows **may require migration**

Example:
```
ACC.1.x.x → ACC.2.x.x
```

---

### 2.2 MINOR
- New features added:
  - New Workflow capabilities
  - New ACC keywords (future)
  - New safety rules
- Backward compatible

Example:
```
ACC.1.2.x → ACC.1.3.x
```

---

### 2.3 PATCH
- Bug fixes
- Performance improvements
- No behavior change

Example:
```
ACC.1.2.3 → ACC.1.2.4
```

---

## 3. Current Version

```
ACC.0.1.0
```

### Meaning:
- 0.x → Experimental but structured
- Safe for internal ecosystems (Aliens WebOS, SDK, WebApp)
- Not public-standardized yet

---

## 4. Version Scope

ACC version controls:

| Area | Controlled |
|----|----|
| Workflow() syntax | YES |
| Workflow execution order | YES |
| Manifest rules | YES |
| Approval logic | YES |
| Auto vs Manual mode | YES |
| File & DB permissions | YES |
| AI decision boundaries | YES |

ACC version does **NOT** control:
- PHP version
- JS version
- Python version

ACC is **above programming languages**.

---

## 5. Version Locking Rules

### 5.1 Global Lock

Every AlienCyborg instance MUST declare:

```json
{
  "ACC_Version": "0.1.0"
}
```

If version mismatch:
- Execution must stop
- Error must be raised
- No silent fallback allowed

---

### 5.2 Workflow Compatibility

Each workflow may optionally declare:

```yaml
MinACC: 0.1.0
MaxACC: 0.2.x
```

If ACC version is outside range:
- Workflow is skipped
- Warning is logged

---

## 6. Upgrade Strategy

### 6.1 Patch Upgrade
- Auto-upgradable
- No approval required

### 6.2 Minor Upgrade
- Auto-upgradable
- Compatibility check required

### 6.3 Major Upgrade
- Manual approval required
- Migration guide mandatory

---

## 7. Backward Compatibility Promise

ACC guarantees:
- Same workflow = same output
- Same input = same execution
- Same rules = same decisions

Unless MAJOR version changes.

---

## 8. Version Lifecycle

| Stage | Description |
|----|----|
| Draft | Internal design |
| Experimental | ACC 0.x |
| Stable | ACC 1.x |
| Mature | ACC 2.x |
| Long-Term | ACC LTS |

---

## 9. LTS (Long-Term Support)

Future plan:
```
ACC.1.LTS
```
- Security fixes only
- No feature changes
- Enterprise-safe

---

## 10. Version & Ecosystem Binding

ACC version is tightly bound to:
- AlienCyborg Agent version
- Workflow definitions
- Manifest schema

Mismatch is **not allowed**.

---

## 11. Philosophy

> ACC versions are contracts.
> Not numbers.
> Not marketing.
> But trust.

---

## 12. End of Document

This document is part of the **ACC Core System**
and must not be modified without Core Team approval.
