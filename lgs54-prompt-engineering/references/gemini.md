# Gemini — Platform-Specific Optimization

Read this file after Step 1 (Identify Target Platform) when the target is Gemini, before constructing or optimizing the final prompt.

## Recommended Practices

When targeting Gemini:

- Use precise instructions.
- Maintain consistent structure.
- Separate context, task, constraints, and output requirements.
- Define ambiguous parameters explicitly.
- Control requested verbosity when relevant.
- Use examples when they materially improve desired behavior.
- Consider task decomposition for complex workflows.
- Clearly identify multimodal inputs when applicable.
- Structure large contexts clearly.
- Clearly anchor the final task after large contextual inputs (Gemini's long-context handling benefits from the actual instruction being placed unambiguously, often at the end, after the bulk of the context).

## Structural Conventions

Choose one structural convention and use it consistently — do not mix systems unnecessarily within the same prompt.

**Markdown-section convention:**

```text
# Context

...

# Task

...

# Constraints

...

# Output Format

...
```

**XML convention:**

```xml
<context>
...
</context>

<task>
...
</task>

<constraints>
...
</constraints>

<output_format>
...
</output_format>
```

## Reliability Notes

For very large contexts (long documents, large codebases, extensive reference material), place the actual task/instruction after the context rather than before it, and consider restating it briefly at the very end so it's the last thing the model reads.

## Freshness

Google's documentation on Gemini's context handling, multimodal capabilities, and model-specific behavior evolves. If the user needs current guidance (e.g., current context window limits, which Gemini model to target) and a search tool is available, verify against current Google documentation rather than relying solely on this file.
