# Process — Git Workflow

Standard branch naming, commit message format, and merge strategy for all projects.

---

## Branch Structure

| Branch | Purpose |
|--------|---------|
| `main` | Always stable. Represents the last reviewed, complete state. Never commit experimental or in-progress work here directly. |
| `design` | Long-running branch for all design documents and goals. All design-adjacent documents — `GOALS.md`, `DESIGN_DOCUMENT.md`, `ROADMAP.md`, `PROTOTYPE_FINDINGS.md`, `BALANCE_NOTES.md` — live under the `design/` folder and are updated here. Merged to `main` when a design version is finalized. |
| `prototype/{name}` | Prototype phase branch. Branched from `main` for a fresh start, or from the previous prototype branch for a continuation iteration. Long-running; all milestone implementation work happens here. Merged to `main` when the prototype is complete and reviewed. Example: `prototype/alpha`. |
| `rc/{name}` | Release candidate branch. Branched from the corresponding prototype branch after RC eligibility is confirmed in the findings log. Only stabilization, polish, and bug fixes go here — no new features. Example: `rc/alpha-1`. |
| `feature/{short-description}` | Isolated feature work, branched off the active prototype branch. Use for self-contained units of functionality. Example: `feature/concept-decay`. |
| `fix/{short-description}` | Bug fixes. Example: `fix/effort-display-hardcoded`. |
| `chore/{short-description}` | Non-functional changes — dependency updates, refactoring, doc cleanup. Example: `chore/update-jackson`. |

### Branch Lifecycle

```
main
 ├── design                          ← always open; design docs live here
 ├── prototype/alpha                 ← feature branches merge here
 │    ├── feature/concept-decay
 │    ├── fix/effort-display
 │    └── rc/alpha-1                 ← cut from prototype after RC confirmed
 └── prototype/beta                  ← next prototype cycle
```

- `design` is not merged wholesale into prototype branches. Design documents inform implementation but don't ship with it. However, individual design commits (e.g. a design document version bump) may be cherry-picked into a prototype branch to keep the working branch aware of the latest design context.
- `rc/{name}` branches are merged to `main` on release. Tag the merge commit with the release version.
- RC branches do not accept new features. If a needed feature surfaces during RC stabilization, it goes on the next prototype branch.

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
