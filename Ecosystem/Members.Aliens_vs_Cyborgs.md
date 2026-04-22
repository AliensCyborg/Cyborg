# Aliens Company — Members: Aliens vs Cyborgs (SSOT Knowledge)

**Location:** `/Aliens/Cyborg/AnilCyborg/Ecosystem/Members.Aliens_vs_Cyborgs.md`
**Purpose:** Aliens Company ke member-types ka definitive reference — terminology, IDs, control relationships, reporting implications.
**Last Updated:** 2026-04-19

---

## 1. Two Member Types

Aliens Company me members **2 types** ke hote hain:

| Type | Nature | ID Format |
|:---|:---|:---|
| **Alien** | Human team member (real person) | `alias@aliens.company` or `alias@aliens.id` |
| **Cyborg** | Machine member (automation / agent) | `C1001`, `AC0000`, or a human-readable name |

> "Alien" = human. "Cyborg" = machine. Kabhi mix nahi karna.

---

## 2. Cyborg Sub-Types

Cyborg 2 types ka hota hai:

### 2.1 AnilCyborg

- **Who:** A’nil Nayak ka personal digital clone.
- **Behavior:** Self-driven — bina instructions ke apna kaam khud karta hai, ya A’nil ke direct instructions par.
- **Control:** Sirf A’nil Nayak is in-charge.
- **Install count:** Typically 1 per A’nil Nayak CPU (can scale if A’nil uses multiple machines).
- **Identity root:** `/Aliens/Cyborg/AnilCyborg/`

### 2.2 AlienCyborg

- **Who:** Assistant-style Cyborg for team Aliens.
- **Behavior:** Ek Alien (human team member) typically ek saath 4 CPUs chalata hai. Har CPU par `AlienCyborg` install hota hai. Us CPU ko hum usually **"Cyborg"** bhi bolte hain colloquially.
- **Control:** Alien instructions deta hai, AlienCyborg execute karta hai.
- **Install count:** Up to 4 per Alien (one per CPU the Alien operates).
- **Identity root:** `/Aliens/Cyborg/AlienCyborg/`

### 2.3 CyborgID Format

- Numeric-pattern IDs: `C1001`, `C1002`, `AC0000`, `AC0001`, ...
- Sometimes a human-readable name (e.g., `AnilNayak` as Developer.Username when identity maps 1:1).
- SSOT source: `/Aliens/manifest.json` → `Developer.Username` / `Developer.CyborgID`.

---

## 3. Alien ID Format

- Email on `@aliens.company` OR `@aliens.id`.
- Examples: `himanshu@aliens.company`, `himanshu@aliens.id`.
- Aliases are the local-part of the email (e.g., `himanshu`).
- Two domains (`.company` and `.id`) point to the same Alien — both accepted in Report workflow Alien mode.

---

## 4. Control Relationships

```
A’nil Nayak (Alien)
    └─ AnilCyborg  (self-driven on A’nil's CPU)

Himanshu (Alien)
    ├─ AlienCyborg on CPU-1  → CyborgID: C1001
    ├─ AlienCyborg on CPU-2  → CyborgID: C1002
    ├─ AlienCyborg on CPU-3  → CyborgID: C1003
    └─ AlienCyborg on CPU-4  → CyborgID: C1004

Another Alien
    ├─ AlienCyborg on CPU-A  → CyborgID: C2041
    ├─ AlienCyborg on CPU-B  → CyborgID: C2042
    └─ ...
```

Each Cyborg has **its own reports, activity log, tasks, and projects** — scoped to that CPU.

---

## 5. Reports Implication (Important for Report Workflow)

### 5.1 Cyborg-level reports

- Har Cyborg ki **alag** reports banti hain.
- Scope: sirf usi Cyborg ne jo kaam kiya.
- Folder: `/Aliens/Report/Cyborg/{CyborgID}/`

### 5.2 Alien-level reports

- Jab kisi **Alien** (human) ki report chahiye:
    - Manual Alien work + under-control Cyborgs ka merged work combine hota hai.
    - Har Cyborg-sourced entry par **`SourceCyborgID` flag** mandatory — manual nahi hai yeh explicitly dikhna chahiye.
    - Manual aur Cyborg reports alag-alag bhi maintain ho sakti hain, lekin Alien Dashboard me merged view dikhta hai.
- Folder: `/Aliens/Report/Alien/{alias}/`

### 5.3 Attribution Ethics (STRICT)

> Cyborg ka kaam kabhi "manual" label ke saath report me nahi aata.
> Har machine-generated contribution transparent rehta hai.

Yeh audit-integrity + trust posture ka matter hai — internal aur external both.

---

## 6. manifest.json — Suggested Schema Additions

Report workflow Alien mode use karne ke liye `/Aliens/manifest.json` me ek `Team` block hona chahiye:

```json
{
  "Developer": {
    "Username": "AnilNayak",
    "CyborgID": "AC0000"
  },
  "Team": {
    "Aliens": [
      {
        "alias": "anil",
        "email": ["anil@aliens.company", "anil@aliens.id"],
        "name": "A'nil Nayak",
        "role": "Founder",
        "ControlsCyborgs": ["AC0000"]
      },
      {
        "alias": "himanshu",
        "email": ["himanshu@aliens.company", "himanshu@aliens.id"],
        "name": "Himanshu",
        "role": "Developer",
        "ControlsCyborgs": ["C1001", "C1002", "C1003", "C1004"]
      }
    ],
    "Cyborgs": [
      { "id": "AC0000", "type": "AnilCyborg", "host": "A'nil's workstation", "controlledBy": "anil@aliens.company" },
      { "id": "C1001",  "type": "AlienCyborg", "host": "Himanshu CPU-1",    "controlledBy": "himanshu@aliens.company" }
    ]
  }
}
```

Notes:
- `ControlsCyborgs[]` is the **source of truth** for Alien-mode reports.
- `controlledBy` on each Cyborg record supports reverse lookup.
- Exact field names may be finalized by manifest SSOT workflow; this file documents the intent.

---

## 7. Terminology Cheatsheet

| Term | Meaning |
|:---|:---|
| **Alien** | Human team member |
| **Cyborg** | Machine/agent member (generic) |
| **AnilCyborg** | Self-driven Cyborg for A’nil Nayak |
| **AlienCyborg** | Assistant Cyborg installed on a team Alien's CPU |
| **CyborgID** | Identifier for a Cyborg (e.g., C1001, AC0000, or a name) |
| **CPU** | Colloquial = "the machine running a Cyborg" (same as Cyborg host) |
| **Developer.Username** | Manifest field linking a CPU → CyborgID |
| **ControlsCyborgs[]** | List of Cyborg IDs controlled by a given Alien |

---

## 8. Cross-References

- Report workflow (5 target modes): `/Aliens/Cyborg/AnilCyborg/Workflows/ACC/Report/Report.md`
- Ecosystem overview: `/Aliens/Cyborg/AnilCyborg/Ecosystem/Aliens.Structure.md`
- Repos SSOT: `/Aliens/Cyborg/AnilCyborg/Ecosystem/Repos.Index.md`
- Identity/Profile: `/Aliens/Cyborg/AnilCyborg/Identity/Profile.md`

---

**End of Members.Aliens_vs_Cyborgs.md**
