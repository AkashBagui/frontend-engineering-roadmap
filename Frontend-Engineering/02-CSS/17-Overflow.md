# CSS Overflow

## Overview

The `overflow` property controls what happens when content exceeds an element's box. It is essential for creating scrollable areas, truncating text, and managing layout.

## Overflow Values

```css
.element {
  overflow: visible;   /* Default: content overflows box */
  overflow: hidden;    /* Content clipped, no scrollbar */
  overflow: scroll;    /* Always shows scrollbars */
  overflow: auto;      /* Scrollbars only when needed */
  overflow: clip;      /* Like hidden but no programmatic scrolling */
}
```

### `overflow-x` / `overflow-y`

Control overflow per axis:

```css
.element {
  overflow-x: hidden;   /* Clip horizontal overflow */
  overflow-y: auto;     /* Scroll vertical if needed */
}
```

## Visual Examples

```
overflow: visible (default)
┌──────────────────┐
│ This text is     │
│ inside the box   │ ← content overflows
│ This text overflo│ws
└──────────────────┘
    ws outside the box

overflow: hidden
┌──────────────────┐
│ This text is     │
│ inside the box   │ ← content clipped
│ This text overfl │
└──────────────────┘

overflow: scroll
┌──────────────────┐
│ This text is     │ █ ← scrollbar always visible
│ inside the box   │ █
│ This text overfl │ █
└──────────────────┘

overflow: auto
┌──────────────────┐
│ This text is     │ ┐ scrollbar appears
│ inside the box   │ │ only when needed
│ This text overfl │ │
└──────────────────┘
```

## Creating a Scrollable Container

```css
.scroll-container {
  overflow: auto;              /* Scroll when content overflows */
  max-height: 400px;
  border: 1px solid #ddd;
  padding: 1rem;
}

/* Horizontal scroll for wide content */
.horizontal-scroll {
  overflow-x: auto;
  overflow-y: hidden;
  white-space: nowrap;         /* Prevents wrapping */
}
```

## `text-overflow`

Controls how overflowed text content is displayed.

```css
.truncate {
  white-space: nowrap;          /* Prevent wrapping */
  overflow: hidden;             /* Clip overflow */
  text-overflow: ellipsis;      /* Show "..." */
}

/* Default */
.text-overflow { text-overflow: clip; }       /* Just clips */
.truncated { text-overflow: ellipsis; }        /* Shows … */
```

### Multi-line Truncation

```css
.clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;        /* Show 2 lines max */
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

## `white-space`

Controls how whitespace and line breaks are handled.

| Value | Wrapping | Line Breaks | Spaces/Tabs | Use Case |
|-------|----------|-------------|-------------|----------|
| `normal` | Wrap | Collapse | Collapse | Default |
| `nowrap` | No wrap | Collapse | Collapse | Prevent wrapping |
| `pre` | No wrap | Preserve | Preserve | Code blocks (like `<pre>`) |
| `pre-wrap` | Wrap | Preserve | Preserve | Preserve formatting |
| `pre-line` | Wrap | Preserve | Collapse | Normal wrapping with preserved lines |
| `break-spaces` | Wrap | Preserve | Preserve | Like `pre-wrap` but breaks at spaces |

```css
.code-block {
  white-space: pre-wrap;        /* Preserve indentation, wrap long lines */
  font-family: monospace;
}
```

## `word-break` / `overflow-wrap`

Control how words break when they overflow.

```css
/* Break long words at any character */
.element {
  word-break: break-all;        /* Breaks ANY character */
  word-break: break-word;       /* Deprecated, use overflow-wrap */
}

/* Modern: break long words if they overflow */
.element {
  overflow-wrap: break-word;    /* Breaks only if entire word overflows */
  overflow-wrap: anywhere;      /* Like break-word but with weaker hyphenation */
}

/* Prevent word breaking */
.element {
  word-break: keep-all;         /* CJK text doesn't break */
}
```

### Comparison

```css
.long-url {
  /* URL breaks if needed */
  word-break: break-all;
}

.paragraph {
  /* Normal text with word wrapping */
  overflow-wrap: break-word;
}

.cjk-text {
  /* Chinese/Japanese/Korean — don't break mid-word */
  word-break: keep-all;
}
```

## Scrollbar Styling

### `scrollbar-width` (Firefox)

```css
.element {
  scrollbar-width: thin;      /* Thin scrollbar */
  scrollbar-width: auto;      /* Default */
  scrollbar-width: none;      /* Hide scrollbar (still scrollable) */
}
```

### `scrollbar-color` (Firefox)

```css
.element {
  scrollbar-color: #666 #f0f0f0;  /* thumb track */
}
```

### WebKit Scrollbar (Chrome, Safari, Edge)

```css
/* Modern: ::-webkit-scrollbar */
.custom-scrollbar::-webkit-scrollbar {
  width: 8px;                     /* Vertical scrollbar width */
  height: 8px;                    /* Horizontal scrollbar height */
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background: #555;
}
```

### Scrollbar-gutter

Prevents layout shift when scrollbar appears/disappears:

```css
.element {
  scrollbar-gutter: stable;       /* Reserves scrollbar space */
  scrollbar-gutter: stable both-edges; /* Space on both sides */
}
```

## Common Patterns

### Custom Scrollable Table

```css
.table-wrapper {
  overflow-x: auto;
  max-width: 100%;
}

.table-wrapper table {
  min-width: 600px;  /* Forces scroll on small screens */
}
```

### Scrollable Tabs

```css
.tabs {
  display: flex;
  overflow-x: auto;
  overflow-y: hidden;
  white-space: nowrap;
  -webkit-overflow-scrolling: touch;  /* Smooth scroll on iOS */
  scrollbar-width: none;              /* Firefox: hide scrollbar */
}

.tabs::-webkit-scrollbar {
  display: none;                      /* Chrome/Safari: hide scrollbar but scrollable */
}
```

### Hide Scrollbar but Keep Scrollable

```css
.hide-scrollbar {
  scrollbar-width: none;              /* Firefox */
  -ms-overflow-style: none;           /* IE/Edge legacy */
}

.hide-scrollbar::-webkit-scrollbar {
  display: none;                      /* Chrome/Safari/Edge (Chromium) */
}
```

## Overflow and Block Formatting Context

Setting `overflow` to anything other than `visible` creates a new **Block Formatting Context (BFC)**:

```css
.clearfix {
  overflow: auto;       /* Contains floats */
}

.parent {
  overflow: hidden;     /* Prevents margin collapse with children */
}
```

## Overflow and Positioned Elements

```css
.clip-container {
  overflow: hidden;
  position: relative;          /* Clips absolute children */
}

.clip-container .child {
  position: absolute;
  top: -50px;                  /* Clipped by parent overflow */
}
```

## Performance

- `overflow: hidden` can improve paint performance (no scrollbar painting)
- `overflow: scroll` with large content can be expensive (needs to compute sizes)
- `-webkit-overflow-scrolling: touch` enables momentum scrolling on iOS (important for UX)
