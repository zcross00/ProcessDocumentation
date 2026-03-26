---
name: agent-orientation
description: "ORIENTATION SKILL — Run at the start of every agent session. Reads project artifacts in the correct order, determines the active role per the waterfall, and produces a session brief. USE FOR: starting a new session; determining which role to assume; fast orientation after a gap; confirming project state before picking up work. DO NOT USE FOR: mid-task context checks (read files directly); orientation when the user has explicitly assigned a role (skip waterfall step)."
---

# Agent Orientation

Use this skill at the beginning of every session before doing any work. It produces a session brief and confirms the active role.

---

## Step 1 — Pull Current State

```bash
git fetch --prune
git checkout prototype/active
git pull origin prototype/active
```

Confirm you are on `prototype/active` and it is up to date before reading any design artifacts.

---

## Step 2 — Read Artifacts in Order

Read these files in sequence. Each one narrows context established by the previous.

| Order | File | Purpose |
|-------|------|---------|
| 1 | `design/STATUS.md` | Fastest orientation. Current iteration, milestone progress, open PRs, backlog summary, RC status. Start here. |
| 2 | `design/GOALS.md` | What success looks like. Read the Goal statements. Understand what the project is optimizing for. |
| 3 | `design/GAME_DESIGN_DOCUMENT.md` (or equivalent) | Current design version and the systems in scope for this iteration. |
| 4 | `design/ROADMAP.md` | Which milestones are planned, which are complete, and current priorities. |
| 5 | `design/BACKLOG.md` | All open items: priorities, dependencies, branch status. Identify what is unblocked and unassigned. |
| 6 | `design/PROTOTYPE_FINDINGS.md` | Current iteration findings. Note any open Discuss entries, pending Ideas, or Pressing Technical Concerns. |

If `design/STATUS.md` does not exist: you are likely starting fresh. Create it from `templates/STATUS.md` before proceeding.

---

## Step 3 — Determine Active Role

Apply the waterfall in order. Stop at the first condition that applies.

1. **Explicit assignment:** If the user has said "you are a SCRIBE", "act as KEEPER", etc. — use that role. Skip all other conditions.
2. **Default:** Begin as **BUILDER** unless there is a reason to advance.
3. **Advance to SCRIBE** if: the backlog has no unassigned, unblocked items remaining.
4. **Advance to KEEPER** if: SCRIBE tasks are also exhausted (STATUS is current, no pending Ideas, no open DRIFT, no new iteration gate open).

Refer to `process/roles.md` for the full definition of each role's responsibilities and exhaustion conditions.

---

## Step 4 — Session Brief

After completing Steps 1–3, output a session brief with this structure:

```
ROLE:        [BUILDER | SCRIBE | KEEPER]

ITERATION:   [name]
DESIGN:      [GDD version]
MILESTONE:   [active milestone name and number] — [status]

BACKLOG:
  Unblocked and unassigned: [list BL-N IDs, or "none"]
  Blocked: [list BL-N IDs and what they're blocked on, or "none"]
  In progress (remote branches exist): [list BL-N IDs, or "none"]

OPEN PRs:    [list, or "none"]
FINDINGS:    [Confirm count] Confirm / [Rework] Rework / [Abandon] Abandon / [Discuss open] Discuss
IDEAS PENDING: [count, or "none"]
DRIFT ITEMS: [count, or "none"]
RC STATUS:   [Not Yet Eligible | Eligible — rc/vN.M | Released]

NEXT ACTION: [one sentence describing the first thing to do this session]
```

---

## Step 5 — Proceed

After producing the brief, begin the first action without waiting for direction. The brief is informational — it does not require user confirmation before proceeding unless the role assignment is ambiguous.
