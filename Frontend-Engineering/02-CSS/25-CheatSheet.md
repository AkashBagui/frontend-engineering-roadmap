# CSS Cheat Sheet

## Selectors

```css
*                 /* Universal */
div               /* Element */
.class            /* Class */
#id               /* ID */
[attr]            /* Attribute */
[attr="value"]    /* Exact */
[attr^="val"]     /* Starts with */
[attr$="val"]     /* Ends with */
[attr*="val"]     /* Contains */
:first-child      /* First child */
:last-child       /* Last child */
:nth-child(n)     /* Nth child */
:nth-of-type(n)   /* Nth of type */
:hover            /* Hover state */
:focus            /* Focus state */
:active           /* Active state */
:not(sel)         /* Negation */
:has(sel)         /* Parent selector */
::before          /* Pseudo-element */
::after           /* Pseudo-element */
div p             /* Descendant */
div > p           /* Child */
div + p           /* Adjacent sibling */
div ~ p           /* General sibling */
```

### Specificity (inline, id, class, element)

| Selector | Specificity |
|----------|-------------|
| `*` | 0,0,0,0 |
| `h1` | 0,0,0,1 |
| `p.active` | 0,0,1,1 |
| `#header` | 0,1,0,0 |
| `style=""` | 1,0,0,0 |

## Box Model

```css
box-sizing: content-box;   /* width = content only */
box-sizing: border-box;    /* width = content + padding + border */
```

## Display

| Value | Behavior |
|-------|----------|
| `block` | Full width, new line |
| `inline` | Content width, no height/width |
| `inline-block` | Content width, respects height/width |
| `flex` | 1D layout container |
| `grid` | 2D layout container |
| `none` | Removed from flow |

## Position

| Value | Context |
|-------|---------|
| `static` | Normal flow (default) |
| `relative` | Normal flow, offset from self |
| `absolute` | Removed from flow, relative to positioned ancestor |
| `fixed` | Removed from flow, relative to viewport |
| `sticky` | Normal flow until scroll threshold |

## Flexbox

### Container

```css
display: flex;
flex-direction: row | column | row-reverse | column-reverse;
flex-wrap: nowrap | wrap | wrap-reverse;
justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
align-items: stretch | flex-start | flex-end | center | baseline;
align-content: stretch | flex-start | flex-end | center | space-between | space-around;
gap: 1rem;
```

### Items

```css
flex: 1;                  /* grow: 1, shrink: 1, basis: 0 */
flex: 0 1 auto;           /* default */
flex: 1 0 auto;           /* grow, no shrink */
flex: 0 0 300px;          /* fixed 300px */
align-self: auto | flex-start | flex-end | center | stretch | baseline;
order: 0;                 /* default, -1 before, 1 after */
```

## CSS Grid

### Container

```css
display: grid;
grid-template-columns: repeat(3, 1fr);
grid-template-columns: 1fr 2fr 1fr;
grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
grid-template-rows: auto 1fr auto;
grid-template-areas:
  "header header"
  "main   sidebar"
  "footer footer";
gap: 1rem;
justify-items: start | end | center | stretch;
align-items: start | end | center | stretch;
```

### Items

```css
grid-column: 1 / 3;
grid-column: span 2;
grid-row: 1 / 3;
grid-area: header;        /* Named area */
grid-area: 1 / 1 / 3 / 3; /* row-start / col-start / row-end / col-end */
justify-self: center;
align-self: end;
```

## Units

| Unit | Description |
|------|-------------|
| `px` | Pixels (1/96 inch) |
| `rem` | Root font-size (default 16px) |
| `em` | Parent font-size |
| `%` | Percentage of parent |
| `vw` | 1% of viewport width |
| `vh` | 1% of viewport height |
| `vmin` | Smaller of vw/vh |
| `vmax` | Larger of vw/vh |
| `ch` | Width of "0" character |
| `fr` | Fraction of free space (grid) |
| `cqi` | 1% of container inline size |
| `cqw` | 1% of container width |

## Typography

```css
font-family: system-ui, sans-serif;
font-size: 1rem;          /* Also: px, em, clamp() */
font-weight: 400;         /* 100-900 or normal/bold */
line-height: 1.6;         /* Use unitless */
text-align: left | center | right | justify;
text-decoration: underline | line-through | none;
text-transform: uppercase | lowercase | capitalize;
letter-spacing: 0.05em;
word-spacing: 0.1em;
white-space: nowrap | pre | pre-wrap;
text-overflow: ellipsis;  /* With overflow: hidden + nowrap */
```

## Colors

```css
color: red;                           /* Named */
color: #ff6600;                       /* Hex */
color: rgb(255, 102, 0);             /* RGB */
color: rgba(255, 102, 0, 0.5);       /* RGB + alpha */
color: hsl(24, 100%, 50%);           /* HSL */
color: hsl(24 100% 50% / 0.5);       /* Modern HSL */
color: oklch(60% 0.2 250);           /* OKLCH */
background: linear-gradient(to right, #ff7e5f, #feb47b);
background: radial-gradient(circle, red, blue);
background: conic-gradient(red, yellow, green);
```

## Animation Shorthand

```css
/* @keyframes name | duration | timing | delay | count | direction | fill | play-state */
animation: slide-in 0.5s ease-out 0.2s 1 normal forwards running;

/* Common */
animation: spin 1s linear infinite;
animation: fade-in 0.3s ease both;
```

### Keyframe Example

```css
@keyframes slide-in {
  from { transform: translateX(-100%); opacity: 0; }
  to   { transform: translateX(0); opacity: 1; }
}
```

## Transition Shorthand

```css
/* property | duration | timing | delay */
transition: opacity 0.3s ease;
transition: transform 0.2s ease-out 0.1s;

/* Multiple */
transition: opacity 0.3s, transform 0.2s;
```

## Transform

```css
transform: translate(50px, 100px);       /* Move */
transform: rotate(45deg);                /* Rotate */
transform: scale(1.5);                   /* Scale */
transform: skew(10deg, 5deg);            /* Skew */
transform: translateX(-50%) translateY(-50%);  /* Center */
transform: translate(0) rotate(0) scale(1);    /* Reset */
transform-origin: center;                /* Default */
```

### 3D

```css
transform: perspective(800px) rotateY(45deg);
transform: translateZ(50px);
transform: rotateX(30deg) rotateY(20deg);
backface-visibility: hidden;
transform-style: preserve-3d;
```

## Media Queries

```css
/* Mobile-first breakpoints */
@media (min-width: 768px) { }   /* Tablet */
@media (min-width: 1024px) { }  /* Desktop */
@media (min-width: 1440px) { }  /* Wide */

/* Modern range syntax */
@media (width >= 768px) { }
@media (768px <= width <= 1023px) { }

/* User preferences */
@media (prefers-color-scheme: dark) { }
@media (prefers-reduced-motion: reduce) { }
@media (print) { }
```

## Container Queries

```css
.container { container-type: inline-size; }
@container (min-width: 400px) {
  .child { flex-direction: row; }
}
```

## CSS Variables

```css
:root { --primary: #007bff; --spacing: 1rem; }
.element {
  color: var(--primary);
  margin: var(--spacing, 1rem);  /* With fallback */
}
```

## Responsive Utilities

```css
img { max-width: 100%; height: auto; }
.container { width: min(100% - 2rem, 1200px); margin-inline: auto; }
.sr-only { position: absolute; width: 1px; height: 1px; overflow: hidden; }
```

## Common Snippets

```css
/* Center a block */
.block-center { margin-inline: auto; width: fit-content; }

/* Center with absolute */
.absolute-center {
  position: absolute; top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}

/* Center with flex */
.flex-center { display: flex; justify-content: center; align-items: center; }

/* Center with grid */
.grid-center { display: grid; place-items: center; }

/* Full viewport */
.fullscreen { width: 100vw; height: 100vh; }

/* Truncate text */
.truncate {
  white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
}

/* Hide scrollbar */
.no-scrollbar { scrollbar-width: none; }
.no-scrollbar::-webkit-scrollbar { display: none; }

/* Aspect ratio */
.aspect-16-9 { aspect-ratio: 16 / 9; }
.aspect-square { aspect-ratio: 1 / 1; }

/* Smooth scroll */
html { scroll-behavior: smooth; }
```
