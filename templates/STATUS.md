---
updated: "{{date}}"
iteration: "{{iteration-name}}"
design-version: "{{GDD vX.Y}}"
branch: "prototype/active"
---

# {{Project Name}} — Status

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update the `updated` field on every commit that touches this file.

  2. Section anchors — HTML comment immediately above every ## heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.

  This document is maintained exclusively by the SCRIBE. It is updated after
  every merge (PR or direct commit) to prototype/active. Keeping this file
  current is the SCRIBE's highest-priority ongoing task.
-->

This is the single source of truth for the current operational state of the project. Any agent starting a session should read this file first for a fast orientation before reading the full design artifacts.

---

<!-- section: iteration -->
## Current Iteration

| Field | Value |
|-------|-------|
| Iteration | {{iteration-name}} |
| Design version | {{GDD vX.Y}} |
| Started | {{date}} |
| Direction | {{Continue Iterating / Parallel Effort / Fresh Start}} |

---

<!-- section: milestones -->
## Milestone Progress

| Milestone | Name | Status | Backlog Items |
|-----------|------|--------|---------------|
| M1 | {{Name}} | ✅ Complete | {{BL-N, BL-N}} |
| M2 | {{Name}} | 🔄 In Progress | {{BL-N, BL-N}} |
| M3 | {{Name}} | ⬜ Not Started | {{BL-N, BL-N}} |

**Active milestone:** M{{N}} — {{Name}}

---

<!-- section: backlog-summary -->
## Backlog Summary

| Priority | Total | In Progress | Blocked |
|----------|-------|-------------|---------|
| P1 | {{N}} | {{N}} | {{N}} |
| P2 | {{N}} | {{N}} | {{N}} |
| P3 | {{N}} | {{N}} | {{N}} |
| P4 | {{N}} | {{N}} | {{N}} |

**Open BL-N items with active branches:**

| Item | Synopsis | Branch | Status |
|------|----------|--------|--------|
| BL-{{N}} | {{synopsis}} | `prototype/work-item/BL-{{N}}` | Open PR / In Progress |

**Pending DRIFT items:**

| Item | Type | Synopsis |
|------|------|----------|
| BL-{{N}} | DESIGN-DRIFT / CODE-DRIFT | {{synopsis}} |

---

<!-- section: open-prs -->
## Open Pull Requests

| PR | Branch | Synopsis | Author Role | Status |
|----|--------|----------|-------------|--------|
| #{{N}} | `prototype/work-item/BL-{{N}}` | {{synopsis}} | BUILDER | Awaiting Review |

---

<!-- section: rc-status -->
## Release Candidate Status

**Current assessment:** {{Not Yet Eligible / Under Evaluation / Eligible / Confirmed}}

**RC tag:** {{rc/vN.M or —}}

{{Brief rationale. What would need to change to advance to the next status?}}

---

<!-- section: findings-health -->
## Findings Health

| Category | Count |
|----------|-------|
| Confirm | {{N}} |
| Rework | {{N}} |
| Abandon | {{N}} |
| Discuss (open) | {{N}} |
| Technical Concerns [Pressing] | {{N}} |
| Ideas (pending) | {{N}} |

---

<!-- section: drift -->
## Known Drift

| Item | Type | Synopsis | Status |
|------|------|----------|--------|
| BL-{{N}} | DESIGN-DRIFT / CODE-DRIFT | {{synopsis}} | Open / Scribe-reviewing / In Progress |

Resolved drift is logged permanently in `design/KNOWN_DRIFT.md`.

---

<!-- section: notes -->
## SCRIBE Notes

{{Any context the SCRIBE wants to preserve for the next session: blockers, pending decisions awaiting user input, known risks, or anything that doesn't fit elsewhere.}}

---

<!--
  Update checklist for the SCRIBE after every merge:

  - [ ] Update the `updated` date in frontmatter
  - [ ] Update milestone status if a milestone completed
  - [ ] Update backlog summary counts
  - [ ] Remove merged work item from Open Pull Requests
  - [ ] Remove resolved DRIFT from pending DRIFT table
  - [ ] Update Findings Health if a finding was added or changed
  - [ ] Update RC Status if eligibility changed
  - [ ] Update SCRIBE Notes if relevant context exists
-->
