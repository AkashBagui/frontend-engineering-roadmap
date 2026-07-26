# Whiteboard

**Difficulty:** Hard | **Est. Time:** 60–90 min

---

## Problem Statement

Build a collaborative whiteboard application where users can draw shapes (rectangles, circles, lines), freehand draw, change colors, and undo/redo actions. The whiteboard should be performant and responsive.

---

## Requirements

### Functional
- [ ] Freehand drawing (pencil tool — draw with mouse/touch)
- [ ] Draw shapes: rectangle, circle/ellipse, line, arrow
- [ ] Color picker to change stroke color
- [ ] Stroke width control (thin to thick)
- [ ] Undo / Redo (infinite stack)
- [ ] Clear entire canvas
- [ ] Select and delete individual elements
- [ ] Background grid or plain white background

### Non-Functional
- [ ] Smooth drawing at 60fps
- [ ] No visible lag on freehand strokes
- [ ] Handle window resize without losing content
- [ ] Responsive canvas (fills container)

---

## Canvas vs SVG

| Approach | Pros | Cons |
|----------|------|------|
| **Canvas 2D** | High performance for many elements, pixel manipulation | No individual element events; manual hit-testing; blurry at high DPI |
| **SVG** | Individual elements are DOM nodes; easy events; sharp at any DPI | Slower with 1000+ elements; more memory |
| **Hybrid** | Canvas for freehand, SVG for shapes | Complex coordination |

**For interview:** Canvas is typical for whiteboard. Use a retained-mode rendering approach (store elements as data, re-render on change).

---

## Component Architecture

```
App
├── Toolbar
│   ├── ToolPicker (Select | Pencil | Rectangle | Circle | Line | Arrow)
│   ├── ColorPicker
│   ├── StrokeWidthSlider
│   ├── UndoButton
│   ├── RedoButton
│   └── ClearButton
├── Canvas (single canvas element)
└── StatusBar
    └── Coordinates (mouse X, Y)
```

---

## Pointer Events

Use **pointer events** (pointerdown, pointermove, pointerup) for unified mouse+touch handling:

```js
<canvas
  onPointerDown={handlePointerDown}
  onPointerMove={handlePointerMove}
  onPointerUp={handlePointerUp}
  style={{ touchAction: 'none' }}  // prevent scroll while drawing
/>
```

---

## State Management

```js
const [elements, setElements] = useState([]);       // array of drawing elements
const [history, setHistory] = useState([[]]);        // undo stack of element arrays
const [historyIndex, setHistoryIndex] = useState(0);
const [activeTool, setActiveTool] = useState('pencil');
const [activeColor, setActiveColor] = useState('#000000');
const [strokeWidth, setStrokeWidth] = useState(2);
const [isDrawing, setIsDrawing] = useState(false);
const [currentElement, setCurrentElement] = useState(null);  // element being drawn
```

### Element Shape

```js
{
  id: 'el_1',
  type: 'rectangle',     // 'pencil' | 'rectangle' | 'circle' | 'line' | 'arrow'
  x: 50, y: 50,
  width: 100, height: 80,
  points: [],            // for pencil: array of [{x, y}]
  color: '#ff0000',
  strokeWidth: 3,
  rotation: 0            // optional
}
```

---

## Implementation Steps

1. Create canvas element with ResizeObserver to match container size
2. Set up HiDPI canvas (multiply by devicePixelRatio)
3. Implement tool selection UI (toolbar)
4. Implement freehand drawing (store points array, draw lines between them)
5. Implement shape drawing (rectangle: mousedown → drag → mouseup defines bounds)
6. Implement circle drawing (center = mousedown, radius = distance to mouse)
7. Implement line/arrow drawing (start → end points)
8. Add color picker and stroke width slider
9. Implement render loop: clear canvas → draw all elements → draw current element
10. Implement undo/redo: snapshot element array before each action, push to history stack
11. Implement element selection (hit-test on pointerdown near element bounds)
12. Implement element deletion (select → Delete key or button)
13. Handle edge cases: empty canvas, rapid drawing, window resize

---

## Code Snippets

### HiDPI Canvas Setup

```js
function setupCanvas(canvas, ctx) {
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  canvas.width = rect.width * dpr;
  canvas.height = rect.height * dpr;
  ctx.scale(dpr, dpr);
  canvas.style.width = `${rect.width}px`;
  canvas.style.height = `${rect.height}px`;
}
```

### Drawing a Rectangle

```js
function drawRectangle(ctx, el) {
  ctx.strokeStyle = el.color;
  ctx.lineWidth = el.strokeWidth;
  ctx.strokeRect(el.x, el.y, el.width, el.height);
}
```

### Freehand Rendering

```js
function drawPencil(ctx, el) {
  if (el.points.length < 2) return;
  ctx.strokeStyle = el.color;
  ctx.lineWidth = el.strokeWidth;
  ctx.lineCap = 'round';
  ctx.lineJoin = 'round';
  ctx.beginPath();
  ctx.moveTo(el.points[0].x, el.points[0].y);
  for (let i = 1; i < el.points.length; i++) {
    ctx.lineTo(el.points[i].x, el.points[i].y);
  }
  ctx.stroke();
}
```

### Hit Testing for Selection

```js
function hitTest(x, y, elements) {
  // Reverse order (draw on top = select first)
  for (let i = elements.length - 1; i >= 0; i--) {
    const el = elements[i];
    const bbox = getBoundingBox(el);
    const margin = 5; // tolerance
    if (x >= bbox.x - margin && x <= bbox.x + bbox.width + margin &&
        y >= bbox.y - margin && y <= bbox.y + bbox.height + margin) {
      return el;
    }
  }
  return null;
}
```

### Undo/Redo (History Stack)

```js
function pushHistory(newElements) {
  const newHistory = history.slice(0, historyIndex + 1);
  newHistory.push([...newElements]);
  setHistory(newHistory);
  setHistoryIndex(newHistory.length - 1);
}

function undo() {
  if (historyIndex > 0) {
    setHistoryIndex(prev => prev - 1);
    setElements([...history[historyIndex - 1]]);
  }
}

function redo() {
  if (historyIndex < history.length - 1) {
    setHistoryIndex(prev => prev + 1);
    setElements([...history[historyIndex + 1]]);
  }
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Zero-size shape (click without drag) | Ignore; require minimum 3px movement to create element |
| Rapid drawing (thousands of points) | Reduce point density: skip points too close to previous |
| Window resize | Recalculate canvas dimensions; trigger re-render from elements data |
| High DPI screens | Scale canvas by devicePixelRatio; all drawing coordinates remain logical |
| Undo at start of history | Disable Undo button when historyIndex === 0 |
| Very long undo stack | Limit history to 100 entries; discard oldest |

---

## Bonus Features

- [ ] **Text tool** (click to add text, inline edit)
- [ ] **Draw perfect shapes** (hold Shift to constrain proportions)
- [ ] **Resize / move** existing elements
- [ ] **Z-order** controls (bring to front, send to back)
- [ ] **Grid snap** (optional snapping to grid lines)
- [ ] **Export as PNG / SVG**
- [ ] **Real-time collaboration** (WebSocket + broadcast element operations)

---

## Common Interview Questions

1. **Why Canvas over SVG for a whiteboard?** — Canvas provides a single bitmap surface, ideal for many freehand strokes. SVG creates one DOM node per element, which causes slowdown with 1000+ elements.

2. **How do you handle undo/redo efficiently?** — Store snapshots of the entire elements array at each action boundary. Use a history array with an index pointer. Limit stack depth to 100.

3. **How do you implement hit detection on Canvas?** — Since Canvas has no individual element events, iterate through the elements array in reverse draw order and check if the click point falls within each element's bounding box plus margin.

4. **How to handle high-DPI displays?** — Multiply canvas width/height by `devicePixelRatio`, then scale the context by the same factor. CSS dimensions remain at logical size.

---

## Resources

- [Canvas API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- [Pointer Events spec](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)
- [perfect-freehand](https://github.com/steveruizok/perfect-freehand) (smooth pencil strokes)
