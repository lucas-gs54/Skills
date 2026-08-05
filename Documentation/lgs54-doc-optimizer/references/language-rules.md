# Language-Specific Rules

Load this file for the detected source language. Apply rules strictly — do not mix conventions across languages.

## Portuguese (BR/PT)

### Ortografia e Gramática
- **Acordo Ortográfico (2009)** — usar formas atuais: `ideia` (não `idéia`), `voo` (não `vôo`), `freqüente` → `frequente`
- **Crase** — `à` antes de feminino (`vá à reunião`), sem crase antes de masculino (`vá ao encontro`)
- **Hífen** — prefixos + vogal igual ou `h`: `autoestima`, `antiamericano`, `hiper-reativo`; sem hífen se vogais diferentes: `reeducar`, `cooperar`
- **Maiúsculas** — apenas nomes próprios, início de frase, siglas. Não capitalizar cargos, departamentos, tecnologias genéricas: `o gerente`, `a api`, `o javascript`
- **Números** — até dez por extenso; acima, algarismos. Unidades com espaço: `5 MB`, `10 ms`, `2 GB`

### Pontuação
- **Vírgula** — não separar sujeito do predicado (`O sistema, processa` → `O sistema processa`). Usar em enumerações, vocativos, aposições, orações subordinadas adverbiais longas no início.
- **Ponto e vírgula** — separar itens complexos em enumeração ou orações coordenadas longas sem conjunção.
- **Dois-pontos** — introduzir enumeração, citação, explicação.
- **Aspas** — `« »` ou `“ ”` para citações; `‘ ’` para citação dentro de citação. Evitar `""` retas.
- **Travessão** — `—` para diálogo ou incisos longos (não confundir com hífen `-`).

### Estilo
- **Voz ativa preferida** — `O sistema processa os dados` (não `Os dados são processados pelo sistema`)
- **Evitar gerundismo excessivo** — `Ao clicar, o botão abre` (não `Clicando no botão, ele abre`)
- **Concordância** — `A maioria dos usuários prefere` (não `preferem`); `Metade dos testes falhou` (não `falharam`)
- **Termos técnicos** — manter em inglês se consagrados (`API`, `SDK`, `CLI`, `JSON`, `YAML`, `Git`, `Docker`). Não traduzir `deploy`, `build`, `pipeline`, `endpoint`, `token`, `cache`, `bug`, `fix`, `release`.

### Formatação em Markdown
- Código inline: `` `comando` ``, `` `variável` ``, `` `arquivo.ext` ``
- Blocos de código: especificar linguagem `` ```bash ``, `` ```json ``, `` ```yaml ``
- Listas: marcador `-` consistente; não misturar `-`, `*`, `+`
- Links: texto descritivo `[documentação da API](url)`, nunca `clique aqui`

---

## English (US/UK)

### Spelling & Grammar
- **US vs UK** — detect dominant variant; keep consistent. Default: US for tech docs.
  - US: `color`, `behavior`, `optimize`, `analyze`, `license`
  - UK: `colour`, `behaviour`, `optimise`, `analyse`, `licence`
- **Technical terms** — keep canonical casing: `GitHub`, `TypeScript`, `JavaScript`, `Node.js`, `Kubernetes`, `PostgreSQL`
- **Numbers** — spell out one to nine; numerals for 10+. Units with space: `5 MB`, `10 ms`
- **Serial comma (Oxford comma)** — use consistently: `foo, bar, and baz`

### Punctuation
- **Commas** — no comma between subject and verb. Use in lists, introductory clauses, non-restrictive clauses.
- **Semicolons** — separate complex list items or independent clauses without conjunction.
- **Colons** — introduce lists, examples, explanations.
- **Quotation marks** — double `" "` for primary, single `' '` for nested. Periods/commas inside quotes (US) or outside (UK) — pick one and be consistent.
- **Dashes** — em dash `—` for parenthetical breaks; en dash `–` for ranges (`v1.0–v2.0`). Hyphen `-` for compound adjectives (`well-known`).

### Style
- **Active voice** — `The system processes data` (not `Data is processed by the system`)
- **Present tense** — `The function returns` (not `will return` or `returned`)
- **Second person** — `You can configure` (not `The user can configure`)
- **Concise phrasing** — `Use` not `Utilize`; `Start` not `Initiate`; `Fix` not `Resolve`
- **Avoid hedging** unless uncertainty is real — `The API returns an error` (not `The API may return an error` if it always does)

### Markdown Formatting
- Inline code: `` `command` ``, `` `variable` ``, `` `file.ext` ``
- Fenced blocks: specify language `` ```bash ``, `` ```json ``, `` ```yaml ``
- Lists: consistent `-` marker; no mixing `-`, `*`, `+`
- Links: descriptive text `[API reference](url)`, never `click here`

---

## Spanish (ES)

### Ortografía y Gramática
- **RAE vigente** — usar formas actuales: `solo` (sin tilde) como adverbio, `este/ese/aquel` (sin tilde) como pronombres/determinantes
- **Mayúsculas** — solo nombres propios, inicio de oración, siglas. No capitalizar cargos, tecnologías genéricas: `el gerente`, `la api`, `el javascript`
- **Números** — hasta diez por escrito; arriba, cifras. Unidades con espacio: `5 MB`, `10 ms`, `2 GB`
- **Términos técnicos** — mantener en inglés si son consagrados: `API`, `SDK`, `CLI`, `JSON`, `YAML`, `Git`, `Docker`, `deploy`, `build`, `pipeline`, `endpoint`, `token`, `caché`, `bug`, `fix`, `release`

### Puntuación
- **Coma** — no separar sujeto de predicado. Usar en enumeraciones, vocativos, aposiciones, incisos.
- **Punto y coma** — separar ítems complejos en enumeración u oraciones coordinadas largas sin conjunción.
- **Dos puntos** — introducir enumeración, cita, explicación.
- **Comillas** — `« »` (angulares) para cita principal; `“ ”` (inglesas) para cita anidada. Evitar `""` rectas.
- **Rayas** — `—` para diálogos o incisos largos (no confundir con guion `-`).

### Estilo
- **Voz activa preferida** — `El sistema procesa los datos` (no `Los datos son procesados por el sistema`)
- **Evitar gerundio abusivo** — `Al hacer clic, el botón abre` (no `Haciendo clic en el botón, este abre`)
- **Concordancia** — `La mayoría de los usuarios prefiere` (no `prefieren`); `La mitad de las pruebas falló` (no `fallaron`)
- **Imperativo para instrucciones** — `Ejecute el comando` (no `El usuario debe ejecutar el comando`)

### Formato en Markdown
- Código inline: `` `comando` ``, `` `variable` ``, `` `archivo.ext` ``
- Bloques de código: especificar lenguaje `` ```bash ``, `` ```json ``, `` ```yaml ``
- Listas: marcador `-` consistente; no mezclar `-`, `*`, `+`
- Enlaces: texto descriptivo `[documentación de la API](url)`, nunca `clic aquí`

---

## Language Detection Heuristics

If language is not explicit, detect by:
1. Majority of text content (stop words, articles, prepositions)
2. Code comments / variable names language
3. Existing document metadata (frontmatter `lang:` field)
4. File path (e.g., `docs/pt/`, `docs/en/`)

If ambiguous between PT-BR and PT-PT, default to PT-BR. If ambiguous between EN-US and EN-GB, default to EN-US.