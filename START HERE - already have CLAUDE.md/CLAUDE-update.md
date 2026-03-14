# Archimedes Update — Connect an Existing Project

**What this is:** Instructions for Claude to retrofit an existing project with Archimedes conventions.

**What to do:** Read this file top to bottom, then execute every step. Do not ask for permission — just do it. Write to the Archimedes outbox as you go (see Standing Jobs below).

---

## Standing Jobs — Active from This Moment

These run continuously from now through the end of every future session. When you notice any of the following, write an entry to `archimedes-mailbox/outbox.md` immediately with status `unread`:

- **project-announce** — mandatory first entry during retrofit (Step 9). Tells Archimedes this project is now on Archimedes protocols.
- **new-pattern** — something that worked well and should be added to Archimedes
- **anti-pattern** — a failure mode that should be captured
- **convention-update** — an Archimedes convention that doesn't work well; suggest a change
- **convention-conflict** — an Archimedes convention that conflicts with what this project needs
- **tool-insight** — something learned about a tool that other projects should know
- **question** — "Should we be doing X?" when Archimedes doesn't have guidance
- **module-request** — this project needs a module for something that doesn't exist yet

Don't batch these for cleanup. Write them when you spot them — conversation dies at compaction, outbox entries survive.

---

## Steps

### 1. Get Access to Archimedes Files

Check for Archimedes files in this order:
- **Local first:** Look for an `output/` or `archimedes-ref/` folder in this project directory containing `start-here.md`. If found, use it.
- **Clone if not local:** Try `git clone https://github.com/kurt316-ai/archimedes.git archimedes-ref` (this is a public repo — no credentials needed for read access).
- **If clone fails (network/auth):** Stop here and ask Kurt to run this on his Mac:
  ```
  cd ~/Desktop/Claude\ Cowork\ Folders/{THIS_FOLDER} && git clone https://github.com/kurt316-ai/archimedes.git archimedes-ref
  ```
  **Do not proceed until Archimedes files are accessible.**

### 2. Create the Install QC Checklist

Copy `templates/TEMPORARY-install-qc-checklist.md` to the project root. Fill in the project name and creation date. Mark items as you complete each step below.

### 3. Read Archimedes Conventions

**Required reading (in order):**
1. `start-here.md` — the full setup guide (you'll use it as reference, not run it end-to-end since this project already exists)
2. `templates/router-readme-template.md` — the target CLAUDE.md structure
3. `templates/naming-conventions.md` — file naming rules
4. `guides/security-conventions-guide.md` — PII guardrails
5. `guides/session-discipline-guide.md` — git, cleanup, compaction conventions

**Skim for awareness:**
- `guides/idea-bin-synthesis-guide.md` — for when Kurt has a brain dump to process
- `templates/cleanup-checklist-template.md` — you'll customize this for the project
- `templates/roadmap-template.md` — you'll restructure the existing roadmap (if any)

### 4. Set Up the Two-Stack Model

Archimedes projects use the **Two-Stack Model** — two stacks, each tracked by its own git repo:

```
{project-parent-folder}/
├── CLAUDE.md                              ← Router (local only, not in any git repo)
├── {project}-overview-for-kurt.md         ← Kurt's orientation file (private, not in public repos)
├── for-claude/                            ← Workshop (flat, type-prefixed, private git repo)
│   ├── design-1-roadmap-for-claude.md
│   ├── guide-1-cleanup-checklist-for-claude.md
│   └── (more files as needed)
├── output/                                ← Deliverable (public or private git repo, separate from for-claude/)
│   ├── guides/                            ← Claude-facing guides
│   ├── modules/                           ← Claude-facing conditional modules
│   ├── references/                        ← Claude-facing reference docs
│   ├── templates/                         ← Template files (if applicable)
│   └── (human-facing files at root)
└── archimedes-mailbox/                    ← Cross-project coordination
    ├── inbox.md                           ← FROM Archimedes
    └── outbox.md                          ← TO Archimedes
```

**Migration actions:**
- Create `archimedes-mailbox/` with empty `inbox.md` and `outbox.md` (use entry format from `templates/archimedes-mailbox-message-template.md`)
- If the project has a single repo, propose the two-repo split to Kurt. NACK-based: "I'll set up the Two-Stack Model unless you want to keep the current structure." Most projects benefit from the split.
- If files exist that belong in `for-claude/` but live elsewhere, propose moves to Kurt (don't move without asking — this is a structural change)
- Ensure CLAUDE.md is in `.gitignore` and lives at root only
- Create `.gitignore` now if it doesn't exist, even before `git init` — standard entries: `CLAUDE.md`, `.env`, `.env.*`, `output/` (from builder repo), overview doc (from public repos)

### 5. Update CLAUDE.md with Archimedes Integration

**Rebuild the full CLAUDE.md** using `templates/router-readme-template.md` as the target structure. Preserve existing project-specific content (task routing, domain context) but restructure into the Archimedes format. Key sections to add or update:

- **"What This Project Is"** — one-sentence description + "This project follows Archimedes protocols" + repo URL
- **Standing Jobs** — 8 outbound message types, "write when you spot them"
- **Working Folder** — Mac path, GitHub repos, git push block convention (`cd` to Mac path, `rm -f .git/index.lock .git/HEAD.lock`)
- **Folder Structure** — Two-Stack Model diagram
- **Start Here** — ordered reading list
- **After Compaction** — gate: run light cleanup (Sections 0–4) before acting on pending requests
- **Task Routing table** — map this project's task types to the right files
- **Naming Convention** — reference `[type]-[seq]-[topic]-for-[audience].md`
- **Writing Style** — all 4 audience styles
- **Archimedes Mailbox** — inbox/outbox description + entry format
- **Autonomy Rules** — pre-authorized actions, NACK-based flow, `[KURT ACTION]` tag
- **Key Principles** — first principles, intelligent pushback, research when it matters

### 6. Self-Assess Against Archimedes Standards

Check the project against conventions and report gaps. Don't fix anything yet — just list what's missing or non-compliant.

**Checklist:**

- [ ] **Naming conventions** — do files follow `[type]-[seq]-[topic]-for-[audience].md` in `for-claude/`? No version numbers in filenames?
- [ ] **Folder structure** — does it match the Two-Stack Model? Is there a clear builder/deliverable split?
- [ ] **Cleanup checklist** — does one exist? Does it follow the template (Light/Full modes)?
- [ ] **Roadmap** — does one exist? Does it use the `for-claude` optimized format (What's Next table, decision log)?
- [ ] **Overview doc** — does `{project}-overview-for-kurt.md` exist at root?
- [ ] **CLAUDE.md routing** — can a fresh Claude session navigate the project from CLAUDE.md alone?
- [ ] **Idea Bin** — if the project is pre-build or has unstructured ideas, does it have a capture file?
- [ ] **Security** — are `.env`, `CLAUDE.md`, and overview files excluded from public repos? Is `.gitignore` set up with standard exclusions?
- [ ] **Git reliability** — does the push process include `cd` to Mac path + `rm -f .git/index.lock .git/HEAD.lock`?
- [ ] **Cowork interface** — is Cowork documented as the default Claude interface (plus others if applicable)?

### 7. Run the Build-Decision-Tree

Based on the project's tools and complexity, determine which conditional modules and guides apply. See `start-here.md` Step 5 for the full decision tree.

Key decisions:
- Which Claude interfaces does this project use? (Cowork is default — note it explicitly, plus others if applicable)
- Which tools/platforms? (Notion, Railway, Google Sheets, etc.)
- What's the complexity level? (single-session, multi-session, parallel workstreams)

Load the relevant modules from `modules/` (in the Archimedes repo) and note any that don't exist yet — write those gaps to the Archimedes outbox.

### 8. Create or Restructure Core Project Files

Based on the self-assessment (Step 6), create any missing files and restructure existing ones:

**Always needed:**
- Overview doc (`{project}-overview-for-kurt.md` at root — conversational, describes the product for Kurt)
- Roadmap (restructure to match `templates/roadmap-template.md` if it exists but doesn't match)
- Cleanup checklist (create from `templates/cleanup-checklist-template.md`)
- Glossary (create from `templates/glossary-template.md` — includes universal Archimedes terms section)
- Archimedes mailbox (`inbox.md` + `outbox.md`)

**Create if missing:**
- Constraints doc (from `templates/constraints-template.md`) — create when first hard rule is identified
- Idea Bin (from `templates/idea-bin-template.md`) — create if project has unstructured ideas to capture

**Key principle: Don't replace working files.** If the project already has a roadmap, align it with the Archimedes template format over time — don't start over. Convergence over 2–3 sessions, not a one-shot rewrite.

### 9. Write Initial Outbox Entries

Write to `archimedes-mailbox/outbox.md` with everything you've learned during setup.

**Mandatory first entry — `project-announce`:**
```markdown
## Entry — {DDD DD MMM YYYY HHMM PT}
**Status:** unread
**Type:** project-announce
**Topic:** Retrofit: {project name} now on Archimedes protocols
**Content:**
- **What:** {one-line description}
- **Claude account:** {personal or Northslope}
- **GitHub:** {repo URL}
- **Mac path:** ~/Desktop/Claude Cowork Folders/{folder}/
- **Tech stack:** {tools/platforms}
- **Archimedes compliance:** Brownfield retrofit — CLAUDE-update.md applied this session.
- **Gaps found:** {summary from Step 6 self-assessment}
```

**Additional entries (as applicable):**
- What the project does well that Archimedes should know about
- What gaps the self-assessment found
- Any patterns or conventions this project uses that might benefit other projects
- Any convention conflicts discovered during migration
- Any modules that were needed but don't exist yet

### 10. Run the Install QC Checklist

Open `TEMPORARY-install-qc-checklist.md` and verify every section:

1. **Punch through remaining items** — any unchecked boxes from Steps 1–9
2. **Run Section 9 (Final Self-Audit)** — re-read every file you created or modified, compare against templates
3. **Fill in the Summary** — total checks, passed, failed/deferred
4. **Clean up bootstrap artifacts** (Section 8) — tell Kurt to delete the Archimedes reference files (`archimedes-ref/` or `output/`) from this project folder
5. **Report to Kurt** — "Retrofit complete. Here's what passed, what's deferred, and what you need to do manually."
6. **Delete `TEMPORARY-install-qc-checklist.md`** and this file (`CLAUDE-update.md`) — their jobs are done
