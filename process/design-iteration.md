# Process — Design Iteration Cycle

This document describes the standard workflow for moving from an initial concept through design, prototyping, observation, and into the next iteration. It applies to any project that has a design document and a prototype phase.

---

## The Cycle

```
[GOALS] → [DESIGN DOCUMENT] → [ROADMAP] → [prototype/active] → [FINDINGS LOG] → [RC EVALUATION?]
   ↑                                              ↑                     |                  |
   │                                    [ITERATE] ────── [GOALS RE-EVAL] ←                  ↓
   │                                                        |              [ELIGIBLE? → SHIP TO main]
   └──────── [UPDATED DESIGN DOCUMENT] ← ── [TAG ITERATION, CLEAR FINDINGS, UPDATE BACKLOG]
```

Each pass through the cycle produces one design version and one iteration. The findings feed back into goals and the next design version. The same `prototype/active` branch carries all iterations forward. Merging to `main` is a separate release decision — RC eligibility means the product *could* ship, not that it automatically does.

---

## Phase 0 — Define Goals

Before any design work begins, write a `GOALS.md` using the template in `templates/GOALS.md`.

Goals are the root-level purpose of the project. They are value statements — what the project should deliver or make a person feel — not features or mechanics. The design document's pillars and systems are derived *from* these goals and should always serve them.

**Rules:**
- Goals describe experiences and values, not solutions. "Players should feel that information is as powerful as physical resources" is a goal. "Implement a journal system" is a design decision.
- Goals are falsifiable: you should be able to look at a working prototype and evaluate whether a goal was achieved.
- Goals live in the `design/` folder on `prototype/active` alongside the design document and roadmap. There is no separate design branch.
- Version goals (e.g., `v1.0`) from the start. A version bump signals a meaningful shift in what the project is trying to accomplish.
- Goals should survive at least one full design iteration unchanged. If a goal is changing every cycle, it is probably a design assumption in disguise.

---

## Phase 1 — Write the Design Document

The design document (`DESIGN_DOCUMENT.md` or equivalent) defines the intended product: its pillars, mechanics, scope, and goals. It is a statement of intent, not a specification.

**Rules:**
- Write what you intend, not what you've implemented. The design document describes the target; the ROADMAP describes the path to it.
- Mark speculative or unvalidated assumptions explicitly. These become the first candidates for prototype investigation.
- Assign a version number (e.g., `v0.1`). Versions increment after each resolution pass.
- When a design version is finalized and committed to `prototype/active`, tag that commit: `git tag design/v{N.M}`.

---

## Phase 2 — Create the Roadmap and Backlog

The roadmap (`design/ROADMAP.md`) describes the intended milestones for the iteration — the thematic groupings of work and how they build on each other. It is the *narrative* plan.

The backlog (`design/BACKLOG.md`) is the *operational* plan. Every piece of work described in the roadmap must have a corresponding `BL-N` entry in the backlog. The backlog is what agents and developers actually pick up.

**Rules for the roadmap:**
- Each milestone should have a clear framing: what it delivers, why it comes when it does, and its done-when condition.
- Milestones reference the `BL-N` items they contain.
- The roadmap is written once per iteration at planning time. Implementation detail lives in the backlog, not the roadmap.

**Rules for the backlog:**
- Every item referenced in the roadmap must exist as a `BL-N` entry in `BACKLOG.md` before implementation begins.
- Priority is informed by roadmap placement: items in earlier milestones get higher priority.
- Every item that has a prerequisite (must not start until another item is complete) must declare it in a `Dependencies:` field, and is treated as blocked until all dependencies are resolved.
- IDs are sequential and never reused. New item ID = (highest existing `BL-N` in the document) + 1.
- Status tracking (`Branch:`) in the backlog item reflects whether a work branch exists.

See [`templates/BACKLOG.md`](../templates/BACKLOG.md) for the full item format.

---

## Phase 3 — Implement via the Backlog

All prototype work lives on `prototype/active` — the single long-lived prototype branch. Individual work items are developed on short-lived `prototype/work-item/{BL-N}` branches and delivered via **pull requests** targeting `prototype/active`. The `main` branch holds the last stable state shipped to users.

See [`git-workflow.md`](git-workflow.md) for the full work-item PR workflow and the **Agent Development Loop** section, which describes the complete cycle for picking up, implementing, and submitting work items.

**Rules:**
- Never commit implementation work directly to `prototype/active`.
- Pick items from the backlog in priority order. Check that the `prototype/work-item/{BL-N}` branch does not already exist remotely before starting — its existence means another agent has claimed it.
- Blocked items (those with unresolved `Dependencies:`) must be skipped until all dependencies are resolved.
- When a work item is fully implemented and tests pass: remove it from `BACKLOG.md`, update `PROTOTYPE_FINDINGS.md` and balance notes where relevant, and open a PR referencing `Closes BL-N`.
- After opening a PR, review any other open PRs while waiting for feedback.
- Do not hold more than two open PRs at once. If two PRs are open and there are no other PRs to review, pause and wait.

---

## Phase 4 — Keep the Findings Log

The findings log (`design/PROTOTYPE_FINDINGS.md`) is updated continuously as you build and test — not just at the end of a milestone. It is a living document.

**How to add a finding:**
1. Observe something that confirms, challenges, or complicates an assumption from the design document.
2. Write a concise entry: what was assumed, what was observed, and why it matters.
3. Assign one of the four categories:

| Category | Meaning |
|----------|---------|
| **Confirm** | Assumption validated and desirable. Should be marked Confirm in the design document. |
| **Rework** | Base functionality is desirable but has a problem: unclear design, gameplay friction, poor fit with other systems, or limited extensibility. Keep the feature; rework the approach. |
| **Abandon** | Same failure modes as Rework, but the decision is to remove the feature entirely. Call out any replacements or downstream effects. |
| **Discuss** | Needs major rework or abandonment, but the direction has implications broad enough to affect other confirmed or unconfirmed assumptions. Park for a dedicated design conversation; don't resolve inline. |

4. Add the finding to the Resolution Tracker at the bottom of the findings log.

**[Idea] tags:**  
Anywhere in the findings log, an entry may be supplemented with an `[Idea]` tag to flag a cheap-win opportunity observed adjacent to the current work: testing gaps, narrow security exposures, extensibility hooks, or coding patterns worth adopting or avoiding. Ideas are not resolved by the BUILDER — they are surfaced in the findings log for the SCRIBE to collect and cross-check with the controller. See [`process/roles.md`](roles.md) for the full Idea workflow.

After the prototype is complete (all milestones at ✅ or deliberately deferred), run a resolution pass:

1. Go through every finding in the log.
2. For each finding, decide its final status if not already clear:
   - **Confirm** → Carry the assumption forward unchanged into the next design document.
   - **Rework** → Write the revised approach. Include it in the next design document with the updated version.
   - **Abandon** → Note it as abandoned in the design document. Document what (if anything) replaces it and what other features are affected.
   - **Discuss** → Record the discussion and its outcome. Move resolved findings to Confirm, Rework, or Abandon before closing the discussion.
3. Update the Resolution Tracker with final status and GDD target notes.

---

## Phase 5b — Release Candidate Evaluation

At any resolution pass, evaluate whether the current prototype qualifies as a release candidate. This is an explicit gate, not an automatic step.

**A prototype is a release candidate when:**
- The balance of findings has shifted from design rework to polish and quality concerns. Confirm findings substantially outnumber Rework and Abandon findings.
- No Rework or Abandon finding undermines a core goal stated in `GOALS.md`.
- Discuss findings are resolved or scoped out of the current release.
- The product is playable or usable end-to-end without hitting dead ends.

**Tracking:**
- RC eligibility is tracked in `design/PROTOTYPE_FINDINGS.md` under a dedicated **Release Candidate Status** section (see template).
- After a resolution pass, the SCRIBE updates the RC Status section with the current assessment and rationale, then communicates any change in eligibility to the controller.
- When RC status is confirmed, the SCRIBE applies an `rc/v{N.M}` tag to the current head of `prototype/active`. There is no separate RC branch. If additional fixes land on `prototype/active` after the tag is applied, the SCRIBE moves the tag to the new commit and informs the controller.
- If continued prototype development is needed while the RC tag is active, that development happens on a parallel prototype branch (`prototype/{name}`) branched from `prototype/active` — the same rules as any other parallel prototype effort.
- The decision to ship — merging `prototype/active` to `main` and applying a release tag (`v{major}.{minor}.{patch}`) — is made by the controller. The SCRIBE executes the merge and applies the tag when explicitly instructed. See [`git-workflow.md`](git-workflow.md).

---

## Phase 6 — Goals Re-evaluation

After the resolution pass (and RC evaluation, if applicable), return to `GOALS.md` before updating the design document.

1. Read each goal against the findings from the completed prototype.
2. Ask: Did this prototype confirm we are building toward this goal? Did any findings reveal that a goal was pointed at the wrong value, or that a goal should be pursued more aggressively?
3. If a goal needs to change — in scope, priority, or premise — update it and bump the version in `GOALS.md`. Record the reason in the changelog.
4. If all goals are still valid and the prototype supported them, note the alignment in the RC Alignment table in `GOALS.md` and don't bump the version.

Goals should never change silently. Every change gets a changelog entry.

---

## Phase 7 — Next Design Document Version

After the goals re-evaluation:

1. Increment the design document version number.
2. Update each section affected by Rework or Abandon findings.
3. Add new assumptions or mechanics surfaced during the prototype.
4. Note the prototype phase version in the document header.
5. Verify that every design pillar still maps to at least one goal in `GOALS.md`. Remove or revise pillars that have drifted.

Then return to Phase 2: write the next milestone's ROADMAP entries based on the updated design.

---

## Phase 8 — Prepare for the Next Iteration

This phase is owned by the **SCRIBE**. The SCRIBE must confirm with the controller before beginning a new iteration. See [`process/roles.md`](roles.md).

There is no new branch to create. All continued work stays on `prototype/active`.

1. **Tag the iteration.** Tag the final commit of the completed iteration on `prototype/active`: `git tag -a iteration/{name} -m "{iteration summary}"`. Push the tag. This is the permanent record of where the iteration ended.
2. **Clear the findings log.** Reset `design/PROTOTYPE_FINDINGS.md` to the template structure with empty milestone sections, an empty Resolution Tracker, and fresh metadata (new iteration name, new design version, current date). Preserve the RC History. The previous iteration's findings have been consumed by the design document update — they do not carry forward.
3. **Update the backlog.** Open `design/BACKLOG.md`. Remove items that were resolved during the completed iteration (git history records them as done). Add new items surfaced by the findings — Rework findings, Technical Concerns marked [Pressing], and any SCRIBE-approved Ideas that were converted to backlog entries. Assign priorities. Every new item must have a `BL-N` ID using the next available number.
4. **Write the new roadmap.** Build `design/ROADMAP.md` from the updated design document and `BACKLOG.md` priorities. Every item described in the roadmap must have a corresponding `BL-N` entry in the backlog before the iteration begins. The roadmap describes the *shape* of the work; the backlog contains the *operational detail*. Backlog item priorities must align with roadmap milestone order.
5. **Commit and push.** A single `docs:` commit containing the updated design document, cleared findings, updated backlog, and new roadmap. This commit on `prototype/active` is the clean start of the next iteration.

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why it Hurts |
|--------------|-------------|
| Updating the design document during implementation | Blurs the line between intent and reality; makes findings less meaningful |
| Deferring all findings to end of milestone | Observations are freshest close to implementation; delayed logging loses nuance |
| Marking a finding Confirm because it "works" | Confirm means it's *desirable*, not just functional — a working but bad design is still a Rework |
| Letting Discuss findings sit unresolved | They block downstream design; assign a time to discuss and force a resolution |
| Skipping the resolution pass | Without it, the next design document inherits unvalidated assumptions from the previous one |
| Skipping goals re-evaluation | Goals drift silently; the design eventually serves the wrong purpose without anyone noticing |
| Declaring RC without explicitly evaluating goals | RC means ready for someone else to experience — if goals aren't met, it's not ready |
| Writing goals that are really design decisions | Goals should survive a full tech rewrite; if they wouldn't, they're not goals |
| Carrying forward the previous iteration's findings | Findings are consumed by the design update; the new iteration starts clean to avoid conflating old and new observations |
