---
name: backlog-entry
description: "BACKLOG ENTRY SKILL — Produce a correctly formatted BL-N backlog entry with all required fields. USE FOR: adding new items to design/BACKLOG.md (features, rework, tech debt, drift items, ideas converted to backlog entries); any role that needs to create a backlog entry. Enforces ID uniqueness and required field completeness. DO NOT USE FOR: updating or removing existing entries (edit directly); SCRIBE backlog sweeps (read backlog directly)."
---

# Backlog Entry

Use this skill whenever a new item must be added to `design/BACKLOG.md`. It enforces correct format and prevents ID collisions.

---

## Step 1 — Determine the Next ID

Open `design/BACKLOG.md`. Find the highest `BL-N` number currently present anywhere in the file — including in existing table rows, detail blocks, and comments referencing resolved items. The new item's ID is `(highest found) + 1`.

IDs are never reused. If the previous highest was `BL-19`, the new item is `BL-20` regardless of how many items were removed.

---

## Step 2 — Classify the Item

Choose exactly one Type and one Priority.

**Type:**

| Type | When to use |
|------|-------------|
| `Feature` | New capability or behavior described in the design doc |
| `Rework` | Existing behavior needs to change — mechanics work but harm gameplay or future work |
| `TechDebt` | Internal quality improvement with no user-visible behavior change |
| `Bug` | Defect — something is broken or incorrect |
| `Design` | A design document gap, ambiguity, or needed decision |
| `Deferred` | Not for this iteration; tracked for the future |
| `DESIGN-DRIFT` | Artifact inconsistency that conflicts with stated goals — not the code, but the docs |
| `CODE-DRIFT` | Production code diverges from what the design says it should do |

**Priority:**

| Priority | Meaning |
|----------|---------|
| `P1` | Must address next iteration — correctness-blocking or goal-critical |
| `P2` | Important; schedule soon |
| `P3` | Valuable but deferrable |
| `P4` | Low urgency or speculative |

---

## Step 3 — Write the Entry

Produce both a table row and a detail block. Both are required.

**Table row** (place in the correct priority section):
```
| BL-N | [Type] | [One-line synopsis] | [Source] |
```

- **Synopsis:** One sentence, imperative, describing what needs to happen. Example: `Add dirty-flag score caching to EraEngine`.
- **Source:** Where this item came from — a finding/TC tag (`TC-4`, `DN-M3-2`), `DRIFT`, `KEEPER sweep`, `SCRIBE review`, or a direct observation.

**Detail block** (inserted immediately after the table row, or grouped at the end of the priority section):
```
<!-- item: BL-N -->
**BL-N Detail:**  
[Describe what needs to happen, what prompted this, and any constraints or context that would help someone pick it up. Be specific — vague items stall. Include done-when criteria if not obvious.]  
**Roadmap-Ref:** [M1–M5, or Deferred]  
**Dependencies:** [BL-N ID(s) that must be merged first, or None]  
**Branch:** —
```

---

## Step 4 — Placement Rules

- Items go in the section matching their priority (`### P1`, `### P2`, etc.).
- Within a priority section, new items append to the end of the table and end of the detail blocks.
- Do not sort or renumber existing items.
- For `DESIGN-DRIFT` and `CODE-DRIFT` items: place at the appropriate priority level. P2 is a reasonable default unless the drift is correctness-blocking (P1) or cosmetic (P3).

---

## Step 5 — How to Land the Entry

| Role | How to commit |
|------|--------------|
| BUILDER | Add on the current work-item branch; it lands on `prototype/active` when the PR merges |
| SCRIBE | Direct commit to `prototype/active` (backlog updates are non-directional) |
| KEEPER | Add on the current `prototype/fix/` branch; it lands when the PR merges |

**Never** add a backlog entry via direct commit from a BUILDER or KEEPER role.

---

## Checklist Before Committing

- [ ] ID is one higher than the current maximum in the file
- [ ] Both table row and detail block are present
- [ ] Synopsis is one sentence, imperative
- [ ] Type and Priority are valid values from the tables above
- [ ] `Roadmap-Ref` is set (not blank)
- [ ] `Dependencies` is set to either specific BL-N IDs or `None`
- [ ] `Branch` is `—` (new items start unassigned)
- [ ] Source is traceable (TC tag, finding ID, or clear description)
