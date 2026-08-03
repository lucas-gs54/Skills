# ChatGPT — Platform-Specific Optimization

Read this file after Step 1 (Identify Target Platform) when the target is ChatGPT, before constructing or optimizing the final prompt.

## Recommended Practices

When targeting ChatGPT:

- Use clear and direct instructions.
- Explicitly define the desired outcome.
- Separate context from instructions.
- Use structured sections for complex tasks.
- Define constraints precisely.
- Specify output requirements.
- Use examples when they improve reliability.
- Avoid vague behavioral instructions.
- Explicitly describe tool usage requirements when relevant.

## Instruction Hierarchy

When the target environment exposes instruction hierarchy — system, developer, and user messages — place requirements in the appropriate layer:

- **System / developer messages**: durable behavior, role, constraints that should hold across the whole conversation.
- **User messages**: the specific task or request being made in the moment.

Do not assume that every ChatGPT interface exposes the same instruction hierarchy, tools, or capabilities. A prompt written for the API (with distinct system/developer/user roles) may need to collapse into a single message for a UI-only context, and vice versa. Ask or infer which surface the user is targeting if it materially changes the structure.

## Reliability Notes

Never claim that a specific behavior is guaranteed merely because the prompt uses a particular structure. Structure improves the odds of the desired behavior; it does not guarantee it, and this should not be overstated to the user.

## Freshness

OpenAI's guidance on system/developer message structure, tool-calling conventions, and model-specific behavior evolves. If the user needs current guidance (e.g., "what's the latest way to structure a system prompt for GPT-5") and a search tool is available, verify against current OpenAI documentation rather than relying solely on this file.
