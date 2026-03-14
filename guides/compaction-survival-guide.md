# Compaction Survival Guide

**Purpose:** What compaction is, what survives it, how to prevent information loss, and how to recover after it happens.
**When to Load:** After compaction recovery. Also useful when designing new files (to ensure they survive compaction well).
**Audience:** `for-claude`
**Last updated:** Sat 14 Mar 2026
**Source:** Incorporates patterns from [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

---

## What Is Compaction?

When a Cowork session runs long enough, the conversation history is summarized (compacted) to free up context budget. This is automatic and irreversible. After compaction:

- Conversation details are replaced by a summary.
- Files on disk are unchanged.
- Anything that existed only in conversation is gone or degraded.

**The core principle:** Structured facts in files survive compaction. Conversation doesn't. Design accordingly.

---

## What Survives vs. What Doesn't

### Survives (in files on disk)

- Roadmap decisions and phase status
- Glossary terms and definitions
- Reference tables (IDs, URLs, accounts)
- Cleanup checklist items
- Architecture and design docs
- Code files and configs
- Lessons-learned entries
- CLAUDE.md routing tables

### Usually lost or degraded (in conversation only)

- "We agreed to do X instead of Y" — unless captured in roadmap Key Decisions
- "Kurt said he prefers..." — unless captured in constraints or CLAUDE.md
- Mid-task state: what's done, what's next, what's blocked
- Context about why a particular approach was chosen
- Debugging context: what was tried, what failed, why
- Verbal agreements about naming, structure, or conventions

---

## Prevention: Write It Down Before You Need To

### 1. Capture decisions immediately

When a decision is made in conversation, write it to the roadmap's Key Decisions section before doing anything else. Format:

```
##. **YYYY-MM-DD:** [Decision]. [Alternatives considered]. [Reasoning].
```

Real example from Marvin: "Do NOT set cronSchedule in Railway dashboard — it turns a persistent HTTP service into a one-shot cron job." This decision, captured in CLAUDE.md, has prevented the same mistake across multiple sessions.

### 2. Use structured formats over prose

Tables, front matter, and key-value pairs survive compaction summaries far better than narrative paragraphs.

**Good — survives well:**
```markdown
| Env Var | Value | Where |
|---|---|---|
| NOTION_TOKEN | (in .env) | Railway dashboard |
| GOOGLE_SA_KEY_JSON | (stringified JSON) | Railway dashboard |
```

**Bad — gets summarized away:**
```
The Notion token is stored in the .env file and also needs to be in Railway.
The Google service account key should be a stringified JSON blob in Railway...
```

### 3. Front matter on every file

Version, date, audience tag. When a post-compaction session reads a file, front matter tells it instantly: is this current? who is it for? This is a 3-second orientation that prevents re-reading files that haven't changed.

### 4. Steer compaction with `/compact`

When compaction is approaching (or you want to trigger it early to reclaim context), use `/compact` with instructions to control what's preserved:

```
/compact Focus on the API changes, modified file list, and outstanding test failures
```

You can also set default compaction behavior in CLAUDE.md:
```markdown
When compacting, always preserve: full list of modified files, test commands, and active roadmap items.
```

This turns compaction from a blunt summarizer into a directed tool. Without instructions, compaction preserves what it thinks is important — which may not match what the current task needs.

### 5. Use the checkpoint template for long tasks

When working on something that will take more than ~30 minutes of conversation, write a checkpoint file:

- What we were working on
- Current state (completed / in-progress / next)
- Key context that might get lost
- Files modified this session

The checkpoint is disposable — delete it once the task is done. But if compaction hits mid-task, it's the fastest way to recover.

### Native checkpoints vs. file-based checkpoints

Claude Code has built-in checkpointing — every action creates a restore point. Press `Esc+Esc` or run `/rewind` to restore conversation, code, or both.

| Feature | Native (`/rewind`) | File-based (checkpoint template) |
|---------|-------------------|----------------------------------|
| **Scope** | Conversation + code state | Task context only |
| **Survives compaction** | No — `/rewind` checkpoints are conversation-bound | Yes — files persist |
| **Survives session end** | Yes — checkpoints persist locally | Yes |
| **Best for** | Quick undo, trying risky approaches | Cross-compaction recovery, multi-session handoffs |

**Use both.** `/rewind` for "let me try this and undo if it fails." File-based checkpoints for "I need this context to survive compaction."

---

## Recovery: What to Do After Compaction

### Step 1: Run the light cleanup checklist (Sections 0–3)

This takes 2–3 minutes and catches the most common post-compaction issues:

- **Section 0: Self-check.** Does the checklist itself need updating? (Requires Kurt)
- **Section 1: Kurt input.** Lessons learned, anything else before autonomous run. (Requires Kurt)
- **Section 2: Context preservation.** Read CLAUDE.md, roadmap, and any checkpoint files. Verify you know what was in progress.
- **Section 3: Roadmap sync.** Check that roadmap status matches what's actually on disk.
- **Section 4: Archimedes mailbox check.** Process any inbound messages.

### Step 2: Check for checkpoint files

If a checkpoint file exists, read it. It tells you exactly where the previous session left off. Once you've recovered the context, delete the checkpoint.

### Step 3: Don't re-read everything

The compaction summary gives you the gist. CLAUDE.md gives you routing. Read only what the current task requires. Loading every file to "catch up" is the worst thing you can do — it burns context budget and accelerates the next compaction.

---

## Design Patterns That Survive Compaction Well

### The router CLAUDE.md pattern

CLAUDE.md is small and always loaded. It tells Claude where to find things without embedding them. After compaction, Claude reads CLAUDE.md and knows the project structure — even if it forgot the details of specific files.

### Reference tables with "Last Fetched" columns

Real example from Cato: builder-11 has a table of Notion page IDs, person lookup data, and connector status. Each row includes a "Last Fetched" date. After compaction, Claude can check whether its cached knowledge is stale.

### Decision log as append-only record

Decisions are never edited, only appended. After compaction, Claude reads the latest entries to catch up on what changed. No risk of the summary omitting a critical decision.

### Immutable change logs

Real example from Marvin: `marvin-system/change-log/` stores every applied calendar change. After compaction, Claude reads the log to understand what the system has been doing — without relying on conversation memory.

---

## Anti-Patterns That Don't Survive Compaction

### "We discussed this earlier"

If it's not in a file, it didn't happen. After compaction, "earlier" is a summary paragraph. Anything important enough to reference later is important enough to write down.

### Long unstructured prose in architecture docs

Compaction summaries lose detail from prose. A 500-word explanation of how the approval flow works gets compressed to "the approval flow uses Notion." Tables and bullet points with specific values survive better.

### Implicit naming conventions

"We agreed to name files like X" — captured only in conversation. After compaction, the next session invents its own naming. Fix: canonical location for naming conventions, referenced by CLAUDE.md.

### Context-dependent abbreviations

"SC" means "Starship Captain" in Cato, but after compaction, the summary might not include the glossary. Fix: glossary file loaded on demand when unfamiliar terms appear.

---

## The Context Budget Tradeoff

Every file you load after compaction is context budget spent. There's a tension:

- **Load more** → better accuracy, faster compaction
- **Load less** → longer session, risk of mistakes

The resolution is **precise loading**: load exactly what the current task needs. The CLAUDE.md router enables this by mapping tasks to files. Don't load the architecture doc if you're just updating a glossary entry.

**Rule of thumb:** If you're not sure whether to load a file, check CLAUDE.md's task routing table. If the current task isn't listed there, you probably don't need the file.

---

## Quick Reference: Compaction Survival Checklist

1. Every decision goes to Key Decisions in the roadmap — immediately
2. Critical facts go in tables, not prose
3. Front matter on every file (version, date, audience)
4. Steer compaction with `/compact Focus on [what matters]`
5. Checkpoint file for tasks longer than 30 minutes
6. Use `/rewind` for in-session recovery, file-based checkpoints for cross-session
7. After compaction: light cleanup (Sections 0–3), then check for checkpoints
8. Don't re-read everything — use CLAUDE.md routing
9. If it's not in a file, it didn't happen
