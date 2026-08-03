---
name: lgs54-markdown-generation
description: Create, structure, and optimize Markdown (.md) documents following industry best practices — README, CHANGELOG, CONTRIBUTING, technical/API documentation, and general documentation. Use when the user wants to write, generate, restructure, or improve any .md file, or asks for a "README", "changelog", "guia de contribuição", "documentação técnica", or similar Markdown deliverable. Always identify the document type before producing the final file.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: universal-markdown-generation
---

# LGS54 Markdown Generation

## Purpose

This skill is a universal workflow for creating, structuring, and optimizing Markdown (`.md`) documents. It covers:

- README
- CHANGELOG
- CONTRIBUTING
- Technical / API documentation
- General documentation (guides, wikis, tutorials, notes)

The skill distinguishes between:

1. Universal Markdown formatting principles (apply to every document type).
2. Document-type-specific structure and conventions.
3. Project-specific content, which only the user can supply.

Never assume that a structure that works well for one document type is automatically correct for another. A CHANGELOG and a README solve different problems and follow different conventions.

## Language

Communicate with the user in whatever language they are using. Do not default to any single language. The generated Markdown itself should also be written in the user's language, unless the user asks for it in a different language (e.g., an English README for an international open-source project).

## Supported Document Types

The skill supports five document types:

- README
- CHANGELOG
- CONTRIBUTING
- Technical / API documentation
- General documentation (guides, tutorials, wikis)

Identifying the document type is mandatory before producing the final file.

If the user has already specified the type (explicitly, or implicitly via context — e.g., "preciso de um changelog", "documenta essa API"), use that information.

If the type is not specified or ambiguous, ask (in the user's language):

> What kind of document do you need: README, CHANGELOG, CONTRIBUTING, technical/API documentation, or general documentation?

Do not generate the final document until the type is known.

### Document-type reference files

Once the type is known, read the matching reference file before constructing or optimizing the final document:

- README → `references/readme.md`
- CHANGELOG → `references/changelog.md`
- CONTRIBUTING → `references/contributing.md`
- Technical / API documentation → `references/technical-docs.md`
- General documentation → `references/docs.md`

Do not load a reference file for a type that was not selected — this keeps the workflow focused and avoids mixing conventions from unrelated document types.

## Mandatory Operating Rule

The skill MUST always follow the defined workflow.

The standard pipeline is:

1. Identify document type.
2. Understand the project/context.
3. Gather or request necessary content.
4. Analyze existing document, when applicable.
5. Construct or optimize.
6. Apply document-type structure (read the matching `references/*.md` file).
7. Apply universal Markdown formatting (see Core Principles below).
8. Validate.
9. Deliver final document.
10. Explain improvements.

Do not skip validation.

## Core Principles (Universal Markdown Formatting)

These rules apply to every Markdown document, regardless of type.

### Heading Hierarchy

- Exactly one `# H1` per document — the title.
- Never skip levels (`#` → `##` → `###`, not `#` → `###`).
- Headings describe content; avoid vague headings like "More info" or "Stuff".
- Leave a blank line before and after every heading.

### Consistent Emphasis and Lists

- Bold: `**text**`. Italic: `*text*`. Do not mix `_underscore_` and `*asterisk*` styles within the same document.
- Pick one unordered-list marker (`-` is the safe default) and use it consistently; never mix `-`, `*`, and `+`.
- Use ordered lists (`1.`, `2.`, ...) only when sequence or ranking matters.
- Avoid nesting lists more than two levels deep — restructure with headings or a table instead.

### Code

- Always specify the language on fenced code blocks (` ```bash `, ` ```python `, ` ```json `, etc.) for correct syntax highlighting.
- Use inline code (`` `like this` ``) for filenames, commands, flags, variables, and short literals — never plain text or bold for these.
- Show real, runnable examples where possible; avoid pseudo-code when the actual syntax is short.

### Links and Images

- Use descriptive link text — never "click here" or a bare URL as the visible text.
- Always include meaningful alt text on images: `![alt text](path)`.
- In long documents, prefer reference-style links (`[text][ref]` + a definitions block at the bottom) to keep prose readable.
- Verify that internal anchors/links match the actual heading slugs.

### Tables

- Use tables for structured, comparable data (options, parameters, compatibility matrices) — not for layout.
- Keep column headers short and consistent; align columns in the source when it doesn't hurt readability.
- Avoid tables wider than the content actually requires; break very wide tables into multiple smaller ones or a list.

### Structure and Length

- One idea/sentence per line, or clear semantic line breaks — this produces clean diffs in version control.
- Blank line between paragraphs; never rely on a single line break to separate them.
- Add a table of contents for documents long enough to need one (roughly 6+ headings or 300+ lines).
- Prefer shorter documents that link to deeper reference material over a single very long file, when the content naturally splits.

### Metadata and Files

- File naming: `kebab-case.md` (e.g., `getting-started.md`), except conventional fixed names (`README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `LICENSE.md` — uppercase, as expected by GitHub/GitLab/hosting tooling).
- Use YAML front matter only when the target platform consumes it (static site generators, docs frameworks); do not add it to plain repository files unless requested.
- End every file with a single trailing newline.
- Avoid embedded raw HTML unless Markdown cannot express the needed structure (e.g., collapsible `<details>` sections) — it reduces portability across renderers.

### Accessibility and Clarity

- Write for skimmability: short paragraphs, meaningful headings, bullets over dense prose.
- Don't rely on color, emoji, or formatting alone to convey required meaning (e.g., "✅ = done" needs a legend the first time it's used).
- Avoid unnecessary jargon; define acronyms on first use if the audience may not know them.

## Document Architecture

When appropriate, construct documents using this conceptual set of building blocks — not every document needs every block:

```text
Title / Identity
Short Description (what & why)
Status / Badges (if applicable)
Table of Contents (if long)
Body (type-specific structure — see references/*.md)
Examples
Contribution / Support pointers
License / Footer
```

Only include components that serve the specific document. A one-page internal note does not need badges or a table of contents; a public open-source README usually benefits from both.

## Document-Type-Specific Structure

Structure guidance has moved out of this file to keep it focused. After identifying the document type in Step 1, read the corresponding file:

- `references/readme.md`
- `references/changelog.md`
- `references/contributing.md`
- `references/technical-docs.md`
- `references/docs.md`

Each reference file covers that document type's expected sections, ordering, and industry conventions (e.g., Keep a Changelog format, Conventional Commits, Diátaxis-style docs). Apply that guidance during Step 6 of the Mandatory Workflow.

## Gathering Content

Markdown structure alone does not make a good document — accurate content does. Before writing the final document:

Identify, when relevant:

- project/product name and one-line purpose
- audience (end users, contributors, other engineers, internal team)
- existing material the user has already provided (code, package files, prior docs, commit history)
- required sections dictated by the document type (see the matching reference file)
- tone (formal, casual, terse, marketing-oriented)
- target platform (plain GitHub/GitLab render, a static site generator, an internal wiki)

Do not invent factual project details (license type, install commands, API signatures, version numbers) — use placeholders clearly marked as such (e.g., `<!-- TODO: confirm install command -->` or `[SEU_COMANDO_AQUI]`) rather than guessing.

If information is missing and materially affects the result (e.g., no clue what the project does, or what license applies), ask for it. If the missing information is minor (e.g., exact badge style), use a reasonable default and note the assumption.

## Analyzing an Existing Document

If the user provides an existing `.md` file to improve, analyze it before rewriting:

- **Structure**: Does it follow the expected sections for its document type?
- **Formatting**: Does it violate any Core Principles above (heading skips, inconsistent lists, missing code-block languages, broken links)?
- **Completeness**: Are required sections for this document type missing?
- **Redundancy**: Can it become clearer or shorter without losing information?
- **Accuracy risk**: Does anything look outdated or inconsistent with itself (e.g., install instructions that contradict the stated stack)?

Preserve the original content and intent. Improve structure, formatting, and clarity — do not silently remove factual content or change the project's described behavior unless the user asks for that.

## Validation Checklist

Before delivering the final document, validate every applicable category.

### Structure

- [ ] Document type was identified.
- [ ] Required sections for this document type are present (per `references/*.md`).
- [ ] Heading hierarchy is correct (single H1, no skipped levels).
- [ ] Table of contents is present if the document is long enough to need one.

### Formatting

- [ ] Emphasis and list markers are used consistently.
- [ ] Code blocks specify a language.
- [ ] Links use descriptive text; images have alt text.
- [ ] Tables are used only where they aid comparison, not for layout.
- [ ] File ends with a trailing newline.

### Content

- [ ] No invented facts (license, commands, APIs, numbers) — placeholders used where information was missing.
- [ ] Tone matches the intended audience.
- [ ] Original meaning was preserved when optimizing an existing document.

### Quality

- [ ] Redundant or filler content was removed.
- [ ] The document is skimmable (short paragraphs, clear headings).
- [ ] No unnecessary complexity (badges, sections, or nesting) was added without purpose.

If a critical requirement fails validation, revise before delivery.

## Delivering the Document

Markdown documents are standalone deliverables. Always create an actual `.md` file (do not just paste the content inline) and present it to the user, unless the user is asking a narrow, punctual question that doesn't require a full document (e.g., "essa hierarquia de headings tá certa?", "por que meu changelog não segue o padrão?") — in that case, answer directly and conversationally instead.

When delivering a new or optimized document, briefly explain the most relevant improvements or structural choices made — focus on concrete changes (missing sections added, formatting fixed, structure reordered), not a restatement of the whole file.

## Anti-Patterns

Avoid:

- multiple H1 headings in the same document;
- mixing list/emphasis syntax styles within one file;
- code blocks without a specified language;
- "click here" links or bare URLs as link text;
- inventing project facts (license, version, commands) instead of using placeholders;
- copying a README structure onto a CHANGELOG (or vice versa) — each type has its own convention;
- unnecessary badges, emoji, or decoration that doesn't serve the reader;
- excessively long documents that should be split into linked pages;
- embedded HTML when native Markdown would work just as well;
- silently deleting factual content while "improving" formatting.

## Source of Truth

Document-type conventions referenced in this skill (Keep a Changelog, Conventional Commits, Diátaxis, common README structures) are widely adopted community standards, not proprietary rules — they evolve slowly but do evolve. If the user needs current guidance on a specific convention or platform rendering behavior (e.g., "does GitHub still support this badge service", "current Keep a Changelog version") and a search tool is available, verify against current sources rather than relying solely on the reference files.

## Completion Criteria

A task is complete only when the Mandatory Workflow (Steps 1–10) has been followed, the matching `references/*.md` file has been applied, and the Validation Checklist has been completed with no unresolved critical items.
