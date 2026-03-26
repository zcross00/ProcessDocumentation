---
name: git-workflow
description: "GIT WORKFLOW SKILL — Exact command sequences for all branch lifecycle operations in this project. USE FOR: claiming a backlog item (creating and pushing a work-item branch); submitting a work-item PR; cleaning up after merge; creating a SCRIBE or KEEPER fix branch; tagging iterations, design versions, or RC candidates. Eliminates the need to look up git-workflow.md during implementation. DO NOT USE FOR: deciding what to work on (use backlog-entry or agent-orientation); PR review (use pr-review skill)."
---

# Git Workflow

Exact command sequences for every branch lifecycle operation. Copy and substitute `{placeholders}`.

---

## Claiming a Work Item (BUILDER)

```bash
# 1. Confirm the item is unassigned
git fetch --prune
git branch -r | grep prototype/work-item/BL-{N}
# If the above returns output, the item is claimed — pick a different one.

# 2. Claim it
git checkout prototype/active
git pull origin prototype/active
git checkout -b prototype/work-item/BL-{N}
git push -u origin prototype/work-item/BL-{N}
# The pushed branch is the claim signal. Do this before any implementation.
```

---

## During Implementation (BUILDER)

Periodically sync with `prototype/active` to catch conflicts early:

```bash
git fetch origin
git merge origin/prototype/active
# Resolve any conflicts now; do not wait until the PR stage.
```

---

## Submitting a Work-Item PR (BUILDER)

```bash
# 1. Sync with any other in-flight branches that touch the same files
git fetch origin
# If other work-item branches exist and touch overlapping files, merge them:
git merge origin/prototype/work-item/BL-{other-N}

# 2. Final commit — remove the item from BACKLOG.md in this commit
git add .
git commit -m "{type}({scope}): {imperative description} — Closes BL-{N}"
git push origin prototype/work-item/BL-{N}

# 3. Create the PR on GitHub (pushing alone is not sufficient)
gh pr create \
  --repo {owner}/{repo} \
  --head "prototype/work-item/BL-{N}" \
  --base "prototype/active" \
  --title "BL-{N}: {synopsis from backlog}" \
  --body "$(cat <<'EOF'
## Summary
{Backlog item ID and synopsis}

## Done When
{Done-when condition from the item detail}

## Tests
{Test results summary — e.g., "All 47 tests pass. Added 4 new tests covering..."}

## Findings Updated
{Yes — {finding ref} added to PROTOTYPE_FINDINGS.md | No findings warranted}

Closes BL-{N}
EOF
)"

# 4. Verify the PR exists
gh pr list --state open
```

---

## After a Work-Item PR Merges (BUILDER)

```bash
git push origin --delete prototype/work-item/BL-{N}
git checkout prototype/active
git pull origin prototype/active
# Local branch cleanup (if policy allows: git branch -d prototype/work-item/BL-{N})
```

---

## Creating a SCRIBE PR

```bash
git checkout prototype/active
git pull origin prototype/active
git checkout -b prototype/scribe/{topic}
git push -u origin prototype/scribe/{topic}

# ... apply changes to the branch ...

git add .
git commit -m "docs({scope}): {description}"
git push origin prototype/scribe/{topic}

gh pr create \
  --repo {owner}/{repo} \
  --head "prototype/scribe/{topic}" \
  --base "prototype/active" \
  --title "SCRIBE: {topic}" \
  --body "{SCRIBE reasoning, recommendation, and what merging this approves}"

# After merge:
git push origin --delete prototype/scribe/{topic}
```

---

## Creating a KEEPER Fix PR

```bash
git checkout prototype/active
git pull origin prototype/active
git checkout -b prototype/fix/{short-description}
git push -u origin prototype/fix/{short-description}

# ... implement the fix ...

git add .
git commit -m "fix: {description} — addresses {standard cited}"
git push origin prototype/fix/{short-description}

gh pr create \
  --repo {owner}/{repo} \
  --head "prototype/fix/{short-description}" \
  --base "prototype/active" \
  --title "fix: {description}" \
  --body "Standards violation: {cite the rule from standards/}. Change: {what was changed and why}."

# After merge:
git push origin --delete prototype/fix/{short-description}
```

---

## Tagging

```bash
# Design version — when a design doc version is finalized
git tag design/v{N.M}
git push origin design/v{N.M}

# Iteration close — when all milestones done, findings resolved, backlog updated
git tag -a iteration/{name} -m "Complete {iteration-name}: {one-line summary}"
git push origin iteration/{name}

# RC eligibility — after SCRIBE RC PR merges
git tag -a rc/v{N.M} -m "RC eligible: {one-line rationale}"
git push origin rc/v{N.M}

# Move an RC tag (if further fixes land on prototype/active after tagging)
git push origin --delete rc/v{N.M}
git tag -d rc/v{N.M}
git tag -a rc/v{N.M} -m "RC eligible (updated): {one-line rationale}"
git push origin rc/v{N.M}

# Release — only when user explicitly instructs
git tag -a v{major}.{minor}.{patch} -m "Release {version}: {changelog summary}"
git push origin v{major}.{minor}.{patch}
```

---

## Checking Branch Claim Status

```bash
# Is a specific item claimed?
git fetch --prune
git branch -r | grep prototype/work-item/BL-{N}

# List all active work-item branches
git branch -r | grep prototype/work-item

# List all active SCRIBE branches
git branch -r | grep prototype/scribe
```

---

## Commit Message Format

```
{type}({optional-scope}): {imperative subject, ≤72 chars}

{optional body — why, not what; reference Closes BL-N here}
```

**Types:** `feat`, `fix`, `refactor`, `test`, `docs`, `chore`

Examples:
```
feat(engine): extract ThreatEngine from ResolutionEngine — Closes BL-5
fix(persistence): add @JsonIgnore to DayState.combatLog — Closes BL-1
docs(planning): add M5 builder items BL-17, BL-18, BL-19
refactor(era): add dirty-flag cache to EraEngine — Closes BL-11
```
