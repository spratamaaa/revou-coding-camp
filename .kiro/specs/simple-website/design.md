# Design Document

## Overview

A self-contained single-page landing website implemented in plain HTML5, CSS3, and Vanilla JavaScript — no external frameworks or libraries. The design prioritises semantic markup, a token-based CSS architecture (custom properties), and progressive enhancement: the page is fully readable without JavaScript, and interactivity (smooth scrolling, mobile nav toggle) is layered on top.

---

## Architecture

The site follows a simple three-layer architecture with no build tooling:

- **Structure layer** — `index.html`: a single HTML5 document providing all semantic markup and content.
- **Style layer** — `css/style.css`: a token-first stylesheet using CSS custom properties for all design values, with layout, component, and responsive rules layered on top.
- **Behaviour layer** — `js/main.js`: a plain script (no modules, no bundler) that attaches event listeners for smooth scrolling and the mobile nav toggle after the DOM is ready.

The three layers are strictly separated: HTML carries no inline styles or scripts; CSS contains no behaviour logic; JS manipulates only CSS classes and ARIA attributes, never inline styles.

---

## File Structure

```
revou-coding-camp/
└── simple-website/
    ├── index.html        # Single HTML5 document (all content)
    ├── css/
    │   └── style.css     # All styling — tokens, reset, layout, components, utilities, media queries
    └── js/
        └── main.js       # All behaviour — smooth scroll handler, mobile nav toggle
```

> All three files are authored from scratch. No build step, bundler, or external dependency is required.

---

## HTML Structure

### Document Skeleton

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Site Title</title>
  <link rel="stylesheet" href="css/style.css">
</head>
<body>

  <header>
    <nav id="navbar">
      <div class="nav-brand">Brand</div>
      <button class="nav-toggle" aria-label="Toggle navigation" aria-expanded="false">
        <span class="hamburger"></span>
      </button>
      <ul class="nav-links">
        <li><a href="#hero">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#features">Features</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </nav>
  </header>

  <main>

    <section id="hero" class="hero">
      <h1>Headline</h1>
      <p class="hero-sub">Subheadline copy goes here.</p>
      <a href="#about" class="btn btn-primary cta-btn">Get Started</a>
    </section>

    <section id="about" class="content-section">
      <h2>About</h2>
      <p>About content.</p>
    </section>

    <section id="features" class="content-section">
      <h2>Features</h2>
      <p>Features content.</p>
    </section>

    <section id="contact" class="content-section">
      <h2>Contact</h2>
      <p>Contact content.</p>
    </section>

  </main>

  <footer>
    <p>&copy; <span id="year"></span> Brand Name. All rights reserved.</p>
  </footer>

  <script src="js/main.js"></script>
</body>
</html>
```

### Semantic Element Mapping

| Element       | Role                                           |
|---------------|------------------------------------------------|
| `<header>`    | Contains the fixed navigation bar              |
| `<nav>`       | Navigation bar with anchor links               |
| `<main>`      | All primary page content                       |
| `<section>`   | Hero and each named content section            |
| `<footer>`    | Copyright notice and optional secondary links  |

Each `<section>` carries a unique `id` (`hero`, `about`, `features`, `contact`) that is mirrored by a `href="#<id>"` anchor in `<nav>`.

---

## CSS Architecture

### 1. Custom Properties (Design Tokens)

All visual values are defined once on `:root` and consumed everywhere else.

```css
:root {
  /* Color palette */
  --color-bg:          #ffffff;
  --color-surface:     #f5f5f5;
  --color-border:      #e0e0e0;
  --color-text:        #1a1a1a;   /* contrast ≥ 4.5:1 on --color-bg */
  --color-text-muted:  #555555;   /* contrast ≥ 4.5:1 on --color-bg */
  --color-accent:      #2563eb;   /* contrast ≥ 4.5:1 on --color-bg */
  --color-accent-dark: #1d4ed8;

  /* Typography */
  --font-family:  system-ui, -apple-system, 'Segoe UI', sans-serif;
  --font-size-base: 1rem;

  /* Spacing scale */
  --space-xs:  0.5rem;
  --space-sm:  1rem;
  --space-md:  2rem;
  --space-lg:  4rem;
  --space-xl:  6rem;

  /* Breakpoints (for reference; used in @media queries) */
  --bp-mobile: 768px;

  /* Nav */
  --nav-height: 60px;
}
```

### 2. Reset / Base

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

html { scroll-behavior: smooth; }   /* CSS fallback for smooth scroll */

body {
  font-family: var(--font-family);
  font-size: var(--font-size-base);
  color: var(--color-text);
  background-color: var(--color-bg);
  line-height: 1.6;
  overflow-x: hidden;               /* prevents horizontal scroll */
}
```

### 3. Navigation Bar

```css
nav#navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: var(--nav-height);
  background: var(--color-bg);
  border-bottom: 1px solid var(--color-border);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 var(--space-md);
  z-index: 1000;
}

.nav-links {
  display: flex;
  list-style: none;
  gap: var(--space-sm);
}

.nav-toggle { display: none; }      /* hidden on desktop */
```

### 4. Hero Section

```css
.hero {
  width: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: calc(var(--nav-height) + var(--space-lg)) var(--space-md) var(--space-lg);
  background-color: var(--color-surface);
}
```

### 5. Content Sections

```css
.content-section {
  padding: var(--space-xl) var(--space-md);
  max-width: 1100px;
  margin: 0 auto;
}

/* Alternating surface for visual separation */
.content-section:nth-of-type(even) {
  background-color: var(--color-surface);
}
```

### 6. Footer

```css
footer {
  background-color: var(--color-surface);
  border-top: 1px solid var(--color-border);
  text-align: center;
  padding: var(--space-md);
  color: var(--color-text-muted);
}
```

### 7. Button Component

```css
.btn {
  display: inline-block;
  padding: var(--space-xs) var(--space-md);
  border-radius: 4px;
  font-size: var(--font-size-base);
  font-weight: 600;
  cursor: pointer;
  text-decoration: none;
  transition: background-color 0.2s ease;
}

.btn-primary {
  background-color: var(--color-accent);
  color: #ffffff;
  border: none;
}

.btn-primary:hover {
  background-color: var(--color-accent-dark);
}
```

### 8. Responsive Breakpoints

All responsive rules live in a single `@media` block at the end of `style.css`.

```css
@media (max-width: 768px) {
  /* Show hamburger, hide nav links by default */
  .nav-toggle {
    display: block;
  }

  .nav-links {
    display: none;                  /* hidden until toggled */
    flex-direction: column;
    position: absolute;
    top: var(--nav-height);
    left: 0;
    width: 100%;
    background: var(--color-bg);
    border-bottom: 1px solid var(--color-border);
    padding: var(--space-sm) var(--space-md);
  }

  .nav-links.is-open {
    display: flex;                  /* revealed by JS toggle */
  }

  /* Single-column content */
  .content-section {
    padding: var(--space-lg) var(--space-sm);
  }
}
```

---

## JavaScript Behaviour (`main.js`)

### Module Structure

```
main.js
├── initYear()           — writes current year into #year span
├── initSmoothScroll()   — intercepts all internal anchor clicks
└── initNavToggle()      — manages mobile hamburger toggle
```

### 1. Dynamic Year

```js
function initYear() {
  const yearEl = document.getElementById('year');
  if (yearEl) yearEl.textContent = new Date().getFullYear();
}
```

### 2. Smooth Scroll Handler

Intercepts every `<a href="#...">` click, looks up the target element by `id`, and calls `scrollIntoView`. Falls back gracefully if the target does not exist.

```js
function initSmoothScroll() {
  document.addEventListener('click', (e) => {
    const link = e.target.closest('a[href^="#"]');
    if (!link) return;

    const targetId = link.getAttribute('href').slice(1);   // strip leading '#'
    const targetEl = document.getElementById(targetId);

    if (!targetEl) {
      // Requirement 7.3: log warning, perform no scroll
      console.warn(`Smooth scroll: no element found with id="${targetId}"`);
      e.preventDefault();
      return;
    }

    e.preventDefault();
    targetEl.scrollIntoView({ behavior: 'smooth' });
  });
}
```

**Design notes:**
- A single delegated listener on `document` covers all present and future anchor links.
- `e.target.closest(...)` handles clicks on child elements inside an `<a>`.
- Missing targets produce a `console.warn` and abort — no silent failures.

### 3. Mobile Nav Toggle

Manages the `is-open` class on `.nav-links` and keeps `aria-expanded` in sync.

```js
function initNavToggle() {
  const toggle  = document.querySelector('.nav-toggle');
  const navList = document.querySelector('.nav-links');
  if (!toggle || !navList) return;

  // Open / close on hamburger click
  toggle.addEventListener('click', () => {
    const isOpen = navList.classList.toggle('is-open');
    toggle.setAttribute('aria-expanded', String(isOpen));
  });

  // Close nav when any nav link is clicked (Requirement 6.4)
  navList.addEventListener('click', (e) => {
    if (e.target.closest('a')) {
      navList.classList.remove('is-open');
      toggle.setAttribute('aria-expanded', 'false');
    }
  });
}
```

**Design notes:**
- `classList.toggle` returns the new state, driving `aria-expanded` updates.
- A single delegated listener on `.nav-links` handles all link clicks inside the list.
- The JS is state-agnostic: it reads actual DOM class state rather than tracking a boolean variable, making it resilient to external class manipulation.

### 4. Initialisation

```js
document.addEventListener('DOMContentLoaded', () => {
  initYear();
  initSmoothScroll();
  initNavToggle();
});
```

---

## Components and Interfaces

### HTML Components

| Component          | Element(s)                              | Key Attributes / Classes               |
|--------------------|-----------------------------------------|----------------------------------------|
| Navigation Bar     | `<header> > <nav#navbar>`               | `position: fixed`; wraps brand, toggle, links |
| Nav Toggle Button  | `<button.nav-toggle>`                   | `aria-label`, `aria-expanded`          |
| Nav Link List      | `<ul.nav-links>`                        | Toggled by `.is-open` class            |
| Hero Section       | `<section#hero.hero>`                   | Full-viewport-height, flex-centered    |
| CTA Button         | `<a.btn.btn-primary.cta-btn>`           | `href="#about"` (first content section)|
| Content Section    | `<section.content-section>`             | Unique `id`; matches nav anchor        |
| Footer             | `<footer>`                              | Contains `<span#year>` for dynamic year|

### JavaScript Interfaces

Each function has a clear single responsibility and no shared mutable state.

```
initYear()
  Input:  none
  Effect: writes current year string into DOM element #year
  Output: none

initSmoothScroll()
  Input:  none
  Effect: attaches a delegated 'click' listener on document;
          intercepts <a href="#..."> clicks, calls scrollIntoView or
          console.warn for missing targets
  Output: none

initNavToggle()
  Input:  none
  Effect: attaches 'click' listener on .nav-toggle (open/close);
          attaches delegated 'click' listener on .nav-links (close on link click);
          toggles .is-open class and aria-expanded attribute
  Output: none
```

### CSS Class Contracts

| Class / State        | Meaning                                              |
|----------------------|------------------------------------------------------|
| `.nav-links`         | Navigation list; hidden on mobile by default         |
| `.nav-links.is-open` | Navigation list visible on mobile (added by JS)      |
| `.nav-toggle`        | Hamburger button; hidden on desktop via CSS          |
| `.hero`              | Full-viewport hero section                           |
| `.content-section`   | Generic content section with standard vertical padding|
| `.btn.btn-primary`   | Accent-colored call-to-action button                 |

---

## Data Models

This project has no server-side data layer. The only runtime data is:

| Value           | Source                        | Storage     |
|-----------------|-------------------------------|-------------|
| Current year    | `new Date().getFullYear()`    | DOM text    |
| Nav open state  | `.nav-links.classList`        | DOM class   |

---

## Error Handling

| Scenario                                  | Behaviour                                                                 |
|-------------------------------------------|---------------------------------------------------------------------------|
| Anchor `href` points to missing `id`      | `console.warn` message; default navigation prevented; no scroll attempted |
| `.nav-toggle` or `.nav-links` absent      | `initNavToggle` returns early; no error thrown                            |
| `#year` span absent                       | `initYear` returns early; no error thrown                                 |

---

## Accessibility Considerations

- `<nav>` uses a `<button>` for the toggle (keyboard-accessible by default).
- `aria-expanded` on the toggle button reflects open/closed state for screen readers.
- `aria-label="Toggle navigation"` provides a meaningful name for the button.
- Color contrast ratios for all token pairs are ≥ 4.5:1 (WCAG 2.1 AA), verified at design time against the chosen palette.
- `<main>` landmark enables skip-to-content with screen readers.

---

## Testing Strategy

### Approach

Because the project is pure HTML/CSS/Vanilla JS with no build step, tests are written using a lightweight DOM testing setup (e.g., jsdom + a minimal test runner such as Vitest). There is no server or external dependency to mock for unit/property tests.

**Dual Testing Approach:**

- **Example-based unit tests** — verify specific structural requirements (correct element counts, required attributes, footer year, semantic markup).
- **Property-based tests** — verify universal behavioural invariants (nav/section id matching, smooth scroll dispatch, toggle round-trip, no horizontal overflow, color contrast ratios).

### Test File Layout

```
simple-website/
└── tests/
    ├── structure.test.js      # Example tests: DOM structure, semantic elements, meta tags
    ├── navigation.test.js     # Property tests: nav anchors ↔ section ids, smooth scroll
    ├── toggle.test.js         # Property tests: mobile nav toggle round-trip, link-close
    └── contrast.test.js       # Property tests: color contrast ratio ≥ 4.5:1
```

### Property Test Configuration

- Minimum 100 iterations per property (randomised input generation via fast-check or equivalent).
- Each property test references its design document property using the tag format:
  `Feature: simple-website, Property {N}: {property_title}`

### What Is Not Property-Tested

| Concern                                      | Reason                           | Alternative                         |
|----------------------------------------------|----------------------------------|-------------------------------------|
| CSS `position: fixed` on navbar              | CSS declaration, not logic       | Snapshot / structural check         |
| Responsive layout rendering (single-column)  | Requires real browser layout engine | Visual regression / manual check |
| `scroll-behavior: smooth` CSS declaration    | Static code check                | Smoke test (regex on CSS source)    |
| No external framework links                  | Static code check                | Smoke test (grep on HTML source)    |

---

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system — essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Nav anchors cover all sections

*For any* set of `<section>` elements on the page, each section's `id` SHALL have a corresponding `<a href="#<id>">` element in the navigation bar.

**Validates: Requirements 2.2, 4.2**

---

### Property 2: Smooth scroll fires for every internal anchor

*For any* `<a href="#...">` link present on the page, when clicked, the page SHALL call `scrollIntoView({ behavior: 'smooth' })` on the element whose `id` matches the fragment — provided that element exists.

**Validates: Requirements 2.3, 7.1**

---

### Property 3: Missing anchor target is handled gracefully

*For any* internal anchor link whose `href` fragment does not correspond to an existing element `id`, clicking the link SHALL result in no scroll action and SHALL emit a `console.warn` message.

**Validates: Requirements 7.3**

---

### Property 4: Nav toggle is a boolean round-trip

*For any* initial nav visibility state (open or closed), clicking the Nav Toggle twice SHALL return the nav list to its original visibility state — i.e., `toggle(toggle(state)) == state`.

**Validates: Requirements 6.2, 6.3**

---

### Property 5: Nav link click always closes the nav list

*For any* nav link clicked while the nav list is open, the nav list SHALL transition to the hidden state.

**Validates: Requirements 6.4**

---

### Property 6: No horizontal overflow at any supported viewport width

*For any* viewport width in the range [320 px, ∞), the document body's scroll width SHALL NOT exceed its client width (i.e., no horizontal scrollbar is produced).

**Validates: Requirements 9.3**

---

### Property 7: Color contrast ratio is always sufficient

*For any* (foreground-color, background-color) pair defined by the CSS custom properties and applied to body text, the WCAG relative luminance contrast ratio SHALL be ≥ 4.5:1.

**Validates: Requirements 8.4**
