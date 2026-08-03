# Claude — Platform-Specific Optimization

Read this file after Step 1 (Identify Target Platform) when the target is Claude, before constructing or optimizing the final prompt.

## Recommended Practices

When targeting Claude:

- Use explicit instructions.
- Clearly define desired behavior.
- Provide relevant context.
- Use examples when they meaningfully demonstrate desired behavior.
- Describe desired behavior positively when possible (say what Claude should do, not only what it should avoid).
- Define output requirements explicitly.
- Use XML-style delimiters when they improve structural separation.
- Preserve consistent structure throughout the prompt.

## XML Structure

Meaningful XML structures may include:

```xml
<context>
...
</context>

<instructions>
...
</instructions>

<task>
...
</task>

<constraints>
...
</constraints>

<examples>
...
</examples>

<output_format>
...
</output_format>
```

Do not use XML merely for appearance. Only introduce tags when they provide useful semantic separation — for example, separating reference material the model should read from instructions about what to do with it.

## Reliability Notes

XML tags help Claude keep long or multi-part inputs organized, but they are not magic — a well-structured prompt with clear prose can outperform a poorly-reasoned one wrapped in tags. Don't add tag nesting beyond what actually clarifies structure.

## Freshness

Anthropic's documentation on prompting techniques, supported models, and platform features evolves. If the user needs current guidance (e.g., which Claude model to target, or current best practices for a specific feature like tool use or extended thinking) and a search tool is available, verify against current Anthropic documentation rather than relying solely on this file.
