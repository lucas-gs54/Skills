# Craft Checklist — Per-Component Validation

Run this checklist for **every component** before considering it done. Zero tolerance.

---

## 1. Design System Compliance

- [ ] **Zero magic numbers** — all spacing, colors, sizes, shadows, durations reference tokens
- [ ] **Semantic token usage** — `color-fg` not `color-slate-900`, `elevation-2` not `shadow-lg`
- [ ] **Dark mode works** — component renders correctly in `[data-theme="dark"]` without component changes
- [ ] **No hardcoded breakpoints** — container queries or token-mapped breakpoints only
- [ ] **Typography scale** — `textStyle` keys used, no arbitrary `font-size`
- [ ] **Consistent radius** — `radius-md`/`radius-lg` tokens, no `rounded-xl` arbitrary
- [ ] **No arbitrary values** — Tailwind `[]` / CSS `calc()` with raw numbers banned

## 2. Semantic HTML & Accessibility

- [ ] **Correct element** — `<button>` for actions, `<a>` for navigation, `<label>` for inputs
- [ ] **Heading hierarchy** — if component has headings, they fit page outline (h1→h2→h3)
- [ ] **Landmarks** — component doesn't orphan content (wraps in `<section>`/`<article>` if needed)
- [ ] **Focus visible** — `:focus-visible` ring using `--color-focus` token
- [ ] **Focus order** — logical tab sequence, no `tabindex` > 0
- [ ] **ARIA only when native fails** — no `role="button"` on `<button>`, no `aria-label` duplicating text
- [ ] **Labels on all inputs** — explicit `for`/`id` or implicit wrapping
- [ ] **Error messages linked** — `aria-describedby` points to `role="alert"` error
- [ ] **Icon-only controls labeled** — `aria-label` or visible text + `.sr-only`
- [ ] **Reduced motion respected** — animations instant/disabled when `prefers-reduced-motion: reduce`
- [ ] **Color contrast** — 4.5:1 text, 3:1 UI elements (verify in DevTools)
- [ ] **Touch targets** — ≥ 44×44px (48×48 preferred) for mobile

## 3. TypeScript & Props

- [ ] **Strict props interface** — no `any`, no `React.ComponentPropsWithoutRef`
- [ ] **Discriminated unions** for variants — `tone: 'primary' | 'secondary' | 'danger'`
- [ ] **Polymorphic `as`/`asChild`** — `Slot` pattern, `ElementType`, `forwardRef`
- [ ] **No escape hatches** — `className`/`style` only for layout overrides, not design tokens
- [ ] **Generic types** for data components — `Table<User>`, `Select<Option>`
- [ ] **Exhaustiveness checking** — `assertNever` in variant switches
- [ ] **JSDoc on exported components** — description, example, prop references

## 4. Component Architecture

- [ ] **Primitive or composite** — primitives have token props only; composites compose primitives
- [ ] **Compound components** for complex state — `Select.Root/Trigger/Content/Item`
- [ ] **Variant props semantic** — `tone` not `bgColor`, `size` not `height`
- [ ] **No god components** — max ~8 props, split if more
- [ ] **Controlled + uncontrolled** — forms support both patterns
- [ ] **Ref forwarding** — `forwardRef` on all primitives, `React.Ref` types correct
- [ ] **DisplayName set** — `Component.displayName = 'ComponentName'`

## 5. CSS & Styling

- [ ] **CSS custom properties** — tokens as `--color-*`, `--space-*`, etc.
- [ ] **Explicit transitions** — no `transition: all`, list properties
- [ ] **No `will-change` on static** — only on actively animating elements
- [ ] **Containment** — `contain: layout style paint` on component roots
- [ ] **Logical properties** — `margin-inline-start`, not `margin-left`
- [ ] **Fluid where appropriate** — `clamp()` for typography/spacing at page level
- [ ] **Container queries** over media queries for component responsiveness
- [ ] **Cascade layers** — styles loaded in correct layer order
- [ ] **Print styles** — `@media print` hides UI chrome, shows content

## 6. Motion & Interaction

- [ ] **Purposeful animation** — every motion answers "what changed?"
- [ ] **Token durations/easings** — `--motion-duration-base`, `--motion-easing-out`
- [ ] **Reduced motion** — instant transitions, no parallax, no auto-play
- [ ] **No layout animation** — `transform`/`opacity` only, or `contain` + FLIP
- [ ] **Micro-feedback** — press scale, focus ring, loading state
- [ ] **Enter/exit animations** — `AnimatePresence` / CSS keyframes for mount/unmount
- [ ] **Stagger only for lists** — `staggerChildren` max 50ms delay

## 7. Performance

- [ ] **Bundle size** — component tree-shakable, no heavy deps bundled unnecessarily
- [ ] **Code splitting** — heavy sub-components lazy-loaded (`React.lazy` + `Suspense`)
- [ ] **No layout shift** — images have `width`/`height`, fonts `size-adjust`, skeletons match final size
- [ ] **Fonts optimized** — variable font, subsetted, `font-display: optional`, preload
- [ ] **Images optimized** — AVIF/WebP, responsive `srcset`, lazy loading
- [ ] **No inline styles** — token variables only, no `style={{}}` with raw values

## 8. Testing

- [ ] **Unit tests** — logic, prop variations, edge cases
- [ ] **A11y tests** — `jest-axe` / `vitest-axe` zero violations
- [ ] **Interaction tests** — Testing Library user events (click, keyboard, focus)
- [ ] **Visual regression** — Storybook stories for all states (default, hover, focus, disabled, loading, error)
- [ ] **Type tests** — `expectTypeOf` for prop contracts, discriminated unions

## 9. Documentation

- [ ] **Storybook/Histoire stories** — all variant combos, states, composition examples
- [ ] **Props table** — auto-generated from TypeScript (no manual maintenance)
- [ ] **Accessibility notes** — keyboard, screen reader, focus behavior documented
- [ ] **Motion behavior** — what animates, when, reduced-motion fallback
- [ ] **Composition examples** — how to extend, customize, compose with other components
- [ ] **Changelog entry** — if modifying existing component

---

## Quick Run: CI Gates

```yaml
# .github/workflows/quality.yml
name: Quality Gates
on: [push, pull_request]
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm run typecheck      # tsc --noEmit
      - run: npm run lint           # eslint + stylelint
      - run: npm run test           # vitest (unit + a11y)
      - run: npm run test:e2e       # playwright (critical paths)
      - run: npm run build          # production build
      - run: npm run bundle:analyze # check budgets
```

---

## Per-Component Sign-Off Template

```markdown
## Component: `<Button />`

### Design System
- [ ] Tokens only (color, space, radius, elevation, motion)
- [ ] Dark mode verified
- [ ] No arbitrary values

### A11y
- [ ] Semantic element (`<button>`)
- [ ] Focus ring visible (`--color-focus`)
- [ ] Loading state announced (`aria-busy`)
- [ ] Disabled state (`aria-disabled`)
- [ ] Reduced motion respected
- [ ] Contrast ≥ 4.5:1

### TypeScript
- [ ] Strict props (tone, size, asChild, as)
- [ ] Polymorphic `as`/`asChild` works
- [ ] Discriminated union for anchor/button
- [ ] `assertNever` on tone switch

### Architecture
- [ ] Primitive (no children composition)
- [ ] Compound? N/A
- [ ] Forwarded ref
- [ ] displayName set

### Motion
- [ ] Press scale (0.98)
- [ ] Hover elevation (shadow token)
- [ ] Focus ring transition
- [ ] Loading spinner (motion token)
- [ ] Reduced motion: instant

### Performance
- [ ] No heavy deps
- [ ] Tree-shakable
- [ ] No layout shift

### Testing
- [ ] Unit: 100% branches
- [ ] A11y: 0 violations
- [ ] Stories: default, primary/secondary/danger/ghost, sm/md/lg, loading, disabled, asChild

### Docs
- [ ] Props table auto-generated
- [ ] A11y notes
- [ ] Motion notes
- [ ] Composition examples

**Signed off by:** _______________ **Date:** _______________
```

---

## Anti-Patterns Quick Scan (Grep)

```bash
# Run before PR
grep -r "transition-all\|transition: all" src/ && echo "FAIL: transition-all found"
grep -r "scale(1.0[5-9])\|rotate(" src/ && echo "FAIL: meaningless hover scale/rotate"
grep -r "backdrop-blur\|backdrop-filter" src/ && echo "FAIL: backdrop-filter abuse"
grep -r "box-shadow.*rgba.*[0-9]" src/ && echo "FAIL: arbitrary colored shadows"
grep -r "z-index: 999\|z-index: 1000\|z-index: 50" src/ && echo "FAIL: z-index chaos"
grep -r "outline: none\|outline:none" src/ && echo "FAIL: outline:none without focus-visible"
grep -r "className=.*text-.*dark:" src/ && echo "FAIL: hardcoded dark mode in components"
grep -r "placeholder=.*[A-Za-z]" src/ --include="*.tsx" && echo "FAIL: placeholder as label"
grep -r "<div.*onClick" src/ && echo "FAIL: div onClick"
grep -r "any\]" src/ && echo "FAIL: any in props"
grep -r "forwardRef" src/ --include="*.tsx" | wc -l  # should match primitive count
```

**Zero tolerance** — any hit = fix before merge.