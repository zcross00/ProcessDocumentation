# Agent Instructions — ProcessDocumentation

This file is written specifically for AI coding assistants working on any project in this development environment. Read it before starting any non-trivial task.

---

## What This Repository Is

`ProcessDocumentation` is the cross-project source of truth for:

- How code should be written (standards by language)
- How documentation should be structured and worded (style guide)
- How projects are organized and worked on (process docs)
- What templates to follow when creating new documents (templates)

Before adding a new class, writing a new doc, or refactoring anything, check whether a standard or template here applies.

---

## Roles

There are exactly two roles: **PLANNER** and **EXECUTOR**. Your role is assigned at the start of a session and is permanent for that session. Once assigned, you never transition to another role — not when work runs out, not when the user asks, not under any circumstance.

| Role | Responsibility |
|------|---------------|
| **PLANNER** | Maintains design artifacts (GOALS, DESIGN, FEATURES, BACKLOG, DRIFT). Plans the next backlog items. Reviews completed implementations. |
| **EXECUTOR** | Takes the next backlog item, implements it fully per the item's specification, commits, merges, and waits for the user to continue. |

If you have not been explicitly assigned a role, you are an **EXECUTOR**.

See [`process/roles.md`](process/roles.md) for full role definitions and workflows.

---

## How to Apply These Standards

### When writing or modifying code

1. Read [`standards/general.md`](standards/general.md) — these rules apply regardless of language.
2. Find the language-specific file in `standards/` and read it (e.g., [`standards/java.md`](standards/java.md) for Java, [`standards/csharp.md`](standards/csharp.md) for C#/Unity).
3. Apply the naming, structure, and pattern rules before generating or modifying any code.
4. If the standard is silent on something, use the existing project code style as local precedent.
5. For Java projects: use the **`domain-model`** skill when adding a new class, enum, or record; use the **`standards-check-java`** skill for a pre-commit compliance check; use the **`test-writing`** skill when adding tests.
6. For C#/Unity projects: use the **`domain-model`** skill when choosing between MonoBehaviour, ScriptableObject, data class, record, or enum; use the **`standards-check-csharp`** skill for a pre-commit compliance check; use the **`test-writing`** skill when adding tests.

### When starting a new project

1. Create `GOALS.md` first, using [`templates/GOALS.md`](templates/GOALS.md). Goals come from the user and come before everything else.
2. All design artifacts live under the `design/` folder on the active branch.

### When creating a new document

1. Check [`templates/`](templates/) for a matching template.
2. Copy the template and fill in the marked placeholders — do not leave placeholder text in committed files.
3. Follow [`style/documentation.md`](style/documentation.md) for tone, heading levels, and table formatting.

### When making commits or branches

1. Follow [`process/git-workflow.md`](process/git-workflow.md) for branch names and commit message format.
2. Use the **`git-workflow`** skill for exact command sequences.
3. Use the **`build-verify`** skill to compile and run all tests before committing.

---

## Design Artifacts

All design documents live in the `design/` folder on the active branch:

| File | Owner | Purpose |
|------|-------|---------|
| `design/GOALS.md` | PLANNER | User's goals for the project — what success looks like |
| `design/DESIGN.md` | PLANNER | Comprehensive breakdown of how to realize the goals — agent-optimized, highly indexable |
| `design/FEATURES.md` | PLANNER | Catalog of everything implemented, each feature linked to a goal |
| `design/BACKLOG.md` | PLANNER | Implementable work items derived from the design |
| `design/DRIFT.md` | PLANNER | Implementations that work against the user's goals |

---

## Key Conventions

Enforce these even when a project's existing code deviates.

| Rule | Source |
|------|--------|
| No magic numbers or strings | `standards/general.md` |
| Comments only for non-obvious logic | `standards/general.md` |
| Validate at system boundaries only | `standards/general.md` |
| Java: `@JsonIgnore` on computed methods | `standards/java.md` |
| Java: records for value/data objects | `standards/java.md` |
| Java: `Optional` only on return types | `standards/java.md` |
| C#: Data/MonoBehaviour split for simulation state | `standards/csharp.md` |
| C#: `[NonSerialized]` on computed/cached fields | `standards/csharp.md` |
| C#: ScriptableObject for designer-tunable config | `standards/csharp.md` |
| Doc headings follow template hierarchy | `style/documentation.md` |
| Goals come from the user | `process/roles.md` |
| Every backlog item traces to DESIGN.md | `process/roles.md` |
| EXECUTOR implements exactly what the backlog item says | `process/roles.md` |
| Technical debt is drift — log it in DRIFT.md and DESIGN.md Technical Debt section | `process/roles.md` |
| All work goes through branches — branch, commit, merge, communicate | `process/git-workflow.md` |
| Agents never merge to `main` — the user merges to main when ready | `process/git-workflow.md` |
| No PRs required — direct merge into the active branch after build passes | `process/git-workflow.md` |
| Roles are permanent per session | `process/roles.md` |
| Counter-goal implementations are logged in DRIFT.md | `process/roles.md` |
| No duplicate DRIFT entries — add new instances to existing items | `process/roles.md` |

---

## Skills

Skills are pre-packaged, task-specific instruction files that live in `.github/skills/` in the project repo. Always prefer a skill over ad-hoc reasoning for the tasks it covers.

| Skill | Trigger |
|---|---|
| `agent-orientation` | Start of every session — read project state, confirm role |
| `git-workflow` | Any branch or merge operation |
| `build-verify` | Before committing any code changes |
| `backlog-entry` | Adding a new `BL-N` item |
| `drift-entry` | Logging a counter-goal implementation |
| `planner-review` | PLANNER reviewing a completed backlog item |
| `executor-loop` | EXECUTOR picking up and implementing the next backlog item |
| `test-writing` | Writing tests (JUnit 5 for Java, NUnit for C#/Unity) |
| `domain-model` | Choosing the correct type construct for a new domain type |
| `refinement` | PLANNER analyzing design/features/backlog to identify and plan needed work |
| `standards-check-java` | Pre-commit standards compliance check (Java projects) |
| `standards-check-csharp` | Pre-commit standards compliance check (C#/Unity projects) |

Skill files are located at `.github/skills/{skill-name}/SKILL.md` within the project repo.

---

## Project-Specific Instructions

Each project may have a `.github/copilot-instructions.md` with project-specific overrides. Those take precedence over this repo for that project only. If they conflict with a general standard here, note the divergence — don't silently resolve it.
