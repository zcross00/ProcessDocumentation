# {{Project Name}} — Backlog

**Last updated:** {{date}}  
**Active iteration:** {{iteration name, e.g. prototype-dawn}}

This is the living work backlog. It tracks every identified item that has not been resolved — features, rework, technical concerns, design gaps, and deferred decisions.

Git history is the log of resolved items. When an item is completed, remove it from here and reference the backlog ID in the commit message (e.g., `Closes BL-4`). The backlog contains only what's still pending.

---

## How to Read This

Each item has:
- **ID** — Sequential `BL-N`, never reused. If an item is removed, the number is retired, not recycled.
- **Priority** — `P1` (must address next iteration), `P2` (important, schedule soon), `P3` (valuable but deferrable), `P4` (low urgency or speculative).
- **Type** — `Feature`, `Rework`, `TechDebt`, `Bug`, `Design`, `Deferred`.
- **Synopsis** — One-line description.
- **Detail** — Any helpful context: what prompted this item, which finding or TC it came from, known constraints, related items.
- **Source** — Where the item came from: a finding ID (e.g. `AW-M4-3`), a TC tag (e.g. `TC-1`), a direct observation, or `Next Iteration Direction`.

---

## Backlog

### P1 — Must Address Next Iteration

| ID | Type | Synopsis | Source |
|----|------|----------|--------|
| BL-1 | TechDebt | {{One-line description}} | {{TC-N / finding ID / observation}} |

**BL-1 Detail:**  
{{Describe what needs to happen, what prompted this, and any constraints or context that would help someone pick it up.}}

---

### P2 — Important, Schedule Soon

| ID | Type | Synopsis | Source |
|----|------|----------|--------|
| BL-2 | Rework | {{One-line description}} | {{Source}} |

**BL-2 Detail:**  
{{Detail}}

---

### P3 — Valuable but Deferrable

| ID | Type | Synopsis | Source |
|----|------|----------|--------|
| BL-3 | Feature | {{One-line description}} | {{Source}} |

**BL-3 Detail:**  
{{Detail}}

---

### P4 — Low Urgency / Speculative

| ID | Type | Synopsis | Source |
|----|------|----------|--------|
| BL-4 | Design | {{One-line description}} | {{Source}} |

**BL-4 Detail:**  
{{Detail}}

---

<!--
  Guidelines:

  - Add items as soon as they're identified. Don't wait until end of iteration.
  - One item per entry. Don't bundle unrelated work under one ID.
  - Priority P1 items should appear in the next ROADMAP. If they don't, explain why.
  - When an item is resolved: remove it from this file and reference BL-N in the commit message.
  - When an iteration ends, do a backlog sweep: re-prioritize items based on findings, add new items from Rework findings, TC [Pressing] concerns, and the Next Iteration Direction section.
  - IDs are permanent identifiers — never renumber after assignment.
-->
