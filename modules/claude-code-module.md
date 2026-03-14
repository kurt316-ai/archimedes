# Claude Code Module

**Audience:** `for-claude`
**Purpose:** Build Effectiveness (BE), Build Efficiency (BI)
**Load when:** Project uses Claude Code for implementation work, or Cowork is routing a task to Claude Code
**Last updated:** Fri 13 Mar 2026
**Source:** [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

---

## When to Load This Module

Load this module if:
- Kurt is working in a Claude Code terminal session
- Cowork is preparing a handoff to Claude Code
- The project's CLAUDE.md references Claude Code workflows

Do NOT load for Cowork-only sessions unless routing to Code is being considered.

---

## What Claude Code Is

Claude Code is a terminal-native agentic coding environment. Claude reads files, runs commands, makes changes, and works through problems autonomously. It's not a chatbot — it's a coding agent with tools for file manipulation, git, testing, and external service interaction.

**Key distinction from Cowork:** Claude Code has native tools for code-specific workflows (checkpointing, permissions, hooks, subagents, skills). Cowork has broader integration (MCP services, file delivery, visual artifacts). They complement each other.

---

## Claude Code-Specific Tools

### Context Management Commands

| Command | What it does | When to use |
|---------|-------------|-------------|
| `/compact Focus on X` | Steers compaction to preserve specific context | Before compaction, when you know what matters most |
| `/rewind` or `Esc+Esc` | Restores conversation + code to a previous checkpoint | Tried something that didn't work, need to undo |
| `/clear` | Resets context entirely | Between unrelated tasks, or after 2+ failed corrections |
| `/btw` | Side question in dismissible overlay, doesn't enter history | Quick lookup without growing context |
| `--continue` | Resume most recent conversation | Picking up where you left off |
| `--resume` | Choose from recent sessions | Finding a specific previous session |
| `/rename` | Name a session descriptively | Organizing sessions like branches |

**Cowork has none of these.** This is why Claude Code is better for long implementation sessions — recovery options are stronger.

### Permissions and Safety

| Command | What it does |
|---------|-------------|
| `/permissions` | Allowlist safe commands (e.g., `npm run lint`, `git commit`) to reduce approval interruptions |
| `/sandbox` | OS-level isolation for filesystem and network |
| `--dangerously-skip-permissions` | Bypass all permission checks (only in sandboxed environments without internet) |

**Rule of thumb:** After approving the same command 10 times, add it to the allowlist via `/permissions`.

### Skills (`.claude/skills/`)

Skills are `SKILL.md` files that give Claude domain knowledge and reusable workflows. They load on demand — no CLAUDE.md budget cost.

**Structure:**
```markdown
# .claude/skills/api-conventions/SKILL.md
---
name: api-conventions
description: REST API design conventions for our services
---
# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
```

**Workflow skills** with `$ARGUMENTS` for direct invocation:
```markdown
# .claude/skills/fix-issue/SKILL.md
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.
1. Use `gh issue view` to get the issue details
2. Search the codebase for relevant files
3. Implement the fix
4. Write and run tests
5. Create a descriptive commit and PR
```

Run with `/fix-issue 1234`. Use `disable-model-invocation: true` for skills with side effects.

**Relationship to Archimedes:** Archimedes guides are skill-shaped. As projects mature, high-value guides can migrate to `.claude/skills/` for automatic invocation instead of CLAUDE.md routing.

### Subagents (`.claude/agents/`)

Subagents run in their own context with scoped tools. They explore without cluttering your main conversation.

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

**Mapping to Archimedes patterns:**

| Archimedes Pattern | Agent definition |
|---|---|
| **Scout** | `tools: Read, Grep, Glob`. Explore codebase, return structured report. |
| **Adversarial Review** | `model: opus`, read-only tools. Fresh context review with no builder bias. |
| **Parallel Specialists** | Multiple agents, each scoped to specific tools and file paths. |

### Hooks

Deterministic scripts that fire at specific workflow points. Unlike CLAUDE.md instructions (advisory), hooks always execute.

**Use cases:**
- Lint after every file edit
- Naming convention check before commit
- Secrets scan before push
- Auto-format on save

Configure via `/hooks` (interactive) or `.claude/settings.json` (direct edit). Claude can write hooks: "Write a hook that runs eslint after every file edit."

**When to promote a CLAUDE.md rule to a hook:** If Claude keeps forgetting a rule despite it being in CLAUDE.md, the file is too long and the rule is getting lost. Make it a hook — guaranteed execution, zero context cost.

### Non-Interactive Mode and Fan-Out

For batch operations, CI integration, and automation:

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

# Pipeline integration
claude -p "<prompt>" --output-format json | your_command
```

**Key flags:**
- `--output-format json|stream-json` — machine-readable output
- `--allowedTools` — restrict what Claude can do unattended
- `--verbose` — debugging (off in production)

**Process:** Generate file list → test on 2–3 files → refine prompt → run at scale.

---

## CLAUDE.md in Claude Code

Claude Code reads CLAUDE.md at session start — same concept as Archimedes's CLAUDE.md routing pattern, but with some differences:

**What to include (Claude Code context):**
- Bash commands Claude can't guess
- Code style rules that differ from defaults
- Testing instructions and preferred test runners
- Repo conventions (branch naming, PR format)
- Architectural decisions specific to the project
- Dev environment quirks (required env vars)
- Common gotchas

**What NOT to include:**
- Anything Claude can figure out by reading code
- Standard language conventions Claude already knows
- Detailed API docs (link to them instead)
- Frequently changing information
- File-by-file codebase descriptions
- Self-evident practices ("write clean code")

**Size discipline:** If Claude keeps doing something wrong despite a CLAUDE.md rule, the file is probably too long and the rule is getting lost. Prune aggressively. Use emphasis ("IMPORTANT", "YOU MUST") for critical rules.

**Imports:** CLAUDE.md files support `@path/to/import` syntax for modular organization.

**Placement hierarchy:**
- `~/.claude/CLAUDE.md` — applies to all sessions (personal preferences)
- `./CLAUDE.md` — project root, checked into git
- Parent/child directories — auto-loaded contextually

---

## The Work Cycle in Claude Code

Same as Archimedes's universal work cycle, with Code-specific tools at each phase:

| Phase | Claude Code tool |
|-------|-----------------|
| **Explore** | Subagent (scout pattern) to keep main context clean |
| **Plan** | Plan Mode (`Ctrl+G` to edit plan in editor) |
| **[Fresh]** | `/clear` or new session |
| **Implement** | Normal Mode with verification criteria |
| **Verify** | Subagent (adversarial review) or separate session |
| **Improve** | Update CLAUDE.md, capture lessons, `/rewind` if needed |

---

## Common Failure Patterns in Claude Code

These are in addition to Archimedes's anti-patterns guide:

1. **Kitchen sink session** — Start with one task, ask something unrelated, go back. Context full of irrelevant info. Fix: `/clear` between unrelated tasks.
2. **Correction spiral** — Wrong, correct, still wrong, correct again. Context polluted with failed approaches. Fix: After 2 failed corrections, `/clear` and write a better initial prompt.
3. **Over-specified CLAUDE.md** — Too long, Claude ignores half of it. Fix: Prune ruthlessly. Promote to hooks or skills.
4. **Trust-then-verify gap** — Plausible output, no verification. Fix: Always provide tests or expected output.
5. **Infinite exploration** — "Investigate X" without scope. Reads hundreds of files. Fix: Scope narrowly or use subagent.

---

## Cross-References

- `cowork-module.md` — Cowork-specific mechanics, when to stay in Cowork
- `chat-module.md` — Chat-specific patterns
- `session-discipline-guide.md` — universal session principles
- `multi-task-decision-guide.md` — when to use multiple sessions or interfaces
- `anti-patterns-guide.md` — failure patterns (universal + Code-specific)
