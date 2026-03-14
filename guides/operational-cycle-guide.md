# Archimedes Operational Cycle Guide

**Purpose:** Defines when each maintenance process runs, how they chain together, and the full cadence inventory. Covers cleanup, push, compaction, reconciliation, and all recurring routines.
**Audience:** `for-claude`
**Companion doc:** `archimedes-system-guide-for-kurt.md` (project root) Part 3 has human-readable explanations.
**Prerequisite:** `session-discipline-guide.md` covers session-level behavior (context budget, workflow modes). This guide covers the system-level cycle that sits above any single session.
**Last updated:** Fri 13 Mar 2026
**Source:** Incorporates patterns from [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

## When to Load

- After compaction recovery (to know what maintenance to run)
- Before a push (to confirm you've followed the full pre-push sequence)
- When planning session work (to know what recurring maintenance is due)
- When setting up a new project (to configure its maintenance cadences)

---

## The Session Lifecycle

Every session follows this cycle. Each step has a defined trigger.

```
Session start (or compaction recovery)
    │
    ├─ Light cleanup (2–3 min)
    │
    ├─ Work phase
    │   ├─ Checkpoint if autonomous build > 30 min
    │   ├─ Push after completing a coherent unit of work
    │   │   └─ Full cleanup runs BEFORE each push
    │   └─ Continue working
    │
    ├─ Compaction approaching (context getting heavy)
    │   ├─ Finish current unit of work if close to done
    │   ├─ Push if meaningful uncommitted work exists
    │   └─ Checkpoint if mid-task
    │
    ├─ Compaction happens
    │   └─ Return to top: light cleanup → resume
    │
    └─ Session end
        ├─ Full cleanup
        ├─ Push
        └─ End-of-session protocol (see session discipline guide)
```

---

## The Work Cycle

The session lifecycle (above) describes maintenance. The work cycle describes how to approach a non-trivial task within a session. Each phase ideally starts at full context capacity.

```
Explore → Plan → [Fresh] → Implement → Verify → Improve
```

| Phase | What happens | Key principle |
|-------|-------------|---------------|
| **Explore** | Read codebase, trace dependencies, map existing patterns. Scout subagent if heavy. | Build understanding before committing to a direction |
| **Plan** | Scope work, decompose into subtasks, identify risks. Plan mode or structured discussion with Kurt. | Catch misunderstandings before 500 lines in the wrong direction |
| **[Fresh]** | Start a new session for implementation. The planning phase consumed context; execution starts clean. | Planning context is dead weight during implementation |
| **Implement** | Build from the plan. Autonomous if Design then Build mode. | Full attention capacity on the actual work |
| **Verify** | Run tests, review output, adversarial check. Separate session for review avoids builder bias. | Fresh context = no confirmation bias |
| **Improve** | Update CLAUDE.md with what worked. Capture lessons. Push. | The cycle compounds — each pass improves the next |

The `[Fresh]` step is optional for small tasks but critical for anything that consumed significant context during planning. The planning phase produces a refined brief; the implementation session starts with only that brief — not the exploration history.

**The "interview me" technique:** Before planning, tell Claude "Interview me about this feature." Claude asks clarifying questions that surface requirements you wouldn't have specified — producing dramatically better plans.

**Course correction during implementation:** Use `Esc` to stop Claude mid-action (context preserved, redirect). Use `Esc+Esc` or `/rewind` to restore any previous checkpoint. If you've corrected Claude more than twice on the same issue, `/clear` and start fresh with a better prompt — a clean session with a refined prompt always outperforms a cluttered one with accumulated corrections.

---

## Skills and Hooks in the Lifecycle

Two Claude Code extension mechanisms fit into the operational cycle:

### Skills (`.claude/skills/`)

Skills are `SKILL.md` files loaded on demand — domain knowledge, reusable workflows, and repeatable processes that don't consume CLAUDE.md budget. They auto-detect by relevance or invoke explicitly with `/skill-name`.

**Where skills fit:** Replace "conditional loading" patterns in CLAUDE.md routing. Instead of routing Claude to a guide file, the guide becomes a skill that Claude loads when it recognizes the task type. This reduces CLAUDE.md size and improves targeting.

### Hooks (`.claude/settings.json`)

Hooks are deterministic scripts that run at specific workflow points — after file edits, before commits, etc. Unlike CLAUDE.md rules (advisory), hooks execute every time with no exceptions.

**Where hooks fit:** Promote any cleanup checklist item that should *never* be skipped. Examples: lint after edit, naming convention validation before commit, secrets scan before push. Configure via `/hooks` or by editing `.claude/settings.json` directly.

---

## Cleanup Conventions

### Light Cleanup

**Trigger:** After every compaction recovery. Also after completing a major task mid-session.
**Time:** 2–3 minutes.
**What it covers:** Sections 0–4 of the cleanup checklist. Kurt input (0–1), then autonomous: context preservation, roadmap sync, mailbox check. "Did we lose anything?"
**Gate rule:** Do not act on the user's pending request until light cleanup is complete. The pending task will still be there after the checklist.

### Full Cleanup

**Trigger:** Before every `git push`. End of session. When Kurt says "run the cleanup checklist."
**Time:** 10–15 minutes.
**What it covers:** All sections (0–12) of the cleanup checklist. File inventory, naming, style, stale content, registry sweep, repo health.
**Output:** One copy-paste push block for all repos (chained with `&&` and `cd ../sibling`). Never split across multiple code fences.

### The Relationship

Light cleanup is a subset of full cleanup. They never conflict. The rule is simple:

| Event | Cleanup type |
|-------|-------------|
| Compaction recovery | Light |
| Major task completed mid-session | Light |
| Before git push | Full |
| End of session | Full |
| Kurt says "run the checklist" | Full (unless he specifies light) |

---

## Push Conventions

### When to Push

A push happens when there's a **coherent unit of work** worth committing. Not after every file edit, but not only at session end either.

| Signal | Push? |
|--------|-------|
| Completed a feature, guide, or design decision set | Yes |
| Finished a conversation that produced file changes | Yes |
| About to start a task that will consume heavy context (large file reads, long builds) | Yes — push first to back up current work |
| Compaction feels imminent (context getting heavy, long session) | Yes — push to back up, then checkpoint if mid-task |
| Fixed a typo or updated a date | No — batch with next meaningful change |
| Mid-task, files are in a transitional state | No — finish the unit of work first |

### Push Sequence

Every push follows this exact order:

1. Full cleanup (sections 0–12)
2. Stage specific files (not `git add -A`)
3. Commit with descriptive message
4. Present one copy-paste push block to Kurt
5. Wait for Kurt to paste and confirm

### Pre-Compaction Push

When context is getting heavy and compaction feels likely:

1. Assess: am I close to finishing the current task?
   - If yes → finish, then push
   - If no → push current state, then checkpoint the remaining work
2. After compaction → light cleanup → resume from checkpoint or roadmap

---

## Compaction Conventions

### Before Compaction

Claude cannot predict exactly when compaction will happen, but the signals are:
- Session has been long (many turns, many file reads)
- Context feels heavy (Claude's responses becoming less precise)
- Large file operations are about to happen

**When signals appear:**
1. Finish the current atomic unit of work if possible
2. Push if there are meaningful uncommitted changes
3. Write a checkpoint if mid-task with context that only lives in conversation
4. Ensure the roadmap reflects current state

### After Compaction

1. Run light cleanup (sections 0–3) — this is a gate, not optional
2. Re-read the roadmap What's Next queue
3. Check for checkpoint files
4. Resume the pending task
5. Do NOT apologize for compaction or re-explain what happened

---

## Cadence Inventory

All recurring processes, their triggers, and their frequencies.

### Per-Session Cadences

| Process | Trigger | Frequency | Time | Reference |
|---------|---------|-----------|------|-----------|
| Light cleanup | Compaction recovery | Every compaction | 2–3 min | Cleanup checklist §0–3 |
| Full cleanup | Before push / end of session | 1–3× per session | 10–15 min | Cleanup checklist §0–12 |
| Push | Coherent unit of work complete | 1–3× per session | 2 min | This guide § Push Conventions |
| Checkpoint | Long autonomous build / pre-compaction | As needed | 2 min | Session discipline guide |
| Lessons capture | Decisions, conventions, gotchas | Immediately when discovered | 1 min each | Session discipline guide |

### Periodic Cadences

| Process | Trigger | Frequency | Time | Reference |
|---------|---------|-----------|------|-----------|
| Reconciliation cycle | Scheduled or context bloat | Weekly | 30–60 min | Markdown architecture guide |
| Template freshness check | Part of reconciliation | Weekly (bundled) | 10 min | File type optimization guide § Template |
| Lessons harvest | Part of reconciliation | Weekly (bundled) | 15 min | Cross-project sweep of capture files |
| Token budget audit | Part of reconciliation | Weekly (bundled) | 10 min | Markdown architecture guide § Principle 3 |
| TEMP tag processing | Part of reconciliation | Weekly (bundled) | 5 min | Markdown architecture guide § TEMP convention |

### Project-Level Cadences

| Process | Trigger | Frequency | Time | Reference |
|---------|---------|-----------|------|-----------|
| Project reconciliation | Scheduled | Weekly per active project | 15–30 min | Cleanup checklist full mode |
| Archimedes mailbox check (per project) | Session start or routing trigger | Every session (if mailbox exists) | 1 min | TBD — mailbox design pending |
| Archimedes mailbox scan (all projects) | Scheduled | Weekly from Archimedes session | 10–15 min | TBD — mailbox design pending |

### Strategic Cadences

| Process | Trigger | Frequency | Time | Reference |
|---------|---------|-----------|------|-----------|
| Preferences alignment audit | Cleanup checklist §0 or scheduled | Monthly (bundled with reconciliation) | 15 min | Preferences reference file (in builder repo) — verify all 4 surfaces match |
| Toolkit research | Scheduled or new project need | Monthly | 1–2 hours | Tools research guide (TBD) |
| Official docs review | Scheduled | Monthly | 30–60 min | Docs monitor file (in builder repo) |
| Cold-read audit | Milestone or periodic | Per project, quarterly or per major milestone | 30–60 min | Separate auditor session |
| Project registry update | After project changes | As needed, verified monthly | 15 min | Project registry |

---

## Cross-References

- `session-discipline-guide.md` — session-level behavior (context budget, workflow modes, checkpoint timing)
- Cleanup checklist — the actual checklist Claude runs (this guide defines *when*, the checklist defines *what*)
- `markdown-architecture-guide.md` — reconciliation cycle details
- `file-type-optimization-guide.md` — type-specific maintenance considerations
- Roadmap template — What's Next queue drives session priorities
