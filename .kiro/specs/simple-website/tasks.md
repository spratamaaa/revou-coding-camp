# Implementation Plan: Simple Website

## Overview

Implement a self-contained single-page landing site using plain HTML5, CSS3, and Vanilla JavaScript. The three files (`index.html`, `css/style.css`, `js/main.js`) are created incrementally: structure first, then styling, then behaviour, then tests. No build tooling or external dependencies are required.

## Tasks

- [-] 1. Set up project structure and test framework
  - Create the `simple-website/` directory with sub-folders `css/`, `js/`, and `tests/`
  - Initialise a minimal `package.json` and install Vitest + jsdom as dev dependencies (for unit and property tests)
  - Add a `vitest.config.js` that sets the test environment to `jsdom`
  - _Requirements: 1.1, 8.3_

- [ ] 2. Author `index.html` — semantic structure and content
  - [~] 2.1 Create `simple-website/index.html` with full document skeleton
    - `<!DOCTYPE html>`, `<html lang="en">`, UTF-8 charset meta, responsive viewport meta
    - `<link rel="stylesheet" href="css/style.css">` in `<head>`, `<script src="js/main.js">` before `</body>`
    - `<header> > <nav id="navbar">` containing `.nav-brand`, `.nav-toggle` button (with `aria-label` and `aria-expanded="false"`), and `<ul class="nav-links">` with four `<li><a>` anchors: `#hero`, `#about`, `#features`, `#contact`
    - `<main>` with `<section id="hero" class="hero">` (h1, `.hero-sub` paragraph, `.btn.btn-primary.cta-btn` linking to `#about`) and three content sections: `id="about"`, `id="features"`, `id="contact"`, each with an `h2` and placeholder paragraph
    - `<footer>` with copyright notice and `<span id="year"></span>`
    - _Requirements: 1.1, 1.2, 1.3, 2.1, 2.2, 3.1, 3.2, 4.1, 4.2, 5.1, 5.2_

  - [~] 2.2 Write example-based unit tests for HTML structure (`tests/structure.test.js`)
    - Verify semantic elements present: `<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`
    - Verify exactly four nav anchor links (`#hero`, `#about`, `#features`, `#contact`)
    - Verify `.nav-toggle` button has `aria-label` and `aria-expanded` attributes
    - Verify `<span id="year">` exists inside `<footer>`
    - Verify UTF-8 charset meta and viewport meta present
    - _Requirements: 1.1, 1.2, 1.3, 2.2_

  - [~] 2.3 Write property test — nav anchors cover all sections (`tests/navigation.test.js`)
    - **Property 1: Nav anchors cover all sections**
    - For every `<section id="...">` on the page, assert a matching `<a href="#...">` exists in `<nav>`
    - **Validates: Requirements 2.2, 4.2**

- [ ] 3. Author `css/style.css` — tokens, reset, layout, components, responsive
  - [~] 3.1 Write the CSS custom properties block (design tokens on `:root`)
    - Define all color, typography, spacing, breakpoint, and nav-height tokens exactly as specified in the design
    - _Requirements: 8.1, 8.2_

  - [~] 3.2 Write the reset/base rules, navigation bar styles, hero styles, content-section styles, footer styles, and button component styles
    - Reset: `box-sizing: border-box`, zero margin/padding, `html { scroll-behavior: smooth }`
    - Nav: `position: fixed`, full-width, flex row, `z-index: 1000`, `.nav-toggle { display: none }` on desktop
    - Hero: full-viewport-height flex column, centred text, `overflow-x: hidden` on body
    - Content sections: vertical padding via `--space-xl`, max-width 1100 px, alternating surface colour on even sections
    - Footer: surface background, border-top, centred text
    - Button: `.btn` base + `.btn-primary` accent fill with hover state
    - _Requirements: 2.1, 3.2, 4.3, 5.2, 8.1, 8.2, 8.3, 8.4, 9.3_

  - [~] 3.3 Write the responsive `@media (max-width: 768px)` block
    - Show `.nav-toggle`, hide `.nav-links` by default on mobile
    - `.nav-links.is-open` reveals the link list as a full-width column dropdown
    - Stack content sections to single column; adjust padding via `--space-lg` and `--space-sm`
    - _Requirements: 6.1, 6.5, 9.1, 9.2, 9.3_

  - [~] 3.4 Write property test — color contrast ratio ≥ 4.5:1 (`tests/contrast.test.js`)
    - **Property 7: Color contrast ratio is always sufficient**
    - Parse CSS token values for all (foreground, background) pairs applied to body text; compute WCAG relative luminance contrast; assert ratio ≥ 4.5
    - **Validates: Requirements 8.4**

- [~] 4. Checkpoint — verify structure and styles
  - Ensure all tests written so far pass (`vitest --run`). Ask the user if any questions arise.

- [ ] 5. Author `js/main.js` — dynamic year, smooth scroll, mobile nav toggle
  - [~] 5.1 Implement `initYear()`
    - Query `#year`; if present, set `textContent` to `new Date().getFullYear()`
    - _Requirements: 5.1_

  - [~] 5.2 Implement `initSmoothScroll()`
    - Attach a single delegated `click` listener on `document`
    - Use `e.target.closest('a[href^="#"]')` to detect internal anchor clicks
    - Strip `#`, look up target element by `id`; call `scrollIntoView({ behavior: 'smooth' })` if found
    - If not found: call `e.preventDefault()` and `console.warn` with the missing id
    - _Requirements: 2.3, 3.3, 7.1, 7.2, 7.3_

  - [~] 5.3 Implement `initNavToggle()`
    - Query `.nav-toggle` and `.nav-links`; return early if either is absent
    - Toggle button click: `classList.toggle('is-open')` on `.nav-links`; sync `aria-expanded` to new state
    - Nav-link click (delegated on `.nav-links`): remove `is-open`, set `aria-expanded="false"`
    - _Requirements: 6.2, 6.3, 6.4_

  - [~] 5.4 Wire up `DOMContentLoaded` initialiser
    - Add `document.addEventListener('DOMContentLoaded', () => { initYear(); initSmoothScroll(); initNavToggle(); })`
    - _Requirements: 2.3, 5.1, 6.2_

  - [~] 5.5 Write property test — smooth scroll fires for every internal anchor (`tests/navigation.test.js`)
    - **Property 2: Smooth scroll fires for every internal anchor**
    - For any `<a href="#...">` whose target exists, assert `scrollIntoView` is called with `{ behavior: 'smooth' }`
    - **Validates: Requirements 2.3, 7.1**

  - [~] 5.6 Write property test — missing anchor target handled gracefully (`tests/navigation.test.js`)
    - **Property 3: Missing anchor target is handled gracefully**
    - For any anchor whose `href` fragment does not match an existing element, assert no scroll occurs and `console.warn` is emitted
    - **Validates: Requirements 7.3**

  - [~] 5.7 Write property test — nav toggle is a boolean round-trip (`tests/toggle.test.js`)
    - **Property 4: Nav toggle is a boolean round-trip**
    - For any initial nav visibility state, clicking toggle twice returns `.nav-links` to its original `is-open` state and `aria-expanded` to its original value
    - **Validates: Requirements 6.2, 6.3**

  - [~] 5.8 Write property test — nav link click always closes nav (`tests/toggle.test.js`)
    - **Property 5: Nav link click always closes the nav list**
    - For any nav link clicked while `.nav-links.is-open` is present, assert the `is-open` class is removed afterwards
    - **Validates: Requirements 6.4**

- [~] 6. Final checkpoint — all tests pass
  - Run `vitest --run` and confirm all tests pass. Ask the user if any questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster MVP delivery.
- Each task references specific requirements for full traceability.
- Checkpoints (tasks 4 and 6) ensure incremental validation before moving to the next layer.
- Property tests use fast-check (or Vitest's built-in randomisation helpers) with a minimum of 100 iterations per property.
- Unit tests and property tests are complementary — both live in the `tests/` directory.
- The site requires no build step; open `simple-website/index.html` directly in a browser to verify visually.

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1"] },
    { "id": 1, "tasks": ["2.1"] },
    { "id": 2, "tasks": ["2.2", "2.3", "3.1"] },
    { "id": 3, "tasks": ["3.2"] },
    { "id": 4, "tasks": ["3.3", "3.4"] },
    { "id": 5, "tasks": ["5.1", "5.2", "5.3"] },
    { "id": 6, "tasks": ["5.4"] },
    { "id": 7, "tasks": ["5.5", "5.6", "5.7", "5.8"] }
  ]
}
```
