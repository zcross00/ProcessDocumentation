---
name: scribe-duties
description: "SCRIBE DUTIES SKILL — Execute the full SCRIBE responsibility loop in the correct order. USE FOR: starting a SCRIBE session after role transition or merge; running the complete SCRIBE checklist to confirm nothing was missed; transitioning from BUILDER when the backlog is exhausted. Covers STATUS update, Ideas adjudication, DRIFT triage, RC evaluation, and new iteration gate. DO NOT USE FOR: mid-session STATUS updates (commit directly); implementing backlog items (that is BUILDER work)."
---

# SCRIBE Duties

The SCRIBE session loops through responsibilities in a defined order. Work through each section completely before advancing to the next. Every directional decision travels through a SCRIBE PR — never a direct commit.

---

## Before Starting

1. Pull `prototype/active`: `git pull origin prototype/active`
2. Confirm `design/STATUS.md` exists. If it does not, create it from `templates/STATUS.md` before doing anything else.
3. Confirm `design/KNOWN_DRIFT.md` exists. If it does not, create it from `templates/KNOWN_DRIFT.md` before proceeding.
4. Read: STATUS.md → GOALS.md → design doc → ROADMAP.md → BACKLOG.md → PROTOTYPE_FINDINGS.md (in that order).

---

## Duty 1 — Update STATUS.md

**Trigger:** Always — this is the first thing a SCRIBE does after any merge or at session start.

- Update every section that has changed since the last STATUS update: iteration, milestone progress, open PRs, backlog summary, DRIFT status, findings health counts, RC status.
- Update the `updated` field in the YAML frontmatter to today's date.
- Commit **directly to `prototype/active`** (no PR needed — STATUS is informational, not directional).

```bash
git add design/STATUS.md
git commit -m "docs: update STATUS.md — [brief description of what changed]"
git push origin prototype/active
```

**Checklist:**
- [ ] `updated` date is current
- [ ] Milestone progress table is accurate
- [ ] Backlog summary counts are correct
- [ ] Open PRs section is accurate (run `gh pr list --state open`)
- [ ] Findings health counts match PROTOTYPE_FINDINGS.md
- [ ] RC Status matches current assessment
- [ ] Branch field for each open BL-N item reflects whether a remote branch exists (`git branch -r`)

---

## Duty 2 — Ideas Adjudication

**Trigger:** When `design/PROTOTYPE_FINDINGS.md` contains `[Idea]` entries not yet disposed.

For each `[Idea]` entry, decide one of:
- **Convert to backlog entry:** Create a new `BL-N` item. Most ideas are P3 or P4 unless context justifies P2.
- **Apply as documentation/standards change:** Include the change in a SCRIBE PR.
- **Discard:** Include the removal from the findings log in a SCRIBE PR with reasoning.

Deliver all idea dispositions in a single SCRIBE PR. The PR description must state the reasoning for each disposition. Ideas are not removed from the findings log until the SCRIBE PR merges.

**Branch:** `prototype/scribe/ideas-{iteration-short}` or `prototype/scribe/ideas-batch-N`

---

## Duty 3 — DRIFT Triage

**Trigger:** When `design/BACKLOG.md` contains unresolved `DESIGN-DRIFT` or `CODE-DRIFT` entries.

For each DRIFT item:

**DESIGN-DRIFT** (SCRIBE owns resolution):
1. Understand what the inconsistency is and what goal it conflicts with.
2. Prepare the proposed resolution: updated design docs, goals, or other artifacts.
3. Open a SCRIBE PR (`prototype/scribe/drift-{BL-N}`). PR description must state: what the drift was, why the resolution is correct, what changed.
4. Include the resolved entry in `design/KNOWN_DRIFT.md` as part of the same PR.
5. Wait for user to merge (approval) or comment (redirect).

**CODE-DRIFT** (BUILDER implements; SCRIBE refines):
- The SCRIBE does not implement CODE-DRIFT.
- Review the entry for clarity and completeness. If vague, sharpen the Detail and adjust priority.
- If a CODE-DRIFT has an accompanying DESIGN-DRIFT, ensure the DESIGN-DRIFT SCRIBE PR is prepared first.

---

## Duty 4 — RC Eligibility Assessment

**Trigger:** After every resolution pass; after M5 (or the final iteration milestone) completes.

Evaluate against the four RC criteria in `design/PROTOTYPE_FINDINGS.md`:

| Criterion | Check |
|-----------|-------|
| Confirmed findings substantially outnumber Rework/Abandon | Count from findings log |
| No Rework/Abandon undermines a core goal | Read each Rework/Abandon against GOALS.md |
| All Discuss findings are resolved or explicitly scoped out | Review Discuss section |
| Product is playable/usable end-to-end without dead ends | Based on last test session / integration run |

If RC status changes (either direction): open a SCRIBE PR (`prototype/scribe/rc-{version}-evaluation`). PR description is the formal recommendation: current assessment, rationale, proposed next step.

After the RC PR merges: apply the `rc/v{N.M}` tag:
```bash
git tag -a rc/v{N.M} -m "RC evaluation: {one-line summary}"
git push origin rc/v{N.M}
```

Update `design/STATUS.md` immediately after tagging.

---

## Duty 5 — Backlog Maintenance

**Trigger:** Always — review the backlog on every SCRIBE session.

- Sharpen vague item details.
- Adjust priorities if iteration findings have changed the picture.
- Add missing Dependencies fields.
- Add new items surfaced by recent findings that haven't been captured yet.
- If an item was resolved without a formal `Closes BL-N` commit (e.g., resolved by another means), remove it now.

Backlog updates that are purely organizational (not directional) can be committed directly. If the update changes the scope or priority of a significant item, include it in a SCRIBE PR so the change is visible.

---

## Duty 6 — New Iteration Gate

**Trigger:** All milestones complete; RC eligible; user has indicated they want to proceed to the next iteration.

Prepare a single SCRIBE PR containing all of the following:
- Tag the final commit of the completed iteration: `git tag -a iteration/{name} -m "{iteration summary}"`
- Clear `design/PROTOTYPE_FINDINGS.md` (reset to empty template state for the new iteration; update the YAML frontmatter with the new iteration name)
- Update `design/BACKLOG.md` — sweep and re-prioritize; retire resolved items
- Write the new `design/ROADMAP.md` — milestones for the next iteration, each with a `BL-N` entry
- Update `design/STATUS.md` to reflect the new iteration starting state

The PR description is the SCRIBE's recommendation: summary of the completed iteration, why the next iteration scope makes sense, and the starting conditions. Merging the PR is the user's go-ahead. Never advance to a new iteration via direct commit.

After the PR merges: push the iteration tag.

---

## SCRIBE Does Not Do

- Does not implement backlog items or write production code.
- Does not resolve Ideas unilaterally — all dispositions require a PR merge.
- Does not update the roadmap mid-iteration.
- Does not resolve CODE-DRIFT items (that is BUILDER work).
- Does not apply RC tags before the corresponding SCRIBE PR merges.

---

## Exhaustion Condition

The SCRIBE is exhausted when:
- STATUS.md is current
- No pending undisposed Ideas in the findings log
- No unresolved DESIGN-DRIFT items
- RC assessment is current
- No new iteration gate is open

When exhausted → transition to **KEEPER**.
