# Claude Chat Module

**Audience:** `for-claude`
**Purpose:** Build Effectiveness (BE)
**Load when:** Project uses Claude Chat (web/mobile) for design discussions, brainstorming, or mobile work
**Last updated:** Fri 13 Mar 2026

---

## When to Load This Module

Load this module if:
- Kurt is working in Claude Chat (claude.ai web or mobile app)
- Cowork is preparing a handoff to Chat for a design discussion
- The project needs mobile-accessible collaboration

Do NOT load for Cowork or Claude Code sessions.

---

## What Claude Chat Is

Claude Chat is the web and mobile conversational interface. No file system access, no terminal, no MCP integrations. Pure conversation with optional file uploads and artifacts.

**Key distinction:** Chat is the lightest-weight interface. Best for thinking, not building. No persistence beyond the conversation (unless using Projects).

---

## Chat Strengths — Route Here For

1. **Design discussions** — Open-ended architecture debates, tradeoff analysis, brainstorming. Chat's conversational flow is better than Cowork for extended back-and-forth on ambiguous topics.
2. **Mobile work** — Kurt can continue design conversations from his phone. Cowork and Code require a desktop.
3. **Research and analysis** — Web search, document analysis, synthesis. When the output is insight (not files), Chat is sufficient.
4. **Quick questions** — "How does X work?" "What's the tradeoff between A and B?" No need to spin up Cowork for a 2-minute question.
5. **Writing and editing** — Drafting prose, editing copy, refining language. Artifacts provide inline preview.

## Chat Limitations — Route Elsewhere For

1. **File creation** — Chat can produce artifacts but can't save files to disk. Route to Cowork for docx, pptx, xlsx, pdf delivery.
2. **Code execution** — Chat can't run code, tests, or commands. Route to Code or Cowork.
3. **MCP integrations** — No Slack, Notion, Calendar, Drive access. Route to Cowork.
4. **Git operations** — No terminal. Route to Code.
5. **Persistence** — Conversations are ephemeral (without Projects). Decisions must be captured elsewhere.

---

## Chat-Specific Mechanics

### Projects (Persistent Context)

Chat supports Projects — collections of files and instructions that persist across conversations. This is Chat's equivalent of CLAUDE.md.

**When to use Projects:**
- Recurring design discussions for the same project
- Shared context that multiple conversations need
- Custom instructions that should apply to all conversations in a project

**Relationship to Archimedes:** A Project's custom instructions can reference Archimedes conventions. Keep it minimal — the same "route, don't store" principle applies.

### Artifacts

Chat produces artifacts (code, markdown, HTML, React, SVG, Mermaid) that render inline. Useful for:
- Visualizing architecture (Mermaid diagrams)
- Prototyping UI (React/HTML)
- Drafting documents (Markdown)

**Limitation:** Artifacts live in the conversation. To make them permanent, copy the content to a file via Cowork.

### File Uploads

Kurt can upload files for analysis. Chat reads the content but can't modify the original file. For edit-in-place workflows, use Cowork.

---

## Handoff Protocols

### Chat → Cowork

After a design discussion produces actionable decisions:

1. **Summarize decisions** — Ask Claude to produce a structured summary: what was decided, what's still open, what to build next.
2. **Organize by target** — Group feedback by destination file (roadmap, architecture doc, new component), not by conversation order.
3. **Kurt brings it to Cowork** — Paste the summary or key decisions. Cowork integrates into project files.

**Anti-pattern:** Don't try to do the file updates from Chat. Chat can't access the project files. The handoff is: Chat produces decisions, Cowork writes them down.

### Chat → Claude Code

When a design discussion is ready for implementation:

1. **Produce a spec** — Ask Claude to write a complete spec based on the discussion. Include success criteria, constraints, file paths, and testing approach.
2. **Kurt starts a Code session** — Paste the spec or save it to a file first.
3. **Code implements from the spec** — Fresh context, focused on execution.

### Cowork → Chat

When Cowork identifies a task that's better as a conversation:

1. **Summarize current state** — What's been decided, what's open, what files are relevant.
2. **Provide a starting prompt** — "Start a Chat conversation with: [prompt]. When done, bring back [specific deliverables]."

---

## Context Management in Chat

Chat has the same context window constraints as other interfaces, but fewer tools to manage them:

- **No `/compact`** — compaction happens automatically, unsteered
- **No `/rewind`** — can't undo to a previous state
- **No `/clear`** — start a new conversation instead
- **No file-based persistence** — unless using Projects

**Implication:** Chat sessions should be shorter and more focused than Cowork or Code sessions. One topic per conversation. When context is getting heavy, summarize and start fresh.

**The write-it-down rule still applies:** If a Chat conversation produces a decision, it must be captured in a file via Cowork or Code. Otherwise it's lost.

---

## Cross-References

- `cowork-module.md` — Cowork-specific mechanics, file delivery
- `claude-code-module.md` — Claude Code-specific tools
- `multi-task-decision-guide.md` — Cowork ↔ Chat handoff protocol
- `session-discipline-guide.md` — universal session principles (apply in Chat too)
