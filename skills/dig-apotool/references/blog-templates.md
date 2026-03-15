# Blog Templates for `/dig-apotool`

## Frontmatter: File + hint mode (deep-dive)

```yaml
---
title: '<ClassName>: <functionName> — Deep Dive'
date: '<YYYY-MM-DD>'         # use today's actual date
tags: ['<module>', '<kebab-function-name>', 'business-logic', 'cakephp']
draft: false
summary: '<2-sentence plain-English summary of what this code does and why it matters>'
---
```

## Frontmatter: File-only mode (model overview)

```yaml
---
title: '<ClassName> — Model Overview'
date: '<YYYY-MM-DD>'         # use today's actual date
tags: ['<module>', 'model-overview', 'business-logic', 'cakephp']
draft: false
summary: '<2-sentence plain-English summary of what this model represents and its role in the system>'
---
```

---

## Content Structure: Deep-dive (File + hint mode)

### TL;DR
2–3 sentence plain-language summary for quick recall.

### Business Layer
- What real-world event triggers this function?
- What business rules does it enforce?
- What changes in the clinic/business workflow when this runs?

### Technical Layer
- Method signature and parameters (with types where inferrable from code)
- Step-by-step walkthrough of the code logic
- DB tables touched (reads and writes)
- Side effects: notifications triggered, logs written, billing updated, etc.

### One Level Deep
Scan the function body for `$this->ModelName->method()` patterns only.
Other call patterns (`$this->method()`, `ClassRegistry::init(...)`, `requestAction(...)`)
are out of scope for one-level-deep analysis.

For up to 3 significant `$this->ModelName->method()` calls:
- Read the called method's source and write a 3–5 sentence inline summary.
- If the called method's source cannot be located within the two repos, write:
  "Source not available in this repo (likely CakePHP core or external library)."

### Edge Cases & Gotchas
- Null/empty checks
- Status enums and their meaning
- Important conditional branches

### Related Reading
Links to other MDX posts already present in the same module subdirectory.
If no sibling posts exist yet, write: "No related posts yet in this module."

---

## Content Structure: Model Overview (File-only mode)

### TL;DR
What is this model's business role in 2–3 sentences?

### Domain Purpose
What real-world entity or process does this model represent?

### Public API Summary
Markdown table of all public methods with a one-line description each.

| Method | Description |
|--------|-------------|
| `methodName()` | What it does |

### Key Relationships
`belongsTo`, `hasMany`, `hasOne` associations and their business meaning.

### Edge Cases & Gotchas
Anything surprising: complex callbacks, unusual validation, heavy conditional logic.

### Related Reading
Links to deep-dive posts already present in this module subdirectory.
If none exist yet, write: "No related posts yet in this module."
