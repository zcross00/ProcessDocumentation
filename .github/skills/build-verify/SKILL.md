---
name: build-verify
description: "BUILD VERIFY SKILL — Compile the project and run all tests before opening a PR. USE FOR: BUILDER pre-PR gate; KEEPER standards sweep; verifying a fix did not break existing tests; confirming a new test class passes. Provides exact Maven commands, how to read Surefire output, and the pass/fail gate definition. DO NOT USE FOR: writing new tests (use test-writing skill); diagnosing application runtime errors (read source directly)."
---

# Build Verify

Run these commands in the project root (`c:\Users\zcros\development-2\Sovereign` or wherever the `pom.xml` lives) before opening any PR.

---

## Gate Rule

**A PR must not be opened if any test fails or the project does not compile.** No exceptions. Fix the failure first.

---

## Step 1 — Compile

```bash
mvn compile
```

Expected: `BUILD SUCCESS`. Any `ERROR` line means a compilation failure — do not proceed to tests until resolved.

```bash
# To see full compiler output without truncation:
mvn compile -e
```

---

## Step 2 — Run All Tests

```bash
mvn test
```

Expected output ends with:
```
Tests run: N, Failures: 0, Errors: 0, Skipped: 0
BUILD SUCCESS
```

If any test fails, Maven prints a summary like:
```
Tests run: 12, Failures: 1, Errors: 0, Skipped: 0
```

---

## Reading Surefire Reports

When a test fails, get the full detail from the Surefire report for that class:

```
target/surefire-reports/{fully.qualified.ClassName}.txt
```

The `.txt` file contains the full failure message, stack trace, and expected vs. actual values.

Current report files (update as new test classes are added):
```
target/surefire-reports/com.sovereign.ConceptPrerequisiteTest.txt
target/surefire-reports/com.sovereign.DecayEngineTest.txt
target/surefire-reports/com.sovereign.engine.EraTransitionTest.txt
target/surefire-reports/com.sovereign.engine.NeedAdvancementTest.txt
target/surefire-reports/com.sovereign.MainTest.txt
target/surefire-reports/com.sovereign.SettlementTest.txt
```

---

## Running a Single Test Class

To isolate a failing class without running all tests:

```bash
mvn test -Dtest=ClassName
# Example:
mvn test -Dtest=NeedAdvancementTest
mvn test -Dtest=EraTransitionTest
```

To run a single test method:

```bash
mvn test -Dtest=ClassName#methodName
# Example:
mvn test -Dtest=NeedAdvancementTest#advanceNeeds_worsensHungerThirstFatigue
```

---

## Common Failure Causes

| Symptom | Likely cause |
|---|---|
| `package X does not exist` | Import missing or class moved/renamed |
| `cannot find symbol` | Method/field renamed; check the domain class |
| `AssertionError: expected <X> but was <Y>` | Logic change broke an existing contract; fix the code or update the test if the contract legitimately changed |
| `NullPointerException` in test | Domain object not fully initialized; check constructor and getter signatures |
| `BUILD FAILURE` with no test output | Compilation failed; run `mvn compile -e` first |

---

## PR Checklist Integration

Before running `gh pr create`, confirm:

- [ ] `mvn compile` exits with `BUILD SUCCESS`
- [ ] `mvn test` exits with `BUILD SUCCESS` and 0 failures/errors
- [ ] Any new tests added for the backlog item are included in the commit
- [ ] Test count increased (or stayed the same) relative to the base branch
