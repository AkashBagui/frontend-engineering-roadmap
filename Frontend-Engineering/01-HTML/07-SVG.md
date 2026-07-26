# SVG (Scalable Vector Graphics)

## What is SVG?

SVG is an XML-based vector image format for two-dimensional graphics. Unlike raster formats (PNG, JPG), SVGs are **resolution-independent** — they look sharp at any size.

```
┌──────────┬────────────────────┬────────────────────┐
│          │   Raster (PNG/JPG) │   Vector (SVG)     │
├──────────┼────────────────────┼────────────────────┤
│ Scaling  │ Gets pixelated     │ Stays sharp        │
│ Size     │ Larger = bigger    │ Small file         │
│ Editing  │ Pixel-level only   │ Each element       │
│          │                    │  is editable       │
│ Animation│ Animated PNG/GIF   │ CSS + SMIL         │
│ Colors   │ Fixed at export    │ Change with CSS    │
└──────────┴────────────────────┴────────────────────┘
```

## Inline SVG vs `<img>` SVG

### Method 1: `<img>` Tag

```html
<img src="icon.svg" alt="Settings icon" width="32" height="32">
```

- ✅ Caching works (browser caches the file)
- ✅ Simple to use
- ❌ Cannot style with external CSS
- ❌ Cannot add interactivity
- ❌ No access to SVG internals

### Method 2: Inline SVG (Recommended for UI)

```html
<svg width="32" height="32" viewBox="0 0 32 32"
     xmlns="http://www.w3.org/2000/svg">
    <circle cx="16" cy="16" r="14"
            fill="none" stroke="currentColor"
            stroke-width="2"/>
    <path d="M16 10v6l4 4"
          fill="none" stroke="currentColor"
          stroke-width="2"/>
</svg>
```

- ✅ Style with CSS (`currentColor`, variables)
- ✅ Add event handlers (click, hover)
- ✅ Animations with CSS
- ❌ Increases HTML size

### Method 3: SVG as Background

```css
.icon {
    background-image: url('icon.svg');
    /* Can change color in modern CSS: */
    filter: invert(48%) sepia(79%) saturate(2476%) ...;
}
```

## Basic Shapes

```
┌─────────────────────────────────────────────────────────┐
│  <rect>    <circle>    <ellipse>    <line>    <polygon> │
│                                                        │
│  ┌─────┐     ╭───╮       ╭───╮       ────────    ┌────┐│
│  │     │    ╱     ╲     ╱     ╲     │       │    ╱    ╱│
│  └─────┘   ╲     ╱    ╲     ╱      │       │   ╱   ╱  │
│              ╰───╯      ╰───╯       ────────   ╱  ╱   │
│                                               ╱ ╱    │
│                                              ╱╱     │
└─────────────────────────────────────────────────────────┘
```

### Rectangle

```html
<rect x="10" y="10" width="100" height="50"
      rx="8" ry="8"
      fill="#4A90D9" stroke="#2C5F8A" stroke-width="2"/>
```

### Circle

```html
<circle cx="60" cy="60" r="50"
        fill="#E74C3C" opacity="0.8"/>
```

### Ellipse

```html
<ellipse cx="80" cy="60" rx="70" ry="40"
         fill="#2ECC71"/>
```

### Line

```html
<line x1="10" y1="90" x2="150" y2="10"
      stroke="#9B59B6" stroke-width="3" stroke-linecap="round"/>
```

### Polygon

```html
<polygon points="50,5 100,90 0,90"
         fill="#F1C40F" stroke="#E67E22" stroke-width="2"/>
```

### Path (The most powerful element)

```html
<path d="M10 80 C 40 10, 65 10, 95 80 S 150 150, 180 80"
      fill="none" stroke="#E74C3C" stroke-width="3"/>
```

```
Path Commands:
─────────────
M = Move to (absolute)
m = Move to (relative)
L = Line to
l = Line to (relative)
H = Horizontal line
V = Vertical line
C = Cubic bezier curve
S = Smooth cubic bezier
Q = Quadratic bezier
T = Smooth quadratic bezier
A = Arc
Z = Close path
```

## Text in SVG

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg">
    <rect width="100%" height="100%" fill="#f0f0f0"/>

    <text x="200" y="50"
          text-anchor="middle"
          font-family="Arial"
          font-size="24"
          font-weight="bold"
          fill="#333">
        Hello, SVG!
    </text>

    <text x="200" y="100"
          text-anchor="middle"
          font-size="16"
          fill="#666">
        Scalable Vector Graphics
    </text>

    <!-- Text on path -->
    <defs>
        <path id="curve" d="M 50 150 Q 200 250, 350 150"
              fill="transparent"/>
    </defs>
    <text font-size="14" fill="#E74C3C">
        <textPath href="#curve">
            Text following a curved path in SVG
        </textPath>
    </text>
</svg>
```

## The `viewBox` Attribute

`viewBox` defines the coordinate system of the SVG canvas.

```
viewBox="minX minY width height"
```

```html
<!-- This SVG draws the same content regardless of width/height -->
<svg width="100%" height="400" viewBox="0 0 200 200">
    <circle cx="100" cy="100" r="80" fill="steelblue"/>
</svg>
```

```
Without viewBox:
  width="200" → circle at px 100
  width="400" → circle at px 100 (barely visible)

With viewBox="0 0 200 200":
  width="200" → circle fills half the SVG
  width="400" → circle fills half the SVG (scaled proportionally)
```

## Responsive SVG

```html
<svg viewBox="0 0 100 100"
     xmlns="http://www.w3.org/2000/svg"
     role="img" aria-label="A responsive icon">
    <circle cx="50" cy="50" r="45"
            fill="currentColor"/>
    <path d="M30 50 L50 70 L70 30"
          fill="none" stroke="white"
          stroke-width="6" stroke-linecap="round"/>
</svg>
```

```css
svg {
    width: 100%;
    height: auto;
    max-width: 48px; /* or whatever max you want */
    color: #4A90D9; /* controls currentColor */
}

/* Hover effect with CSS */
svg:hover circle {
    fill: #E74C3C;
    transition: fill 0.3s ease;
}
```

## Styling SVG

### CSS Properties for SVG Elements

| Property | Applies To | Description |
|----------|-----------|-------------|
| `fill` | Shapes, paths | Fill color |
| `stroke` | Shapes, paths | Outline color |
| `stroke-width` | Shapes, paths | Outline thickness |
| `stroke-linecap` | Lines, paths | `butt`, `round`, `square` |
| `stroke-linejoin` | Lines, paths | `miter`, `round`, `bevel` |
| `opacity` | Any | Overall transparency |
| `transform` | Any | Rotate, scale, translate |

### Using `currentColor`

```html
<button class="icon-button">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M12 5v14M5 12h14"/>
    </svg>
    Add Item
</button>
```

```css
.icon-button {
    color: #333; /* Inherited by SVG currentColor */
}
.icon-button:hover {
    color: #0066ff; /* SVG updates automatically */
}
```

## SVG Animation

### CSS Animation

```html
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
    <style>
        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.1); opacity: 0.7; }
        }
        .pulse {
            animation: pulse 2s ease-in-out infinite;
            transform-origin: center;
        }
        @keyframes dash {
            to { stroke-dashoffset: 0; }
        }
        .draw {
            stroke-dasharray: 1000;
            stroke-dashoffset: 1000;
            animation: dash 2s ease forwards;
        }
    </style>

    <circle class="pulse" cx="50" cy="50" r="30"
            fill="#4A90D9"/>
    <path class="draw" d="M35 50 L45 60 L65 40"
          fill="none" stroke="white" stroke-width="4"
          stroke-linecap="round" stroke-linejoin="round"/>
</svg>
```

### Interactive SVG

```html
<svg viewBox="0 0 200 200"
     xmlns="http://www.w3.org/2000/svg"
     onclick="alert('Star clicked!')"
     style="cursor: pointer;">
    <defs>
        <radialGradient id="glow" cx="50%" cy="50%" r="50%">
            <stop offset="0%" stop-color="#fff4a3"/>
            <stop offset="100%" stop-color="#f1c40f"/>
        </radialGradient>
    </defs>

    <polygon points="100,10 130,70 190,75 145,115 160,175 100,140 40,175 55,115 10,75 70,70"
             fill="url(#glow)"
             stroke="#e67e22"
             stroke-width="2">
        <title>Click the star!</title>
    </polygon>
</svg>
```

## Real-World: Animated Logo

```html
<svg viewBox="0 0 200 60" xmlns="http://www.w3.org/2000/svg"
     role="img" aria-label="MyCompany Logo">
    <style>
        .logo-icon { fill: #0066ff; }
        .logo-text { fill: #333; font-family: Arial, sans-serif; font-size: 28px;
                     font-weight: bold; }
        .logo-dot { animation: bounce 1s ease-in-out infinite alternate; }
        @keyframes bounce {
            from { transform: translateY(0); }
            to { transform: translateY(-5px); }
        }
    </style>

    <rect class="logo-icon" x="10" y="15" width="30" height="30" rx="6"/>
    <circle class="logo-dot" cx="25" cy="15" r="4" fill="white"/>

    <text x="50" y="40" class="logo-text">MyCompany</text>
</svg>
```

## Key Takeaways

1. SVG is **resolution-independent** — perfect for responsive design.
2. Use **inline SVG** when you need styling, interactivity, or animation.
3. Use `<img src="file.svg">` for simple, static icons.
4. Always set `viewBox` for proper scaling.
5. Use `currentColor` to inherit text color for icon integration.
6. SVG can be animated with CSS for performant UI effects.

---

**Next:** [08-Canvas.md](08-Canvas.md) — HTML Canvas for 2D graphics and games.
