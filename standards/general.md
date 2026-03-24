# Coding Standards — General

These principles apply to all code in all projects, regardless of language. Language-specific files in this directory add rules on top of these.

---

## 1. Naming

**Clarity over brevity.** A name should answer "what is this?" without needing the surrounding context.

- Use the full word unless the abbreviation is universally known in the domain (`id`, `url`, `http`, `json` are fine; `cnt`, `tmp`, `val` are not).
- Names that include "Manager", "Handler", "Util", or "Helper" are a design smell — they often mean the responsibility isn't clearly defined. Find a noun that names the concept.
- Booleans should read as a question: `isComplete`, `hasChildren`, `canQueue`.
- Don't encode the type in the name: `characterList` → `characters`; `stringName` → `name`.

---

## 2. Size and Focus

**Each unit of code (method, class, module) should do one thing.**

- A method longer than ~30 lines is usually doing more than one thing. Extract.
- A class that references more than ~5 other types is usually doing more than one thing. Split.
- If naming a method requires "and" (e.g., `validateAndSave()`), it is doing two things. Separate them.

---

## 3. Magic Values

**No unexplained literals in logic.**

Bad:
```java
if (familiarity < 0.3) { ... }
roll += 8;
```

Good:
```java
if (familiarity < HOSTILITY_THRESHOLD) { ... }
roll += MAX_SHELTER_BONUS;
```

Constants belong at class scope (or in a dedicated constants class/enum) with a name that states *why* the value exists, not just what it is.

---

## 4. Comments

**Comment the why, never the what.**

If a comment just restates the code, delete it. Comments are for decisions, constraints, and non-obvious reasoning.

Good:
```java
// Jackson requires a no-arg constructor; field values are set by deserialization
public Character() {}

// Clamp to 0 rather than throwing — callers may legitimately deplete a pool
public void withdraw(float amount) { ... }
```

Bad:
```java
// Increment day counter
dayCount++;

// Get the character's name
return character.getName();
```

Don't add docstrings to methods unless they form part of a public API surface that others will consume without reading the source.

---

## 5. Validate at Boundaries, Trust Internally

**Validate data when it enters the system. Trust it once inside.**

- Validate at system entry points: user input, file reads, network responses, external APIs.
- Do not add defensive null/range checks inside internal code called only from other internal code you control. The caller is responsible for passing valid data.
- If you find yourself adding `if (x == null)` deep inside a domain method, the real problem is that null is being passed at all — fix the caller or the design.

---

## 6. Immutability

**Prefer immutable data where feasible.**

- Objects that represent a snapshot or value — and don't need to change after creation — should be immutable.
- Expose mutable collections only when the caller genuinely owns the mutation. Otherwise return unmodifiable views or copies.
- Avoid setters unless the mutation is the explicit purpose of the method.

---

## 7. Error Handling

**Fail fast; don't swallow errors silently.**

- Unexpected states should throw, not silently return null or default values. A hiding failure is harder to debug than a loud one.
- Only catch exceptions you can meaningfully handle at that call site. Let others propagate.
- Don't use exceptions for control flow. If something is a normal optional outcome (not found, not applicable), model it in the return type.

---

## 8. Tests

**Tests document expected behavior; they are also the first user of your API.**

- Name tests as sentences: `playerCannotQueueSameActionTypeTwiceInOneDay()`, not `testQueue2()`.
- One assertion concept per test. A test that asserts five things can only tell you *something* failed, not *what*.
- Tests should not depend on execution order. Each test sets up its own state.
- Don't test implementation details — test observable behavior. If renaming a private method breaks a test, the test is testing the wrong thing.

---

## 9. Abstraction Level

**Don't abstract until you have two different uses for the same thing.**

- Helpers and utilities created for one call site are premature. Write it inline; extract when the second use appears.
- Don't design for hypothetical future requirements. Build for what is needed now.
- When a simpler solution and a clever solution both solve the problem, choose simple.

---

## 10. Code Review Mindset

When reviewing (or generating) code, ask:

- Would someone unfamiliar with this project understand what this does in under 30 seconds?
- If this breaks at 2am, does the error message or stack trace tell you exactly where and why?
- Does this add complexity that isn't earning its keep?
