# Design Tokens — Specification & Architecture

Single source of truth for every visual decision. Tokens live in code, not design tools.

---

## Token Categories

### 1. Color — Semantic Aliases Required

**Raw palette** (generated once, rarely touched):
```ts
// tokens/color-palette.ts
export const colorPalette = {
  // Brand — 10-step scale per hue
  indigo: { 50: '#eef2ff', 100: '#e0e7ff', ..., 900: '#1e1b4b', 950: '#0f0f2d' },
  rose:   { 50: '#fff1f2', 100: '#ffe4e6', ..., 900: '#881337', 950: '#4c0519' },
  // Neutrals
  slate:  { 50: '#f8fafc', ..., 900: '#0f172a', 950: '#020617' },
  // Semantic states
  success: { 50: '#f0fdf4', 500: '#22c55e', 600: '#16a34a', 700: '#15803d' },
  warning: { 50: '#fffbeb', 500: '#f59e0b', 600: '#d97706', 700: '#b45309' },
  danger:  { 50: '#fef2f2', 500: '#ef4444', 600: '#dc2626', 700: '#b91c1c' },
} as const;
```

**Semantic aliases** (what components consume):
```ts
// tokens/color.ts
import { colorPalette } from './color-palette';

export const color = {
  // Backgrounds
  bg: {
    DEFAULT: colorPalette.slate[50],
    muted: colorPalette.slate[100],
    inverted: colorPalette.slate[900],
    brand: colorPalette.indigo[600],
  },
  // Foregrounds (text)
  fg: {
    DEFAULT: colorPalette.slate[900],
    muted: colorPalette.slate[500],
    inverted: colorPalette.slate[50],
    brand: colorPalette.indigo[600],
    link: colorPalette.indigo[600],
  },
  // Borders
  border: {
    DEFAULT: colorPalette.slate[200],
    muted: colorPalette.slate[100],
    focus: colorPalette.indigo[500],
    error: colorPalette.danger[500],
  },
  // Interactive
  primary: {
    DEFAULT: colorPalette.indigo[600],
    hover: colorPalette.indigo[700],
    active: colorPalette.indigo[800],
    fg: colorPalette.slate[50],
  },
  secondary: {
    DEFAULT: colorPalette.slate[100],
    hover: colorPalette.slate[200],
    active: colorPalette.slate[300],
    fg: colorPalette.slate[900],
  },
  danger: {
    DEFAULT: colorPalette.danger[600],
    hover: colorPalette.danger[700],
    active: colorPalette.danger[800],
    fg: colorPalette.slate[50],
  },
  ghost: {
    DEFAULT: 'transparent',
    hover: colorPalette.slate[100],
    active: colorPalette.slate[200],
    fg: colorPalette.slate[700],
  },
  // Focus — single token, used everywhere
  focus: colorPalette.indigo[500],
  // Overlay
  overlay: 'rgb(0 0 0 / 0.5)',
} as const;
```

**Dark mode** = token swap, not component changes:
```ts
// tokens/color-dark.ts
export const colorDark = {
  bg: {
    DEFAULT: colorPalette.slate[950],
    muted: colorPalette.slate[900],
    inverted: colorPalette.slate[50],
    brand: colorPalette.indigo[500],
  },
  fg: {
    DEFAULT: colorPalette.slate[50],
    muted: colorPalette.slate[400],
    inverted: colorPalette.slate[900],
    brand: colorPalette.indigo[400],
    link: colorPalette.indigo[400],
  },
  // ... rest swapped
} as const;
```

---

### 2. Spacing — Base Unit + Scale

```ts
// tokens/spacing.ts
const BASE = 4; // px

export const space = {
  0: '0',
  1: `${BASE * 1}px`,   // 4px
  2: `${BASE * 2}px`,   // 8px
  3: `${BASE * 3}px`,   // 12px
  4: `${BASE * 4}px`,   // 16px
  5: `${BASE * 5}px`,   // 20px
  6: `${BASE * 6}px`,   // 24px
  8: `${BASE * 8}px`,   // 32px
  10: `${BASE * 10}px`, // 40px
  12: `${BASE * 12}px`, // 48px
  16: `${BASE * 16}px`, // 64px
  20: `${BASE * 20}px`, // 80px
  24: `${BASE * 24}px`, // 96px
} as const;

// Semantic aliases for components
export const spaceSemantic = {
  inline: space[1],    // 4px — between inline elements
  stack: space[3],     // 12px — between block elements
  section: space[6],   // 24px — between sections
  page: space[8],      // 32px — page margins
} as const;
```

**Usage rule**: Components reference `spaceSemantic` or `space[N]` — never raw `px`/`rem`.

---

### 3. Typography — Scale + Line Height + Weight

```ts
// tokens/typography.ts
export const fontFamily = {
  sans: 'var(--font-sans), system-ui, sans-serif',
  mono: 'var(--font-mono), ui-monospace, monospace',
  brand: 'var(--font-brand), var(--font-sans), system-ui, sans-serif',
} as const;

export const fontSize = {
  xs: '0.75rem',    // 12px
  sm: '0.875rem',   // 14px
  base: '1rem',     // 16px
  lg: '1.125rem',   // 18px
  xl: '1.25rem',    // 20px
  '2xl': '1.5rem',  // 24px
  '3xl': '1.875rem',// 30px
  '4xl': '2.25rem', // 36px
  '5xl': '3rem',    // 48px
} as const;

export const lineHeight = {
  tight: '1.1',
  snug: '1.375',
  normal: '1.5',
  relaxed: '1.625',
} as const;

export const fontWeight = {
  normal: '400',
  medium: '500',
  semibold: '600',
  bold: '700',
} as const;

export const letterSpacing = {
  tight: '-0.02em',
  normal: '0',
  wide: '0.02em',
} as const;

// Semantic text styles — components use these
export const textStyle = {
  heading: {
    xl: { size: fontSize['5xl'], weight: fontWeight.bold, lineHeight: lineHeight.tight, letterSpacing: letterSpacing.tight },
    lg: { size: fontSize['4xl'], weight: fontWeight.bold, lineHeight: lineHeight.tight, letterSpacing: letterSpacing.tight },
    md: { size: fontSize['3xl'], weight: fontWeight.semibold, lineHeight: lineHeight.snug },
    sm: { size: fontSize['2xl'], weight: fontWeight.semibold, lineHeight: lineHeight.snug },
    xs: { size: fontSize.xl, weight: fontWeight.semibold, lineHeight: lineHeight.snug },
  },
  body: {
    lg: { size: fontSize.lg, weight: fontWeight.normal, lineHeight: lineHeight.relaxed },
    base: { size: fontSize.base, weight: fontWeight.normal, lineHeight: lineHeight.normal },
    sm: { size: fontSize.sm, weight: fontWeight.normal, lineHeight: lineHeight.normal },
    xs: { size: fontSize.xs, weight: fontWeight.normal, lineHeight: lineHeight.normal },
  },
  label: {
    lg: { size: fontSize.sm, weight: fontWeight.medium, lineHeight: lineHeight.normal },
    base: { size: fontSize.xs, weight: fontWeight.medium, lineHeight: lineHeight.normal },
  },
  code: {
    base: { size: fontSize.sm, weight: fontWeight.normal, lineHeight: lineHeight.normal, family: fontFamily.mono },
    sm: { size: fontSize.xs, weight: fontWeight.normal, lineHeight: lineHeight.normal, family: fontFamily.mono },
  },
} as const;
```

---

### 4. Elevation — Shadows as Tokens

```ts
// tokens/elevation.ts
export const elevation = {
  0: 'none',
  1: '0 1px 2px 0 rgb(0 0 0 / 0.05)',
  2: '0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)',
  3: '0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)',
  4: '0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1)',
  // Brand elevation (rare — only if brand requires)
  brand: '0 0 0 1px var(--color-primary), 0 4px 6px -1px rgb(0 0 0 / 0.1)',
} as const;
```

---

### 5. Motion — Duration + Easing Tokens

```ts
// tokens/motion.ts
export const motion = {
  duration: {
    instant: '0ms',
    fast: '120ms',
    base: '200ms',
    slow: '350ms',
    slower: '500ms',
  },
  easing: {
    linear: 'linear',
    out: 'cubic-bezier(0.2, 0, 0, 1)',      // Material "standard"
    in: 'cubic-bezier(0.4, 0, 1, 1)',
    inOut: 'cubic-bezier(0.4, 0, 0.2, 1)',   // Material "emphasized"
    spring: 'cubic-bezier(0.34, 1.56, 0.64, 1)', // Overshoot for delight
  },
} as const;
```

---

### 6. Border Radius

```ts
// tokens/border-radius.ts
export const radius = {
  none: '0',
  sm: '0.25rem',   // 4px
  md: '0.375rem',  // 6px
  lg: '0.5rem',    // 8px
  xl: '0.75rem',   // 12px
  '2xl': '1rem',   // 16px
  full: '9999px',
} as const;
```

---

### 7. Breakpoints — Mobile-First, Named

```ts
// tokens/breakpoints.ts
export const breakpoint = {
  sm: '640px',   // ≥ mobile landscape
  md: '768px',   // ≥ tablet
  lg: '1024px',  // ≥ laptop
  xl: '1280px',  // ≥ desktop
  '2xl': '1536px', // ≥ wide
} as const;

// Container widths
export const container = {
  sm: '640px',
  md: '768px',
  lg: '1024px',
  xl: '1280px',
  '2xl': '1400px',
  full: '100%',
} as const;
```

---

## Token Format & Distribution

### TypeScript (Source of Truth)
```ts
// tokens/index.ts
export * from './color';
export * from './spacing';
export * from './typography';
export * from './elevation';
export * from './motion';
export * from './border-radius';
export * from './breakpoints';
```

### CSS Custom Properties (For Runtime / Non-TS)
```css
/* tokens.css — generated from TS at build */
:root {
  --color-bg: #ffffff;
  --color-bg-muted: #f5f5f5;
  --color-fg: #0f0f1a;
  --color-fg-muted: #525252;
  --color-primary: #1a1a2e;
  --color-primary-hover: #16213e;
  --color-focus: #e94560;
  --color-border: #e5e5e5;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  /* ... */
  --radius-md: 0.375rem;
  --elevation-1: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --motion-duration-base: 200ms;
  --motion-easing-out: cubic-bezier(0.2, 0, 0, 1);
}
```

### Tailwind Config (1:1 Mapping)
```js
// tailwind.config.ts
import { color, space, fontSize, fontWeight, lineHeight, radius, elevation, motion, breakpoint } from './tokens';

export default {
  theme: {
    extend: {
      colors: {
        bg: color.bg,
        fg: color.fg,
        border: color.border,
        primary: color.primary,
        secondary: color.secondary,
        danger: color.danger,
        focus: color.focus,
      },
      spacing: space,
      fontSize: Object.fromEntries(
        Object.entries(fontSize).map(([k, v]) => [k, [v, { lineHeight: lineHeight.normal }]])
      ),
      fontWeight,
      lineHeight,
      borderRadius: radius,
      boxShadow: elevation,
      transitionDuration: motion.duration,
      transitionTimingFunction: motion.easing,
      screens: breakpoint,
    },
  },
};
```

---

## Theming Strategy

### 1. CSS Variable Swap (Preferred)
```css
:root { /* light tokens */ }
[data-theme="dark"] { /* dark tokens */ }
```
- Zero component changes
- Works with SSR (no flash)
- `color-scheme: light dark` on `<html>`

### 2. Class-Based (If Required)
```tsx
<ThemeProvider theme="dark">
  <App />
</ThemeProvider>
```
- Provider injects `data-theme` on `<html>`
- Same token values, different CSS variable scope

### 3. Component-Level Override (Rare)
```tsx
<Card style={{ '--color-bg': 'var(--color-brand)' }}>
```
- Only for brand-specific highlights
- Documented as exception

---

## Token Governance

| Rule | Enforcement |
|------|-------------|
| No raw `px`/`rem`/`em` in components | Stylelint: `color-hex-length`, `unit-case`, custom rule |
| No raw color values in components | Stylelint: `color-hex-length`, custom rule for `rgb()`/`hsl()` |
| No raw `z-index` numbers | ESLint rule |
| No raw `transition` values | Stylelint |
| Tokens versioned with semver | `tokens/package.json` |
| Breaking token change = major version | CI check |
| New token = PR with design review | CODEOWNERS on `tokens/` |

---

## Anti-Patterns to Reject

| Anti-Pattern | Why | Fix |
|--------------|-----|-----|
| `text-gray-900 dark:text-gray-100` in component | Hardcoded, not themeable | `color-fg` token |
| `mt-7 mb-3.5` | Not in scale | `mt-6 mb-3` or `spaceSemantic` |
| `shadow-[0_4px_6px_-1px_rgb(0_0_0_/0.1)]` | Arbitrary, not systematic | `elevation-2` token |
| `transition-all duration-300` | Performance, no intent | Explicit properties + motion tokens |
| `z-index: 50` | Chaos | `zIndex.header` token |
| `backdrop-blur-lg` | Performance, decoration | Solid bg + elevation |