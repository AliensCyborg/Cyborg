---
WorkflowId: PyApp-Research
WorkflowName: PyApp Research
Type: Workflow_Plural
Domain: PyApp
Scope: Enterprise-grade research for a new PyApp
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp-Research"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Research/PyApp/{AppName}/"
    FileName: "PyApp-Research.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp-Research", "{AppName}", "{Description}")`

Enterprise-grade research workflow.

Hard rule (non-negotiable): **Quality > Speed**.
Time allowed: 10 hours, 10 days, more—acceptable.

Output MUST be stored in `.Alien/{Developer}/Research/...`.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [2] Inputs
- Target: `{AppName}`
- Description: `{Description}`

If Description is empty:
- still run research with safe defaults
- record assumptions explicitly

---

# [3] Research Scope (Minimum Required)
Research must cover (as applicable to the described app):
- Problem statement + success metrics
- User personas + journeys
- Competitive/benchmark analysis (patterns, UX conventions)
- Information architecture (screens, navigation)
- Data model (entities, relationships, IDs)
- API boundaries (auth, error model, pagination)
- Security baseline (auth/session, CSRF, rate limits, PII)
- Performance baseline (caching, pagination, payload size)
- SEO/share (meta/canonical, structured data if needed)
- Accessibility baseline
- Internationalization (currency, locale, time)
- Operational risks + monitoring (logs, alerts)

---

# [4] Save Outputs
ResearchOut (markdown):
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp-Research.md`

Optional supporting artifacts:
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/Sources.md`
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/OpenQuestions.md`

---

# [5] Quality Gate
ResearchOut MUST include:
- explicit assumptions
- explicit open questions
- explicit non-goals (scope boundaries)

---

# [6] Errors
- `WF_TARGET_INVALID`
- `WF_PERMISSION_DENIED`
