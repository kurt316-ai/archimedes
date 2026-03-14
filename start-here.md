# Archimedes — Start Here Guide

**Last updated:** Sat 14 Mar 2026

This is the first thing Claude reads when bootstrapping a new project with Archimedes. Follow these steps in order. By the end, the project folder will have all the right files and conventions in place.

---

## Standing Jobs — Active from This Moment

These run continuously from now through the end of every future session. When you notice any of the following, write an entry to `archimedes-mailbox/outbox.md` immediately with status `unread`.

**What qualifies — the 8 outbound message types:**
- **project-announce** — mandatory first entry during setup (greenfield or retrofit). Tells Archimedes this project exists.
- **new-pattern** — something that worked well and should be added to Archimedes
- **anti-pattern** — a failure mode that should be captured in the anti-patterns guide
- **convention-update** — an Archimedes convention that doesn't work well; suggest a change
- **convention-conflict** — an Archimedes convention that conflicts with what this project needs
- **tool-insight** — something learned about a tool that other projects should know
- **question** — "Should we be doing X?" when Archimedes doesn't have guidance
- **module-request** — this project needs a module for something that doesn't exist yet

These map to the lessons-learned categories (Pattern, Anti-pattern, Convention, Tool insight, Workflow, Glossary term, Archimedes suggestion). If you'd capture it in lessons-learned, it probably also belongs in the outbox.

Don't batch these for cleanup. Write them when you spot them — conversation dies at compaction, outbox entries survive.

---

## Brownfield Projects — Retrofit Path

If the project already has files, a codebase, or an existing CLAUDE.md, don't start from scratch. Use the **CLAUDE-update.md** file (`START HERE - already have CLAUDE.md/CLAUDE-update.md`) — it's a self-contained 9-step retrofit guide designed for exactly this situation.

**Quick summary of what CLAUDE-update.md does:**

1. **Read Archimedes conventions** — required reading list, prioritized
2. **Set up the Two-Stack Model** — create `archimedes-mailbox/`, map existing files to the target structure
3. **Upgrade CLAUDE.md** — rebuild from `templates/router-readme-template.md`
4. **Self-assess** — checklist of what's compliant vs. what needs work
5. **Run the Build-Decision-Tree** — identify which modules apply
6. **Scaffold missing core files** — roadmap, cleanup checklist, glossary, mailbox (use Step 3's universal table as the checklist)
7. **Write initial outbox entries** — including a `project-announce` entry
8. **Report to Kurt** — what's done, what gaps remain, what's next
9. **Delete CLAUDE-update.md** — it's done its job

**Key principles:**
- **Adopt incrementally.** Convergence with Archimedes protocols over 2–3 sessions, not a one-shot rewrite. The cleanup checklist catches remaining gaps each session.
- **CLAUDE.md is the highest-leverage single change.** A brownfield project with a good router gets 80% of the benefit immediately.
- **Don't replace working files.** If the project already has a roadmap, align it with the Archimedes template format over time — don't start over.

---

## Greenfield Projects — Full Setup

The steps below assume a new project starting from scratch.

---

## Step 0: Create the Install QC Checklist

**Do this first.** Copy `templates/TEMPORARY-install-qc-checklist.md` to the project root. Fill in the project name and creation date.

This file tracks every setup step. Mark items as you complete them. At the end (Step 11), run the final self-audit using this checklist, report results to Kurt, and delete the file.

---

## Step 1: Understand the Project

Gather this information from Kurt. **Ask one question at a time** — don't batch all six. Skip any he's already answered in CLAUDE.md or conversation.

1. **What are we building?** (one sentence)
2. **What's the end product?** (deployed app, set of docs, automation, tool, etc.)
3. **What tools/platforms will this project use?** (Notion, Railway, Google Sheets, Twilio, etc.)
4. **How complex is this?** (simple one-session task, multi-session project, large multi-workstream effort)
5. **Is there an existing codebase or are we starting fresh?**
6. **Does Kurt have a brain dump or idea bin to process?** (If yes, the Idea Bin pipeline runs during or after setup.)

**Efficiency note:** If Kurt volunteers multiple answers at once, great — don't re-ask. The one-at-a-time rule prevents Claude from dumping six questions in one message, not from accepting multiple answers.

---

## Step 2: Set Up the Project Folder Structure (Two-Stack Model)

Create the standard Archimedes folder structure. This follows the **Two-Stack Model** — two stacks, each tracked by its own git repo:

```
{project-parent-folder}/                   ← STACK 1: Builder (how this project works)
├── .git/                                  ← Builder repo (private)
├── .gitignore                             ← Excludes output/ and CLAUDE.md
├── CLAUDE.md                              ← Router (local only, gitignored)
├── {project}-overview-for-kurt.md         ← Kurt's orientation file (builder repo)
├── for-claude/                            ← Workshop (flat, type-prefixed, builder repo)
│   ├── design-1-roadmap-for-claude.md
│   ├── guide-1-cleanup-checklist-for-claude.md
│   ├── ref-1-glossary-for-claude.md
│   └── (more files as needed)
├── archimedes-mailbox/                    ← Cross-project coordination (builder repo)
│   ├── inbox.md                           ← FROM Archimedes (Archimedes writes, project reads)
│   └── outbox.md                          ← TO Archimedes (project writes, Archimedes reads)
│
└── output/                                ← STACK 2: Product (what this project delivers)
    ├── .git/                              ← Output repo (public or private)
    ├── guides/                            ← Claude-facing guides
    ├── modules/                           ← Claude-facing conditional modules
    ├── references/                        ← Claude-facing reference docs
    ├── templates/                         ← Template files (if applicable)
    └── (human-facing files at output/ root)
```

**Two-Stack Model:** Stack 1 (builder) = everything about *how* the project works. Stack 2 (product) = everything the project *delivers*. A file belongs in the stack it serves. The audience tag (`for-kurt` vs `for-claude`) determines which layer within a stack; the stack determines which folder.

**Key rules:**
- `CLAUDE.md` lives at the parent folder root — local only, in `.gitignore`
- `for-claude/` is flat (no subfolders) — builder repo `.git` is at the project root (not inside `for-claude/`)
- `output/` is consumer-first: Claude-facing content in `output/guides/`, `output/modules/`, `output/references/`; human-facing files at root; templates at root
- `archimedes-mailbox/` sits at the parent folder level, tracked by the builder repo
- The overview doc (`{project}-overview-for-kurt.md`) sits at root — never push to public repos (contains PII)

**Naming convention:** `[type]-[seq]-[topic]-for-[audience].md` in `for-claude/`. No version numbers — git tracks versions.
Full reference: `templates/naming-conventions.md`

---

## Step 3: Scaffold Core Files

Every project gets these files. Copy from Archimedes templates and customize:

### Universal (every project, create in order)

| File to Create | Location | Template Source | Priority |
|---|---|---|---|
| Overview doc | `{project}-overview-for-kurt.md` (root) | No template — written from Step 1 answers | **First** — Kurt's orientation to the project |
| Roadmap | `for-claude/design-1-roadmap-for-claude.md` | `templates/roadmap-template.md` | **First** — defines what we're building |
| Cleanup checklist | `for-claude/guide-1-cleanup-checklist-for-claude.md` | `templates/cleanup-checklist-template.md` | **Second** — establishes quality process |
| Glossary | `for-claude/ref-1-glossary-for-claude.md` | `templates/glossary-template.md` | **At setup** — includes universal Archimedes terms plus project-specific terms as they arise |
| Archimedes mailbox | `archimedes-mailbox/inbox.md` + `outbox.md` | `templates/archimedes-mailbox-message-template.md` | **At setup** — enables Archimedes communication |
| Constraints doc | `for-claude/` | `templates/constraints-template.md` | When first hard rule is identified |

**Overview doc note:** The overview is `for-kurt` — conversational, plain English, enough context to orient Kurt after weeks away. Write it from the Step 1 answers. It describes the *product* (what we're building); the roadmap describes the *build* (how we're building it). If the project uses the Idea Bin pipeline (Step 6), the synthesis process produces both the overview and roadmap together.

### When Needed

| File to Create | Location | Template Source | When |
|---|---|---|---|
| Idea Bin | `for-claude/capture-1-idea-bin-for-kurt-and-claude.md` | `templates/idea-bin-template.md` | When Kurt has unstructured ideas to capture (brain dump path) |
| Decision log | `for-claude/` | `templates/decision-log-template.md` | When project has 20+ decisions (otherwise keep in roadmap) |
| Checkpoint | `for-claude/` | `templates/checkpoint-template.md` | Mid-session when context might degrade |
| Auditor feedback | `for-claude/capture-{seq}-audit-findings-for-claude.md` | `templates/auditor-feedback-template.md` + `guides/audit-guide.md` | Before deployment, after major milestones, or on cadence |

---

## Step 4: Build the Project CLAUDE.md

Create the project's `CLAUDE.md` at the parent folder root using `templates/router-readme-template.md` as the base. Customize:

1. Fill in project name, description, Mac path, GitHub repos
2. Include the **Standing Jobs** section (already in the template — customize trigger types if needed)
3. Build the **Task Routing table** — map this project's task types to the files Claude should read
4. Set up the **Archimedes Mailbox** section with entry format
5. Add **Autonomy Rules** — what's pre-authorized vs. what requires asking
6. Add **After Compaction** gate — run light cleanup (Sections 0–4) before acting on pending requests
7. Include the Archimedes repo URL so future sessions can find updated conventions

---

## Step 5: Run the Build-Decision-Tree

Based on what Kurt described in Step 1, determine which conditional modules and guides apply.

### Complexity Check

| If the project is... | Then... |
|---|---|
| Simple, one-session task | Skip the builder folder. Just build the thing. |
| Multi-session project | Full Two-Stack setup (Steps 2–4 above). |
| Research-heavy + build | Opus for research/planning, Sonnet for execution. See `guides/multi-task-decision-guide.md` |
| Quality-sensitive | Add periodic auditor. See `guides/multi-task-decision-guide.md` |
| Mobile workflow needed | Add Cowork ↔ Chat handoff protocol. See `modules/chat-module.md` |
| Large, parallel workstreams | Multi-agent concurrent protocol. See `guides/multi-task-decision-guide.md` |

### Claude Interface — Conditional Modules

| If the project uses... | Load this module | Status |
|---|---|---|
| **Cowork** (desktop, primary) | `modules/cowork-module.md` | ✅ Built |
| **Claude Code** (terminal, implementation) | `modules/claude-code-module.md` | ✅ Built |
| **Claude Chat** (web/mobile, design) | `modules/chat-module.md` | ✅ Built |

Most projects default to Cowork with occasional routing to Code or Chat. Load the Cowork module by default; load others when routing decisions come up.

### Tech Stack — Conditional Modules

| If the project uses... | Load this module | Status |
|---|---|---|
| **Notion** (databases, pages, API) | `modules/notion-module.md` | ✅ Built |
| **Railway** (hosting, deployment) | `modules/railway-module.md` | ✅ Built |
| **Google Sheets** (UI layer, data) | `modules/google-sheets-module.md` | ⚠️ Not yet built |
| **Twilio** (SMS, voice) | `modules/twilio-module.md` | ⚠️ Not yet built |
| **Resend** (transactional email) | `modules/resend-module.md` | ⚠️ Not yet built |
| **healthchecks.io** (monitoring) | `modules/healthchecks-module.md` | ⚠️ Not yet built |
| **Scrapfly** (web scraping) | `modules/scrapfly-module.md` | ⚠️ Not yet built |

**When a module doesn't exist yet:** Don't skip it. Ask Kurt about his preferences for that tool, create project-specific notes in `for-claude/`, and write an outbox entry so Archimedes knows the module is needed.

### Project Type — Specialized Patterns

| If the project involves... | Consider... |
|---|---|
| Website automation / bots | API-first approach + rate limiting + error handling |
| Data pipelines | Checkpoint/retry patterns + monitoring with healthchecks.io |
| User-facing tools | Google Sheets or Notion as UI layer + style consistency |

---

## Step 6: Idea Bin Pipeline (If Applicable)

If Kurt has unstructured ideas, a brain dump, or a semi-structured description:

1. Create the Idea Bin file from `templates/idea-bin-template.md`
2. Have Kurt dump everything into it (or transcribe from conversation)
3. When Kurt says "ready to synthesize" — follow `guides/idea-bin-synthesis-guide.md` (in Archimedes repo)
4. Synthesis produces two files: the overview doc (`{project}-overview-for-kurt.md` at root) and the roadmap (`for-claude/design-1-roadmap-for-claude.md`)

This pipeline is reentrant — it can run at project kickoff or mid-project when new ideas accumulate.

---

## Step 7: Tech Stack Research Sweep

For each tool the project will use, do a quick web research sweep:

1. Is this still the best tool for the job? (Check for meaningfully better alternatives — don't switch for minor improvements)
2. Any breaking changes or deprecations since Kurt last used it?
3. Any new features that would change our approach?

Document findings in `for-claude/` as `research-` prefixed files. Flag anything significant to Kurt.

**Convention:** This sweep runs once at project kickoff. Don't re-run every session — only when Kurt asks or when a tool issue comes up.

---

## Step 8: Security Setup

Read `guides/security-conventions-guide.md` (in Archimedes repo) and apply:

1. Set up `.gitignore` for each repo (see guide for standard entries)
2. Verify CLAUDE.md is excluded from all repos
3. Verify overview doc is excluded from public repos
4. Verify `.env` files are excluded everywhere
5. Note the pre-push security checklist — this runs before every `git push` to public repos

---

## Step 9: Replace the Seed File

Once setup is complete, **replace the seed CLAUDE.md** with the full project CLAUDE.md you built in Step 4. The seed file's job is done.

Confirm with Kurt: "I've set up the project with Archimedes conventions. Here's what I created: [list files]. Ready to start building?"

---

## Step 10: Write Initial Outbox Entries

Write everything you learned during setup to `archimedes-mailbox/outbox.md`. Also update the install QC checklist (Section 5) as you write these.

**Mandatory first entry — `project-announce`:**
```markdown
## Entry — {DDD DD MMM YYYY HHMM PT}
**Status:** unread
**Type:** project-announce
**Topic:** New project: {project name}
**Content:**
- **What:** {one-line description}
- **Claude account:** {personal or Northslope}
- **GitHub:** {repo URL or "not yet created"}
- **Mac path:** ~/Desktop/Claude Cowork Folders/{folder}/
- **Tech stack:** {tools/platforms}
- **Archimedes compliance:** Greenfield — set up from start-here.md this session.
```

This entry tells Archimedes the project exists. Archimedes discovers it during its next cleanup sweep and adds it to the project registry.

**Additional entries (as applicable):**
- Gaps in Archimedes guidance discovered during setup
- Modules that were needed but don't exist
- Patterns from this project that might benefit others
- Any convention conflicts

This is the first test of the standing jobs pattern. If you noticed things worth writing during Steps 1–9, they should already be in the outbox. Step 10 is the backstop.

---

## Step 11: Run the Install QC Checklist

Open `TEMPORARY-install-qc-checklist.md` and verify every section:

1. **Punch through remaining items** — any unchecked boxes from Steps 0–10
2. **Run Section 9 (Final Self-Audit)** — re-read every file you created, compare against templates, check for unfilled placeholders
3. **Fill in the Summary** — total checks, passed, failed/deferred
4. **Clean up bootstrap artifacts** (Section 8) — tell Kurt to delete the Archimedes `output/` folder and any `START HERE` folders from this project. These are Archimedes's files, not this project's.
5. **Report to Kurt** — "Install complete. Here's what passed, what's deferred, and what you need to do manually."
6. **Delete `TEMPORARY-install-qc-checklist.md`** — its job is done

---

## Quick Reference: Kurt's Universal Preferences

These apply to every project unless Kurt says otherwise:

- **Date format:** `DDD DD MMM YYYY HHMM PT` (military time, Pacific)
- **APIs over browser automation** — always prefer APIs when they exist
- **`.env` for all secrets** — never hardcode credentials
- **Every project gets:** roadmap, cleanup checklist, naming conventions
- **GitHub accounts:** `kurt316-ai` (personal, default), `kurt-ai-316` (Northslope)
- **Git push blocks:** always start with `cd` to Mac path (from CLAUDE.md "Working Folder"), then `rm -f .git/index.lock .git/HEAD.lock` for each repo
- **Git in Cowork agents:** read-only commands only (`status`, `diff`, `log`, `ls-files`). Never `add`, `commit`, or other write operations from within the VM.
- **Primary reader** of project docs is the next Claude session, not Kurt
- **Structured facts in files** survive compaction — conversation doesn't
- **Northslope** is one word. Each project specifies which account it uses.
- **Write-it-down rule:** When Kurt states a preference or correction, write it to the appropriate file immediately — don't just acknowledge in conversation.
