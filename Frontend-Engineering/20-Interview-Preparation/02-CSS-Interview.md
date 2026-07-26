# CSS Interview Questions

## 1. Explain the CSS box model.

**Answer:** The CSS box model describes how every element is rendered as a rectangular box with the following layers (from inside out):

```
┌─────────────────────────────────┐
│          Margin                  │
│  ┌───────────────────────────┐  │
│  │         Border             │  │
│  │  ┌─────────────────────┐  │  │
│  │  │       Padding        │  │  │
│  │  │  ┌───────────────┐  │  │  │
│  │  │  │   Content      │  │  │  │
│  │  │  └───────────────┘  │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

```css
.box {
  width: 300px;
  padding: 20px;   /* Internal space */
  border: 5px solid black; /* Edge border */
  margin: 15px;    /* External space */
}
```

**Box-sizing property:**

- `content-box` (default): `width` applies only to content area. Total width = width + padding + border
- `border-box`: `width` includes content, padding, and border. Total width = width

```css
*, *::before, *::after {
  box-sizing: border-box; /* Preferred global reset */
}
```

## 2. How does CSS specificity work?

**Answer:** Specificity determines which CSS rule is applied when multiple rules target the same element. It's calculated as a 4-part score (a, b, c, d):

| Selector | Score |
|---------|-------|
| Inline styles | (1, 0, 0, 0) |
| ID selectors (#id) | (0, 1, 0, 0) |
| Class, attribute, pseudo-class (.class, [attr], :hover) | (0, 0, 1, 0) |
| Element, pseudo-element (div, ::before) | (0, 0, 0, 1) |

```css
/* Specificity: (0, 0, 0, 1) */
div { color: blue; }

/* Specificity: (0, 0, 1, 0) */
.my-class { color: green; }

/* Specificity: (0, 1, 0, 0) */
#my-id { color: red; }

/* Specificity: (0, 1, 1, 1) */
div.my-class#my-id { color: purple; }

/* Specificity: (1, 0, 0, 0) */
<div style="color: orange;">Text</div>
```

**Important notes:**
- `!important` overrides all specificity but should be avoided
- Universal selector `*`, combinators `+ > ~`, and `:not()` add no specificity
- `:where()` has zero specificity, `:is()` takes highest specificity of its arguments

## 3. What is Flexbox and how does it work?

**Answer:** Flexbox is a one-dimensional layout model that distributes space and aligns content within a container.

```css
.container {
  display: flex;
  flex-direction: row;      /* row | row-reverse | column | column-reverse */
  flex-wrap: wrap;          /* nowrap | wrap | wrap-reverse */
  justify-content: center;  /* flex-start | flex-end | center | space-between | space-around | space-evenly */
  align-items: center;      /* stretch | flex-start | flex-end | center | baseline */
  align-content: center;    /* For multi-line containers */
  gap: 16px;                /* Gap between items */
}

.item {
  flex-grow: 1;     /* Ability to grow (default 0) */
  flex-shrink: 1;   /* Ability to shrink (default 1) */
  flex-basis: auto; /* Initial size (default auto) */
  align-self: center; /* Override align-items for this item */
  order: 0;         /* Visual order */
}
```

**Flex shorthand:** `flex: grow shrink basis` → `flex: 1` means `flex: 1 1 0%`

**Common patterns:**
```css
/* Centering */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Sticky footer */
body { display: flex; flex-direction: column; min-height: 100vh; }
main { flex: 1; }

/* Equal columns */
.columns { display: flex; }
.column { flex: 1; }

/* Holy grail layout */
.holy-grail { display: flex; flex-direction: column; min-height: 100vh; }
.holy-grail main { display: flex; flex: 1; }
.holy-grail nav, .holy-grail aside { flex: 0 0 200px; }
.holy-grail article { flex: 1; }
```

## 4. Explain CSS Grid.

**Answer:** CSS Grid is a two-dimensional layout system that handles both rows and columns simultaneously.

```css
.container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;       /* 3 columns */
  grid-template-rows: auto 1fr auto;         /* 3 rows */
  grid-template-areas:                       /* Named areas */
    "header header header"
    "nav    main   aside"
    "footer footer footer";
  gap: 16px;                                 /* grid-row-gap and grid-column-gap */
  justify-items: stretch;                    /* Horizontal alignment of items */
  align-items: stretch;                      /* Vertical alignment of items */
}
```

**Flexbox vs Grid:**

| Aspect | Flexbox | Grid |
|--------|---------|------|
| Dimensions | 1D (row OR column) | 2D (rows AND columns) |
| Use case | Component-level layout | Page-level layout |
| Content vs layout | Content-first | Layout-first |
| Item sizing | Based on content | Explicit placement |
| Overlap | Not supported | Supported (grid items can overlap) |

## 5. How does CSS positioning work?

**Answer:** The `position` property determines how an element is positioned in the document flow.

```css
/* Default - normal document flow */
.static { position: static; }

/* Relative to its normal position */
.relative {
  position: relative;
  top: 10px; left: 20px;  /* Offset from normal position */
}

/* Relative to nearest positioned ancestor */
.absolute {
  position: absolute;
  top: 0; right: 0;  /* Removed from flow, positioned to parent */
}

/* Relative to viewport */
.fixed {
  position: fixed;
  bottom: 0; left: 0;  /* Stays fixed during scroll */
}

/* Hybrid - acts as relative until scrolled past, then fixes */
.sticky {
  position: sticky;
  top: 0;  /* Sticks to top when scrolled past */
}
```

**Position summary:**

| Value | Positioning context | In document flow? |
|-------|-------------------|-------------------|
| `static` | Normal flow | Yes |
| `relative` | Self (offset from normal position) | Yes (space preserved) |
| `absolute` | Nearest positioned ancestor | No |
| `fixed` | Viewport | No |
| `sticky` | Scroll container | Yes (until threshold) |

## 6. What is z-index and stacking context?

**Answer:** `z-index` controls the stacking order of positioned elements along the Z-axis (depth).

```css
.box1 { position: relative; z-index: 1; }
.box2 { position: absolute; z-index: 10; }  /* Appears above box1 */
.box3 { position: fixed; z-index: 100; }    /* Appears above both */
```

**Creating a stacking context** - A stacking context is a group of elements with a common parent. Elements within a stacking context are stacked together.

A stacking context is created by:
- Root element (`<html>`)
- `position` with `z-index` not `auto`
- `opacity` less than 1
- `transform`, `filter`, `backdrop-filter`, `perspective`, `clip-path`, `mask`
- `isolation: isolate`
- `contain: paint` or `contain: layout`
- `mix-blend-mode` not `normal`

**Critical:** z-index only works within the same stacking context. A child can't stack above elements in a parent's stacking context.

```html
<div style="position: relative; z-index: 1; background: blue;">
  Parent z-index: 1
  <div style="position: absolute; z-index: 999; background: red;">
    Child z-index: 999 (still behind next sibling)
  </div>
</div>
<div style="position: relative; z-index: 2; background: green;">
  Sibling z-index: 2 (appears above red box)
</div>
```

## 7. How do you create responsive designs?

**Answer:** Responsive design ensures websites work across all screen sizes using:

### 1. Fluid Grids (relative units)
```css
.container {
  width: 100%;
  max-width: 1200px;
  padding: 0 1rem;
}

.column {
  flex: 1 1 33.333%; /* Flexible columns */
}
```

### 2. Media Queries
```css
/* Mobile-first approach */
.element { font-size: 16px; }

/* Tablet (>= 768px) */
@media (min-width: 768px) {
  .element { font-size: 18px; }
}

/* Desktop (>= 1024px) */
@media (min-width: 1024px) {
  .element { font-size: 20px; }
}

/* Print styles */
@media print {
  .nav { display: none; }
}

/* Prefers reduced motion */
@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; }
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root { --bg: #1a1a2e; --text: #eee; }
}
```

### 3. Responsive Images
```html
<img srcset="small.jpg 480w, medium.jpg 768w, large.jpg 1200w"
     sizes="(max-width: 600px) 100vw, (max-width: 1200px) 50vw, 33vw"
     src="fallback.jpg" alt="Description">
```

### 4. Viewport meta tag
```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

## 8. Explain CSS animations and transitions.

**Answer:**

### Transitions (state changes)
```css
.button {
  background: blue;
  color: white;
  /* property duration timing-function delay */
  transition: background 0.3s ease, transform 0.2s;
}

.button:hover {
  background: darkblue;
  transform: scale(1.05);
}
```

### Animations (keyframe-based)
```css
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.animated {
  animation: slideIn 0.5s ease-out 0.2s both;
  /* name duration timing-function delay fill-mode */
}
```

**Transition vs Animation:**

| Feature | Transition | Animation |
|---------|-----------|-----------|
| Trigger | State change (hover, focus) | Any (can auto-start) |
| Control | Simple (start to end) | Multiple keyframes |
| Looping | No | Yes |
| Reverse | Only on state revert | Can specify reverse |
| Fill modes | No | Yes (forwards, backwards, both) |

## 9. What's the difference between `visibility: hidden` and `display: none`?

**Answer:**

```css
.hidden {
  visibility: hidden;  /* Element hidden but still occupies space */
  display: none;       /* Element completely removed from flow */
}
```

| | `display: none` | `visibility: hidden` | `opacity: 0` |
|---|----------------|---------------------|-------------|
| Visual | Hidden | Hidden | Invisible |
| Space | Collapses | Preserved | Preserved |
| Events | No | No | Yes (clicks pass through) |
| Accessibility | Hidden from screen readers | Hidden from screen readers | May be read |
| Children | All hidden | Can override with `visibility: visible` | All invisible |
| Animation | Cannot animate to/from | Cannot animate | Can animate |
| Reflow | Triggers reflow | No reflow | No reflow |

## 10. What are pseudo-classes and pseudo-elements?

**Answer:**

### Pseudo-classes (`:`) - Select elements based on state
```css
/* User action */
:hover, :focus, :active, :visited, :link, :focus-within, :focus-visible

/* Form states */
:checked, :disabled, :enabled, :required, :optional, :valid, :invalid, :in-range

/* Position */
:first-child, :last-child, :nth-child(n), :nth-of-type(n), :first-of-type

/* Negation */
:not(.class)

/* Selection */
:is(section, article) h2 {}  /* Matches h2 inside section or article */
:where(.warning) {}           /* Zero specificity */
:has(selector) {}             /* Parent selector - "contains" */
```

### Pseudo-elements (`::`) - Create virtual elements
```css
::before, ::after       /* Content before/after element (needs content property) */
::first-line            /* First line of text */
::first-letter          /* First letter of text */
::selection             /* User-selected text */
::placeholder           /* Input placeholder */
::backdrop              /* Dialog/modal backdrop */
::marker                /* List marker */
```

## 11. What is BEM and how does it help?

**Answer:** BEM (Block Element Modifier) is a CSS naming methodology that creates reusable, maintainable, and scalable CSS.

```css
/* Block - standalone component */
.card { }

/* Element - part of a block (double underscore) */
.card__title { }
.card__body { }
.card__button { }

/* Modifier - variation of block or element (double dash) */
.card--featured { }
.card__button--primary { }
.card__button--disabled { }
```

```html
<div class="card card--featured">
  <h2 class="card__title">Card Title</h2>
  <p class="card__body">Card content here.</p>
  <button class="card__button card__button--primary">Click Me</button>
</div>
```

**Benefits:**
- **Self-documenting:** HTML shows component structure
- **No specificity wars:** All selectors are equally specific (single class)
- **Reusability:** Components are independent and portable
- **Scalability:** Easy to add new components without breaking existing ones
- **Team collaboration:** Clear naming conventions

## 12. How does `calc()`, `min()`, `max()` and `clamp()` work?

**Answer:** CSS math functions enable dynamic calculations:

```css
/* calc() - Basic arithmetic */
.element {
  width: calc(100% - 40px);              /* Subtraction */
  height: calc(100vh - 60px);            /* Viewport calculation */
  font-size: calc(1rem + 2vw);           /* Fluid typography */
  padding: calc(1rem + 0.5vw);           /* Responsive spacing */
}

/* min() - Choose smallest value */
.element {
  width: min(100%, 1200px);              /* Responsive: fill width up to 1200px */
  font-size: min(5vw, 2rem);             /* Scale but cap at 2rem */
}

/* max() - Choose largest value */
.element {
  width: max(300px, 50%);                /* At least 300px, but can grow */
  font-size: max(1rem, 2vw);             /* At least 1rem */
}

/* clamp() - Clamp between min and preferred */
.element {
  font-size: clamp(1rem, 2vw + 1rem, 3rem);  /* Fluid: between 1rem and 3rem */
  width: clamp(300px, 50%, 1200px);           /* Between 300px and 1200px */
}

/* Nesting allowed */
.element {
  width: calc(min(100%, 1200px) - 2rem);
}
```

**Use case - fluid typography:**
```css
/* Fluid typography formula: clamp(min, preferred, max) */
h1 { font-size: clamp(2rem, 4vw + 1rem, 4rem); }
p  { font-size: clamp(1rem, 1vw + 0.5rem, 1.25rem); }
```

## 13. Explain CSS preprocessors (Sass/SCSS) vs PostCSS vs Tailwind.

**Answer:**

### Sass/SCSS
```scss
// Variables, nesting, mixins, functions
$primary: #007bff;

@mixin respond($breakpoint) {
  @if $breakpoint == 'tablet' {
    @media (min-width: 768px) { @content; }
  }
}

.button {
  background: $primary;
  padding: 1rem;
  
  &:hover { opacity: 0.8; }
  
  @include respond('tablet') {
    padding: 1.5rem;
  }
}
```

### PostCSS
- Tool to transform CSS with JavaScript plugins
- Autoprefixer, CSS Modules, future CSS syntax, minification
- No new syntax - processes standard CSS

### Tailwind CSS
```html
<!-- Utility-first framework -->
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Click Me
</button>
```

**Comparison:**

| Feature | Sass/SCSS | PostCSS | Tailwind |
|---------|-----------|---------|----------|
| Type | Preprocessor | Postprocessor | Utility framework |
| Syntax | Extended CSS | Standard CSS | HTML classes |
| Learning curve | Medium | Low | Medium |
| Bundle size | Variable | Variable | Optimized with purge |
| Design system | Custom | Custom | Configurable |

## 14. How do you center a div?

**Answer:** Multiple approaches depending on context:

### Flexbox (most versatile)
```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### Grid (simplest)
```css
.parent {
  display: grid;
  place-items: center; /* Centers both horizontally and vertically */
}
```

### Absolute positioning with transform
```css
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

### Margin auto (horizontal only, with fixed width)
```css
.child {
  width: 300px;
  margin: 0 auto;
}
```

### Text centering (inline content)
```css
.parent {
  text-align: center;
  line-height: 200px; /* Match parent height for vertical */
}
```

### Table method
```css
.parent {
  display: table-cell;
  text-align: center;
  vertical-align: middle;
}
```

## 15. What is container queries?

**Answer:** Container queries allow styling elements based on their container's size rather than the viewport.

```css
/* Define a containment context */
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* Query based on container width */
@container card (min-width: 400px) {
  .card {
    display: flex;
    flex-direction: row;
  }
  
  .card__image {
    width: 200px;
  }
}

@container card (max-width: 399px) {
  .card {
    display: flex;
    flex-direction: column;
  }
  
  .card__image {
    width: 100%;
  }
}
```

```html
<div class="card-container" style="width: 50%;"><!-- Narrow -->
  <div class="card">
    <img class="card__image" src="image.jpg" alt="">
    <div class="card__content">...</div>
  </div>
</div>

<div class="card-container" style="width: 100%;"><!-- Wide -->
  <!-- Same card component, different layout -->
</div>
```

**Benefits over media queries:**
- Components are truly reusable across different layouts
- Responsive based on available space, not viewport
- Ideal for component libraries and design systems

## 16. How does CSS custom properties (variables) work?

**Answer:** CSS custom properties store values for reuse throughout stylesheets.

```css
/* Definition */
:root {
  --primary: #007bff;
  --primary-hover: #0056b3;
  --spacing: 1rem;
  --font-size: 16px;
  --shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  
  /* Theme support */
  --bg: white;
  --text: #333;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a2e;
    --text: #eee;
  }
}

/* Usage */
.button {
  background: var(--primary, blue); /* Fallback to blue if --primary not defined */
  padding: var(--spacing);
  color: var(--text);
  background: var(--bg);
}

/* Scoped override */
.dark-section {
  --bg: #333;
  --text: #eee;
}

/* Dynamic with JavaScript */
document.documentElement.style.setProperty('--primary', '#ff6600');
```

**Benefits:**
- Dynamic - can be changed at runtime (unlike Sass variables)
- Cascade and inheritance - scoped to DOM tree
- Works with media queries, animations, and transitions
- Enables theming without duplicate styles

## 17. What is the difference between `em` and `rem` in depth?

**Answer:**

```css
html { font-size: 16px; } /* 1rem = 16px (root) */

section { font-size: 20px; }

/* em multiplies current element's computed font-size */
section p {
  font-size: 1.5em;   /* 30px (20px × 1.5) */
  padding: 1em;        /* 30px (based on this element's font-size) */
  margin-bottom: 2rem; /* 32px (based on root font-size) */
}

div { font-size: 1.25rem; } /* 20px */

/* Nested em values compound */
.nested-em {
  font-size: 1.5em; /* Compounding problem with nesting */
}
```

**Compound issue with em:**
```html
<ul style="font-size: 1.2em;">
  <li>Level 1 - 1.2em = 19.2px</li>
  <li style="font-size: 1.2em;"> <!-- 23.04px -->
    <ul>
      <li>Level 2 - compounds to 27.65px</li> <!-- Continues growing -->
    </ul>
  </li>
</ul>
```

**Rule of thumb:**
- Use `rem` for spacing, typography, layout (global sizing)
- Use `em` for component-relative sizing (padding relative to font-size)
- Use `px` for borders, shadows, precise elements

## 18. Explain the `display` property values in detail.

**Answer:**

```css
/* Block - takes full width, starts on new line */
display: block;       /* div, p, h1-h6, section */
display: inline;      /* span, a, strong, em - flows inline, no width/height */
display: inline-block; /* Inline flow but can have width/height, margin */
display: none;        /* Removed from document flow */

/* Modern layouts */
display: flex;        /* One-dimensional layout */
display: inline-flex; /* Flex but inline-level container */
display: grid;        /* Two-dimensional layout */
display: inline-grid; /* Grid but inline-level */

/* Table display (for non-table elements) */
display: table;        /* Acts like <table> */
display: table-row;    /* Acts like <tr> */
display: table-cell;   /* Acts like <td> */
display: table-caption; /* Acts like <caption> */

/* Other */
display: list-item;    /* Acts like <li> */
display: flow-root;    /* Creates block formatting context (clearfix alternative) */
display: contents;     /* Container disappears, children become direct children of parent */
display: inherit;      /* Inherits from parent */
```

**`display: contents` example:**
```html
<div style="display: grid; grid-template-columns: 1fr 1fr;">
  <div style="display: contents;"><!-- This wrapper is invisible to grid -->
    <div>Item 1</div>  <!-- These become direct grid children -->
    <div>Item 2</div>
  </div>
</div>
```

## 19. How do you optimize CSS performance?

**Answer:**

### 1. Efficient Selectors
```css
/* Slow - universal selector */
.container * { }

/* Slow - pseudo-selectors in complex selectors */
.container > div:nth-child(odd) ul li { }

/* Fast - class-based */
.container .item { }

/* Fastest - single class */
.item { }
```

### 2. Minimize Reflows and Repaints
```css
/* Causes reflow - avoids */
.element {
  width: 100px;   /* Triggers layout */
  height: 100px;
  margin: 10px;
  border: 1px solid;
}

/* Animating these is cheaper (compositor-only) */
.element {
  transform: scale(1.5);
  opacity: 0.5;
}
```

### 3. Use `will-change` sparingly
```css
.element {
  will-change: transform; /* Hint browser to prepare for changes */
}
```

### 4. Reduce Specificity Overrides
```css
/* Bad - overriding high specificity */
.parent .child .grandchild .target { }

/* Good - single class */
.target { }
```

### 5. Avoid expensive properties
- `border-radius` (especially on large elements)
- `box-shadow` (especially multiple)
- `filter`
- `:nth-child` selectors

### 6. File optimization
- Minification
- Remove unused CSS (PurgeCSS, UnCSS)
- Critical CSS inlining
- Code splitting

## 20. What is the cascade in CSS?

**Answer:** The cascade is the algorithm that determines which CSS rules apply to an element when there are conflicts. The order of importance:

1. **Origin and importance:** !important wins
   - Transition styles
   - !important user agent styles
   - !important user styles
   - !important author styles
   - Animation styles
   - Normal author styles
   - Normal user styles
   - Normal user agent styles

2. **Specificity:** Higher specificity wins

3. **Order of appearance:** Later rules override earlier ones (within same specificity)

4. **Inheritance:** Some properties are inherited by children

```css
/* Example of cascade resolution */
p { color: blue; }       /* Specificity: 0,0,0,1 */
p { color: green; }      /* Later, same specificity - wins */
.text { color: red; }    /* Specificity: 0,0,1,0 - wins over element */
p.text { color: yellow; } /* Specificity: 0,0,1,1 - wins */

/* !important breaks natural cascade (avoid) */
p { color: purple !important; }
```

## 21. Explain `@import` vs `<link>` for CSS.

**Answer:**

```html
<!-- Method 1: Link tag (preferred) -->
<link rel="stylesheet" href="styles.css">

<!-- Method 2: @import inside CSS or <style> -->
<style>
  @import url('styles.css');
  @import 'styles.css';
</style>
```

| Aspect | `<link>` | `@import` |
|--------|---------|-----------|
| Parallel loading | Yes (parallel with other resources) | No (sequential, blocks other downloads) |
| Performance | Better | Worse |
| DOM order | Deterministic | Non-deterministic |
| JavaScript | Can detect load events | Cannot detect easily |
| Conditional loading | Media queries in `media` attribute | Requires JavaScript workaround |
| Browser support | All | All (IE5+) |

**Best practice:** Always use `<link>` for performance. @import can cause render blocking and cascade delays.

## 22. How does `aspect-ratio` property work?

**Answer:** The `aspect-ratio` property sets a preferred aspect ratio for an element.

```css
/* Fixed ratio */
.video-container {
  aspect-ratio: 16 / 9;
}

/* Square */
.avatar {
  aspect-ratio: 1;
}

/* Auto - uses intrinsic dimensions if available */
.image {
  aspect-ratio: auto;
}

/* Combined with max-width for responsive containers */
.responsive-container {
  width: 100%;
  max-width: 800px;
  aspect-ratio: 4 / 3; /* Maintains 4:3 at any width */
}
```

**Before `aspect-ratio` (padding hack):**
```css
/* Old way - requires wrapper */
.container {
  position: relative;
  width: 100%;
  padding-top: 56.25%; /* 16:9 aspect ratio (9/16 = 0.5625) */
}

.container > * {
  position: absolute;
  top: 0; left: 0;
  width: 100%; height: 100%;
}
```

## 23. What are CSS logical properties?

**Answer:** Logical properties handle layout direction-agnostically, adapting to writing modes (LTR, RTL, vertical).

```css
/* Physical properties (don't adapt) */
.element {
  margin-left: 1rem;
  padding-top: 0.5rem;
  border-right: 1px solid;
  width: 100%;
  height: 200px;
}

/* Logical properties (adapt to writing mode) */
.element {
  margin-inline-start: 1rem;  /* Left in LTR, right in RTL */
  padding-block-start: 0.5rem; /* Top in horizontal, right in vertical */
  border-inline-end: 1px solid; /* Right in LTR, left in RTL */
  inline-size: 100%;           /* Horizontal size (width in horizontal) */
  block-size: 200px;           /* Vertical size (height in horizontal) */
}
```

**Mapping:**
| Physical | Logical (for horizontal LTR) |
|---------|------------------------------|
| `width` | `inline-size` |
| `height` | `block-size` |
| `margin-top` | `margin-block-start` |
| `margin-right` | `margin-inline-end` |
| `padding-left` | `padding-inline-start` |
| `border-bottom` | `border-block-end` |
| `top` | `inset-block-start` |
| `left` | `inset-inline-start` |

**Use case - RTL support:**
```css
/* Works for both LTR and RTL without overriding */
.card {
  padding-inline: 1rem;
  margin-block: 0.5rem;
  border-inline-start: 4px solid blue;
}
```

## 24. What is the `contain` property?

**Answer:** The `contain` property tells the browser that an element and its contents are independent from the rest of the DOM tree, enabling optimization.

```css
.element {
  contain: none;      /* No containment (default) */
  contain: layout;    /* Internal layout doesn't affect outside */
  contain: paint;     /* Element's children don't display outside bounds */
  contain: size;      /* Element can be sized without measuring children */
  contain: style;     /* Scoped counters and quotes */
  contain: content;   /* layout + paint + style (not size) */
  contain: strict;    /* layout + paint + size + style */
}
```

**Benefits:**
- **Layout containment:** Browser doesn't need to reflow outside when content changes
- **Paint containment:** Browser clips children to element's bounds
- **Size containment:** Browser can calculate layout without waiting for children to load

**Use cases:**
- Virtual scrolling containers
- Widgets in a dashboard
- Dynamically updated sections
- Off-screen content

## 25. Explain CSS `@layer` and cascade layers.

**Answer:** `@layer` explicitly controls the order of the cascade, letting you define layers with specific priority.

```css
/* Define layers (first declaration wins in priority) */
@layer reset, base, components, utilities;

/* Add styles to layers */
@layer reset {
  * { margin: 0; padding: 0; box-sizing: border-box; }
}

@layer base {
  body { font-family: system-ui; line-height: 1.5; }
}

@layer components {
  .card { background: white; border-radius: 8px; }
}

@layer utilities {
  .text-center { text-align: center; }
}

/* Layer order determines specificity: utilities > components > base > reset */
```

**How cascade layers change specificity:**
```
1. Normal styles (lowest priority)
2. Layer styles (in order of layer declaration)
3. Unlayered styles (higher priority than layers)
4. !important layered styles (reversed layer order)
5. !important unlayered styles
6. Animation
7. Transitions (highest priority)
```

**Key insight:** Layer declarations before they're used establishes priority without needing higher specificity or `!important`.

## 26. How does `scroll-snap` work?

**Answer:** `scroll-snap` creates smooth, snap-to-point scrolling behavior.

```css
/* Container */
.scroll-container {
  scroll-snap-type: x mandatory;       /* x | y | both | block | inline */
  /* mandatory: always snap (more strict) */
  /* proximity: snap near points (more relaxed) */
  overflow-x: auto;
  display: flex;
}

/* Child items */
.scroll-item {
  scroll-snap-align: start;            /* start | center | end */
  min-width: 100%;
  height: 100vh;
  
  /* Padding for offset */
  scroll-padding: 2rem;                /* Snap offset from container edges */
  
  /* Margin between items */
  scroll-margin: 1rem;                 /* Snap offset from item edges */
}
```

```html
<div class="scroll-container">
  <section class="scroll-item" style="background: red;">Slide 1</section>
  <section class="scroll-item" style="background: blue;">Slide 2</section>
  <section class="scroll-item" style="background: green;">Slide 3</section>
</div>
```

**Use cases:** Image carousels, horizontal galleries, full-page scrolling, product showcases

## 27. What is `content-visibility`?

**Answer:** `content-visibility` allows the browser to skip rendering of off-screen elements, dramatically improving performance.

```css
/* Skip rendering of off-screen content */
.lazy-section {
  content-visibility: auto;  /* Browser automatically handles */
  contain-intrinsic-size: 100vh; /* Reserve space (prevents layout shift) */
}

/* Never render */
.hidden-section {
  content-visibility: hidden; /* Like display: none but for layout containment */
}

/* Normal */
.visible-section {
  content-visibility: visible; /* Default */
}
```

**Benefits:**
- **Initial load:** Up to 50% faster rendering on long pages
- **Scroll performance:** Smooth scrolling with deferred rendering
- **Memory:** Reduced memory usage for off-screen content

**How it works:**
- The browser skips rendering (layout, paint, compositing) for off-screen elements
- When the element scrolls near the viewport, it's rendered
- `contain-intrinsic-size` provides an estimated size to prevent scroll jump

## 28. Explain CSS `subgrid`.

**Answer:** `subgrid` allows grid items to inherit grid lines from their parent grid, enabling alignment across nested grids.

```css
.page-grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 1rem;
}

.card {
  grid-column: 2 / 3;
  display: grid;
  grid-template-columns: subgrid; /* Inherit parent's column tracks */
  grid-template-rows: auto 1fr auto;
}

/* Without subgrid - card's children don't align with page grid */
/* With subgrid - card's children use the page grid's column lines */
```

**Without vs With Subgrid:**
```html
<!-- Without subgrid: independent grid tracks -->
<div style="display: grid; grid-template-columns: 1fr 1fr 1fr;">
  <div style="display: grid; grid-template-columns: 1fr 2fr;"><!-- Different tracks -->
    <p>Label</p>
    <p>Value</p>
  </div>
</div>

<!-- With subgrid: aligned with parent -->
<div style="display: grid; grid-template-columns: 1fr 1fr 1fr;">
  <div style="display: grid; grid-template-columns: subgrid;">
    <p style="grid-column: 1;">Label</p>  <!-- Uses parent's column 1 -->
    <p style="grid-column: 2 / 4;">Value</p> <!-- Spans parent's columns 2-3 -->
  </div>
</div>
```

## 29. How does `isolation: isolate` work?

**Answer:** `isolation: isolate` creates a new stacking context, preventing an element from mixing with its backdrop.

```css
/* Creates a new stacking context - protects from z-index war */
.dropdown-menu {
  isolation: isolate;
  z-index: 999; /* Starts a new stacking context, independent of siblings */
}
```

**Versus other stacking context creation:**
```css
/* All create stacking contexts, but isolation is more explicit */
.method-1 { position: relative; z-index: 1; }
.method-2 { opacity: 0.99; }
.method-3 { transform: scale(1); }
.method-4 { isolation: isolate; } /* No side effects */
```

**Use with mix-blend-mode:**
```css
/* Prevent child elements from blending with background */
.blend-container {
  isolation: isolate;
}

.blend-element {
  mix-blend-mode: difference; /* Blends within container but not with page bg */
}
```

## 30. What are hashing/content-addressable CSS techniques?

**Answer:** CSS Modules and CSS-in-JS solutions generate unique class names to prevent naming collisions.

### CSS Modules
```css
/* styles.module.css */
.button {
  background: blue;
  color: white;
  padding: 1rem;
}
```

```javascript
import styles from './styles.module.css';
// Output: <button class="styles_button_1a2b3c">
```

### CSS-in-JS (styled-components)
```javascript
const Button = styled.button`
  background: blue;
  color: white;
  padding: 1rem;
`;
// Generates unique class like sc-bdVaJa
```

### Benefits
- **Scoped styles:** No global naming conflicts
- **Dead code elimination:** Unused styles removed
- **Component isolation:** Changes in one component don't affect others
- **Dynamic styling:** Runtime style computation

### Comparison with BEM

| Aspect | BEM | CSS Modules |
|--------|-----|-------------|
| Scope | Convention-based | Build-time enforced |
| Learning curve | Low | Medium |
| Bundle size | No extra | Minimal hash |
| Dynamic styles | Manual | Built-in |
| Runtime overhead | None | None (hashed at build) |
