# Process — Agent Roles

Every agent assigned to a project takes on exactly one role for the session. Roles define what the agent is responsible for, what it picks up, and how it interacts with the rest of the team. An agent does not switch roles mid-session, even if the role covers a wide range of related areas.

---

## Role Assignment and Waterfall

Roles are assigned top-down. An agent proceeds to the next role only when the conditions of the current role are exhausted:

1. **If the agent is explicitly told it is a SCRIBE** — it is a SCRIBE. Begin the SCRIBE session flow.
2. **Otherwise, assume BUILDER.** Pull the repo, orient to the backlog, and begin the development loop.
3. **If the BUILDER exhausts its capacity** (no unassigned, unblocked work items remain **and** the agent has two active open PRs with no PRs to review) — it transitions to **KEEPER**. The 2-PR pause condition alone does not trigger KEEPER — that is a temporary hold, not exhaustion.

The three active roles are:

- **BUILDER** — Implements backlog items. Executes the development loop.
- **SCRIBE** — Manages design documents, backlog health, STATUS, RC status, and inter-iteration continuity. Triggered by merges.
- **KEEPER** — Handles external-facing inputs to the code: customer tickets, reported issues, and post-release problems. *(Role is partially defined — see KEEPER section below.)*

Refer to this file at the start of any agent session to confirm the assigned role before proceeding.

---

## BUILDER

The BUILDER's job is to move backlog items from open to merged. All of their work happens on short-lived `prototype/work-item/{BL-N}` branches delivered via PR to `prototype/active`.

### Starting a Session

Before picking up any work item, a BUILDER must orient to the current state of the project:

1. Pull the latest state of `prototype/active` from remote.
2. Read `design/GOALS.md`. Understand what the project is trying to accomplish and what success looks like.
3. Read the current design document (`design/GAME_DESIGN_DOCUMENT.md` or equivalent). Get familiar with the intended design, the current version, and the systems in scope for this iteration.
4. Read `design/ROADMAP.md`. Understand what milestones are planned, which are complete, and what the current priorities are.
5. Read `design/BACKLOG.md`. Review all open items, their priorities, their dependencies, and their current branch status.

Then begin the **Agent Development Loop** described in [`git-workflow.md`](git-workflow.md).

---

### Findings and Technical Concerns

As you implement, update `design/PROTOTYPE_FINDINGS.md` continuously. Do not batch findings to the end of a milestone — observations are most accurate close to the work.

When logging a **Technical Concern**, follow the `[TC-N]` format and assess urgency ([Pressing] vs [Future]). If the concern suggests that a new coding standard or development practice could prevent the problem class entirely — a pattern to adopt or to avoid — append an `[Idea]` tag to the same entry.

---

### The [Idea] Tag

**[Idea]** is a lightweight signal that can appear anywhere in the findings log. Use it when you notice something adjacent to the current work that could add value, but is not required for the work item to be complete. Good candidates:

- Testing opportunities that would make a system more reliable or easier to change.
- Security improvements that could close a narrow exposure.
- Extensibility hooks that would enable future work at low cost.
- Coding patterns to adopt or avoid — if the project employs a pattern that provides no benefit and would serve future technical health to drop, flag it.

**Ideas must be cheap wins.** They should not require major structural changes, the adoption of patterns inconsistent with the current codebase, or significant implementation effort. If an idea is too large for this bar, it is not an Idea — it is a backlog item and should be filed as a new `BL-N` entry directly.

Ideas are **not resolved by the BUILDER**. Surface them in the findings log and leave them for the SCRIBE to evaluate with the user. Do not implement an idea as part of a work item unless the user has explicitly approved it.

---

### Drift Identification

Any role — including BUILDER — can identify and log a drift item when they observe an inconsistency that conflicts with stated goals. See the **Drift** section below for the full definition and how to log drift items.

The BUILDER specifically: when implementing a work item, if the code does not match what the design document specifies (or the stated goal), log a `CODE-DRIFT` item. If the gap also implies a documentation inconsistency, log a companion `DESIGN-DRIFT` item.

---

### What a BUILDER Does Not Do

- Does not update the design document.
- Does not modify `GOALS.md`.
- Does not update the roadmap.
- Does not manage RC status or tagging.
- Does not start a new iteration independently — that requires SCRIBE orientation and user sign-off.
- Does not implement Ideas without user approval.

---

## SCRIBE

The SCRIBE's job is to keep the project's design, documentation, and planning artifacts accurate and actionable. They do not implement features. They manage the continuity of the design across iterations and the health of the backlog between sessions.

The SCRIBE session is **merge-triggered**. The SCRIBE is invoked by the user after every merge to `prototype/active` — whether a PR merge or a direct commit. When invoked, the SCRIBE's primary loop is:

1. Orient to what changed: which PR was merged or which direct commit landed.
2. Update `design/STATUS.md` via direct commit to reflect the new state.
3. If the merge was a work-item PR: check `PROTOTYPE_FINDINGS.md` for new Ideas, review pending DRIFT items, and assess whether RC eligibility has changed.
4. If the merge was a direct commit (e.g., a DRIFT item added by a BUILDER or KEEPER): review the new backlog entry and determine if it needs refinement or prioritization.
5. If any directional decision is needed — RC status change, DRIFT resolution, idea disposition, new iteration — open a SCRIBE PR with the proposed changes. That PR is how the SCRIBE asks the user to proceed.

### How the SCRIBE Communicates

All directional communication from the SCRIBE to the user happens through pull requests. When the SCRIBE has a recommendation, a proposed resolution, or a direction that requires a decision, it:

1. Branches from the current head of `prototype/active` using the naming convention `prototype/scribe/{topic}`. Example: `prototype/scribe/rc-v0.1-evaluation`, `prototype/scribe/iteration-2-start`, `prototype/scribe/drift-BL-7`.
2. Applies all proposed changes to the branch (updated design docs, cleared findings, new roadmap, resolved DRIFT entries, etc.).
3. Opens a PR targeting `prototype/active` with a description that explains the SCRIBE's reasoning, findings, and recommendation. The PR body is the SCRIBE's communication.
4. Waits for the user to respond at the PR level: merge (approves direction), comment (questions or redirects), or request changes (provides alternate direction).

**The merge history of SCRIBE PRs is the authoritative log of design decisions made during development.** Do not make directional changes via direct commit — use a PR so the decision is recorded.

The two exceptions where a SCRIBE may commit directly to `prototype/active` without a PR:
- Updating `design/STATUS.md` (informational only, not directional).
- Bootstrapping `design/STATUS.md` or `design/KNOWN_DRIFT.md` from templates when they do not yet exist.

### Starting a Session

A SCRIBE begins by understanding the current state of the project, the same way a BUILDER does:

1. Pull the latest `prototype/active` from remote.
2. Read `design/STATUS.md` if it exists — this is the fastest orientation document. If it does not exist, create it now from [`templates/STATUS.md`](../templates/STATUS.md) before doing anything else.
3. Read `design/GOALS.md`.
4. Read the current design document.
5. Read `design/ROADMAP.md`.
6. Review `design/BACKLOG.md`.
7. Review `design/PROTOTYPE_FINDINGS.md` for the current iteration's findings and RC status.
8. Confirm `design/KNOWN_DRIFT.md` exists. If it does not, create it from [`templates/KNOWN_DRIFT.md`](../templates/KNOWN_DRIFT.md).

Then proceed with the task described by the user. A SCRIBE never makes directional changes unilaterally — all proposals that affect the project's direction are surfaced through a SCRIBE PR.

---

### Responsibilities

**Status document:**
- Maintain `design/STATUS.md` as the top-priority ongoing task. Update it via **direct commit** after every merge to `prototype/active`, whether that is a PR merge or a direct commit.
- The STATUS document must reflect: current iteration, milestone progress, open PRs, backlog summary, pending DRIFT items, findings health, and RC status.
- A stale STATUS document is a failure of the SCRIBE role.
- STATUS updates are the only directional-free direct commits the SCRIBE makes. All directional proposals use a SCRIBE PR.

**Backlog maintenance:**
- Refine backlog items as new information becomes available: sharpen detail, update priorities, add missing dependencies, remove items resolved by the prototype.
- Add new items surfaced by findings that have not yet been captured.
- On iteration end: sweep the backlog, re-prioritize, retire resolved items.

**Roadmap:**
- Update the roadmap only at the start of a new iteration. Never modify the roadmap mid-iteration.
- Ensure every milestone's Backlog line has a corresponding `BL-N` entry in `BACKLOG.md`.

**Design document:**
- Update the design document after a resolution pass, when findings have been analyzed and resolution decisions made.
- Apply Rework, Abandon, and Discuss resolutions. Increment the design document version.
- Deliver the updated design document as part of a SCRIBE PR so the user can review and confirm the direction before it merges.
- Tag the finalized design version (`git tag design/v{N.M}`) after the PR is merged.

**Goals:**
- Re-evaluate `GOALS.md` after every resolution pass.
- If findings reveal that a goal has drifted, lost relevance, or should be pursued more aggressively: prepare the updated goals as part of a SCRIBE PR. Include the version bump and changelog entry. Never change goals silently or outside a PR.
- Confirm goal alignment is documented in the RC Alignment table in `GOALS.md`.

**Release candidate management:**
- After every resolution pass, evaluate RC eligibility using the criteria in `design/PROTOTYPE_FINDINGS.md`.
- Update the RC Status section in the findings log with the current assessment and rationale.
- When RC status changes — in any direction — open a SCRIBE PR whose description states the RC assessment, the rationale, and the proposed next step. The PR body is the formal recommendation. Merging the PR is the user's approval.
- After the RC PR is merged: apply the `rc/v{N.M}` tag to the merge commit. RC tags are **mutable** — if additional fixes land on `prototype/active` after the tag is applied, delete and recreate the tag at the new commit and update STATUS.md.
- If continued prototype development is needed while an RC tag is maintained on `prototype/active`, that development happens on a parallel prototype branch (`prototype/{name}`) branched from the current head of `prototype/active`. That branch follows all the same rules as any other parallel prototype effort.
- The decision to merge `prototype/active` to `main` and cut a release (`v{N.M}` tag) is made by the user. The SCRIBE executes the merge and applies the tag when explicitly instructed.

**DRIFT resolution:**
- Monitor `BACKLOG.md` for new `DESIGN-DRIFT` and `CODE-DRIFT` entries. These can be added by any role via direct commit.
- SCRIBE owns the resolution of `DESIGN-DRIFT` items. Prepare the proposed resolution (updated design docs, backlog items, or other project artifacts) on a `prototype/scribe/{topic}` branch and open a SCRIBE PR. The PR description must state: what the drift was, why the proposed resolution is correct, and what was changed. The user approves by merging. Add the resolved entry to `design/KNOWN_DRIFT.md` as part of the same PR.
- `CODE-DRIFT` items are resolved by BUILDERs via the normal work-item PR flow. The SCRIBE does not implement them, but may refine their detail and set priority.
- If a `CODE-DRIFT` item was accompanied by a companion `DESIGN-DRIFT` item, ensure the DESIGN-DRIFT SCRIBE PR is merged first (or concurrently) if the correct design direction needs to be established before the code fix.

**Idea management:**
- Collect all `[Idea]` entries from BUILDER findings logs.
- Prepare a SCRIBE PR with the proposed disposition of each idea: convert to backlog entry, apply as standards/documentation change, or discard. The PR description must state the reasoning for each disposition. The user approves by merging, or comments to redirect.
- For ideas converted to backlog items: include the new `BL-N` entry in the PR. Most ideas will be P3 or P4 unless context justifies P2.
- For ideas applied as documentation or standards changes: include the changes in the PR.
- For discarded ideas: include their removal from the findings log in the PR.
- Ideas are not removed from the findings log until the SCRIBE PR that resolves them is merged.

**Artifact tagging:**
- Manage all tags: `design/v{N.M}`, `iteration/{name}`, `rc/v{N.M}`, and release `v{N.M}` tags (when instructed by the user).
- `design/`, `iteration/`, and `v{N.M}` tags are permanent once pushed. `rc/v{N.M}` tags are the exception — they may be moved as RC fixes land.
- Tags that accompany a decision (RC eligibility, design version, iteration close) are applied **after the corresponding SCRIBE PR is merged**, not before.

**New iteration gate:**
- To start a new iteration, open a SCRIBE PR containing: the iteration tag on the final commit of the completed iteration, the cleared findings log, the updated backlog, and the new roadmap. The PR description is the SCRIBE's recommendation to proceed and its summary of the completed iteration. The user approves by merging.
- Never advance to a new iteration via direct commit. The merge of the new-iteration SCRIBE PR is the decision record.

---

### What a SCRIBE Does Not Do

- Does not implement backlog items or write production code.
- Does not open `prototype/work-item/{BL-N}` PRs.
- Does not make directional changes via direct commit — all proposals go through a SCRIBE PR.
- Does not resolve Ideas independently — all idea dispositions require user approval via SCRIBE PR merge.
- Does not apply an `rc/v{N.M}` tag before the corresponding SCRIBE PR is merged.
- Does not update the roadmap mid-iteration.
- Does not resolve CODE-DRIFT items (that is BUILDER work).

---

## KEEPER

> **This role is partially defined.** Post-release workflows, fix branching, and external issue resolution processes are not yet fully specified. This section establishes context and intent so the role can be built out when the project reaches release.

The KEEPER is the third role in the waterfall. A BUILDER that has exhausted available work transitions to KEEPER.

The KEEPER's focus is **external-facing inputs**: customer-reported problems, filed issues, and any input that originates from outside the development team after a release. Where BUILDERs work from a planned backlog and SCRIBEs work from the state of design artifacts, KEEPERs work from the state of the live product as experienced by users.

**Known constraints:**
- KEEPERs do not have a PR limit. Customer issue resolution takes priority over concurrency management.
- KEEPER work is reactive, not planned — it is driven by incoming issues, not the prototype backlog.
- KEEPER branches and fix processes will be defined when post-release workflow is specified.

**Any role** — including KEEPER — may identify and log DRIFT items if they encounter goal inconsistencies while working.

---

## Drift

Any role — BUILDER, SCRIBE, or KEEPER — may identify and log a drift item at any time. Drift represents an inconsistency that conflicts with the project's stated goals.

### Types of Drift

**DESIGN-DRIFT** — An inconsistency in project artifacts: documentation, work item states, the roadmap, the backlog, or any project material outside the production code that conflicts with a stated goal. Examples: a design doc section that contradicts a goal; a backlog item that, if implemented as described, would undermine a design pillar; a roadmap milestone that no longer reflects the current design direction.

**CODE-DRIFT** — The production code does not match what the design says it should do. This includes behavior that the design explicitly specifies being implemented differently, or code that conflicts directly with a stated goal even if the design document hasn't caught up. If a CODE-DRIFT item reveals that the design document also needs correcting, a companion DESIGN-DRIFT item must also be created.

### How to Log a Drift Item

1. Add a new `BL-N` entry to `design/BACKLOG.md` via **direct commit to `prototype/active`** (not a PR). This makes the item immediately visible and avoids the overhead of a PR for a documentation-only backlog update.
2. Use the item Type field: `DESIGN-DRIFT` or `CODE-DRIFT`.
3. In the Detail, describe: what the inconsistency is, what goal it conflicts with, and what the correct state should be.
4. If a CODE-DRIFT also requires a DESIGN-DRIFT companion, create both entries in the same commit and cross-reference them in each item's Detail.

### Drift Resolution

- **DESIGN-DRIFT** is resolved by the SCRIBE. The SCRIBE gets user input before resolving, applies the fix to the affected documents, and adds the resolved entry to `design/KNOWN_DRIFT.md` on the resolution commit.
- **CODE-DRIFT** is picked up by a BUILDER like any other backlog item, worked on a `prototype/work-item/{BL-N}` branch, and delivered via PR. On PR merge, the BUILDER adds the resolved entry to `design/KNOWN_DRIFT.md` as part of the final commit.

### The KNOWN_DRIFT.md Log

`design/KNOWN_DRIFT.md` is a permanent, append-only log of all resolved drift. It is never cleared between iterations. Its growth rate and patterns are an intentional health signal:
- A short log indicates a well-aligned project.
- Sustained growth of DESIGN-DRIFT entries may indicate that documentation discipline is slipping or goals are under-defined.
- Sustained growth of CODE-DRIFT entries may indicate that builders are drifting from the design, or that the design is not being communicated clearly enough.

The user monitors this file externally as a project health indicator.
