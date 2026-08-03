# Technical / API Documentation — Document-Specific Structure

Read this file after Step 1 (Identify Document Type) when the target is technical or API documentation, before constructing or optimizing the final document.

## Purpose of Technical / API Documentation

Technical documentation lets a developer use a system (a library, an API, a CLI, an internal service) correctly without reading the source code. Optimize for precision and completeness over narrative — the reader is usually looking something up, not reading start to finish.

## Recommended Section Order

1. **Title** — the API/module/service name.
2. **Overview** — what this does, in what context it's used, one short paragraph.
3. **Authentication** (if applicable) — how to obtain and use credentials/tokens, with a real example request.
4. **Base URL / Environment info** (for APIs) — production vs. staging/sandbox endpoints.
5. **Quick Start** — the smallest working call/example, end to end.
6. **Reference** — the core of the document. For each endpoint/function/method:
   - Name and one-line purpose.
   - Parameters (name, type, required/optional, default, description) — as a table.
   - Return value / response shape.
   - A realistic request/response example in a fenced code block with the correct language.
   - Error responses/codes specific to this endpoint, if they differ from the general error section.
7. **Error Handling** — general error format, status codes, and how to interpret them.
8. **Rate Limits / Quotas** (if applicable).
9. **Versioning** — how the API/library versions itself, and how breaking changes are communicated.
10. **SDKs / Client Libraries** (if applicable) — links, not full duplication of their own docs.
11. **Changelog pointer** — link to `CHANGELOG.md` rather than duplicating version history here.

## Parameter and Response Tables

Use a table for parameters — this is one of the clearest legitimate uses of Markdown tables:

```markdown
| Name    | Type   | Required | Description                  |
|---------|--------|----------|-------------------------------|
| `id`    | string | Yes      | Unique identifier of the resource. |
| `limit` | number | No       | Max items to return. Default: `20`. |
```

## Code Examples

- Every endpoint/function should have at least one realistic, runnable example — not just a signature.
- Show both the call and its expected output/response where relevant (e.g., a request and its JSON response as two separate fenced blocks).
- Prefer one canonical language for examples unless the audience explicitly needs multiple (e.g., `curl` + one or two SDK languages); don't pad the doc with redundant examples in every possible language unless requested.
- Always specify the fence language (` ```bash `, ` ```json `, ` ```python `, etc.).

## Precision Requirements

- Do not invent parameter names, types, default values, status codes, or endpoint paths. If the user hasn't supplied this information, mark it clearly as a placeholder (e.g., `<!-- TODO: confirm parameter list -->`) rather than guessing plausible-looking values.
- State units explicitly (milliseconds vs. seconds, bytes vs. KB) wherever ambiguity is possible.
- Distinguish clearly between required and optional parameters — never leave this implicit.

## Structuring Large Reference Sections

- When documenting many endpoints/functions, group them logically (by resource, by module) with consistent sub-heading structure per item, so the reader can predict where information lives.
- For very large APIs, consider splitting into multiple linked files (one per resource/module) rather than one massive document, and mention this in `docs.md`-style guidance if a broader documentation set is being built.
- Consider whether this content would be better served by a dedicated format (OpenAPI/Swagger spec, generated API reference) when the API is large or evolves frequently — Markdown by hand is best suited for smaller or more stable interfaces, or as a hand-written companion to generated reference docs.

## Common Mistakes to Avoid

- Descriptions without a runnable example.
- Inconsistent parameter table structure between endpoints, making the doc harder to scan.
- Missing error/status code documentation, leaving integrators to guess from testing.
- Mixing narrative tutorial content into what should be terse reference material (put tutorials in `references/docs.md`-style guides instead, and link to them).
- Hardcoding example credentials/tokens that look real — use obvious placeholders (`YOUR_API_KEY`).

## Freshness

API documentation conventions (OpenAPI versions, common auth schemes) evolve. If the user needs current guidance on a specific standard (e.g., "current OpenAPI spec version") and a search tool is available, verify rather than relying solely on this file.
