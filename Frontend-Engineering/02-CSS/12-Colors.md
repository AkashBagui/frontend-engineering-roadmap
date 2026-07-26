# CSS Colors

## Overview

CSS provides multiple ways to represent colors. Modern CSS has expanded from basic named colors to high-precision color spaces like OKLCH and OKLab.

## Color Formats

### Named Colors

CSS has 148 named colors (including `transparent`):

```css
.primary   { color: red; }
.secondary { color: blue; }
.dark      { color: darkslategray; }
```

Common named colors: `black`, `white`, `gray`, `red`, `green`, `blue`, `orange`, `yellow`, `purple`, `pink`, `brown`, `coral`, `crimson`, `navy`, `teal`, `aqua`, `silver`, `maroon`, `olive`, `lime`, `fuchsia`.

### Hexadecimal (Hex)

```css
.hex-full   { color: #ff6600; }       /* RRGGBB */
.hex-short  { color: #f60; }         /* Shorthand (if pairs) */
.hex-alpha  { color: #ff6600cc; }    /* RRGGBBAA (8-digit) */
.hex-alpha-short { color: #f60c; }   /* RGBA shorthand */
```

### `rgb()` and `rgba()`

```css
.rgb        { color: rgb(255, 102, 0); }
.rgb-percent { color: rgb(100%, 40%, 0%); }
.rgba       { color: rgba(255, 102, 0, 0.8); }  /* 0.8 = 80% opacity */
.rgb-modern { color: rgb(255 102 0 / 0.8); }    /* Modern syntax (spaces) */
```

### `hsl()` and `hsla()`

HSL = Hue (0–360), Saturation (0–100%), Lightness (0–100%).

```css
.hsl       { color: hsl(24, 100%, 50%); }         /* Same orange */
.hsla      { color: hsla(24, 100%, 50%, 0.8); }
.hsl-spaces{ color: hsl(24 100% 50% / 0.8); }

/* Why HSL is intuitive */
.hue-rotate-examples {
  color: hsl(0, 100%, 50%);    /* Red */
  color: hsl(120, 100%, 50%);  /* Green */
  color: hsl(240, 100%, 50%);  /* Blue */
  color: hsl(60, 100%, 50%);   /* Yellow */
}
```

### Alpha Channels (Opacity)

All modern color functions support alpha:

```css
.opacity-examples {
  /* With rgba / hsla */
  color: rgba(0, 0, 0, 0.5);
  background: hsla(200, 50%, 50%, 0.3);

  /* Modern syntax — use ' / ' separator */
  color: rgb(0 0 0 / 0.5);
  background: hsl(200 50% 50% / 0.3);

  /* Hex alpha */
  border-color: #00000080;
}
```

### `currentColor`

Represents the **current** `color` value of the element. Useful for matching borders/shadows to text color.

```css
.button {
  color: #333;
  border: 2px solid currentColor;   /* Border matches text color */
}

.button:hover {
  color: blue;
  /* Border automatically becomes blue */
}
```

## Color Contrast

**WCAG 2.2** requirements:

| Level | Normal Text | Large Text (≥24px or ≥19px bold) |
|-------|-------------|----------------------------------|
| AA    | 4.5:1       | 3:1                              |
| AAA   | 7:1         | 4.5:1                            |

### Tools

- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Chrome DevTools — inspect element → Color Picker shows contrast ratio
- Firefox Accessibility Inspector

```css
/* Ensure sufficient contrast */
body {
  color: #1a1a1a;      /* ~90% black — safe on white */
  background: #fafafa;
}

/* Light text on dark backgrounds */
.dark-card {
  color: #f0f0f0;       /* Not pure white — reduces eye strain */
  background: #1a1a2e;
}
```

## Gradients

### Linear Gradient

```css
.linear-gradient {
  background: linear-gradient(to right, #ff7e5f, #feb47b);
  /* direction: to right, to left, to bottom, to top, 45deg, etc. */
}

.diagonal {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.multi-stop {
  background: linear-gradient(
    to right,
    red 0%,
    orange 25%,
    yellow 50%,
    green 75%,
    blue 100%
  );
}
```

### Radial Gradient

```css
.radial-gradient {
  background: radial-gradient(circle, #ff7e5f, #feb47b);
}

.elliptical {
  background: radial-gradient(ellipse at center, #ffe259, #ffa751);
}
```

### Conic Gradient

```css
.conic-gradient {
  background: conic-gradient(from 0deg, red, yellow, lime, aqua, blue, magenta, red);
}

/* Pie chart */
.pie {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: conic-gradient(
    red 0% 40%,
    blue 40% 70%,
    green 70% 100%
  );
}
```

## Modern Color Spaces (CSS Color Level 4)

### OKLCH

Perceptually uniform color space — excellent for design systems.

```css
.oklch-example {
  color: oklch(0.6 0.2 200);       /* lightness, chroma, hue */
  color: oklch(60% 0.2 200 / 0.8); /* With alpha */
}
```

**Why OKLCH?**
- **Perceptually uniform:** Same delta = same visual difference
- **Predictable lightness:** Changing hue doesn't change perceived brightness
- **Wider gamut:** Can represent P3 colors
- **Better for gradients:** Smoother, no gray dead zone

### OKLab

```css
.oklab-example {
  color: oklab(0.6 0.1 -0.05);    /* lightness, a-axis, b-axis */
}
```

### Color Function

```css
/* Convert to any color space */
.color-function {
  background: color(display-p3 1 0.5 0);   /* P3 color */
  background: color(srgb 1 0.5 0 / 0.5);  /* SRGB with alpha */
}
```

## Comparison Table

| Format | Range | Alpha | Gamut | Perceptually Uniform | Use Case |
|--------|-------|-------|-------|---------------------|----------|
| Named | ~148 | No | sRGB | — | Quick prototyping |
| Hex | 0–255 | 8-digit | sRGB | No | Legacy, simple colors |
| `rgb()` | 0–255 | Yes | sRGB | No | Universal, good support |
| `hsl()` | H:0–360, S/L:0–100% | Yes | sRGB | Partially | Intuitive hue adjustments |
| `hwb()` | H:0–360, W/B:0–100% | Yes | sRGB | No | Simpler than HSL |
| `lch()` | L:0–100, C:0–~230, H:0–360 | Yes | Wider | Yes | Print, perceptually uniform |
| `oklch()` | L:0–1, C:0–~0.4, H:0–360 | Yes | Wider | Yes | **Modern best choice** |
| `color()` | Varies by space | Yes | Display P3, etc. | Varies | Wide gamut displays |

## Creating a Color Palette

```css
:root {
  /* Using OKLCH for perceptual uniformity */
  --color-primary: oklch(55% 0.25 250);
  --color-primary-light: oklch(70% 0.2 250);
  --color-primary-dark: oklch(40% 0.2 250);

  --color-neutral-100: oklch(95% 0 0);
  --color-neutral-300: oklch(80% 0 0);
  --color-neutral-500: oklch(55% 0 0);
  --color-neutral-700: oklch(35% 0 0);
  --color-neutral-900: oklch(15% 0 0);

  /* Semantic colors */
  --color-success: oklch(60% 0.2 140);
  --color-warning: oklch(70% 0.2 80);
  --color-error: oklch(55% 0.2 30);
  --color-info: oklch(60% 0.15 250);
}
```

## Color in DevTools

Chrome DevTools color picker features:
- Inspect element → click color swatch
- Switch between HEX, RGB, HSL, HWB, OKLCH
- Contrast ratio checker with AA/AAA indicators
- Color palette suggestions
- Accessibility annotations

## Accessibility: `prefers-contrast`

```css
@media (prefers-contrast: more) {
  :root {
    --text: #000;
    --bg: #fff;
    --link: #00f;
    --border: 2px solid;
  }
}

@media (prefers-contrast: less) {
  :root {
    --text: #555;
    --bg: #f5f5f5;
  }
}
```
