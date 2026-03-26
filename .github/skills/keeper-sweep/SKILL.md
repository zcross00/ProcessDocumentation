---
name: keeper-sweep
description: "KEEPER SWEEP SKILL — Execute all KEEPER maintenance duties in sequence. USE FOR: starting a KEEPER session after SCRIBE exhaustion or explicit role assignment; running the complete KEEPER checklist to ensure nothing is missed. Covers PR review, merge conflict cleanup, testing plan, standards compliance, architecture review, and standards improvement. All KEEPER output travels through PRs — no direct commits. DO NOT USE FOR: implementing backlog features (BUILDER work); updating design artifacts directly (SCRIBE work)."
---

# KEEPER Sweep

The KEEPER works through a defined set of maintenance duties. Complete each one before advancing. All KEEPER code changes travel through a PR — the KEEPER never commits directly to `prototype/active`.

---

## Before Starting

1. Pull `prototype/active`: `git pull origin prototype/active`
2. Review `design/STATUS.md` and `design/BACKLOG.md` for current project state.
3. List all open PRs: `gh pr list --state open`
4. List all remote branches: `git branch -r`

---

## Duty 1 — PR Review

**Review all open PRs targeting `prototype/active`.**

For each open PR:
- Apply the full `pr-review` skill checklist.
- This includes any PRs previously opened by this agent session. Review own code with the same critical, impersonal lens as external code — familiarity with the implementation is not a reason to approve uncritically.
- Leave specific, actionable comments on blocking issues.
- Approve PRs with no blocking issues. Do not leave PRs in limbo.

If there are no open PRs, note it and proceed.

---

## Duty 2 — Merge Conflict Cleanup

**Inspect in-flight work-item branches for conflicts with `prototype/active`.**

```bash
git fetch --prune
git branch -r | grep prototype/work-item
```

For each active work-item branch found:
1. Attempt to merge `prototype/active` into it locally and check for conflicts.
2. If conflicts exist and the branch owner is known to be active: leave a comment on the associated PR describing the conflict so the owner can resolve it.
3. If the branch appears abandoned (no recent commits, no open PR): note it in `design/PROTOTYPE_FINDINGS.md` as a Technical Concern so the SCRIBE can triage with the user.

Do not force-resolve conflicts on someone else's active branch without confirmation.

---

## Duty 3 — Testing Plan

**Review the current test suite and identify gaps.**

Scan the test directory (`src/test/`) and the production source (`src/main/`). For each major system or engine class, ask:
- Does a meaningful test class exist?
- Does coverage include the happy path, edge cases, and known error conditions?
- Are there systems backed only by integration tests when targeted unit tests would be more informative?
- Are there any systems with zero test coverage?

Document findings as a single cohesive testing plan. Deliver it as an `[Idea]` entry in `design/PROTOTYPE_FINDINGS.md` — **do not implement test coverage directly**. The SCRIBE and user convert ideas to backlog items on the appropriate timeline.

Format for the findings entry: use the `findings-entry` skill with category `[Idea]`. Label it clearly: `[Idea] — Comprehensive testing plan for {project/iteration}`.

---

## Duty 4 — Standards Compliance Review

**Sweep the production codebase for violations of `standards/general.md` and the applicable language standards (e.g., `standards/java.md`).**

Work file by file through `src/main/`. For each file, check:

**From `standards/general.md`:**
- Magic values (unexplained literals in logic)
- Method length (>~30 lines without clear extraction opportunity noted)
- Class focus (>~5 type references — possible single-responsibility violation)
- Comments that restate the code (delete; comments explain *why*)
- Validation happening inside internal code (should only be at system boundaries)
- Mutability — setters and mutable collections exposed without reason

**From `standards/java.md`:**
- Records should be used for value/data objects with no mutable state
- `@JsonIgnore` on computed/transient methods
- `Optional` used only as return type — never as parameter or field
- Raw string comparisons for domain types (should be enum constants)
- Unmodifiable views not returned from collection accessors
- Pattern matching not used where it would replace instanceof casts

For each violation found: open a `prototype/fix/{short-description}` branch, implement the fix, and submit a PR.

```bash
git checkout prototype/active
git pull
git checkout -b prototype/fix/{short-description}
git push -u origin prototype/fix/{short-description}
# ... implement fix ...
gh pr create --base prototype/active --head prototype/fix/{short-description} \
  --title "fix: {description of standard addressed}" \
  --body "Standards violation: [cite the rule]. Change: [what was changed and why]."
```

One PR per logical fix area is fine. Do not bundle unrelated violations.

---

## Duty 5 — Architecture Review

**Review the codebase architecture against the current design document and GOALS.md.**

Questions to ask:
- Are there classes that have accumulated responsibilities beyond what the design intended?
- Are there coupling patterns that will create a large blast radius for future changes?
- Are layer boundaries respected? (domain ← engine ← ui/persistence — no reverse deps)
- Are there components whose line count or type-reference count suggests they are doing too much?
- Are there patterns that made sense early in the iteration but are now limiting as the codebase grows?

For each concern found: log a `[TC-N]` entry in `design/PROTOTYPE_FINDINGS.md`. Assess urgency:
- **[Pressing]** — Will compound materially if not addressed in the next iteration. Could block future milestones.
- **[Future]** — Worth tracking; does not block anything currently.

Do not refactor architecture unilaterally. Log the concern; the SCRIBE and user decide whether it becomes a backlog item.

Get the next TC-N number: scan `design/PROTOTYPE_FINDINGS.md` for the highest `[TC-N]` number already present and increment by 1.

---

## Duty 6 — Standards Improvement

**Look for patterns in the codebase that would benefit from a new or updated coding standard.**

Good candidates:
- A convention that has emerged organically across multiple files and should be codified.
- An anti-pattern that has appeared more than once and is likely to recur.
- A language feature that is being used inconsistently across the codebase.
- A security practice that should be universally applied but isn't captured in any standard.

For each candidate: add an `[Idea]` entry to `design/PROTOTYPE_FINDINGS.md`. The SCRIBE adjudicates which become formal additions to the `standards/` files.

These are delivered as ideas, not implemented. The SCRIBE PR process is how standards get updated.

---

## Exhaustion Condition

The KEEPER is exhausted when:
- All open PRs have been reviewed
- All actionable merge conflicts have been surfaced or resolved
- A testing plan has been documented in findings
- A standards compliance sweep is complete
- An architecture review is complete and concerns are logged
- Standards improvement ideas are logged

When exhausted → transition to **BUILDER**. Re-read `design/BACKLOG.md` — new items may have been added as a result of KEEPER findings.

---

## KEEPER Does Not Do

- Does not implement backlog items or write new features.
- Does not update `design/STATUS.md` (SCRIBE-only).
- Does not update `design/GOALS.md` or the roadmap.
- Does not commit directly to `prototype/active` for any reason — all changes travel through `prototype/fix/` PRs.
- Does not resolve DESIGN-DRIFT items (SCRIBE resolves) or CODE-DRIFT items (BUILDER resolves) — the KEEPER may *identify* them and log the entry.
