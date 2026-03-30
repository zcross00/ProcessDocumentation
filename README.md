# ProcessDocumentation

This repository is the **source of truth** for coding standards, documentation style, process workflows, and document templates across all projects. It applies to every repository in this development environment.

Both human developers and AI coding agents should consult this repository before starting work on any project.

---

## What's Here

| Directory | Purpose |
|-----------|---------|
| [`standards/`](standards/) | Coding conventions and language-specific rules |
| [`process/`](process/) | Workflow processes: roles, git workflow |
| [`templates/`](templates/) | Reusable document templates for projects |
| [`style/`](style/) | Writing and formatting rules for all documentation |

---

## For AI Coding Agents

See [`AGENTS.md`](AGENTS.md) — this file is written specifically for AI assistants and explains how to apply these standards when working on any project.

---

## Roles

There are two roles: **PLANNER** and **EXECUTOR**. See [`process/roles.md`](process/roles.md) for definitions.

---

## Standards

| File | Covers |
|------|--------|
| [`standards/general.md`](standards/general.md) | Language-agnostic principles valid for all code |
| [`standards/java.md`](standards/java.md) | Java 21 naming, structure, patterns, and idioms |

---

## Process

| File | Covers |
|------|--------|
| [`process/roles.md`](process/roles.md) | PLANNER and EXECUTOR role definitions and workflows |
| [`process/git-workflow.md`](process/git-workflow.md) | Branch naming, commit messages, merge strategy |

---

## Templates

All templates live in [`templates/`](templates/). When starting a new project or adding a design artifact, copy the relevant template and fill in the placeholders.

| Template | Use For |
|----------|---------|
| [`templates/GOALS.md`](templates/GOALS.md) | Project goals — user-defined value statements |
| [`templates/DESIGN.md`](templates/DESIGN.md) | Comprehensive design breakdown — agent-optimized |
| [`templates/FEATURES.md`](templates/FEATURES.md) | Catalog of implemented features linked to goals |
| [`templates/BACKLOG.md`](templates/BACKLOG.md) | Work items derived from the design |
| [`templates/DRIFT.md`](templates/DRIFT.md) | Counter-goal implementation log |
