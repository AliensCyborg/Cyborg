# ACC.Runtime.md

## ACC Runtime Specification  
**Version:** 1.0  
**Status:** Locked  
**Scope:** AlienCyborg Agent · ACC Language · WebOS · VS Code · Azure AI

---

## 1. Purpose of ACC Runtime

ACC Runtime define karta hai:

- `Workflow()` kaise parse hoga
- kaise execute hoga
- kaise control hoga
- kaise safe rahega
- kaise repeatable rahega

> ACC Runtime ka goal hai  
> **Human intent → deterministic execution**  
> bina long prompts, bina confusion.

---

## 2. Core Execution Primitive

ACC ka **sirf ek execution primitive** hai:

```
Workflow(WorkflowID, Name, Description)
```

Iske alawa ACC runtime me koi imperative instruction nahi hoti.

---

## 3. Runtime Resolution Flow

User / Developer  
→ Workflow()  
→ ACC Runtime  
→ ACC.Manifest.json  
→ Workflow Definition (.md)  
→ Execution Plan  
→ Sub-Workflows  
→ File / Code / Docs Generation  
→ Summary Output

---

## 4. Workflow() Argument Resolution

### 4.1 WorkflowID
- Manifest me registered hona zaroori
- Missing / forbidden = execution stop

### 4.2 Name
- Comma = multiple targets  
- Slash = hierarchy  
- Implicit loop generate hota hai

### 4.3 Description
- Human language allowed  
- Constraints, quality, preference me convert hota hai

---

## 5. Workflow Definition Loading

- Ek workflow = ek file  
- Location: `AlienCyborg/Workflows/**/WorkflowID.md`  
- Metadata header mandatory

---

## 6. Execution Lifecycle

1. Validation  
2. Planning  
3. Approval (Manual mode only)  
4. Execution  
5. Verification  
6. Documentation

---

## 7. Auto vs Manual Mode

- Auto: direct execution  
- Manual: approval checkpoints active

---

## 8. Sub-Workflow Rules

- Manifest-allowed calls only  
- Circular calls forbidden  
- Parent context inherit hota hai

---

## 9. Permission Rules

- ❌ File delete not allowed  
- ✅ File edit allowed  
- ✅ Prod modify allowed  
- ✅ DB touch allowed  

Sab kuch logged hota hai.

---

## 10. Idempotency

- Duplicate creation nahi hota  
- Existing structure reuse hota hai

---

## 11. Failure Handling

- Partial changes documented  
- Resume-safe execution  
- Silent failure nahi hoti

---

## 12. Output Contract

- Executed workflows  
- Targets  
- Files created / modified  
- Docs updated  
- Suggested next workflows

---

## 13. Runtime Guarantees

- Predictable  
- Repeatable  
- Safe  
- Ecosystem-aligned

---

## 14. Scope Limitation

- General programming language nahi  
- Orchestration-focused language

---

## 15. Philosophy

**Human bolta hai kya chahiye**  
**ACC decide karta hai kaise**  
**AlienCyborg execute karta hai**

---

END OF FILE
