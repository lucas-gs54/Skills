# Prompts

Prompts prontos para uso, otimizados por plataforma.

## Estrutura

```
Prompts/
├── ChatGPT/   # Prompts otimizados para ChatGPT (GPT-4, GPT-3.5)
├── Claude/    # Prompts otimizados para Claude (Opus, Sonnet, Haiku)
└── Gemini/    # Prompts otimizados para Gemini (Pro, Flash)
```

## Prompts Disponíveis

| Prompt | Descrição | Plataformas |
|--------|-----------|-------------|
| `concise-teaching` | Ensino conciso — explicações diretas, exemplos práticos, sem verbosidade | ChatGPT, Claude, Gemini |
| `concise-thinking` | Pensamento conciso — reasoning estruturado, step-by-step, foco na essência | ChatGPT, Claude, Gemini |

## Como Usar

1. Escolha a plataforma alvo (ChatGPT / Claude / Gemini)
2. Copie o conteúdo do `.md` correspondente
3. Cole como system prompt ou primeira mensagem na conversa
4. Adapte placeholders `[SEU_CONTEXTO_AQUI]` conforme necessário

## Otimização por Plataforma

Cada versão é adaptada para:
- **ChatGPT**: Instruções hierárquicas, `#` sections, function calling awareness
- **Claude**: XML delimiters (`<instructions>`, `<context>`), thinking tags, long context handling
- **Gemini**: `#` section structure, grounding citations, structured output hints

## Quando Criar Novo Prompt

Use a skill **`lgs54-prompt-engineering`** (em `Engineering/`) para:
- Criar prompts do zero otimizados por plataforma
- Adaptar prompts existentes entre plataformas
- Diagnosticar/otimizar prompts que falham