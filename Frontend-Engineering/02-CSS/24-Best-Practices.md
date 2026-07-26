# CSS Best Practices

## Overview

This guide consolidates proven best practices for writing maintainable, performant, and scalable CSS.

## 1. CSS Performance

### Avoid Expensive Selectors

```css
/* ❌ Bad: universal key selector */
.container * { color: blue; }

/* ❌ Bad: overly qualified */
html body .wrapper .content .article p { }

/* ✅ Good: specific class selector */
.article-text { color: blue; }
```

### Use `transform` and `opacity` for Animations

```css
/* ❌ Bad: triggers layout */
.element {
  animation: move 0.3s;
}
@keyframes move {
  from { left: 0; }
  to   { left: 100px; }
}

/* ✅ Good: composited only */
@keyframes move {
  from { transform: translateX(0); }
  to   { transform: translateX(100px); }
}
```

### Containment

```css
/* Tell browser this subtree is self-contained */
.widget {
  contain: layout style paint;
}
```

### Reduce Paint Area

```css
/* ❌ Bad: large shadow on thin element */
.shadow-heavy { box-shadow: 0 0 100px rgba(0,0,0,0.5); }

/* ✅ Good: smaller blur area */
.shadow-light { box-shadow: 0 2px 8px rgba(0,0,0,0.15); }
```

## 2. CSS Organization

### Use a Methodology

- **BEM** for component naming
- **ITCSS** for file structure
- **OOCSS** for reusable objects

### File Structure

```
styles/
├── 01-settings/
│   └── _variables.css
├── 02-tools/
│   └── _mixins.css
├── 03-generic/
│   ├── _reset.css
│   └── _box-sizing.css
├── 04-elements/
│   ├── _typography.css
│   └── _links.css
├── 05-objects/
│   ├── _container.css
│   └── _grid.css
├── 06-components/
│   ├── _button.css
│   └── _card.css
└── 07-utilities/
    └── _helpers.css
```

### Comment Sections

```css
/* =========================================
   COMPONENTS: BUTTON
   ========================================= */

/* =========================================
   STATES
   ========================================= */
```

## 3. Naming Conventions

```css
/* ✅ BEM — clear hierarchy */
.card { }
.card__title { }
.card__image { }
.card--featured { }

/* ✅ SMACSS — prefix-based */
.l-header { }
.c-btn { }
.is-active { }

/* ❌ Avoid: ambiguous names */
.red-box { }
.content { }      /* Too generic */
.main-area { }    /* Unclear */
```

## 4. Maintainability

### Use CSS Custom Properties

```css
:root {
  --color-primary: #007bff;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --font-base: 1rem;
}

.element {
  color: var(--color-primary);
  padding: var(--spacing-md);
}
```

### Keep Specificity Low

```css
/* ❌ Bad: high specificity */
#main .sidebar .widget .title { }

/* ✅ Good: class-based */
.widget-title { }
```

### Avoid `!important`

```css
/* ❌ Bad: breaks cascade */
.error { color: red !important; }

/* ✅ Good: use specificity or reorder */
.error { color: red; }
.form-error .error { color: red; }
/* Or: place later in source */
```

### Don't Over-Nest (Sass/SCSS)

```scss
// ❌ Bad: 4 levels deep
.card {
  .content {
    .body {
      .title { }
    }
  }
}

// ✅ Good: 2-3 levels max
.card { }
.card__content { }
.card__title { }
```

## 5. Reset vs Normalize

### CSS Reset

Removes all default browser styles:

```css
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

/* Or use: https://meyerweb.com/eric/tools/css/reset/ */
```

### Normalize.css

Makes default styles consistent across browsers:

```bash
npm install normalize.css
```

```css
/* main.css */
@import 'normalize.css';
```

**Recommendation:** Use a **minimal reset** + `box-sizing: border-box`:

```css
*, *::before, *::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  -webkit-font-smoothing: antialiased;
}
```

## 6. Critical CSS

Inline critical CSS (above-the-fold styles) in `<head>` for faster first paint:

```html
<head>
  <style>
    /* Critical: header, hero, above-fold styles */
    body { margin: 0; font-family: sans-serif; }
    header { height: 80px; background: white; }
    .hero { padding: 4rem 2rem; font-size: 2rem; }
  </style>
  <link rel="stylesheet" href="/styles/main.css" media="print" onload="this.media='all'">
  <!-- Load non-critical CSS asynchronously -->
</head>
```

## 7. Responsive Best Practices

```css
/* Mobile-first */
.container {
  padding: 1rem;
}

@media (min-width: 768px) {
  .container {
    padding: 2rem;
    max-width: 1200px;
    margin-inline: auto;
  }
}

/* Use logical properties */
.element {
  margin-inline: auto;       /* Instead of margin: 0 auto */
  padding-block: 1rem;      /* Instead of padding-top/bottom */
  border-inline-start: 2px; /* Instead of border-left */
}
```

## 8. Accessibility

```css
/* Respect user preferences */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}

@media (prefers-color-scheme: dark) {
  :root { --bg: #121212; --text: #e0e0e0; }
}

/* Focus indicators */
:focus-visible {
  outline: 2px solid blue;
  outline-offset: 2px;
}

/* Screen-reader only */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  white-space: nowrap;
  border: 0;
}
```

## 9. Avoid Anti-Patterns

```css
/* ❌ Relying on extremely fragile selectors */
div:first-child > ul:last-child li:nth-child(3) { }

/* ❌ Overusing !important */
.error { color: red !important; }

/* ❌ Very large CSS files without organization */
/* Split into logical partials */

/* ❌ Deep nesting (Sass) */
.a { .b { .c { .d { } } } }

/* ✅ Use specific classes instead */

/* ❌ Hacks/comments for browser bugs */
/* .fix-ie { behavior: url('ie-fix.htc'); } — remove legacy */
```

## 10. Tools and Automation

| Tool | Purpose |
|------|---------|
| [Stylelint](https://stylelint.io/) | CSS linter — catch errors, enforce conventions |
| [Prettier](https://prettier.io/) | Auto-formatting |
| [Autoprefixer](https://autoprefixer.github.io/) | Add vendor prefixes (auto via PostCSS) |
| [PurgeCSS](https://purgecss.com/) | Remove unused CSS (via Tailwind/Parcel build) |
| [Lightroom](https://chrome.google.com/webstore/detail/css-used/cdopjfddjlonogibjahpnmjpoangjfff) | Find unused CSS in DevTools |
| [Critical](https://github.com/addyosmani/critical) | Extract/inline critical CSS |

## 11. Metrics to Track

- **Bundle size** — keep CSS under 100KB (gzipped) for decent FCP
- **Render-blocking resources** — inline critical CSS
- **Unused CSS** — use coverage tool in DevTools
- **Layout shifts (CLS)** — ensure dimensions for images/videos
- **Selector performance** — avoid universal/qualified selectors on hot paths

## Quick Checklist

```
✅ Reset box-sizing globally
✅ Use a CSS methodology (BEM)
✅ Keep specificity low (classes only)
✅ Use custom properties for theming
✅ Mobile-first media queries
✅ Prefer transform/opacity for animations
✅ Respect prefers-reduced-motion
✅ Logical properties (inset-block, margin-inline)
✅ CSS variables over preprocessor vars for dynamic values
✅ Lint with Stylelint
✅ Purge unused CSS in production
✅ Contain self-contained widgets
✅ Never use !important (almost never)
```
