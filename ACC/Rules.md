# AlienCyborg — ACC Rules
# File: AlienCyborg/ACC/Rules.md
# Scope: Global governance layer — applies to all ACC Workflow executions

---

## 1. Purpose of Rules

Rules are AlienCyborg's **governance layer**.

Workflow batata hai:
- kya kaam karna hai

Rules batata hai:
- kaise kaam karna hai
- kya allow hai
- kya forbidden hai
- kis cheez ko priority milegi

Rules **Workflow ke upar** apply hoti hain aur **execution ko control** karti hain.

---

## 2. Rule Types

### 2.1 Hard Rules
- Kabhi violate nahi ho sakti
- System execution stop kar degi

Examples:
- Files delete nahi karni
- Database drop nahi karna
- Hidden behaviour allowed nahi
- Sirf AlienCyborg ke apne files access karna (self-contained boundary)

---

### 2.2 Soft Rules
- Preference define karti hain
- Conflict me workflow ka goal jeet sakta hai

Examples:
- Performance > Beauty
- Simplicity > Overengineering

---

### 2.3 Quality Rules
- Output ki quality control karti hain

Examples:
- UI Premium hona chahiye
- Code readable hona chahiye
- Documentation complete honi chahiye

---

### 2.4 Safety Rules
- Ecosystem protection ke liye

Examples:
- No destructive SQL
- No credential exposure
- No insecure defaults

---

## 3. Engineering Rules

### 3.1 AliensStyle Mandatory
- PascalCase everywhere
- Clean modular structure
- Stepwise echo markers
- Zero noise code

### 3.2 WebOS First
- Follow WebOS patterns always
- Boot → System → Kernel → Unit → OS → App
- Row engine for DB
- Multi-tenant awareness

### 3.3 Enterprise-Grade Output
- No skeleton code
- No placeholder files
- Full, production-ready implementations
- Complete error handling
- Proper validation

---

## 4. Safety Rules

### 4.1 No Deletion
- Files MUST NEVER be deleted
- Folders MUST NEVER be deleted
- Soft-disable allowed (status flags, comments)

### 4.2 No Destructive Operations
- No DROP/TRUNCATE
- No mass DELETE
- No unscoped UPDATE
- No force push
- No hard reset

### 4.3 Access Boundaries
- Only access AlienCyborg's own files (self-contained)
- Owner-level goals, strategies, and private data are outside scope
- AlienCyborg operates within its self-contained installation

---

## 5. Quality Rules

### 5.1 Code Quality
- Enterprise-grade completeness
- Proper contracts/interfaces
- Validation at boundaries
- Security-first design
- Observable with debug logs

### 5.2 Documentation Quality
- Structured and clear
- Accurate file paths
- Step-by-step where needed
- Easy Hinglish for team accessibility

---

## 6. Workflow Execution Rules

### 6.1 Workflow Compliance
- Always follow workflow SSOT exactly
- Never skip steps
- Fail fast on missing data
- Report status after execution

### 6.2 Approval Gates
- Respect approval requirements
- Production code changes need review
- Core system changes need explicit authorization

---

# END OF RULES
