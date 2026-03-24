---
project: "{{Project Name}}"
---

# {{Project Name}} — Known Drift Log

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable project metadata.

  2. Section anchors — HTML comment immediately above every ## heading.
     Pattern: <!-- section: name -->

  This document is a permanent, append-only running log. Entries are never
  removed. The growth rate and pattern of this log is an intentional health
  signal: a steadily growing log may indicate goals are under-defined, design
  is ahead of implementation understanding, or scope is expanding faster than
  the design can absorb.
-->

This is the permanent record of all resolved drift — design inconsistencies and code-design gaps that were identified, tracked, and closed. It is never cleared between iterations.

**Entry rules:**
- SCRIBE adds resolved `DESIGN-DRIFT` entries on the resolution commit.
- BUILDER adds resolved `CODE-DRIFT` entries on the PR merge commit that closes the work item.
- Entries are append-only. Do not modify or remove existing entries.
- Reference the original `BL-N` ID and (if applicable) the `DESIGN-DRIFT` companion item.

**Reading this log:** A short log indicates a well-aligned project. Clustering of entries in a short time window may indicate a sprint of exploratory work, a design gap being discovered, or goals that weren't precise enough. Large clusters of `DESIGN-DRIFT` entries suggest documentation discipline issues. Large clusters of `CODE-DRIFT` entries suggest builders are drifting from the design during implementation.

---

<!-- section: design-drift -->
## DESIGN-DRIFT — Resolved

| ID | Synopsis | Root Cause | Resolved By | Date |
|----|----------|------------|-------------|------|
| BL-{{N}} | {{What the inconsistency was}} | {{Why it happened — under-defined goal, stale doc, missed update}} | SCRIBE / docs commit | {{date}} |

---

<!-- section: code-drift -->
## CODE-DRIFT — Resolved

| ID | Synopsis | Root Cause | Design-Drift Companion | Resolved By | Date |
|----|----------|------------|----------------------|-------------|------|
| BL-{{N}} | {{What the code was doing vs. what the design said}} | {{Why it happened}} | BL-{{N}} / — | BUILDER / PR merge | {{date}} |

---

<!--
  Guidelines:

  - One row per resolved BL-N DRIFT item. Reference the BL-N even after it has
    been removed from BACKLOG.md — this log is the permanent trace.
  - Root Cause should be honest. Was the goal unclear? Was the design outdated?
    Did implementation outpace documentation? Honest root causes improve future
    process.
  - If a CODE-DRIFT item also had a companion DESIGN-DRIFT item, reference both
    IDs so the full picture is traceable.
  - This file is committed: by the SCRIBE (DESIGN-DRIFT resolutions) and by the
    BUILDER (CODE-DRIFT resolutions, as part of the PR merge commit).
-->
