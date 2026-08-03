---
name: lgs54-skill-creator
description: Create, refine, structure, validate, and optimize new skills for the repository adhering to the lgs54 standard. Use when creating new agent skills, updating skill structures, establishing reference files, or standardizing skill architecture. Always specify category, skill scope, and modular architecture.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: skill-creation-and-optimization
---

# LGS54 Skill Creator

## Purpose

This skill is a universal, standardized workflow for creating, refining, structuring, validating, and optimizing skills in the `lgs54` ecosystem.

It ensures every skill created:
1. Follows consistent frontmatter metadata layout.
2. Adheres to clean, modular architecture (placing sub-references in `references/*.md`).
3. Enforces language flexibility (communicating in user's language while providing structured guidelines).
4. Includes explicit step-by-step operating rules and mandatory validation checklists.
5. Integrates seamlessly into the repository's categorised directory layout (`Documentation/`, `Engineering/`, etc.).

## Language

Communicate with the user in whatever language they are using (defaulting to Portuguese if initiated in Portuguese). All instructions in generated skills must be written cleanly in the user's preferred target language or English based on target usage.

## Supported Categories & Directory Structure

Skills must be placed in appropriate top-level category directories within the workspace:

- `Engineering/`: Skills focused on prompt engineering, workflow automation, code architecture, testing, debugging, and context management.
- `Documentation/`: Skills focused on content generation, markdown formatting, technical writing, API specs, and READMEs.
- `[Custom Category]`: A new top-level folder if the scope demands a distinct domain (e.g., `Design/`, `Data/`).

Directory layout for any skill:

```
<Category>/<skill-name>/
├── SKILL.md
└── references/
    ├── <aspect-1>.md
    └── <aspect-2>.md
```

- Skill directory name: MUST match the `name` field in `SKILL.md` (e.g., `lgs54-skill-creator`).
- Core file: `SKILL.md` (contains YAML frontmatter + main operational workflow).
- Modular documentation: `references/` (contains specialized sub-guides, templates, or platform/domain-specific details loaded on-demand).

## Mandatory Operating Rule

The skill MUST always follow the defined workflow:

1. **Understand Purpose & Scope**: Define what problem the new skill solves and when it should trigger.
2. **Determine Category & Folder Name**: Choose appropriate parent category and `lgs54-*` naming scheme.
3. **Define Frontmatter**: Draft standard YAML header (`name`, `description`, `metadata`).
4. **Draft Main Operational Sections**: Write `Purpose`, `Language`, `Supported Types/Aspects`, `Mandatory Operating Rule`, `Core Principles`, and `Execution Workflow`.
5. **Extract References**: Identify modular details or platform/domain specifics and split them into `references/*.md`.
6. **Validate Architecture**: Verify frontmatter validity, directory paths, trigger descriptions, and checklist compliance against [`references/skill-standards.md`](file:///home/lucas/Documentos/Projetos/Skills/Engineering/lgs54-skill-creator/references/skill-standards.md).
7. **Deliver Skill Files**: Present or write the created `SKILL.md` and reference files.

## Core Principles of LGS54 Skills

### 1. Clear Triggers & Frontmatter
The `description` in YAML frontmatter MUST explicitly define **what** the skill does and **when** to use it. This enables agentic models to recognize when to invoke the skill.

### 2. Progressive Disclosure (Modular References)
Main `SKILL.md` files must remain concise, high-level operational workflows. Granular rules, schemas, templates, or platform details MUST be delegated to `references/*.md` files and loaded only when relevant.

### 3. Imperative & Actionable Language
Use direct, deterministic guidelines (e.g., "Determine target platform before generating output", "Do not skip validation step").

### 4. Zero Assumption Policy
Skills must explicitly state inputs, mandate clarification when ambiguous, and never guess user intentions or missing technical schemas.

### 5. Mandatory Quality Validation
Every skill must conclude with an operational checklist ensuring the deliverable meets quality standards before completion.

## Step-by-Step Skill Creation Workflow

### Step 1: Definition & Information Gathering
Collect or clarify:
- **Skill Name**: Must be lowercase kebab-case, prefixed with `lgs54-` (e.g., `lgs54-code-review`).
- **Category**: Identify parent folder (`Engineering`, `Documentation`, etc.).
- **Primary Goal**: What task does this skill accomplish?
- **Triggers**: When should this skill be activated?
- **Modular Sub-domains**: What reference files (`references/*.md`) are needed?

### Step 2: Frontmatter Construction
Use the standard frontmatter structure:

```yaml
---
name: lgs54-<skill-name>
description: <Clear, concise description of what the skill does AND explicit trigger conditions/keywords>.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: <short-kebab-case-purpose>
---
```

### Step 3: Structuring SKILL.md
Draft `SKILL.md` using the canonical section layout:
1. `# Title`
2. `## Purpose`
3. `## Language`
4. `## Supported Sub-types / Modes`
5. `## Mandatory Operating Rule`
6. `## Core Principles`
7. `## Workflow Steps`
8. `## Quality Checklist`

Read [`references/template-skill.md`](file:///home/lucas/Documentos/Projetos/Skills/Engineering/lgs54-skill-creator/references/template-skill.md) for exact section templates.

### Step 4: Structuring References
For each sub-domain, create a dedicated file in `references/`:
- Keep reference files concise, focused, and structured.
- Use headers, bullet points, and code blocks.
- Provide practical examples and edge-case handling.

Read [`references/skill-standards.md`](file:///home/lucas/Documentos/Projetos/Skills/Engineering/lgs54-skill-creator/references/skill-standards.md) for quality criteria.

## Quality Checklist

Before finalizing any created skill, verify:

- [ ] Folder name matches `name` in `SKILL.md` exactly.
- [ ] YAML frontmatter contains `name`, `description`, and `metadata` (`author: lucas-gs54`, `version`, `purpose`).
- [ ] `description` includes clear trigger conditions ("Use when...").
- [ ] Large reference tables/guidelines are split into `references/` directory.
- [ ] Markdown links inside the skill use proper relative file syntax (`references/<file>.md`).
- [ ] Language instruction section is included.
- [ ] Mandatory Operating Rule and Validation steps are explicitly defined.
