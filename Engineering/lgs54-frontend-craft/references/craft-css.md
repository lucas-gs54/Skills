# Craft CSS — Architecture, Custom Properties, Fluid Systems, Containment

CSS that scales, performs, and maintains. No utility soup in components.

---

## Architecture Principles

### 1. Tokens as Custom Properties
```css
/* tokens.css — generated from design-tokens.ts */
:root {
  /* Color */
  --color-bg: #ffffff;
  --color-bg-muted: #f5f5f5;
  --color-bg-inverted: #0f0f1a;
  --color-fg: #0f0f1a;
  --color-fg-muted: #525252;
  --color-fg-inverted: #ffffff;
  --color-primary: #1a1a2e;
  --color-primary-hover: #16213e;
  --color-primary-active: #0f0f1a;
  --color-primary-fg: #ffffff;
  --color-secondary: #f5f5f5;
  --color-secondary-hover: #e5e5e5;
  --color-secondary-active: #d4d4d4;
  --color-secondary-fg: #0f0f1a;
  --color-danger: #dc2626;
  --color-danger-hover: #b91c1c;
  --color-danger-active: #991b1b;
  --color-danger-fg: #ffffff;
  --color-focus: #e94560;
  --color-border: #e5e5e5;
  --color-border-muted: #f5f5f5;
  --color-overlay: rgb(0 0 0 / 0.5);

  /* Spacing */
  --space-0: 0;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;

  /* Typography */
  --font-sans: system-ui, -apple-system, sans-serif;
  --font-mono: ui-monospace, SFMono-Regular, monospace;
  --font-brand: var(--font-sans);
  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
  --text-4xl: 2.25rem;
  --text-5xl: 3rem;
  --leading-tight: 1.1;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --tracking-tight: -0.02em;
  --tracking-normal: 0;
  --tracking-wide: 0.02em;

  /* Elevation */
  --elevation-0: none;
  --elevation-1: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --elevation-2: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --elevation-3: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --elevation-4: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);

  /* Motion */
  --motion-duration-instant: 0ms;
  --motion-duration-fast: 120ms;
  --motion-duration-base: 200ms;
  --motion-duration-slow: 350ms;
  --motion-duration-slower: 500ms;
  --motion-easing-linear: linear;
  --motion-easing-out: cubic-bezier(0.2, 0, 0, 1);
  --motion-easing-in: cubic-bezier(0.4, 0, 1, 1);
  --motion-easing-in-out: cubic-bezier(0.4, 0, 0.2, 1);
  --motion-easing-spring: cubic-bezier(0.34, 1.56, 0.64, 1);

  /* Border Radius */
  --radius-none: 0;
  --radius-sm: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
  --radius-2xl: 1rem;
  --radius-full: 9999px;

  /* Z-Index */
  --zIndex-base: 0;
  --zIndex-dropdown: 100;
  --zIndex-sticky: 200;
  --zIndex-header: 300;
  --zIndex-modal: 400;
  --zIndex-popover: 500;
  --zIndex-tooltip: 600;
  --zIndex-toast: 700;
}

/* Dark mode — token swap only */
[data-theme="dark"] {
  --color-bg: #0f0f1a;
  --color-bg-muted: #1a1a2e;
  --color-bg-inverted: #ffffff;
  --color-fg: #f5f5f5;
  --color-fg-muted: #a3a3a3;
  --color-fg-inverted: #0f0f1a;
  --color-primary: #818cf8;
  --color-primary-hover: #6366f1;
  --color-primary-active: #4f46e5;
  --color-secondary: #27272a;
  --color-secondary-hover: #3f3f46;
  --color-secondary-active: #52525b;
  --color-border: #3f3f46;
  --color-border-muted: #27272a;
  --color-focus: #f472b6;
}
```

---

### 2. Component Styles — Token Consumers Only

```css
/* components/button.css */
.button {
  /* Layout */
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  border-radius: var(--radius-md);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  font-weight: var(--font-weight-medium);
  line-height: var(--leading-normal);
  white-space: nowrap;

  /* Interaction */
  cursor: pointer;
  user-select: none;
  border: 1px solid transparent;
  transition:
    background-color var(--motion-duration-base) var(--motion-easing-out),
    border-color var(--motion-duration-base) var(--motion-easing-out),
    color var(--motion-duration-base) var(--motion-easing-out),
    box-shadow var(--motion-duration-base) var(--motion-easing-out),
    transform var(--motion-duration-fast) var(--motion-easing-out);

  /* Focus */
  outline: none;
}
.button:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
}

/* Variants — semantic, not visual */
.button--primary {
  background: var(--color-primary);
  color: var(--color-primary-fg);
}
.button--primary:hover { background: var(--color-primary-hover); }
.button--primary:active { background: var(--color-primary-active); }

.button--secondary {
  background: var(--color-secondary);
  color: var(--color-secondary-fg);
}
.button--secondary:hover { background: var(--color-secondary-hover); }
.button--secondary:active { background: var(--color-secondary-active); }

.button--danger {
  background: var(--color-danger);
  color: var(--color-danger-fg);
}
.button--danger:hover { background: var(--color-danger-hover); }
.button--danger:active { background: var(--color-danger-active); }

.button--ghost {
  background: transparent;
  color: var(--color-fg);
}
.button--ghost:hover { background: var(--color-bg-muted); }
.button--ghost:active { background: var(--color-border); }

/* States */
.button:disabled,
.button[aria-disabled="true"] {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}
.button[aria-busy="true"] { /* loading spinner via pseudo or child */ }

/* Sizes */
.button--sm { padding: var(--space-1) var(--space-3); font-size: var(--text-xs); }
.button--lg { padding: var(--space-3) var(--space-6); font-size: var(--text-base); }
```

---

### 3. Primitive Layout Components

```css
/* primitives/box.css */
.box { display: block; }
.box--inline { display: inline-flex; }
.box--flex { display: flex; }
.box--grid { display: grid; }

/* primitives/stack.css — vertical rhythm */
.stack {
  display: flex;
  flex-direction: column;
}
.stack > * + * { margin-top: var(--space-4); }
.stack--sm > * + * { margin-top: var(--space-2); }
.stack--lg > * + * { margin-top: var(--space-6); }
.stack--xl > * + * { margin-top: var(--space-8); }

/* primitives/inline.css — horizontal rhythm */
.inline {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
}
.inline > * + * { margin-left: var(--space-3); }
.inline--sm > * + * { margin-left: var(--space-1); }
.inline--lg > * + * { margin-left: var(--space-4); }

/* primitives/grid.css */
.grid {
  display: grid;
  gap: var(--space-4);
}
.grid--2 { grid-template-columns: repeat(2, 1fr); }
.grid--3 { grid-template-columns: repeat(3, 1fr); }
.grid--auto { grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); }

/* primitives/container.css */
.container {
  width: 100%;
  max-width: var(--container-xl);
  margin-inline: auto;
  padding-inline: var(--space-6);
}
@media (min-width: 1280px) {
  .container { padding-inline: var(--space-8); }
}
```

---

## Fluid Systems

### Fluid Typography — `clamp()`
```css
/* tokens.css additions */
:root {
  /* Fluid scale: min, preferred (viewport), max */
  --text-fluid-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
  --text-fluid-sm: clamp(0.875rem, 0.825rem + 0.25vw, 1rem);
  --text-fluid-base: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
  --text-fluid-lg: clamp(1.125rem, 1.05rem + 0.375vw, 1.25rem);
  --text-fluid-xl: clamp(1.25rem, 1.15rem + 0.5vw, 1.5rem);
  --text-fluid-2xl: clamp(1.5rem, 1.35rem + 0.75vw, 2rem);
  --text-fluid-3xl: clamp(1.875rem, 1.65rem + 1.125vw, 2.5rem);
  --text-fluid-4xl: clamp(2.25rem, 1.95rem + 1.5vw, 3rem);
  --text-fluid-5xl: clamp(3rem, 2.5rem + 2.5vw, 4rem);
}
```

### Fluid Spacing
```css
:root {
  --space-fluid-1: clamp(0.25rem, 0.2rem + 0.25vw, 0.5rem);
  --space-fluid-2: clamp(0.5rem, 0.4rem + 0.5vw, 1rem);
  --space-fluid-3: clamp(0.75rem, 0.6rem + 0.75vw, 1.25rem);
  --space-fluid-4: clamp(1rem, 0.85rem + 0.75vw, 1.5rem);
  --space-fluid-6: clamp(1.5rem, 1.25rem + 1.25vw, 2rem);
  --space-fluid-8: clamp(2rem, 1.75rem + 1.25vw, 2.5rem);
}
```

### Container Queries (Over Media Queries)
```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card { grid-template-columns: 1fr 1fr; }
  .card__media { aspect-ratio: 16/9; }
}

@container card (min-width: 600px) {
  .card__content { padding: var(--space-6); }
}
```

---

## Logical Properties — RTL Ready

```css
/* Instead of directional properties */
.box {
  /* margin-left → */
  margin-inline-start: var(--space-4);
  /* padding-right → */
  padding-inline-end: var(--space-2);
  /* border-left → */
  border-inline-start: 1px solid var(--color-border);
  /* text-align: left → */
  text-align: start;
  /* float: left → */
  float: inline-start;
  /* left: 0 → */
  inset-inline-start: 0;
  /* width → */
  inline-size: 100%;
  /* height → */
  block-size: auto;
  /* min-width → */
  min-inline-size: 200px;
  /* max-height → */
  max-block-size: 400px;
}
```

---

## Containment — Performance Isolation

```css
/* Component root — isolate layout/style/paint */
.component {
  contain: layout style paint;
  /* size containment if fixed dimensions */
}

/* Animated elements only */
.animated {
  will-change: transform, opacity;
}
/* Remove after animation */
.animated:not(.animating) { will-change: auto; }

/* Scroll containers */
.scrollable {
  contain: layout paint;
  overflow: auto;
  overscroll-behavior: contain;
}

/* Third-party embeds */
.embed {
  contain: layout style paint size;
}
```

---

## Cascade Layers — Architecture Control

```css
/* layers.css — loaded first */
@layer reset, tokens, primitives, components, patterns, utilities, overrides;

/* 1. Reset — normalize */
@layer reset {
  *, *::before, *::after { box-sizing: border-box; }
  html { -webkit-text-size-adjust: 100%; }
  body { margin: 0; line-height: 1.5; }
  img, video { max-width: 100%; height: auto; }
}

/* 2. Tokens — custom properties */
@layer tokens {
  @import 'tokens.css';
}

/* 3. Primitives — Box, Stack, Inline, Grid, Container */
@layer primitives {
  @import 'primitives/box.css';
  @import 'primitives/stack.css';
  @import 'primitives/inline.css';
  @import 'primitives/grid.css';
  @import 'primitives/container.css';
}

/* 4. Components — Button, Input, Card, etc. */
@layer components {
  @import 'components/button.css';
  @import 'components/input.css';
  @import 'components/card.css';
}

/* 5. Patterns — composed components */
@layer patterns {
  @import 'patterns/form-field.css';
  @import 'patterns/dialog.css';
  @import 'patterns/table.css';
}

/* 6. Utilities — only token-mapped, no arbitrary values */
@layer utilities {
  .sr-only {
    position: absolute;
    width: 1px; height: 1px;
    padding: 0; margin: -1px;
    overflow: hidden; clip: rect(0, 0, 0, 0);
    white-space: nowrap; border: 0;
  }
  .focus-ring:focus-visible {
    outline: 2px solid var(--color-focus);
    outline-offset: 2px;
  }
  .truncate {
    overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
  }
}

/* 7. Overrides — page-specific, last resort */
@layer overrides {
  .home-hero { /* ... */ }
}
```

---

## Critical CSS — Inline First Paint

```html
<head>
  <!-- Inline critical CSS for above-fold -->
  <style>
    /* tokens + primitives + above-fold components only */
    :root { --color-bg: #fff; --color-fg: #0f0f1a; ... }
    .container { ... }
    .stack { ... }
    .button { ... }
    .header { ... }
    .hero { ... }
  </style>

  <!-- Non-critical deferred -->
  <link rel="preload" as="style" href="/styles.full.css" onload="this.rel='stylesheet'">
  <noscript><link rel="stylesheet" href="/styles.full.css"></noscript>
</head>
```

---

## Font Loading — Zero CLS

```css
/* tokens.css */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-weight: 100 900;
  font-display: optional; /* or swap + size-adjust */
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}

/* Fallback font metrics matched */
@font-face {
  font-family: 'Inter Fallback';
  src: local('Arial');
  size-adjust: 100.06%;
  ascent-override: 90%;
  descent-override: 25%;
  line-gap-override: 0%;
}

/* Usage */
:root { --font-sans: 'Inter', 'Inter Fallback', system-ui, sans-serif; }
```

```html
<!-- Preload critical font -->
<link rel="preload" as="font" type="font/woff2" crossorigin href="/fonts/inter-var.woff2">
```

---

## Print Styles

```css
@media print {
  .no-print { display: none !important; }
  .print-only { display: block !important; }

  a::after { content: " (" attr(href) ")"; font-size: 0.8em; color: #666; }
  a[href^="#"]::after,
  a[href^="javascript:"]::after { content: ""; }

  .page-break { page-break-before: always; }
  .avoid-break { page-break-inside: avoid; }

  * { background: transparent !important; color: #000 !important; box-shadow: none !important; }
}
```

---

## Anti-Patterns to Reject

| Anti-Pattern | Why | Fix |
|--------------|-----|-----|
| `@apply` in components | Hides dependencies, breaks token traceability | Token variables directly |
| `tailwind.config` arbitrary values | Bypasses design system | Extend theme properly or use tokens |
| `transition: all` | Layout thrashing | Explicit property list |
| `will-change` on static | GPU memory waste | Only on animating elements |
| `z-index: 9999` | Stacking chaos | Layer tokens + portals |
| `backdrop-filter` everywhere | Mobile GPU kill | Solid bg + elevation |
| Media queries for components | Brittle, not portable | Container queries |
| `px`/`rem` in components | Not themeable | Token variables |
| `!important` (non-utility) | Cascade breakdown | Layer order / specificity |
| `@media (prefers-color-scheme)` in components | SSR flash, duplication | `[data-theme]` on root |