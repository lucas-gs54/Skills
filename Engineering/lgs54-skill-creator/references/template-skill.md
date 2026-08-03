# Canonical SKILL.md Template

Use the template below when generating a new skill file:

```markdown
---
name: lgs54-<skill-name>
description: <Description of skill capabilities>. Use when <trigger conditions and keywords>.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: <purpose-slug>
---

# LGS54 <Skill Title>

## Purpose

<High-level summary of skill scope, targets, and objectives.>

## Language

Communicate with the user in whatever language they are using. Do not default to any single language. Translate example instructions into the user's target language.

## Supported Platforms / Modes

The skill supports the following modes:

- <Mode 1>
- <Mode 2>

Determine the mode before producing final deliverables. Ask if ambiguous.

## Mandatory Operating Rule

The skill MUST always follow the defined workflow:

1. Identify target mode / scope.
2. Gather requirements.
3. Load corresponding reference files (`references/*.md`).
4. Execute core generation or transformation logic.
5. Perform quality validation.
6. Deliver result with clear explanation of improvements.

## Core Principles

### 1. Clarity & Precision
<Principle description>

### 2. Modular Design
<Principle description>

## Execution Workflow

### Step 1: <Step Title>
<Instructions>

### Step 2: <Step Title>
<Instructions>

## Quality Checklist

- [ ] Frontmatter complete with metadata.
- [ ] Operational workflow clearly sequenced.
- [ ] References split into modular markdown files.
- [ ] No assumptions made without explicit user context.
```
