# {PROJECT_NAME} — Checkpoint

**Captured:** {DATE}
**Session context:** {Brief description of what was being worked on}

A mid-session state capture. Use this when context might degrade (long session, complex task, about to switch focus). Claude can re-ingest this file to recover state faster than re-reading the entire folder.

---

## What We Were Working On

{1–3 sentences: the active task or conversation thread}

## Current State

{Where things stand right now. What's done, what's in progress, what's next.}

### Completed This Session
- {Thing 1}
- {Thing 2}

### In Progress
- {What's actively being worked on}
- {Any blockers or open questions}

### Next Steps
- {What should happen next, in order}

## Key Context That Might Get Lost

{Facts, decisions, or nuances from this session's conversation that aren't yet written into permanent files. This is the most important section — it captures what exists only in conversation.}

- {Fact or decision}
- {Fact or decision}
- {Fact or decision}

## Files Modified This Session

{List every file touched, with a one-line summary of what changed.}

| File | What Changed |
|---|---|
| {filename} | {brief description} |

---

## Instructions for Claude

If you're reading this checkpoint to recover context:
1. Read the roadmap and CLAUDE.md first (they have the full project state)
2. Then read this checkpoint for session-specific context
3. Verify the "In Progress" items — are they still in progress, or did they get completed?
4. Check if the "Key Context" items have since been written into permanent files
5. If everything in this checkpoint is now captured elsewhere, delete this file
