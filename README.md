# ProcessDocumentation

This repository is the **source of truth** for coding standards, documentation style, process workflows, and document templates across all projects. It applies to every repository in this development environment.

Both human developers and AI coding agents should consult this repository before starting work on any project.

---

## What's Here

| Directory | Purpose |
|-----------|---------|
| [`standards/`](standards/) | Coding conventions and language-specific rules |
| [`process/`](process/) | Workflow processes: design iteration, prototyping, git |
| [`templates/`](templates/) | Reusable document templates for new projects and phases |
| [`style/`](style/) | Writing and formatting rules for all documentation |

---

## For AI Coding Agents

See [`AGENTS.md`](AGENTS.md) — this file is written specifically for AI assistants and explains how to apply these standards when working on any project in this development environment.

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
| [`process/design-iteration.md`](process/design-iteration.md) | The full design → prototype → findings → iteration cycle |
| [`process/git-workflow.md`](process/git-workflow.md) | Branch naming, commit messages, merge strategy |

---

## Templates

All templates live in [`templates/`](templates/). Filenames match the document type they represent. When starting a new project or phase, copy the relevant template and fill in the placeholders.

| Template | Use For |
|----------|---------|| [`templates/GOALS.md`](templates/GOALS.md) | Project goals — high-level value statements, versioned || [`templates/PROJECT_README.md`](templates/PROJECT_README.md) | Project-level README |
| [`templates/DESIGN_DOCUMENT.md`](templates/DESIGN_DOCUMENT.md) | Design / concept document (GDD, product spec, etc.) |
| [`templates/ROADMAP.md`](templates/ROADMAP.md) | Milestone-based implementation roadmap |
| [`templates/PROTOTYPE_FINDINGS.md`](templates/PROTOTYPE_FINDINGS.md) | Prototype observation and findings log |

---

## Style Guide

| File | Covers |
|------|--------|
| [`style/documentation.md`](style/documentation.md) | Tone, heading hierarchy, table rules, sentence style for all markdown documents |

---

## Maintenance

This repository evolves alongside the projects it serves. When a pattern works consistently or a new convention is established:

1. Update the relevant standard or process document here first.
2. Reference the change in the affected project's commit or PR message.
3. Treat divergence from these standards in any project as technical debt to be noted and resolved.
