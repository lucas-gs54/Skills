# LGS54 Skill Architecture & Quality Standards

## 1. Naming & Directory Conventions

- **Directory Name**: Must use `kebab-case` and be prefixed with `lgs54-` (e.g. `lgs54-sql-optimizer`).
- **Category Folders**: Skills must reside inside a top-level domain folder such as `Engineering/` or `Documentation/`.
- **Primary File**: Must be named `SKILL.md` in root of skill directory.
- **References Directory**: Detailed manuals, schemas, platform guides, or templates must go into `references/*.md`.

## 2. YAML Frontmatter Requirements

Every `SKILL.md` must start with:

```yaml
---
name: lgs54-<skill-name>
description: <Comprehensive description detailing functionality AND trigger conditions>. Use when <trigger context>.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: <domain-purpose-slug>
---
```

### Description Rules:
- State what the skill does in the first sentence.
- Include explicit trigger conditions starting with "Use when...".
- List relevant domain keywords, file types, or user requests.

## 3. SKILL.md Section Standards

1. **Title**: `# LGS54 <Skill Title>`
2. **Purpose**: High-level statement of goals and boundaries.
3. **Language**: Explicit instruction on handling multilingual interactions.
4. **Supported Modes / Sub-types**: List distinct variants or options supported by the skill.
5. **Mandatory Operating Rule**: Sequenced step-by-step pipeline that must be executed.
6. **Core Principles**: Key operational constraints (e.g., clarity, modularity, zero assumption).
7. **Workflow Steps**: Granular step-by-step instructions.
8. **Quality Checklist**: Self-validation list before delivering final artifacts.

## 4. Progressive Disclosure & References Design

- Keep `SKILL.md` focused on high-level workflow and orchestration.
- Place domain-specific details into `references/`.
- Load reference files conditionally when specific modes or platforms are selected.
- Cross-reference using relative links: `references/<filename>.md`.

## 5. Tone & Writing Style

- Imperative, concise, operational instructions.
- Avoid vague advice; use clear rules and conditional checks.
- Maintain formatting consistency with markdown headers, lists, and code blocks.
