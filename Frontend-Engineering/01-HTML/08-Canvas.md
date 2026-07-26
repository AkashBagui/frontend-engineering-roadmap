# HTML Canvas

## What is Canvas?

The `<canvas>` element provides a bitmap drawing surface that you control with JavaScript. Unlike SVG (which retains shapes as objects), Canvas is **pixel-based** and **immediate mode** — once you draw something, there is no individual reference to it.

```
┌──────────────────┬────────────────────────┬────────────────────────┐
│                  │        Canvas           │          SVG           │
├──────────────────┼────────────────────────┼────────────────────────┤
│ Rendering        │ Pixel-based (bitmap)    │ Vector (shapes)       │
│ Performance      │ Fast for many objects   │ Better for fewer      │
│                  │ (thousands)             │ objects (< 1000)      │
│ Interactivity    │ Manual (hit detection)  │ Built-in (event       │
│                  │                         │ listeners per element)│
│ DOM Access       │ No individual elements  │ Each shape is a DOM   │
│                  │                         │ node                  │
│ Resolution       │ Blurry when scaled up   │ Sharp at any size     │
│ Use Case         │ Games, data viz,        │ Icons, illustrations, │
│                  │ image processing, video │ logos, diagrams       │
└──────────────────┴────────────────────────┴────────────────────────┘
```

## Basic Setup

```html
<canvas id="myCanvas" width="800" height="600">
    Your browser does not support the canvas element.
</canvas>
```

```javascript
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');
```

### Canvas Coordinate System

```
(0,0) ─────── x increases ──────►
   │
   │   ┌─────────────────────────┐
   │   │                         │
   y   │                         │
   in  │       (x, y)            │
   cr  │         ●               │
   ea  │                         │
   se  │                         │
   s   └─────────────────────────┘
   │                         (width, height)
   ▼
```

## Drawing Shapes

### Rectangle

```javascript
// Filled rectangle
ctx.fillStyle = '#4A90D9';
ctx.fillRect(20, 20, 150, 100);

// Outlined rectangle
ctx.strokeStyle = '#E74C3C';
ctx.lineWidth = 4;
ctx.strokeRect(200, 20, 150, 100);

// Clear rectangle
ctx.clearRect(30, 30, 130, 80);
```

### Circle (Arc)

```javascript
ctx.beginPath();
ctx.arc(400, 200, 80, 0, Math.PI * 2);
ctx.fillStyle = '#2ECC71';
ctx.fill();
ctx.strokeStyle = '#27AE60';
ctx.lineWidth = 3;
ctx.stroke();
```

### Paths

```javascript
// Triangle
ctx.beginPath();
ctx.moveTo(50, 300);
ctx.lineTo(150, 300);
ctx.lineTo(100, 200);
ctx.closePath();
ctx.fillStyle = '#F1C40F';
ctx.fill();

// Curved path
ctx.beginPath();
ctx.moveTo(200, 350);
ctx.quadraticCurveTo(300, 250, 400, 350);
ctx.bezierCurveTo(500, 450, 550, 250, 650, 350);
ctx.strokeStyle = '#9B59B6';
ctx.lineWidth = 3;
ctx.stroke();
```

### Drawing Shapes Table

| Method | Description |
|--------|-------------|
| `fillRect(x, y, w, h)` | Filled rectangle |
| `strokeRect(x, y, w, h)` | Outlined rectangle |
| `clearRect(x, y, w, h)` | Clear area to transparent |
| `arc(x, y, r, startAngle, endAngle)` | Circle / arc |
| `moveTo(x, y)` | Move pen to position |
| `lineTo(x, y)` | Draw line to position |
| `bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)` | Cubic bezier |
| `quadraticCurveTo(cpx, cpy, x, y)` | Quadratic bezier |
| `rect(x, y, w, h)` | Rectangle path (not drawn) |

## Text

```javascript
// Filled text
ctx.font = 'bold 36px Arial';
ctx.fillStyle = '#333';
ctx.fillText('Hello Canvas!', 50, 450);

// Stroked text
ctx.font = '24px Georgia';
ctx.strokeStyle = '#E74C3C';
ctx.lineWidth = 2;
ctx.strokeText('Stroked Text', 50, 500);

// Text with shadow
ctx.shadowColor = 'rgba(0, 0, 0, 0.3)';
ctx.shadowBlur = 8;
ctx.shadowOffsetX = 3;
ctx.shadowOffsetY = 3;
ctx.fillStyle = '#4A90D9';
ctx.font = '32px Arial';
ctx.fillText('Shadowed Text', 50, 550);
ctx.shadowBlur = 0; // Reset
ctx.shadowOffsetX = 0;
ctx.shadowOffsetY = 0;
```

### Text Properties

| Property | Description |
|----------|-------------|
| `font` | CSS-compatible font string |
| `textAlign` | `start`, `end`, `left`, `right`, `center` |
| `textBaseline` | `top`, `hanging`, `middle`, `alphabetic`, `ideographic`, `bottom` |
| `fillText(text, x, y, maxWidth?)` | Draw filled text |
| `strokeText(text, x, y, maxWidth?)` | Draw outlined text |
| `measureText(text)` | Returns TextMetrics (width, etc.) |

## Images

```javascript
const img = new Image();
img.src = 'photo.jpg';
img.onload = function() {
    // Draw image at its original size
    ctx.drawImage(img, 10, 10);

    // Draw scaled image
    ctx.drawImage(img, 10, 10, 200, 150);

    // Draw a portion of the image
    // drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)
    ctx.drawImage(img, 50, 50, 100, 100,  // source crop
                       10, 10, 200, 200); // destination
};
```

### Image Manipulation (Pixel-level)

```javascript
const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
const data = imageData.data;

for (let i = 0; i < data.length; i += 4) {
    // Convert to grayscale
    const gray = 0.299 * data[i] + 0.587 * data[i+1] + 0.114 * data[i+2];
    data[i] = gray;     // Red
    data[i+1] = gray;   // Green
    data[i+2] = gray;   // Blue
    // data[i+3] = alpha (skip — keep original)
}

ctx.putImageData(imageData, 0, 0);
```

## Gradients

```javascript
// Linear gradient
const linearGrad = ctx.createLinearGradient(20, 20, 220, 200);
linearGrad.addColorStop(0, '#4A90D9');
linearGrad.addColorStop(0.5, '#E74C3C');
linearGrad.addColorStop(1, '#F1C40F');
ctx.fillStyle = linearGrad;
ctx.fillRect(20, 20, 200, 200);

// Radial gradient
const radialGrad = ctx.createRadialGradient(400, 100, 10, 400, 100, 80);
radialGrad.addColorStop(0, 'white');
radialGrad.addColorStop(0.5, '#4A90D9');
radialGrad.addColorStop(1, '#2C5F8A');
ctx.fillStyle = radialGrad;
ctx.beginPath();
ctx.arc(400, 100, 80, 0, Math.PI * 2);
ctx.fill();
```

## Transformations

```javascript
ctx.save(); // Save current state

// Translate (move origin)
ctx.translate(400, 300);

// Rotate (radians)
ctx.rotate(Math.PI / 4);

// Scale
ctx.scale(1.5, 1.5);

// Draw rotated/scaled rectangle
ctx.fillStyle = '#E74C3C';
ctx.fillRect(-25, -25, 50, 50);

ctx.restore(); // Restore original state
```

## Animation Loop

```javascript
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');

let x = 0;
let y = 0;
let dx = 3;
let dy = 2;
const radius = 20;

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw ball
    ctx.beginPath();
    ctx.arc(x, y, radius, 0, Math.PI * 2);
    ctx.fillStyle = '#4A90D9';
    ctx.fill();

    // Update position
    x += dx;
    y += dy;

    // Bounce off walls
    if (x + radius > canvas.width || x - radius < 0) dx *= -1;
    if (y + radius > canvas.height || y - radius < 0) dy *= -1;

    requestAnimationFrame(draw);
}

draw();
```

## Simple Game Example: Catch the Square

```html
<canvas id="game" width="400" height="400"
        style="border: 2px solid #333; cursor: pointer;">
    Your browser does not support canvas.
</canvas>
<p>Score: <span id="score">0</span></p>
```

```javascript
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const scoreEl = document.getElementById('score');

const player = { x: 175, y: 350, size: 50, color: '#4A90D9' };
let target = { x: 0, y: 0, size: 30, color: '#E74C3C' };
let score = 0;
let keys = { left: false, right: false };

// Spawn target at random position
function spawnTarget() {
    target.x = Math.random() * (canvas.width - target.size);
    target.y = Math.random() * (canvas.height - target.size - 50);
}

// Draw everything
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Player
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.size, player.size);

    // Target
    ctx.fillStyle = target.color;
    ctx.fillRect(target.x, target.y, target.size, target.size);
}

// Check collision (AABB)
function checkCollision() {
    return player.x < target.x + target.size &&
           player.x + player.size > target.x &&
           player.y < target.y + target.size &&
           player.y + player.size > target.y;
}

// Game loop
function update() {
    // Move player
    if (keys.left && player.x > 0) player.x -= 5;
    if (keys.right && player.x + player.size < canvas.width) player.x += 5;

    // Check collision
    if (checkCollision()) {
        score++;
        scoreEl.textContent = score;
        spawnTarget();
        // Speed up slightly
    }

    draw();
    requestAnimationFrame(update);
}

// Keyboard input
document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft') keys.left = true;
    if (e.key === 'ArrowRight') keys.right = true;
});
document.addEventListener('keyup', (e) => {
    if (e.key === 'ArrowLeft') keys.left = false;
    if (e.key === 'ArrowRight') keys.right = false;
});

// Initialize
spawnTarget();
update();
```

## Real-World: Bar Chart

```html
<canvas id="chart" width="500" height="300" aria-label="Bar chart showing monthly sales">
</canvas>
```

```javascript
const canvas = document.getElementById('chart');
const ctx = canvas.getContext('2d');

const data = [120, 200, 150, 80, 70, 110, 130, 90, 180, 250, 210, 190];
const labels = ['Jan','Feb','Mar','Apr','May','Jun',
                'Jul','Aug','Sep','Oct','Nov','Dec'];
const barWidth = 35;
const gap = 5;
const chartHeight = 250;
const maxVal = Math.max(...data);

// Draw bars
data.forEach((value, i) => {
    const barHeight = (value / maxVal) * chartHeight;
    const x = 20 + i * (barWidth + gap);
    const y = chartHeight - barHeight + 20;

    // Gradient
    const grad = ctx.createLinearGradient(x, y, x, chartHeight + 20);
    grad.addColorStop(0, '#4A90D9');
    grad.addColorStop(1, '#2C5F8A');

    ctx.fillStyle = grad;
    ctx.fillRect(x, y, barWidth, barHeight);

    // Label
    ctx.fillStyle = '#666';
    ctx.font = '10px Arial';
    ctx.textAlign = 'center';
    ctx.fillText(labels[i], x + barWidth / 2, chartHeight + 35);

    // Value
    ctx.fillStyle = '#333';
    ctx.fillText(value, x + barWidth / 2, y - 5);
});
```

## Canvas Performance Tips

- ✅ Use `requestAnimationFrame` for smooth animations
- ✅ Batch draw calls together (minimize `beginPath`/`stroke`)
- ✅ Use `ctx.save()` / `ctx.restore()` sparingly
- ✅ Offload to **OffscreenCanvas** for complex operations
- ✅ Use `willReadFrequently: true` when using `getImageData`
- ❌ Don't clear the entire canvas if only small parts change
- ❌ Don't create new Image objects in animation loops

## Accessibility

Canvas content is **not accessible by default**. Always provide:

```html
<!-- 1. Fallback content -->
<canvas width="400" height="400">
    <p>An animated chart showing sales data for 2026.</p>
</canvas>

<!-- 2. ARIA label -->
<canvas aria-label="Bar chart: monthly sales for 2026"
        role="img">
</canvas>

<!-- 3. Alternative data table for charts -->
<table class="sr-only">
    <caption>Monthly Sales 2026</caption>
    <tr><th>Month</th><th>Sales</th></tr>
    <tr><td>Jan</td><td>$120</td></tr>
    <!-- ... -->
</table>
```

## Key Takeaways

1. Canvas is **pixel-based** — use for games, data viz, image processing.
2. SVG is **shape-based** — use for UI icons, illustrations, logos.
3. Always `beginPath()` before drawing new shapes.
4. Use `requestAnimationFrame` for animations (not `setInterval`).
5. Provide fallback content and ARIA labels for accessibility.
6. Use `ctx.save()`/`restore()` to isolate transformations.

---

**Next:** [09-Meta-Tags.md](09-Meta-Tags.md) — Everything about meta tags.
