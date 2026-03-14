# Claude Cowork Module

**Audience:** `for-claude`
**Purpose:** Build Effectiveness (BE), Build Efficiency (BI)
**Load when:** Project uses Cowork as the primary Claude interface
**Last updated:** Fri 13 Mar 2026
**Source:** Incorporates patterns from [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

---

## When to Load This Module

Load this module if:
- The project's CLAUDE.md specifies Cowork as the primary interface
- Kurt is working in a Cowork session (this is the default — Cowork is Kurt's primary interface)

Do NOT load for Claude Code terminal sessions or Claude Chat web sessions — use those respective modules instead.

---

## What Cowork Is

Cowork is Claude's desktop application mode. It runs in a lightweight Linux VM on Kurt's computer with controlled access to a workspace folder. It can execute code, create files, use MCP integrations (Slack, Notion, Google Calendar, Google Drive), and interact with web browsers.

**Key distinction from Claude Code:** Cowork is visual, file-oriented, and integrated with external tools. Claude Code is terminal-native and code-focused. They share the same underlying model but have different tool sets, different UX, and different optimal workflows.

---

## Cowork Strengths — Stay Here For

1. **File creation and delivery** — docx, pptx, xlsx, pdf, html, jsx. Cowork renders artifacts and provides download links. This is its primary delivery mechanism.
2. **Multi-tool orchestration** — Slack search + Notion update + Calendar check + file creation in one session. Cowork's MCP integrations make this seamless.
3. **Design discussions** — Back-and-forth with Kurt about architecture, planning, tradeoffs. Cowork's chat interface is better for conversation than a terminal.
4. **Research and analysis** — Web search, Google Drive search, document reading, synthesis into deliverables.
5. **Project management** — Updating roadmaps, running cleanup checklists, capturing lessons. Archimedes's core workflow.

## Cowork Limitations — Route to Claude Code For

1. **Deep codebase work** — Cowork can read/write files, but Claude Code has `.claude/skills/`, `.claude/agents/`, hooks, `/rewind`, `/compact`, and other code-native tools. For multi-file refactors, debugging sessions, or test-driven development, Claude Code is better equipped.
2. **Git operations** — Cowork can run git commands via Bash, but Claude Code's native git integration (checkpointing, branch awareness, PR creation) is more robust.
3. **Fan-out / batch processing** — `claude -p` for non-interactive batch operations, `--allowedTools` for scoped permissions. Not available in Cowork.
4. **CI/CD integration** — Claude Code plugs into pipelines. Cowork doesn't.
5. **Long autonomous builds** — Claude Code's `/rewind` and built-in checkpointing give better recovery options for 30+ minute autonomous work than Cowork's file-based checkpoints alone.

---

## Cowork-Specific Mechanics

### Context and Compaction

Cowork has the same context window as Claude Code. All Archimedes context management principles apply:
- Read on demand, not on startup
- Token budget tiers (under 1k inline, 1–3k ideal, over 5k split)
- Front-load high-value information
- File-based persistence survives compaction; conversation doesn't

**Cowork does NOT have:** `/compact` with instructions, `/rewind`, `/btw`, `/clear`. These are Claude Code features. In Cowork, compaction is automatic and unsteered. This makes file-based persistence *even more critical* — you can't steer what survives.

### Skills in Cowork

Cowork has its own skills system (separate from Claude Code's `.claude/skills/`). Skills in Cowork are loaded via the Skill tool and provide specialized capabilities (pdf, docx, pptx, xlsx creation). Always check available skills before starting file creation tasks.

### Working Folder

Kurt selects a folder when starting a Cowork session. Files saved to `/sessions/.../mnt/{folder-name}/` persist on Kurt's actual computer. Files outside this path are temporary and lost when the session ends.

**Convention:** All final deliverables go to the workspace folder. Use the session's working directory for scratch work.

### MCP Integrations

Cowork can connect to external services via MCP:
- **Slack** — search, read channels/threads, send messages, create canvases
- **Notion** — search, create/update pages and databases, manage views
- **Google Calendar** — list events, create/update events, find meeting times
- **Google Drive** — search and read documents

**When to use MCP vs. browser:** Always prefer MCP (API-based) over browser automation. MCP is faster, more reliable, and cheaper on context. Only fall back to browser when no MCP exists for the service.

---

## Handoff Protocols

### Cowork → Claude Code

When Cowork identifies that a task needs Claude Code:

1. **Document the task** — Write a clear brief in a file (not just conversation). Include: what to build, success criteria, relevant file paths, constraints.
2. **Tell Kurt** — "This task is better suited for Claude Code because [reason]. Here's what to do: open a terminal, run `claude`, and give it this prompt: [prompt]."
3. **Provide the prompt** — Give Kurt a complete prompt he can paste into Claude Code, referencing the brief file.

### Cowork → Chat

When a task is pure conversation (brainstorming, design debate, mobile-friendly):

1. **Summarize current state** — What's been decided, what's open, what files are relevant.
2. **Tell Kurt** — "This is a good Chat conversation. Start with: [prompt]. When you're done, bring the decisions back and I'll update the files."

### Chat/Code → Cowork

When work done elsewhere needs to land in Archimedes-managed files:

1. **Kurt pastes the output** — or shares a file.
2. **Cowork integrates** — Update roadmap, lessons learned, architecture docs. Run cleanup checklist.
3. **Push** — Standard push protocol.

---

## Cross-References

- `session-discipline-guide.md` — session-level behavior (applies in Cowork)
- `compaction-survival-guide.md` — compaction resilience (especially critical in Cowork where compaction is unsteered)
- `multi-task-decision-guide.md` — when to use multiple sessions or interfaces
- `claude-code-module.md` — Claude Code-specific tools and workflows
- `chat-module.md` — Chat-specific patterns
