---
name: pr-review
description: "PR REVIEW SKILL — Apply the full standards/pr.md checklist to a pull request. USE FOR: reviewing any PR targeting prototype/active before approving or requesting changes; self-reviewing a work-item PR before submitting; KEEPER reviewing open PRs during a maintenance sweep. Produces a structured verdict with category-by-category findings. DO NOT USE FOR: reviewing SCRIBE PRs (use normal judgment — SCRIBE PRs are directional proposals, not code changes)."
---

# PR Review

Apply this checklist to every PR before assigning a verdict. Work through categories in order. Assign a **Pass**, **Comment**, or **Block** to each before moving on.

---

## Pre-Check

Before starting the checklist, confirm:
- The PR has a description referencing a `BL-N` item (or a KEEPER fix with a `prototype/fix/` branch). If neither is present, **request the PR description be updated before reviewing anything else**.
- The branch name matches `prototype/work-item/{BL-N}` or `prototype/fix/{description}` or `prototype/scribe/{topic}`.

---

## Checklist

### 1. Scope
**Does the PR do exactly what the backlog item says, and nothing more?**

- Read the work item's Detail section. Then read the diff. Do they match?
- Changes outside scope — even improvements — are **scope creep**. Flag them. If worthwhile, they must become a new `BL-N` entry.
- Exception: trivial single-line fixes (typo in adjacent comment) are acceptable if noted in the PR description.

**Block if:** diff contains unrelated changes without explanation.

---

### 2. Correctness
**Does the implementation actually solve the stated problem?**

- Re-read the done-when condition. Does the code, as written, satisfy it?
- Trace the code path: does behavior match intent?
- Look for: off-by-one errors, null paths, unchecked input assumptions, incorrect condition direction (`<` vs `<=`).
- Verify edge cases called out in the item Detail are handled.

**Block if:** implementation does not satisfy the done-when condition, or a reachable code path produces incorrect behavior.

---

### 3. Design Alignment
**Does the implementation match the design document?**

- Cross-reference changed systems with the relevant section of `design/GAME_DESIGN_DOCUMENT.md`.
- Implementation that contradicts described behavior is potential CODE-DRIFT. Flag it.
- If the design doc is incomplete on this point, note it — the SCRIBE needs to know there is a gap.

**Block if:** code contradicts a clear design statement with no finding or rationale explaining the divergence.

---

### 4. Architecture
**Is this the right structural solution?**

- Could this change have been made in fewer places?
- Does it respect existing layer boundaries? (domain ← engine ← ui/persistence; no outward deps from domain)
- Is a new abstraction justified? Does it simplify surrounding code or add indirection without payback?
- Is a class referencing more than ~5 other types? It may be doing too much.

**Comment if:** a simpler approach exists.  
**Block if:** layer violation or responsibility conflation that will compound with future work.

---

### 5. Code Quality

#### Naming
- Names answer "what is this?" without requiring context.
- No `Manager`, `Handler`, `Util`, or `Helper` classes introduced without clear justification.

#### Size
- Methods exceeding ~30 lines are likely doing more than one thing. Flag it.
- New classes already large on introduction (>200 lines) warrant scrutiny.

#### Magic Values
- No unexplained numeric or string literals in logic.
- Constants are named for *why* the value exists, not what it is.

#### Comments
- Comments explain decisions and constraints, not what the code does.

#### Java-specific
- Records used for value/data objects (no mutable state, no complex behavior).
- `@JsonIgnore` on all computed or transient methods.
- `Optional` only on return types, never as parameters or fields.
- Enum constants for typed values — no raw string comparisons for domain types.

---

### 6. Security
- **Injection:** User input or external data used in file paths or system calls — is it sanitized?
- **Unvalidated boundary data:** External input validated before use?
- **Serialization exposure:** New fields on serialized classes — intentionally serialized? Should be `@JsonIgnore`?
- **Hardcoded credentials:** Must never appear in source.

**Block if:** any of the above are present.

---

### 7. Tests
- Verifiable behaviors described in the item Detail have test coverage.
- Tests fail if the behavior they test breaks. No unconditional-pass tests.
- Test method names read as sentences describing expected behavior.
- No existing tests broken without justification.

**Comment if:** coverage clearly missing for verifiable behavior.  
**Block if:** existing tests broken without justification.

---

### 8. Findings and Documentation
- If the implementation revealed findings worth logging (Confirm, Rework, TC), verify `design/PROTOTYPE_FINDINGS.md` was updated.
- If this work resolves DRIFT, verify `design/KNOWN_DRIFT.md` was updated.
- `design/STATUS.md` is SCRIBE-handled post-merge — do not block on this.

**Comment if:** observable design findings are absent and the author is unlikely to circle back.

---

### 9. Commit and PR Hygiene
- Commit messages use the type-subject format from `process/git-workflow.md`.
- PR description includes: backlog item ID, done-when condition, test results summary, `Closes BL-N`.
- Work item removed from `BACKLOG.md` in the final commit.
- Branch name matches `prototype/work-item/{BL-N}`.

**Comment if:** hygiene issues present but don't affect the change.  
**Block if:** `Closes BL-N` missing or work item not removed from `BACKLOG.md`.

---

## Verdict

| Verdict | When |
|---------|------|
| **Approve** | All categories pass. Ready to merge. |
| **Comment** | No blocking issues; improvements noted. Author may merge after acknowledging. |
| **Request Changes** | One or more Block conditions present. Must not merge until resolved. |

When uncertain whether something rises to a Block, default to Request Changes and explain the concern clearly.

---

## DRIFT Identification During Review

Any reviewer may identify drift. If the implementation — while correct per its work item — diverges from the design in a way that conflicts with stated goals, flag it with a `CODE-DRIFT` or `DESIGN-DRIFT` comment. Do not block the PR for this alone unless the divergence is severe; instead, ensure a DRIFT entry gets filed.
