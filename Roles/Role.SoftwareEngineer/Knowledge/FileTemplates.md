# FileTemplates.md — Ultra-Detailed Version  
**Location:** `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Knowledge/FileTemplates.md`  
**Purpose:** This document defines *standard file templates* used across Aliens WebOS, SDK, Apps, Brand, CLI, and all internal development systems.*  
These templates ensure Cyborg always generates consistent, scalable, AliensStyle-compliant code.*

---

# 1. Purpose of File Templates
File templates allow:

- Zero deviation in structure  
- Zero mistakes in formatting  
- Maximum compatibility with WebOS + SDK  
- Fast file generation  
- Predictable engineering output  
- AI-safe auto-generation  

These templates act as the **baseline for every new file Cyborg creates**.

---

# 2. PHP Templates

---

## 2.1 PHP Module API Template
```
<?php

// [MODULE API] — {ModuleName}
// PATH: App/{AppName}/API/{ModuleName}/{FileName}.php

$Echo = []; // Stepwise debugging log

// [1] Input Validation
$Echo[] = "Validating Inputs";
// TODO: Add validation code

// [2] Query Preparation
$Echo[] = "Preparing DB Query";
// TODO: Add Row() call preparation

// [3] Row() Execution
$Echo[] = "Executing DB Query";
$Result = Row('{TableName}', [
    'id' => $ID,
]);

// [4] Response
$Echo[] = "Returning API Response";

return [
    'status' => 1,
    'data' => $Result,
    'echo' => $Echo
];
```

---

## 2.2 PHP Component Template
```
<?php

// [COMPONENT] — {ComponentName}
// PATH: App/{AppName}/Components/{ComponentName}.php

class {ComponentName}
{
  public function Render($Data = [])
  {
    $Echo[] = "Rendering Component";

    // TODO: Add logic here

    return $HTML;
  }
}
```

---

## 2.3 PHP Template File (HTML + CSS)
```
<!-- [TEMPLATE]: {TemplateName} -->

<div class="Template-{TemplateName}">
  <!-- Component HTML -->
</div>

<style>
.Template-{TemplateName} {
  /* Component visuals */
}
</style>
```

---

# 3. JavaScript Templates

---

## 3.1 WebOS JS API Registration Template
```
WebOS.API({
  name: "{APIName}",
  path: "/{App}/{Module}/{APIName}",
  method: "POST",
  handler: async function (Input) {

    WebOS.Log("Handling API: {APIName}");

    // TODO: Validate Input

    const Response = await WebOS.DB.Select("{Table}", {
      id: Input.id
    });

    return {
      status: 1,
      data: Response
    };
  }
});
```

---

## 3.2 WebOS JS Component Template
```
WebOS.Component({
  name: "{ComponentName}",
  template: `
    <div class="Component-{ComponentName}">
      <!-- HTML -->
    </div>
  `,
  mounted() {
    WebOS.Log("Mounted {ComponentName}");
  }
});
```

---

## 3.3 WebOS JS Table Template
```
WebOS.Table({
  name: "{TableName}",
  columns: [
    { key: "id", title: "ID" },
    { key: "name", title: "Name" }
  ],
  fetch: async (Params) => {
    return await WebOS.API.Call("{APIName}", Params);
  }
});
```

---

# 4. Brand Templates

---

## 4.1 Brand Layout Template
```
<!-- Brand Layout -->

<div class="BrandPage">
  <header class="BrandHeader">
    <!-- NAV -->
  </header>

  <main class="BrandContent">
    {Content}
  </main>

  <footer class="BrandFooter">
    <!-- Footer -->
  </footer>
</div>
```

---

## 4.2 Brand CSS Template
```
.Brand-{ComponentName} {
  width: 100%;
  padding: 16px;
  border-radius: 12px;
  background: var(--brand-surface);
  box-shadow: var(--shadow-1);
}
```

---

# 5. SDK Templates

---

## 5.1 SDK Module Template
```
<?php

// [SDK MODULE]: {ModuleName}

class {ModuleName}
{
  public static function Init()
  {
    // Initialization logic
  }

  public static function Run($Input)
  {
    return [
      'status' => 1,
      'message' => "{ModuleName} executed"
    ];
  }
}
```

---

## 5.2 SDK API Template
```
<?php

// [SDK API]: {APIName}

function {APIName}($Input)
{
  $Echo[] = "Executing SDK API {APIName}";

  // TODO: Add logic

  return [
    'status' => 1,
    'message' => "{APIName} complete",
    'echo' => $Echo
  ];
}
```

---

# 6. CLI Templates

---

## 6.1 CLI Command Template
```
#!/usr/bin/env bash
# COMMAND: {CommandName}

echo "[1] Initializing {CommandName}"

# TODO: Steps

echo "[X] Completed {CommandName}"
```

---

# 7. Database Templates

---

## 7.1 SQL Table Template
```
CREATE TABLE `{TableName}` (
  `id` INT AUTO_INCREMENT PRIMARY KEY,
  `uid` VARCHAR(32),
  `status` INT DEFAULT 1,
  `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

# 8. Template Rules — THE GOLDEN LAWS

1. Must follow **AliensStyle**  
2. Every template must include `{Placeholders}`  
3. Never guess table names  
4. Always add stepwise debugging markers  
5. Must work inside WebOS + SDK architecture  
6. Must keep logic modular  
7. Must use PascalCase naming  
8. Must never break existing WebOS patterns  

---

# 9. Summary

These templates ensure:

- Consistent system-level architecture  
- Developer-ready output  
- Easy scaling of apps/modules  
- Perfect compatibility with WebOS + SDK  
- Zero errors during generation  

Cyborg must always begin file generation using these base templates.

---

End of File  
`FileTemplates.md`
