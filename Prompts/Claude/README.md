# Claude Prompts

Prompts otimizados para **Claude** (Opus, Sonnet, Haiku — 3.5, 3.7, etc.).

## Disponíveis

| Arquivo | Uso |
|---------|-----|
| `concise-teaching(claude).md` | Explicações diretas, didáticas, sem fluff — ideal para tutoriais, docs, onboarding |
| `concise-thinking(claude).md` | Reasoning estruturado, cadeia de pensamento explícita, foco na conclusão acionável |

## Otimizações Específicas Claude

- **XML delimiters** para separar contextos: `<instructions>`, `<context>`, `<examples>`, `<input>`, `<output_format>`
- **Thinking tags** `<thinking>` / `<thinking_budget>` para reasoning complexo
- Long context handling — instruções de como processar documentos grandes
- **Constitutional AI** awareness — harmless, helpful, honest framing
- Structured output com XML ou JSON schemas explícitos

## Como Aplicar

```bash
# Copie o conteúdo do .md desejado
# Cole como System Prompt (API: system parameter / UI: System Prompt field)
# OU cole como primeira mensagem com XML delimiters
```

## Customização

Substitua placeholders:
- `[SEU_TEMA_AQUI]` → tópico específico
- `[SEU_PUBLICO_ALVO]` → nível técnico (iniciante, intermediário, expert)
- `[FORMATO_DESEJADO]` → markdown, json, xml, bullet points, etc.
- `[CONTEXTO_ADICIONAL]` → documentos, código, dados para grounding