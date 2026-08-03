# CONTRIBUTING — Document-Specific Structure

Read this file after Step 1 (Identify Document Type) when the target is a CONTRIBUTING guide, before constructing or optimizing the final document.

## Purpose of a CONTRIBUTING Guide

A CONTRIBUTING guide removes friction for someone who wants to submit a change. Optimize for a first-time contributor who has never touched this codebase: what do they set up, what rules must they follow, and how does a change actually get merged?

## Recommended Section Order

1. **Title** — usually just "Contributing" or "Contributing to <Project>".
2. **Welcome / intent** — one or two sentences: contributions are welcome, and this document explains how.
3. **Code of Conduct** — link to `CODE_OF_CONDUCT.md` if one exists; do not fabricate a code of conduct the project doesn't have.
4. **Ways to Contribute** — not just code: bug reports, docs, translations, triage, design.
5. **Development Setup** — exact steps to get a local environment running (clone, install dependencies, run tests) in fenced code blocks.
6. **Branching / Workflow** — branch naming convention, whether the project uses trunk-based development, GitFlow, forks vs. branches, etc.
7. **Commit Message Convention** — e.g., Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`) if the project uses it; state clearly if it doesn't use any convention.
8. **Coding Style / Standards** — linters, formatters, style guides, and how to run them locally.
9. **Testing Requirements** — how to run tests, minimum coverage expectations if any, how to add new tests.
10. **Pull Request Process** — what a PR needs before it can be reviewed/merged (description, linked issue, passing CI, tests, docs updated).
11. **Issue Reporting** — how to file a good bug report (repro steps, environment, expected vs. actual behavior); link to an issue template if one exists.
12. **Review Process** — who reviews, expected turnaround, how to request re-review.
13. **Recognition** (optional) — how contributors are credited.
14. **Questions / Getting Help** — where to ask (discussions, chat, issue tracker) if something in this guide is unclear.

## Development Setup

- Give exact, copy-pasteable commands, not descriptions ("run the setup script" is worse than the actual command).
- State required tool versions explicitly (language runtime, package manager) rather than assuming the reader has the right one.
- If setup differs by OS, say so explicitly rather than giving one OS-specific set of instructions silently.

## Commit and PR Conventions

- If the project follows Conventional Commits, show the format and 2–3 concrete examples (`feat: add dark mode toggle`, `fix: correct off-by-one in pagination`).
- If the project has no fixed convention, don't invent one — say commit messages should be clear and descriptive, and leave it there.
- Be explicit about what makes a PR mergeable: does it need a linked issue, a changelog entry, passing tests, one approval, two approvals?

## Tone

- Welcoming and clear, not bureaucratic. The goal is to lower the barrier to a first contribution, not to intimidate.
- Be explicit rather than assuming shared context — a first-time contributor doesn't know the maintainers' unwritten norms.

## Common Mistakes to Avoid

- Assuming the reader already knows the project's internal tooling or jargon.
- Missing exact setup commands, forcing readers to reverse-engineer them from a `package.json` or `Makefile`.
- No guidance on commit/PR expectations, causing avoidable back-and-forth in review.
- Inventing a code of conduct, license clause, or governance process the project doesn't actually have.
- Duplicating the entire README instead of linking to it for project overview/installation.

## Freshness

Conventional Commits and common OSS contribution norms are fairly stable community standards but can be revised. If the user needs to confirm a specific current convention (e.g., "is this still the latest Conventional Commits spec") and a search tool is available, verify rather than relying solely on this file.
