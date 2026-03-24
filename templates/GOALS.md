---
version: "1.0"
updated: "{{date}}"
status: "{{Active | Under Review | Stable}}"
---

# {{Project Name}} — Goals

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update these fields on every version or status change.

  2. Section anchors — HTML comment immediately above every ## or ### heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.

  3. Prototype note IDs — HTML comment immediately above each versioned blockquote.
     Pattern: <!-- note: vX.Y-sectionN-topic -->
     Add one before every > **vX.Y — ...** observation block.
-->

Goals are the root-level purpose of this project. They are not mechanics or features — they are the value statements that justify the project's existence and guide every design decision beneath them. The design document's pillars are derived *from* these goals and should serve them.

Goals evolve slowly. A goal version bump signals a meaningful shift in what the project is trying to accomplish — not just what it builds.

---

<!-- section: goals-header -->
## Goals — v1.0

<!-- section: goal-1 -->
### Goal 1: {{Title}}

{{2–4 sentences. What is the core value this project is trying to create or deliver? Describe it in terms of what a person experiences or gains, not in terms of features or mechanics.}}

---

<!-- section: goal-2 -->
### Goal 2: {{Title}}

{{Same structure. Keep goals distinct — if two goals feel like the same thing said differently, merge them.}}

---

<!-- section: goal-3 -->
### Goal 3: {{Title}}

{{Add or remove goals as needed. Most projects have 3–6 meaningful goals. More than that usually means some are actually design concerns, not goals.}}

---

<!--
  What makes a good goal:
  
  - Describes a value or experience, not a solution. "Players should feel that information is as valuable as resources" is a goal. "Implement a knowledge/familiarity system" is a design decision in service of that goal.
  - Is falsifiable by observation. You should be able to look at a prototype or product and say whether this goal was achieved.
  - Is stable enough to survive one full design iteration without changing. If a goal changes every sprint, it's probably a design assumption in disguise.
  - Doesn't prescribe implementation. Goals survive tech rewrites; designs don't have to.
-->

---

<!-- section: rc-alignment -->
## Release Candidate Alignment

Before any release candidate is considered, each goal should be explicitly assessed. A prototype may be a release candidate when its confirmed findings demonstrate measurable progress against every goal, and no Rework or Abandon finding directly undermines a goal's core premise.

| Goal | RC Alignment | Notes |
|------|-------------|-------|
| Goal 1: {{Title}} | {{Supported / Partial / Not Yet Demonstrated}} | {{Brief note}} |
| Goal 2: {{Title}} | {{Supported / Partial / Not Yet Demonstrated}} | {{Brief note}} |
| Goal 3: {{Title}} | {{Supported / Partial / Not Yet Demonstrated}} | {{Brief note}} |

---

<!-- section: changelog -->
## Changelog

<!-- section: changelog-v1.0 -->
### v1.0 — {{date}}
- Initial goals established.

<!--
  When to version-bump:
  
  - A goal is added, removed, or its premise meaningfully changes (not just wording).
  - Prototype findings reveal that a goal was wrong, too narrow, or pointed at the wrong value.
  - A goal is being more aggressively pursued due to prototype validation — bump to record that the priority or scope has intensified.
  
  A wording clarification that doesn't change the intent is not a version bump — just edit in place and note it as a minor edit in the changelog.
  
  Version format: major.minor where major = a goal added/removed/fundamentally changed; minor = a goal's scope or priority adjusted.
-->
