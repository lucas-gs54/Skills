# lgs54-doc-optimizer

**Otimiza documentos Markdown existentes** — melhora clareza, concisão, correção gramatical, legibilidade e consistência sem alterar fatos.

## Quando Usar

- Tem um README, CHANGELOG, guia ou doc técnico que está **verboso, confuso, com erros gramaticais ou inconsistente**
- Quer **refinar a expressão** mantendo o sentido técnico original intacto
- Precisa padronizar terminologia, formatação, estilo em documentos existentes

## Modos de Operação

| Modo | Foco |
|------|------|
| `full` | Todas as otimizações (padrão para "melhore isso") |
| `conciseness` | Reduzir word count preservando significado |
| `readability` | Frases curtas, parágrafos quebrados, escaneabilidade |
| `correctness` | Ortografia, gramática, pontuação, tipografia |
| `consistency` | Unificar termos, formatação, estilos |

## Exemplo de Trigger

> "Melhora esse README, tá muito verboso e confuso"
> "Otimiza esse CHANGELOG pra ficar consistente"
> "Corrige a gramática e deixa esse guia mais legível"

## O Que Não Faz

- ❌ Inventa fatos, comandos, versões, APIs
- ❌ Traduz (a menos que pedido explicitamente)
- ❌ Altera código examples ou especificações técnicas
- ❌ Reescreve docs auto-gerados (OpenAPI, etc.) — apenas sinaliza

## Referências

- `references/language-rules.md` — Regras por idioma (pt-BR, en-US, es-ES)
- `references/conciseness-guide.md` — Padrões para reduzir verbosidade
- `references/readability-guide.md` — Estrutura de frases/parágrafos, escaneabilidade
- `references/correctness-guide.md` — Padrões de erro comuns, regras de correção
- `references/consistency-guide.md` — Unificação de terminologia, formatação