# Correctness Guide

Common error patterns and correction rules per language. Apply strictly per `language-rules.md`.

## Universal Categories

### Spelling
- **Technology names** — canonical casing: `GitHub`, `TypeScript`, `JavaScript`, `Node.js`, `Kubernetes`, `PostgreSQL`, `MySQL`, `Redis`, `Docker`, `Kafka`, `GraphQL`, `REST`, `gRPC`, `WebAssembly`, `WebSocket`
- **Acronyms** — all caps: `API`, `SDK`, `CLI`, `UI`, `UX`, `CI`, `CD`, `SSH`, `TLS`, `SSL`, `HTTP`, `HTTPS`, `JSON`, `YAML`, `XML`, `HTML`, `CSS`, `SQL`, `NoSQL`, `ORM`, `JWT`, `OAuth`, `OIDC`
- **File extensions** — lowercase: `.js`, `.ts`, `.py`, `.rs`, `.go`, `.json`, `.yaml`, `.yml`, `.md`, `.txt`, `.env`, `.toml`
- **Commands** — lowercase: `npm`, `yarn`, `pnpm`, `pip`, `cargo`, `go`, `docker`, `kubectl`, `git`, `make`, `bash`, `zsh`, `curl`, `wget`

### Grammar
- **Subject-verb agreement** — singular subject → singular verb; plural → plural
- **Pronoun antecedent clarity** — avoid ambiguous `it`, `they`, `this` without clear referent
- **Tense consistency** — don't mix present/past/future in same context without reason
- **Parallel structure** — list items same grammatical form

### Punctuation
- **Missing/extra commas** — especially in lists, clauses, compound sentences
- **Apostrophe misuse** — `it's` = `it is`; `its` = possessive; `you're`/`your`, `they're`/`their`/`there`
- **Quotation marks** — matching pairs; correct nesting
- **Dash vs hyphen** — em dash `—` for breaks; en dash `–` for ranges; hyphen `-` for compounds

### Typography
- **Straight quotes** `""` → curly `“”` (or `«»` per language)
- **Prime marks** `'` `"` → proper apostrophe `'` and quotes
- **Ellipsis** — `…` (single char) not `...`
- **Multiplication sign** — `×` not `x` in measurements (`1920×1080`)
- **Non-breaking spaces** — before units (`5 MB`), after section symbols (`§ 12`)

---

## Portuguese (BR/PT)

### Erros Ortográficos Frequentes

| Incorreto | Correto |
|-----------|---------|
| `ideía` / `idéia` | `ideia` |
| `vôo` | `voo` |
| `freqüente` / `frequente` | `frequente` |
| `exceção` | `exceção` (mantém) |
| `privilégio` | `privilégio` (mantém) |
| `benefício` | `benefício` (mantém) |
| `hífen` | `hífen` (mantém) |
| `assíncrono` | `assíncrono` (mantém) |

### Crase
| Incorreto | Correto |
|-----------|---------|
| `vá a reunião` | `vá à reunião` |
| `referente a API` | `referente à API` |
| `conforme a documentação` | `conforme a documentação` (sem crase — `conforme` pede `a` sem artigo) |
| `segundo a especificação` | `segundo a especificação` (sem crase) |

### Hífen (Pós-Acordo)
| Incorreto | Correto |
|-----------|---------|
| `auto-estima` | `autoestima` |
| `anti-inflamatório` | `anti-inflamatório` (mantém — `i` + `i`) |
| `hiper-reativo` | `hiper-reativo` (mantém — `h` inicial) |
| `re-educar` | `reeducar` |
| `co-operar` | `cooperar` |
| `micro-ondas` | `micro-ondas` (mantém — substantivo composto) |
| `pé-de-meia` | `pé-de-meia` (mantém — locução) |

### Concordância
| Incorreto | Correto |
|-----------|---------|
| `a maioria dos usuários preferem` | `a maioria dos usuários prefere` |
| `metade dos testes falharam` | `metade dos testes falhou` |
| `uma série de erros ocorreram` | `uma série de erros ocorreu` |
| `vários usuários relatou` | `vários usuários relataram` |

### Regência Verbal
| Incorreto | Correto |
|-----------|---------|
| `precisa de instalar` | `precisa instalar` |
| `ajuda a fazer` | `ajuda a fazer` (correto) |
| `permite de fazer` | `permite fazer` |
| `obriga de fazer` | `obriga a fazer` |

### Pontuação
- **Vírgula antes de `e`/`ou` em enumeração** — usar vírgula de Oxford: `A, B, e C`
- **Não separar sujeito de predicado**: `O sistema, processa` → `O sistema processa`
- **Dois-pontos** para introduzir lista/citação: `Use os comandos: ls, cd, pwd`

### Maiúsculas Indevidas
| Incorreto | Correto |
|-----------|---------|
| `O Gerente de Projeto` | `o gerente de projeto` |
| `A API REST` | `a API REST` (API já é sigla) |
| `O JavaScript` | `o JavaScript` (nome próprio — mantém) |
| `A Documentação` | `a documentação` |

---

## English (US/UK)

### Common Spelling Errors

| Incorrect | Correct |
|-----------|---------|
| `teh` | `the` |
| `recieve` | `receive` |
| `seperate` | `separate` |
| `definately` | `definitely` |
| `occured` | `occurred` |
| `refering` | `referring` |
| `its` (for `it's`) | `it's` / `its` (context) |
| `your` (for `you're`) | `you're` / `your` |
| `there` (for `their`/`they're`) | `their` / `they're` / `there` |

### Technology Names (Canonical)
- `Github` → `GitHub`
- `Typescript` → `TypeScript`
- `Javascript` → `JavaScript`
- `Nodejs` / `NodeJS` → `Node.js`
- `Postgres` → `PostgreSQL` (in formal docs)
- `Mysql` → `MySQL`
- `K8s` → `Kubernetes` (first use), then `K8s`
- `Graphql` → `GraphQL`
- `Grpc` → `gRPC`

### Grammar
- **Subject-verb**: `The list of files are long` → `The list of files is long`
- **Collective nouns**: US → singular (`The team is`); UK → plural (`The team are`) — pick one
- **Dangling modifiers**: `Running the script, the error appeared` → `Running the script, I saw the error appear`
- **Parallelism**: `The script validates, transforms, and loading data` → `The script validates, transforms, and loads data`

### Punctuation
- **Oxford comma** — use consistently: `foo, bar, and baz`
- **Semicolons** — `Item one; item two; and item three` (complex items)
- **Colons** — `The command takes three arguments: input, output, and mode`
- **Dashes** — `The result—unexpected but correct—was logged`
- **Apostrophes** — `it's` = `it is`; `its` = possessive

### US vs UK Spelling (Pick One, Be Consistent)

| US | UK |
|----|----|
| `color` | `colour` |
| `behavior` | `behaviour` |
| `optimize` | `optimise` |
| `analyze` | `analyse` |
| `license` (noun/verb) | `licence` (noun) / `license` (verb) |
| `program` | `programme` (non-computing) |
| `disk` | `disc` (optical media) |

---

## Spanish (ES)

### Errores Ortográficos Frecuentes

| Incorrecto | Correcto |
|------------|----------|
| `haiga` | `haya` |
| `vaya` (verbo ir) | `vaya` (correcto) / `valla` (cerca) |
| `haya` (verbo haber) | `haya` / `halla` (encontrar) / `allá` (lugar) |
| `echo` (verbo echar) | `hecho` (participio) / `echo` (echar) |
| `a ver` | `a ver` / `haber` (infinitivo) |
| `sino` (conjuncción) | `sino` / `si no` (condicional) |
| `por que` / `porqué` / `por qué` / `porquè` | context-dependent |

### Acentuación (RAE Actual)
- **Monosílabos** — sin tilde: `guion`, `truhan`, `fie`, `lio`, `sion` (excepciones: `té`, `dé`, `sé`, `mí`, `tú`, `él`, `sí`)
- **Demostrativos** — sin tilde: `este`, `ese`, `aquel`, `esta`, `esa`, `aquella`
- **Solo** — sin tilde como adverbio (`solo quiero` = `solamente quiero`)
- **Guion** — sin tilde (palabra llana terminada en `n`)

### Concordancia
| Incorrecto | Correcto |
|------------|----------|
| `la mayoría de los usuarios prefieren` | `la mayoría de los usuarios prefiere` |
| `la mitad de las pruebas fallaron` | `la mitad de las pruebas falló` |
| `una serie de errores ocurrieron` | `una serie de errores ocurrió` |
| `varios usuarios reportó` | `varios usuarios reportaron` |

### Regencia Verbal
| Incorrecto | Correcto |
|------------|----------|
| `necesita de instalar` | `necesita instalar` |
| `ayuda a hacer` | `ayuda a hacer` (correcto) |
| `permite de hacer` | `permite hacer` |
| `obliga de hacer` | `obliga a hacer` |

### Mayúsculas Indevidas
| Incorrecto | Correcto |
|------------|----------|
| `El Gerente de Proyecto` | `el gerente de proyecto` |
| `La API REST` | `la API REST` |
| `El JavaScript` | `el JavaScript` (nombre propio — mantiene) |
| `La Documentación` | `la documentación` |

### Pontuación
- **Coma antes de `y`/`o` en enumeración** — usar coma de Oxford: `A, B, y C`
- **No separar sujeto de predicado**: `El sistema, procesa` → `El sistema procesa`
- **Dos puntos** para introducir lista/cita: `Use los comandos: ls, cd, pwd`
- **Comillas** — `« »` principales; `“ ”` anidadas

---

## Validation Checklist (Per Language)

### Portuguese
- [ ] Acordo Ortográfico aplicado
- [ ] Crase correta
- [ ] Hífen conforme regras atuais
- [ ] Concordância sujeito-verbo
- [ ] Regência verbal
- [ ] Pontuação (vírgula Oxford, não separar S+P)
- [ ] Maiúsculas apenas em próprios/início
- [ ] Termos técnicos em inglês canônicos

### English
- [ ] US/UK variant consistent
- [ ] Canonical tech name casing
- [ ] Subject-verb agreement
- [ ] No dangling modifiers
- [ ] Parallel structure in lists
- [ ] Oxford comma consistent
- [ ] Apostrophes correct (`it's` vs `its`)
- [ ] Tense consistency

### Spanish
- [ ] RAE ortografía vigente
- [ ] Acentuación monosílabos/demostrativos/solo
- [ ] Concordancia sujeto-verbo
- [ ] Regencia verbal
- [ ] Pontuação (coma Oxford, no separar S+P)
- [ ] Mayúsculas solo en propios/inicio
- [ ] Comillas angulares `« »`
- [ ] Términos técnicos en inglés canónico

---

## Correction Workflow

1. **Automated pass** — spellchecker + linter (cSpell, Vale, LanguageTool)
2. **Manual review** — focus on tech terms, context-dependent grammar
3. **Language-specific rules** — apply tables above
4. **Flag uncertainties** — `<!-- TODO: verify grammar -->` for ambiguous cases
5. **Don't correct** — code examples, variable names, CLI output, user-provided strings