# lgs54-markdown-generation

**Cria, estrutura e otimiza documentos Markdown do zero** — README, CHANGELOG, CONTRIBUTING, docs técnicas/API, guias gerais.

## Quando Usar

- Precisa **criar um documento novo** seguindo convenções da indústria
- Tem conteúdo bruto (código, package.json, commit history) e quer transformar em doc estruturado
- Quer **reestruturar** um doc existente para o tipo correto (ex: README → CHANGELOG)

## Tipos de Documento Suportados

| Tipo | Convenção |
|------|-----------|
| **README** | Identidade, badges, install, usage, contributing, license |
| **CHANGELOG** | Keep a Changelog + Conventional Commits |
| **CONTRIBUTING** | Guidelines, setup, code style, PR process |
| **Technical/API** | Diátaxis: reference, guide, tutorial, explanation |
| **General** | Wikis, tutoriais, notas, guias internos |

## Fluxo Obrigatório

1. **Identifica o tipo** (pergunta se ambíguo)
2. **Entende o projeto/contexto** (nome, propósito, audience, stack)
3. **Coleta conteúdo** (usa placeholders `<!-- TODO: ... -->` para fatos faltando)
4. **Aplica estrutura do tipo** (lê `references/<tipo>.md`)
5. **Aplica formatação universal** (headings, lists, code blocks, links, tables)
6. **Valida** (Quality Checklist)
7. **Entrega arquivo `.md`** + summary de melhorias

## Exemplo de Trigger

> "Preciso de um README pra esse projeto"
> "Gera um CHANGELOG seguindo Keep a Changelog"
> "Documenta essa API em Markdown"
> "Cria um CONTRIBUTING.md pro repo"

## O Que Não Faz

- ❌ Inventa fatos (licença, comandos install, versões, APIs) — usa placeholders
- ❌ Copia estrutura de README pra CHANGELOG (cada tipo tem convenção própria)
- ❌ Adiciona badges/emoji/decoração sem propósito

## Referências

- `references/readme.md` — Estrutura README padrão indústria
- `references/changelog.md` — Keep a Changelog format
- `references/contributing.md` — Guia de contribuição padrão
- `references/technical-docs.md` — Diátaxis + API docs conventions
- `references/docs.md` — Documentação geral (guias, tutoriais, wikis)