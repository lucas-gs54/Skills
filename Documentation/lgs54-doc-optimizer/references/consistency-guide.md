# Consistency Guide

Rules for unifying terminology, formatting, and style across a document. Apply per language rules from `language-rules.md`.

## Universal Consistency Rules

### Terminology
- **One term per concept** — pick one and use everywhere
  - `config` / `configuration` / `settings` → choose one
  - `auth` / `authentication` / `login` → choose one
  - `env` / `environment` / `env vars` → choose one
- **Canonical names** — use official product names: `GitHub Actions` (not `GH Actions`), `Visual Studio Code` (not `VSCode` in first use), `Amazon Web Services` → `AWS` (after first use)
- **Abbreviations** — define on first use: `Application Programming Interface (API)`, then `API`
- **Case sensitivity** — preserve: `JavaScript`, `TypeScript`, `GitHub`, `Docker`, `Kubernetes`, `PostgreSQL`

### Formatting

#### Code Elements
| Element | Format | Example |
|---------|--------|---------|
| Inline code | backticks | `` `npm install` `` |
| File paths | backticks | `` `src/config.yaml` `` |
| Commands | backticks | `` `docker build` `` |
| Variables | backticks | `` `API_KEY` `` |
| Parameters | backticks | `` `timeout` `` |
| Functions/Methods | backticks + `()` | `` `fetchData()` `` |
| Classes/Types | backticks | `` `UserConfig` `` |
| Environment vars | backticks | `` `NODE_ENV` `` |

#### Lists
- **Single marker** — `-` (default), never mix `-`, `*`, `+`
- **Parallel structure** — all items same grammatical form
- **Punctuation** — consistent: all with period or all without
- **Capitalization** — consistent: all start with capital or all lowercase

#### Headings
- **Single H1** — document title only
- **No skipped levels** — H1 → H2 → H3
- **Descriptive** — `## Installation` not `## Getting Started`
- **Parallel phrasing** — `## Install`, `## Configure`, `## Run` (all verbs) or `## Installation`, `## Configuration`, `## Execution` (all nouns)

#### Tables
- **Header row** — always present
- **Alignment** — left for text, right for numbers, center for boolean/flags
- **Consistent formatting** — pipes aligned in source when readable
- **Units in headers** — `| Timeout (ms) |` not `| Timeout |` with `5000` in cells

#### Links
- **Descriptive text** — `[API reference](url)` not `[click here](url)`
- **Reference style** for long docs — `[text][ref]` + definitions at bottom
- **Internal links** — match actual heading slugs

#### Dates & Numbers
- **Dates** — `YYYY-MM-DD` (ISO 8601) or `DD/MM/YYYY` (PT/ES) or `MM/DD/YYYY` (US) — pick one per doc
- **Versions** — `v1.2.3` or `1.2.3` — consistent prefix
- **Large numbers** — `1_000_000` or `1,000,000` or `1000000` — consistent separator
- **Units** — space between number and unit: `5 MB`, `10 ms`, `2 GB`

### Style Conventions

#### Voice & Tone
- **Imperative for instructions** — `Run the command` not `You should run the command`
- **Present tense** — `The function returns` not `will return`
- **Second person** — `You can configure` not `The user can configure`
- **Avoid** — `We recommend`, `It is recommended that` → use `Recommend:` or direct instruction

#### Capitalization in Sentences
- **Sentence case** for headings, list items, table cells (not Title Case)
- **Proper nouns** — canonical casing
- **First word only** capitalized unless proper noun

#### Emphasis
- **Bold** (`**`) — key terms, UI elements, file names, commands in prose
- **Italic** (`*`) — first use of term, emphasis, placeholders (`*your-api-key*`)
- **Never** — bold+italic, underline, ALL CAPS (except acronyms)

---

## Portuguese (BR/PT)

### Terminologia
| Conceito | Termo Preferido | Evitar |
|----------|----------------|--------|
| configuração | `configuração` | `config`, `settings`, `ajustes` |
| autenticação | `autenticação` | `auth`, `login`, `acesso` |
| variável de ambiente | `variável de ambiente` | `env`, `env var` |
| linha de comando | `linha de comando` | `CLI`, `terminal`, `console` |
| código-fonte | `código-fonte` | `source code`, `src` |
| banco de dados | `banco de dados` | `database`, `DB` |
| implantação | `implantação` | `deploy`, `publicação` |

### Formatação
- **Listas** — marcador `-` ; letra minúscula no início; sem ponto final (exceto frases completas)
- **Tabelas** — cabeçalho em maiúscula inicial; unidades no cabeçalho
- **Datas** — `DD/MM/AAAA` ou `AAAA-MM-DD` (ISO) — consistente
- **Números** — separador de milhar `.` (ponto), decimal `,` (vírgula): `1.000,50`

### Estilo
- **Imperativo** para instruções: `Execute o comando`
- **Presente do indicativo** para descrições: `O sistema processa`
- **Segunda pessoa** opcional: `Você pode configurar` ou `Configure`
- **Evite** — `Recomendamos que`, `É recomendado que` → use `Recomendação:`

### Capitalização
- **Sentence case** em headings, itens de lista, células de tabela
- **Nomes próprios** — casing canônico: `JavaScript`, `GitHub`, `Docker`

---

## English (US/UK)

### Terminology
| Concept | Preferred Term | Avoid |
|---------|----------------|-------|
| configuration | `configuration` | `config`, `settings`, `setup` |
| authentication | `authentication` | `auth`, `login`, `sign-in` |
| environment variable | `environment variable` | `env`, `env var` |
| command line | `command line` | `CLI`, `terminal`, `shell` |
| source code | `source code` | `src`, `codebase` |
| database | `database` | `DB`, `data store` |
| deployment | `deployment` | `deploy`, `release` |

### Formatting
- **Lists** — `-` marker; capital first letter; period if complete sentences, no period if fragments
- **Tables** — header capitalized; units in header
- **Dates** — `YYYY-MM-DD` (ISO) or `MM/DD/YYYY` (US) — consistent
- **Numbers** — thousands separator `,` (comma), decimal `.` (period): `1,000.50`

### Style
- **Imperative** for instructions: `Run the command`
- **Present tense** for descriptions: `The system processes`
- **Second person**: `You can configure` or `Configure`
- **Avoid** — `We recommend`, `It is recommended that` → use `Recommendation:`

### Capitalization
- **Sentence case** for headings, list items, table cells
- **Proper nouns** — canonical casing: `JavaScript`, `GitHub`, `Docker`

---

## Spanish (ES)

### Terminología
| Concepto | Término Preferido | Evitar |
|----------|------------------|--------|
| configuración | `configuración` | `config`, `settings`, `ajustes` |
| autenticación | `autenticación` | `auth`, `login`, `acceso` |
| variable de entorno | `variable de entorno` | `env`, `env var` |
| línea de comandos | `línea de comandos` | `CLI`, `terminal`, `consola` |
| código fuente | `código fuente` | `source code`, `src` |
| base de datos | `base de datos` | `database`, `BD` |
| despliegue | `despliegue` | `deploy`, `publicación` |

### Formato
- **Listas** — marcador `-` ; minúscula inicial; sin punto final (salvo frases completas)
- **Tablas** — encabezado con mayúscula inicial; unidades en encabezado
- **Fechas** — `DD/MM/AAAA` o `AAAA-MM-DD` (ISO) — consistente
- **Números** — separador de millar `.` (punto) o ` ` (espacio), decimal `,` (coma): `1.000,50` o `1 000,50`

### Estilo
- **Imperativo** para instrucciones: `Ejecute el comando`
- **Presente de indicativo** para descripciones: `El sistema procesa`
- **Segunda persona** opcional: `Puede configurar` o `Configure`
- **Evite** — `Recomendamos que`, `Se recomienda que` → use `Recomendación:`

### Capitalización
- **Sentence case** en encabezados, ítems de lista, celdas de tabla
- **Nombres propios** — casing canónico: `JavaScript`, `GitHub`, `Docker`

---

## Cross-Document Consistency (When Applicable)

If optimizing multiple docs in a project:
- **Shared glossary** — maintain term mappings in a central file
- **Style decisions** — document choices (date format, list punctuation, heading style)
- **Automated checks** — Vale, cSpell, markdownlint configs in repo

---

## Validation Checklist

- [ ] Single term used for each concept throughout document
- [ ] All code elements formatted consistently (backticks, language tags)
- [ ] List marker consistent (`-` only)
- [ ] List items parallel structure
- [ ] Heading hierarchy correct (H1→H2→H3, no skips)
- [ ] Heading phrasing parallel (all verbs or all nouns)
- [ ] Table headers present, aligned, units in headers
- [ ] Link text descriptive (no "click here")
- [ ] Date format consistent
- [ ] Number/unit format consistent
- [ ] Emphasis style consistent (`**bold**`, `*italic*`)
- [ ] Voice/tone consistent (imperative for instructions, present for descriptions)
- [ ] Capitalization consistent (sentence case for headings/lists/tables)