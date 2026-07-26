# CSS Transitions

## Overview

**Transitions** allow property changes to occur smoothly over a duration, rather than instantaneously. They are triggered by state changes (hover, focus, class toggle, etc.).

## Transition Properties

### `transition-property`

Specifies which CSS properties to transition:

```css
.element {
  transition-property: all;               /* Transition all animatable properties */
  transition-property: opacity;           /* Only opacity */
  transition-property: transform, opacity;/* Multiple properties */
}
```

**Prefer specific properties over `all`** — better performance and more predictable.

### `transition-duration`

```css
.element {
  transition-duration: 0.3s;    /* Seconds */
  transition-duration: 300ms;   /* Milliseconds */
}
```

### `transition-timing-function`

Controls the speed curve of the transition:

```css
.element {
  transition-timing-function: ease;            /* Default: slow start/end, fast middle */
  transition-timing-function: linear;          /* Constant speed */
  transition-timing-function: ease-in;         /* Slow start, fast end */
  transition-timing-function: ease-out;        /* Fast start, slow end */
  transition-timing-function: ease-in-out;     /* Slow start/end, fast middle */

  /* Cubic bezier — custom curve */
  transition-timing-function: cubic-bezier(0.25, 0.1, 0.25, 1);

  /* Bounce-like effect */
  transition-timing-function: cubic-bezier(0.68, -0.55, 0.27, 1.55);

  /* Step functions */
  transition-timing-function: steps(4, end);
  transition-timing-function: step-start;
  transition-timing-function: step-end;
}
```

### `transition-delay`

```css
.element {
  transition-delay: 0.1s;    /* Wait 0.1s before starting */
  transition-delay: -0.1s;   /* Start 0.1s in (skip part of transition) */
}
```

## `transition` Shorthand

```css
/* property duration timing-function delay */
.element {
  transition: opacity 0.3s ease;
  transition: transform 0.2s ease-out 0.1s;
}

/* Multiple transitions */
.element {
  transition:
    opacity 0.3s ease,
    transform 0.2s ease-out 0.1s;
}
```

## Triggering Transitions

Transitions are automatically triggered when a property changes:

```css
.button {
  background: blue;
  color: white;
  transition: background 0.3s ease, transform 0.2s ease;
}

.button:hover {
  background: darkblue;
  transform: scale(1.05);
}

.button:active {
  transform: scale(0.95);
}
```

### JavaScript Triggering

```js
// Toggle class
element.classList.toggle('is-active');

// Direct style change
element.style.transform = 'translateX(100px)';
```

### CSS Class + Transition

```css
.modal {
  opacity: 0;
  transform: scale(0.9);
  transition: opacity 0.3s ease, transform 0.3s ease;
  pointer-events: none;
}

.modal.open {
  opacity: 1;
  transform: scale(1);
  pointer-events: all;
}
```

## Transitionable Properties

### Safe (GPU-accelerated — cheap)

| Property | Performance |
|----------|-------------|
| `transform` | ✅ Good (composited) |
| `opacity` | ✅ Good (composited) |
| `filter` | ⚠️ Moderate |
| `clip-path` | ⚠️ Moderate |

### Layout-triggering (expensive)

| Property | Performance |
|----------|-------------|
| `width`, `height` | ❌ Triggers layout |
| `top`, `left`, `right`, `bottom` | ❌ Triggers layout |
| `margin`, `padding` | ❌ Triggers layout |
| `border-width` | ❌ Triggers layout |
| `color`, `background-color` | ⚠️ Triggers paint (but no layout) |
| `box-shadow` | ⚠️ Triggers paint |

## Real-World Examples

### Hover Card

```css
.card {
  background: white;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.15);
}
```

### Accordion

```css
.accordion-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s ease-out;
}

.accordion.open .accordion-content {
  max-height: 500px;  /* Large enough to contain content */
  transition-timing-function: ease-in;
}
```

### Off-Canvas Menu

```css
.sidebar {
  transform: translateX(-100%);
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.sidebar.open {
  transform: translateX(0);
}
```

### Staggered List Items

```css
.list-item {
  opacity: 0;
  transform: translateX(-20px);
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.list-item:nth-child(1) { transition-delay: 0ms; }
.list-item:nth-child(2) { transition-delay: 50ms; }
.list-item:nth-child(3) { transition-delay: 100ms; }
.list-item:nth-child(4) { transition-delay: 150ms; }

.list.visible .list-item {
  opacity: 1;
  transform: translateX(0);
}
```

## Performance Tips

```css
/* ✅ Best: transform and opacity only */
.element {
  transition: transform 0.3s, opacity 0.3s;
}

/* ⚠️ OK: background-color (paint only) */
.element {
  transition: background-color 0.3s;
}

/* ❌ Worst: triggers layout */
.element {
  transition: width 0.3s, height 0.3s, left 0.3s;
}

/* Use transform instead of position changes */
/* Instead of */
.element { left: 0; }
.element.active { left: 100px; }

/* Use */
.element { transform: translateX(0); }
.element.active { transform: translateX(100px); }
```

## Transition vs Animation

| Feature | Transitions | Animations |
|---------|-------------|------------|
| Trigger | State change (hover, class) | Can start automatically |
| Control | Start to end (2 states) | Multiple keyframes |
| Loop | No | Yes (`infinite`) |
| Direction | One way | `normal`, `reverse`, `alternate` |
| Pause | No | Yes (`play-state`) |
| Use Case | Hover effects, simple state changes | Loading spinners, complex sequences |

## Browser Support

CSS Transitions are supported in all modern browsers. Part of CSS Transitions Level 1 (W3C Working Draft).
