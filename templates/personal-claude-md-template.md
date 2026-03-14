# {YOUR_NAME}'s Personal CLAUDE.md

## Who I Am

{YOUR_NAME} (initials: {YOUR_INITIALS}). Two Claude accounts:
- **Personal:** `{your-personal-email}` — GitHub: `{your-github-username}`
- **Work:** `{your-work-email}` — GitHub: `{your-work-github-username}`

Each project specifies which account it uses in its own CLAUDE.md.

## Archimedes

This file is part of Archimedes (Kurt & Claude Best Practices). The Archimedes repo is at `github.com/kurt316-ai/archimedes`. If a project has its own CLAUDE.md pointing to Archimedes, follow that project's setup instructions — they take precedence over this file for project-specific work.

## Universal Preferences

- **Date format:** `DDD DD MMM YYYY HHMM PT` (military time, Pacific). Example: `Thu 12 Mar 2026 1045 PT`
- **APIs over browser automation** — always prefer APIs when they exist
- **`.env` for all secrets** — never hardcode credentials
- **Every project gets:** roadmap, cleanup checklist, naming conventions
- **File naming:** `[type]-[seq]-[topic]-for-[audience].md` in `for-claude/`, `[project]-[topic]-for-[audience].md` at root. No version numbers anywhere — git tracks versions. Full spec: `output/templates/naming-conventions.md`
- **Folder skeleton:** project root (`CLAUDE.md` + `[project]-overview-for-kurt.md`), `for-claude/`, `output/`, `archimedes-mailbox/`
- **Northslope** is one word

## Writing Style

- `for-claude` — Written for a technical audience (the next Claude session). Structured. No narrative.
- `for-kurt` — Conversational. Plain English.
- `for-kurt-and-claude` — Structured but readable.
- `for-archimedes` — Same style as `for-claude`. Signals this file feeds back into Archimedes.

## Kurt's Prose Style (for human-facing text)

- **Numbered outline form** where relevant — not everything needs to be paragraphs.
- **Lists follow a logical order.** The two most common defaults: priority order for thinking (rank by importance), sequential order for doing (order by sequence). But any intentional logic works — scope order (small → large), specificity (broad → narrow), etc. The point is that the order is never arbitrary.
- **Max 3 wrapped lines per paragraph.** If you hit that, split into another paragraph. Dense blocks are hard to scan.
- **Enumerate, don't embed.** When listing items inside a paragraph, break the paragraph and use numbered outline form instead.
- **Bold key words** to make them pop — the reader should be able to skim the bolds and get the gist.
- **Key points format:** numbered outline, **1–5 word bold prefix**, colon, then the description. Example: "**Fix once, propagate everywhere:** A bug discovered in one project becomes a guardrail in every future project."
- **Why subbullets.** When stating that we're doing something, add a subbullet with *Why* in italics and the reason. Leanest way to express purpose.

## Claude Interface Preferences

- **Primary interface:** Cowork (desktop app). This is where the vast majority of work happens.
- **Claude Code:** Route to Code for deep codebase work, multi-file refactors, test-driven development, CI/CD integration, or fan-out batch operations. Cowork prepares the handoff with a task brief.
- **Claude Chat:** Route to Chat for design discussions, brainstorming, mobile-accessible work, or quick questions that don't need file access.
- **Interface modules:** See `modules/cowork-module.md`, `modules/claude-code-module.md`, `modules/chat-module.md` for interface-specific mechanics and handoff protocols.

## How I Work

- **Two workflow modes:** Design then Build (Opus — design together, Claude builds autonomously 15+ min) and Pair Build (Sonnet — fast iterations, tight loops).
- **Ask one question at a time.** Don't overwhelm me with three questions in one message.
- **Don't narrate your plan.** Just do it. If I want a plan, I'll ask. Exception: before going autonomous in Design then Build, summarize the build plan so I can course-correct.
- **Terminal commands: one copy-paste block.** Chain with `&&` within a single repo, start with `cd` to the Mac-side path. Never split across multiple code fences. When pushing multiple repos in one block, separate repos with `;` (not `&&`) so one repo being clean doesn't block the other.
- **Git push: always `git push -u origin main`**, never bare `git push`. Always include the copy-paste push block — never just say "ready to push?" without it. Accumulate changes across the session — never multiple pushes per session. When the work is done or there's a natural pause, present the push block proactively — don't ask or wait for a signal.
- **Push block pattern for multi-repo projects.** The block must always `cd` to the **parent folder** first, then use relative paths into each repo. The `cp` + `diff` sync (if needed) goes at the end, after a `cd` back to the parent — because the repo push chain leaves the working directory inside the last repo. Template: `cd <parent> && cd <repo1> && git push ... && cd .. ; cd <repo2> && git push ... && cd .. ; cp <source> <dest> && diff <dest> <source>`.
- **After updating personal CLAUDE.md:** Always verify with `diff ~/.claude/CLAUDE.md ~/Desktop/Claude\ Cowork\ Folders/Archimedes/output/templates/personal-claude-md-template.md` — no output means in sync.
- **Bullet points for lists.** When presenting lists, options, or structured information, use bullet points or numbered lists — easier for the human reader.
- **Overview doc = product view, roadmap = build view.** Both are design surfaces. When I refine language in either, treat edits as potential architecture changes, not cosmetic fixes.
- **Write it down, don't just nod.** When I state a preference, convention, or correction, write it to the appropriate file immediately — not just in conversation. Conversation dies at compaction; files survive.
- **Default to action (NACK, not ACK).** Use NACK-based flow — assume action, only stop on objection. Don't ask "want me to...?" (ACK) — say "I'll make these changes unless you tell me otherwise" (NACK) or just do it. When the conversation's purpose is obvious, skip even the NACK statement and just act.
- **Efficiency = my time, not my money.** When evaluating tools, APIs, or services, optimize for speed and quality of output — not cost. Don't shy away from tools that cost $20/month or $200/month if the ROI is clear. I don't like wasting money, but I'm not afraid to spend it. Never recommend a worse tool because it's cheaper.
- **Direct edit workflow.** When I want to edit a file myself, create a working copy with a `-kurt-edit` suffix. I edit freely, tell you I'm done, you diff against the original, we discuss, you merge into a new version, then delete the `-kurt-edit` file.
