---
name: findings-entry
description: "FINDINGS ENTRY SKILL — Produce a correctly formatted PROTOTYPE_FINDINGS.md entry. USE FOR: logging any finding during implementation or review — Confirm, Rework, Abandon, Discuss, Technical Concern [TC-N], or Idea; ensuring correct HTML comment anchors, category tags, and three-part structure. Used by all roles. DO NOT USE FOR: RC eligibility assessment (use scribe-duties); resolution pass decisions (those are SCRIBE decisions made at iteration end)."
---

# Findings Entry

Use this skill whenever adding an entry to `design/PROTOTYPE_FINDINGS.md`. Consistent format makes findings machine-navigable and easy to review during the resolution pass.

---

## Entry Types

| Category | When to Use |
|----------|------------|
| `[Confirm]` | An assumption is proven correct and desirable. Should be cemented in the next design version. |
| `[Rework]` | The base idea is desirable but the current implementation harms gameplay, conflicts with other systems, is technically limiting, or is not feasible. Keep it — but change it. |
| `[Abandon]` | The feature or assumption should not continue. Either has a replacement, or the direction is being dropped entirely. State what replaces it (if anything). |
| `[Discuss]` | Needs major rework or potential abandonment, but the direction has broad implications that can't be decided here. Requires conversation. State what other assumptions are implicated. |
| `[TC-N]` | A technical concern — architectural, structural, or implementation risk. Separate from gameplay findings. |
| `[Idea]` | An adjacent opportunity: testing coverage, security improvement, extensibility hook, or coding pattern. Must be a cheap win; not a large structural change. |

`[Idea]` can appear inside any other entry type (e.g., a `[TC-N]` entry that also surfaces an `[Idea]` about a relevant coding standard). Append the `[Idea]` tag inline when this applies.

---

## Standard Entry Format (Confirm / Rework / Abandon / Discuss)

```markdown
<!-- note: v{iteration-short}-{milestone-short}-{N}-{topic} -->
> **{iteration-name} — {BL-N or finding ref} [{Confirm|Rework|Abandon|Discuss}]** — {Short title}
>
> Assumed: {What was expected or designed. One to two sentences. Be specific about the mechanic or behavior.}
>
> Observed: {What actually happened. What was built, what the tests showed, what was felt during play. Be specific — numbers and mechanics matter.}
>
> Meaning: {What this tells us. Should this be cemented, changed, dropped, or discussed? What does it imply for adjacent systems or future work?}
```

**Anchor format:** `v{iteration-short}-{milestone-short}-{N}-{topic}`
- `iteration-short`: a 2–4 char abbreviation. `DN` for prototype-dawn, `AW` for prototype-awakening, etc.
- `milestone-short`: `m1`, `m2`, `m3`, etc. For rework or cross-cutting items, use `rw` or `td`.
- `N`: sequential number within the milestone's block.
- `topic`: short camelCase word — `eraHysteresis`, `buildingType`, `tradePicker`.

Example: `<!-- note: vDN-m2-1-era-hysteresis -->`

---

## Technical Concern Format [TC-N]

```markdown
<!-- note: v{iteration-short}-tc-{N}-{topic} -->
> **[TC-{N}] [{Pressing|Future}]** — {Short description of the concern}
>
> {Describe what the concern is, which part of the codebase it applies to, and why it is a risk. Be concrete — name the class, method, or pattern.}
>
> {If Pressing: describe the impact if not addressed and how urgently it will compound.}
> {If Future: describe the trigger condition that would make it Pressing.}
>
> {Optional — if this TC suggests a coding pattern to adopt or avoid, append an `[Idea]` tag here and state the pattern.}
```

**Getting the next TC-N number:** Search `design/PROTOTYPE_FINDINGS.md` for the highest `[TC-N]` currently present. Increment by 1.

---

## Idea Format (standalone)

When an Idea is standalone (not attached to a TC or other finding):

```markdown
<!-- note: v{iteration-short}-idea-{N}-{topic} -->
> **[Idea]** — {Short title}
>
> {Describe the opportunity. What is it, where does it apply, and why is it worth considering?}
>
> {State why it qualifies as a cheap win. If it would require major structural changes, it should be a BL-N entry instead.}
```

---

## Where to Place Entries

| Entry type | Section |
|-----------|---------|
| Confirm / Rework / Abandon / Discuss for a milestone item | Under the matching `## M{N}: {Milestone Name}` section |
| Rework or cross-cutting items not tied to a milestone | Under `## Rework Items` |
| Technical Concerns | Under `## Technical Concerns` |
| Standalone Ideas | Under `## Technical Concerns` (append after the last TC entry) or the most relevant milestone section |

Within a section, append new entries after existing ones. Do not reorder existing entries.

---

## How to Land the Entry

| Role | How to commit |
|------|--------------|
| BUILDER | On the current work-item branch; lands when the PR merges |
| SCRIBE | Direct commit to `prototype/active` (findings updates are non-directional) |
| KEEPER | On the current `prototype/fix/` branch or as a standalone direct commit if it's a pure findings update |

---

## Checklist Before Committing

- [ ] The `<!-- note: ... -->` anchor is present and follows the naming format
- [ ] The blockquote header includes the iteration name, BL-N reference (if applicable), category tag, and a short title
- [ ] All three parts are present for Confirm/Rework/Abandon/Discuss: Assumed, Observed, Meaning
- [ ] TC entries state urgency (`[Pressing]` or `[Future]`) and name the affected class/method
- [ ] Idea entries include a sentence on why it qualifies as a cheap win
- [ ] Entry is placed in the correct section
- [ ] Finding counts in `design/STATUS.md` will need to be updated after this commit (SCRIBE duty)
