# AI Frontend Anti-Patterns — Catalog & Fixes

Comprehensive reference of patterns that indicate generic AI-generated frontend code. Each entry includes: **what the AI does**, **why it fails**, and **the craft alternative**.

---

## 1. Color & Visual Identity

### 1.1 Neon/Cyberpunk Gradients on Dark Backgrounds
**AI Output:**
```css
/* Tailwind */
bg-gradient-to-br from-cyan-500 via-purple-600 to-pink-500
/* or CSS */
background: linear-gradient(135deg, #06b6d4 0%, #9333ea 50%, #ec4899 100%);
```
**Why It Fails:**
- Zero brand connection — used because "looks techy"
- Accessibility nightmare: text contrast unpredictable over gradient
- Dates the product; screams "2023 AI template"
- Printing/reader modes destroy it

**Craft Alternative:**
```ts
// tokens/colors.ts
export const color = {
  brand: {
    primary: { DEFAULT: '#1a1a2e', hover: '#16213e', active: '#0f0f1a' },
    accent: { DEFAULT: '#e94560', hover: '#d63e5a', active: '#b8324a' },
  },
  bg: { DEFAULT: '#ffffff', muted: '#f5f5f5', inverted: '#0f0f1a' },
  fg: { DEFAULT: '#0f0f1a', muted: '#525252', inverted: '#ffffff' },
  border: { DEFAULT: '#e5e5e5', focus: '#e94560' },
  focus: '#e94560', // single focus token, used everywhere
} as const;
```
```tsx
// Usage — semantic, themeable
<Button tone="primary">Primary Action</Button>
<Card tone="muted">Content</Card>
```

---

### 1.2 Arbitrary Colored Glows/Shadows
**AI Output:**
```css
box-shadow: 0 0 40px rgba(99, 102, 241, 0.6);
filter: drop-shadow(0 0 20px rgba(236, 72, 153, 0.5));
```
**Why It Fails:**
- "Glow" ≠ depth — it's decoration masquerading as elevation
- Colored shadows break in high-contrast/forced-color modes
- No systematic elevation language — every component different
- Performance: `filter`/`box-shadow` with blur expensive on scroll

**Craft Alternative:**
```ts
// tokens/elevation.ts
export const elevation = {
  1: '0 1px 2px 0 rgb(0 0 0 / 0.05)',
  2: '0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)',
  3: '0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)',
  4: '0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1)',
} as const;
```
```tsx
// Consistent, predictable, themeable
<Card elevation={2}>Content</Card>
<Dialog elevation={4}>Modal</Dialog>
<Dropdown elevation={3}>Menu</Dropdown>
```

---

### 1.3 Hardcoded Color Values in Components
**AI Output:**
```tsx
<div className="text-[#1a1a2e] bg-[#f5f5f5] border-[#e5e5e5]">
  <button className="bg-[#e94560] hover:bg-[#d63e5a] text-white">
```
**Why It Fails:**
- Dark mode = find/replace nightmare
- No design system — every component reinvents colors
- Brand changes require grep across codebase
- Tokens exist in Figma but not code

**Craft Alternative:** See §1.1 — semantic token aliases only in components.

---

## 2. Motion & Animation

### 2.1 Meaningless Scale/Rotate on Hover
**AI Output:**
```tsx
// Framer Motion
<motion.div whileHover={{ scale: 1.05, rotate: 2 }} />
// CSS
@keyframes float { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }
.card:hover { transform: scale(1.05) rotate(3deg); }
```
**Why It Fails:**
- No semantic meaning — "I'm hoverable" ≠ scale+rotate
- Fails `prefers-reduced-motion` — vestibular disorders triggered
- Layout shift on adjacent elements (unless `contain` used)
- Feels "gamey," not professional

**Craft Alternative:**
```ts
// tokens/motion.ts
export const motion = {
  duration: { fast: '120ms', base: '200ms', slow: '350ms' },
  easing: { out: 'cubic-bezier(0.2, 0, 0, 1)', inOut: 'cubic-bezier(0.4, 0, 0.2, 1)' },
} as const;
```
```tsx
// Purposeful: elevation change communicates "pressable"
<Button>
  <span className="transition-shadow duration-base ease-out hover:shadow-elevation-2">
    Action
  </span>
</Button>

// Purposeful: slide + fade for entered content
<AnimatePresence mode="wait">
  <motion.div
    initial={{ opacity: 0, y: 8 }}
    animate={{ opacity: 1, y: 0 }}
    exit={{ opacity: 0, y: -8 }}
    transition={{ duration: motion.duration.base, easing: motion.easing.out }}
  >
    Content
  </motion.div>
</AnimatePresence>
```

---

### 2.2 `transition: all` / `transition-all`
**AI Output:**
```css
.card { transition: all 0.3s ease; }
.button { transition-all duration-300; }
```
**Why It Fails:**
- Animates layout properties (`width`, `height`, `margin`) → layout thrashing, jank
- Animates `color`/`background` on non-interactive elements — wasted work
- No intent — what *should* animate?
- Performance killer on complex components

**Craft Alternative:**
```css
/* Explicit properties only */
.button {
  transition:
    background-color var(--motion-duration-base) var(--motion-easing-out),
    box-shadow var(--motion-duration-base) var(--motion-easing-out),
    transform var(--motion-duration-fast) var(--motion-easing-out);
}

/* Or token-mapped */
@layer utilities {
  .transition-interactive {
    transition-property: background-color, border-color, color, box-shadow, transform;
    transition-duration: var(--motion-duration-base);
    transition-timing-function: var(--motion-easing-out);
  }
}
```

---

### 2.3 No `prefers-reduced-motion` Handling
**AI Output:**
```tsx
// Animations run regardless of user preference
<motion.div animate={{ x: 100 }} transition={{ duration: 1 }} />
```
**Why It Fails:**
- Violates WCAG 2.3.3 (Animation from Interactions)
- Triggers vestibular disorders, nausea, seizures
- Legal liability (ADA, EAA, etc.)
- Zero excuse — one media query

**Craft Alternative:**
```css
/* Global — all motion respects preference */
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

/* Component-level — instant state changes */
@media (prefers-reduced-motion: reduce) {
  .modal { animation: none; opacity: 1; transform: none; }
  .toast { animation: none; }
}
```
```tsx
// Framer Motion — built-in
<motion.div
  animate={{ opacity: 1 }}
  transition={{ duration: 0.2 }}
  style={{ transitionDuration: '0.2s' }} // overridden by media query
/>
```

---

### 2.4 Auto-Playing Carousels/Parallax/Scroll Animations
**AI Output:**
```tsx
// Infinite auto-scroll carousel
useEffect(() => {
  const id = setInterval(() => next(), 3000);
  return () => clearInterval(id);
}, []);
// Parallax on scroll
<div style={{ transform: `translateY(${scrollY * 0.5}px)` }} />
```
**Why It Fails:**
- Carousels: users miss content, WCAG 2.2.2 (Pause, Stop, Hide)
- Parallax: `prefers-reduced-motion` violation, performance hazard
- Scroll animations: main thread work on scroll → jank
- No user control

**Craft Alternative:**
- Carousels: manual navigation only, or `prefers-reduced-motion` pauses auto-play
- Parallax: CSS `background-attachment: fixed` (no JS) + media query disable
- Scroll animations: `IntersectionObserver` + `will-change` only during animation

---

## 3. HTML & Semantics

### 3.1 `div` Soup — Missing Landmarks & Structure
**AI Output:**
```tsx
<div className="flex min-h-screen flex-col">
  <div className="flex items-center justify-between p-4">
    <div className="text-2xl font-bold">Logo</div>
    <div className="flex gap-4">
      <div className="px-4 py-2 rounded bg-blue-500">Home</div>
      <div className="px-4 py-2 rounded">About</div>
    </div>
  </div>
  <div className="flex-1 p-8">
    <div className="text-4xl font-bold">Welcome</div>
    <div className="mt-4 text-gray-600">Content...</div>
  </div>
  <div className="p-4 border-t">Footer</div>
</div>
```
**Why It Fails:**
- Screen readers: no landmarks → "main content" not discoverable
- No heading hierarchy → outline broken
- Keyboard: `div` not focusable → navigation broken
- SEO: no semantic signals

**Craft Alternative:**
```tsx
<PageLayout>
  <header role="banner">
    <nav aria-label="Main navigation">
      <Link href="/">Logo</Link>
      <ul className="flex gap-4">
        <li><Link href="/">Home</Link></li>
        <li><Link href="/about">About</Link></li>
      </ul>
    </nav>
  </header>

  <main id="main-content" role="main">
    <section aria-labelledby="welcome-heading">
      <h1 id="welcome-heading">Welcome</h1>
      <p>Content...</p>
    </section>
  </main>

  <footer role="contentinfo">Footer</footer>
</PageLayout>
```

---

### 3.2 Heading Hierarchy Violations
**AI Output:**
```tsx
<h3>Section Title</h3>
<h1>Page Title</h1> {/* or missing */}
<h2>Subsection</h2>
<h4>Detail</h4> {/* skipped h3 */}
```
**Why It Fails:**
- Screen reader users navigate by headings — broken outline = lost
- SEO: heading structure signals content importance
- Legal: WCAG 1.3.1 (Info and Relationships)

**Craft Alternative:**
```tsx
// One h1 per page
<h1>Page Title</h1>

<section aria-labelledby="section-heading">
  <h2 id="section-heading">Section Title</h2>

  <section aria-labelledby="subsection-heading">
    <h3 id="subsection-heading">Subsection</h3>

    <h4 id="detail-heading">Detail</h4>
  </section>
</section>
```

---

### 3.3 Lists Not Used for Repeated Items
**AI Output:**
```tsx
<div className="space-y-2">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```
**Why It Fails:**
- Screen readers: no list announcement → count unknown, navigation broken
- `ul`/`ol`/`dl` provide semantics for free
- CSS can style lists identically to `div` soup

**Craft Alternative:**
```tsx
<ul className="space-y-2" role="list">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>

<!-- Description list for key/value -->
<dl className="grid grid-cols-[auto_1fr] gap-x-4 gap-y-2">
  <dt>Name</dt><dd>Value</dd>
  <dt>Email</dt><dd>value@example.com</dd>
</dl>
```

---

### 3.4 Buttons vs Links Confusion
**AI Output:**
```tsx
<!-- Navigation as button -->
<button onClick={() => router.push('/about')}>About</button>

<!-- Action as link -->
<a href="#" onClick={handleDelete}>Delete</a>
```
**Why It Fails:**
- Buttons: actions (submit, open modal, toggle)
- Links: navigation (URL change)
- Screen readers announce differently
- Keyboard: `Enter` activates button, `Enter` follows link
- Crawlers follow links, not button clicks

**Craft Alternative:**
```tsx
// Navigation → Link
<Link href="/about">About</Link>

// Action → Button
<Button onClick={handleDelete} tone="danger">Delete</Button>

// Router-aware link that looks like button
<Link href="/settings" asChild>
  <Button>Settings</Button>
</Link>
```

---

### 3.5 Icon-Only Buttons Without Accessible Name
**AI Output:**
```tsx
<button className="p-2" onClick={handleMenu}>
  <MenuIcon className="w-5 h-5" />
</button>
```
**Why It Fails:**
- Screen reader announces: "Button" — no purpose
- WCAG 4.1.2 (Name, Role, Value)
- Voice control: "Click menu button" fails

**Craft Alternative:**
```tsx
<button aria-label="Open main menu" className="p-2" onClick={handleMenu}>
  <MenuIcon className="w-5 h-5" aria-hidden="true" />
</button>

<!-- Or visible text + visually hidden -->
<button className="flex items-center gap-2 p-2" onClick={handleMenu}>
  <MenuIcon className="w-5 h-5" aria-hidden="true" />
  <span className="sr-only">Open main menu</span>
</button>
```

---

### 3.6 Form Inputs Without Associated Labels
**AI Output:**
```tsx
<input placeholder="Email" className="w-full p-2 border rounded" />
<select className="w-full p-2 border rounded">
  <option>Option 1</option>
</select>
```
**Why It Fails:**
- Placeholder ≠ label (disappears on input, not announced consistently)
- No programmatic association → screen reader: "Edit text, blank"
- Clicking label should focus input (UX + a11y)

**Craft Alternative:**
```tsx
<!-- Explicit label -->
<label htmlFor="email" className="block text-sm font-medium mb-1">
  Email
</label>
<input id="email" type="email" placeholder="you@example.com" className="w-full p-2 border rounded" />

<!-- Implicit label (wrapping) -->
<label className="block">
  <span className="block text-sm font-medium mb-1">Email</span>
  <input type="email" placeholder="you@example.com" className="w-full p-2 border rounded" />
</label>

<!-- Select -->
<label htmlFor="country" className="block text-sm font-medium mb-1">
  Country
</label>
<select id="country" className="w-full p-2 border rounded">
  <option value="">Select...</option>
  <option value="br">Brazil</option>
</select>
```

---

### 3.7 Error Messages Not Linked to Inputs
**AI Output:**
```tsx
<input className="border-red-500" />
<p className="text-red-500 text-sm">Invalid email</p>
```
**Why It Fails:**
- Screen reader doesn't know error belongs to input
- `aria-describedby` required for programmatic link
- Error must be announced on focus/change

**Craft Alternative:**
```tsx
<label htmlFor="email">Email</label>
<input
  id="email"
  type="email"
  aria-invalid={hasError}
  aria-describedby={hasError ? 'email-error' : undefined}
  className="w-full p-2 border rounded"
/>
{hasError && (
  <p id="email-error" role="alert" className="text-red-600 text-sm mt-1">
    Invalid email format
  </p>
)}
```

---

### 3.8 Images Without Dimensions / Lazy Loading / Formats
**AI Output:**
```tsx
<img src="/hero.png" alt="Hero" className="w-full" />
```
**Why It Fails:**
- No `width`/`height` → layout shift (CLS)
- No `loading="lazy"` → bandwidth waste
- No modern formats (AVIF/WebP) → larger downloads
- `alt` often missing or generic

**Craft Alternative:**
```tsx
<picture>
  <source srcSet="/hero.avif" type="image/avif" />
  <source srcSet="/hero.webp" type="image/webp" />
  <img
    src="/hero.png"
    alt="Descriptive, contextual alt text"
    width={1200}
    height={600}
    loading="lazy"
    decoding="async"
    className="w-full h-auto"
  />
</picture>

<!-- For LCP images — eager, preload -->
<link rel="preload" as="image" href="/hero.avif" type="image/avif" />
<img
  src="/hero.avif"
  alt="..."
  width={1200}
  height={600}
  loading="eager"
  fetchPriority="high"
  className="w-full h-auto"
/>
```

---

### 3.9 Missing Focus Styles / `outline: none` Without Replacement
**AI Output:**
```css
button:focus { outline: none; }
a:focus { outline: none; }
input:focus { outline: none; }
```
**Why It Fails:**
- Keyboard users: invisible focus = unusable
- WCAG 2.4.7 (Focus Visible)
- `outline: none` is fine *only* with visible alternative

**Craft Alternative:**
```css
/* Global focus ring — tokenized */
:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
  border-radius: var(--radius-sm);
}

/* Component-specific if needed */
.button:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
}

.input:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 1px;
}
```

---

## 4. CSS & Architecture

### 4.1 Magic Numbers Everywhere
**AI Output:**
```tsx
<div className="mt-7 mb-3.5 px-[17px] py-[11px] text-[15px] leading-[1.4] w-[342px]">
```
**Why It Fails:**
- Not in spacing/typography scale
- Dark mode / density changes = manual edits
- No design system — every value bespoke
- `px`/`rem` mixing, arbitrary decimals

**Craft Alternative:**
```tsx
<!-- All values from tokens -->
<div className="mt-6 mb-3 px-4 py-3 text-sm leading-normal w-full max-w-md">
<!-- Or semantic props -->
<Box mt="space-6" mb="space-3" px="space-4" py="space-3" maxWidth="container-md">
  <Text size="sm" weight="normal">Content</Text>
</Box>
```

---

### 4.2 `className` Soup — Styling in Component Props
**AI Output:**
```tsx
<Card
  className="bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-700 rounded-xl shadow-lg hover:shadow-xl transition-shadow duration-300 p-6"
>
  <h3 className="text-xl font-semibold text-gray-900 dark:text-gray-100 mb-2">Title</h3>
  <p className="text-gray-600 dark:text-gray-400 leading-relaxed">Content</p>
</Card>
```
**Why It Fails:**
- Component has no opinion — caller does all styling
- No design system enforcement
- Dark mode hardcoded in every usage
- Cannot theme/extend without props explosion

**Craft Alternative:**
```tsx
// Card.tsx — owns its styling via tokens
interface CardProps {
  tone?: 'default' | 'muted' | 'bordered';
  elevation?: 1 | 2 | 3;
  padding?: SpaceToken;
  children: React.ReactNode;
}

export function Card({ tone = 'default', elevation = 1, padding = 'space-4', children }: CardProps) {
  return (
    <article
      className={cn(
        'rounded-lg transition-shadow',
        toneStyles[tone],
        elevationStyles[elevation],
        paddingStyles[padding]
      )}
    >
      {children}
    </article>
  );
}

// Usage — semantic only
<Card tone="muted" elevation={2} padding="space-6">
  <Heading size="lg">Title</Heading>
  <Text tone="muted">Content</Text>
</Card>
```

---

### 4.3 Wrapper `div`s for Layout Only
**AI Output:**
```tsx
<div className="flex items-center justify-between">
  <div className="flex items-center gap-2">
    <Icon />
    <span>Label</span>
  </div>
  <div className="flex items-center gap-4">
    <Button>Action</Button>
  </div>
</div>
```
**Why It Fails:**
- No semantics — `div` conveys nothing
- Could be `<header>`, `<footer>`, `<nav>`, `<toolbar>`, `<Stack>`
- Primitives should handle layout

**Craft Alternative:**
```tsx
// Primitive layout components
<Stack direction="horizontal" align="center" justify="space-between" gap="space-4">
  <Inline gap="space-2" align="center">
    <Icon />
    <Text>Label</Text>
  </Inline>
  <Inline gap="space-4" align="center">
    <Button>Action</Button>
  </Inline>
</Stack>
```

---

### 4.4 `z-index` Chaos
**AI Output:**
```css
.modal { z-index: 9999; }
.dropdown { z-index: 100; }
.tooltip { z-index: 1000; }
.header { z-index: 50; }
.sidebar { z-index: 60; }
```
**Why It Fails:**
- Unmaintainable — new layer = guess a number
- Stacking contexts unpredictable
- Portals break context

**Craft Alternative:**
```ts
// tokens/zIndex.ts
export const zIndex = {
  base: 0,
  dropdown: 100,
  sticky: 200,
  header: 300,
  modal: 400,
  popover: 500,
  tooltip: 600,
  toast: 700,
} as const;
```
```tsx
// Portal-managed — each layer owns its context
<Portal to="body">
  <ModalManager zIndex={zIndex.modal}>
    <Dialog>Content</Dialog>
  </ModalManager>
</Portal>
```

---

### 4.5 `backdrop-filter: blur()` Abuse
**AI Output:**
```tsx
<div className="backdrop-blur-lg bg-white/50 border border-white/20">
<div className="backdrop-blur-xl bg-black/30">
```
**Why It Fails:**
- GPU rasterization cost — kills scroll performance on mobile
- No fallback for unsupported browsers
- Often used as "glassmorphism" decoration, not purpose
- Text readability over blur often fails contrast

**Craft Alternative:**
```tsx
// Solid background + elevation token — performant, accessible
<Card elevation={2} tone="muted">Content</Card>

// If glass effect is BRAND (rare) — scoped, with fallback
@supports (backdrop-filter: blur(8px)) {
  .glass-brand {
    backdrop-filter: blur(8px);
    background: var(--color-bg-glass);
  }
}
@media (prefers-reduced-transparency: reduce) {
  .glass-brand { backdrop-filter: none; background: var(--color-bg); }
}
```

---

## 5. Performance & UX

### 5.1 No Font Loading Strategy
**AI Output:**
```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
```
**Why It Fails:**
- `display=swap` → layout shift (CLS) when font loads
- No preload → late discovery
- Full font family downloaded (unused weights)
- No self-hosting → third-party request, privacy

**Craft Alternative:**
```html
<!-- Preload critical weights -->
<link rel="preload" as="font" type="font/woff2" crossorigin href="/fonts/inter-var.woff2" />

<!-- Self-hosted, subsetted, variable font -->
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-weight: 100 900;
  font-display: optional; /* or swap + size-adjust for zero CLS */
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}

<!-- Fallback font metrics matched -->
@font-face {
  font-family: 'Inter Fallback';
  src: local('Arial');
  size-adjust: 100.06%;
  ascent-override: 90%;
  descent-override: 25%;
  line-gap-override: 0%;
}
```

---

### 5.2 No Bundle Budget / Code Splitting
**AI Output:**
```tsx
// Single 500kb+ bundle
import HeavyChart from './HeavyChart';
import HeavyEditor from './HeavyEditor';
import HeavyTable from './HeavyTable';
```
**Why It Fails:**
- Initial JS > 170kb gzipped → slow INP, poor LCP
- Unused code downloaded for every route
- No route-level splitting

**Craft Alternative:**
```tsx
// Route-level splitting (React Router / Next.js / TanStack Router)
const HeavyChart = lazy(() => import('./HeavyChart').then(m => ({ default: m.HeavyChart })));
const HeavyEditor = lazy(() => import('./HeavyEditor').then(m => ({ default: m.HeavyEditor })));

// Suspense boundary per route
<Suspense fallback={<ChartSkeleton />}>
  <HeavyChart />
</Suspense>

// Bundle analyzer in CI
// webpack-bundle-analyzer / vite-bundle-analyzer / @next/bundle-analyzer
// Budget: initial < 170kb gz, route chunks < 50kb gz
```

---

### 5.3 No Image Optimization Strategy
**AI Output:**
```tsx
<img src="/huge-raw-photo.jpg" alt="Photo" />
```
**Why It Fails:**
- 2MB+ images on mobile
- No responsive sizes → desktop image on phone
- No modern formats
- No lazy loading

**Craft Alternative:**
```tsx
// Build-time optimization (Sharp, @vercel/og, next/image, vite-imagetools)
// Runtime: <Image /> component with automatic srcset/sizes/AVIF/WebP

<Image
  src="/photo.jpg"
  alt="Descriptive alt"
  width={1200}
  height={800}
  sizes="(max-width: 768px) 100vw, 50vw"
  loading="lazy"
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>
```

---

## 6. TypeScript & Component Patterns

### 6.1 `any` / Loose Types in Props
**AI Output:**
```tsx
interface Props {
  data: any;
  onChange: (value: any) => void;
  className?: string;
  style?: React.CSSProperties;
}
```
**Why It Fails:**
- No IDE autocomplete
- Runtime errors not caught
- Refactoring = fear
- `className`/`style` escape hatch breaks design system

**Craft Alternative:**
```tsx
// Discriminated unions for variants
type ButtonTone = 'primary' | 'secondary' | 'danger' | 'ghost';
type ButtonSize = 'sm' | 'md' | 'lg';

interface ButtonProps extends Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, 'children'> {
  tone?: ButtonTone;
  size?: ButtonSize;
  isLoading?: boolean;
  children: React.ReactNode;
}

// Polymorphic `as` / `asChild` for primitives
interface BoxProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'as'> {
  as?: React.ElementType;
  asChild?: boolean;
}
```

---

### 6.2 No ForwardRef on Primitives
**AI Output:**
```tsx
const Button = ({ children, ...props }) => <button {...props}>{children}</button>;
```
**Why It Fails:**
- Parent cannot focus/measure/access DOM node
- `ref` required for `asChild` pattern, animation libraries, focus management
- Breaks composition

**Craft Alternative:**
```tsx
const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ children, tone = 'primary', size = 'md', isLoading, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={cn(buttonStyles(tone, size), props.className)}
        disabled={props.disabled || isLoading}
        {...props}
      >
        {isLoading ? <Spinner size={size} /> : children}
      </button>
    );
  }
);
Button.displayName = 'Button';
```

---

### 6.3 God Components with 20+ Props
**AI Output:**
```tsx
interface TableProps {
  data: any[];
  columns: any[];
  sortable: boolean;
  filterable: boolean;
  paginated: boolean;
  pageSize: number;
  onSort: (col: string, dir: 'asc'|'desc') => void;
  onFilter: (col: string, value: string) => void;
  onPageChange: (page: number) => void;
  onRowClick: (row: any) => void;
  onSelectionChange: (ids: string[]) => void;
  emptyMessage: string;
  loading: boolean;
  error: string | null;
  stickyHeader: boolean;
  rowHeight: 'sm' | 'md' | 'lg';
  density: 'compact' | 'comfortable';
  showCheckbox: boolean;
  // ... 10 more
}
```
**Why It Fails:**
- Impossible to test, document, extend
- Props coupling — changing one breaks others
- Composition > configuration

**Craft Alternative:**
```tsx
// Composable sub-components
<Table>
  <Table.Header>
    <Table.Row>
      <Table.HeaderCell sortKey="name">Name</Table.HeaderCell>
      <Table.HeaderCell sortKey="email">Email</Table.HeaderCell>
    </Table.Row>
  </Table.Header>
  <Table.Body>
    {data.map(row => (
      <Table.Row key={row.id} onClick={() => select(row.id)}>
        <Table.Cell>{row.name}</Table.Cell>
        <Table.Cell>{row.email}</Table.Cell>
      </Table.Row>
    ))}
  </Table.Body>
</Table>

// Logic hooks separated
const { sortState, setSort } = useTableSort(data);
const { filteredData } = useTableFilter(data, filters);
const { page, setPage, pageData } = usePagination(filteredData, pageSize);
```

---

## 7. Testing & Quality Gates

### 7.1 No Automated Accessibility Testing
**AI Output:**
```json
// package.json
"scripts": { "test": "vitest run" }
```
**Why It Fails:**
- A11y regressions invisible until audit
- Manual testing doesn't scale
- CI must gate on a11y

**Craft Alternative:**
```json
// package.json
"scripts": {
  "test": "vitest run",
  "test:a11y": "vitest run --project=a11y",
  "lint": "eslint . --ext ts,tsx && stylelint '**/*.css'",
  "typecheck": "tsc --noEmit",
  "bundle:analyze": "vite-bundle-analyzer"
}
```
```ts
// vitest.config.ts
export default defineConfig({
  projects: [
    { extends: true, test: { name: 'unit', include: ['**/*.test.ts'] } },
    { extends: true, test: { name: 'a11y', include: ['**/*.a11y.test.tsx'] } },
  ],
});
```
```tsx
// Component.a11y.test.tsx
import { render, screen } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

test('Button has no a11y violations', async () => {
  const { container } = render(<Button>Click</Button>);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

---

## 8. Documentation & Handoff

### 8.1 No Component Documentation / Storybook
**AI Output:**
```tsx
// Button.tsx — no JSDoc, no stories, no usage examples
export const Button = ({ children, ...props }) => <button {...props}>{children}</button>;
```
**Why It Fails:**
- Designers/devs don't know variants, states, composition
- No visual regression testing
- Onboarding = reading source code

**Craft Alternative:**
```tsx
// Button.tsx
/**
 * Primary action button.
 * @example
 * <Button tone="primary" onClick={handleSubmit}>Submit</Button>
 * <Button tone="danger" isLoading>Deleting...</Button>
 */
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(...);
Button.displayName = 'Button';
```

```tsx
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Primitives/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    tone: { control: 'select', options: ['primary', 'secondary', 'danger', 'ghost'] },
    size: { control: 'select', options: ['sm', 'md', 'lg'] },
    isLoading: { control: 'boolean' },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = { args: { tone: 'primary', children: 'Primary' } };
export const Danger: Story = { args: { tone: 'danger', children: 'Delete' } };
export const Loading: Story = { args: { isLoading: true, children: 'Saving' } };
export const AsChild: Story = {
  render: () => (
    <Button asChild><Link href="/settings"><span>Settings</span></Link></Button>
  ),
};
```

---

## Quick Reference: Detection Checklist

Run this against any frontend PR to catch AI patterns:

```bash
# Grep patterns
grep -r "transition-all\|transition: all" src/
grep -r "scale(1.05)\|rotate(" src/
grep -r "backdrop-blur\|backdrop-filter" src/
grep -r "box-shadow.*rgba.*[0-9]" src/
grep -r "z-index: 999\|z-index: 1000\|z-index: 50" src/
grep -r "outline: none\|outline:none" src/
grep -r "className=.*text-.*dark:" src/
grep -r "placeholder=.*[A-Za-z]" src/ --include="*.tsx"
grep -r "<div.*onClick" src/
grep -r "any\]" src/
grep -r "forwardRef" src/ --include="*.tsx" | wc -l  # should match primitive count
```

**Zero tolerance** for the above in crafted frontends.