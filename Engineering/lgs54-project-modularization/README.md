# lgs54-project-modularization

**Reorganiza codebases bagunçados em projetos modulares limpos** — survey → plan → implement seguro, preservando **todo comportamento**. Complementa `lgs54-deep-code-analysis` (diagnostica) agindo na estrutura.

## Quando Usar

- Projeto **espalhado**, sem boundaries claros de módulo
- **Mixed responsibilities**, god modules, naming/layout inconsistente
- **High coupling**, deps emaranhadas, circular dependencies
- Precisa **reestruturar** seguindo convenções da linguagem/framework
- Migração segura: **nenhum arquivo perdido**, behavior invariável

## 3 Modos de Operação

| Modo | O Que Faz |
|------|-----------|
| `plan` | Survey + entrega plano de reestruturação **sem mudar arquivos** |
| `implement` | Executa plano **já aprovado** anteriormente |
| `full` | Survey → plano → **aguarda aprovação explícita** → implementa |

## 7 Steps Obrigatórios

1. **Identify Mode & Scope** — mode, language, entry points, critical modules, git commit baseline
2. **Survey Project** — indexa estrutura atual, lista problemas org (scattered code, mixed responsibilities, naming inconsistente, high coupling)
3. **Design Target Structure** — 1 responsibility/module, clean public interface, minimal deps, layout per language conventions
4. **Build Restructuring Plan** — new layout, splits/merges com rationale, dependency rewiring, safe migration order
5. **Await Approval** — `plan` para aqui; `full` pede aprovação; `implement` confirma plano aprovado
6. **Implement Safely** — small verifiable steps, before/after inventory tracked, buildable/testable after each step
7. **Verify & Report** — confirma behavior preservation (build, tests, spot checks), entrega report

## Princípios Core

- **Behavior Is Inviolable** — runtime behavior, business logic, contracts **nunca mudam** sem aprovação
- **No File Left Behind** — todo arquivo movido/renomeado/mergeado/deletado tracked em inventory
- **Small Steps Over Big Bangs** — minimal, verifiable steps; big rewrites = anti-pattern
- **Best Practices, Not Templates** — convenções da linguagem/framework, não estrutura genérica
- **Context Is King** — julga organização por coupling, cohesion, naming — não estética
- **Zero Assumption Policy** — scope, target, approval ambíguo? **Pergunta. Nunca assume.**

## Output: Report

- Before/after inventory completo
- Module splits/merges com rationale
- Dependency graph before/after
- Verification results (build, tests pass)
- Migration log step-by-step

## Exemplo de Trigger

> "Esse projeto tá uma bagunça, modulariza pra mim (mode: full)"
> "Só quero o plano de reestruturação, não mexa nos arquivos (mode: plan)"
> "Já aprovei o plano, implementa agora (mode: implement)"
> "Reorganiza esse monorepo seguindo convenções Rust/Go/TS"

## Referências

- `references/modularization-rules.md` — Organizational rules: structure, naming, responsibilities, dependencies, per-language conventions
- `references/migration-workflow.md` — Safe migration: inventory format, step sequencing, verification
- `references/report-template.md` — Delivery output format