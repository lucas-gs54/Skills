---
name: lgs54-project-modularization
description: "Organize and restructure disorganized codebases into clean, modular, well-structured projects following best practices. Surveys the current structure, designs a modular target architecture, and implements the reorganization safely, preserving all behavior. Use when a project is messy, scattered, lacks clear module boundaries, has inconsistent naming or layout, or needs reorganization, modularization, or refactoring into a maintainable structure. Complements lgs54-deep-code-analysis."
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: project-modularization
---

# LGS54 Project Modularization

## Purpose

This skill restructures disorganized codebases into clean, modular projects that follow best practices for the target language and framework. It complements `lgs54-deep-code-analysis` (which diagnoses problems) by acting on them: establishing clear module boundaries, single responsibilities, consistent naming, and minimal dependencies — always preserving existing behavior.

The skill applies to any language and adapts its rules to language/framework-specific conventions.

## Language

Communicate with the user in whatever language they are using. Plans, reports, and annotations follow the user's language. Technical terms remain in English.

## Supported Modes

Determine the mode before executing — if ambiguous, ask the user.

- **plan** — Survey the project and deliver a restructuring plan only. No files are changed.
- **implement** — Execute a previously approved restructuring plan.
- **full** — Survey → deliver plan → await explicit approval → implement.

## Mandatory Operating Rule

The skill MUST always follow this workflow:

1. **Identify Mode & Scope** — Determine mode, language, entry points, critical modules.
2. **Survey the Project** — Index current structure and organization problems.
3. **Design Target Structure** — Define modular architecture per best practices.
4. **Build Restructuring Plan** — Concrete moves, splits, renames, rewiring, migration order.
5. **Await Approval** — In `plan` mode, stop here. In `full` mode, require explicit approval before changing anything.
6. **Implement Safely** — Execute small, verifiable steps with a tracked before/after inventory.
7. **Verify & Report** — Confirm behavior preservation and deliver the report.

Do not skip steps. Never change behavior or contracts without approval.

## Core Principles

### 1. Behavior Is Inviolable
Runtime behavior, business logic, and external contracts never change without explicit user approval. Refactoring is structural, not semantic.

### 2. No File Left Behind
Every moved, renamed, merged, or deleted file is tracked in a before/after inventory. Nothing is lost silently.

### 3. Small Steps Over Big Bangs
Prefer minimal, small, verifiable steps. Large rewrites are an anti-pattern unless explicitly requested.

### 4. Best Practices, Not Templates
Apply the target language/framework's own conventions. Generic advice is a fallback, never the default.

### 5. Context Is King
A module is fine in isolation; its boundaries and dependencies define whether the structure is good. Judge organization by coupling, cohesion, and naming, not by aesthetics.

### 6. Zero Assumption Policy
If scope, target structure, or approval is ambiguous, ask. Never guess.

## Workflow Steps

### Step 1: Identify Mode & Scope
- Determine mode (`plan`, `implement`, `full`).
- Identify language(s), framework, entry points, and critical modules.
- Record the current working state (branch/commit if using git).

### Step 2: Survey the Project
Index the current structure and list organization problems:
- Scattered code and unclear module boundaries.
- Mixed responsibilities and god modules.
- Inconsistent naming and layout.
- High coupling and tangled dependencies.

### Step 3: Design Target Structure
Design the modular target:
- One responsibility per module and a clean public interface.
- Clear boundaries and minimal dependencies.
- Layout consistent with language/framework conventions.

Apply the rules in `references/modularization-rules.md`.

### Step 4: Build Restructuring Plan
Produce the concrete plan:
- New file/folder layout.
- Module splits and merges with rationale per decision.
- Dependency rewiring.
- Safe migration order.

Use `references/migration-workflow.md` for plan structure and inventory format.

### Step 5: Await Approval
- `plan` mode: deliver the plan; stop.
- `implement` mode: confirm the plan exists and was approved; otherwise stop.
- `full` mode: present the plan and require explicit approval before proceeding.

### Step 6: Implement Safely
- Execute the plan in small, verifiable steps.
- Update the before/after inventory after each step.
- Keep the project buildable/testable after each step when feasible.

### Step 7: Verify & Report
- Confirm behavior preservation (build, tests, spot checks).
- Deliver the report per `references/report-template.md`.

## Quality Checklist

- [ ] Mode identified and scope recorded.
- [ ] Survey complete: organization problems listed.
- [ ] Target structure follows `references/modularization-rules.md`.
- [ ] Plan contains layout, splits, rewiring, and migration order.
- [ ] Approval obtained before any file change (unless `implement` mode with existing approval).
- [ ] Every change tracked in the before/after inventory.
- [ ] Behavior preserved: build/tests pass where available.
- [ ] Report delivered per `references/report-template.md`.

## Reference Files

- `references/modularization-rules.md` — Organizational rules: structure, naming, responsibilities, dependencies, per-language conventions.
- `references/migration-workflow.md` — Safe migration: inventory format, step sequencing, verification.
- `references/report-template.md` — Delivery output format.

## Anti-Patterns

Avoid:
- Rewriting code instead of reorganizing it.
- Changing behavior or contracts without approval.
- Moving files without updating the inventory.
- Big-bang rewrites when small steps work.
- Enforcing generic structure over language-specific conventions.
- Merging modules with different responsibilities to reduce file count.
- Delivering a plan with no rationale per decision.
