# CHANGELOG — Document-Specific Structure

Read this file after Step 1 (Identify Document Type) when the target is a CHANGELOG, before constructing or optimizing the final document.

## Purpose of a CHANGELOG

A CHANGELOG communicates, to humans (not machines — that's what commit history and diffs are for), what actually changed between releases and why it matters to someone upgrading. Optimize for a reader scanning to answer: "does this release affect me?"

## Recommended Convention: Keep a Changelog

Follow the widely-adopted "Keep a Changelog" convention unless the user specifies another system:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- ...

## [1.2.0] - 2026-08-03

### Added
- ...

### Changed
- ...

### Fixed
- ...

## [1.1.0] - 2026-06-15

### Added
- ...
```

## Section Categories (per version)

Use only the categories that apply to that release — do not include empty categories:

- **Added** — new features.
- **Changed** — changes in existing functionality.
- **Deprecated** — soon-to-be-removed features.
- **Removed** — now-removed features.
- **Fixed** — bug fixes.
- **Security** — vulnerability fixes (call these out clearly; users scan for them).

## Versioning

- Follow Semantic Versioning (`MAJOR.MINOR.PATCH`) unless the project explicitly uses something else (calendar versioning, etc.) — state which scheme is in use at the top of the file if it isn't obvious.
- Each version heading should be a real, releasable version — not a work-in-progress label — except for the reserved `[Unreleased]` section at the top, which accumulates entries until the next release.
- Include the release date in `YYYY-MM-DD` format next to each version.
- Link version headings to the actual diff/comparison (e.g., a Git host's compare URL) when that information is available; do not fabricate a link if the repository URL wasn't provided.

## Writing Entries

- One entry per line, starting with a verb or clear noun phrase: "Add support for X", not "X support was added, and also we fixed some bugs" (multiple changes = multiple bullets).
- Write for the end user/consumer of the change, not for the internal implementation. "Fix crash when uploading files over 2GB" is useful; "Refactor internal buffer handling in `uploader.go`" usually isn't, unless the audience is contributors to the codebase itself.
- Reference issue/PR numbers when available (e.g., `(#123)`), but don't invent numbers that weren't supplied.
- Keep entries terse — one line each. If an entry needs a paragraph of explanation, it likely belongs in the release notes or docs, with just a link from the changelog entry.

## What NOT to Put in a CHANGELOG

- Every single commit (that's what `git log` is for) — only user-relevant, notable changes.
- Internal refactors with no external effect, unless the audience is specifically other contributors to the codebase.
- Marketing language or narrative storytelling — save that for release announcements.
- Duplicated content already in the README or a migration guide — link instead.

## Common Mistakes to Avoid

- Missing or stale `[Unreleased]` section, making it unclear what's coming next.
- Version headings with no date.
- Mixing categories within a single unlabeled list (readers can't tell a fix from a new feature).
- Reverse-chronological order broken (always newest version at the top).
- Copying raw commit messages verbatim instead of rewriting for a human reader.

## Freshness

The Keep a Changelog specification and Semantic Versioning are stable but do get point revisions. If the user needs to confirm the current version of either specification and a search tool is available, verify rather than relying solely on this file.
