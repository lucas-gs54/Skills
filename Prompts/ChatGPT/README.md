# ChatGPT Prompts

Prompts otimizados para **ChatGPT** (GPT-4, GPT-3.5, GPT-4o, etc.).

## Disponíveis

| Arquivo | Uso |
|---------|-----|
| `concise-teaching(gpt).md` | Explicações diretas, didáticas, sem fluff — ideal para tutoriais, docs, onboarding |
| `concise-thinking(gpt).md` | Reasoning estruturado, cadeia de pensamento explícita, foco na conclusão acionável |

## Otimizações Específicas ChatGPT

- Hierarquia de instruções com `#` sections
- System prompt + user prompt separados
- Awareness de function calling / tools quando relevante
- Output format explícito (JSON, Markdown, code blocks)
- Exemplos few-shot quando melhoram adherence

## Como Aplicar

```bash
# Copie o conteúdo do .md desejado
# Cole como System Prompt (Settings → System Prompt)
# OU cole como primeira mensagem da conversa
```

## Customização

Substitua placeholders:
- `[SEU_TEMA_AQUI]` → tópico específico
- `[SEU_PUBLICO_ALVO]` → nível técnico (iniciante, intermediário, expert)
- `[FORMATO_DESEJADO]` → markdown, json, bullet points, etc.