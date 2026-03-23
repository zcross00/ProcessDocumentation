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
2. Prototype work goes on a `prototype/{name}` branch, not `main` or `design`.
3. The `design/PROTOTYPE_FINDINGS.md` log should be updated as you build — findings accumulate continuously, not only at milestone completion.
4. Use the four finding categories exactly as defined: **Confirm**, **Rework**, **Abandon**, **Discuss**. Do not invent new categories.
5. After the resolution pass, evaluate RC eligibility using the criteria in the findings log's Release Candidate Status section before updating goals or the design document.

### When making commits or branches

1. Follow [`process/git-workflow.md`](process/git-workflow.md) for branch names and commit message format.

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
| Prototype branches named `prototype/{name}` | Never `prototype-name` or flat branch names (`process/git-workflow.md`) |
| Design documents live on `design` branch | Not on `main` or prototype branches (`process/git-workflow.md`) |
| RC branches named `rc/{name}` | Cut from prototype branch after RC eligibility confirmed (`process/git-workflow.md`) |

---

## Project-Specific Instructions

Each project may have a `.github/copilot-instructions.md` or similar file with project-specific overrides. Those take precedence over this repo for that project only. If they conflict with a general standard here, note the divergence — don't silently resolve it.

---

## When This Repo Needs Updating

If you encounter a pattern that works well and isn't documented, or discover that an existing standard is wrong or missing a case — surface it. Either update the relevant file here (if the change is clear and additive) or flag it in a comment for the developer to address.
