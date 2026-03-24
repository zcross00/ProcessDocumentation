---
version: "{{1.0.0}}"
status: "{{Active | Archived | On Hold}}"
updated: "{{date}}"
---

# {{Project Name}}

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update these fields on every version or status change.

  2. Section anchors — HTML comment immediately above every ## or ### heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.
-->

> **One-sentence summary of what this project is and why it exists.**

---

<!-- section: overview -->
## Overview

{{2–4 sentences. What problem does this project solve? What is it, broadly? Who is it for?}}

---

<!-- section: getting-started -->
## Getting Started

<!-- section: prerequisites -->
### Prerequisites

- {{Dependency or tool}} — {{version}} — {{install link or command}}
- {{Dependency or tool}} — {{version}} — {{install link or command}}

<!-- section: build -->
### Build

```bash
{{build command}}
```

<!-- section: run -->
### Run

```bash
{{run command}}
```

---

<!-- section: project-structure -->
## Project Structure

```
{{project root}/
  {{key directory}}/    # {{what it contains}}
  {{key directory}}/    # {{what it contains}}
  {{key file}}          # {{what it is}}
```

---

<!-- section: key-docs -->
## Key Documents

| Document | Purpose |
|----------|---------|
| [`ROADMAP.md`](ROADMAP.md) | Milestone breakdown and implementation status |
| [`design/{{DESIGN_DOCUMENT}}.md`](design/) | Full design / specification document |
| [`PROTOTYPE_FINDINGS.md`](PROTOTYPE_FINDINGS.md) | Prototype observations and findings log |

---

<!-- section: design-reference -->
## Design Reference

{{If this is a game or product with a design document, mention it here. One sentence describing what the design document covers and where to find it.}}

---

<!-- section: contributing -->
## Contributing

{{How to work on this project: branch strategy reference, any project-specific conventions, who to contact.}}
