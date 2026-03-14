# Archimedes — Best Practices for Kurt & Claude

The nucleus of how you build with Cowork. Archimedes evolves best practices, propagates the relevant ones out to every project, and absorbs lessons learned back from each one — so problems only get solved once, you only explain things once, and Claude never repeats mistakes.

Any project built with Archimedes maintains a living connection to this repo. Any time you re-open a project, it checks for the latest updates relevant to that project.

---

## Why "Archimedes"?

**"Give me a lever long enough and I'll move the world."** That's what this system does — it's leverage. A small investment in capturing best practices amplifies your output across every project.

Archimedes was the quintessential problem solver who bridged theory and practice. He didn't just solve individual problems — he built foundations (displacement, the lever, the screw) that let you solve entire classes of problems. That's exactly what the templates, modules, and guides here do: solve a class of problems once so you never re-solve them.

The parallels run deep. The **Archimedes screw** is a simple mechanism that moves water uphill against gravity. This system moves knowledge uphill against entropy — compaction, forgetting, context loss. Simple mechanism, powerful result. And every **"Eureka!"** moment — every anti-pattern spotted, every lesson learned — gets captured before it's forgotten, through the real-time capture loop.

---

## START HERE

Claude Cowork works out of folders on your desktop. Each project gets its own folder, and Cowork reads that folder to understand the project.

**Do you have a `CLAUDE.md` file in your project folder?**

**NO** (new project or no CLAUDE.md yet):
1. Create a project folder on your desktop with your preferred project name (e.g., `my-app`, `client-proposal`)
2. Open the `START HERE - no CLAUDE.md yet` folder above and copy the `CLAUDE.md` file into your project folder
3. Open a new Cowork task and point it at your project folder — Claude takes it from there

**YES** (adding Archimedes to an existing project):
1. Open the `START HERE - already have CLAUDE.md` folder above
2. Copy `CLAUDE-update.md` into your project folder
3. Open the folder in Cowork and tell Claude: "Read CLAUDE-update.md and follow the instructions"
4. The update file deletes itself when done

---

## What is this repo?

This repo contains the conventions, templates, and best practices that power Archimedes. The starter files above point Claude here. You don't need to read the rest of the repo — Claude does that for you.

**What's inside:**
- **`guides/`** — Best practices for sessions, security, audits, markdown architecture, and more
- **`templates/`** — Project scaffolding (CLAUDE.md router, roadmap, cleanup checklist, glossary, etc.)
- **`modules/`** — Conditional modules loaded per tool (Cowork, Claude Code, Chat, Notion, Railway)
- **`references/`** — Shared reference docs (universal glossary)
- **`start-here.md`** — The full setup guide Claude reads during project bootstrap
