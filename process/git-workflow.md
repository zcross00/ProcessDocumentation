# Process — Git Workflow

Standard branch naming, commit message format, tagging, and merge strategy for all projects.

---

## Branch Structure

There are two long-lived branches. Everything else is short-lived and deleted after delivery.

| Branch | Purpose |
|--------|---------|
| `main` | Always stable. Represents the version in users' hands — the last shipped, reviewed release. Never commit work-in-progress or prototype code here directly. Updated only when a completed prototype iteration is ready for users. |
| `prototype/active` | The single active prototype branch. All current development effort goes here. Long-running; survives across multiple design iterations. Work-item branches are short-lived and PR'd into this branch. Design documents, roadmaps, and findings live in the `design/` folder on this branch — they travel with the code. |
| `prototype/{random-noun}` | A parallel prototype branch, created only when a second prototype direction needs to run concurrently alongside `prototype/active`. Example: `prototype/thornwood`. Rare — most work stays in `prototype/active`. |
| `prototype/work-item/{id}` | Short-lived work-item branch. Each backlog item is implemented here and delivered to `prototype/active` via a pull request — never by direct commit. Branched from the current head of `prototype/active`. Example: `prototype/work-item/BL-3`. Delete after merge. |
| `prototype/scribe/{topic}` | Short-lived SCRIBE branch. Used when the SCRIBE has a directional proposal that requires user approval: new iteration start, RC eligibility change, DRIFT resolution, goals update, design doc update, or idea disposition. Branched from the current head of `prototype/active`. Example: `prototype/scribe/iteration-2-start`. Delete after merge. |
| `feature/{short-description}` | Isolated feature work not tied to a specific backlog item, branched off `prototype/active`. Use sparingly — prefer backlog items for all tracked work. Example: `feature/concept-decay`. |
| `fix/{short-description}` | Bug fixes not already captured in a backlog item. |
| `chore/{short-description}` | Non-functional changes — dependency updates, refactoring, doc cleanup. |

### Branch Lifecycle

```
main                                    ← stable; tagged on every update
 └── prototype/active                   ← long-lived; all development happens here
      ├── prototype/work-item/BL-1      ← work-item branch; merged via PR
      ├── prototype/work-item/BL-2      ← work-item branch; merged via PR
      ├── prototype/work-item/BL-3      ← work-item branch; merged via PR
      ├── prototype/scribe/rc-v0.1      ← SCRIBE PR; directional proposal
      └── chore/update-jackson          ← non-backlog chore branch

prototype/thornwood                     ← parallel prototype, if ever needed
```

- Work-item branches are short-lived. Delete after the PR is merged.
- The existence of a `prototype/work-item/{id}` branch (pushed to the remote) is the signal that the item is already being worked. An agent must check for the branch's existence before picking up any item.
- Design documents (`GOALS.md`, `GAME_DESIGN_DOCUMENT.md`, `ROADMAP.md`, `PROTOTYPE_FINDINGS.md`, `BACKLOG.md`) live in the `design/` folder on `prototype/active`. They are updated as part of the work-item PR that resolves them — no separate design branch.
- `main` is only updated when the iteration is ready for users. See Merge Strategy below.
- **Exception — DRIFT backlog entries:** When any role identifies a DRIFT item (`DESIGN-DRIFT` or `CODE-DRIFT`), they add the entry to `design/BACKLOG.md` via a **direct commit to `prototype/active`**. This and STATUS.md updates are the only permitted direct commits to `prototype/active` outside a SCRIBE session. See [`process/roles.md`](roles.md) for the full DRIFT workflow.
- **SCRIBE PRs:** All directional SCRIBE work — new iteration, RC evaluation, DRIFT resolution, idea disposition, goals/design updates — is delivered via `prototype/scribe/{topic}` branches and PRs. Merging a SCRIBE PR is the user's approval of its direction. See [`process/roles.md`](roles.md) for full details.

---

## Tags

Tags are the permanent record of meaningful states. They replace long-lived branches as state captures.

| Tag | When to Create | Example |
|-----|---------------|---------|
| `design/v{N.M}` | When a design document version is finalized and committed to `prototype/active`. | `design/v0.2` |
| `iteration/{name}` | When a prototype iteration is complete: all milestones done, findings resolved, BACKLOG updated. Tag the final commit on `prototype/active`. | `iteration/prototype-awakening` |
| `rc/v{N.M}` | When RC eligibility is confirmed by the SCRIBE after a resolution pass. Tag the current head of `prototype/active`. | `rc/v0.1` |
| `v{major}.{minor}.{patch}` | When production-ready work is merged to `main`. Tag the merge commit. | `v1.0.0` |

**Rules:**
- Tags are permanent, with one exception: `rc/v{N.M}` tags are **mutable**. If additional fixes land on `prototype/active` after an RC tag is applied, the SCRIBE deletes and recreates the tag at the new commit and communicates the change to the user. All other tags are permanent once pushed.
- Annotated tags are preferred for `iteration/`, `rc/`, and `v` releases: `git tag -a {tag} -m "{message}"`.
- Lightweight tags are acceptable for `design/` snapshots.
- There is no RC branch. RC state is represented entirely by the `rc/v{N.M}` tag on `prototype/active`. If development must continue in parallel with a live RC tag, it branches off as a parallel prototype branch (`prototype/{name}`) — not off `main`.

---

## Work-Item PR Workflow

All implementation work is driven by `BACKLOG.md`. Every item in the backlog has a `BL-N` identifier. Items are picked up in priority order (P1 before P2, P2 before P3, P4 last). Within a priority tier, items are picked in `BL-N` ascending order.

### Agent Development Loop

This describes the full loop an AI agent follows when working the backlog:

**1. Review open PRs first.**  
Before picking up any new work, check for open PRs targeting `prototype/active`. If any exist, review them using the checklist in [`standards/pr.md`](../standards/pr.md) — read the diff, check for problems, and respond with comments or approval. If a PR has unresolved blocking issues identified by the agent after review, leave a review comment and continue.

**2. Pick the highest unassigned item.**  
Open `design/BACKLOG.md`. Starting at P1, find the first item that does not have an existing `prototype/work-item/{id}` remote branch. The existence of that branch means another agent instance is already working the item — skip it and move to the next.

**Check for blockers first.** If the item has a `Dependencies:` field listing unresolved items, skip it and move to the next eligible item.

**3. Assign by creating and pushing the branch immediately.**  
Once an item is selected, create the branch and push it to the remote before doing any implementation work. The pushed branch is the assignment signal — it prevents two agents from picking up the same item.

```bash
git checkout prototype/active
git pull
git checkout -b prototype/work-item/{BL-N}
git push -u origin prototype/work-item/{BL-N}
```

**4. Implement the work item.**  
Build everything described in the item's Detail section. This includes:
- All code changes specified.
- Tests or verification as required by the item or applicable standards.
- Any updates to findings (`PROTOTYPE_FINDINGS.md`) and balance notes that the item warrants.
- Removing the resolved item from `BACKLOG.md` as part of the final commit.

Do not implement anything beyond the item's stated scope. If adjacent issues are found, add them as new BL-N items in `BACKLOG.md` with the next available ID.

**5. Submit a PR.**  
When the item is fully implemented and all tests pass:

1. Push the final commits to the work-item branch.
2. Remove the item from `BACKLOG.md` as part of this final commit.
3. Open a PR targeting `prototype/active`.
4. The PR description must include:
   - The backlog item ID and its synopsis.
   - The done-when condition from the item detail (or derived from it).
   - Test results summary.
   - Any findings or balance notes updated as part of this work.
   - The commit message `Closes BL-N` reference.
5. Follow [`standards/pr.md`](../standards/pr.md) when reviewing the PR before submitting. Apply the same checklist when reviewing others' PRs.

**6. After submitting the PR, review any open PRs while waiting.**  
If there are other open PRs targeting `prototype/active`, review them now using the checklist in [`standards/pr.md`](../standards/pr.md). Leave comments, flag issues, or approve.

**7. Continue to the next item if capacity allows.**  
If there are no PRs to review, and the agent has fewer than **two active open PRs**, continue to the next highest unassigned item and repeat from step 3.

If the agent has **two active open PRs** and no PRs to review: **pause**. Do not pick up additional work until one of the open PRs is merged or requires response.

**8. Respond to PR feedback.**  
When a reviewer comments on an open PR:
- Acknowledge questions and clarify intent.
- Address flagged issues on the work-item branch and push.
- Delegate complex investigation to a sub-agent, passing the PR context, specific comment, and affected code. Confirm output before pushing.
- Once feedback is resolved, push and re-request review. Do not re-open merged work.

**9. After merge, check for more PRs to review, then continue the loop.**  
Every merge is a signal to return to step 1: review open PRs, then pick up the next item.

---

### Work-Item ID Rules

Work-item IDs follow the `BL-N` convention defined in `BACKLOG.md`:
- IDs are sequential integers. The next ID is always `(highest BL-N currently in the document) + 1`.
- IDs are never reused. When an item is removed from the backlog after being resolved, its ID is retired.
- This ensures IDs don't clash across concurrent work and can be traced in git history even after the item is removed.

---

### Checking for an Existing Branch

```bash
# Check if a work-item branch is already claimed
git fetch --prune
git branch -r | grep prototype/work-item/BL-{N}
```

If the branch exists remotely, the item is claimed. Move to the next eligible item.

---

### Removing an Item from the Backlog on Merge

When writing the PR's final commit (the one that completes the implementation), remove the item from `BACKLOG.md`:
- Delete the item's row from the priority table.
- Delete the item's `<!-- item: BL-N -->` detail block.
- Do not renumber remaining items.
- Reference `Closes BL-N` in the commit message.

This makes the PR itself the authoritative record that the item was resolved — the git history preserves it permanently.

---

---

## Commit Message Format

```
{type}: {imperative description}

{optional body — explain WHY, not WHAT, if the change is not self-evident}
```

**Types:**

| Type | Use For |
|------|---------|
| `feat` | New functionality |
| `fix` | Bug fix |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `test` | Adding or updating tests |
| `chore` | Build config, dependencies, CI, formatting |
| `docs` | Documentation only changes |

**Rules:**
- Subject line: imperative mood, present tense, no period. Max ~72 characters.
- Subject line answers: "If applied, this commit will..." — complete that sentence.
- Body: explain *why* the change was made, not *what* it does (the diff shows what).
- Reference backlog item IDs or finding IDs when a commit addresses one (e.g., `Closes BL-4`, `Addresses finding AW-M4-3`).

**Examples:**

```
feat: add dynamic effort pool based on fitness and fatigue

Replaces the hardcoded effortBudget = 20 in DayState with a
per-character calculation driven by FitnessLevel and FatigueLevel
multipliers. Closes BL-7.
```

```
fix: correct effort display showing "N / 10" when budget exceeds 10
```

```
docs: update ROADMAP to mark milestones 1-3 complete
```

---

## Merge Strategy

- **Work-item branches → `prototype/active`:** PR required. Squash merge or merge commit depending on whether commit history is worth preserving. Delete the work-item branch after merge.
- **`prototype/active` → `main`:** Merge commit when the iteration is ready for users. Run `git tag v{major}.{minor}.{patch}` on the merge commit immediately after.
- **Never force-push to `main` or `prototype/active`** — these are permanent reference points. Tags on these branches are also permanent.
- **Parallel prototype branches:** Treated as long-lived like `prototype/active`. Merge to `main` independently if they ship. Never merge into each other without explicit intent.

---

## When to Commit

Commit at logical checkpoints, not by time interval. A good commit is:
- Self-contained: the code compiles and tests pass.
- Purposeful: it does one thing (see commit types above).
- Describable in a single subject line.

Don't commit broken intermediate states unless you're using a WIP commit on a local-only branch that will be squashed before sharing.

---

## .gitignore Essentials

Every project should ignore at minimum:

```
# Build output
target/
build/
*.class

# IDE
.idea/
*.iml
.vscode/settings.json

# Runtime artifacts
*.log
*_save.json
```

Don't commit IDE project files or runtime save data.


---

## Branch Structure

| Branch | Purpose |
|--------|---------|
| `main` | Always stable. Represents the last reviewed, complete state. Never commit experimental or in-progress work here directly. |
| `prototype/active` | Long-running prototype branch. All development effort goes here. Work-item branches are short-lived and PR'd here via pull requests. Design documents, roadmaps, and findings live in the `design/` folder. |
| `prototype/work-item/{id}` | Short-lived work-item branch. Each backlog item is developed here and merged via PR. The existence of this branch on the remote signals the item is claimed. Branched from `prototype/active`; deleted after merge. Example: `prototype/work-item/BL-3`. |
| `rc/{name}` | Release candidate branch. Branched from `prototype/active` after RC eligibility is confirmed. Only stabilization and bug fixes. Example: `rc/alpha-1`. |
| `feature/{short-description}` | Isolated feature work not tied to a backlog item. Use sparingly. Example: `feature/concept-decay`. |
| `fix/{short-description}` | Bug fixes not captured in a backlog item. Example: `fix/effort-display-hardcoded`. |
| `chore/{short-description}` | Non-functional changes — dependency updates, refactoring, doc cleanup. Example: `chore/update-jackson`. |

### Branch Lifecycle

```
main
 ├── design                              ← always open; design docs live here
 ├── prototype/alpha                     ← milestone PRs merge here
 │    ├── prototype/alpha/m1             ← milestone branch; merged via PR
 │    ├── prototype/alpha/m2             ← milestone branch; merged via PR
 │    ├── feature/concept-decay          ← isolated features still supported
 │    ├── fix/effort-display
 │    └── rc/alpha-1                     ← cut from prototype after RC confirmed
 └── prototype/beta                      ← next prototype cycle
```

- `prototype/{name}/{milestone}` branches are short-lived. Delete them after the PR is merged.
- `design` is not merged wholesale into prototype branches. Design documents inform implementation but don't ship with it. However, individual design commits (e.g. a design document version bump) may be cherry-picked into a prototype branch to keep the working branch aware of the latest design context.
- `rc/{name}` branches are merged to `main` on release. Tag the merge commit with the release version.
- RC branches do not accept new features. If a needed feature surfaces during RC stabilization, it goes on the next prototype branch.

---

## Milestone PR Workflow

All milestone implementation work lives on `prototype/{name}/{milestone}` branches (e.g. `prototype/awakening/m4`). Milestones are delivered to the prototype branch via pull requests — never by committing directly to the prototype branch.

### Creating a Milestone Branch

1. Branch from the current head of `prototype/{name}`.
2. Name the branch `prototype/{name}/{milestone}`, where the milestone segment is a lowercase identifier matching the roadmap entry (e.g. `m1`, `m4`, `m4-era-progression`).
3. Implement the milestone on this branch using the standard commit message format.

### Opening a Pull Request

When the milestone's "Done when:" condition is satisfied and all tests pass:

1. Push the milestone branch to the remote.
2. Open a PR targeting `prototype/{name}`.
3. The PR description must include:
   - A reference to the ROADMAP milestone name and its "Done when:" condition.
   - The test results summary (number of tests passing).
   - A note confirming that the findings log was updated for this milestone.
4. Assign the PR for review.

### Ongoing PR Check-ins

After a PR is open, check in periodically to:

- **Respond to review comments** — acknowledge questions, clarify intent, and confirm planned changes.
- **Address flagged issues** — if a reviewer raises a concern, resolve it on the milestone branch and push. Delegate complex investigation or rework to a sub-agent, passing the sub-agent: the PR context, the specific comment, and the affected code. Confirm the sub-agent's output before pushing.
- **Do not re-open merged work** — if a concern surfaces after merge, log it as a finding in the findings log and address it through the appropriate scope (Rework finding, fix branch, etc.).

### Merging

Merge only after:

- At least one approval is recorded.
- All blocking review comments are resolved.
- All tests pass.

Use a **squash merge** if the milestone branch contains many fixup or WIP commits. Use a **merge commit** if the milestone commit history is meaningful and worth preserving. Delete the milestone branch after merging.

---

## Commit Message Format

```
{type}: {imperative description}

{optional body — explain WHY, not WHAT, if the change is not self-evident}
```

**Types:**

| Type | Use For |
|------|---------|
| `feat` | New functionality |
| `fix` | Bug fix |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `test` | Adding or updating tests |
| `chore` | Build config, dependencies, CI, formatting |
| `docs` | Documentation only changes |

**Rules:**
- Subject line: imperative mood, present tense, no period. Max ~72 characters.
- Subject line answers: "If applied, this commit will..." — complete that sentence.
- Body: explain *why* the change was made, not *what* it does (the diff shows what).
- Reference finding IDs when a commit addresses a findings log entry (e.g., `Addresses finding M2-5`).

**Examples:**

```
feat: add dynamic effort pool based on fitness and fatigue

Replaces the hardcoded effortBudget = 20 in DayState with a
per-character calculation driven by FitnessLevel and FatigueLevel
multipliers. Addresses finding M2-3.
```

```
fix: correct effort display showing "N / 10" when budget exceeds 10

Addresses finding M2-5.
```

```
docs: update ROADMAP to mark milestones 1-3 complete
```

---

## Merge Strategy

- **Design branch → main:** Merge commit when a design version is finalized. Preserve the design history.
- **Prototype branches → main:** Squash-merge or merge commit with a clear summary of the prototype phase. Do not rebase a long-running prototype branch.
- **Feature branches → prototype branch:** Either merge commit or squash, depending on whether the feature commit history is worth preserving.
- **RC branches → main:** Merge commit. Tag immediately with the release version: `git tag v{major}.{minor}.{patch}`.
- **Never force-push to `main`, `design`, or any `rc/` branch** — these are permanent reference points.

---

## When to Commit

Commit at logical checkpoints, not by time interval. A good commit is:
- Self-contained: the code compiles and tests pass.
- Purposeful: it does one thing (see commit types above).
- Describable in a single subject line.

Don't commit broken intermediate states unless you're using a WIP commit on a local-only branch that will be squashed before sharing.

---

## .gitignore Essentials

Every project should ignore at minimum:

```
# Build output
target/
build/
*.class

# IDE
.idea/
*.iml
.vscode/settings.json

# Runtime artifacts
*.log
*_save.json
```

Don't commit IDE project files or runtime save data.
