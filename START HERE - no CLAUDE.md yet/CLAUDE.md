# Project Configuration

## Archimedes Protocols (Kurt & Claude Best Practices)

This project follows **Archimedes protocols** — a cross-project system of conventions, templates, and infrastructure for all of Kurt's Cowork projects. "Archimedes protocols" and "Archimedes conventions" are roughly equivalent terms — both mean "follow the Archimedes system."

**Archimedes repo:** https://github.com/kurt316-ai/archimedes

## First Session Instructions

**Do these in order. Don't skip steps.**

1. **Get access to Archimedes files.** Check for Archimedes files in this order:
   - **Local first:** Look for an `output/` folder in this project directory containing `start-here.md`. If found, use it.
   - **Clone if not local:** Try `git clone https://github.com/kurt316-ai/archimedes.git archimedes-ref` (this is a public repo — no credentials needed for read access).
   - **If clone fails (network/auth):** Stop here and ask Kurt to run this on his Mac:
     ```
     cd ~/Desktop/Claude\ Cowork\ Folders/{THIS_FOLDER} && git clone https://github.com/kurt316-ai/archimedes.git archimedes-ref
     ```
     Then tell Kurt to let you know when it's done. **Do not proceed until Archimedes files are accessible.**

2. **Start the Archimedes outbox immediately.** Create `archimedes-mailbox/outbox.md` in this project folder. From this moment forward, write an entry whenever you spot a pattern, convention conflict, guidance gap, or tool learning that Archimedes should know about. Don't wait for cleanup — write entries as they happen.

3. **Read `start-here.md` and follow it step by step.** It walks through the full project setup: install QC checklist, Two-Stack Model folder structure, core files (overview, roadmap, cleanup checklist, glossary, mailbox), CLAUDE.md routing, conditional modules, security, and the Idea Bin pipeline.

4. **Ask Kurt one question at a time** during Step 1 of `start-here.md`. Don't batch all questions in one message.

5. **Replace this file.** By the end of `start-here.md`, you'll have built a full project CLAUDE.md using `templates/router-readme-template.md`. Replace this seed file with that.

**What you'll create during setup:** `TEMPORARY-install-qc-checklist.md` (your progress tracker — delete when done). `for-claude/` folder with roadmap, cleanup checklist, and glossary. `archimedes-mailbox/` with inbox and outbox. Overview doc at root. Full CLAUDE.md router. Plus a `project-announce` outbox entry so Archimedes discovers this project.

---

_This is a seed file. After setup, Claude replaces it with a full project CLAUDE.md._
