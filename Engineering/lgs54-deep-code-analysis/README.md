# lgs54-deep-code-analysis

**Análise profunda de código** — encontra bugs que reviews/linters perdem: off-by-one, race conditions, memory leaks, invariant violations, architectural flaws, security vulns.

## Quando Usar

- Validar prontidão para produção de código crítico
- Debugar issues elusivas (heisenbugs, data corruption, flakiness)
- Revisar paths críticos (auth, payments, consensus, kernel, FFI)
- Auditoria de segurança ou performance profunda

## Modos

`full` · `correctness` · `concurrency` · `performance` · `security` · `architecture` (combináveis)

## Detalhes

Veja [`SKILL.md`](SKILL.md) para 6 passes obrigatórios, output annotated report, linguagens suportadas e referências.