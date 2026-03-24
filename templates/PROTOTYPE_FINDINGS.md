---
iteration: "{{iteration-name}}"
branch: "{{branch-name}}"
design-version: "{{GDD vX.Y}}"
started: "{{date}}"
---

# {{Project Name}} — Prototype Findings Log

<!--
  AI-navigability patterns used in this document:

  1. YAML frontmatter — machine-readable metadata at the top of the file.
     Update these fields at the start of each iteration.

  2. Section anchors — HTML comment immediately above every ## or ### heading.
     Pattern: <!-- section: name -->
     Use to find and update specific sections without relying on heading text.

  3. Prototype note IDs — HTML comment immediately above each versioned blockquote.
     Pattern: <!-- note: vX.Y-sectionN-topic -->
     Add one before every > **vX.Y — ...** observation block.
-->

This is a living document. It records observations made during prototype implementation and playtesting, organized into four categories:

- **[Confirm]** — Assumptions confirmed and desirable. Should be marked as Confirm in the design document to cement functionality.
- **[Rework]** — Base functionality is desirable, but in its current state it harms gameplay in some way, doesn't play well with other systems, is technically unfeasible, or is limiting to future extensibility. Assumption should be included but reworked in the next design iteration.
- **[Abandon]** — Assumption may fall into the same reasons as Rework, but the decision has been made to move away from implementing this functionality in future iterations. Any replacements or effects on other features will be called out.
- **[Discuss]** — Assumption needs major rework or potentially abandonment, but the direction has broader implications to both confirmed and unconfirmed assumptions. This discussion will take place elsewhere, and findings and final direction will be moved to the appropriate categories before the next design iteration.

Any finding entry may also carry an **[Idea]** tag to flag a cheap-win opportunity that is adjacent to the work but not required to deliver it. See the Idea section below.

Findings progress toward a **design resolution** pass after the prototype is complete. Resolved findings feed back into the next design document version and define the following prototype's scope.

This document also tracks **release candidate eligibility**. See the Release Candidate Status section at the bottom.

---

<!-- section: m1 -->
## Milestone 1 — {{Milestone Name}}

### [Confirm] {{Finding title}}

{{Describe what was assumed, what was actually observed, and why this confirms the assumption. Be specific. Reference GDD section numbers or design document sections if relevant.}}

---

### [Rework] {{Finding title}}

{{Describe what was assumed, what was observed, why the current approach is problematic. Call out specifically what makes it harmful or limited. End with a brief note on what the rework should address.}}

---

### [Abandon] {{Finding title}}

{{Describe what was assumed, what was observed, and why the decision was made to abandon rather than rework this functionality. List any other confirmed or planned features this decision affects, and note any replacement functionality if applicable.}}

---

<!-- section: m2 -->
## Milestone 2 — {{Milestone Name}}

### [Confirm] {{Finding title}}

{{...}}

---

<!-- section: cross-cutting -->
## Cross-Cutting Observations

### [Discuss] {{Finding title}}

{{Describe the observation and the question it raises. Identify which confirmed and unconfirmed assumptions are implicated. Note that this requires a broader design conversation before resolution. Do not resolve inline — move to a dedicated discussion, then return with the outcome as a Confirm, Rework, or Abandon finding.}}

---

<!--
  Guidelines for finding entries:
  
  - Write findings close to when you observe them — don't batch at the end of a milestone.
  - Each entry should cover: what was assumed, what was actually seen, and what it means.
  - Be specific about mechanics and numbers when they matter.
  - One finding per observation — don't bundle multiple issues under one header.
  - Use [Confirm] for things that feel good AND are wanted. "Works fine" isn't enough.
  - Use [Abandon] not [Rework] when the feature itself is the problem, not the implementation.
  - [Discuss] findings should identify what other assumptions are implicated — that's what earns them this category.
-->

---

<!-- section: tc -->
## Technical Concerns

Technical concerns are architectural, structural, or implementation-level observations that fall outside the design-validation scope of the standard finding categories. They are not about whether a mechanic is desirable — they are about whether the codebase, architecture, or technical approach is sustainable, secure, or extensible.

Each entry uses a `[TC-N]` tag and one of two urgency levels:

- **[Pressing]** — Poses a risk to correctness, security, performance at expected scale, or maintainability severe enough that further work on the affected system would compound the problem. Address in the next iteration. Candidates for an explicit task or precondition in the next ROADMAP.
- **[Future]** — Real but non-blocking: extensibility gaps, structural decisions that will need revisiting at larger scale, or tech debt accumulated for prototype speed. Track for future planning. Reference in the Design Document Recommendations under the Findings Summary.

---

<!-- item: TC-1 -->
### [TC-1] [Pressing / Future] {{Concern title}}

**Observation:** {{What was observed in the codebase or implementation that raises this concern.}}

**Implications:** {{What breaks, degrades, or becomes risky if this is not addressed. Scope the blast radius.}}

**Proposed Direction:** {{A concrete starting point for resolution. Does not need to be a full solution — enough to inform the next design or planning cycle.}}

---

<!--
  Guidelines for Technical Concerns:

  - Use [Pressing] for concerns that affect correctness, security, performance at expected scale, or maintainability to the degree that further work on the system would compound the problem.
  - Use [Future] for real but non-blocking concerns: extensibility gaps, structural decisions that will need revisiting at larger scale, or tech debt accumulated for prototype speed.
  - Do not log things that are simply "unpolished" — those are [Rework] findings. Technical Concerns are systemic.
  - [Pressing] concerns should appear in the next iteration's ROADMAP as explicit tasks or preconditions.
  - [Future] concerns should be referenced in the Design Document Recommendations under the Findings Summary.
  - If a TC suggests that a new coding standard or practice could prevent the problem class entirely, append an [Idea] tag to the same entry.
-->

---

<!-- section: ideas -->
## Ideas

This section collects `[Idea]` tags surfaced during implementation. Ideas are cheap-win observations adjacent to the current work that could add value but are not required for any work item to complete. They are logged here by the BUILDER and resolved by the SCRIBE in coordination with the user.

**Lifecycle:** An idea entry lives in this section only while it is pending a decision. Once the SCRIBE and user have acted on it — whether the idea is rejected, applied as a documentation or standards change, or converted into a backlog item — the entry is removed from the findings log. Only unresolved ideas remain here at any given time.

**Good candidates for [Idea] tags:**
- Testing opportunities that would make a system more reliable or easier to change.
- Narrow security improvements that could close a specific exposure.
- Extensibility hooks that would enable future work at low cost.
- Coding patterns to adopt or avoid — patterns that provide no benefit and would serve future technical health to drop.

Ideas must be **cheap wins**. If an idea requires major structural changes or significant effort, it is a backlog item, not an Idea.

<!-- item: Idea-1 -->
### [Idea-1] {{Idea title}}

**Observation:** {{What was noticed that prompted this idea.}}  
**Potential value:** {{What would improve if this were adopted: reliability, security, extensibility, or maintainability.}}  
**Effort:** {{Estimate: trivial / low / medium. If medium or higher, reconsider whether this is really a backlog item instead.}}

<!--
  SCRIBE: Once a decision is made and any resulting action is complete (doc change applied, backlog item added, or idea discarded), remove this entry entirely from the findings log.
-->

---

<!-- section: findings-summary -->
## Findings Summary

{{Written after all milestones are complete and before the resolution pass. This section distills the full findings log into a narrative assessment of what was learned, organized by theme rather than by milestone. It serves two audiences:}}

{{1. **Release candidacy** — Does the prototype demonstrate enough confirmed design assumptions, and are the remaining gaps addressable without fundamental redesign?}}

{{2. **Design document updates** — What specific changes, additions, or removals should feed back into the next version of the design document?}}

<!-- section: findings-what-worked -->
### What Worked

{{Summarize the confirmed assumptions that held up across implementation. Group by system or theme (e.g. "Core loop," "Social systems," "Economy"). Call out which design pillars or goals these findings validate. Be specific about which mechanics proved sound and why.}}

<!-- section: findings-rework-summary -->
### What Needs Rework

{{Summarize findings tagged [Rework]. Group by severity: issues that affect core gameplay vs. polish-level concerns. For each group, note whether the rework is scoped (clear fix, bounded effort) or open-ended (requires design exploration). Call out any rework items that, if left unaddressed, would block release candidacy.}}

<!-- section: findings-abandoned -->
### What Was Abandoned

{{Summarize findings tagged [Abandon], if any. Explain the reasoning and call out downstream effects on other confirmed or planned features.}}

<!-- section: findings-questions -->
### Open Questions

{{Summarize findings tagged [Discuss] that remain unresolved. For each, state what decision is needed and what is blocked until it's made. If a [Discuss] finding was resolved during the prototype, note the resolution and which category it moved to.}}

<!-- section: findings-doc-recs -->
### Design Document Recommendations

{{List specific, actionable changes for the next design document version. Reference finding numbers (e.g. M1-8, CX-2) and the section of the design document each change would affect. Distinguish between: additions (new sections or mechanics to document), revisions (existing sections that need updating), and removals (design assumptions that should be struck).}}

<!--
  Guidelines for the Findings Summary:

  - Write this section AFTER all milestone findings are logged, not during.
  - This is a synthesis, not a recap — don't repeat finding descriptions verbatim.
  - Be honest about gaps. The summary should make it obvious whether the prototype succeeded at validating the design.
  - The "Design Document Recommendations" subsection is the primary input to the next design iteration. Make it concrete enough that someone could act on it without re-reading every finding.
-->

---

<!-- section: resolution-tracker -->
## Resolution Tracker

| # | Finding | Status | Design Target |
|---|---------|--------|---------------|
| M1-1 | {{Finding title}} | Confirm | {{Note for design doc, e.g. "GDD v0.2 unchanged" or "Update Section 5.3"}} |
| M1-2 | {{Finding title}} | Rework | {{What to address in next iteration}} |
| M1-3 | {{Finding title}} | Abandon | {{What is affected; any replacement}} |
| M2-1 | {{Finding title}} | Confirm | {{Note}} |
| CX-1 | {{Finding title}} | Discuss | {{What discussion needs to happen}} |

---

<!-- section: rc-status -->
## Release Candidate Status

**Current assessment:** {{Not Yet Eligible / Under Evaluation / Eligible / Confirmed}}

{{Brief rationale for the current assessment. What would need to change to move to the next status?}}

<!-- section: rc-criteria -->
### RC Eligibility Criteria

| Criterion | Met? | Notes |
|-----------|------|-------|
| Confirmed findings substantially outnumber Rework / Abandon | {{Yes / No / Partial}} | {{e.g. "8 Confirm, 3 Rework, 0 Abandon"}} |
| No Rework or Abandon finding undermines a core goal from GOALS.md | {{Yes / No / Partial}} | {{Which goals are at risk, if any}} |
| All Discuss findings are resolved or explicitly scoped out | {{Yes / No / Partial}} | {{Which discussions are still open}} |
| Product is playable / usable end-to-end without dead ends | {{Yes / No / Partial}} | {{Known blocking gaps}} |

<!-- section: rc-history -->
### RC History

| Date | Assessment | Rationale |
|------|-----------|----------|
| {{date}} | {{Not Yet Eligible}} | {{e.g. "Too many Rework findings across core systems; social layer unvalidated"}} |

<!-- section: rc-next-direction -->
### Next Iteration Direction

**Decision:** {{Continue Iterating / Parallel Effort / Fresh Start}}

{{Explain which direction the project should take for the next iteration, and why.}}

- **Continue Iterating** — The current prototype is structurally sound enough to build on directly. The next iteration extends, reworks, or adds to the existing codebase. Use this when the architecture is healthy, confirmed findings substantially outnumber rework/abandon, and the gaps are addressable without fundamental restructuring.
- **Parallel Effort** — The current prototype validates key design questions but has architectural or structural limitations that make it a poor base for the next phase. A parallel effort preserves the prototype as a reference while building the next iteration on a cleaner foundation. Use this when the prototype succeeded at answering design questions but the codebase has accumulated too much prototype-grade scaffolding to extend cleanly.
- **Fresh Start** — The prototype revealed fundamental issues with the design direction, core assumptions, or technology choices that cannot be addressed by iteration. A fresh start means rethinking the approach with the lessons learned. Use this when the majority of findings are Rework/Abandon, core goals are unvalidated, or the technology stack proved unsuitable.

{{Regardless of direction, list the top 3–5 priorities for the next iteration and any constraints or prerequisites that must be addressed first.}}

<!--
  How to use this section:
  
  - Update after every resolution pass. Don't update mid-prototype.
  - "Eligible" means the criteria above are met. "Confirmed" means the decision was made to cut an rc/ branch.
  - The shift from design concerns to polish concerns is the signal — when most new findings are about feel, balance, and edge cases rather than whether a system should exist at all, you're approaching RC territory.
  - A prototype can become RC-eligible without being fully feature-complete, if the shipped scope is coherent and the unshipped scope is explicitly deferred.
-->
