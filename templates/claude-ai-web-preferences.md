# Claude.ai Web Preferences — Template

**Purpose:** Template for structuring your Claude.ai web preferences. Paste into [Claude.ai Settings](https://claude.ai/settings/general) → Profile → "What would you like Claude to know about you?"

**NOTE:** This is a blank template. Your actual preferences (with personal data) should live in your private builder repo, not in any public-facing file.

---

## Suggested structure:

```
I'm [name]. [Account info if multiple accounts.]

I use Archimedes (Kurt & Claude Best Practices) across all my projects. The Archimedes repo is at [repo URL].

[Family/household context — optional, helps Claude resolve names in conversation.]

[Home/property nicknames — optional, keeps addresses out of always-on context.]

Active projects:
- [Project name] — [one-liner]. [Account]. [Key detail.]
- [repeat for each project]

Cross-project architecture: [describe how projects relate to each other]

Preferences:
- Date format: [your preferred format]
- [technical preferences — API vs browser, secrets handling, etc.]
- [file naming conventions]
- [writing style preferences]

How I work:
- [workflow modes]
- [communication preferences]
- [action preferences]
```

## Tips:

- **Keep it compact.** Web preferences have a character limit. One line per project. No addresses or credentials.
- **Names matter.** Include family members, project names, and key terms so Claude recognizes them in any conversation.
- **Cross-project architecture** in one sentence per project helps Claude route correctly when you mention a project by name.
- **Split question:** If you have multiple Claude accounts, decide whether to use identical preferences or account-specific versions.
