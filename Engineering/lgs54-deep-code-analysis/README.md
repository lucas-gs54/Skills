# lgs54-deep-code-analysis

**Análise profunda e sistemática de código** — encontra bugs que reviews/linters perdem: off-by-one, race conditions, memory leaks, invariant violations, architectural flaws, security vulnerabilities.

## Quando Usar

- Precisa validar **prontidão para produção** de código crítico
- Debugando **issues elusivas** (heisenbugs, data corruption silenciosa, flakiness)
- Revisando **paths críticos** (auth, payments, consensus, kernel, FFI boundaries)
- Auditoria de **segurança** ou **performance** profunda

## Modos de Análise

| Modo | Foco |
|------|------|
| `full` | Todas as 6 passes (padrão) |
| `correctness` | Logic bugs, invariants, edge cases, UB |
| `concurrency` | Data races, deadlocks, lock ordering, atomicity |
| `performance` | Complexidade algorítmica, alocações, cache, async overhead |
| `security` | Injection, auth bypass, info leakage, crypto, supply chain |
| `architecture` | Coupling, circular deps, layer violations, god objects |

Podem ser combinados: `"correctness + concurrency"`

## 6 Passes Obrigatórios (Não Pula)

1. **Static Structure** — syntax, types, unused/dead code, shadowing, visibility leaks
2. **Control & Data Flow** — uninitialized reads, null deref, exception paths, loop invariants, async yield points
3. **Semantic Correctness** — pre/post conditions, boundaries, off-by-one, integer/float semantics, state machines
4. **Concurrency & Memory** — data races, deadlocks, leaks, use-after-free, lifetime violations, goroutine/task leaks
5. **Performance & Security** — complexity, allocation hot paths, cache, sync overhead, input validation, injection, crypto
6. **Architectural Integrity** — coupling metrics, cycles, layer violations, god objects, hidden global state, testability

## Output: Annotated Report

Cada finding inclui:
- **Location** (file:line)
- **Root cause** (exato path de execução)
- **Execution trace** (passo a passo)
- **Fix suggestion** + **Regression test** exato
- **Severity** (Critical/High/Medium/Low) + **Confidence** + **Blast radius** + **Fix effort**

## Exemplo de Trigger

> "Analisa esse módulo de pagamentos pra production readiness"
> "Faz deep analysis modo security nesse codebase Rust"
> "Debuga esse race condition intermitente no connection pool"
> "Review arquitetural: detecta god objects e coupling excessivo"

## Linguagens Suportadas

Qualquer linguagem — adapta checks para semântica específica:
- **Rust**: borrow checker, move semantics, drop order, Send/Sync
- **TypeScript**: structural typing, `any` leakage, narrowing
- **Go**: escape analysis, goroutine leaks, channel patterns
- **Python**: GIL, mutabilidade, async, type hints
- **C++**: UB, object lifetime, template instantiation

## Referências

- `references/analysis-passes.md` — Algoritmos detalhados, data structures, pseudocode
- `references/language-semantics.md` — Regras por linguagem (Rust, TS, Go, Python, C++)
- `references/bug-patterns.md` — Catálogo de deep bug patterns + detection strategies
- `references/severity-rubric.md` — Scoring guidelines, blast radius, fix effort calibration
- `references/architectural-metrics.md` — Coupling, cohesion, cycle detection, layering rules
- `references/report-template.md` — Output format, annotation style, JSON schema