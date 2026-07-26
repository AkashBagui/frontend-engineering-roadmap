# CSS Transforms

## Overview

The `transform` property modifies the coordinate space of an element — allowing you to **translate** (move), **rotate**, **scale**, and **skew** elements without affecting document flow.

## 2D Transforms

### `translate()`

Moves an element from its current position.

```css
.element {
  transform: translate(50px, 100px);       /* X: 50px, Y: 100px */
  transform: translateX(50px);             /* Move right */
  transform: translateY(-20px);            /* Move up */

  /* Percentage — relative to element's OWN dimensions */
  transform: translate(-50%, -50%);        /* Center: moves half width/height up+left */

  /* Common centering trick */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

### `rotate()`

Rotates around the center point (or `transform-origin`).

```css
.element {
  transform: rotate(45deg);   /* Clockwise */
  transform: rotate(-90deg);  /* Counter-clockwise */
  transform: rotate(1.5rad);  /* Radians */
  transform: rotate(0.25turn);/* Turns (0.25 = 90deg) */
}
```

### `scale()`

Grows or shrinks an element.

```css
.element {
  transform: scale(1.5);             /* 150% size (both axes) */
  transform: scale(2, 0.5);          /* X: 2x, Y: 0.5x */
  transform: scaleX(2);              /* Width doubled */
  transform: scaleY(0.5);            /* Height halved */

  /* Note: opacity is better for hiding */
  transform: scale(0);               /* Collapses to nothing */
}
```

### `skew()`

Slants an element along the X and/or Y axis.

```css
.element {
  transform: skew(10deg);            /* X-axis only */
  transform: skew(10deg, 5deg);      /* X: 10deg, Y: 5deg */
  transform: skewX(10deg);
  transform: skewY(5deg);
}
```

### Shorthand: `matrix()`

Combines all transforms into one 2D affine transformation matrix.

```css
/* matrix(scaleX, skewY, skewX, scaleY, translateX, translateY) */
.element {
  transform: matrix(1, 0, 0, 1, 50, 100);  /* translate(50px, 100px) */
}
```

## `transform-origin`

Defines the point around which transforms are applied.

```css
.element {
  transform-origin: center;            /* Default: 50% 50% */
  transform-origin: top left;
  transform-origin: right bottom;
  transform-origin: 20% 80%;           /* Custom */
  transform-origin: 200px 100px;       /* Pixel values */

  /* Effect on rotation */
  transform: rotate(45deg);
}
```

## Combining Multiple Transforms

```css
.element {
  transform: translateX(50px) rotate(45deg) scale(1.2);
}
```

**Order matters!** Transforms are applied right-to-left:

```css
/* Different results! */
.a { transform: translateX(50px) rotate(45deg); }
.b { transform: rotate(45deg) translateX(50px); }

/* .a: moves 50px then rotates in place */
/* .b: rotates first, then moves 50px in rotated direction */
```

## 3D Transforms

### `perspective`

Creates a 3D space. Can be set on parent (recommended) or inline:

```css
/* On parent — shared perspective for all children */
.scene {
  perspective: 800px;
  perspective-origin: 50% 50%;  /* Vanishing point */
}

/* Inline on element */
.card {
  transform: perspective(800px) rotateY(45deg);
}
```

### `rotateX()`, `rotateY()`, `rotateZ()`

```css
.flip-card:hover .card-inner {
  transform: rotateY(180deg);
}

.tilt {
  transform: rotateX(20deg) rotateY(10deg);
}

.spinner {
  animation: spin-3d 2s linear infinite;
}

@keyframes spin-3d {
  100% { transform: rotateY(360deg); }
}
```

### `translateZ()` and `translate3d()`

```css
.element {
  transform: translateZ(100px);      /* Moves closer to viewer */
  transform: translate3d(50px, 100px, 200px);  /* X, Y, Z */
}

/* 3D card stack effect */
.card { transform: translateZ(0); }
.card:hover { transform: translateZ(50px); }
```

### `scaleZ()` and `scale3d()`

```css
.element {
  transform: scale3d(1.5, 1.5, 2);  /* scale Z to 2x depth */
}
```

### `rotate3d()`

```css
/* rotate3d(x, y, z, angle) — axis vector + angle */
.element {
  transform: rotate3d(1, 1, 0, 45deg);  /* Rotate around diagonal axis */
}
```

## `backface-visibility`

Controls whether the back face of an element is visible when rotated.

```css
.card-front, .card-back {
  backface-visibility: hidden;
  position: absolute;
  width: 100%;
  height: 100%;
}

.card-back {
  transform: rotateY(180deg);  /* Hidden by default */
}

.card:hover .card-front {
  transform: rotateY(180deg);  /* Front faces back */
}
.card:hover .card-back {
  transform: rotateY(0);       /* Back faces front */
}
```

## 3D Transform Diagram

```
scene (perspective: 800px)
┌─────────────────────────────────────────┐
│                                         │
│   ┌─────────────────────────────┐      │
│   │   rotateY(45deg)            │      │
│   │   ────                      │      │
│   │   │   │    (angled into     │      │
│   │   │   │    the screen)     │      │
│   │   ────                      │      │
│   └─────────────────────────────┘      │
│                                         │
│  Viewer ←──────────────── perspective ─│
│                                         │
└─────────────────────────────────────────┘
```

## Real-World Examples

### 3D Card Flip

```css
.card {
  perspective: 1000px;
  width: 300px;
  height: 400px;
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}

.card:hover .card-inner {
  transform: rotateY(180deg);
}

.card-front, .card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.card-back {
  transform: rotateY(180deg);
}
```

### Zoom on Hover

```css
.gallery img {
  transition: transform 0.3s;
}

.gallery img:hover {
  transform: scale(1.1);
}
```

### Parallax Scroll Effect

```css
.parallax-layer {
  transform: translateZ(-100px) scale(1.2);
}

.parallax-scene {
  perspective: 1px;
  overflow-x: hidden;
  overflow-y: auto;
  height: 100vh;
}
```

### Button Press Effect

```css
.button:active {
  transform: scale(0.95);
}
```

## Performance Notes

```css
/* ✅ Good — GPU composited */
transform: translate(100px, 0);
transform: rotate(45deg);
transform: scale(1.2);

/* ⚠️ May cause repaint */
filter: blur(4px);
clip-path: circle(50%);
```

- Use `transform` instead of `top/left/margin` for smooth animations
- `will-change: transform` hints browser to optimize
- 3D transforms create compositor layers (uses GPU memory)

## Browser Support

CSS transforms are supported in all modern browsers. `transform-style: preserve-3d` and `backface-visibility` need testing in very old browsers.
