# Readability Guide

Techniques for improving sentence structure, paragraph flow, and scannability. Apply per language rules from `language-rules.md`.

## Universal Principles

### Sentence Length
- **Target**: 15–25 words average per sentence
- **Max**: 35 words — split longer sentences
- **Vary** length for rhythm; avoid monotonous same-length sentences

### Paragraph Structure
- **One idea per paragraph** — 2–4 sentences typical
- **Topic sentence first** — state the point, then elaborate
- **Blank line between paragraphs** — never single line breaks

### Scannability Techniques
- **Meaningful headings** — every 2–4 paragraphs; H2/H3 describe content, not "More Info"
- **Bold key terms** — for concepts, tool names, parameters, file names (`**config.yaml**`, `**timeout**`)
- **Bullet lists** — for 3+ related items, steps, options, requirements
- **Numbered lists** — only when sequence/ranking matters
- **Tables** — for structured comparison data (parameters, versions, options)
- **Code blocks** — for commands, config, examples (always with language tag)

### Transition Management
- Use transitions sparingly: `However`, `Therefore`, `For example`, `In contrast`
- Avoid: `Moreover`, `Furthermore`, `Additionally` (often filler)
- Prefer implicit flow through structure over explicit transition words

---

## Portuguese (BR/PT)

### Estrutura de Frases
- **Ordem direta** (SVO): `O sistema processa os dados` — preferida
- **Evitar** inversões desnecessárias: `Processa os dados o sistema`
- **Subordinação controlada** — máx. 2 níveis de subordinação por frase
- **Coordenar em vez de subordinar** quando possível:
  - *Em vez de*: `Como o sistema valida a entrada, ele garante integridade`
  - *Prefira*: `O sistema valida a entrada e garante integridade`

### Parágrafos
- **Frase-tópico** abre o parágrafo
- **Desenvolvimento** em 1–3 frases
- **Frase de transição/fechamento** opcional
- **Tamanho**: 40–100 palavras típico

### Escaneabilidade
- **Negrito** para: comandos, arquivos, parâmetros, conceitos-chave, nomes de ferramentas
- **Listas com `-`** para itens paralelos (não misturar marcadores)
- **Listas numeradas** apenas para sequências ordenadas
- **Tabelas** para: opções de configuração, matrizes de compatibilidade, parâmetros de API
- **Blocos de código** sempre com linguagem: `` ```bash ``, `` ```json ``

### Conectivos Úteis (usar com moderação)
| Função | Conectivos |
|--------|------------|
| Adição | `Além disso`, `Também` |
| Contraste | `No entanto`, `Por outro lado`, `Contudo` |
| Causa/Consequência | `Por isso`, `Assim`, `Portanto` |
| Exemplificação | `Por exemplo`, `Como` |
| Sequência | `Primeiro`, `Depois`, `Finalmente` |

**Evite**: `Outrossim`, `Destarte`, `Pois`, `Visto que` (formal demais para docs técnicas)

---

## English (US/UK)

### Sentence Structure
- **Active voice, SVO order**: `The system processes data`
- **Front-load key information**: `The API returns an error when the token expires` (not `When the token expires, the API returns an error` — unless the condition is the focus)
- **Limit subordination** — max 2 dependent clauses per sentence
- **Prefer coordination over subordination**:
  - *Instead of*: `Since the system validates input, it ensures integrity`
  - *Prefer*: `The system validates input and ensures integrity`

### Paragraphs
- **Topic sentence first** — states the main point
- **Development** in 1–3 sentences
- **Transition/closing sentence** optional
- **Length**: 40–100 words typical

### Scannability
- **Bold** for: commands, files, parameters, key concepts, tool names
- **Bullet lists** (`-`) for parallel items (consistent marker)
- **Numbered lists** only for ordered sequences
- **Tables** for: config options, compatibility matrices, API parameters
- **Code blocks** always with language: `` ```bash ``, `` ```json ``

### Useful Transitions (Use Sparingly)
| Function | Transitions |
|----------|-------------|
| Addition | `Also`, `Additionally` |
| Contrast | `However`, `Conversely`, `On the other hand` |
| Cause/Effect | `Therefore`, `Thus`, `As a result` |
| Example | `For example`, `For instance` |
| Sequence | `First`, `Next`, `Finally` |

**Avoid**: `Moreover`, `Furthermore`, `In addition`, `Consequently` (often filler)

---

## Spanish (ES)

### Estructura de Oraciones
- **Orden directo (SVO)**: `El sistema procesa los datos` — preferido
- **Evitar** inversiones innecesarias: `Procesa los datos el sistema`
- **Subordinación controlada** — máx. 2 niveles por oración
- **Coordinar en vez de subordinar** cuando sea posible:
  - *En vez de*: `Como el sistema valida la entrada, garantiza integridad`
  - *Prefiera*: `El sistema valida la entrada y garantiza integridad`

### Párrafos
- **Oración temática** abre el párrafo
- **Desarrollo** en 1–3 oraciones
- **Oración de transición/cierre** opcional
- **Longitud**: 40–100 palabras típico

### Escaneabilidad
- **Negrita** para: comandos, archivos, parámetros, conceptos clave, nombres de herramientas
- **Listas con `-`** para ítems paralelos (no mezclar marcadores)
- **Listas numeradas** solo para secuencias ordenadas
- **Tablas** para: opciones de configuración, matrices de compatibilidad, parámetros de API
- **Bloques de código** siempre con lenguaje: `` ```bash ``, `` ```json ``

### Conectivos Útiles (usar con moderación)
| Función | Conectivos |
|---------|------------|
| Adición | `Además`, `También` |
| Contraste | `Sin embargo`, `Por otro lado`, `No obstante` |
| Causa/Consecuencia | `Por eso`, `Así`, `Por tanto` |
| Ejemplificación | `Por ejemplo`, `Como` |
| Secuencia | `Primero`, `Después`, `Finalmente` |

**Evite**: `Por añadidura`, `En consecuencia`, `Visto que` (demasiado formal para docs técnicas)

---

## Document-Type Adjustments

### README / Landing Pages
- **Shorter sentences** (10–20 words)
- **More bullets**, fewer paragraphs
- **Bold** every key feature/benefit
- **Demo/visual** references early

### Tutorials / How-to Guides
- **Imperative mood** for steps
- **Numbered lists** for sequential steps
- **Expected outcome** after each major step
- **Prerequisites** clearly separated

### Reference / API Docs
- **Terse, factual sentences**
- **Tables** for parameters, responses, errors
- **Code examples** for every endpoint/function
- **Minimal narrative** — structure for lookup

### Explanations / Conceptual Docs
- **Longer paragraphs** acceptable (80–150 words)
- **Transitions** more important for flow
- **Analogies/examples** to clarify abstract concepts
- **Cross-links** to related reference/tutorial pages

---

## Application Rules

1. **Measure first** — count words/sentences in problem areas
2. **Split, don't just shorten** — long sentences often hide multiple ideas
3. **Add structure** — headings, lists, tables beat prose for scanning
4. **Bold strategically** — 3–5 bold terms per screenful; over-bolding defeats purpose
5. **Preserve technical accuracy** — never simplify to the point of inaccuracy