# Idea Bin Synthesis Guide

**Last updated:** Fri 13 Mar 2026
**Type:** Guide
**Audience:** `for-claude` — teaches Claude how to synthesize raw brain dumps into overview docs and roadmaps
**Prerequisite:** Read `roadmap-template.md` and the example overview doc before running synthesis (so you know what the output should look like)

---

## When to Load

Kurt says "ready to synthesize the idea bin" or "let's turn the brain dump into a roadmap and overview."

---

## Core Synthesis Process

**Input:** Raw entries from the Idea Bin file (brain dump path) OR a semi-structured description/prompt (prompt path).

**Output:** Two files:
1. **Overview doc** (`{project}-overview-for-kurt.md`) — lives at project root. Kurt's design surface. What the project is, how it works, what's decided, what's open.
2. **Roadmap** (`for-claude/design-1-roadmap-for-claude.md`) — Claude's execution surface. What needs to be built, in what order, with decision log.

---

## Two Entry Paths (Same Synthesis Process)

### Path A: Brain Dump (Raw Messy Thoughts)

**Input:** Idea Bin file with timestamped entries. Entries are rough, incomplete, redundant, or stream-of-consciousness.

**Synthesis job:** Extract signal from noise. Group related thoughts, resolve contradictions, identify gaps, surface open questions.

**Effort:** High. Requires interpretation and cleanup.

### Path B: Prompt (Semi-Structured Description)

**Input:** Kurt provides a focused description — either written himself or generated from conversation. More organized than raw brain dump.

**Synthesis job:** Validate structure, fill gaps, query for missing details, surface open questions.

**Effort:** Medium. Requires less interpretation; focus on completeness.

**Both paths converge on the same two outputs** (overview + roadmap). The difference is how much preprocessing happens before synthesis begins.

---

## Synthesis Workflow

### Step 1: Scan and Summarize (5–10 minutes)

Read the entire Idea Bin or prompt **once** without taking notes. Get a holistic sense of:
- What's the core idea? (one sentence)
- What's the scope? (what's in, what's out)
- What's settled? (clear decisions)
- What's unsettled? (open questions)
- What's vague? (needs clarification)

### Step 2: Surface Questions to Kurt (Before Generating)

If critical details are missing, ask now rather than guessing:

**Essential questions to ask (if unclear):**
- **Purpose:** Why does this project exist? What problem does it solve?
- **Audience/Users:** Who will use this? (Kurt, end users, internal tool?)
- **Scope boundary:** What's definitely in? What's definitely out? What's maybe?
- **Success criteria:** How do you know it's done? What's the minimum viable thing?
- **Constraints:** Any hard tech constraints, timeline, or resource limits?
- **Integrations:** Does it connect to other systems Kurt uses? (Notion, Google Drive, etc.)

**Sample clarifying prompt:**
> I've scanned the idea bin and I'm seeing the core as [one-line summary]. A few things would help me produce a better overview and roadmap:
> 1. Why are we building this? (Who benefits, what problem are we solving?)
> 2. Is the scope [X, Y, Z] or are you thinking bigger?
> 3. What does "done" look like? (MVP vs full vision?)
>
> Once I have clarity on those, I'll synthesize into the overview doc and roadmap.

Wait for Kurt's response before proceeding.

### Step 3: Synthesize Overview Doc

**Primary audience:** Kurt. This is his design surface — the document he reads to remember what the project is.

**Secondary audience:** Claude. This is also how Claude learns what the project is (Claude will read it during synthesis of the roadmap).

**Structure to follow:** (Adapt from `archimedes-overview-for-kurt.md` example)

```
# {Project Name}

**Last updated:** {date}
**Overview audience:** Kurt (primary). Claude (secondary).

## What is {Project Name}?

{One to two sentences. What is it? Why does it exist?}

## Vision

- **What:** {one-line description of the end product}
- **Why:** {one-line purpose}
- **Who benefits:** {Kurt, end users, etc.}

## Scope

- **Definitely in:** {core features/components}
- **Definitely out:** {explicit non-goals}
- **Maybe:** {nice-to-have or future work}

## How It Works

{Two to four sentences explaining the system flow. Enough so someone reading this months from now understands what happens.}

## Key Components

List the major pieces the system is made of. Short one-liner per component.

## Constraints & Requirements

{Any hard requirements, tech constraints, external dependencies.}

## Open Decisions

List questions that still need answers before building:
- Design choice A vs B?
- Which tool or platform?
- Integration required?

## Kurt's Design Notes (Verbatim Capture)

Include key thoughts and preferences from the brain dump — things that shaped the vision and should stay visible as you build.

```

**What makes a good overview doc:**
- **Clear:** Kurt can read it in one sitting and understand what the project is without ambiguity.
- **Captures intent:** Why this project matters to Kurt comes through.
- **Bounded:** Scope is explicit — no wondering what's in or out.
- **Preserves Kurt's voice:** Direct captures of his thinking, not paraphrased away.
- **Short:** 1,000–2,000 words. If longer, extract detail to roadmap or a separate design doc.

**Avoid:**
- Generic descriptions (too vague to guide Claude)
- Missing constraints (hidden gotchas surface later)
- Over-designed decisions (you haven't built yet — keep options open)
- Buried open questions (put them front and center so Claude asks before building)

### Step 4: Synthesize Roadmap

**Primary audience:** Claude. This is Claude's execution surface.

**Secondary audience:** Kurt (through conversation). Kurt engages with the roadmap through talking to Claude, not by reading it directly.

**Reference:** `roadmap-template.md` (structure and conventions)

**Key points:**

- **What's Next:** Create an initial queue of tasks. Don't try to plan the whole project — plan the next 5–7 items, then Phases for the rest.
- **Vision section:** Lift directly from the overview doc's Vision section. Keep it short.
- **Components:** Map to the Key Components from the overview (each component tracks its own status).
- **Phases:** Group related work into logical phases. Most projects have 3–5 phases.
- **Open Questions:** Extract from the overview. These should be resolved early, before they block work.
- **Decision Log:** Start with any decisions already made in the brain dump. Format: `#` · `DATE` · `Summary` · `Updates` (if it modifies a prior decision).
- **Kurt's Notes Capture:** Include raw thinking from the brain dump that's relevant to execution — patterns to preserve, worries to watch for, preferences.

**Start here:** What's the first task? (Usually: refine the design, validate assumptions, or build the skeleton.)

**Decide:** Is the roadmap for an autonomous build (Kurt designs, Claude builds for 15+ minutes without checking in)? Or collaborative (tight back-and-forth)? This affects how detailed the roadmap needs to be.

---

## Overview Doc Audience Patterns

Three options for overview structure (choose based on project complexity):

### Option 1: Single Document, Kurt's Voice (Recommended for Most Projects)

One overview doc written in Kurt's conversational style (`for-kurt`). Claude reads it to learn the project.

**Pros:**
- One source of truth (no sync tax)
- Kurt's intent comes through clearly
- No duplication

**Cons:**
- Claude may miss technical implications of narrative prose
- Longer file (5–7 sentences → 1,000+ tokens)

**Best for:** Straightforward projects where the domain and intent are clear once written well.

### Option 2: Paired Documents (For Ambiguous or Large Scope)

Separate Kurt doc (narrative, for-kurt) and Claude doc (structured, for-claude). Same content, optimized per audience.

**Pros:**
- Each audience gets ideal format
- Claude's version can highlight decisions and constraints
- Kurt's version can be more exploratory/tentative

**Cons:**
- Sync tax — every Kurt edit requires propagating to Claude version
- Risk of version divergence

**Best for:** Highly complex projects (e.g., Marvin) where different info matters to Kurt vs Claude.

**Pattern:** Kurt owns the `{project}-overview-for-kurt.md`. A Claude session creates a `{project}-overview-for-claude.md` that extracts structure. At next compaction or sync point, merge changes if Kurt edited the Kurt version.

### Option 3: Interleaved Sections (For Team Collaboration)

Single document with sections labeled for different audiences. Kurt section, then Claude section, in parallel.

**Pros:**
- One file (no sync tax)
- Both audiences served
- Easy to cross-reference

**Cons:**
- File gets longer (more tokens)
- Harder to read if sections are tightly interleaved

**Best for:** Projects where Kurt and Claude collaborate closely on design decisions.

---

## After Synthesis

### Update Project CLAUDE.md

Add routing for the new overview and roadmap. The task router should point to the roadmap for questions like "what's next?" or "what's the status of X?"

Example addition to CLAUDE.md:
```
| Kurt asks about roadmap status, next steps, or what's been decided | `for-claude/design-1-roadmap-for-claude.md` |
| Kurt asks "what is {project}?" or wants design details | `{project}-overview-for-kurt.md` |
```

### Set Initial State Dates

- Overview: "Last updated: {date}"
- Roadmap: "Last updated: {date}"
- Idea Bin: "Last harvested: {date}" (harvest it now, or mark it as used if starting fresh)

### Tell Kurt

Present the two outputs (overview + roadmap). Conversation from here:
1. Does the overview capture the vision clearly? Any edits?
2. Does the roadmap's priority queue look right? Any reordering?
3. Any open questions the overview surfaced that need answering before starting?

Once Kurt approves both, you're ready to move forward: either execute the roadmap (if it's a build session) or hand off to another Claude session.

---

## Reentrant Pipeline

The Idea Bin stays open throughout the project. New components, questions, and design thoughts keep going in.

When to re-run synthesis:
- **Major scope change** — existing overview no longer accurate
- **Midway through build** — new phase starting, need updated priorities
- **New component discovered** — Idea Bin has accumulated enough new thoughts for a refresh

**How:** Same process as initial synthesis. Kurt says "idea bin is getting full again, let's re-synthesize," and Claude produces an updated overview + roadmap. The old versions are still in git history (never delete them).

---

## Quality Checks

Before handing the outputs to Kurt, verify:

**Overview doc:**
- [ ] Is there one clear sentence that answers "what is this?"
- [ ] Would someone reading this in 6 months understand what the project is?
- [ ] Are scope boundaries explicit (what's in, out, maybe)?
- [ ] Are critical constraints/gotchas captured?
- [ ] Are Kurt's own words and thinking visible, not paraphrased away?
- [ ] Is it under 2,000 words?

**Roadmap:**
- [ ] Is the Vision section clear and actionable?
- [ ] Are components named and scoped clearly?
- [ ] Is the What's Next queue 5–7 items? (Rest in Phases?)
- [ ] Are open questions listed with their impact (what do they block)?
- [ ] Does the Decision Log capture settled choices with rationale?
- [ ] Would a fresh Claude session reading this know what to build first?

If any check fails, revise before presenting.

---

## Cross-References

**Part of the Archimedes project setup flow:**
- `output/start-here.md` — orchestrates the full setup, including the Idea Bin pipeline
- `idea-bin-template.md` — the template every project copies for its Idea Bin
- `roadmap-template.md` — reference for roadmap structure and conventions
- `archimedes-overview-for-kurt.md` — example overview doc (study this for patterns)

**Related guides:**
- `markdown-architecture-guide.md` — design principles for file structure (overview and roadmap are design surfaces)
- `file-type-optimization-guide.md` — design surfaces and capture files have specific optimization rules
- `naming-conventions.md` — naming patterns for the output files
