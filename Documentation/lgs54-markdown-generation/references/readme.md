# README — Document-Specific Structure

Read this file after Step 1 (Identify Document Type) when the target is a README, before constructing or optimizing the final document.

## Purpose of a README

A README answers, in order of urgency: **What is this? Why should I care? How do I use it?** It is usually the first (and sometimes only) thing a visitor reads. Optimize for someone deciding, in under a minute, whether to keep reading.

## Recommended Section Order

Not every project needs every section. Use judgment based on audience (internal tool vs. public open-source library vs. personal project).

1. **Title** — project name as the single H1.
2. **One-line description / tagline** — what it does, immediately under the title.
3. **Badges** (optional) — build status, version, license, coverage. Only include badges that are actually meaningful and kept up to date; a wall of stale badges hurts more than it helps.
4. **Table of Contents** — for READMEs with 6+ sections or significant length.
5. **Demo / Screenshot / GIF** (optional but valuable for UI-facing projects) — show, don't just tell.
6. **Features** — short bullet list of what makes the project useful, not an exhaustive changelog.
7. **Installation** — exact commands, in a fenced code block with the correct language (`bash`, `sh`, etc.). State prerequisites (runtime versions, OS constraints) explicitly.
8. **Usage / Quick Start** — the smallest possible working example. This is the section most readers actually need.
9. **Configuration** (if applicable) — environment variables, config files, flags — usually as a table.
10. **API / Detailed Documentation** (if applicable) — for larger projects, link out to `references/technical-docs.md`-style content rather than inlining everything.
11. **Roadmap** (optional) — near-term plans, useful for setting expectations.
12. **Contributing** — short pointer + link to `CONTRIBUTING.md` rather than duplicating its content.
13. **Tests** (if applicable) — how to run the test suite.
14. **License** — name and link to `LICENSE`/`LICENSE.md`.
15. **Credits / Acknowledgments** (optional).
16. **Contact / Support** (optional) — issue tracker link, discussion forum, maintainer contact.

## Writing the Description

- One sentence, active voice, no jargon the target audience wouldn't already know.
- Lead with the *problem solved* or *value delivered*, not implementation details.
- Avoid empty superlatives ("the best", "blazing fast", "revolutionary") unless backed by something concrete (a benchmark, a comparison).

## Installation and Usage Sections

- Use real, copy-pasteable commands — never pseudo-commands like `install-this-package`.
- Show the minimum viable example first; save advanced options for later sections or linked docs.
- If there are multiple install methods (npm, pip, Docker, from source), give each its own clearly labeled code block rather than one ambiguous block.
- Never invent version numbers, package names, or commands you don't actually have — use a placeholder and flag it if the user hasn't supplied this information.

## Badges

- Common categories: build/CI status, package version, license, code coverage, download count.
- Keep badge count low (3–6 is typical); more becomes visual noise.
- Badges should link somewhere useful (the actual CI run, the package registry page), not be purely decorative.

## Table of Contents

- Generate it from the actual headings used — links must match the real heading slugs.
- Keep it to top-level and, at most, one level of nested sections; don't enumerate every subsection.

## Common Mistakes to Avoid

- Burying the "how do I use this" information below a long history/philosophy section.
- A features list that's really just a restated table of contents.
- Installation instructions that assume undocumented prerequisites.
- Copying another project's README structure verbatim without adapting it to this project's actual needs.
- Badges that no longer reflect reality (broken build badge left green from months ago).
- No license section, leaving usage rights ambiguous.

## Freshness

Badge services, common CI providers, and package registry conventions change over time. If the user needs current guidance on a specific badge/service or current community README conventions and a search tool is available, verify rather than relying solely on this file.
