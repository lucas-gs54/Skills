---
name: lgs54-doc-optimizer
description: Optimize documentation texts for clarity, conciseness, and correctness. Fixes spelling/grammar errors, improves readability using current language best practices, removes redundancy, and enhances scannability — without inventing or altering factual content. Use when refining READMEs, guides, API docs, changelogs, contributing guides, or any Markdown documentation that needs quality polish.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: documentation-text-optimization
---

# LGS54 Doc Optimizer

## Purpose

This skill optimizes existing Markdown documentation by applying universal writing principles and language-specific best practices. It improves:

- **Correctness**: spelling, grammar, punctuation, typography
- **Conciseness**: removes filler, redundancy, verbose constructions
- **Readability**: sentence structure, paragraph flow, scannability
- **Consistency**: terminology, formatting, style conventions
- **Accessibility**: plain language, clear headings, inclusive phrasing

The skill never invents facts, changes technical meaning, or alters code examples — it only refines how existing content is expressed.

## Language

Communicate with the user in whatever language they are using. The optimized document must be in the same language as the source. Do not translate unless explicitly requested. Support at minimum: Portuguese (BR/PT), English (US/UK), Spanish (ES). Apply language-specific rules from `references/language-rules.md`.

## Supported Optimization Modes

The skill supports distinct optimization focuses. Determine the mode before executing — if ambiguous, ask the user.

- **full** — Apply all optimizations (correctness + conciseness + readability + consistency)
- **conciseness** — Focus on reducing word count while preserving meaning
- **readability** — Focus on sentence structure, flow, and scannability
- **correctness** — Focus on spelling, grammar, punctuation, typography
- **consistency** — Focus on terminology, formatting, style unification

Multiple modes can be combined (e.g., "conciseness + correctness").

## Mandatory Operating Rule

The skill MUST always follow this workflow:

1. **Identify Optimization Mode** — Determine which mode(s) apply. Ask if ambiguous.
2. **Analyze Source Document** — Read the full document. Detect language, document type, current issues.
3. **Load Relevant References** — Load `references/language-rules.md` for the detected language, plus mode-specific references.
4. **Execute Optimizations** — Apply transformations per mode, preserving all factual content.
5. **Validate Output** — Run Quality Checklist. Ensure no factual changes, no invented content.
6. **Deliver Optimized Document** — Provide the improved `.md` file with a summary of changes.

Do not skip validation.

## Core Principles

### 1. Zero Fact Alteration
Never add, remove, or modify factual claims, technical specifications, code behavior, version numbers, commands, API signatures, or any verifiable information. If uncertain, preserve original and flag with `<!-- TODO: verify -->`.

### 2. Preserve Author Intent
Maintain the document's original purpose, audience, tone, and structural decisions. Optimize expression, not substance.

### 3. Language-Specific Rules Apply
Optimization rules differ by language (e.g., English prefers active voice; Portuguese uses more subordination; Spanish has different punctuation conventions). Always apply `references/language-rules.md`.

### 4. Incremental, Traceable Changes
Each optimization should be independently justifiable. Prefer many small improvements over one large rewrite.

### 5. Scannability First
Documentation is scanned, not read linearly. Optimize for: clear headings, short paragraphs, bullet lists, bold key terms, meaningful link text.

## Workflow Steps

### Step 1: Identify Optimization Mode
Determine target mode(s): `full`, `conciseness`, `readability`, `correctness`, `consistency`.
If user says "melhore esse texto" / "optimize this" → default to `full`.
If user specifies focus → use that mode.

### Step 2: Analyze Source Document
Read the complete document. Identify:
- Language (pt-BR, pt-PT, en-US, en-GB, es-ES, etc.)
- Document type (README, CHANGELOG, guide, API reference, etc.)
- Current issues: verbose passages, grammar errors, inconsistent terms, poor scannability, mixed formatting
- Factual content that must not change (commands, APIs, versions, names, URLs)

### Step 3: Load References
Load `references/language-rules.md` for the detected language.
Load mode-specific references:
- `conciseness` → `references/conciseness-guide.md`
- `readability` → `references/readability-guide.md`
- `correctness` → `references/correctness-guide.md`
- `consistency` → `references/consistency-guide.md`

### Step 4: Execute Optimizations
Apply transformations in this order (earlier steps create cleaner input for later):

1. **Correctness** (if mode includes it): Fix spelling, grammar, punctuation, typography per language rules.
2. **Conciseness** (if mode includes it): Remove filler words, merge redundant sentences, replace verbose phrases with concise equivalents, convert passive to active where natural.
3. **Readability** (if mode includes it): Shorten sentences, break long paragraphs, add/improve headings, convert dense prose to lists, bold key terms for scanning.
4. **Consistency** (if mode includes it): Unify terminology, formatting (code fences, list markers, heading style), date/number formats, cross-reference style.

### Step 5: Validate Output
Run the Quality Checklist. Critical: verify no factual content was altered.

### Step 6: Deliver Result
Provide:
- Optimized `.md` file (written to disk or returned as content)
- **Changes Summary**: bullet list of improvement categories applied with 1–2 examples each

## Quality Checklist

Before delivering, verify:

- [ ] Optimization mode was identified and applied
- [ ] Source language detected; `references/language-rules.md` loaded and followed
- [ ] No factual content changed (commands, APIs, versions, names, URLs, code)
- [ ] No information invented — placeholders used where verification needed
- [ ] Heading hierarchy preserved (no added/removed/skipped levels)
- [ ] Code blocks unchanged except language tag corrections
- [ ] Links preserved; only link text improved for descriptiveness
- [ ] List markers consistent within document
- [ ] Emphasis style consistent (`**bold**`, `*italic*`)
- [ ] File ends with single trailing newline
- [ ] Changes Summary provided with concrete examples

If any critical item fails, revise before delivery.

## Reference Files

- `references/language-rules.md` — Language-specific grammar, spelling, typography, style rules
- `references/conciseness-guide.md` — Patterns for reducing verbosity per language
- `references/readability-guide.md` — Sentence/paragraph structure, scannability techniques
- `references/correctness-guide.md` — Common error patterns, correction rules
- `references/consistency-guide.md` — Terminology unification, formatting standards

## Anti-Patterns

Avoid:
- Rewriting technical explanations into marketing language
- Changing "click here" to descriptive text if the URL is the only context (preserve and flag)
- Removing "obvious" context that may not be obvious to all readers
- Applying English rules to Portuguese/Spanish texts (or vice versa)
- Optimizing generated/reference content (OpenAPI specs, auto-generated docs) — flag instead
- Silent removal of hedging ("may", "might", "could") that conveys legitimate uncertainty