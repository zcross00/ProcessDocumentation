---
version: "{{X.Y}}"
updated: "{{date}}"
status: "{{Draft | Review | Stable}}"
---

# {{Project Name}} — Design Document

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update these fields on every version or status change.

  2. Section anchors — HTML comment immediately above every ## or ### heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.

  3. Prototype note IDs — HTML comment immediately above each versioned blockquote.
     Pattern: <!-- note: vX.Y-sectionN-topic -->
     Add one before every > **vX.Y — ...** observation block.
-->

---

<!-- section: toc -->
## Table of Contents

1. [Overview](#1-overview)
2. [Core Pillars](#2-core-pillars)
3. [Concept Summary](#3-concept-summary)
4. [Experience Goals](#4-experience-goals)
5. [Core Mechanics](#5-core-mechanics)
6. [Systems](#6-systems)
7. [Scope & Configuration](#7-scope--configuration)
8. [Technical Overview](#8-technical-overview)
9. [Out of Scope / Future Considerations](#9-out-of-scope--future-considerations)

---

<!-- section: 1 -->
## 1. Overview

| Field | Value |
|-------|-------|
| **Title** | {{project name}} |
| **Type** | {{e.g. Game / Tool / Service / Library}} |
| **Platform** | {{PC / Web / Mobile / etc.}} |
| **Audience** | {{Who will use this, and what do they already know?}} |
| **Stack** | {{Languages, frameworks, build tools}} |

> **Elevator Pitch:**
> {{1–3 sentences. What is this? Why would someone want it? What makes it different?}}

---

<!-- section: 2 -->
## 2. Core Pillars

Pillars are the non-negotiable design principles. Every decision should be measured against these. When in doubt, return here.

- **Pillar 1: {{Name}}** — {{One sentence: what this means and why it matters.}}
- **Pillar 2: {{Name}}** — {{One sentence.}}
- **Pillar 3: {{Name}}** — {{One sentence.}}

*Add or remove pillars as needed. Fewer strong pillars are better than many weak ones.*

---

<!-- section: 3 -->
## 3. Concept Summary

<!-- section: 3.1 -->
### Core Loop

```
{{Step 1}} → {{Step 2}} → {{Step 3}} → {{Step 4}}
        ↑                                      |
        └──────────────────────────────────────┘
```

{{2–4 sentences: describe the fundamental unit of interaction and how it cycles.}}

<!-- section: 3.2 -->
### Progression Arc

- **{{Early phase name}}** — {{What the player/user does and experiences early on.}}
- **{{Mid phase name}}** — {{What opens up; what challenges escalate.}}
- **{{Late phase name}}** — {{The mature state; how it ends or continues.}}

---

<!-- section: 4 -->
## 4. Experience Goals

What should the user *feel* while using or playing this?

- {{Emotion or sensation}} — {{Brief explanation. E.g. "Genuine tension — decisions should feel meaningful because failure is real."}}
- {{Emotion or sensation}} — {{Brief explanation.}}
- {{Emotion or sensation}} — {{Brief explanation.}}

---

<!-- section: 5 -->
## 5. Core Mechanics

<!-- section: 5.1 -->
### {{Mechanic 1 Name}}

{{Describe the mechanic: what it is, how it works, and why it exists. Ground rules, constraints, and key interactions.}}

*Design note: {{Any unresolved questions, known limitations, or intent for future iterations.}}*

---

<!-- section: 5.2 -->
### {{Mechanic 2 Name}}

{{Repeat the pattern above for each major mechanic.}}

---

<!-- section: 6 -->
## 6. Systems

<!-- section: 6.1 -->
### {{System Name}} (e.g. Progression, Economy, Social, Combat)

{{Describe the system and how it interrelates with mechanics described in Section 5. What it tracks, what it drives, what the failure and success states are.}}

---

<!-- section: 7 -->
## 7. Scope & Configuration

<!-- section: 7.1 -->
### In Scope for {{Version / Phase}}

- {{Feature or system explicitly included}}
- {{Feature or system explicitly included}}

<!-- section: 7.2 -->
### Explicitly Out of Scope for {{Version / Phase}}

- {{Feature or system deliberately excluded, with brief note on why or when it might return}}

<!-- section: 7.3 -->
### Configuration Options

{{If the project has user-facing configuration at startup or setup, describe the options here.}}

| Option | Default | Effect |
|--------|---------|--------|
| {{option name}} | {{default value}} | {{what changing it does}} |

---

<!-- section: 8 -->
## 8. Technical Overview

<!-- section: 8.1 -->
### Architecture

{{Brief description of the major layers or components and how they relate. A diagram or list is fine.}}

```
{{Layer or component}} — {{what it contains / does}}
{{Layer or component}} — {{what it contains / does}}
{{Layer or component}} — {{what it contains / does}}
```

<!-- section: 8.2 -->
### Key Technical Decisions

| Decision | Rationale |
|----------|-----------|
| {{Technology or pattern chosen}} | {{Why this over alternatives}} |
| {{Technology or pattern chosen}} | {{Why this over alternatives}} |

<!-- section: 8.3 -->
### Known Technical Constraints

- {{Constraint and its implication}}

---

<!-- section: 9 -->
## 9. Out of Scope / Future Considerations

These are ideas or systems that have been explicitly deferred. They are recorded here so they don't get lost, not because they are planned.

| Topic | Notes |
|-------|-------|
| {{Deferred feature}} | {{Why deferred. What would need to be true to revisit it.}} |
| {{Deferred feature}} | {{Why deferred.}} |

---

*This document describes intent. The prototype roadmap and findings log describe reality. When they conflict, the findings log wins — and this document needs updating.*
