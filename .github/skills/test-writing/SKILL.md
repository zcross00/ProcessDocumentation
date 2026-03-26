---
name: test-writing
description: "TEST WRITING SKILL — Write correctly structured JUnit 5 tests that match this project's conventions. USE FOR: BUILDER adding tests for a new backlog item; KEEPER flagging missing test coverage; writing tests for engine classes, domain logic, or persistence. Covers class placement, naming conventions, setup patterns, assertion style, and what not to test. DO NOT USE FOR: running tests (use build-verify skill); diagnosing test failures (read the Surefire report directly)."
---

# Test Writing

Conventions observed across all existing test classes. Follow these exactly — PRs with tests that deviate will be flagged in review.

---

## Test Framework

- JUnit 5 (`junit-jupiter` 5.10.2)
- No mocking framework — use real domain objects only
- No `AssertJ`, no `Hamcrest` — use `org.junit.jupiter.api.Assertions.*` exclusively

---

## File Placement

| What is under test | Test package | Example |
|---|---|---|
| Engine class in `com.sovereign.engine` | `com.sovereign.engine` | `EraTransitionTest`, `NeedAdvancementTest` |
| Domain or root class | `com.sovereign` | `DecayEngineTest`, `SettlementTest`, `ConceptPrerequisiteTest` |
| UI class | Do not test UI classes | — |

File path mirrors the package:
```
src/test/java/com/sovereign/engine/EraTransitionTest.java
src/test/java/com/sovereign/DecayEngineTest.java
```

---

## Class Structure

```java
package com.sovereign.engine;  // match the package of the class under test

import ...;                    // import only what is used

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

/**
 * {Milestone reference, e.g. "M5:"}: {one-line description of what this class tests}.
 * <p>
 * {Optional: key invariants or thresholds being tested, as a short paragraph.}
 */
class {ClassName}Test {        // package-private, NOT public
    // ...
}
```

**Rules:**
- Class is package-private (no `public` modifier)
- One Javadoc comment on the class describing what it tests and citing the milestone
- No `@BeforeEach` unless the same setup is shared across 4+ test methods — prefer private helper methods instead

---

## Private Helper Methods

Use private factory/builder methods rather than `@BeforeEach` when setup varies per test:

```java
private World buildWorld() {
    World w = new World();
    // ... minimal setup for this test scenario
    return w;
}

private Character namedCharWithForaging(float proficiency) {
    Character c = new Character("Forager", CharacterType.NAMED, new Stats(10, 10, 10));
    Concept concept = new Concept(ConceptRegistry.FORAGING, "desc", 1);
    concept.setProficiency(proficiency);
    concept.setDiscovered(true);
    c.getKnowledge().add(concept);
    return c;
}
```

---

## Test Method Naming

Pattern: `{methodUnderTest}_{scenario}` or `{methodUnderTest}_{expectedBehavior}`

```java
void advanceNeeds_worsensHungerThirstFatigue()
void advanceNeeds_clampsAtWorstLevels()
void prehistoricToTribal_requiresScoreAndSettlement()
void conceptDecay_floorsAtZero()
```

All test method names must unambiguously state what is being verified. No generic names like `testHunger()` or `test1()`.

---

## Assertion Style

```java
// Preferred: assertEquals with a descriptive failure message
assertEquals(HungerLevel.SATISFIED, c.getHealth().getHunger(),
        "Hunger should worsen one step");

assertEquals(0f, concept.getProficiency(), 0.0001f,
        "Proficiency should floor at 0");  // use delta for float comparisons

// Boolean assertions
assertTrue(concept.getProficiency() < 0.5f,
        "Proficiency should have decayed after 7+ day gap");

assertFalse(engine.prerequisitesMet(c, ConceptRegistry.CARPENTRY),
        "Carpentry should be blocked without Wood-Working prerequisite");
```

**Rules:**
- Always include a failure message string on assertions
- Float comparisons always use a delta (`assertEquals(expected, actual, delta, msg)`)
- Prefer `assertEquals` over `assertTrue(a == b)` — the failure message shows both values

---

## What to Test

| Target | Testable? | Notes |
|---|---|---|
| Engine classes (`ResolutionEngine`, `EraEngine`, `DecayEngine`, etc.) | Yes — primary target | Instantiate the engine; construct minimal domain state |
| Domain logic methods (`HealthState.getStatMultiplier`, `Settlement.advanceProgress`) | Yes | Construct the domain object directly |
| `ConceptRegistry` static methods | Yes | No instantiation needed |
| UI classes (`GameScreen`, widgets) | No | Require LibGDX runtime; skip |
| `Main.java`, `SovereignGame.java` | Placeholder only | `MainTest` exists as a placeholder; do not expand |
| `SaveManager` | Integration test only | Requires file I/O; add only if needed for a specific backlog item |

---

## What Not to Do

- Do not use `@Mock`, `@Spy`, or any mocking library — test with real objects
- Do not access private fields via reflection
- Do not write tests that only call setters and getters with no assertion logic
- Do not mark test classes or methods `public` — package-private is correct
- Do not share mutable state between test methods (no static fields)

---

## Minimum Coverage Rule

Every backlog item that touches engine logic must include at least one new test. The PR body must state the test count before and after (e.g., "All 51 tests pass. Added 4 new tests covering X").
