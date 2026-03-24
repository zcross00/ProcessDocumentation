# Process — Git Workflow

Standard branch naming, commit message format, tagging, and merge strategy for all projects.

---

## Branch Structure

There are two long-lived branches. Everything else is short-lived and deleted after delivery.

| Branch | Purpose |
|--------|---------|
| `main` | Always stable. Represents the version in users' hands — the last shipped, reviewed release. Never commit work-in-progress or prototype code here directly. Updated only when a completed prototype iteration is ready for users. |
| `prototype/active` | The single active prototype branch. All current development effort goes here. Long-running; survives across multiple design iterations. Milestone branches are short-lived and PR'd into this branch. Design documents, roadmaps, and findings live in the `design/` folder on this branch — they travel with the code. |
| `prototype/{random-noun}` | A parallel prototype branch, created only when a second prototype direction needs to run concurrently alongside `prototype/active`. Example: `prototype/thornwood`. Rare — most work stays in `prototype/active`. |
| `prototype/{milestone-name}` | Short-lived milestone branch. Each milestone is developed here and delivered to `prototype/active` via a pull request — never by direct commit. Branched from the current head of `prototype/active`. Example: `prototype/era-hysteresis`. Delete after merge. |
| `feature/{short-description}` | Isolated feature work, branched off `prototype/active`. Use for self-contained units of functionality that aren't tied to a specific roadmap milestone. Example: `feature/concept-decay`. |
| `fix/{short-description}` | Bug fixes. Branched off `prototype/active`. Example: `fix/scroll-pane-skin`. |
| `chore/{short-description}` | Non-functional changes — dependency updates, refactoring, doc cleanup. Example: `chore/update-jackson`. |

### Branch Lifecycle

```
main                                    ← stable; tagged on every update
 └── prototype/active                   ← long-lived; all development happens here
      ├── prototype/era-hysteresis      ← milestone branch; merged via PR
      ├── prototype/trade-picker        ← milestone branch; merged via PR
      ├── feature/threat-engine-extract ← isolated feature; merged via PR
      └── fix/scroll-pane-skin          ← bug fix; merged via PR

prototype/thornwood                     ← parallel prototype, if ever needed
```

- Milestone and feature branches are short-lived. Delete after the PR is merged.
- Design documents (`GOALS.md`, `GAME_DESIGN_DOCUMENT.md`, `ROADMAP.md`, `PROTOTYPE_FINDINGS.md`, `BACKLOG.md`) live in the `design/` folder on `prototype/active`. They are updated directly — no separate design branch.
- `main` is only updated when the iteration is ready for users. See Merge Strategy below.

---

## Tags

Tags are the permanent record of meaningful states. They replace long-lived branches as state captures.

| Tag | When to Create | Example |
|-----|---------------|---------|
| `design/v{N.M}` | When a design document version is finalized and committed to `prototype/active`. | `design/v0.2` |
| `iteration/{name}` | When a prototype iteration is complete: all milestones done, findings resolved, BACKLOG updated. Tag the final commit on `prototype/active`. | `iteration/prototype-awakening` |
| `v{major}.{minor}.{patch}` | When production-ready work is merged to `main`. Tag the merge commit. | `v1.0.0` |

**Rules:**
- Tags are permanent. Never delete or move a tag after it is pushed.
- Annotated tags are preferred for `iteration/` and `v` releases: `git tag -a iteration/{name} -m "{message}"`.
- Lightweight tags are acceptable for `design/` snapshots.

---

## Milestone PR Workflow

All milestone implementation work lives on `prototype/{milestone-name}` branches. Milestones are delivered to `prototype/active` via pull requests — never by committing directly.

### Creating a Milestone Branch

1. Branch from the current head of `prototype/active`.
2. Name the branch `prototype/{milestone-name}`, using a short lowercase identifier matching the roadmap entry.
3. Implement the milestone on this branch using the standard commit message format.

### Opening a Pull Request

When the milestone's "Done when:" condition is satisfied and all tests pass:

1. Push the milestone branch to the remote.
2. Open a PR targeting `prototype/active`.
3. The PR description must include:
   - A reference to the ROADMAP milestone name and its "Done when:" condition.
   - The test results summary (number of tests passing).
   - A note confirming that the findings log was updated for this milestone.
4. Assign the PR for review.

### Ongoing PR Check-ins

After a PR is open, check in periodically to:

- **Respond to review comments** — acknowledge questions, clarify intent, and confirm planned changes.
- **Address flagged issues** — if a reviewer raises a concern, resolve it on the milestone branch and push. Delegate complex investigation or rework to a sub-agent, passing the sub-agent: the PR context, the specific comment, and the affected code. Confirm the sub-agent's output before pushing.
- **Do not re-open merged work** — if a concern surfaces after merge, log it as a finding in the findings log or a backlog item.

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

- **Milestone / feature / fix branches → `prototype/active`:** PR required. Squash merge or merge commit depending on whether commit history is worth preserving. Delete the branch after merge.
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
| `design` | Long-running branch for all design documents and goals. All design-adjacent documents — `GOALS.md`, `DESIGN_DOCUMENT.md`, `ROADMAP.md`, `PROTOTYPE_FINDINGS.md`, `BALANCE_NOTES.md` — live under the `design/` folder and are updated here. Merged to `main` when a design version is finalized. |
| `prototype/{name}` | Prototype phase branch. Branched from `main` for a fresh start, or from the previous prototype branch for a continuation iteration. Long-running; milestone branches are merged here via pull requests. Merged to `main` when the prototype is complete and reviewed. Example: `prototype/alpha`. |
| `prototype/{name}/{milestone}` | Milestone work branch within a prototype phase. Each milestone is developed here and delivered to the parent prototype branch via a pull request — never by direct commit. Branched from the current head of `prototype/{name}`. Example: `prototype/awakening/m4`. |
| `rc/{name}` | Release candidate branch. Branched from the corresponding prototype branch after RC eligibility is confirmed in the findings log. Only stabilization, polish, and bug fixes go here — no new features. Example: `rc/alpha-1`. |
| `feature/{short-description}` | Isolated feature work, branched off the active prototype branch. Use for self-contained units of functionality. Example: `feature/concept-decay`. |
| `fix/{short-description}` | Bug fixes. Example: `fix/effort-display-hardcoded`. |
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
