# Anti-Patterns Guide

**Audience:** `for-claude`
**Purpose:** Failure catalog for the translation layer. Each anti-pattern is what happens when a human-facing principle or SOP fails to reach Claude's operational reality.
**Last updated:** Sat 14 Mar 2026

---

## What This Is

A catalog of things that have gone wrong across Kurt's projects, organized by category. Each anti-pattern includes what happened, why it's bad, the fix, and which human-layer principle or SOP it's the failure mode of (`Related:` line). The Related link makes the translation bidirectional — from any failure, you can trace back to the source principle.

Read this guide when starting a new project or when something feels off. Don't load it every session — use it as a diagnostic reference.

---

## Context & Compaction Anti-Patterns

### 1. Loading every file at session start

**What happens:** Claude reads CLAUDE.md, then the roadmap, then architecture, then cleanup checklist, then glossary, then every template — "just to be safe."

**Why it's bad:** Burns 30–50% of context budget before any work begins. Accelerates compaction. Most of those files aren't needed for the current task.

**Fix:** Read CLAUDE.md (the router). It tells you which files to load for each task type. Load only what the current task requires.

**Related:** Architecture Principles #1 (Route, Don't Store), #6 (Conditional Loading). Design Principle #4 (Dual Optimization).

### 2. Decisions that live only in conversation

**What happens:** Kurt and Claude agree to "use Railway for deployment" or "name files with the -v## suffix." The decision is never written to a file.

**Why it's bad:** After compaction, the decision is lost or degraded. The next session re-asks the question or silently makes a different choice.

**Fix:** Capture decisions in the roadmap's Key Decisions section immediately. Before moving to the next topic.

**Related:** SOP #2 (Write it down, don't just nod).

### 3. Long unstructured prose for critical facts

**What happens:** The architecture doc explains the deployment flow in a 500-word narrative paragraph. Buried in the middle: "The Notion approvals page ID is 320b34c38f2d8067a8abd984d59b3f2e."

**Why it's bad:** Compaction summaries lose detail from prose. The page ID gets dropped. Tables and key-value pairs survive much better.

**Fix:** Critical facts (IDs, URLs, credentials, accounts) go in tables or front matter. Prose explains concepts; tables store data.

**Related:** Architecture Principles #4 (Front-Load), #5 (Declarative Over Narrative).

### 4. Not running the cleanup checklist

**What happens:** Several sessions pass without running the checklist. Naming drift accumulates. Stale references pile up. The roadmap doesn't match reality.

**Why it's bad:** Each session builds on an increasingly wrong foundation. Small errors compound into big ones.

**Fix:** Light cleanup (Sections 0–3) after every compaction. Full cleanup before every push. The checklist's Section 0 is a meta-check — "does the checklist itself need updating?" — so it evolves with the project.

**Related:** Translation verification cadence. Design Principle #4 (Dual Optimization).

---

## Naming & Organization Anti-Patterns

### 5. Naming conventions in multiple places

**What happens:** Session artifact naming rules appear in the architecture doc AND the workflow doc AND the README. They drift apart over time.

**Why it's bad:** Claude reads one source that says `YYMMDD-name-type.md`, another that says `YYYY-MM-DD-name-type.md`. Picks one at random. Inconsistency spreads.

**Fix:** One canonical location for naming conventions. Everything else references it. In Archimedes projects, that's the naming conventions reference doc.

**Real example from Cato:** Naming rules initially scattered across builder-03, builder-06, and README. Consolidated into builder-03 as the canonical source. README and builder-06 now say "See builder-03 for naming conventions."

**Related:** Architecture Principle #9 (Rosetta Stone). SOP #11 (No version numbers).

### 6. Unprefixed folders that lose context

**What happens:** A folder called `audits/` at the project root. Is it builder audits? User audits? System audits?

**Why it's bad:** A cold-start Claude session can't tell what the folder is for without reading its contents. Wastes context budget on orientation.

**Fix:** Prefix every folder with its audience or purpose: `builder-audits/`, `cato-user-toolkit-v02/`, `archive-marvin/`. Self-documenting at a glance.

**Related:** Architecture Principle #7 (File Names = Free Metadata).

### 7. Mixing audiences in a single file

**What happens:** A doc starts with instructions for Claude ("Parse the JSON response like this...") then switches to instructions for Kurt ("When you see this error, restart the service").

**Why it's bad:** Claude reads the whole file to find its instructions. Kurt reads the whole file to find his. Both waste time. Writing style can't be optimized for either audience.

**Fix:** Separate files with audience tags. `for-claude` files are written for a technical audience (the next Claude session) — structured, no narrative. `for-kurt` files are conversational. `for-kurt-and-claude` files exist for shared references but should be the exception.

**Related:** Architecture Principle #5 (Declarative Over Narrative).

---

## Architecture & Design Anti-Patterns

### 8. Building all builder files on day one

**What happens:** New project starts. Claude creates a roadmap, architecture doc, cleanup checklist, glossary, decision log, constraints doc, checkpoint template, session tracker, improvement queue, and automations reference — before any actual work begins.

**Why it's bad:** Most of those files will sit empty or contain boilerplate. They waste context budget every time Claude checks whether they've been updated. The project isn't complex enough to need them yet.

**Fix:** Start with roadmap, cleanup checklist, and CLAUDE.md. Add files as complexity demands. A project with 5 files doesn't need a standalone decision log — decisions live in the roadmap.

**Real example:** Cato grew to 12+ builder files over months of daily use. Marvin has 9 builder files. A simple utility script might need 3. Scale to the project.

**Related:** Architecture Principle #3 (Token Budget). SOP #13 (Every project gets roadmap, checklist, naming).

### 9. Implicit file dependencies

**What happens:** README says "Read builder-02 if relevant." No specifics about when it's relevant or what it contains.

**Why it's bad:** Claude either loads it every time (waste) or never loads it (misses important context). "If relevant" is a judgment call that a fresh Claude session can't make well.

**Fix:** Task routing tables with explicit "Read" and "Do NOT read" lists per task type.

**Real example from Cato README:** Task "Process a session" → Read: builder-01, builder-03, builder-06, builder-10, kaizen library (latest for this author). Do NOT read: builder-05 (Kurt-only), builder-07 (Notion SOPs unless Notion task).

**Related:** Architecture Principle #6 (Conditional Loading). Architecture Principle #1 (Route, Don't Store).

### 10. Relitigating settled decisions

**What happens:** Session 5 revisits a decision made in session 2 — not because new information surfaced, but because the decision wasn't captured in a file. Claude proposes an approach that contradicts what was already agreed.

**Why it's bad:** Wastes Kurt's time re-explaining. Risks building the wrong thing. Erodes trust.

**Fix:** Key Decisions section in the roadmap. Before proposing an approach, check whether a decision already covers it. If it does, follow it. If new information genuinely changes things, flag it explicitly: "Decision #12 says X, but [new information] suggests we should reconsider."

**Related:** SOP #2 (Write it down).

---

## External Integration Anti-Patterns

### 11. Railway cronSchedule on persistent services

**What happens:** Railway's dashboard has a cronSchedule setting. Someone enables it for a project that runs as a persistent HTTP service (always-on, listening for requests).

**Why it's bad:** cronSchedule turns the deployment into a one-shot cron job. It runs once, then exits. The HTTP server is no longer listening. Incoming requests fail.

**Fix:** Never set cronSchedule for persistent services. Use an external cron (healthchecks.io, Railway's built-in cron service, or a separate cron worker) to hit the service's HTTP endpoint.

**Real example from Marvin:** CLAUDE.md now explicitly warns: "Do NOT add cronSchedule to railway.toml or Railway dashboard."

**Related:** Domain-specific — no SOP mapping. Note: candidate for Railway module content.

### 12. Silent Notion write failures

**What happens:** Code writes to a Notion page. The API call fails (wrong page ID, permissions issue, rate limit). The error is swallowed. Code moves on. The Notion page is stale.

**Why it's bad:** Kurt looks at Notion expecting updated content. Sees old data. Doesn't know whether the system ran or not.

**Fix:** Log every Notion operation. Errors bubble up. Write a scan summary after each run. Marvin now writes: "Scan: 2026-03-12 · CR-001 (3 hits), CR-002 (1 hit) · 47 events already correct."

**Related:** Domain-specific — no SOP mapping. Note: candidate for Notion module content.

### 13. Hardcoded IDs and secrets

**What happens:** A Notion page ID appears directly in source code. A Scrapfly API key is a string literal. A Google service account email is in a config file.

**Why it's bad:** IDs change. Keys rotate. When they do, you're searching code for hardcoded values. Worse: secrets in source code get pushed to GitHub.

**Fix:** Everything goes through environment variables. `.env.example` documents all vars. `.env` has actual values (gitignored). Railway dashboard holds production secrets.

**Related:** SOP #12 (.env for all secrets).

### 14. Scrapfly js_scenario format wrong

**What happens:** Scrapfly's js_scenario parameter expects a base64-encoded JSON array of actions. Developer sends it as a plain JSON object, or as an un-encoded array, or wrapped in an extra object layer.

**Why it's bad:** Takes 3+ deploy cycles to get right. No clear error message from the API.

**Fix:** Document the exact format: `Buffer.from(JSON.stringify(actionsArray)).toString('base64')`. NOT wrapped in an object. NOT plain JSON.

**Related:** Domain-specific — no SOP mapping.

---

## Process Anti-Patterns

### 15. Not capturing lessons learned

**What happens:** A session discovers something useful (a Scrapfly gotcha, a Notion API quirk, a naming pattern that works well). It's discussed in conversation but never written to the lessons-learned file.

**Why it's bad:** The next session hits the same issue. The project after that hits it again. The lesson is learned repeatedly instead of once.

**Fix:** Every session should capture at least one lesson. Use the lessons-learned template. Tag entries `for-archimedes` if they're useful across projects — Archimedes-builder harvests these during periodic sweeps.

**Related:** SOP #2 (Write it down). Translation pipeline step 3 (Encode).

### 16. Git add -A (add everything)

**What happens:** `git add -A` stages everything in the working directory, including `.env` files, service account JSON keys, and large binary files.

**Why it's bad:** Secrets get committed to Git history. Even if removed later, they're in the history. Force-push to remove them is destructive and doesn't guarantee the secret wasn't already pulled.

**Fix:** Always `git add specific-files`. Never `git add -A` or `git add .`. The push command block should list exactly which files are being staged.

**Related:** SOP #12 (.env for all secrets) — security implication.

### 17. git mv on uncommitted files

**What happens:** Claude uses `git mv` to rename a file that hasn't been committed yet (newly created or staged but never committed). Git fails with "not under version control" and leaves a `.git/index.lock` file behind.

**Why it's bad:** The stale `index.lock` silently blocks every subsequent git operation — `add`, `commit`, `push` all fail with "Unable to create index.lock: File exists." The error message doesn't explain *why* the lock exists, so it's confusing to diagnose. Kurt hit this during the KCBP→Archimedes rename.

**Fix:** Before using `git mv`, verify the file is committed (`git ls-files --error-unmatch <file>`). If it's not committed, use regular `mv` instead. If a stale `index.lock` already exists: `rm .git/index.lock` and retry.

**Related:** SOP #6 (Terminal commands: one copy-paste block) — git workflow domain.

### 18. Skipping the dry-run

**What happens:** A new rule or service is deployed directly to production without testing in dry-run mode first.

**Why it's bad:** The rule might match events it shouldn't. The service might write bad data to Notion. Rolling back requires manual cleanup.

**Fix:** Every project that writes to external systems should have a `DRY_RUN` mode. Marvin: `DRY_RUN=true` scans but doesn't write. Weekly Report: `FORCE_RUN=true` triggers immediately for testing but `DRY_RUN=true` should prevent actual sends.

**Related:** Design Principle #3 (Research when it matters) — verify before acting.

### 19. Offering push blocks after every logical unit of work

**What happens:** Claude finishes a set of changes (new anti-pattern, new convention, template update) and immediately offers a push block. Three rounds of changes produce three push blocks instead of one.

**Why it's bad:** Each push block costs Kurt a context switch to the terminal. Three pushes that could have been one means two unnecessary interruptions. It also creates the `&&`-between-repos problem more often — more push blocks means more chances for one repo being clean to block the other.

**Root cause:** Claude treats "finished a coherent set of changes" as a push trigger, using pushes as milestone markers between tasks. But git is backup, not the working store — files are already safe on disk.

**Fix:** Accumulate all changes across the session and provide one push block when the work is done or there's a natural pause. Present the block proactively ("here's the push block for everything this session") — don't ask or wait for a signal, which would violate the "default to action" preference. Track what's been modified so the final block is complete.

**Related:** SOP #18 (Accumulate changes, one push per session).

### 20. Asking permission for things already decided

**What happens:** Claude proposes a change, Kurt agrees and implements it (e.g., reorganizes folders), then after compaction Claude sees the result and asks Kurt to confirm what it should already know — or worse, asks whether it should update a file that obviously needs updating.

**Why it's bad:** Wastes Kurt's time re-confirming decisions that were already made. Violates the autonomy rules: anything prescribed by Archimedes conventions is pre-authorized.

**Root cause:** Compaction erases the conversational context where the decision was made. If the *result* of the decision (e.g., the registry update) wasn't written to a file at the time, post-compaction Claude sees a mismatch but lacks the context to resolve it confidently. So it asks. The deeper cause: Claude deferred a file update that should have been part of the original action.

**Fix:** When a decision produces a downstream file update (registry, roadmap, template, etc.), do the update as part of the same action — don't defer it to cleanup. Cleanup is a safety net, not the primary mechanism. During cleanup, if the folder structure doesn't match the registry, check the roadmap for context first and update — only ask Kurt if a folder is genuinely unrecognized.

**Related:** SOP #1 (Default to action / NACK).

### 21. `git rm` on files already deleted from disk

**What happens:** Claude deletes files using the Bash `rm` command during work, then later in the push block uses `git rm <file>` to stage the deletion. Git fails with "fatal: pathspec did not match any files" because the file is already gone from disk.

**Why it's bad:** The push block fails partway through, leaving the repo in a half-staged state. Kurt has to diagnose and fix manually or wait for a corrected block. We've hit this pattern multiple times.

**Root cause:** `rm` removes from disk only. `git rm` expects the file to either exist on disk or be tracked in the index. If both are gone, it fails. The mismatch happens when Claude uses Bash tools for file operations (which use `rm`) and then writes the push block assuming standard git workflow (which expects `git rm`).

**Fix:** When building push blocks that include deletions, check how the files were deleted. If deleted via Bash `rm` (not `git rm`), use `git add <folder>/` scoped to the affected directory — this picks up both additions and deletions. Alternatively, use `git add -u <specific-files>` to stage tracked-file changes including deletions. Never use `git add -A` (anti-pattern #16 still applies for the unscoped version).

**Related:** SOP #6 (Terminal commands: one copy-paste block) — git workflow domain.

### 22. The marathon session

**What happens:** A single conversation runs to 150k+ tokens across many tasks. By the end, Claude is confidently ignoring CLAUDE.md conventions and introducing subtle bugs.

**Why it's bad:** Context degrades before it fills. Past ~50% utilization, primacy and recency bias dominate — the model pays attention to the start (system prompt) and end (recent messages) but loses the middle. Quality drops silently. The model never says "I'm getting worse." A 200k-token marathon costs more in compute, latency, and rework than three focused 40k-token sessions producing the same output.

**Fix:** Start fresh after ~15-20 exchanges or any red flag (see session discipline guide). New task = new session. Starting fresh isn't losing progress — it's resetting to full attention capacity.

**Related:** Architecture Principle #3 (Token Budget). Design Principle #4 (Dual Optimization).

### 23. The trust-then-verify gap

**What happens:** Claude produces a plausible-looking implementation. It compiles. It looks right. But edge cases aren't handled, error paths are wrong, or the logic is subtly broken. Nobody checks because it *looks* correct.

**Why it's bad:** Kurt becomes the only feedback loop. Every unverified change is a latent bug. The cost of finding bugs later (in production, in a demo) is 10× higher than catching them at build time.

**Root cause:** Claude wasn't given verification criteria. Without tests, expected output, or screenshot comparisons, there's no way for Claude to check its own work.

**Fix:** Always provide verification alongside the task: test cases, expected output, visual references, or a script that validates the result. If verification doesn't exist yet, make creating it the *first* step — not an afterthought. "Write the test, then write the code" is more reliable than "write the code, then test it."

**From the official Claude Code best practices:** "This is the single highest-leverage thing you can do."

**Related:** Design Principle #1 (First principles over authority) — verify, don't assume.

### 24. The infinite exploration

**What happens:** Claude is asked to "investigate how X works" without scope. It reads dozens of files, follows every import chain, explores tangentially related modules. Context fills with exploration data. By the time it reports back, there's no room to act on the findings.

**Why it's bad:** The investigation consumed the context budget that the implementation needed. Restarting fresh loses the investigation. The exploration may have been broader than necessary.

**Fix:** Scope investigations narrowly ("look at the auth flow in src/auth/, specifically token refresh"). Better yet, use a subagent for exploration — the scout pattern. The subagent explores in its own context and returns a structured summary. The orchestrator's context stays clean for implementation.

**Related:** Architecture Principle #3 (Token Budget). Architecture Principle #6 (Conditional Loading).

### 25. Context poisoning from repeated failures

**What happens:** Claude fails a test, adjusts, fails again, adjusts again, fails a third time. Each failed attempt adds to the context — wrong approaches, error messages, abandoned strategies. By the third failure, the context is polluted with more wrong paths than right ones.

**Why it's bad:** The model's attention is now split across multiple failed approaches. It starts combining elements of previous failures or re-trying approaches it already abandoned. More attempts make it worse, not better.

**Fix:** The 3 strikes rule — if the same test fails 3 times, clear the session and reframe the problem in a fresh context. Push current work first so nothing is lost, then start a new session with only the refined problem statement.

**Related:** Architecture Principle #3 (Token Budget).

### 26. The over-specified CLAUDE.md

**What happens:** CLAUDE.md grows to include every convention, preference, gotcha, and exception. Hundreds of lines. Every session starts by reading all of it, consuming significant context budget.

**Why it's bad:** Claude starts ignoring rules because important ones get lost in the noise. The file becomes a kitchen drawer. Adding more rules doesn't improve compliance — it dilutes attention.

**Fix:** For each line, ask: "Would removing this cause Claude to make mistakes?" If not, cut it. Move domain knowledge to skills (loaded on demand). Move workflow steps to hooks (deterministic, no context cost). CLAUDE.md should route, not store. See file-type-optimization-guide for the Router file type constraints (under 500 tokens ideal, hard ceiling 1,000).

**Related:** Architecture Principle #1 (Route, Don't Store). SOP tier system (Tier 2 SOPs don't belong in routers).

### 27. ACK when NACK was appropriate

**What happens:** Claude identifies a set of changes to make, then asks "Want me to go ahead?" or "Should I fix these?" instead of stating "I'll make these changes unless you have alternate thoughts" or just making them.

**Why it's bad:** Wastes Kurt's time confirming things that don't need confirmation. Creates a back-and-forth where none was needed. Slows momentum. Worse, it shifts cognitive load to Kurt — he now has to evaluate whether to say yes instead of evaluating whether to say stop.

**Root cause:** Claude's default politeness instinct overrides the autonomy rules. The model errs toward asking permission because that feels safer — but in Kurt's workflow, the safe default is action, not inaction.

**Fix:** Use **NACK-based flow** — assume action, only stop on objection. Three escalation levels:
1. **Obvious context** (e.g., fixing a typo, updating a date, running the cleanup checklist) — just do it. No announcement needed.
2. **Clear but non-trivial** (e.g., restructuring a file, adding a new anti-pattern) — state what you're doing as you do it: "Adding this to the anti-patterns guide." Not a question.
3. **Ambiguous or high-risk** (e.g., deleting files, new architecture decisions) — NACK statement: "I'll make these changes unless you tell me otherwise." Then proceed after a beat.

Never use ACK-based flow ("want me to...?", "should I...?", "shall I go ahead?") for anything covered by existing Archimedes conventions. ACK is only appropriate when you genuinely don't know whether the action is wanted — not when you're hedging.

**Related:** SOP #1 (Default to action / NACK).

---

## Quick Reference: What to Check Before...

**Before implementing anything:** Do you have verification criteria? Tests, expected output, visual references, or a validation script? If not, create them first.

**Before building a new component:** Check roadmap for existing decisions. Check architecture for design patterns. Don't rebuild what already exists.

**Before naming a new file:** Check the naming conventions reference. Follow the pattern. Don't invent a new one.

**Before renaming files in a git repo:** Use `git mv` only for committed files. For uncommitted files, use plain `mv`. If a bulk rename involves a mix, check each file's status first or rename uncommitted files separately.

**Before pushing to GitHub:** Run the full cleanup checklist. Stage specific files (not `-A`). Check for secrets. If files were deleted via Bash `rm`, use `git add <folder>/` instead of `git rm` to stage deletions.

**Before deploying to Railway:** Test with DRY_RUN. Check railway.toml for cronSchedule (remove it for persistent services). Verify .env.example is current.

**Before writing to Notion:** Verify page IDs are correct. Log the operation. Check the result. Handle failures explicitly.
