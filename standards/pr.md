# Standards — Pull Request Review

This document defines the standard for reviewing pull requests targeting `prototype/active`. It applies to all reviewers — agents and humans alike. When in doubt, start strict and loosen only when there is a concrete reason to.

A PR review is an external perspective on the change: does it make sense, is it the right solution, and does it keep the codebase healthy? The reviewer is not verifying that the code compiles — they are asking whether the change is *correct* at every level of the stack.

---

## Review Checklist

Work through these categories in order. Every category must produce a clear Pass, Comment, or Block decision before moving to the next.

---

### 1. Scope

**Does the PR do exactly what the backlog item says, and nothing more?**

- The PR description must reference a `BL-N` item or an external issue. If it doesn't, request it before reviewing anything else.
- Read the work item's Detail section. Then read the diff. Do they match?
- Changes outside the scope of the work item — even improvements — are **scope creep** and must be flagged. The reviewer should not approve scope that was not agreed upon. If the extra change is worthwhile, it must be a new `BL-N` entry.
- Exception: trivial single-line fixes (e.g., fixing a typo in an adjacent comment found while working) are acceptable if they are clearly noted in the PR description.

**Block if:** the diff contains changes unrelated to the stated backlog item without explanation.

---

### 2. Correctness

**Does the implementation actually solve the stated problem?**

- Re-read the work item's done-when condition. Evaluate whether the code, as written, satisfies it.
- Trace the code path mentally or via test evidence: does the behavior match the intent?
- Look for off-by-one errors, null paths, unchecked assumptions about input, and incorrect condition direction (`<` vs `<=`).
- Verify that edge cases called out in the item Detail are handled.

**Block if:** the implementation does not satisfy the done-when condition, or a clearly reachable code path produces incorrect behavior.

---

### 3. Design Alignment

**Does the implementation match the design document?**

- Cross-reference the changed systems with the relevant section of `design/GAME_DESIGN_DOCUMENT.md` (or equivalent).
- If the implementation introduces behavior not described in the design, or contradicts a described behavior, this is potential CODE-DRIFT. Flag it. The reviewer should not approve code that silently diverges from the stated design.
- If the design document is incomplete on this point, note it — the SCRIBE needs to know there is a gap.

**Block if:** the code contradicts a clear design statement and no finding or rationale explains the divergence.

---

### 4. Architecture

**Is this the right structural solution?**

- Could this change have been made in fewer places? More concentrated changes are usually better designs.
- Does this change respect the existing layer boundaries? (e.g., no domain model importing from the UI layer; no persistence concerns leaking into the engine layer)
- Is a new abstraction being introduced? Does it earn its complexity — does it simplify the surrounding code, or does it add indirection without payback?
- Are responsibilities clearly separated? A method or class that accumulates concerns it doesn't own is a smell.
- If the change involves extracting or creating a class: is the class focused enough? Apply the general standard — a class referencing more than ~5 other types is likely doing too much.

**Comment if:** the structure works but a simpler approach exists.  
**Block if:** the change introduces a layer violation or responsibility conflation that will compound with future work.

---

### 5. Code Quality

These apply on top of the language-specific standards. Flag violations as Comments unless they are severe enough to constitute correctness or security issues.

#### Naming
- Names answer "what is this?" without needing context. See [`general.md`](general.md) §1.
- No `Manager`, `Handler`, `Util`, or `Helper` classes introduced without a clear justification.

#### Size
- Methods should be reviewable in a single screen. A method exceeding ~30 lines is likely doing more than one thing. Flag it.
- New classes that are already large on introduction (>200 lines) warrant scrutiny. Is the responsibility focused?
- If the overall file being modified is growing very large as a result of this change — not as a result of the change alone, but as a cumulative trend — note it as a Technical Concern to track. This is not a block, but the SCRIBE should be informed.

#### Magic values
- No unexplained numeric or string literals in logic. See [`general.md`](general.md) §3.
- Any constant introduced must be named for *why* it exists, not what it is.

#### Comments
- Comments explain decisions, constraints, and non-obvious reasoning. Comments that restate the code should be removed. See [`general.md`](general.md) §4.

#### Java-specific
- Records used for value/data objects (no mutable state, no complex behavior). See [`java.md`](java.md).
- `@JsonIgnore` on all computed or transient methods. See [`java.md`](java.md).
- `Optional` only on return types, never as parameters. See [`java.md`](java.md).
- Enum constants for typed values — no raw string comparisons for domain types.

---

### 6. Security

Review for the most common vulnerability classes. Even in a game prototype, habits formed in code review carry forward.

- **Injection:** If any user input or external data is used in file paths, system calls, or structured queries — verify it is sanitized.
- **Unvalidated data at boundaries:** External input (file reads, network, user-provided values) must be validated before use. Internal data can be trusted. See [`general.md`](general.md) §5.
- **Serialization exposure:** Any new field on a serialized class — is it intentionally serialized? Should it be `@JsonIgnore`? Serializing derived or transient state is both a correctness bug and a potential data exposure risk.
- **Hardcoded credentials or keys:** These must never appear in source. If found, this is an immediate block and should be flagged to the user.

**Block if:** any of the above are present.

---

### 7. Tests

- If the work item's Detail or done-when condition implies a verifiable behavior, that behavior should have test coverage.
- Tests must be meaningful: they should fail if the behavior they test breaks. A test that passes unconditionally is worse than no test.
- Test method names should read as sentences describing the expected behavior. See [`java.md`](java.md) §2.
- If the work is in a system that had no tests and adding a full test suite is out of scope for this item, note it. It should not block the PR, but it is a candidate for a Technical Concern or a future backlog item.

**Comment if:** test coverage is clearly missing for verifiable behavior described in the item.  
**Block if:** existing tests were broken and no justification is provided.

---

### 8. Findings and Documentation

- If the implementation revealed something that should be logged as a finding (Confirm, Rework, or Technical Concern), verify that the author updated `design/PROTOTYPE_FINDINGS.md`.
- If the work item resolves a DRIFT entry, verify that `design/KNOWN_DRIFT.md` has been updated as part of this PR.
- If `design/STATUS.md` needs updating as a result of this merge, the SCRIBE will handle it post-merge — not the PR author.

**Comment if:** observable design findings are absent from the log and the author is unlikely to circle back.

---

### 9. Commit and PR Hygiene

- Commit messages follow the format in [`git-workflow.md`](../process/git-workflow.md): type, imperative subject, optional why-body.
- The PR description includes: the backlog item ID, the done-when condition, a test results summary, and a `Closes BL-N` reference.
- The work item has been removed from `BACKLOG.md` as part of the final commit.
- The branch name matches `prototype/work-item/{BL-N}`.

**Comment if:** hygiene issues are present but don't affect the change.  
**Block if:** the `Closes BL-N` reference is missing or the work item was not removed from `BACKLOG.md`.

---

## Verdict

After completing all categories, assign one of three verdicts:

| Verdict | Meaning |
|---------|---------|
| **Approve** | All categories pass. Ready to merge. |
| **Comment** | No blocking issues, but improvements are noted. Author may merge at their discretion after acknowledging the comments. |
| **Request Changes** | One or more Block conditions are present. The PR must not be merged until they are resolved. |

If the reviewer is uncertain whether something rises to a Block, default to Request Changes and explain the concern clearly. It is easier to downgrade a block than to un-merge problematic code.

---

## Scope of DRIFT Identification

Any reviewer — regardless of role — may identify drift during a PR review. If reviewing a PR reveals that the implementation, while correct per the work item, diverges from the design in a way that conflicts with stated goals:

- Log a `DESIGN-DRIFT` or `CODE-DRIFT` item in `BACKLOG.md` via direct commit referencing the finding.
- Note it in the review comment so the author and SCRIBE are aware.
- Do not block the PR for drift that is not directly caused by the PR's changes — that is a separate concern tracked in the backlog.
