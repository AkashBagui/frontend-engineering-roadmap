# Media Queries

## Overview

**Media queries** allow you to apply CSS rules conditionally based on the characteristics of the device or viewport. They are a core building block of responsive design.

## Syntax

```css
@media media-type and (condition) {
  /* CSS rules */
}
```

### Media Types

| Type | Description |
|------|-------------|
| `all` | All devices (default) |
| `print` | Print preview / printed pages |
| `screen` | Computer screens, tablets, phones |
| `speech` | Screen readers |

```css
/* Applies to screens only */
@media screen { ... }

/* Applies to all media types */
@media (min-width: 768px) { ... }  /* 'all' is implied */

/* Print-specific styles */
@media print { ... }
```

### Logical Operators

#### `and`

```css
/* Both conditions must be true */
@media (min-width: 768px) and (max-width: 1023px) {
  /* Tablet styles */
}
```

#### `or` (comma-separated)

```css
/* Any condition can be true — like CSS OR */
@media (max-width: 600px), (orientation: portrait) {
  /* Applies to small screens OR portrait orientation */
}
```

#### `not`

```css
/* Negation */
@media not (hover: hover) {
  /* Touch devices — no hover capability */
}
```

#### `only`

```css
/* Hides query from older browsers that don't support media queries */
@media only screen and (min-width: 768px) { ... }
```

## Common Media Features

### Viewport Dimensions

```css
/* Width-based */
@Media (min-width: 768px) { }
@Media (max-width: 1023px) { }
@Media (width: 1024px) { }           /* Exact width — rarely used */

/* Height-based */
@Media (min-height: 600px) { }
@Media (max-height: 800px) { }
```

### Device Capabilities

```css
/* Orientation */
@Media (orientation: landscape) { }   /* width > height */
@Media (orientation: portrait) { }    /* height > width */

/* Pointer accuracy */
@Media (pointer: fine) { }            /* Mouse, stylus */
@Media (pointer: coarse) { }          /* Touch finger */
@Media (pointer: none) { }            /* Keyboard-only */

/* Hover capability */
@Media (hover: hover) { }             /* Can hover (mouse) */
@Media (hover: none) { }              /* Cannot hover (touch) */

/* Any-hover — if ANY input can hover */
@Media (any-hover: hover) { }

/* Color */
@Media (color) { }                    /* Any color screen */
@Media (monochrome) { }               /* Monochrome (e.g., e-ink) */
@Media (color-gamut: srgb) { }
@Media (color-gamut: p3) { }          /* Wide-gamut displays */

/* Resolution */
@Media (min-resolution: 2dppx) { }    /* Retina/high-DPI displays */
@Media (min-resolution: 192dpi) { }   /* Dots per inch */
```

### User Preferences (Accessibility)

```css
/* Reduced motion */
@Media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Dark/light mode */
@Media (prefers-color-scheme: dark) {
  :root { --bg: #1a1a2e; --text: #eee; }
}
@Media (prefers-color-scheme: light) {
  :root { --bg: #fff; --text: #222; }
}

/* Reduced transparency */
@Media (prefers-reduced-transparency: reduce) {
  .glass { background: solid; backdrop-filter: none; }
}

/* Contrast preferences */
@Media (prefers-contrast: more) {
  body { color: black; background: white; }
}
@Media (prefers-contrast: less) {
  body { color: #666; }
}

/* Forced colors mode (Windows High Contrast) */
@Media (forced-colors: active) {
  .button { border: 2px solid ButtonText; }
}
```

## Common Breakpoint Ranges

```css
/* Small phones */
@media (max-width: 480px) { }

/* Mobile */
@media (max-width: 767px) { }

/* Tablet */
@media (min-width: 768px) and (max-width: 1023px) { }

/* Desktop */
@media (min-width: 1024px) { }

/* Large desktop */
@media (min-width: 1440px) { }
```

Or with custom properties:

```css
@custom-media --mobile (max-width: 767px);
@custom-media --tablet (min-width: 768px) and (max-width: 1023px);
@custom-media --desktop (min-width: 1024px);

/* Note: @custom-media is a PostCSS feature, not native CSS yet */
```

## Print Styles

Create printer-friendly pages:

```css
@media print {
  /* Remove interactive elements */
  nav, .sidebar, .ads, .video, button { display: none; }

  /* Ensure backgrounds print */
  * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }

  /* Use black text */
  body { color: black; background: white; font-size: 12pt; }

  /* Show links' URLs */
  a[href]::after { content: " (" attr(href) ")"; }
  a[href^="#"]::after { content: ""; }

  /* Page breaks */
  h1, h2, h3 { page-break-after: avoid; }
  article { page-break-inside: avoid; }

  /* Remove fixed positioning */
  .fixed-header { position: static; }
}
```

## Media Query Range Syntax (Modern)

Modern browsers support a cleaner range syntax (2023+):

```css
/* Traditional */
@media (min-width: 768px) and (max-width: 1023px) { }

/* Modern range syntax */
@media (768px <= width <= 1023px) { }

/* Other examples */
@media (width >= 768px) { }         /* min-width: 768px */
@media (width <= 1023px) { }       /* max-width: 1023px */
@media (width > 1024px) { }        /* greater than 1024px */
@media (width < 768px) { }         /* less than 768px */
```

## Container Queries (Brief Intro)

See the **Container Queries** guide for full details. Containers allow queries based on **parent container** size rather than viewport:

```css
@container (min-width: 400px) {
  .card { flex-direction: row; }
}
```

## Performance Tips

- **Order matters:** Put narrowest media queries first in mobile-first approach
- **Avoid duplication:** Group media queries by breakpoint, not by component
- **Don't over-nest:** Media queries inside nested selectors can be hard to maintain
- **Use CSS custom properties** to make responsive style changes easier:

```css
:root { --grid-cols: 1; }
@media (min-width: 768px) { :root { --grid-cols: 2; } }
@media (min-width: 1024px) { :root { --grid-cols: 3; } }

.grid { grid-template-columns: repeat(var(--grid-cols), 1fr); }
```

## Browser Support

All modern media features are widely supported. `prefers-reduced-transparency` is newer (2023+). Range syntax is supported in all modern browsers (2024+).
