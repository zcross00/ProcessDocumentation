# Agent Instructions — ProcessDocumentation

This file is written specifically for AI coding assistants working on any project in this development environment. Read it before starting any non-trivial implementation task.

---

## What This Repository Is

`ProcessDocumentation` is the cross-project source of truth for:

- How code should be written (standards by language)
- How documentation should be structured and worded (style guide)
- How projects are organized and iterated on (process docs)
- What templates to follow when creating new documents (templates)

Before adding a new class, writing a new doc, or refactoring anything, check whether a standard or template here applies.

---

## How to Apply These Standards

### When writing or modifying code

1. Read [`standards/general.md`](standards/general.md) — these rules apply regardless of language.
2. Find the language-specific file in `standards/` and read it (e.g., [`standards/java.md`](standards/java.md) for Java work).
3. Apply the naming, structure, and pattern rules described before generating or modifying any code.
4. If the standard is silent on something, use the existing project code style as the local precedent.
5. For Java projects: use the **`domain-model`** skill when adding a new class, enum, or record; use the **`standards-check-java`** skill for a pre-PR compliance check; use the **`test-writing`** skill when adding tests.

### When starting a new project

1. Create `GOALS.md` first, using [`templates/GOALS.md`](templates/GOALS.md). Goals come before design.
2. Goals go on the `design` branch. Create it from `main` before committing any design documents.
3. All design-adjacent documents — design document, goals, roadmap, findings log, balance notes — live under the `design/` folder on the `design` branch.

### When creating a new document

1. Check [`templates/`](templates/) for a matching template.
2. Copy the template and fill in the marked placeholders — do not leave placeholder text in committed files.
3. Follow [`style/documentation.md`](style/documentation.md) for tone, heading levels, and table formatting.

### When starting or continuing a prototype

1. Read [`process/design-iteration.md`](process/design-iteration.md) to understand the full cycle: goals → design → prototype → findings → RC evaluation → goals re-eval → updated design.
2. All prototype work happens on `prototype/active`. There is no separate design branch. Design documents live in the `design/` folder on `prototype/active`.
3. Work items are developed on short-lived `prototype/work-item/{BL-N}` branches and merged into `prototype/active` via PR — never direct commits. The existence of the remote branch signals that the item is claimed. The only exception is logging a DRIFT item — see rule below.
4. Before starting any backlog item, verify that a `prototype/work-item/{BL-N}` branch does not already exist remotely. If it does, the item is claimed by another agent — skip it and pick the next unblocked item.
5. Every item described in the roadmap must have a corresponding `BL-N` entry in `design/BACKLOG.md` before implementation begins. Priority aligns with roadmap milestone order.
6. The `design/PROTOTYPE_FINDINGS.md` log should be updated as you build — findings accumulate continuously, not only at work-item completion.
7. Use the four finding categories exactly as defined: **Confirm**, **Rework**, **Abandon**, **Discuss**. Do not invent new categories.
8. Log architectural and structural concerns in the **Technical Concerns** section of the findings log using `[TC-N]` tags. Distinguish **[Pressing]** (address next iteration) from **[Future]** (track for later).
9. After the resolution pass, evaluate RC eligibility using the criteria in the findings log's Release Candidate Status section before updating goals or the design document. RC eligibility does not automatically trigger a `main` merge — that is a separate release decision.
10. At iteration end: tag `prototype/active` with `iteration/{name}`, update `design/BACKLOG.md`, clear the findings log, and write the new roadmap. Ensure every item in the new roadmap has a corresponding `BL-N` backlog entry.

### When making commits or branches

1. Follow [`process/git-workflow.md`](process/git-workflow.md) for branch names and commit message format.
2. Use the **`git-workflow`** skill for exact command sequences — including how to resolve `{owner}/{repo}` and diagnose PR publishing failures.
3. Use the **`build-verify`** skill to compile and run all tests before opening any PR.

---

## Key Conventions to Enforce Consistently

These are the highest-priority rules. Enforce them even when a project's existing code deviates.

| Rule | Standard |
|------|----------|
| No magic numbers or strings | Use named constants or enums (`standards/general.md`) |
| Comments only for non-obvious logic | Never comment what the code already says (`standards/general.md`) |
| Validate at system boundaries only | Trust internal code; validate user input and external data (`standards/general.md`) |
| Java: `@JsonIgnore` on computed methods | Don't serialize derived state (`standards/java.md`) |
| Java: records for value/data objects | Use records for domain data carriers without behavior (`standards/java.md`) |
| Java: `Optional` only on return types | Never use `Optional` as a method parameter (`standards/java.md`) |
| Doc headings follow template hierarchy | Don't invent new heading levels within a template (`style/documentation.md`) |
| Goals defined in GOALS.md before design begins | Define goals first, then derive design pillars from them (`templates/GOALS.md`) |
| Goals re-evaluated after every prototype resolution pass | Check alignment before updating the design document (`process/design-iteration.md`) |
| Active prototype branch is `prototype/active` | All development goes here; never commit work-item work directly to this branch (`process/git-workflow.md`) |
| Work-item branches named `prototype/work-item/{BL-N}` | Short-lived; branch from `prototype/active`, push immediately to claim the item, merged via PR, deleted after merge (`process/git-workflow.md`) |
| Delete work-item branch after PR merge | After merge, delete both the remote and local work-item branch immediately; do not leave stale branches on the remote (`process/git-workflow.md`) |
| Remove resolved backlog item on merge | Delete the item's table row and detail block from `BACKLOG.md` as part of the final PR commit; reference `Closes BL-N` in the commit message (`process/git-workflow.md`) |
| Merge available work-item commits before submitting PR | Before opening a PR, fetch and merge commits from other open work-item branches that touch the same files; resolve conflicts before pushing (`process/git-workflow.md`) |
| BUILDERs actively watch for merge conflicts | Pull `prototype/active` during implementation and rebase/merge to catch conflicts early, not at PR time (`process/git-workflow.md`) |
| SCRIBE always works from `prototype/active` | All SCRIBE direct commits and SCRIBE PR branches originate from `prototype/active`; never from a work-item or other branch (`process/roles.md`) |
| Always continue after task completion | After completing any task — PR open, PR merged, review done — immediately proceed to the next step in the loop without pausing for direction (`process/git-workflow.md`) |
| Remote branch existence = item claimed | Before picking up a backlog item, run `git fetch --prune` and check for an existing remote branch — skip if found (`process/git-workflow.md`) |
| Open a PR after each work item; merge only after approval | Follow `standards/pr.md`; delegate complex review issues to a sub-agent; do not merge with blocking comments unresolved (`process/git-workflow.md`) |
| PR must be created on GitHub, not just pushed | Pushing a branch is not sufficient — use `gh pr create` to create the PR on GitHub; verify with `gh pr list` before continuing; the PR must be visible on GitHub to be reviewed and merged (`process/git-workflow.md`) |
| Every roadmap item must have a `BL-N` backlog entry | Roadmap describes shape; backlog contains operational detail; no implementation without a backlog entry (`templates/BACKLOG.md`, `templates/ROADMAP.md`) |
| BUILDER transitions to SCRIBE when backlog is exhausted | No unassigned, unblocked items remain = transition to SCRIBE; SCRIBE exhausted = transition to KEEPER; KEEPER exhausted = back to BUILDER; see role waterfall in `process/roles.md` |
| KEEPER fix branches named `prototype/fix/{description}` | Short-lived; branch from `prototype/active`, submit PR, delete after merge; used for KEEPER standards compliance fixes (`process/git-workflow.md`, `process/roles.md`) |
| RC state is a tag, not a branch | RC eligibility = `rc/v{N.M}` tag on `prototype/active`; tag is mutable until release; no separate RC branch (`process/git-workflow.md`) |
| SCRIBE communicates via PR | All SCRIBE directional proposals (new iteration, RC evaluation, DRIFT resolution, idea disposition, goals/design updates) are delivered as `prototype/scribe/{topic}` PRs; merging = approval (`process/roles.md`) |
| Ideas are surfaced, not implemented | BUILDER flags `[Idea]` entries in findings; SCRIBE collects and cross-checks with user before any action (`process/roles.md`) |
| BUILDERs make no direct commits | All BUILDER output (code, BACKLOG updates, findings, drift entries) travels through a work-item PR — no direct commits to `prototype/active` (`process/roles.md`, `process/git-workflow.md`) |
| DRIFT entries go in the work-item PR | BUILDERs add `CODE-DRIFT` / `DESIGN-DRIFT` entries to `BACKLOG.md` on their work-item branch; they land on `prototype/active` when the PR merges (`process/git-workflow.md`) |
| Resolved DRIFT is logged to `KNOWN_DRIFT.md` | SCRIBE adds DESIGN-DRIFT on resolution commit; BUILDER adds CODE-DRIFT on PR merge; log is never cleared (`process/roles.md`) |
| `design/STATUS.md` is SCRIBE-only | SCRIBE updates STATUS via direct commit after every merge; BUILDERs do not touch STATUS.md; a stale STATUS is a SCRIBE failure (`process/roles.md`) |
| Read access is unrestricted by role | SCRIBEs may inspect code for design adherence; BUILDERs may inspect design docs for consistency; roles define what you produce, not what you read (`process/roles.md`) |
| Design documents live in `design/` on `prototype/active` | No separate design branch; no cherry-picking design commits (`process/git-workflow.md`) |
| Tag design versions: `design/v{N.M}` | Tag on `prototype/active` when a design doc version is finalized (`process/git-workflow.md`) |
| Tag iterations: `iteration/{name}` | Tag final commit of each iteration on `prototype/active` before clearing findings (`process/git-workflow.md`) |
| `main` only receives a merge when shipping to users | RC eligibility ≠ automatic `main` merge; merging to `main` is an explicit release decision (`process/git-workflow.md`) |
| `design/BACKLOG.md` is the planning backlog | Use `BL-N` identifiers; `Roadmap-Ref`, `Dependencies`, and `Branch` fields required per item; update after each iteration; reference in commit messages (`templates/BACKLOG.md`) |

---

## Skills

Skills are pre-packaged, task-specific instruction files that live in `.github/skills/` in the project repo. They contain exact steps, commands, and decision rules for common workflows. Always prefer a skill over ad-hoc reasoning for the tasks it covers.

| Skill | Trigger |
|---|---|
| `agent-orientation` | Start of every session |
| `git-workflow` | Any branch, PR, or tagging operation |
| `build-verify` | Before opening any PR |
| `pr-review` | Reviewing a PR targeting `prototype/active` |
| `backlog-entry` | Adding a new `BL-N` item |
| `findings-entry` | Logging a finding to `PROTOTYPE_FINDINGS.md` |
| `drift-logging` | Classifying a code/design inconsistency |
| `scribe-duties` | Starting a SCRIBE session |
| `keeper-sweep` | Starting a KEEPER session |
| `test-writing` | Writing new JUnit 5 tests (Java projects) |
| `domain-model` | Choosing JavaBean vs. record vs. enum (Java projects) |
| `standards-check-java` | Pre-PR or KEEPER standards compliance check (Java projects) |

Skill files are located at `.github/skills/{skill-name}/SKILL.md` within the project repo.

---

## Project-Specific Instructions

Each project may have a `.github/copilot-instructions.md` or similar file with project-specific overrides. Those take precedence over this repo for that project only. If they conflict with a general standard here, note the divergence — don't silently resolve it.

---

## When This Repo Needs Updating

If you encounter a pattern that works well and isn't documented, or discover that an existing standard is wrong or missing a case — surface it. Either update the relevant file here (if the change is clear and additive) or flag it in a comment for the developer to address.
