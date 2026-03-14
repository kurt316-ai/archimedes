# Archimedes Mailbox — Message Template

**Last updated:** Fri 13 Mar 2026

This template defines the entry format and conventions for the two-file mailbox system (`inbox.md` and `outbox.md`). Copy the entry format below when adding new entries.

---

## Structure

```
archimedes-mailbox/
  inbox.md     ← FROM Archimedes (Archimedes writes, project reads)
  outbox.md    ← TO Archimedes (project writes, Archimedes reads)
```

Two persistent files. Entries are appended chronologically — never reordered.

---

## Entry Format

```markdown
## Entry — DDD DD MMM YYYY HHMM PT
**Status:** unread | harvested | actioned
**Type:** {see types below}
**Topic:** [short description]
**Content:**
[structured content — keep entries independent and scannable]
```

---

## Status Lifecycle

`unread` → `harvested` (reader has processed) → `actioned` (insight incorporated into target file)

---

## Message Types

**Outbound (project → Archimedes via `outbox.md`):**
- **project-announce** — "New project (or retrofit) now on Archimedes protocols." Mandatory first entry during setup. Includes project name, account, repo, tech stack, compliance status. Archimedes uses this to update the project registry.
- **convention-update** — "Convention X doesn't work well. Suggest changing to Y."
- **new-pattern** — "This pattern worked well and should be added to Archimedes."
- **anti-pattern** — "This failure mode should be captured in the anti-patterns guide."
- **tool-insight** — "Learned something about tool X that other projects should know."
- **question** — "Should we be doing X? Archimedes doesn't have guidance on this."
- **module-request** — "This project needs a module for X that doesn't exist yet."
- **convention-conflict** — "Archimedes convention X conflicts with what this project needs."

**Inbound (Archimedes → project via `inbox.md`):**
- **convention-update** — "Archimedes updated convention X. Here's what changed."
- **answer** — "In response to your question: here's the guidance."
- **new-module** — "A new module is available that applies to your project."
- **new-pattern** — "A pattern from another project may be useful here."
- **advisory** — "Heads up — something changed that may affect your project."

---

## Rules

- **Append-only.** New entries go at the bottom. Never reorder.
- **Entry independence.** Each entry must be useful without reading other entries. Include enough context.
- **Write immediately.** Don't batch entries for cleanup. Conversation dies at compaction; outbox entries survive.
- **Archimedes write access.** Archimedes has a narrow write exception to update `inbox.md` in any project's `archimedes-mailbox/`. This is the only cross-project write permission.

---

## Archimedes Convention Note

Archimedes itself does **not** use its own outbox for internal processing. When Archimedes harvests an outbox entry from a project, the response goes directly into the appropriate source-of-truth file (a guide, template, anti-pattern entry, etc.) — not back through inbox. The project's inbox is used only when Archimedes has a **proactive update** for the project (convention change, new module, advisory). Decision #177.
