---
WorkflowId: PyApp_Update-Research
WorkflowName: PyApp Update Research
Type: Workflow_Plural
Domain: PyApp
Scope: Research current state of existing app for bounded updates
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
	Workflow():
		- WorkflowId: "PyApp_Update-Research"
		- Targets: "AppName"
		- Description: "REQUIRED"
Outputs:
	Artifacts:
		SaveTo: "/Aliens/.Alien/{Developer_Username}/Research/PyApp/{AppName}/"
		FileName: "PyApp_Update-Research.md"
Safety:
	NoDelete: true
	ProdWrite: "no"
	DbTouch: "not-required"
NonNegotiables:
	- "DescriptionRequired"
	- "Quality > Speed"
	- "Update scope must be bounded"
Requires:
	- Workflows/ACC/_README.md
Refs:
	- Workflows/ACC/_Refs/ACC.Paths.md
	- Workflows/ACC/_Refs/ACC.Safety.md
	- /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp_Update-Research", "{AppName}", "{Description}")`

Update research workflow.

Goal:
- Capture the *current state* of the existing app
- Convert the requested change intent into safe, bounded update scope

Hard rule (non-negotiable): **Quality > Speed**.
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
- still run update research with safe defaults
- record assumptions explicitly

---

# [3] Research Scope (Minimum Required)
Update research MUST capture:
- Current app inventory summary (screens/sections/components touched by description)
- Current routing/navigation assumptions (only what exists, no new IA unless requested)
- Current UI/CSS SSOT compliance gaps (if any) to constrain update plan
- Risk assessment (breaking changes, regression-prone areas)
- Backward compatibility constraints
- Explicit non-goals (scope boundaries)

---

# [4] Save Outputs
ResearchOut (markdown):
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp_Update-Research.md`

Optional supporting artifacts:
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp_Update-Sources.md`
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp_Update-OpenQuestions.md`

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
