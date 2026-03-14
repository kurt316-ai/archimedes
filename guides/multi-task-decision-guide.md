# Archimedes — Multi-Task Decision Guide

**Audience:** `for-claude`
**Last updated:** Fri 13 Mar 2026
**Source:** Incorporates patterns from [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

When should a project use multiple Claude sessions, which models for which work, and how should they communicate? This guide helps you decide.

---

## Two Workflow Modes

Before deciding on multi-task, understand the two fundamental modes of working with Claude:

### Design then Build (Opus)

Kurt and Claude talk through the design — debating tradeoffs, making decisions, aligning on the plan. Once aligned, Claude goes autonomous for 15+ minutes: scaffolding, building, testing, iterating. Kurt comes back to review and push.

**When Archimedes is mature:** The autonomous window keeps growing. Claude doesn't stop to ask about naming conventions, deployment targets, or API patterns — it's all pre-loaded. Kurt's time is spent on design, not hand-holding.

**Key disciplines:**
- Capture all decisions in the roadmap before building
- Checkpoint if the autonomous stretch will be long
- End with files saved, push command ready, questions listed

### Pair Build (Sonnet)

Kurt and Claude work shoulder-to-shoulder in tight loops. Fast iterations, quick tests, rapid debugging. Sonnet's response speed matters because the feedback cycle is short.

**When to use:** Component implementation, CSS fixes, debugging, test writing — anything where the conversation is "try this, check result, adjust."

**Key disciplines:**
- Keep context lean — don't load architecture docs for a CSS fix
- Short responses. Don't explain unless asked.
- Test fast, fail fast.

### Composing Modes

These aren't mutually exclusive. A typical project session might:
1. Start in Design then Build (Opus) to plan the feature
2. Switch to Pair Build (Sonnet) to implement components
3. Return to Design then Build (Opus) for integration and review

Archimedes enables cold-start model switching — both Opus and Sonnet read the same CLAUDE.md and are instantly productive. No ramp-up penalty.

---

## Decision Tree: Do You Need Multiple Sessions?

Start here and follow the path that fits:

**Simple project, one person building →** Single session (Opus or Sonnet). No multi-task overhead needed. Use Design then Build for complex features, Pair Build for straightforward implementation.

**Research-heavy + build work →** Two sessions. Opus for research and planning (Design then Build), Sonnet for execution (Pair Build). Opus writes a plan file; Sonnet reads it and builds.

**Quality-sensitive project →** Add a periodic auditor. A separate Opus session does a cold read of the project folder following `guides/audit-guide.md` and writes findings to a `capture-` file in `for-claude/`. The builder session checks for audit findings at the start of each session (cleanup checklist Section 4.5).

**Mobile workflow needed →** Add Cowork ↔ Chat handoff protocol. Use handoff files to move context between desktop (Cowork) and mobile (Chat).

**Large project with parallel workstreams →** Multi-agent concurrent protocol. Multiple sessions working simultaneously, with clear file ownership boundaries.

---

## Subagent Patterns: Context Containment

The core insight: subagents are separate context windows. Heavy operations (large file reads, MCP calls, codebase exploration) happen in the subagent's context — not the orchestrator's. The orchestrator holds the map, not the territory.

| Pattern | What it does | When to use |
|---------|-------------|-------------|
| **Scout** | Explores codebase, produces structured report. Orchestrator works from the report. | Before planning — understanding the landscape without burning orchestrator context |
| **Adversarial Review** | Fresh session reviews code/output. Writer/reviewer separation eliminates confirmation bias. | After implementation — fresh context catches what the builder's biased context misses |
| **Parallel Specialists** | Multiple subagents working simultaneously on different domains (frontend, backend, tests). | Large features with independent workstreams. Use git worktrees for parallel file access. |

**The manager mental model:** Within a session, Claude is the manager (delegating to subagents). Across sessions, Kurt is the manager (directing which sessions work on what). This scales: Kurt can run multiple Claude sessions on different projects simultaneously, managing the portfolio while each agent manages its project.

**When NOT to delegate:** Small, well-understood edits are faster done directly. Subagent overhead (spawning, briefing, collecting results) only pays off when uncertainty, scale, or context containment makes delegation worth it.

### Formalizing Subagents with `.claude/agents/`

Claude Code supports persistent subagent definitions in `.claude/agents/`. Each agent gets its own system prompt, scoped tools, and optional model override:

```markdown
# .claude/agents/security-reviewer.md
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
Provide specific line references and suggested fixes.
```

**How this maps to Archimedes patterns:**

| Archimedes Pattern | `.claude/agents/` equivalent |
|---|---|
| Scout | Agent with Read, Grep, Glob tools. Explore codebase, return structured report. |
| Adversarial Review | Agent with Read tools + different model. Fresh context review. |
| Parallel Specialists | Multiple agents, each scoped to specific tools and files. |

**When to use `.claude/agents/` vs. ad-hoc subagents:** Formalize agents you reuse across sessions. Ad-hoc delegation is fine for one-off tasks. The `.claude/agents/` definitions persist in the repo and are available to all team members.

---

## Three Communication Protocols

Claude sessions can't talk to each other directly. Files are the communication layer.

### 1. Cowork ↔ Chat Handoff

**Use when:** Kurt needs to continue work on mobile (Chat) or bring mobile work back to desktop (Cowork).

**The problem:** Chat can't access the project folder. Handoff must be completely self-contained — everything Chat needs is in the handoff message.

**Direction:** Two-way.
- **Cowork → Chat:** Claude creates a self-contained handoff with all context Chat needs, plus return instructions.
- **Chat → Cowork:** Chat drafts a summary of what was done/decided. Kurt pastes it into the Cowork session or saves it as a file.

**Status:** Protocol template not yet built. Needs research on what makes a good handoff.

### 2. Builder ↔ Auditor

**Use when:** You want a fresh pair of eyes on the project — someone who wasn't involved in building it.

**The problem:** The builder session has context bias. It knows what it *meant* to write. An auditor reads what's *actually on disk* and catches blind spots.

**Direction:** One-way (auditor → builder). Builder decides what to act on.

**How it works:**
1. Auditor session opens the project folder cold (no prior context)
2. Reads `guides/audit-guide.md` from the Archimedes repo for the concrete checklist
3. Works through the checklist, checks against Archimedes standards
4. Writes findings to `for-claude/capture-{seq}-audit-findings-for-claude.md` using `templates/auditor-feedback-template.md`
5. Tags items needing human judgment with `[KURT ACTION]`
6. Builder session checks for new audit findings at session start (cleanup checklist Section 4.5)
7. Builder reads findings, acts on Critical/Important, batches Minor into cleanup

**Convention:** Audit findings files accumulate as `capture-` files in `for-claude/`. Don't delete old ones — they're a record of what was reviewed. Sequence numbers distinguish multiple audits.

### 3. Multi-Agent Concurrent

**Use when:** Multiple Claude sessions are working on the same project at the same time.

**The problem:** Two sessions editing the same files will overwrite each other. Need clear boundaries on who owns what.

**Direction:** Multi-way. Needs a coordinator role or strict file ownership.

**Key principles:**
- Each session owns specific files or folders — no overlapping writes
- A coordinator (human or a designated lead session) assigns work
- Output locations are pre-defined so sessions don't collide
- Handoff files mark what's done and what's ready for the next session

**Status:** Protocol template not yet built. Reference: bot-builder-toolkit's 4-agent workflow (Prompt Helper → TR Author → Builder Conductor → Builder).

---

## Model Selection Guide

Not all work needs the same model. Match the model to the task:

| Task Type | Recommended Model | Mode | Why |
|---|---|---|---|
| Architecture, planning, complex decisions | Opus | Design then Build | Strongest reasoning, can go autonomous |
| Research, analysis, writing | Opus | Design then Build | Nuanced understanding, long autonomous stretches |
| Code execution, file scaffolding, repetitive tasks | Sonnet | Pair Build | Fast, cheap, good enough |
| Quick lookups, simple edits | Sonnet or Haiku | Pair Build | Don't waste Opus tokens |
| Cold-read audits | Opus | N/A (auditor protocol) | Needs to catch subtle issues |
| Tech stack research sweeps | Sonnet | Design then Build | Broad but not deep |
| Project kickoff / scaffolding | Sonnet | Design then Build | Setup work, not reasoning |
| Debugging, test-fix cycles | Sonnet | Pair Build | Fast feedback loops |
| Feature integration, system-level thinking | Opus | Design then Build | Cross-cutting concerns need judgment |

**Rule of thumb:** If the task requires judgment, use Opus. If it requires execution speed, use Sonnet. If you're unsure, start with Opus for the plan and switch to Sonnet for the implementation.

**The north star metric:** How long can Claude operate without needing Kurt? Every Archimedes convention, every pre-loaded best practice, every captured decision extends the autonomous build window in Design then Build mode.

---

## Fan-Out: Batch and Non-Interactive Workflows

For large migrations, bulk analyses, or CI integration, Claude Code supports non-interactive mode:

```bash
# One-off queries
claude -p "Explain what this project does"

# Structured output for scripts
claude -p "List all API endpoints" --output-format json

# Batch processing with scoped permissions
for file in $(cat files.txt); do
  claude -p "Migrate $file from React to Vue. Return OK or FAIL." \
    --allowedTools "Edit,Bash(git commit *)"
done
```

**Key options:**
- `claude -p "prompt"` — non-interactive, no session. Exits when done.
- `--output-format json` or `stream-json` — machine-readable output for pipelines.
- `--allowedTools` — restrict what Claude can do in unattended mode. Critical for safety at scale.

**When to fan out:** When the task is the same operation applied to many files (migration, formatting, analysis). Generate the file list first, test on 2–3 files, refine the prompt, then run at scale.

**Relationship to Archimedes:** Fan-out is a Pair Build workflow at extreme scale — fast, repetitive, minimal judgment. Use Sonnet. Keep the per-file prompt tight. Verify a sample of outputs.

---

## Session Resumption

Sessions persist locally. Use them like branches:

- `claude --continue` — resume the most recent conversation
- `claude --resume` — pick from recent sessions
- `/rename` — give sessions descriptive names (e.g., `oauth-migration`, `debugging-memory-leak`)

**Relationship to Archimedes:** File-based checkpoints and roadmap state already enable cold-start recovery. Session resumption adds conversation continuity on top — useful when a session was interrupted but context is still fresh.
