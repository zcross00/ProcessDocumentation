---
name: drift-logging
description: "DRIFT LOGGING SKILL — Classify a drift situation and produce the correct BACKLOG.md entry. USE FOR: any role that has identified an inconsistency between code, design, or goals; deciding whether something is CODE-DRIFT or DESIGN-DRIFT (or both); writing a valid drift backlog entry with the correct cross-references. DO NOT USE FOR: resolving drift (DESIGN-DRIFT resolution is SCRIBE work; CODE-DRIFT resolution is BUILDER work); logging a finding that is not a goal conflict (use findings-entry skill instead)."
---

# Drift Logging

Use when you have identified an inconsistency that conflicts with the project's stated goals. This skill helps classify the drift type and produce the correct backlog entry.

---

## Step 1 — Confirm It Is Drift

Drift is a specific category. Before logging, confirm the situation meets the definition:

> **Drift** = an inconsistency that conflicts with a stated goal — not just a quality issue, a style preference, or an improvement opportunity.

Ask: *Does this inconsistency conflict with a specific statement in `design/GOALS.md` or `design/GAME_DESIGN_DOCUMENT.md`?*

- If **yes** → this is drift. Continue.
- If **no, it's a quality issue** → use the `backlog-entry` skill with Type `TechDebt` or `Bug`.
- If **no, it's an improvement idea** → use the `findings-entry` skill with category `[Idea]`.

---

## Step 2 — Classify the Type

**DESIGN-DRIFT** — The documents are wrong.
- A design doc section contradicts a goal.
- A backlog item, if implemented as described, would undermine a design pillar.
- A roadmap milestone no longer reflects the current design direction.
- Documentation is internally inconsistent.

**CODE-DRIFT** — The code is wrong.
- Production code does not match what the design document says it should do.
- Code behavior conflicts directly with a stated goal, even if the design doc hasn't caught up.

**Both** — If a CODE-DRIFT also reveals that the design document needs correcting, log both. Cross-reference them in each entry's Detail.

---

## Step 3 — Prepare the Entry

Use the `backlog-entry` skill to produce the formatted entry. Apply these specifics for drift:

**Type field:** `DESIGN-DRIFT` or `CODE-DRIFT`

**Priority guidance:**

| Situation | Priority |
|-----------|---------|
| Drift directly breaks a running system or produces incorrect user-visible behavior | P1 |
| Drift is a significant goal misalignment but nothing is broken | P2 |
| Drift is minor or cosmetic — inconsistency is real but has no immediate impact | P3 |

**Detail must include:**
1. What the inconsistency is (be specific — name the file, section, method, or behavior involved).
2. What goal or design statement it conflicts with (cite the goal number or GDD section).
3. What the correct state should be.
4. For CODE-DRIFT: what the code currently does vs. what the design says it should do.
5. For companion entries (both types): cross-reference by ID: "See companion DESIGN-DRIFT BL-{N}" / "See companion CODE-DRIFT BL-{N}".

**Source field:** `DRIFT`

**Roadmap-Ref:** If the drift is in scope for the current iteration milestone, reference it. Otherwise: `Deferred`.

---

## Step 4 — Land the Entry

| Role | How to commit |
|------|--------------|
| BUILDER | Add on the current work-item branch. Lands on `prototype/active` when the PR merges. |
| SCRIBE | Direct commit to `prototype/active`. Backlog entries are non-directional. |
| KEEPER | On the current `prototype/fix/` branch. Lands when the PR merges. |

**Never** add drift entries directly to `prototype/active` as a BUILDER or KEEPER.

---

## Step 5 — Notify the Relevant Role

Drift entries don't resolve themselves. After landing the entry, ensure the right role is aware:

| Drift type | Who resolves | How they learn |
|-----------|-------------|---------------|
| DESIGN-DRIFT | SCRIBE | SCRIBE reads BACKLOG.md on every session — the entry will be picked up |
| CODE-DRIFT | BUILDER | Appears in the backlog as a normal P-priority item; next BUILDER session will see it |

If both types are present: the SCRIBE DESIGN-DRIFT PR should ideally merge first (or concurrently) so the correct design direction is established before the BUILDER implements the code fix.

---

## Resolution Tracking

After a drift item is resolved:
- DESIGN-DRIFT: SCRIBE adds the entry to `design/KNOWN_DRIFT.md` as part of the resolution PR.
- CODE-DRIFT: BUILDER adds the entry to `design/KNOWN_DRIFT.md` as part of the work-item PR merge commit.

`design/KNOWN_DRIFT.md` is a permanent, append-only log. It is never cleared.

---

## Example Entries

**DESIGN-DRIFT example:**
```
| BL-20 | DESIGN-DRIFT | GDD §6.3 describes familiarity decay as linear; decay is actually logarithmic | DRIFT |

<!-- item: BL-20 -->
**BL-20 Detail:**  
GDD §6.3 states familiarity decays at a fixed rate per day. The decay curve in the implementation is logarithmic (observed in BL-11 score cache work). The design document needs to be updated to reflect the actual implemented mechanic, or a decision made about which curve is correct.  
Conflicts with: Goal 3 (Relationships as Gameplay) — the intended relationship weight curve affects player strategy.  
Correct state: GDD §6.3 updated to describe the logarithmic curve, or a backlog item opened to change the implementation to match the linear design.  
**Roadmap-Ref:** Deferred  
**Dependencies:** None  
**Branch:** —
```

**CODE-DRIFT example:**
```
| BL-21 | CODE-DRIFT | WorldFactory seeds all NPCs to the same start area; GDD §4.1 describes area-scoped seeding | DRIFT |

<!-- item: BL-21 -->
**BL-21 Detail:**  
GDD §4.1 specifies that knowledge seeding should be scoped to the character's home area. The current WorldFactory implementation seeds PERSON journal entries based on the global character list rather than the character's locationId. At current scale (single area) the result is identical, but this will diverge at multi-area scale.  
Conflicts with: Goal 6 (Knowledge as Sight) — seeding global knowledge violates the stated design.  
Correct state: WorldFactory.seedJournals() uses homeArea.getPresentCharacterIds() for PERSON entries, not world.getCharacters().  
**Roadmap-Ref:** M4  
**Dependencies:** None  
**Branch:** —
```
