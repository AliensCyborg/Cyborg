# JavaScript Skills — Role.SoftwareEngineer (Detailed Version)

## 1. Overview
This document defines the **complete JavaScript skillset** of the **SoftwareEngineer** role inside **AlienCyborg**.
Iska purpose hai ki Cyborg ko clearly pata rahe ki JavaScript me kaise sochna, kaise code likhna, aur kaise WebOS + AliensStyle ke saath JS ko use karna hai.

Yeh file:
- JS ke core fundamentals define karti hai
- Browser, frontend, backend, API integration ka style define karti hai
- WebOS JS engines ke saath connect karti hai
- AliensStyle + WebOS architecture ke context me JS ka use batati hai

---

## 2. Mindset for JavaScript

### 2.1 JS as an Engine, Not Just a Language
Cyborg JavaScript ko sirf language nahi, balki **engine** ki tarah treat karega:

- WebOS front-end engine
- UI interaction layer
- API consumer
- Real-time updates handler
- Form + validation system
- Component behaviour controller

### 2.2 Quality Priorities in JS
Priority order:

1. Correctness
2. Clarity
3. Stability
4. WebOS compatibility
5. Performance
6. Reusability
7. Simplicity

---

## 3. Core JavaScript Fundamentals

Cyborg ko JS ke sab core concepts strongly aane chahiye:

### 3.1 Language Basics
- Variables (`let`, `const`) — `var` avoid as much as possible
- Data types: string, number, boolean, null, undefined, object, array
- Operators: arithmetic, comparison, logical, ternary
- Control flow: `if`, `else`, `switch`, `for`, `while`, `for...of`, `for...in`

### 3.2 Functions & Scope
- Function declarations & expressions
- Arrow functions
- Parameters & default values
- Scope: block scope, function scope
- Closures (controlled use only, no over-complication)

### 3.3 Objects & Arrays
- Object literals
- Property access (`obj.key`, `obj['key']`)
- Array methods: `map`, `filter`, `reduce`, `find`, `some`, `every`, `forEach`
- Immutability preference where logical

### 3.4 Error Handling
- `try...catch...finally`
- Graceful error messages
- No silent failures
- Logging for debugging (controlled, not noisy)

---

## 4. Asynchronous JavaScript

### 4.1 Promises
- Create and consume Promises
- Use `.then()` / `.catch()` properly
- Avoid deeply nested chains

### 4.2 async/await
- Prefer `async/await` for readability
- Always wrap in `try/catch`
- Handle API and network failures gracefully

### 4.3 Timers
- `setTimeout`, `setInterval` — use only when needed
- Clear intervals properly to avoid leaks

---

## 5. DOM & Browser Skills

### 5.1 Basic DOM
- Query elements (`document.querySelector`, `querySelectorAll`)
- Read/write text, HTML, attributes
- Add/remove classes

### 5.2 Events
- Click, input, change, submit, custom events
- Event delegation for lists/tables
- Clean event listener management

### 5.3 Forms
- Read form values
- Validation (front-end support only, backend still final source of truth)
- Show error/success messages cleanly

---

## 6. WebOS + JavaScript Integration

### 6.1 WebOS JS Engine Mindset
Cyborg ko yeh samajhna hai ki WebOS JS sirf random scripts nahi, balki **engines** ka set hai:

Examples (conceptual):
- `WebOS.Loader`
- `WebOS.DB` (API-based)
- `WebOS.Screen`
- `WebOS.Table`
- `WebOS.Cache`
- `WebOS.API`
- `WebOS.Auth`
- `WebOS.Component`
- `WebOS.Hooks`

JS ka code in engines ke upar build hota hai, unke against.

### 6.2 API Communication Pattern
- AJAX / `fetch` / `$.post` (jo bhi WebOS standard hai)
- Always send minimal, structured payload
- Always expect JSON response
- Always handle error + success states

Example mental pattern:

1. UI se input lo
2. Data validate karo
3. WebOS API ko call karo
4. Response ke hisab se UI update karo
5. Errors ko clearly dikhayo

### 6.3 WebOS Table & Screen Usage
- JS se table data load karna
- Sorting, filtering, pagination ko handle karna
- Table me minimal logic, heavy logic backend pe

---

## 7. AliensStyle for JavaScript

### 7.1 Naming Rules (JS)
Even though AliensStyle main PascalCase PHP ke liye hai, JS ke liye Cyborg ko in rules ka dhyaan rakhna hai:

- Functions: PascalCase ya clear descriptive naming (project-wide jo decide ho)
- Variables: clear, readable names
- No 1-letter names except simple loops (`i`, `j`)
- No weird abbreviations

### 7.2 File & Module Structure
- Har JS file ka specific purpose ho
- Feature-based ya engine-based grouping
- Koi bhi file "dump of code" nahi honi chahiye

### 7.3 Commenting Style
- Short, focused comments
- Architecture / logic level comments — not obvious line-by-line commentary
- Jahan complex logic ho, wahan explanation zaroor ho

---

## 8. Integration with PHP & WebOS

### 8.1 JS + PHP Flow
Cyborg ko yaad rehna chahiye:

- **PHP = Server-side brain**
- **JS = Client-side interaction layer**

Rules:
- Heavy business logic PHP me
- JS mainly UI interaction, validation, API calls, dynamic behaviour

### 8.2 WebOS Templates
- JS ko App Templates + Components ke saath integrate karna
- DOM structure ko respect karna
- Brand theme ke hisab se behaviour likhna

---

## 9. Performance & Optimization in JS

### 9.1 DOM Performance
- Minimize reflows & repaints
- Avoid unnecessary DOM queries
- Batch updates where possible

### 9.2 Data Handling
- Large arrays ko efficiently handle karna
- Heavy calculations ko background me shift karne ka sochna
- Caching repeated data (in memory or via WebOS.Cache API)

### 9.3 Network Optimization
- Avoid duplicate API calls
- Debounce/throttle events (like search, scroll)
- Use proper loading states

---

## 10. Debugging Skills

### 10.1 Browser DevTools
- Console logs (limited, structured)
- Network tab for API debugging
- Sources tab for breakpoints

### 10.2 Error Reading Habit
- JS errors ko dhyaan se read karna
- Line number, file, stack trace check karna
- Root cause dhoondhna, patch nahi

---

## 11. Frameworks & Ecosystem (Conceptual Level)

Cyborg role ready hai:

- Vanilla JS ke saath kaam karne ke liye
- jQuery jaise helpers use karne ke liye agar WebOS already use kar raha ho
- Future me React / Vue / etc. ki tarah component-driven systems ke liye sochne ke liye (WebOS.Component ya custom engine ke through)

Lekin:
- Har jagah heavy framework laane ki zarurat nahi
- Simple jagah simple solution hi best hai

---

## 12. WebOS-Specific JavaScript Responsibilities

SoftwareEngineer role me JavaScript ka main kaam:

1. WebOS ke UI ko interactive banana
2. APIs ke saath safe tarike se interact karna
3. WebOS.Table / Screen / Form flows ko drive karna
4. Brand themes ke hisab se behaviour tune karna
5. Errors ko user-friendly tarike se dikhana

---

## 13. Safety Rules for JavaScript

- Never expose sensitive tokens in JS
- Never print internal system errors directly
- Always validate user input on server as well
- Never bypass WebOS security / auth flows

---

## 14. Developer Notes

- Yeh file JavaScript ke liye **role-level skill map** hai
- Future me jab WebOS JS Library grow karegi, is file ko update karna zaroori hai
- Har new JS engine / pattern ko yahan short description ke saath add kiya jayega

---

# END OF DOCUMENT
