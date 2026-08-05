# Semantic HTML & Accessibility — Element Selection, Landmarks, ARIA Patterns

Every HTML element has meaning. Use the right element for the job — CSS styles it, semantics define it.

---

## Landmark Regions — Page Structure

Required on every page. Screen readers navigate by landmarks.

```html
<body>
  <a href="#main-content" class="skip-link">Skip to main content</a>

  <header role="banner">
    <!-- Site header: logo, primary navigation, search -->
  </header>

  <nav role="navigation" aria-label="Main navigation">
    <!-- Primary nav -->
  </nav>

  <main id="main-content" role="main">
    <!-- Unique page content -->
  </main>

  <aside role="complementary" aria-label="Sidebar">
    <!-- Related content, not essential to main flow -->
  </aside>

  <footer role="contentinfo">
    <!-- Site footer: legal, links, copyright -->
  </footer>
</body>
```

**Rules:**
- One `<main>` per page
- `<header>`/`<footer>` inside `<main>` = section header/footer, not page
- `<nav>` only for major navigation blocks (not every link list)
- `<aside>` for tangentially related content
- `<section>` needs accessible name (`aria-labelledby` or `aria-label`)

---

## Heading Hierarchy — Document Outline

```html
<h1>Page Title</h1>

<section aria-labelledby="section-1">
  <h2 id="section-1">Section Title</h2>

  <section aria-labelledby="subsection-1">
    <h3 id="subsection-1">Subsection</h3>

    <h4>Detail</h4>
  </section>
</section>
```

**Rules:**
- Single `<h1>` per page (usually in `<main>`)
- No skipped levels (h1 → h2 → h3 → h4)
- Every `<section>`/`<article>`/`<aside>`/`<nav>` should have heading
- If no visible heading → `aria-label` or `aria-labelledby`

---

## Lists — Repeated Items

```html
<!-- Unordered: no sequence -->
<ul role="list">
  <li>Item 1</li>
  <li>Item 2</li>
</ul>

<!-- Ordered: sequence matters -->
<ol>
  <li>Step 1</li>
  <li>Step 2</li>
</ol>

<!-- Description: key/value pairs -->
<dl>
  <dt>Name</dt>
  <dd>Value</dd>
  <dt>Email</dt>
  <dd>user@example.com</dd>
</dl>
```

**Rules:**
- Use `<ul>`/`<ol>`/`<dl>` — not `<div>` soup
- `role="list"` on `<ul>` (Safari removes list semantics without)
- `<li>` direct child of `<ul>`/`<ol>`
- `<dt>` + `<dd>` pairs in `<dl>`

---

## Buttons vs Links — Action vs Navigation

| Element | Purpose | Keyboard | Screen Reader |
|---------|---------|----------|---------------|
| `<button>` | Action (submit, toggle, open modal) | `Enter` / `Space` | "Button, [name]" |
| `<a href>` | Navigation (URL change) | `Enter` | "Link, [name]" |

```html
<!-- Correct: action -->
<button type="button" onClick={openModal}>Open Modal</button>
<button type="submit">Submit Form</button>
<button type="button" onClick={handleDelete}>Delete</button>

<!-- Correct: navigation -->
<a href="/settings">Settings</a>
<Link href="/dashboard">Dashboard</Link>

<!-- Router-aware link that looks like button -->
<Link href="/settings" asChild>
  <Button>Settings</Button>
</Link>
```

**Never:**
- `<button onClick={() => router.push('/x')}>Navigate</button>`
- `<a href="#" onClick={handleAction}>Action</a>`
- `<div onClick={...}>` — not focusable, no semantics

---

## Form Controls — Labels, Errors, Groups

### Label Association (Required)

```html
<!-- Explicit: for + id -->
<label for="email">Email</label>
<input id="email" type="email" name="email" />

<!-- Implicit: wrapping -->
<label>
  Email
  <input type="email" name="email" />
</label>
```

### Required / Invalid / DescribedBy

```html
<label for="email">Email <span aria-hidden="true">*</span></label>
<input
  id="email"
  type="email"
  required
  aria-invalid="true"
  aria-describedby="email-error email-hint"
/>
<p id="email-hint" class="hint">We'll never share your email</p>
<p id="email-error" role="alert">Invalid email format</p>
```

### Fieldset + Legend for Groups

```html
<fieldset>
  <legend>Contact Preference</legend>
  <label><input type="radio" name="contact" value="email" /> Email</label>
  <label><input type="radio" name="contact" value="sms" /> SMS</label>
</fieldset>
```

### Select

```html
<label for="country">Country</label>
<select id="country" name="country">
  <option value="">Select a country</option>
  <option value="br">Brazil</option>
  <option value="us">United States</option>
</select>
```

---

## Interactive Patterns — ARIA Only When Native Fails

### Disclosure (Show/Hide)

```html
<button
  aria-expanded="false"
  aria-controls="panel-1"
  id="disclosure-1"
>
  Show details
</button>
<div id="panel-1" role="region" aria-labelledby="disclosure-1" hidden>
  Content
</div>

<script>
// Toggle aria-expanded, hidden attribute
</script>
```

### Tabs

```html
<div role="tablist" aria-label="Settings">
  <button role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1">Profile</button>
  <button role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1">Notifications</button>
</div>
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1" hidden>Content 1</div>
<div role="tabpanel" id="panel-2" aria-labelledby="tab-2" hidden>Content 2</div>
```

**Keyboard:**
- `Tab` → enters tab list
- `Arrow Left/Right` → moves between tabs (activates)
- `Tab` → enters active panel
- `Shift+Tab` → exits panel to tab list

### Dialog (Modal)

```html
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm Delete</h2>
  <p>This action cannot be undone.</p>
  <button>Cancel</button>
  <button>Delete</button>
</div>
```

**Requirements:**
- Focus trapped inside
- `Escape` closes
- Focus returns to trigger on close
- Background inert (`<dialog>` element handles this natively)

```html
<!-- Native <dialog> — preferred -->
<dialog>
  <form method="dialog">
    <h2>Confirm Delete</h2>
    <p>This action cannot be undone.</p>
    <button value="cancel">Cancel</button>
    <button value="delete" autofocus>Delete</button>
  </form>
</dialog>

<script>
  dialog.showModal(); // opens, traps focus, handles Escape
  dialog.addEventListener('close', () => { /* result in dialog.returnValue */ });
</script>
```

### Menu / Dropdown

```html
<div role="menu" aria-label="User menu">
  <button role="menuitem">Profile</button>
  <button role="menuitem">Settings</button>
  <hr role="separator" />
  <button role="menuitem">Log out</button>
</div>
```

**Keyboard:**
- `Enter`/`Space` → opens
- `Arrow Down/Up` → navigates items
- `Escape` → closes, focus returns to trigger
- `Home`/`End` → first/last
- Type-ahead (letter keys) → jumps to item

### Tooltip

```html
<button aria-describedby="tooltip-1">Label</button>
<div id="tooltip-1" role="tooltip">Helpful hint</div>
```

**Rules:**
- Only on focus/hover (not touch-only)
- No interactive content inside
- Dismiss on `Escape`, focus loss, scroll

---

## Focus Management

### Visible Focus (Mandatory)

```css
:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
  border-radius: var(--radius-sm);
}

/* Component-specific if needed */
.button:focus-visible { /* ... */ }
.input:focus-visible { /* ... */ }
```

### Skip Link

```html
<a href="#main-content" class="skip-link">Skip to main content</a>
```
```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 50%;
  transform: translateX(-50%);
  padding: var(--space-2) var(--space-4);
  background: var(--color-bg);
  color: var(--color-fg);
  z-index: var(--zIndex-toast);
}
.skip-link:focus {
  top: var(--space-4);
}
```

### Focus Trapping (Modals, Drawers)

```ts
// Focus trap utility
function trapFocus(element: HTMLElement) {
  const focusable = element.querySelectorAll(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0] as HTMLElement;
  const last = focusable[focusable.length - 1] as HTMLElement;

  function handleTab(e: KeyboardEvent) {
    if (e.key !== 'Tab') return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault(); last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault(); first.focus();
    }
  }
  element.addEventListener('keydown', handleTab);
  first?.focus();
  return () => element.removeEventListener('keydown', handleTab);
}
```

---

## Live Regions — Dynamic Updates

```html
<!-- Polite: announced after current speech -->
<div aria-live="polite" aria-atomic="true" class="sr-only">
  Form saved successfully
</div>

<!-- Assertive: announced immediately (errors, critical) -->
<div aria-live="assertive" aria-atomic="true" class="sr-only" role="alert">
  Connection lost. Retrying...
</div>
```

**Rules:**
- `aria-atomic="true"` — read entire region, not just changed part
- `role="alert"` = `aria-live="assertive" aria-atomic="true"`
- Only for dynamic content — not static

---

## Images & Media

### Alt Text Decision Tree

```
Is the image purely decorative?          → alt="" (empty, not missing)
Does it convey information?              → alt="Concise description"
Is it a complex chart/diagram?           → alt="Summary" + longdesc or adjacent text
Is it a link/button with only icon?      → aria-label on parent, alt=""
Is it a logo?                            → alt="Company name" (not "Logo")
```

```html
<!-- Decorative -->
<img src="pattern.svg" alt="" aria-hidden="true" />

<!-- Informative -->
<img src="chart.png" alt="Revenue increased 23% YoY, from $2.1M to $2.6M" />

<!-- Complex — summary + data table -->
<figure>
  <img src="complex-chart.png" alt="Revenue by quarter, 2023-2024" />
  <figcaption>See table below for exact values</figcaption>
</figure>
<table>...</table>

<!-- Icon-only button -->
<button aria-label="Search">
  <svg aria-hidden="true"><use href="#search-icon" /></svg>
</button>
```

---

## Reduced Motion

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

**Component-level:**
```tsx
const prefersReducedMotion = useReducedMotion(); // hook

<motion.div
  animate={prefersReducedMotion ? {} : { x: 100 }}
  transition={{ duration: 0.3 }}
/>
```

---

## Color Contrast — WCAG 2.2 AA

| Element | Minimum Ratio |
|---------|---------------|
| Normal text (< 24px / < 18.5px bold) | 4.5:1 |
| Large text (≥ 24px / ≥ 18.5px bold) | 3:1 |
| UI components (borders, icons, focus) | 3:1 |
| Text on gradient/image | Measure worst case |

**Tools:** `axe-core`, `color-contrast-checker`, browser devtools.

---

## Touch Targets

```css
/* Minimum 44×44px (iOS) / 48×48px (Material) */
.touch-target {
  min-height: 44px;
  min-width: 44px;
}

/* For dense UIs — 44px with 8px spacing = 52px center-to-center */
```

---

## Language & Direction

```html
<html lang="pt-BR" dir="ltr">
<!-- or -->
<html lang="en" dir="ltr">
<!-- RTL -->
<html lang="ar" dir="rtl">
```

**Logical properties** (auto-flip in RTL):
```css
/* Instead of margin-left */
margin-inline-start: var(--space-4);

/* Instead of padding-right */
padding-inline-end: var(--space-4);

/* Instead of border-left */
border-inline-start: 1px solid var(--color-border);
```

---

## Quick Reference: Element Cheat Sheet

| Need | Use | Avoid |
|------|-----|-------|
| Page header | `<header>` | `<div class="header">` |
| Navigation | `<nav>` | `<div class="nav">` |
| Main content | `<main>` | `<div class="main">` |
| Sidebar | `<aside>` | `<div class="sidebar">` |
| Footer | `<footer>` | `<div class="footer">` |
| Section with heading | `<section>` | `<div>` |
| Independent content | `<article>` | `<div>` |
| Action | `<button>` | `<a href="#">`, `<div onClick>` |
| Navigation | `<a href>` | `<button onClick={nav}>` |
| List of items | `<ul>`/`<ol>` | `<div>` + `<div>` |
| Key/value | `<dl>` | `<div>` + `<span>` |
| Form group | `<fieldset>` + `<legend>` | `<div>` + `<h3>` |
| Modal | `<dialog>` | `<div role="dialog">` |
| Tooltip | `role="tooltip"` | `title` attribute |
| Decorative image | `alt=""` | `alt="decorative"` |

---

## Anti-Patterns Checklist

- [ ] No `<div onClick>` — use `<button>`
- [ ] No `<a href="#" onClick>` — use `<button>`
- [ ] No `<button>` for navigation — use `<a href>`
- [ ] No `<input>` without `<label>`
- [ ] No `placeholder` as label
- [ ] No `outline: none` without `:focus-visible` replacement
- [ ] No heading skips
- [ ] No `<section>` without accessible name
- [ ] No `role="button"` on non-button — use `<button>`
- [ ] No `aria-label` duplicating visible text
- [ ] No `tabindex="0"` on non-interactive
- [ ] No `tabindex` > 0
- [ ] No `aria-hidden="true"` on focusable element
- [ ] No missing `alt` on `<img>`
- [ ] No `aria-live` on static content