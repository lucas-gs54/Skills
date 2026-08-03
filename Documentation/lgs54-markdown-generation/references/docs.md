# General Documentation — Document-Specific Structure

Read this file after Step 1 (Identify Document Type) when the target is general documentation: a guide, tutorial, wiki page, internal how-to, or any `.md` document that isn't a README, CHANGELOG, CONTRIBUTING file, or API reference.

## Purpose of General Documentation

General documentation teaches or explains, rather than just listing facts to look up. Optimize for the reader's actual goal in opening the page: are they trying to *learn* something, *accomplish* a specific task, *look up* a fact, or *understand* a concept? Each of those wants a different shape of document.

## The Four Documentation Modes (Diátaxis)

Before writing, identify which mode this document is (a single document can mostly stay in one mode; mixing modes within one page is a common source of confusion):

- **Tutorial** — learning-oriented. Takes a beginner through a concrete series of steps to a working result. Assumes no prior knowledge of the specific task. Success = the reader did the thing and it worked.
- **How-to guide** — task-oriented. Assumes some familiarity; answers "how do I accomplish X" for someone who already knows the basics. Terser than a tutorial, focused on the goal.
- **Reference** — information-oriented. Describes the system accurately and completely (this overlaps with `references/technical-docs.md` for APIs, but also applies to config options, CLI flags, etc.). Not narrative; structured for lookup.
- **Explanation** — understanding-oriented. Discusses why something works the way it does, design decisions, background, alternatives considered. No steps to follow.

Ask (or infer from context) which mode fits before structuring the document, if it materially changes the shape of what's needed.

## Recommended Structure by Mode

**Tutorial:**
1. Title + one-line statement of what the reader will build/achieve.
2. Prerequisites (tools, accounts, prior knowledge assumed).
3. Numbered steps, each with the exact command/action and the expected result, so the reader can confirm they're on track.
4. A working end state — what success looks like.
5. Next steps / where to go from here.

**How-to guide:**
1. Title framed as the goal ("How to configure X for Y").
2. One-line context (when you'd need this).
3. Steps — can be terser than a tutorial; assumes familiarity.
4. Variations/edge cases, if relevant, as a short subsection.

**Reference:**
1. Title.
2. Structured, scannable content — tables for options/flags/settings, consistent sub-heading pattern per item.
3. No narrative framing; a reader should be able to jump to any subsection independently.

**Explanation:**
1. Title framed as the question or topic ("Why we use X instead of Y").
2. Context/background.
3. The reasoning or trade-offs, in prose.
4. Related decisions or links to relevant reference/tutorial material.

## Cross-Linking

- General documentation rarely stands alone — link to related tutorials, how-tos, reference pages, and explanations rather than duplicating their content.
- If this document is part of a larger docs set, note where it fits (e.g., a short "See also" section) so readers can navigate outward.

## Common Mistakes to Avoid

- A "tutorial" that's actually reference material with numbered headings — the reader can't tell if they're supposed to follow along or look something up.
- Assuming too much prior knowledge in a tutorial meant for beginners.
- Explanation content buried inside a how-to guide, making the steps harder to follow.
- No stated prerequisites, so the reader discovers a missing dependency mid-tutorial.
- Long, single-purpose pages that should be split into a tutorial + reference + explanation instead of one document trying to do all three.

## Freshness

The Diátaxis framework and general docs-as-code conventions are stable but do evolve. If the user needs current guidance on a specific documentation framework or tool (e.g., a specific static site generator's current conventions) and a search tool is available, verify rather than relying solely on this file.
