# Purposeful Motion — Principles, Tokens, Patterns, Reduced Motion

Motion guides, informs, delights — never decorates. Every animation answers: *What changed? Where did it come from? Where is it going?*

---

## Motion Principles

### 1. Motion Has Meaning
| Animation | Communicates |
|-----------|--------------|
| Enter (fade + slide) | "New content arrived" |
| Exit (fade + slide) | "Content leaving" |
| Scale down on press | "Pressed, action pending" |
| Elevation change on hover | "Interactive, pressable" |
| Color shift on focus | "Keyboard focus here" |
| Shake on error | "Invalid, try again" |
| Progress ring | "Working, wait" |
| Staggered list enter | "Multiple items, related" |

**No animation without semantic purpose.**

### 2. Respect Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```
- Test with `prefers-reduced-motion: reduce` in DevTools
- No parallax, no auto-play, no infinite animations
- Instant state changes when reduced

### 3. Performance First
- Animate only `transform`, `opacity`, `filter` (compositor-only)
- Avoid `width`/`height`/`margin`/`padding`/`top`/`left` — layout thrashing
- `will-change` only on animating elements, remove after
- `contain: layout paint` on animated containers
- Target 60fps (16.67ms/frame) — measure in Performance tab

### 4. Consistency Through Tokens
```ts
// tokens/motion.ts
export const motion = {
  duration: {
    instant: '0ms',
    fast: '120ms',      // micro-feedback (button press)
    base: '200ms',      // standard transitions
    slow: '350ms',      // enter/exit, modals
    slower: '500ms',    // complex choreography
  },
  easing: {
    linear: 'linear',
    out: 'cubic-bezier(0.2, 0, 0, 1)',      // Material "standard" — decelerate
    in: 'cubic-bezier(0.4, 0, 1, 1)',       // accelerate
    inOut: 'cubic-bezier(0.4, 0, 0.2, 1)',  // symmetric
    spring: 'cubic-bezier(0.34, 1.56, 0.64, 1)', // overshoot for delight
  },
} as const;
```

---

## Animation Patterns

### 1. Enter / Exit (Mount / Unmount)
```tsx
// Framer Motion
<AnimatePresence mode="wait">
  {isOpen && (
    <motion.div
      initial={{ opacity: 0, y: 8 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -8 }}
      transition={{ duration: motion.duration.base, easing: motion.easing.out }}
    >
      Content
    </motion.div>
  )}
</AnimatePresence>
```

```css
/* CSS-only (for non-React) */
@keyframes enter {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
@keyframes exit {
  from { opacity: 1; transform: translateY(0); }
  to { opacity: 0; transform: translateY(-8px); }
}
.entering { animation: enter var(--motion-duration-base) var(--motion-easing-out); }
.exiting { animation: exit var(--motion-duration-base) var(--motion-easing-in); }
```

### 2. State Transitions (Hover, Focus, Active, Disabled)
```tsx
<motion.button
  whileHover={{ scale: 1.02 }}           // subtle — not 1.05
  whileTap={{ scale: 0.98 }}             // press feedback
  whileFocus={{ boxShadow: 'var(--elevation-3)' }}
  style={{ transition: 'transform var(--motion-duration-fast) var(--motion-easing-out)' }}
>
  Button
</motion.button>
```

```css
/* CSS — preferred for simple states */
.button {
  transition:
    transform var(--motion-duration-fast) var(--motion-easing-out),
    box-shadow var(--motion-duration-base) var(--motion-easing-out),
    background-color var(--motion-duration-base) var(--motion-easing-out);
}
.button:hover { transform: translateY(-1px); box-shadow: var(--elevation-2); }
.button:active { transform: translateY(0) scale(0.98); }
.button:focus-visible { box-shadow: var(--elevation-3), 0 0 0 2px var(--color-focus); }
```

### 3. Loading / Progress States
```tsx
// Skeleton — pulse animation
<motion.div
  animate={{ opacity: [1, 0.5, 1] }}
  transition={{ duration: 1.5, repeat: Infinity, ease: 'linear' }}
  className="skeleton"
/>

// Spinner — rotate
<motion.svg
  animate={{ rotate: 360 }}
  transition={{ duration: 1, repeat: Infinity, ease: 'linear' }}
>
  <circle ... />
</motion.svg>

// Progress ring
<motion.path
  initial={{ pathLength: 0 }}
  animate={{ pathLength: 1 }}
  transition={{ duration: motion.duration.slow, easing: motion.easing.out }}
/>
```

### 4. Staggered Children
```tsx
<motion.ul
  initial="hidden"
  animate="show"
  variants={{
    hidden: { opacity: 0 },
    show: {
      opacity: 1,
      transition: { staggerChildren: 0.05 },
    },
  }}
>
  {items.map(item => (
    <motion.li
      key={item.id}
      variants={{
        hidden: { opacity: 0, y: 10 },
        show: { opacity: 1, y: 0 },
      }}
    >
      {item.label}
    </motion.li>
  ))}
</motion.ul>
```

### 5. Layout Animations (FLIP) — Shared Layout
```tsx
// Framer Motion layoutId — automatic FLIP
<motion.div layoutId="card" layout>Content</motion.div>

// CSS — view transitions (experimental)
@keyframes slide {
  from { transform: var(--view-transition-old-transform); }
  to { transform: var(--view-transition-new-transform); }
}
::view-transition-old(root) { animation: none; }
::view-transition-new(root) { animation: slide 0.3s ease-out; }
```

### 6. Micro-Feedback (Press, Error, Success)
```tsx
// Press ripple (CSS)
.button::after {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: currentColor;
  opacity: 0;
  transform: scale(0);
}
.button:active::after {
  animation: ripple var(--motion-duration-fast) var(--motion-easing-out);
}
@keyframes ripple {
  to { opacity: 0.1; transform: scale(2); }
}

// Error shake
<motion.input
  animate={hasError ? { x: [-8, 8, -8, 8, 0] } : {}}
  transition={{ duration: 0.4, easing: motion.easing.spring }}
/>

// Success checkmark
<motion.path
  initial={{ pathLength: 0 }}
  animate={{ pathLength: 1 }}
  transition={{ duration: 0.4, delay: 0.2, easing: motion.easing.out }}
/>
```

---

## Reduced Motion Strategies

### 1. Instant Transitions
```css
@media (prefers-reduced-motion: reduce) {
  .modal,
  .dialog,
  .toast,
  .dropdown,
  .tooltip {
    animation: none !important;
    transition: none !important;
    opacity: 1 !important;
    transform: none !important;
  }
}
```

### 2. Conditional Animation in JS
```tsx
const prefersReducedMotion = useMediaQuery('(prefers-reduced-motion: reduce)');

<motion.div
  animate={prefersReducedMotion ? {} : { y: 0 }}
  initial={prefersReducedMotion ? {} : { y: 20 }}
  transition={prefersReducedMotion ? undefined : { duration: 0.3 }}
>
  Content
</motion.div>
```

### 3. Disable Parallax / Infinite
```tsx
const prefersReducedMotion = useMediaQuery('(prefers-reduced-motion: reduce)');

<prefersReducedMotion ? null : (
  <ParallaxLayer factor={0.5}><Background /></ParallaxLayer>
)>

<prefersReducedMotion ? (
  <StaticHero />
) : (
  <AutoCarousel />
)}
```

---

## Motion Accessibility Checklist

- [ ] All animations respect `prefers-reduced-motion`
- [ ] No infinite animations without pause control
- [ ] No auto-playing carousels/videos (or pause on hover/focus)
- [ ] No parallax without reduced-motion fallback
- [ ] Flashing content ≤ 3 flashes/sec (WCAG 2.3.1)
- [ ] Motion not sole carrier of information (color + text + icon)
- [ ] `aria-live` for dynamic updates, not animation
- [ ] Focus order preserved during layout animations
- [ ] `Tab` navigation works during modal enter/exit

---

## Performance Budget

| Metric | Target |
|--------|--------|
| Animation frame time | < 16.67ms (60fps) |
| Long task during animation | 0 |
| `will-change` elements | ≤ 3 concurrent |
| Layout shifts during animation | 0 (CLS) |
| Paint area per frame | Minimal (devtools: "Paint flashing") |

---

## Anti-Patterns

| Anti-Pattern | Why | Fix |
|--------------|-----|-----|
| `whileHover={{ scale: 1.05, rotate: 3 }}` | Meaningless, vestibular trigger | Subtle elevation/color shift |
| `transition: all 0.3s` | Layout thrashing, no intent | Explicit properties + tokens |
| Infinite spinner on static page | Distraction, no purpose | Remove or `prefers-reduced-motion` |
| Parallax on scroll | Main thread work, reduced-motion violation | CSS `background-attachment: fixed` + media query |
| Auto-play carousel | WCAG 2.2.2 violation | Manual only, or pause on hover/focus |
| `animate={{ x: 100 }}` no exit | Content disappears abruptly | `AnimatePresence` + exit animation |
| No focus animation | Keyboard users lost | `:focus-visible` ring transition |
| Loading spinner blocks interaction | Perceived slowness | Skeleton screens, progressive load |