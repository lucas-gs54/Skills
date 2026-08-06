# Skills

Repositório de skills reutilizáveis para aumentar produtividade e extrair o máximo de modelos de IA em diferentes tarefas.

As skills seguem as documentações e boas práticas oficiais de **Anthropic**, **Google** e **OpenAI**, adaptadas para uso prático.

## Estrutura

```
Skills/
├── Documentation/     # Skills de documentação, escrita e geração de conteúdo
├── Engineering/       # Skills de engenharia, prompt engineering, análise de código
└── Prompts/           # Prompts prontos otimizados por plataforma
```

## Documentation/

| Skill | Quando Usar |
|-------|-------------|
| [`lgs54-doc-optimizer`](Documentation/lgs54-doc-optimizer/) | Melhorar clareza, concisão, correção e legibilidade de docs Markdown existentes (READMEs, guias, CHANGELOGs, docs de API) |
| [`lgs54-markdown-generation`](Documentation/lgs54-markdown-generation/) | Criar, estruturar ou otimizar documentos Markdown do zero: README, CHANGELOG, CONTRIBUTING, docs técnicas, guias |

## Engineering/

| Skill | Quando Usar |
|-------|-------------|
| [`lgs54-prompt-engineering`](Engineering/lgs54-prompt-engineering/) | Criar, analisar, otimizar, validar ou adaptar prompts para **ChatGPT, Claude ou Gemini** |
| [`lgs54-deep-code-analysis`](Engineering/lgs54-deep-code-analysis/) | Análise profunda de código: bugs sutis, race conditions, memory leaks, falhas arquiteturais, performance, segurança |
| [`lgs54-frontend-craft`](Engineering/lgs54-frontend-craft/) | Buildar/revisar frontends com qualidade profissional: HTML semântico, acessibilidade, design tokens, motion purposeful, CSS performático |
| [`lgs54-project-modularization`](Engineering/lgs54-project-modularization/) | Reorganizar codebases bagunçados em estrutura modular limpa (survey → plan → implement), preservando comportamento |
| [`lgs54-skill-creator`](Engineering/lgs54-skill-creator/) | Criar, refinar, validar e padronizar novas skills no padrão **lgs54** |

## Prompts/

Prompts prontos para uso, otimizados por plataforma:

- **ChatGPT/**: `concise-teaching`, `concise-thinking`
- **Claude/**: `concise-teaching`, `concise-thinking`
- **Gemini/**: `concise-teaching`, `concise-thinking`

## Como Escolher a Skill Certa

```
Precisa otimizar um texto/documento existente?
  → lgs54-doc-optimizer

Precisa criar um documento Markdown do zero (README, CHANGELOG, etc.)?
  → lgs54-markdown-generation

Precisa trabalhar com prompts (criar, melhorar, adaptar entre plataformas)?
  → lgs54-prompt-engineering

Precisa analisar código profundamente (bugs, arquitetura, performance, segurança)?
  → lgs54-deep-code-analysis

Precisa buildar/revisar frontend com padrão profissional?
  → lgs54-frontend-craft

Precisa reorganizar/modularizar um projeto bagunçado?
  → lgs54-project-modularization

Precisa criar uma nova skill no padrão lgs54?
  → lgs54-skill-creator
```

## Padrão lgs54

Todas as skills seguem:

- **Frontmatter YAML** obrigatório (`name`, `description`, `metadata`)
- **Arquitetura modular**: `SKILL.md` (workflow principal) + `references/*.md` (detalhes carregados sob demanda)
- **Triggers explícitos** na description ("Use when...")
- **Regras operacionais obrigatórias** + **Quality Checklist** final
- **Linguagem flexível**: comunica no idioma do usuário
- **Zero assunção**: pergunta quando ambíguo, nunca assume