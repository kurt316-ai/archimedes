# {PROJECT_NAME} — Project Roadmap

**Last updated:** {DDD DD MMM YYYY HHMM PT}
**Audience:** `for-claude` — Kurt engages with this doc through conversation, not by reading it directly. Optimize for Claude answering "what's next?" and "what was decided about X?"

---

## What's Next

Priority-ordered queue of active and upcoming work. This is the front door — when Kurt asks "what should we work on?" read this section first.

**Two numbering systems:** **Seq** = execution order (changes as priorities shift). **ID** = permanent identifier (for decision log cross-references, never changes). Done items use `—` for Seq. Parked items use `P`.

| Seq | ID | Task | Status | Depends on | Target |
|-----|-----|------|--------|------------|--------|
| 1 | 1 | {Highest-priority task} | `not-started` | — | {Phase or milestone} |
| 2 | 2 | {Second-priority task} | `in-progress` | — | {Phase or milestone} |
| 3 | 3 | {Third-priority task} | `blocked` | ID2 | {Phase or milestone} |

**Status values:** `not-started` · `in-progress` · `blocked` · `done`

**Rules:**
- Max 7 active items. If more than 7, the rest live in Phases until they're next up.
- `done` items: set Seq to `—`, keep in table or move to Phase at start of each session.
- Parked items (`in-progress` but deprioritized): set Seq to `P`, keep at bottom of table.
- Re-rank Seq after every session that changes priorities. Never change IDs.
- Dependencies reference by ID (e.g., `ID2`), not Seq.

### Active Item Detail

For each `in-progress` or `blocked` item, expand here. **Pruning rule:** When an item reaches `done`, collapse its detail entry to a one-liner pointing at the deliverable (e.g., `**Status:** done — see [deliverable path]`). The detail section is a working surface; once work is done, deliverables are self-documenting and the decision log captures history.

**{Task name}**
- Status: `in-progress`
- What's happening: {one-line current state}
- Next concrete step: {what Claude should do next}
- Kurt's notes: {verbatim or structured capture of Kurt's thinking — preserved for context across compactions}

---

## Vision

{Two to three structured lines. No narrative.}

- **What:** {one-line description of the end product}
- **Why:** {one-line purpose}
- **End state:** {what "done" looks like}

**Workspace:**
- `{project}/` — Deliverable. What ships.
- `{project}-builder/` — Workshop. Research, drafts, this roadmap. Never ships.

---

## Components

What we're building, broken into major pieces. Each component tracks its own status independently.

### A. {Component Name}

- **What:** {one-line description}
- **Status:** `not-started` | `in-progress` | `blocked` | `done`
- **Depends on:** {other components or "none"}
- **Deliverable:** {specific file or artifact this produces}
- **Kurt's notes:** {captured thinking, if any}

### B. {Component Name}

{Same structure. Repeat as needed.}

### Z. Tech Stack

| Tool | Role in this project |
|------|---------------------|
| {Tool} | {Role} |

---

## Phases

Phases group related work. The source of truth for what's been done and what remains. Items here use the same status tags as What's Next.

### Phase 1: {Name}

| Task | Status | Notes |
|------|--------|-------|
| {Task description} | `done` | {Optional: date completed, relevant decision #} |
| {Task description} | `not-started` | |

### Phase 2: {Name}

| Task | Status | Notes |
|------|--------|-------|
| {Task description} | `not-started` | |

{Add phases as needed. Most projects have 3–5.}

### Milestones

Named checkpoints that gate progress. Each milestone lists its conditions.

**{Milestone name}**
- [ ] {Condition 1}
- [ ] {Condition 2}
- [ ] {Condition 3}
- **Gate for:** {what can't proceed until this passes}

---

## Open Questions

Unresolved decisions. Structured so Claude can present options when Kurt asks.

| # | Question | Options considered | Blocking |
|---|----------|--------------------|----------|
| 1 | {Question} | {A vs B vs C} | {What task or component this blocks, or "nothing yet"} |
| 2 | {Question} | {Options} | {Blocking} |

---

## Decision Log

Sequential record of every meaningful decision. One line per decision. Details only when needed.

**Format:** `#` · `DATE` · `Summary` · `Updates` (if it modifies a prior decision)

| # | Date | Decision | Updates |
|---|------|----------|---------|
| 1 | {YYYY-MM-DD} | {One-line summary of what was decided and why} | — |
| 2 | {YYYY-MM-DD} | {Summary} | Refines #1 |

**Convention:** Decisions are append-only and numbered sequentially. Never reorder. If a later decision modifies an earlier one, the later entry says "Updates #X" and the earlier entry stays unchanged.

---

## Kurt's Notes Capture

Running capture of Kurt's thinking that doesn't fit neatly into a single task or decision. Structured for Claude to reference across compactions.

Each entry is tagged with a date and topic so Claude can surface relevant notes when working on related tasks.

- **{YYYY-MM-DD} — {Topic}:** {Kurt's thinking, captured in structured sub-bullets. Verbatim where possible, restructured for Claude's parsing where needed.}

---

## How Claude Uses This Doc

**Primary use case:** Kurt says "what's next?" or "what should we work on?" → Read What's Next section, present the top items with their detail blocks.

**Secondary use cases:**
- "Was X decided?" → Search Decision Log by keyword
- "What's the status of Y?" → Search Components and Phases by name
- "What are the open questions?" → Read Open Questions table

**Every session:**
1. Check if any work done this session changes What's Next priorities
2. Move `done` items out of What's Next into their Phase
3. Add any new decisions to the Decision Log
4. Capture any Kurt thinking in the appropriate place (task detail, Kurt's Notes, or decision entry)
5. Update the "Last updated" timestamp

**Relationship to overview doc:** The roadmap is the top-down view of the **build** (process). The overview doc is the top-down view of the **product** (what you're building). Both are design surfaces. If a roadmap decision changes the product, update the overview doc too.
