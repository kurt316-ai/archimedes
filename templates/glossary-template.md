# {PROJECT_NAME} — Glossary

**Last updated:** {DATE}

Use these terms consistently across all project files. When in doubt, check here first. This prevents Claude from hallucinating meanings or using inconsistent terminology across sessions.

---

## Archimedes Terms (Universal — Include in Every Project)

These terms are standard across all Archimedes-equipped projects. Keep this section as-is.

| Term | Definition |
|---|---|
| **Archimedes** | The cross-project system of conventions, templates, guides, and infrastructure that governs how Kurt's projects are structured, documented, and maintained. The hub — all projects inherit from it. |
| **Archimedes protocols** | The full operating system Archimedes provides: folder structure (Two-Stack Model), file naming, cleanup checklist, mailbox communication, session discipline, writing style by audience, autonomy rules, and standing jobs. When Kurt says "are we following Archimedes protocols?" he means the whole package. |
| **Archimedes conventions** | Roughly equivalent to Archimedes protocols. Sometimes used more narrowly to mean the individual rules (naming patterns, date format, audience tags) vs. the full system. From a project's perspective, treat "protocols" and "conventions" as aliases — both mean "follow the Archimedes system." |
| **Two-Stack Model** | Folder architecture: Stack 1 (builder) = how the project works (`for-claude/`, `archimedes-mailbox/`, root files). Stack 2 (product) = what the project delivers (`output/`). Each stack has its own git repo. |
| **Archimedes mailbox** | Two-file communication channel (`archimedes-mailbox/inbox.md` + `outbox.md`) for bidirectional feedback between a project and Archimedes. Projects write to outbox; Archimedes writes to inbox. |
| **Standing jobs** | Eight outbound message types that run continuously (not batched for cleanup): project-announce, new-pattern, anti-pattern, convention-update, convention-conflict, tool-insight, question, module-request. Write to `archimedes-mailbox/outbox.md` when spotted. |
| **NACK-based flow** | Default action model. "I'll do this unless you stop me" — assumes action, only stops on objection. Opposite of ACK-based ("want me to do this?"). |
| **`[KURT ACTION]`** | Tag for items requiring human judgment. Escalation marker that Kurt or the next session can scan for. |

## Domain Terms

{Terms specific to this project's domain. What does this project call things?}

| Term | Definition |
|---|---|
| {Term} | {Definition} |

## Project-Specific Terms

{Abbreviations, code names, and conventions unique to this project.}

| Term | Definition |
|---|---|
| {Term} | {Definition} |

## People & Accounts

{Who's involved? What accounts matter?}

| Name | Role / Context |
|---|---|
| Kurt | Project owner |
| {Name} | {Role} |

## Technical Terms

{APIs, services, data structures, or technical concepts Claude needs to know.}

| Term | Definition |
|---|---|
| {Term} | {Definition} |

---

## Setup Instructions for Claude

**When to update this glossary:**
- A new term comes up in conversation that Claude might misinterpret later
- Kurt corrects Claude's use of a term
- A term is retired or renamed (mark it as retired, don't delete — future sessions might encounter the old term in other files)

**Convention:** Retired terms stay in the glossary with a note: `Retired. Use {new term} instead.`

**Cross-project terms:** When a term from this project might be useful across other projects, add it to the lessons-learned file under the **Glossary term** category. Archimedes-builder's harvest cycle will evaluate whether to promote it to the universal glossary (`references/glossary.md`).
