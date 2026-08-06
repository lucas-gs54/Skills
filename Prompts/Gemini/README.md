# Gemini Prompts

Prompts otimizados para **Gemini** (Pro, Flash, 1.5, 2.0, etc.).

## Disponíveis

| Arquivo | Uso |
|---------|-----|
| `concise-teaching(gemini).md` | Explicações diretas, didáticas, sem fluff — ideal para tutoriais, docs, onboarding |
| `concise-thinking(gemini).md` | Reasoning estruturado, cadeia de pensamento explícita, foco na conclusão acionável |

## Otimizações Específicas Gemini

- **`#` section structure** — `# Instructions`, `# Context`, `# Examples`, `# Output Format`
- **Grounding & citations** — instruções para citar fontes, usar Google Search quando disponível
- **Structured output hints** — `response_mime_type`, `response_schema` para JSON controlado
- **Long context** — otimizado para 1M/2M token windows, instruções de chunking
- **Multimodal awareness** — text + image/video/audio handling quando relevante
- **Safety settings** awareness — harm categories, threshold tuning

## Como Aplicar

```bash
# Copie o conteúdo do .md desejado
# Cole como System Instruction (API: system_instruction / UI: System Prompt)
# OU cole como primeira mensagem com # sections
```

## Customização

Substitua placeholders:
- `[SEU_TEMA_AQUI]` → tópico específico
- `[SEU_PUBLICO_ALVO]` → nível técnico (iniciante, intermediário, expert)
- `[FORMATO_DESEJADO]` → markdown, json, bullet points, etc.
- `[FONTES_GROUNDING]` → URLs, documentos para citation
- `[SCHEMA_JSON]` → schema para structured output (se aplicável)