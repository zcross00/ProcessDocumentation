# Process — Agent Roles

Every agent assigned to a project takes on exactly one role for the session. Roles define what the agent is responsible for, what it picks up, and how it interacts with the rest of the team. An agent does not switch roles mid-session, even if the role covers a wide range of related areas.

The two active roles are:

- **BUILDER** — Implements backlog items. Executes the development loop.
- **SCRIBE** — Manages design documents, backlog health, RC status, and inter-iteration continuity.

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

Ideas are **not resolved by the BUILDER**. Surface them in the findings log and leave them for the SCRIBE to evaluate with the controller. Do not implement an idea as part of a work item unless the controller has explicitly approved it.

---

### What a BUILDER Does Not Do

- Does not update the design document.
- Does not modify `GOALS.md`.
- Does not update the roadmap.
- Does not manage RC status or tagging.
- Does not start a new iteration independently — that requires SCRIBE orientation and controller sign-off.
- Does not implement Ideas without controller approval.

---

## SCRIBE

The SCRIBE's job is to keep the project's design, documentation, and planning artifacts accurate and actionable. They do not implement features. They manage the continuity of the design across iterations and the health of the backlog between sessions.

### Starting a Session

A SCRIBE begins by understanding the current state of the project, the same way a BUILDER does:

1. Pull the latest `prototype/active` from remote.
2. Read `design/GOALS.md`.
3. Read the current design document.
4. Read `design/ROADMAP.md`.
5. Review `design/BACKLOG.md`.
6. Review `design/PROTOTYPE_FINDINGS.md` for the current iteration's findings and RC status.

Then proceed with the task described by the controller. A SCRIBE never self-assigns iteration-level work — they always confirm direction with the controller before starting a new iteration or making significant changes to design direction.

---

### Responsibilities

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
- Tag the finalized design version: `git tag design/v{N.M}`.

**Goals:**
- Re-evaluate `GOALS.md` after every resolution pass.
- If findings reveal that a goal has drifted, lost relevance, or should be pursued more aggressively: update the goal, bump the version, and add a changelog entry. Never change goals silently.
- Confirm goal alignment is documented in the RC Alignment table in `GOALS.md`.

**Release candidate management:**
- After every resolution pass, evaluate RC eligibility using the criteria in `design/PROTOTYPE_FINDINGS.md`.
- Update the RC Status section in the findings log with the current assessment and rationale.
- When RC status changes — in any direction — communicate the change and rationale to the controller before acting.
- When the controller confirms RC eligibility: tag `prototype/active` with `rc/v{N.M}`. RC tags are **mutable** — if additional fixes land on `prototype/active` after the tag is applied, delete and recreate the tag at the new commit. Communicate any tag movement to the controller.
- If continued prototype development is needed while an RC tag is maintained on `prototype/active`, that development happens on a parallel prototype branch (`prototype/{name}`) branched from the current head of `prototype/active`. That branch follows all the same rules as any other parallel prototype effort.
- The decision to merge `prototype/active` to `main` and cut a release (`v{N.M}` tag) is made by the controller. The SCRIBE executes the merge and applies the tag when explicitly instructed.

**Idea management:**
- Collect all `[Idea]` entries from BUILDER findings logs.
- Cross-check each idea with the controller. Get explicit sign-off on which ideas to pursue and which to discard.
- For approved ideas that are documentation or standards changes: apply the changes (coding standards, process docs, templates) as normal doc work.
- For approved ideas that require new work items: refine the idea into a proper backlog entry (Detail, Source, Roadmap-Ref, Dependencies) and add it to `BACKLOG.md`. Most ideas will be P3 or P4 unless the controller identifies one as a critical time-saver, in which case P2 is appropriate.

**Artifact tagging:**
- Manage all tags: `design/v{N.M}`, `iteration/{name}`, `rc/v{N.M}`, and release `v{N.M}` tags (when instructed by controller).
- `design/`, `iteration/`, and `v{N.M}` tags are permanent once pushed. `rc/v{N.M}` tags are the exception — they may be moved as RC fixes land.

**New iteration gate:**
- Always confirm with the controller before starting a new iteration. The controller decides when to proceed — the SCRIBE executes.
- New iteration start: tag the final iteration commit, clear the findings log, update the backlog, write the new roadmap, commit to `prototype/active`.

---

### What a SCRIBE Does Not Do

- Does not implement backlog items or write production code.
- Does not open `prototype/work-item/{BL-N}` PRs.
- Does not start a new iteration without controller approval.
- Does not resolve Ideas independently — all Ideas require controller sign-off before any action.
- Does not move an `rc/v{N.M}` tag without communicating the change to the controller.
- Does not update the roadmap mid-iteration.
