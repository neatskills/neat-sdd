# Change Notes Skill

Generate curated, audience-appropriate change notes from git commit history. Categorizes commits using conventional commit prefixes or AI, summarizes in the right tone for your audience, and outputs markdown with terminal-ready text.

## What It Does

Reads git commits and diffs for a selected range, filters bots and reverts, categorizes and summarizes changes, and produces change notes tailored to the target audience.

**Not for:** GitHub/GitLab releases, full product history, PDF generation (use `neat-util-pdf`).

## Skills

| Skill           | What It Does                                                                       |
|-----------------|------------------------------------------------------------------------------------|
| `neat-sdd-changes`  | Conversational wizard — gather commits, categorize, summarize, output change notes |

## Prerequisites

- Git repository

## When to Use

- Summarize what changed over a commit range
- Sprint review — summarize recent work for stakeholders
- Customer-facing changelog for a new version
- Internal team update on what shipped
- Generate release notes between tags

## Workflow

1. **Setup** — Verify git, determine product name, list available tags, ask for audience and commit range
2. **Gather & Analyze** — Fetch commits, filter bots and reverts, optionally read diffs (≤50 commits), categorize using conventional commits or AI
3. **Output** — Present draft, iterate on feedback, save to specs directory, print to terminal, register in knowledge base if specs.md exists

## Audiences

- **Developers** — Technical details, breaking changes, migration steps
- **Stakeholders** — Business impact, feature value
- **End users** — Plain language, user-facing changes only

## Commit Ranges

- **Tag to tag** (requires ≥2 tags) — e.g., `v1.0.0..v2.0.0`
- **Since last tag** (requires ≥1 tag) — auto-detected
- **Date range** (always available) — e.g., last 30 days
- **Custom range** (always available) — any git ref range

## Output

Saves to `docs/specs/<product>/changes/change-notes-<range>.md` in the current working directory. Also prints to terminal for copy-paste.

Output format:

- Breaking Changes (if present)
- Features
- Bug Fixes
- Other
- Includes short commit hash per entry
- Empty sections omitted
