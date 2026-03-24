---
iteration: "{{iteration-name}}"
design-version: "{{GDD vX.Y}}"
previous: "{{previous-iteration (N Confirm, M Rework, K Abandon)}}"
direction: "{{Continue Iterating | Parallel Effort | Fresh Start}}"
branch: "{{branch-name}}"
---

# {{Project Name}} — Roadmap

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update these fields at the start of each iteration.

  2. Section anchors — HTML comment immediately above every ## or ### heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.
-->

Legend: ✅ Done &nbsp;|&nbsp; 🔄 Partial &nbsp;|&nbsp; ⬜ Not started

---

<!-- section: m1 -->
## Milestone 1 — {{Name}} ✅ *Complete*

{{1–2 sentences describing what this milestone delivers and why it comes first.}}

**Backlog:** {{BL-N, BL-N, …}} — All items listed here must exist as `BL-N` entries in `design/BACKLOG.md`.

| # | Task | Status | Key Files |
|---|------|--------|-----------|
| 1.1 | **{{Task name.}}** {{One-sentence description of what to build and how.}} | ✅ | `{{file path}}` |
| 1.2 | **{{Task name.}}** {{description}} | ✅ | `{{file path}}` |
| 1.3 | **{{Task name.}}** {{description}} | 🔄 | `{{file path}}` |

**Done when:** {{The exact condition that must be true for this milestone to be marked complete. Should be evaluable yes/no.}}

---

<!-- section: m2 -->
## Milestone 2 — {{Name}}

{{1–2 sentences describing what this milestone delivers and why it follows Milestone 1.}}

**Backlog:** {{BL-N, BL-N, …}} — All items listed here must exist as `BL-N` entries in `design/BACKLOG.md`.

| # | Task | Key Files |
|---|------|-----------|
| 2.1 | **{{Task name.}}** {{description}} | `{{file path}}` |
| 2.2 | **{{Task name.}}** {{description}} | `{{file path}}` |

**Done when:** {{Done-when condition.}}

---

<!--
  Repeat the pattern above for each milestone.
  
  Guidelines for milestones:
  - Each milestone should deliver a testable slice of the product, not scaffolding.
  - Earlier milestones establish foundations (domain model, core loop, data persistence).
  - Later milestones build depth (AI, polish, edge cases).
  - Every milestone has a clear Done-when condition.
  - Every item listed in the Backlog line of a milestone must have a corresponding BL-N entry in design/BACKLOG.md before implementation of that milestone begins. Roadmap milestone placement determines backlog priority (M1 → P1, M2 → P2, etc.).
  - Tasks reference the files they primarily affect — this makes finding related work fast.
-->

---

<!-- section: status-summary -->
## Quick Reference: Status Summary

| System | Status | Milestone |
|--------|--------|-----------|
| {{System or feature name}} | ✅ Done | {{milestone number}} |
| {{System or feature name}} | 🔄 Partial | {{milestone number}} |
| {{System or feature name}} | ⬜ Pending | {{milestone number}} |
