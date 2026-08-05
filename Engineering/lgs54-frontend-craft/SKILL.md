---
name: lgs54-frontend-craft
description: Craft production-grade frontend UIs that are unique, clean, performant, and purposeful — not generic AI templates. Enforces visual hierarchy, semantic HTML, accessible interactions, restrained design systems, and deliberate motion. Use when building or reviewing frontend code (React, Vue, Svelte, vanilla, etc.) that must look and feel professionally crafted, not AI-generated boilerplate.
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: frontend-craftsmanship
---

# LGS54 Frontend Craft

## Purpose

This skill produces frontend implementations that demonstrate **intentional craftsmanship** — every pixel, interaction, and line of code serves a purpose. It rejects the hallmarks of generic AI output: neon gradients, meaningless animations, chaotic spacing, inaccessible markup, and template-looking components.

The skill covers:
- **Design system discipline** — tokens, not magic numbers; restraint over decoration
- **Semantic, accessible HTML** — structure first, style second
- **Purposeful motion** — motion that guides, not distracts
- **Performance as a feature** — bundle size, paint metrics, interaction latency
- **Visual depth without noise** — layering, elevation, contrast — not glow/blur spam
- **Component architecture** — composable, typed, testable, documented

## Language

Communicate with the user in whatever language they are using. Code comments, documentation, and generated content follow the project's language. Technical terms (CSS properties, ARIA roles, HTML elements) remain in English.

## Supported Contexts

The skill applies to any frontend stack. Identify the context before executing — if ambiguous, ask.

- **Framework**: React, Vue, Svelte, Solid, Astro, Next.js, Remix, Vite, vanilla
- **Styling**: CSS Modules, Tailwind, styled-components, Emotion, vanilla CSS, PostCSS, UnoCSS
- **Type system**: TypeScript (preferred), JSDoc, Flow
- **Testing**: Vitest, Jest, Playwright, Cypress, Testing Library
- **Output target**: SPA, SSR, SSG, islands, edge

## Mandatory Operating Rule

The skill MUST always follow this workflow:

1. **Understand Requirements & Constraints** — Extract design intent, brand guidelines, accessibility targets, performance budgets, browser support, existing design tokens.
2. **Analyze Existing Codebase** (if applicable) — Detect patterns, debt, token usage, component library, anti-patterns from `references/ai-frontend-antipatterns.md`.
3. **Define Design Tokens & Primitives** — Colors, spacing, typography, elevation, motion, breakpoints as typed tokens (`references/design-tokens.md`).
4. **Architect Component Hierarchy** — Atomic/semantic breakdown; composition over configuration; strict props interfaces.
5. **Implement with Craft** — Write semantic HTML, accessible interactions, performant CSS, typed components. Apply `references/craft-checklist.md` at each step.
6. **Validate Against Standards** — Run Quality Checklist + automated checks (a11y, lint, typecheck, bundle size).
7. **Deliver Documented Components** — Component API docs, usage examples, Storybook/Histoire stories if applicable.

Do not skip validation.

## Core Principles

### 1. Restraint Over Decoration
No decorative gradients, glows, blurs, or animations unless they communicate state, hierarchy, or brand. "Clean" means **purposeful minimalism**, not emptiness.

### 2. Semantic First, Visual Second
HTML structure must make sense without CSS. Screen readers, search engines, and `reader` modes consume semantics — not your Tailwind classes.

### 3. Tokens Are Law
Zero magic numbers in styles. Every color, space, size, duration, easing comes from a defined token. Theming = token swap, not find/replace.

### 4. Motion Has Meaning
Animation answers: *Where did it come from? Where is it going? What changed?* No `transition: all 0.3s ease`. Specify property, duration, easing per interaction.

### 5. Accessibility Is Not Optional
WCAG 2.2 AA baseline. Keyboard navigation, focus management, ARIA only when native HTML fails, color contrast, reduced motion, screen reader testing.

### 6. Performance Is UX
Bundle budgets, critical CSS, font loading strategy, image optimization, interaction latency < 100ms, no layout shift. Measure, don't guess.

### 7. Depth Through Structure, Not Effects
Visual hierarchy via: typographic scale, spacing rhythm, elevation layers, contrast ratios, alignment grids. Not: `box-shadow: 0 0 40px rgba(0,255,255,0.5)`.

### 8. Consistency Through Composition
Shared primitives (`Box`, `Text`, `Stack`, `Inline`, `Grid`) compose into patterns. No "god components" with 20 props. Variant props only for semantic variations (size, tone, state).

## Workflow Steps

### Step 1: Understand Requirements & Constraints
Gather or clarify:
- Brand/design system (existing or new) — colors, typography, voice
- Accessibility target (WCAG level, legal requirements)
- Performance budget (bundle size, LCP, INP, CLS thresholds)
- Browser/device support matrix
- Existing codebase patterns, tokens, component library
- Design files (Figma, etc.) — if none, define tokens first

**If missing critical info → ask. Do not assume.**

### Step 2: Analyze Existing Codebase (if applicable)
Run detection for anti-patterns from `references/ai-frontend-antipatterns.md`:
- Generic neon/cyberpunk color schemes
- Meaningless `framer-motion` `whileHover` scale/rotate on everything
- `div` soup — missing landmarks, headings, lists
- Magic numbers in CSS/JSX
- `className="flex items-center justify-center"` on every wrapper
- `transition-all duration-300` on interactive elements
- No focus styles / `outline-none` without replacement
- Images without `width`/`height` / `loading="lazy"` / proper formats
- Icon-only buttons without `aria-label`
- `z-index: 9999` stacking chaos

### Step 3: Define Design Tokens & Primitives
Create/extend typed token system per `references/design-tokens.md`:
- **Color**: semantic aliases (`color-bg`, `color-fg`, `color-primary`, `color-danger`, `color-focus`) + scales
- **Spacing**: base unit (4px/8px) + scale (`space-1`…`space-12`)
- **Typography**: font families, size scale, line heights, letter spacing, font weights
- **Elevation**: shadow tokens (`elevation-1`…`elevation-4`) — no arbitrary `box-shadow`
- **Motion**: duration tokens (`motion-fast`, `motion-base`, `motion-slow`), easing tokens (`ease-out`, `ease-in-out`, `ease-spring`)
- **Breakpoints**: named, mobile-first (`bp-sm`, `bp-md`, `bp-lg`, `bp-xl`)
- **Border radius**: `radius-none`, `radius-sm`, `radius-md`, `radius-lg`, `radius-full`

**Tokens live in code** (TypeScript/JS/JSON/CSS) — not Figma only.

### Step 4: Architect Component Hierarchy
Break UI into:
- **Primitives** — `Box`, `Text`, `Heading`, `Image`, `Button`, `Link`, `Input`, `Label`, `Separator`, `VisuallyHidden`
- **Composites** — `Card`, `Dialog`, `Dropdown`, `Tabs`, `Table`, `FormField`, `Navigation`, `Toast`
- **Patterns** — `PageLayout`, `DataList`, `SettingsPanel`, `AuthFlow`, `Dashboard`

Rules:
- Primitives accept only token-based props (`space`, `color`, `radius`, `elevation`)
- Composites compose primitives; no duplicate styles
- Variant props = semantic only (`tone: 'primary' | 'secondary' | 'danger'`, `size: 'sm' | 'md' | 'lg'`)
- Compound components for complex state (`<Select><Select.Trigger/><Select.Options/></Select>`)

### Step 5: Implement with Craft
Per component, apply:

**HTML & Accessibility** (`references/semantic-html.md`):
- Correct landmarks (`<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`, `<section>`)
- Heading hierarchy (h1→h2→h3, no skips)
- Lists for repeated items (`<ul>`/`<ol>`/`<dl>`)
- Buttons for actions, links for navigation
- `aria-*` only when native element insufficient
- Focus visible, logical tab order, focus trapping in modals
- `prefers-reduced-motion` respected

**CSS & Styling** (`references/craft-css.md`):
- CSS custom properties for tokens (or Tailwind config mapping 1:1)
- No `@apply` soup — use primitives
- Fluid typography (`clamp()`), fluid spacing
- Container queries over media queries where possible
- Logical properties (`margin-inline-start`, `padding-block`)
- `will-change` only on animated elements, removed after
- `contain: layout style` on isolated components

**Interactions & Motion** (`references/purposeful-motion.md`):
- Enter/exit animations for mounted/unmounted content
- State transitions (hover, focus, active, disabled, loading)
- Micro-feedback (button press, input validation, toast appear)
- No animation on layout-affecting properties unless `contain` used
- Reduced motion: instant transitions, no parallax, no auto-play

**TypeScript** (`references/typed-components.md`):
- Strict props interfaces — no `any`, no `React.ComponentPropsWithoutRef` abuse
- `forwardRef` for primitives
- Discriminated unions for variants
- Generic slots for polymorphic components (`asChild` pattern)

### Step 6: Validate Against Standards
Run **automated**:
- TypeScript strict mode — zero errors
- ESLint + `@typescript-eslint` — zero warnings
- Stylelint — zero warnings
- `@axe-core/playwright` or `jest-axe` — zero violations
- Bundle analyzer — within budget
- Lighthouse CI — performance ≥ 90, a11y ≥ 95, best practices ≥ 90

Run **manual** (Quality Checklist):

### Step 7: Deliver Documented Components
Per component:
- Props table (auto-generated from TypeScript)
- Usage examples (realistic, not `lorem ipsum`)
- Accessibility notes (keyboard, screen reader, focus)
- Motion behavior (what animates, when, reduced-motion fallback)
- Composition examples (how to extend/customize)
- Storybook/Histoire stories for all states

## Quality Checklist

Before delivering, verify:

### Design System & Tokens
- [ ] Zero magic numbers in styles — all values reference tokens
- [ ] Color palette has semantic aliases, not just `blue-500`
- [ ] Spacing scale used consistently (no `px` values in components)
- [ ] Typography scale defined; no arbitrary `font-size`
- [ ] Elevation/shadow system defined; no ad-hoc `box-shadow`
- [ ] Motion tokens defined; durations/easings not hardcoded
- [ ] Dark mode works via token swap (no `[class*="dark"]` hacks in components)

### Semantic HTML & Accessibility
- [ ] Page has single `<h1>`, logical heading hierarchy
- [ ] Landmarks present (`<main>`, `<nav>`, `<header>`, `<footer>`)
- [ ] All interactive elements keyboard-accessible
- [ ] Focus styles visible and distinct (not just `outline: none`)
- [ ] Focus order matches visual order
- [ ] `aria-label`/`aria-labelledby` on icon-only controls
- [ ] Form inputs have associated `<label>` (explicit or implicit)
- [ ] Error messages linked via `aria-describedby`
- [ ] Live regions for dynamic updates (toasts, validation)
- [ ] `prefers-reduced-motion` respected — no motion if enabled
- [ ] Color contrast ≥ 4.5:1 (AA) for text, ≥ 3:1 for UI elements
- [ ] Touch targets ≥ 44×44px (or 48×48px preferred)

### CSS & Performance
- [ ] No `transition: all` — explicit property list
- [ ] No `will-change` on static elements
- [ ] Critical CSS inlined / loaded early
- [ ] Fonts: `preload`, `font-display: swap`, subsetted
- [ ] Images: proper formats (AVIF/WebP), `width`/`height`, `loading="lazy"` (below fold)
- [ ] No layout shift (CLS = 0) — reserved space for images/ads/fonts
- [ ] Bundle size within budget (initial JS < 170kb gzipped typical)
- [ ] Code-split by route/feature; no waterfall requests

### Component Architecture
- [ ] Primitives composable; no style duplication
- [ ] Variant props semantic, not visual (`tone` not `bgColor`)
- [ ] Compound components for complex state
- [ ] Polymorphic `as`/`asChild` pattern for primitives
- [ ] TypeScript strict — no `any`, proper generics
- [ ] Tests: unit (logic), integration (interaction), a11y (automated)

### Anti-Pattern Compliance
- [ ] No neon/cyberpunk gradients unless brand-mandated
- [ ] No `scale(1.05)`/`rotate(3deg)` on hover for non-draggable items
- [ ] No `backdrop-filter: blur()` on every card/modal
- [ ] No `box-shadow` with colored glow (unless brand elevation)
- [ ] No `div` wrappers for styling only — use primitives
- [ ] No `className` soup — semantic props map to tokens
- [ ] No `z-index` stacking context chaos — use elevation tokens
- [ ] No `outline: none` without visible focus alternative

## Reference Files

- `references/ai-frontend-antipatterns.md` — Comprehensive catalog of AI-generated frontend anti-patterns with fixes
- `references/design-tokens.md` — Token specification, structure, naming, theming strategy
- `references/semantic-html.md` — HTML element selection, landmarks, ARIA patterns, accessibility rules
- `references/craft-css.md` — CSS architecture, custom properties, fluid systems, containment, logical properties
- `references/purposeful-motion.md` — Motion principles, tokenized durations/easings, reduced-motion, animation patterns
- `references/typed-components.md` — TypeScript patterns, polymorphic components, variant discrimination, testing types

## Anti-Patterns (Summary)

**Never do these — they scream "AI generated":**

| Anti-Pattern | Why It Fails | Correct Approach |
|--------------|--------------|------------------|
| Neon cyan/magenta gradients on dark bg | Generic "tech" aesthetic, no brand | Brand colors as semantic tokens |
| `whileHover={{ scale: 1.05 }}` on cards/buttons | Meaningless, distracts, fails reduced-motion | Subtle elevation change or color shift |
| `backdrop-blur-lg` on every modal/card | Performance cost, visual noise | Solid bg + elevation token |
| `box-shadow: 0 0 30px rgba(99,102,241,0.5)` | "Glow" ≠ depth; accessibility risk | Layered neutral shadows via tokens |
| `div` + `flex` + `items-center` × 20 | No semantics, a11y broken | `<header>`, `<nav>`, `<main>`, primitives |
| `transition-all duration-300` | Animates layout → jank; no intent | Explicit property + motion token |
| `z-index: 9999` / `50` / `100` chaos | Unmaintainable stacking | Elevation tokens + portal strategy |
| Icon-only `<button>` no label | Screen reader: "button" | `aria-label` or visible text + `visually-hidden` |
| `<img>` no `width`/`height` | Layout shift (CLS) | Explicit dimensions + aspect-ratio |
| `outline: none` no replacement | Keyboard users lost | Visible focus ring via token |
| Magic numbers: `mt-7`, `text-[17px]`, `w-[342px]` | Unmaintainable, breaks tokens | Token references only |
| `className="text-gray-900 dark:text-gray-100"` in components | Hardcoded, not themeable | `color-fg` / `color-muted` tokens |

## Completion Criteria

A task is complete only when:
- Mandatory Workflow (Steps 1–7) followed
- All Quality Checklist items pass
- Zero anti-patterns from `references/ai-frontend-antipatterns.md` present
- Components documented with props, a11y, motion, composition
- Automated checks (type, lint, a11y, bundle) pass in CI