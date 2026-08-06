# lgs54-skill-creator

**Cria, refina, estrutura, valida e otimiza skills no padrão lgs54** — workflow universal padronizado para o ecossistema.

## Quando Usar

- Quer **criar uma skill nova** do zero seguindo o padrão do repositório
- Precisa **refinar/atualizar** skill existente para compliance lgs54
- Quer **estabelecer reference files** ou padronizar arquitetura de skills
- Validar se uma skill existente bate com os standards

## Categoria & Estrutura de Diretório

Skills vão em pastas de categoria no workspace:

```
<Category>/<skill-name>/
├── SKILL.md
└── references/
    ├── <aspect-1>.md
    └── <aspect-2>.md
```

| Categoria | Foco |
|-----------|------|
| `Engineering/` | Prompt engineering, workflow automation, code architecture, testing, debugging, context management |
| `Documentation/` | Content generation, markdown formatting, technical writing, API specs, READMEs |
| `[Custom]` | Nova pasta se domínio exigir (ex: `Design/`, `Data/`) |

**Regra**: diretório da skill = `name` no `SKILL.md` (ex: `lgs54-skill-creator`)

## 5 Princípios Core lgs54

1. **Clear Triggers & Frontmatter** — `description` define **o que faz** + **quando usar** ("Use when...")
2. **Progressive Disclosure** — `SKILL.md` = workflow high-level; detalhes em `references/*.md` carregados sob demanda
3. **Imperative & Actionable** — "Determine target platform before generating output", "Do not skip validation"
4. **Zero Assumption Policy** — inputs explícitos, pergunta quando ambíguo, nunca assume intenção
5. **Mandatory Quality Validation** — checklist operacional antes de completar

## Workflow de Criação (7 Steps)

1. **Understand Purpose & Scope** — problema que resolve, triggers, categoria, folder name, modular sub-domains
2. **Determine Category & Folder Name** — parent category + `lgs54-*` naming
3. **Define Frontmatter** — YAML padrão (`name`, `description`, `metadata`: author, version, purpose)
4. **Draft Main Sections** — Purpose, Language, Supported Modes, Mandatory Operating Rule, Core Principles, Workflow Steps, Quality Checklist
5. **Extract References** — identifica sub-domínios → split em `references/*.md`
6. **Validate Architecture** — frontmatter validity, paths, triggers, checklist compliance vs `references/skill-standards.md`
7. **Deliver Skill Files** — `SKILL.md` + reference files

## Quality Checklist (Antes de Finalizar)

- [ ] Folder name == `name` em `SKILL.md`
- [ ] Frontmatter completo: `name`, `description`, `metadata` (author: lucas-gs54, version, purpose)
- [ ] `description` com triggers claros ("Use when...")
- [ ] Referências grandes splitadas em `references/`
- [ ] Links markdown usam relative syntax (`references/<file>.md`)
- [ ] Seção Language incluída
- [ ] Mandatory Operating Rule + Validation steps explícitos

## Exemplo de Trigger

> "Cria uma skill lgs54 pra code review de Rust"
> "Valida se essa skill tá no padrão lgs54"
> "Refina a skill X pra seguir os standards"
> "Padroniza a arquitetura de references das skills"

## Referências

- `references/skill-standards.md` — Architecture & quality standards (naming, frontmatter, sections, progressive disclosure, tone)
- `references/template-skill.md` — Canonical SKILL.md template with exact section structure