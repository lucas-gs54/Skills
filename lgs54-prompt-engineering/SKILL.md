---
name: lgs54-prompt-engineering
description: Create, analyze, optimize, validate, and adapt prompts for ChatGPT, Claude, and Gemini. Use when the user wants to create a prompt from scratch, improve an existing prompt, diagnose prompt failures, or adapt a prompt between supported platforms. Always identify the target platform before producing the final prompt.
metadata:
  author: lucas-gs54
  version: "2.0.0"
  purpose: universal-prompt-engineering
---

# LGS54 Prompt Engineering

## Purpose

This skill is a universal prompt-engineering workflow for creating, analyzing, optimizing, validating, and adapting prompts for:

- ChatGPT
- Claude
- Gemini

The skill itself must remain platform-agnostic and portable across compatible Agent Skills environments.

The prompts it produces are platform-specific by default. Every final prompt MUST be optimized according to the target platform selected by the user.

The skill must distinguish between:

1. Universal prompt-engineering principles.
2. Platform-specific prompting practices.
3. Model/version-specific behavior.

Never assume that a technique that works well on one platform is automatically optimal on another.

## Language

Communicate with the user in whatever language they are using. Do not default to any single language. All example questions in this skill are written in English as neutral placeholders — translate their intent into the user's language rather than reusing the fixed wording.

## Supported Platforms

The skill supports exactly three target platforms:

- ChatGPT
- Claude
- Gemini

The target platform is mandatory.

Before creating or optimizing a prompt, determine which platform will execute it.

If the user has already specified the platform, use that information.

If the platform is not specified, ask (in the user's language):

> Which LLM will run this prompt: ChatGPT, Claude, or Gemini?

Do not generate the final prompt until the target platform is known.

### Platform identification

Map model names to their platform when unambiguous:

- GPT models → ChatGPT
- Claude Opus, Sonnet, Haiku → Claude
- Gemini models → Gemini

If the model cannot be confidently mapped to one of the supported platforms, ask for clarification.

### Platform reference files

Once the target platform is known, read the matching reference file before constructing or optimizing the final prompt:

- ChatGPT → `references/chatgpt.md`
- Claude → `references/claude.md`
- Gemini → `references/gemini.md`

Do not load a reference file for a platform that was not selected — this keeps the workflow focused and avoids mixing platform-specific conventions.

## Mandatory Operating Rule

The skill MUST always follow the defined workflow.

The standard pipeline is:

1. Identify target platform.
2. Understand objective.
3. Classify task.
4. Analyze existing prompt, when applicable.
5. Construct or optimize.
6. Apply platform adaptation (read the matching `references/*.md` file).
7. Validate.
8. Deliver final prompt.
9. Explain improvements.

Do not skip validation.

## Core Principles

### Clarity

Instructions must be clear, explicit, and operational.

Avoid vague instructions such as:

- "Do your best."
- "Be smart about it."
- "Answer perfectly."
- "Think like an expert."
- "Give an amazing response."

Replace subjective instructions with observable requirements.

### Specificity

When relevant, define:

- objective
- context
- task
- inputs
- constraints
- expected behavior
- output format
- audience
- tone
- quality criteria
- edge cases
- examples

Do not introduce unnecessary requirements simply to make the prompt appear sophisticated.

### Consistency

All instructions must be internally compatible.

Detect:

- contradictory instructions
- conflicting output requirements
- ambiguous priorities
- redundant requirements
- impossible constraints
- undefined variables
- inconsistent terminology
- incompatible examples

When conflicts exist, resolve them while preserving the user's intended priority.

Never silently choose a materially different objective.

### Context Separation

Clearly distinguish between:

- instructions
- context
- reference material
- user input
- examples
- expected output

Use structural delimiters when they improve clarity.

Do not allow contextual content to accidentally become an instruction.

### Output Control

When the expected output has a specific structure, define it explicitly.

Possible formats include:

- Markdown
- JSON
- XML
- table
- numbered list
- code
- fixed sections
- schema
- structured report
- concise paragraph

When structured output is required, define required fields, formatting, ordering, constraints, and expected data types when relevant.

Do not specify a rigid format when the task does not benefit from one.

### Examples

Use examples when they improve instruction following.

Examples should:

- represent the desired behavior;
- be consistent with the instructions;
- demonstrate important edge cases when relevant;
- avoid introducing accidental requirements.

Do not add examples merely to make a prompt longer.

### Minimal Sufficient Prompt

The goal is not to produce the longest possible prompt.

Optimize for:

> maximum clarity, reliability, and controllability with minimum unnecessary complexity.

Remove:

- redundant instructions
- decorative language
- unnecessary roleplay
- repeated constraints
- generic motivational statements
- irrelevant requirements
- instructions that do not affect desired behavior

A longer prompt is not automatically a better prompt.

### Platform Adaptation

Platform-specific techniques must only be introduced when they provide a meaningful advantage.

Do not artificially insert platform-specific syntax into every prompt.

The resulting prompt should remain maintainable and understandable while taking advantage of relevant platform capabilities.

## Mandatory Workflow

### Step 1 — Identify Target Platform

Determine whether the target is:

- ChatGPT
- Claude
- Gemini

If unknown, ask the user.

This step is mandatory.

### Step 2 — Understand the Objective

Determine exactly what the user wants the target LLM to accomplish.

Identify, when relevant:

- desired outcome
- task
- audience
- context
- inputs
- expected output
- constraints
- important requirements
- success criteria

Do not invent requirements that materially alter the user's objective.

If information is missing and materially affects the result, ask for it.

If the missing information is minor, use a reasonable assumption or explicit placeholder.

### Step 3 — Classify the Task

Determine the prompt's task category.

Possible categories include:

- writing
- coding
- analysis
- research
- transformation
- extraction
- classification
- summarization
- reasoning
- planning
- automation
- agentic workflow
- tool use
- multimodal task
- structured output
- conversational assistant
- system instruction
- developer instruction
- other

Multiple categories may apply.

Task classification determines which optimization techniques are appropriate.

### Step 4 — Analyze the Existing Prompt

If the user provides an existing prompt, analyze it before rewriting.

This analysis uses the same categories as the Validation Checklist (see below): objective, context, instructions, constraints, inputs, output, examples, and platform fit. Additionally look for:

- **Ambiguity**: Could reasonable interpretations produce materially different results?
- **Conflicts**: Are there contradictory requirements or priorities?
- **Redundancy**: Can the prompt become shorter without losing behavior?

## Prompt Architecture

When appropriate, construct prompts using this conceptual architecture:

```text
Role / Identity
Objective
Context
Inputs
Task
Rules / Process
Constraints
Examples
Output Requirements
Quality Criteria
```

Do NOT automatically include every component.

Only use components that improve the specific task.

The architecture is a tool, not a mandatory template.

## Platform-Specific Optimization

Platform-specific guidance has moved out of this file to keep it focused. After identifying the target platform in Step 1, read the corresponding file:

- `references/chatgpt.md`
- `references/claude.md`
- `references/gemini.md`

Each reference file covers that platform's recommended structure, instruction hierarchy considerations, and relevant syntax (e.g., XML delimiters for Claude, `#`-section structure for Gemini). Apply that guidance during Step 6 of the Mandatory Workflow.

## Optimization Rules

When optimizing an existing prompt, preserve the original intent.

Never silently change:

- desired outcome
- audience
- scope
- business rules
- technical requirements
- constraints
- tone
- expected output

unless the user explicitly requests those changes.

Optimization should generally follow this order:

1. Remove ambiguity.
2. Detect contradictions.
3. Resolve contradictions.
4. Clarify the objective.
5. Add necessary context.
6. Define inputs.
7. Make instructions actionable.
8. Define constraints.
9. Define output requirements.
10. Add examples when useful.
11. Apply platform-specific improvements (see `references/*.md`).
12. Remove unnecessary complexity.
13. Validate the final prompt.

## Iterative Optimization

Prompt engineering must be treated as an iterative process when evidence of failure is available.

If the user provides:

- a failed model response;
- an inconsistent response;
- an undesirable response;
- multiple outputs with inconsistent quality;
- a prompt that only works intermittently;

use that evidence as diagnostic input.

Determine:

1. What the prompt intended.
2. What the model actually produced.
3. Which prompt characteristic allowed the failure.
4. What modification can reduce that failure.
5. Whether the modification introduces another problem.

Prefer the smallest effective change.

Do not blindly increase prompt length.

## Prompt Testing

When the user provides actual model outputs, compare:

```text
Expected behavior
        vs.
Observed behavior
```

Identify the mismatch.

Determine whether the problem originates from:

- ambiguity
- missing context
- weak constraints
- poor output specification
- contradictory requirements
- insufficient examples
- excessive instructions
- platform mismatch
- model-specific behavior
- missing input data

If multiple test cases are available, look for recurring failure patterns rather than optimizing for a single response.

Do not claim that a prompt has been empirically tested unless actual testing data was provided or an available execution environment was used.

## Validation Checklist

Before delivering the final prompt, validate every applicable category. This checklist also defines the categories used in Step 4 (Analyze the Existing Prompt).

### Objective

- [ ] Objective is explicit.
- [ ] Expected outcome is unambiguous.
- [ ] Original user intent is preserved.

### Context

- [ ] Necessary context is present.
- [ ] Context is distinguishable from instructions.
- [ ] No critical information was invented.

### Instructions

- [ ] Instructions are actionable.
- [ ] Instructions are sufficiently specific.
- [ ] Material ambiguity has been removed.
- [ ] Instructions do not contradict each other.

### Constraints

- [ ] Important constraints are explicit.
- [ ] Constraints are internally consistent.
- [ ] Constraints are achievable.
- [ ] No unnecessary constraints were introduced.

### Inputs

- [ ] Required inputs are identified.
- [ ] Placeholders are clear when necessary.
- [ ] Input data is distinguishable from instructions.

### Output

- [ ] Desired output is clearly defined.
- [ ] Output format is explicit when relevant.
- [ ] Required fields or sections are defined.
- [ ] Output constraints are consistent with the task.

### Examples

- [ ] Examples are included when useful.
- [ ] Examples demonstrate desired behavior.
- [ ] Examples do not contradict instructions.

### Platform

- [ ] Target platform is known.
- [ ] Platform-specific techniques (from `references/*.md`) are appropriate.
- [ ] Platform-specific techniques have a reason for being present.
- [ ] The prompt remains maintainable.

### Quality

- [ ] Redundancy was minimized.
- [ ] Unnecessary verbosity was removed.
- [ ] Prompt is operational rather than merely descriptive.
- [ ] Final prompt is internally consistent.
- [ ] No unsupported guarantees about model behavior were introduced.

If a critical requirement fails validation, revise the prompt before delivery.

## Final Response

The default final response applies when a complete final prompt is being delivered (new prompt creation, optimization, or cross-platform adaptation). In that case, the response MUST contain exactly two primary sections:

### Final Prompt

Provide the complete final prompt in a code block.

The prompt itself must not contain explanations about the optimization process.

### Improvements Made

Briefly explain the most relevant improvements.

Focus on concrete modifications such as:

- objective clarification
- context separation
- improved task definition
- stronger constraints
- improved output specification
- ambiguity reduction
- contradiction resolution
- platform-specific adaptation
- removal of unnecessary instructions
- addition or removal of examples
- structural improvements

Do not expose private chain-of-thought or hidden reasoning.

Explain resulting changes and their purpose, not private reasoning traces.

**Exception**: if the user asks a punctual diagnostic question that does not require producing a full final prompt (e.g., "does this prompt have any contradictions?", "why did this prompt fail?"), answer directly and conversationally instead of forcing the two-section format. The two-section format exists to make deliverables easy to extract, not to be used for every reply.

## Creating vs. Optimizing

Both scenarios follow the same Mandatory Workflow above (Steps 1–9). Two things differ by scenario:

- **Creating from scratch** (no existing prompt): Step 4 (Analyze the Existing Prompt) is skipped, since there is nothing to analyze. Go straight from Step 3 (Classify the Task) to Step 5 (Construct).
- **Optimizing an existing prompt**: Step 4 applies in full, and the original objective, audience, scope, and constraints must be preserved unless the user explicitly asks to change them (see Optimization Rules above). Do not rewrite purely for stylistic reasons if the original prompt is already effective — only change what actually improves reliability or clarity.

## Cross-Platform Adaptation

When converting a prompt between supported platforms:

1. Identify the source platform when relevant.
2. Identify the destination platform.
3. Preserve original intent.
4. Preserve requirements and constraints.
5. Identify source-platform-specific techniques.
6. Determine whether those techniques apply to the destination platform (compare `references/*.md` for source and destination).
7. Replace or adapt platform-specific constructs when necessary.
8. Optimize for the destination platform.
9. Validate the result.
10. Explain platform-specific changes.

Do not merely replace the platform name.

## Missing Information

Ask for additional information only when it can materially affect the final prompt.

Prefer concrete questions, asked in the user's language. For example (shown here in English as placeholders):

Good:

> What result do you expect the model to produce?

Good:

> What kind of input will the model receive?

Good:

> What format does the final response need to follow?

Avoid vague questions such as:

> Can you give more details?

When missing information is minor, use:

- a reasonable assumption; or
- an explicit placeholder.

Do not unnecessarily interrupt the workflow.

## Anti-Patterns

Avoid:

- unnecessarily long prompts;
- generic "expert" personas without a functional purpose;
- excessive roleplay;
- redundant instructions;
- contradictory constraints;
- vague quality requirements;
- unsupported claims about model behavior;
- platform-specific syntax without purpose;
- excessive XML or Markdown nesting;
- examples that contradict desired behavior;
- requests for private chain-of-thought;
- arbitrary temperature/top-p recommendations without knowing the execution environment;
- assuming all three platforms behave identically;
- silently changing the user's objective;
- optimizing only for a single observed output;
- adding complexity without measurable or practical benefit.

## Platform Independence

The skill itself is universal.

Its workflow, principles, and terminology must remain usable regardless of the host LLM executing the skill.

Only the generated prompt is specialized according to the selected target platform.

Conceptually: universal workflow → target platform identified (ChatGPT / Claude / Gemini) → matching `references/*.md` applied → Validation Checklist → Final Prompt + Improvements.

## Source of Truth

Platform-specific prompting guidance is maintained separately from this universal workflow, in `references/*.md`.

When current documentation is available (e.g., via a web search tool), prioritize official documentation from:

- OpenAI, for ChatGPT
- Anthropic, for Claude
- Google, for Gemini

If a search or documentation-retrieval tool is available and the user is asking about current platform-specific behavior, use it before applying the guidance in `references/*.md` verbatim — treat the reference files as a solid default, not as a substitute for current documentation when freshness matters.

Platform-specific recommendations must be treated as evolving guidance, not permanent laws. Model behavior and official recommendations can change over time.

The skill must distinguish between:

### Universal principles

Concepts that generally apply across LLMs. Defined in this file.

### Platform-specific practices

Techniques recommended or useful for ChatGPT, Claude, or Gemini. Defined in `references/*.md`.

### Model/version-specific behavior

Behavior associated with a specific model or version. Treat with extra caution — verify with current documentation or the user's own testing evidence before relying on it.

Never generalize model-specific behavior into a universal rule without evidence.

## Completion Criteria

A task is complete only when the Mandatory Workflow (Steps 1–9) has been followed and the Validation Checklist has been completed with no unresolved critical items. The skill MUST NOT skip validation.
