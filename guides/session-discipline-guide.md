# Session Discipline Guide

**Purpose:** Session workflow, context budget management, conversation hygiene, verification patterns, and end-of-session protocol.
**When to Load:** Start of any session (for orientation), or when session quality degrades (red flags, context issues).
**Audience:** `for-claude`
**Last updated:** Sat 14 Mar 2026
**Source:** Incorporates patterns from [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

---

## Principle

A Cowork session has a fixed context budget. Every file read, every conversation turn, every tool call consumes budget. When it runs out, compaction happens — and compaction always loses something. Session discipline is about spending budget on work that matters and avoiding waste that accelerates compaction.

---

## Verification: The Highest-Leverage Habit

Claude performs dramatically better when it can verify its own work — run tests, compare screenshots, validate output against expected results. Without verification, you become the only feedback loop, and every mistake requires Kurt's attention.

**Build verification into every task:**

| Strategy | Before | After |
|----------|--------|-------|
| **Provide success criteria** | "implement email validation" | "write validateEmail. Test: user@example.com → true, invalid → false, user@.com → false. Run the tests after implementing." |
| **Verify UI visually** | "make the dashboard look better" | "[screenshot] implement this design. Take a screenshot and compare to the original. List differences." |
| **Address root causes** | "the build is failing" | "Build fails with [error]. Fix it and verify the build succeeds. Address the root cause, don't suppress the error." |

**Rule:** If you can't verify it, flag it. Tell Kurt what verification is missing and suggest how to add it (test, script, visual check). Never ship unverified work and assume it's fine.

**`[KURT ACTION]` convention:** When recording findings, decisions, or recommendations in any file, tag items that require human judgment with `[KURT ACTION]`. This is the general escalation marker across all projects — it flags architecture decisions, ambiguous intent, risk tradeoffs, or anything outside existing conventions. Use it in audit findings, lessons learned, roadmap notes, checkpoint files — anywhere Claude writes something that needs Kurt's eyes before proceeding.

**Verification in Cowork:** Use subagents for adversarial review — a fresh context catches what the builder's biased context misses. See multi-task-decision-guide for the Adversarial Review pattern.

---

## The Two Workflow Modes

Archimedes supports two modes. Session discipline differs for each.

### Design then Build (Opus)

Kurt and Claude talk through the design, debate tradeoffs, make decisions. Once aligned, Claude goes autonomous for 15+ minutes. Kurt comes back to review and push.

**Discipline priorities:**
- Capture all design decisions in the roadmap immediately — before building
- Front-load file reads: load what you need for the build, then stop reading
- Don't ask Kurt questions you can answer from existing files
- Checkpoint if the autonomous build will be long (30+ min of Claude work)
- End with a clean state: files saved, push command ready, questions listed

### Pair Build (Sonnet)

Kurt and Claude work shoulder-to-shoulder. Fast iterations, quick tests, rapid debugging.

**Discipline priorities:**
- Keep context lean — don't load architecture docs for a CSS fix
- Short responses. Don't explain what you're doing unless asked.
- Test fast, fail fast. Don't over-plan.
- Save files frequently — Kurt may want to push mid-session
- Capture lessons only at the end, not between every iteration

---

## Session Sizing

One focused topic per session. Archimedes design sessions naturally run 30–50k tokens for a focused task (read files, discuss, edit, push). If a session is growing past that range, it's usually a sign of stacked tasks rather than one deep topic.

After a push, the decision is: is the next thing a continuation of *this* work, or a new topic? If new, start a fresh task in the Cowork sidebar. The task boundary is the signal, not a token counter.

## Folder Selection

Select the **project folder** as the working folder in Cowork — not the parent CCF directory.

- Archimedes work → select your Archimedes project folder. Its CLAUDE.md loads, cleanup checklist runs, Claude is oriented in 2–3 minutes.
- Other project work → select that project's folder so its CLAUDE.md loads.
- Read access to other folders (for lessons harvest, project registry sweeps) is declared in CLAUDE.md and works regardless of which folder is selected — as long as both folders are mounted.
- Never select the parent CCF folder. It's a directory, not a project. There's no CLAUDE.md there to orient from.

## Session Start Checklist

Every session, regardless of mode:

1. **Read CLAUDE.md.** This is your map. It tells you what the project is and where files are.
2. **Check for checkpoint files.** If one exists, read it — the previous session left you context.
3. **Check roadmap status.** What phase are we in? What was last completed? What's next?
4. **Don't read everything.** CLAUDE.md routes you to the right files. Load only what the current task requires.

Time budget for session start: under 2 minutes. If you're spending more than that orienting, the CLAUDE.md routing table needs improvement (note it in lessons learned).

---

## Context Budget Management

### What costs context budget

- Reading files (bigger files cost more)
- Long conversation turns (from you or Kurt)
- Tool calls and their output
- Error messages and stack traces
- Loading files you don't need

### What's cheap

- Short, focused responses
- Small targeted file reads (specific sections)
- Referencing file locations without reading them
- Asking Kurt a direct question instead of guessing

### The 50% cliff

Treat ~50% context utilization as the quality ceiling. Degradation is gradual, not cliff-like — but the practical effect is real. The model will never say "my quality is dropping." You have to notice the red flags (below) and act proactively. Past 50%, primacy bias strengthens (CLAUDE.md stays anchored, but middle conversation content fades), and the model starts making confident mistakes based on degraded context.

### Rules of thumb

**Read on demand, not on startup.** The router CLAUDE.md pattern exists specifically for this. Don't load every project file at the start of a session. Load each file when a task requires it.

**If a file is over 200 lines, read the section you need.** Use line offsets. Don't read the entire architecture doc when you need one table.

**Don't repeat file contents in conversation.** "I see the roadmap says X" is wasted tokens. Just act on what the roadmap says. Kurt can read the file himself.

**Avoid exploratory browsing.** Don't `ls` folders and `cat` files to "get oriented." Read CLAUDE.md, which already has the folder map and routing table.

**New task, new session.** If the topic shifts substantially (e.g., finishing a feature and moving to deployment config), start a fresh session. All the context from the previous task is dead weight for the new one. Starting fresh isn't losing progress — it's sharpening the tool.

**Steer compaction with `/compact` (Claude Code only).** When compaction is approaching, you can direct what to preserve: `/compact Focus on the API changes and modified file list`. Not available in Cowork — which makes file-based persistence even more critical there. See `claude-code-module.md` for full details.

**Use `/rewind` for course correction (Claude Code only).** Every Claude action creates a checkpoint. Press `Esc+Esc` or run `/rewind` to restore conversation, code, or both. In Cowork, use file-based checkpoints instead — they survive compaction and session boundaries. Use both when in Claude Code:
- `/rewind` → quick undo within a session (tried something, didn't work, revert)
- File-based checkpoint → survive compaction and session boundaries

**Use `/btw` for side questions (Claude Code only).** Quick lookups in a dismissible overlay that doesn't enter history. In Cowork, keep side questions short to minimize context cost.

**Copy-pasting is an automation signal.** If Kurt is copy-pasting anything into or out of a Claude session repeatedly, that's a signal to automate it — a skill, a template, or a CLAUDE.md convention can eliminate the manual step.

---

## When to Write a Checkpoint

Write a checkpoint file when:

- You're about to start a build that will take 30+ minutes
- The conversation has accumulated significant context that isn't in any file yet
- Kurt says he's stepping away and you'll be building autonomously
- You notice the session is getting long (100+ conversation turns)

Don't write a checkpoint when:

- The task is simple and will be done in the next few turns
- All relevant context is already captured in files (roadmap, architecture, etc.)
- You just wrote one recently

Checkpoints are disposable. Delete them when the task is complete.

---

## When to Capture Lessons Learned

**Capture immediately:**
- A decision that changes how the project works (→ roadmap Key Decisions)
- A convention that should apply going forward (→ constraints or naming doc)
- A gotcha that will bite the next session (→ lessons learned)

**Capture at end of session:**
- General observations about what worked well or poorly
- Patterns that might be useful across projects (tag `for-archimedes`)
- Suggestions for improving Archimedes templates or guides

**Don't capture during rapid iteration.** In Pair Build mode, save lessons for the end. Stopping to write lessons every 5 minutes breaks flow and wastes budget.

---

## Conversation Hygiene

### Default to action (NACK, not ACK)

Use **NACK-based flow** — assume action, only stop on objection. Not ACK-based flow ("want me to...?") which assumes inaction and waits for permission.

- **Obvious context** (cleanup, typo fix, date update) — just do it silently.
- **Clear but non-trivial** (adding an anti-pattern, restructuring a file) — state what you're doing as you do it. Not a question.
- **Ambiguous or high-risk** (deleting files, new architecture) — "I'll make these changes unless you tell me otherwise."

When the conversation's purpose is obvious (e.g., discussing roadmap additions), act on each item without reconfirming at all. See anti-pattern #27 for failure modes.

### Ask one question at a time

Asking Kurt three questions in one message forces him to address all three before work continues. Ask the most important one. If the answer doesn't resolve the others, ask the next.

### Don't narrate your plan

Bad: "I'm going to read the roadmap, then check the architecture doc, then look at the cleanup checklist, then start building the component."

Good: Just do it. If Kurt wants a plan, he'll ask.

Exception: In Design then Build mode, before going autonomous, summarize the build plan so Kurt can course-correct before stepping away.

### Don't apologize for compaction

After compaction, don't spend turns apologizing or explaining what might have been lost. Run the light cleanup checklist, recover state from files, and get back to work.

### Terminal commands: one copy-paste block

When Claude provides git commands, deploy scripts, or any terminal work, always format as a single multi-line block Kurt can copy-paste in one action. Never split across multiple code fences.

**Chaining rules:**
- **Within a single repo:** chain with `&&` (fail-fast — if one step fails, stop)
- **Between repos in multi-repo projects:** separate repos with `;` (not `&&`) so one repo being clean doesn't block the other
- **Always `cd` to the parent folder first**, then use relative paths into each repo

Bad:
```
cd ~/project/repo-a
git add -A && git commit -m "update"
git push origin main
```
then separately:
```
cd ~/project/repo-b
git add -A && git commit -m "update"
git push origin main
```

Good:
```bash
cd ~/Desktop/Claude\ Cowork\ Folders/Project && \
rm -f .git/index.lock .git/HEAD.lock && \
git add specific-files && \
git commit -m "update" && \
git push -u origin main ; \
cd output && \
rm -f .git/index.lock .git/HEAD.lock && \
git add specific-files && \
git commit -m "update" && \
git push -u origin main
```

Claude can explain what the block does above it, but the human action is always one copy-paste.

### Git command reliability

Defensive practices so push blocks don't fail mid-chain:

- **Start every push block with `cd` to the Mac-side path, then `rm -f .git/index.lock .git/HEAD.lock`** for each repo. The `cd` ensures Kurt doesn't need to be in the right directory — the Mac path is in the project CLAUDE.md under "Working Folder." The lock file cleanup covers both `index.lock` and `HEAD.lock`, which can both persist when Cowork VM git operations fail mid-execution. The `rm -f` is a no-op when there's no lock file.
- **Cowork agents: git read-only.** During cleanup, verification, and cross-reference checks, use only `git status`, `git diff`, `git log`, and `git ls-files`. Never run `git add`, `git commit`, or other write operations from within the VM — those create lock files that can't always be cleaned up. All git writes happen in the push block Kurt runs on his Mac.
- **Always `git push -u origin main`**, never bare `git push`. The `-u` flag sets upstream tracking if it's missing and is harmless if it's already set. Bare `git push` fails on repos that have lost their upstream tracking.
- **Always start with `cd` to the Mac-side path.** Kurt may have closed terminal or be in a different directory. The Mac path is in the project CLAUDE.md under "Working Folder."
- **Use `cd ../sibling` for multi-repo projects**, not a second absolute `cd`. The sibling folder structure (`output/` and `for-claude/`) means relative navigation is reliable once you're in the parent.
- **Add specific files, not `git add -A`**, unless you're sure every change should be committed. Listing files explicitly prevents accidentally staging secrets or temporary files.
- **If files were deleted via Bash `rm`**, don't use `git rm` in the push block — the file is already gone from disk and `git rm` will fail. Instead, use `git add <folder>/` scoped to the affected directory (picks up both additions and deletions) or `git add -u <file>` for specific tracked files. See anti-pattern #21.

When Kurt pastes terminal output back into Cowork, it means something went wrong — read the error, fix the command, and give a corrected single block.

### Keep responses proportional to the task

- Fixing a typo → one sentence
- Building a new component → structured plan + execution
- Design discussion → thoughtful paragraphs
- Status update → bullet points

---

## End-of-Session Protocol

Claude owns this protocol — Kurt should never have to ask for it. When Kurt signals "that's it," "let's wrap up," "push," or "I'm done," Claude runs the full cleanup and delivers the push block without being prompted for each step.

1. **All files saved.** Nothing exists only in conversation that should be in a file.
2. **Roadmap updated.** Phase status, completed items, new decisions.
3. **Lessons learned captured.** Anything worth preserving from this session.
4. **Push command ready.** Single copy-paste block. `&&` within each repo, `;` between repos. Never two separate code fences.
5. **Questions and next steps listed.** What should the next session do first?

If the cleanup gets skipped (roadmap not updated, dates stale, no push block), that's an anti-pattern — improve the convention, don't add a manual step for Kurt.

### Human Input First, Then Autonomous

Any checklist or routine that involves both human input and autonomous work must front-load the human input. Sections that require Kurt go first (self-check, lessons learned, "anything else?"). Once Kurt has answered, Claude runs the rest without asking further questions. Kurt should be able to walk away after the input sections.

This applies to both light and full cleanup modes, and to any future checklists or routines.

---

## Red Flags

Two categories: **budget waste** (fixable within the session) and **context degradation** (requires starting fresh).

### Budget Waste — fix the behavior and continue

- Re-reading a file you read 10 minutes ago
- Loading architecture docs for a naming question (use the naming conventions file)
- Writing a 500-word explanation when Kurt asked a yes/no question
- Exploring folder contents when CLAUDE.md has a folder map
- Asking Kurt a question that's answered in the roadmap
- Loading all project files "just in case"
- Debugging by re-reading code instead of running it

### Context Degradation — start fresh immediately

Any of these signals mean the session is past its quality ceiling. Push current work and start a new session:

- Claude repeats Kurt's instruction verbatim instead of acting on it
- Code stops following conventions from CLAUDE.md
- Claude confidently contradicts an earlier discussion or a decision in a file
- Kurt is repeating the same constraint for the third time
- Token meter past 50%
- Claude fails the same test 3 times (the **3 strikes rule** — the context is now polluted with failed approaches; reframe in a fresh session)

---

## Skills and Hooks: Extending Claude's Capabilities

Claude Code has two extension mechanisms that complement Archimedes conventions. See `claude-code-module.md` for full details; summary here for cross-reference:

### Skills (`.claude/skills/`)

Skills are `SKILL.md` files that give Claude domain knowledge and reusable workflows. Claude loads them on demand — they don't bloat every session like CLAUDE.md content does.

**How they relate to Archimedes:** Archimedes guides are already skill-shaped — structured, declarative, loaded conditionally. The difference is that `.claude/skills/` files get auto-detected by Claude when relevant, while Archimedes guides require CLAUDE.md routing. As Archimedes matures, consider migrating high-value guides into the skills format for automatic invocation.

**Workflow skills** can define repeatable multi-step processes (e.g., "fix a GitHub issue" → fetch issue, find files, implement, test, commit, PR). Use `disable-model-invocation: true` for skills with side effects that should only run when explicitly invoked.

### Hooks (deterministic automation)

Hooks run scripts automatically at specific points in Claude's workflow — after file edits, before commits, etc. Unlike CLAUDE.md instructions (which are advisory), hooks are guaranteed to execute.

**Where hooks beat conventions:** Parts of the cleanup checklist that should *never* be skipped (like lint after edit, or naming convention checks) are candidates for hooks. Configure via `/hooks` or `.claude/settings.json`.

**Rule of thumb:** If you've added a CLAUDE.md rule and Claude still forgets it, consider promoting it to a hook.

---

### The "Interview Me" technique

For non-trivial features, tell Claude "Interview me about this before planning." Claude will ask clarifying questions you wouldn't have thought to specify — producing dramatically better plans. Especially useful in Design then Build mode before going autonomous.
