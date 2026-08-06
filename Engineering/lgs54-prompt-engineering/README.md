# lgs54-prompt-engineering

**Cria, analisa, otimiza, valida e adapta prompts** para **ChatGPT, Claude e Gemini**.

## Quando Usar

- Quer criar um prompt **do zero** para qualquer uma das 3 plataformas
- Tem um prompt existente que **falha, é inconsistente ou podia ser melhor**
- Precisa **adaptar um prompt** de uma plataforma para outra (ex: ChatGPT → Claude)
- Quer diagnosticar **por que um prompt falhou** (análise de resposta ruim)

## Plataformas Suportadas

| Plataforma | Referência |
|------------|------------|
| ChatGPT (GPT-4, GPT-3.5, etc.) | `references/chatgpt.md` |
| Claude (Opus, Sonnet, Haiku) | `references/claude.md` |
| Gemini (Pro, Flash, etc.) | `references/gemini.md` |

**Sempre identifica a plataforma alvo ANTES de gerar o prompt final.**

## Pipeline Obrigatório

1. **Identifica plataforma** (pergunta se não especificada)
2. **Entende objetivo** (outcome, task, audience, constraints, output format)
3. **Classifica task** (writing, coding, analysis, reasoning, agentic, etc.)
4. **Analisa prompt existente** (se houver) — ambiguidades, contradições, redundâncias
5. **Constrói/otimiza** — aplica princípios universais + plataforma específica
6. **Adapta plataforma** — lê `references/<plataforma>.md`
7. **Valida** (Validation Checklist: objective, context, instructions, constraints, inputs, output, examples, platform, quality)
8. **Entrega** — **Final Prompt** (code block) + **Improvements Made**

## Princípios Core

- **Clareza**: instruções acionáveis, não "faça seu melhor"
- **Especificidade**: define objective, context, task, constraints, output format
- **Consistência**: detecta contradições, prioridades ambiguas, variáveis indefinidas
- **Separação de contexto**: instructions ≠ context ≠ examples ≠ user input
- **Controle de output**: formato explícito quando relevante
- **Prompt mínimo suficiente**: máximo de clareza com mínimo de complexidade
- **Adaptação de plataforma**: técnicas específicas só quando trazem vantagem real

## Exemplo de Trigger

> "Cria um prompt pro Claude pra fazer code review"
> "Otimiza esse prompt pro ChatGPT, tá dando resposta inconsistente"
> "Adapta esse prompt do Gemini pro ChatGPT"
> "Por que esse prompt falhou? [cola prompt + resposta ruim]"

## Referências

- `references/chatgpt.md` — Estrutura, instruction hierarchy, syntax OpenAI
- `references/claude.md` — XML delimiters, system prompts, thinking tags Anthropic
- `references/gemini.md` — `#` section structure, grounding, citations Google