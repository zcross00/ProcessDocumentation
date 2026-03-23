# Process — Design Iteration Cycle

This document describes the standard workflow for moving from an initial concept through design, prototyping, observation, and into the next iteration. It applies to any project that has a design document and a prototype phase.

---

## The Cycle

```
[GOALS] → [DESIGN DOCUMENT] → [ROADMAP] → [PROTOTYPE BRANCH] → [FINDINGS LOG] → [RC EVALUATION?]
   ↑                                                                  |                  |
   │                                                  [GOALS RE-EVAL] ←                  ↓
   │                                                        |                    [RELEASE CANDIDATE]
   └──────── [UPDATED DESIGN DOCUMENT] ← ── [CLEAR FINDINGS & NEW BRANCH] ←────┘
```

Each pass through the cycle produces one design version and one prototype phase. The findings from the prototype feed back into the goals and the next design version. When confirmed findings significantly outnumber rework and design concerns, the prototype may be evaluated as a release candidate.

---

## Phase 0 — Define Goals

Before any design work begins, write a `GOALS.md` using the template in `templates/GOALS.md`.

Goals are the root-level purpose of the project. They are value statements — what the project should deliver or make a person feel — not features or mechanics. The design document's pillars and systems are derived *from* these goals and should always serve them.

**Rules:**
- Goals describe experiences and values, not solutions. "Players should feel that information is as powerful as physical resources" is a goal. "Implement a journal system" is a design decision.
- Goals are falsifiable: you should be able to look at a working prototype and evaluate whether a goal was achieved.
- Goals live on the `design` branch alongside the design document.
- Version goals (e.g., `v1.0`) from the start. A version bump signals a meaningful shift in what the project is trying to accomplish.
- Goals should survive at least one full design iteration unchanged. If a goal is changing every cycle, it is probably a design assumption in disguise.

---

## Phase 1 — Write the Design Document

The design document (`DESIGN_DOCUMENT.md` or equivalent) defines the intended product: its pillars, mechanics, scope, and goals. It is a statement of intent, not a specification.

**Rules:**
- Write what you intend, not what you've implemented. The design document describes the target; the ROADMAP describes the path to it.
- Mark speculative or unvalidated assumptions explicitly. These become the first candidates for prototype investigation.
- Assign a version number (e.g., `v0.1`). Versions increment after each resolution pass.

---

## Phase 2 — Create the Roadmap

The roadmap (`ROADMAP.md`) breaks the design document into milestones — ordered, scoped deliverables that together realize the design.

**Milestone structure:**
- One milestone per coherent system or feature group.
- Each milestone has a clear "Done when:" condition — a statement that can be evaluated yes or no.
- Tasks within a milestone include the key files they affect.
- Earlier milestones establish foundations; later milestones build on them.

**Status tracking:**
- `✅ Done` — fully complete, done-when condition met.
- `🔄 Partial` — in progress or partially implemented.
- `⬜ Not started` — not yet begun.

---

## Phase 3 — Implement on a Prototype Branch

All prototype work lives on a dedicated branch named `prototype/{name}` (e.g., `prototype/alpha`). The `design` branch holds the living design document and goals. The `main` branch always contains the last stable, reviewed state.

See [`git-workflow.md`](git-workflow.md) for the full branch strategy.

**Rules:**
- Mark tasks `🔄 Partial` in the roadmap as you begin them.
- Mark tasks `✅ Done` once the done-when condition is met and verified.
- Don't mark milestones complete until every task in them is ✅.

---

## Phase 4 — Keep the Findings Log

The findings log (`PROTOTYPE_FINDINGS.md`) is updated continuously as you build and test — not just at the end of a milestone. It is a living document.

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

---

## Phase 5 — Resolution Pass

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
- RC eligibility is tracked in the `PROTOTYPE_FINDINGS.md` under a dedicated **Release Candidate Status** section (see template).
- After a resolution pass, update the RC Status section with the current assessment and the rationale.
- When RC status is confirmed, cut an `rc/{name}` branch from the prototype branch and begin stabilization. See [`git-workflow.md`](git-workflow.md).

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

Before starting the next prototype branch:

1. **Clear the findings log.** Reset `PROTOTYPE_FINDINGS.md` to the template structure with empty milestone sections, an empty Resolution Tracker, and fresh metadata (new branch name, new design version, current date). Preserve the RC History so the project's evaluation trail is maintained. The previous iteration's findings have already been consumed by the design document update — they do not carry forward.
2. **Create the next prototype branch.** If the iteration direction is "Continue Iterating," branch from the current prototype branch to inherit its codebase. If the direction is "Fresh Start," branch from `main`. See [`git-workflow.md`](git-workflow.md) for naming.
3. **Cherry-pick the design commit.** Bring the updated design document and goals into the new prototype branch by cherry-picking the commit from the `design` branch.
4. **Write the new roadmap.** Build `ROADMAP.md` from the updated design document and the previous iteration's Next Iteration Direction. For continuation iterations, the roadmap is informed by the rework backlog and priorities established in the findings summary.
5. **Commit and push.** The new branch should start with a clean commit containing the roadmap and cleared findings.

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
