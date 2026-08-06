# lgs54-frontend-craft

**Frontend com craftsmanship profissional** — HTML semântico, acessibilidade real, design tokens, motion purposeful, CSS performático, componentes tipados e composáveis. Não é template genérico de IA.

## Quando Usar

- Buildando **UI nova** do zero com padrão profissional
- Revisando/refatorando frontend existente — **detecta e corrige anti-patterns de IA**
- Precisa de **design system disciplinado** (tokens, não magic numbers)
- Quer **acessibilidade WCAG 2.2 AA** baseline real, não checklist vazio
- Performance como feature: bundle budgets, paint metrics, INP < 100ms

## Contextos Suportados

- **Framework**: React, Vue, Svelte, Solid, Astro, Next.js, Remix, Vite, vanilla
- **Styling**: CSS Modules, Tailwind, styled-components, Emotion, vanilla CSS, PostCSS, UnoCSS
- **Type system**: TypeScript (preferido), JSDoc, Flow
- **Testing**: Vitest, Jest, Playwright, Cypress, Testing Library
- **Output**: SPA, SSR, SSG, islands, edge

## 8 Princípios Core

1. **Restraint Over Decoration** — sem gradients neon, glows, blurs decorativos
2. **Semantic First, Visual Second** — HTML faz sentido sem CSS
3. **Tokens Are Law** — zero magic numbers; theming = token swap
4. **Motion Has Meaning** — animação responde: de onde veio? pra onde vai? o que mudou?
5. **Accessibility Is Not Optional** — WCAG 2.2 AA, keyboard, focus, ARIA só quando nativo falha
6. **Performance Is UX** — bundle budgets, critical CSS, font loading, CLS = 0
7. **Depth Through Structure** — hierarquia via tipografia, spacing, elevation, contrast — não glow/blur
8. **Consistency Through Composition** — primitives (`Box`, `Text`, `Stack`) compõem patterns; nada de god components

## Workflow (7 Steps)

1. **Requirements & Constraints** — brand, a11y target, perf budget, browser support, existing tokens
2. **Analyze Codebase** — detecta anti-patterns (`references/ai-frontend-antipatterns.md`)
3. **Define Design Tokens** — color, spacing, typography, elevation, motion, breakpoints, radius
4. **Architect Components** — primitives → composites → patterns; variant props semânticos apenas
5. **Implement with Craft** — semantic HTML, accessible interactions, performant CSS, typed components
6. **Validate** — automated (TS strict, ESLint, Stylelint, axe, bundle, Lighthouse) + manual checklist
7. **Deliver Documented** — props table, usage examples, a11y notes, motion behavior, Storybook stories

## Anti-Patterns que Elimina (Resumo)

| Anti-Pattern IA | Correção |
|-----------------|----------|
| Neon cyan/magenta gradients | Brand colors como semantic tokens |
| `whileHover={{ scale: 1.05 }}` em tudo | Subtle elevation/color shift |
| `backdrop-blur-lg` em todo modal/card | Solid bg + elevation token |
| `box-shadow` glow colorido | Layered neutral shadows via tokens |
| `div` + `flex` × 20 (sem landmarks) | `<header>`, `<nav>`, `<main>`, primitives |
| `transition-all duration-300` | Explicit property + motion token |
| `z-index: 9999` chaos | Elevation tokens + portal strategy |
| Icon-only button sem label | `aria-label` ou visible text + visually-hidden |
| `<img>` sem `width`/`height` | Explicit dimensions + aspect-ratio |
| `outline: none` sem replacement | Visible focus ring via token |

## Exemplo de Trigger

> "Builda esse dashboard React com padrão profissional"
> "Revisa esse frontend — tem cara de template IA genérico"
> "Cria design tokens + primitives pro projeto"
> "Faz esse componente acessível de verdade (WCAG AA)"

## Referências

- `references/ai-frontend-antipatterns.md` — Catálogo completo de anti-patterns IA + fixes
- `references/design-tokens.md` — Spec, estrutura, naming, theming strategy
- `references/semantic-html.md` — Element selection, landmarks, ARIA patterns, a11y rules
- `references/craft-css.md` — CSS architecture, custom properties, fluid systems, containment, logical properties
- `references/purposeful-motion.md` — Motion principles, tokenized durations/easings, reduced-motion
- `references/typed-components.md` — TS patterns, polymorphic components, variant discrimination, testing types
- `references/craft-checklist.md` — Checklist aplicado em cada step