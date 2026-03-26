---
name: domain-model
description: "DOMAIN MODEL SKILL — Decide the correct Java construct for a new domain type and implement it to project conventions. USE FOR: BUILDER adding a new class, enum, or record; KEEPER reviewing whether an existing construct is the right type; deciding between mutable JavaBean, record, or enum when the standards are ambiguous. Covers the full decision tree with project-specific examples. DO NOT USE FOR: general Java style checks (use standards-check-java); architecture-level design decisions (use keeper-sweep Duty 5)."
---

# Domain Model

Use this decision tree before writing any new class, record, or enum. The wrong construct choice creates serialization bugs or unnecessary complexity.

---

## Decision Tree

```
Will this type be serialized to JSON by Jackson (saved to disk)?
│
├─ YES → Mutable JavaBean
│        └─ No-arg constructor + getters + setters
│           @JsonIgnore on computed/derived methods
│           @JsonValue/@JsonCreator if enum needs custom serialization key
│
└─ NO → Is it a small, immutable value carrier with no behavior?
         │
         ├─ YES → record
         │
         └─ NO → Does it represent a fixed vocabulary where each
                  constant carries numeric modifiers or config?
                  │
                  ├─ YES → enum with fields
                  │
                  └─ NO → Regular class (engine, utility, factory, etc.)
```

---

## Mutable JavaBean — When and How

**Use when:** The type is part of the object graph saved by `SaveManager` (i.e., reachable from `World`).

All of `Character`, `Settlement`, `LocalArea`, `OverworldGrid`, `Concept`, `Claim`, etc. are mutable JavaBeans.

```java
public class MyDomainClass {
    // Fields — no final
    private String id;
    private int value;
    private List<String> items = new ArrayList<>();

    // Required by Jackson — no-arg constructor
    public MyDomainClass() {}

    // Convenience constructor for construction in code
    public MyDomainClass(String id, int value) {
        this.id = id;
        this.value = value;
    }

    // Standard getters and setters for every field
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }

    // Computed property — MUST use @JsonIgnore
    @JsonIgnore
    public boolean isEmpty() { return items.isEmpty(); }
}
```

**Rules:**
- No `final` fields (Jackson requires mutation during deserialization)
- `@JsonIgnore` on every method that computes a derived value — if Jackson serializes it, it will try to deserialize it and fail or produce corrupted state
- Transient game-loop state (queued actions, combat logs) must also be `@JsonIgnore`d — see `DayState` for the pattern

---

## Record — When and How

**Use when:** The type is a small, immutable value carrier that is never serialized to JSON and has no behavior.

Existing examples: `Inventory.Item`, `ConceptRegistry.ConceptDef`, `GameController.DayResult`.

```java
public record MyValue(String name, float score) {}
```

**Rules:**
- Never use a record if the type appears anywhere in the serialized `World` graph
- Never add setters or mutable state to a record
- If you need to "update" a record, replace the instance — do not add mutation methods

---

## Enum with Fields — When and How

**Use when:** The type represents a fixed vocabulary where each constant carries numeric modifiers that affect engine calculations.

Existing examples: `HungerLevel`, `ThirstLevel`, `FatigueLevel`, `FitnessLevel`, `TemperatureLevel`, `WeatherType`, `DayActionType`.

```java
public enum MyLevel {
    BEST(1.0f),
    GOOD(0.8f),
    BAD(0.5f),
    WORST(0.2f);

    private final float multiplier;

    MyLevel(float multiplier) {
        this.multiplier = multiplier;
    }

    public float getMultiplier() { return multiplier; }
}
```

**Rules:**
- Put the constant-specific data directly on the enum constant — never use a switch statement in an engine to look up constant-specific values
- `HealthState.getStatMultiplier()` should multiply enum field values together, not contain any per-level logic
- Enums that are serialized (they nearly all are) will serialize by name by default — use `@JsonValue`/`@JsonCreator` only when you need a different serialized key (see `BuildingType`)

---

## Named Constants — When and How

**Use when:** A numeric threshold, limit, or configuration value appears more than once or requires explanation.

- Engine-wide difficulty constants → `DifficultyConfig` (add as a `public static` field)
- Class-local constant → `private static final` on the class that owns the invariant (e.g., `Settlement.SETTLEMENT_DAYS_REQUIRED`)
- Concept name strings → `ConceptRegistry` string constants

**Never use a magic number or magic string inline in engine code.** If there is no existing constant file for the domain, add a `public static final` field to the nearest owning class.

---

## Quick Reference

| Construct | Serialized? | Mutable? | Example |
|---|---|---|---|
| Mutable JavaBean | Yes | Yes | `Character`, `Settlement`, `Concept` |
| record | No | No | `Inventory.Item`, `GameController.DayResult` |
| enum with fields | Typically yes (by name) | No | `HungerLevel`, `WeatherType` |
| enum with `@JsonValue` | Yes (custom key) | No | `BuildingType` |
| Constants class/field | N/A | `static final` | `DifficultyConfig`, `ConceptRegistry.FORAGING` |
