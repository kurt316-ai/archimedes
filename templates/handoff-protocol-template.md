# Handoff Protocol — [Project Name]

**Audience:** `for-kurt-and-claude`
**Purpose:** Build Efficiency (BI)
**Last updated:** [DDD DD MMM YYYY]

---

## Overview

This protocol handles moving work between Cowork (desktop) and Chat (mobile/browser). Chat can't access the project folder, so every handoff must be self-contained.

**Practical setup:** Kurt uses Cowork on desktop (personal account) and Chat in Chrome (Northslope account). Files are dragged from the desktop into the Chat conversation in Chrome.

---

## Cowork → Chat Handoff

### Trigger

Kurt says **"prep for handoff"** (or similar). Claude runs the following steps.

### Step 1: Claude suggests focus areas

Claude pulls the next steps from the roadmap and suggests what to focus on in Chat:

> "Any particular files or parts of the project you want to focus on? Based on the roadmap, I'd suggest:
> - [next step 1]
> - [next step 2]
> - [next step 3]"

### Step 2: Kurt picks or adds

Kurt responds with "next steps" (accepting Claude's suggestions) or describes different focus areas.

### Step 3: Claude creates the handoff file

Claude creates a markdown file in the builder folder's `handoffs/` subfolder:

**Filename:** `[project]-handoff-for-chat-[date].md`

**The file contains:**

1. **Your Job** — What Chat's role is for this session. Example: "You are a design partner. Kurt will talk through ideas and feedback. Your job is to engage substantively — push back, suggest alternatives, ask clarifying questions — and capture everything in a structured deliverable."

2. **End Deliverable** — What Chat should produce at the end. Description of the format and structure. Example: "A structured markdown organized by target (specific doc, roadmap, templates, new ideas). Each section should be actionable — Cowork can parse it and route changes to the right files."

3. **Deliverable Structure Guidance** — Suggested sections for the deliverable, based on the focus areas. Always includes an **"Other Thoughts"** section so Chat captures tangents and random ideas about any part of the project, not just the focus areas.

4. **Focus Areas** — For each focus area, a series of questions to explore or explanations of current state. Gives Chat a guided conversation structure. Include enough context that Chat understands the current design and can engage at the right level.

5. **Model recommendation** — At the top of the file: "Recommended model: **Opus**" (or Sonnet if the task is quick review or simple research). Default is Opus — Chat handoffs are almost always design and architecture work.

### Step 4: Claude identifies files to paste

Claude lists which project files Kurt should drag into the Chat conversation, in order:

1. **Handoff file first** — so Chat has the framing before the content
2. **Relevant content files** — overview doc, roadmap sections, drafts, etc.

Claude provides the Mac-side paths so Kurt can find them on the desktop. Example:

> Drag these into Chat in this order:
> 1. `~/{your-cowork-root}/{your-project}/for-claude/capture-2-handoff-log-for-claude.md`
> 2. `~/{your-cowork-root}/{your-project}/{project}-overview-for-kurt.md`

---

## Chat → Cowork Handoff

### The simple version

Chat produces a structured feedback file. Kurt saves it to the builder folder's `handoffs/` subfolder (or pastes it into conversation). Kurt tells Cowork: **"new handoff doc in folder"** (or pastes the content). Cowork reads it and incorporates the feedback.

### What Chat produces

A structured markdown organized by **target** (not conversation order):

- Feedback on [specific doc being refined]
- Feedback on roadmap / system design
- Feedback on templates, modules, or protocols
- New concepts or ideas
- Other thoughts (tangents, random ideas, anything that came up)

Each section should be actionable — Cowork can parse it and route changes to the right files.

**File convention:** `[project]-chat-feedback-[date].md` in the handoffs folder.

### What Claude does (in Cowork)

1. Read the feedback file from the handoff folder
2. Compare against current project state (check for conflicts)
3. Incorporate per the feedback, section by section
4. Update roadmap if decisions were made
5. Capture any lessons learned
6. Delete the handoff files after incorporation is complete

---

## Handoff File Convention

**Location:** `[project]-builder/handoffs/` subfolder

**Filenames:**
- Cowork → Chat: `[project]-handoff-for-chat-[date].md`
- Chat → Cowork: `[project]-chat-feedback-[date].md`

**Cleanup:** Handoff files are ephemeral. Delete after the round-trip is complete. They should not accumulate.

---

## When to Use This Protocol

**Good use cases:**
- Refining the overview doc or roadmap design
- Brainstorming ideas or exploring new directions
- Reviewing drafts or plans
- Research tasks that don't need file access
- Any design conversation Kurt wants to have away from the desk

**Bad use cases (stay in Cowork):**
- Any task that requires reading or editing project files
- Debugging code
- Running cleanup checklists
- Building components or templates
- Anything that needs tool access (bash, file editing)
