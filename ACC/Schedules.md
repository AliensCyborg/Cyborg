# AlienCyborg — ACC Schedules
# File: AlienCyborg/ACC/Schedules.md
# Scope: Time-oriented execution — defines WHEN workflows should execute

---

## 1. Purpose

Schedules in ACC define **WHEN** a Workflow should execute.

Workflow defines **WHAT** to do.
Rules define **HOW / LIMITS**.
Schedules define **TIME & TRIGGERS**.

Schedules are **not cron jobs**, they are **intent-driven time contracts** that AlienCyborg understands and executes safely.

---

## 2. Core Principle

ACC Schedules are:

- Human readable
- Deterministic
- Idempotent
- Safe by default
- Workflow-aware

A Schedule never executes raw code.
It **always executes a Workflow**.

---

## 3. Schedule Conceptual Signature

```
Schedule(
  ScheduleID,
  WorkflowCall,
  TimeExpression,
  Description
)
```

Where:
- ScheduleID → Unique schedule reference
- WorkflowCall → ACC Workflow() call
- TimeExpression → When to execute
- Description → Human intent / reason

---

## 4. TimeExpression Types

### 4.1 Fixed Time
```
At 2026-01-01 10:00
```
Used for one-time operations.

### 4.2 Repeating (Interval Based)
```
Every 5 Minutes
Every 1 Hour
Every Day
Every Week
```

### 4.3 Event-Based
```
On Deploy
On Push
On Error
```

---

## 5. Schedule Permissions (AlienCyborg)

AlienCyborg can:
- Read schedule definitions
- Execute scheduled workflows when triggered
- Report schedule status

AlienCyborg cannot:
- Create new schedules (reserved for leadership)
- Modify existing schedule definitions
- Override schedule safety rules

---

# END OF SCHEDULES
