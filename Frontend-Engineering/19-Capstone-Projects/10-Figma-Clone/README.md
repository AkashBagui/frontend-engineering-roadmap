# Figma Clone — Design Tool

## Project Overview

Build a browser-based design tool with canvas, layers, shapes, text, vector editing, color picker, zoom/pan, and export. This is the most technically challenging project, requiring custom rendering with Canvas API and SVG, complex geometry math, and building a design tool UX from scratch.

## Learning Objectives

- Canvas 2D API for rendering design elements
- SVG for vector shapes and paths
- Custom rendering pipeline (render loop, dirty checking)
- Geometry and coordinate transformations
- Hit testing and selection
- Layer management (z-index, grouping)
- Vector editing (bezier curves, path manipulation)
- Undo/redo command pattern
- Zoom, pan, and viewport management
- Export to PNG/SVG/PDF
- Keyboard shortcuts and tool system

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| React + Vite | Framework | Component UI, state management |
| TypeScript | Language | Type safety for geometry |
| Canvas API | Rendering | High-performance 2D rendering |
| SVG | Vectors | Export, path data, scalable output |
| Zustand | State management | Document state, UI state, tool state |
| Tailwind CSS | Styling | Panels, toolbar, UI chrome |
| React Router v6 | Routing | Different views |
| roughjs | Sketch effect | Hand-drawn style shapes (optional) |
| html-to-image | Export | PNG export of canvas |

## Feature List

### MVP Features
- Canvas with zoom (scroll wheel) and pan (space + drag)
- Shape tools (rectangle, ellipse, triangle, line)
- Text tool (add, edit, style text)
- Selection (click, shift-select, marquee select)
- Move, resize, rotate selected elements
- Fill and stroke color picker
- Layer panel (reorder, visibility, lock, rename)
- Undo/redo (50+ steps)
- Export to PNG
- Keyboard shortcuts (V for select, R for rect, etc.)

### Advanced Features
- Vector editing (pen tool, bezier curves, path editing)
- Boolean operations (union, subtract, intersect)
- Grouping and nested layers
- Alignment tools (left, center, right, distribute)
- Rulers and guides (snap to grid)
- Gradient fills and strokes
- Image import and cropping
- Text formatting (font, size, alignment, line height)
- Component/symbol system (master + instances)
- Copy/paste between documents
- Real-time collaboration (basic)
- Export to SVG

## Architecture Diagram

```
src/
├── main.tsx
├── App.tsx
├── pages/
│   ├── EditorPage.tsx               # Main editor
│   └── DashboardPage.tsx            # File list (optional)
├── components/
│   ├── layout/
│   │   ├── MenuBar.tsx              # File, Edit, View, etc.
│   │   ├── Toolbar.tsx              # Left tool panel
│   │   └── StatusBar.tsx            # Zoom %, selected element info
│   ├── canvas/
│   │   ├── Canvas.tsx               # Main canvas container
│   │   ├── CanvasRenderer.tsx       # Canvas 2D render loop
│   │   ├── Viewport.tsx             # Zoom + pan transform
│   │   ├── Grid.tsx                 # Background grid rendering
│   │   └── SelectionOverlay.tsx     # Selection handles + bounds
│   ├── tools/
│   │   ├── ToolSelector.tsx         # Active tool indicator
│   │   ├── SelectTool.tsx           # Move, resize, rotate
│   │   ├── ShapeTool.tsx            # Rectangle, ellipse, polygon
│   │   ├── TextTool.tsx             # Add/edit text
│   │   ├── PenTool.tsx              # Vector paths
│   │   ├── HandTool.tsx             # Pan canvas
│   │   └── ZoomTool.tsx             # Zoom in/out
│   ├── panels/
│   │   ├── LayersPanel.tsx          # Layer tree
│   │   ├── PropertiesPanel.tsx      # Selected element properties
│   │   ├── ColorPicker.tsx          # Fill/stroke color
│   │   ├── StrokeOptions.tsx        # Width, dash, cap
│   │   ├── TextOptions.tsx          # Font, size, alignment
│   │   ├── AlignmentPanel.tsx       # Align/distribute buttons
│   │   └── ExportPanel.tsx          # Export options
│   ├── elements/
│   │   ├── ElementRenderer.tsx      # Dispatches to shape renderers
│   │   ├── RectRenderer.tsx
│   │   ├── EllipseRenderer.tsx
│   │   ├── LineRenderer.tsx
│   │   ├── TextRenderer.tsx
│   │   ├── PathRenderer.tsx
│   │   ├── GroupRenderer.tsx
│   │   └── ImageRenderer.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Slider.tsx
│       ├── Tabs.tsx
│       ├── Tooltip.tsx
│       └── ContextMenu.tsx
├── store/
│   ├── useDocumentStore.ts          # Elements, layers, page
│   ├── useToolStore.ts              # Active tool, tool options
│   ├── useUIStore.ts                # Panels, zoom, theme
│   └── useHistoryStore.ts           # Undo/redo stack
├── lib/
│   ├── canvas/
│   │   ├── renderer.ts              # Main render loop
│   │   ├── viewport.ts              # Coordinate transforms
│   │   ├── hitTest.ts               # Point-in-shape detection
│   │   └── selection.ts             # Bounding box, handles
│   ├── geometry/
│   │   ├── math.ts                  # Vector math, matrix operations
│   │   ├── bezier.ts                # Bezier curve calculations
│   │   ├── bounds.ts                # Bounding box calculations
│   │   └── boolean.ts               # Boolean operations (advanced)
│   ├── tools/
│   │   ├── toolManager.ts           # Tool lifecycle
│   │   ├── selectTool.ts            # Selection logic
│   │   ├── shapeTool.ts             # Shape creation
│   │   ├── penTool.ts               # Path editing
│   │   └── textTool.ts              # Text handling
│   ├── export/
│   │   ├── toPNG.ts
│   │   ├── toSVG.ts
│   │   └── toJSON.ts
│   ├── history.ts                   # Undo/redo command stack
│   ├── utils.ts
│   └── constants.ts
├── types/
│   ├── element.ts                   # DesignElement, ShapeElement, etc.
│   ├── tool.ts
│   ├── viewport.ts
│   └── document.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<EditorPage>
  <MenuBar>
    <FileMenu />                    {/* New, Import, Export */}
    <EditMenu />                    {/* Undo, Redo, Copy, Paste */}
    <ViewMenu />                    {/* Zoom, Grid, Rulers */}
  </MenuBar>
  <EditorArea>
    <Toolbar>
      <ToolButton tool="select" />  {/* V */}
      <ToolButton tool="rect" />    {/* R */}
      <ToolButton tool="ellipse" /> {/* E */}
      <ToolButton tool="text" />    {/* T */}
      <ToolButton tool="pen" />     {/* P */}
      <ToolButton tool="hand" />    {/* H */}
      <ToolButton tool="zoom" />    {/* Z */}
      <Separator />
      <ShapeToolOptions />          {/* Fill, stroke */}
    </Toolbar>
    <CanvasContainer>
      <Canvas>                      {/* <canvas> element */}
        {/* Canvas 2D renders elements here */}
      </Canvas>
      <SelectionOverlay>            {/* HTML overlay for handles */}
        <ResizeHandle dir="nw" />
        <ResizeHandle dir="ne" />
        {/* ... */}
        <RotateHandle />
      </SelectionOverlay>
    </CanvasContainer>
    <RightPanel>
      <LayersPanel>
        <LayerItem />*              {/* Element name, visibility, lock */}
      </LayersPanel>
      <PropertiesPanel>
        <PositionInputs />          {/* X, Y, W, H */}
        <RotationInput />
        <FillOptions>
          <ColorPicker />
          <OpacitySlider />
        </FillOptions>
        <StrokeOptions>
          <ColorPicker />
          <StrokeWidthSlider />
          <DashPatternSelect />
        </StrokeOptions>
        <TextOptions />             {/* Only for text elements */}
      </PropertiesPanel>
    </RightPanel>
  </EditorArea>
  <StatusBar>
    <ZoomControl />                 {/* Zoom percentage + presets */}
    <ElementInfo />                 {/* "Selected: Rectangle 1" */}
    <CanvasSize />                  {/* "1920 x 1080" */}
  </StatusBar>
</EditorPage>
```

## Rendering Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                        RENDER PIPELINE                              │
├─────────────────────────────────────────────────────────────────────┤
│  1. Clear canvas (transparent or checkerboard)                     │
│  2. Apply viewport transform (translate + scale)                   │
│  3. Render background grid                                         │
│  4. Render elements (back-to-front, respecting z-index):           │
│     ┌───────────────┐                                               │
│     │ ElementTree   │   Traverse recursively                        │
│     │ ├── Group     │   → Render children in order                  │
│     │ │ ├── Rect   │   → Canvas 2D fillRect + strokeRect           │
│     │ │ ├── Text   │   → fillText with font styles                 │
│     │ │ └── Path   │   → beginPath + bezierCurveTo + fill/stroke  │
│     │ └── Image    │   → drawImage                                  │
│     └───────────────┘                                               │
│  5. Render selection overlay (dashed border, handles)              │
│  6. Render guides and rulers (optional)                            │
│  7. RequestAnimationFrame for next frame                           │
└─────────────────────────────────────────────────────────────────────┘

Dirty checking: only re-render if elements/properties changed using a flag
```

## Data Model

```typescript
// Core element types
interface DesignElement {
  id: string;
  type: 'rectangle' | 'ellipse' | 'line' | 'text' | 'path' | 'group' | 'image';
  name: string;
  x: number;
  y: number;
  width: number;
  height: number;
  rotation: number;
  opacity: number;
  visible: boolean;
  locked: boolean;
  zIndex: number;
  parentId: string | null;
  children: string[]; // For groups
}

interface RectElement extends DesignElement {
  type: 'rectangle';
  cornerRadius: number;
  fill: FillStyle;
  stroke: StrokeStyle;
}

interface TextElement extends DesignElement {
  type: 'text';
  content: string;
  fontFamily: string;
  fontSize: number;
  fontWeight: number;
  textAlign: 'left' | 'center' | 'right';
  lineHeight: number;
  fill: FillStyle;
}

interface PathElement extends DesignElement {
  type: 'path';
  pathData: PathCommand[]; // [{type: 'M', x, y}, {type: 'C', x1, y1, cx, cy, x, y}]
  closed: boolean;
  fill: FillStyle;
  stroke: StrokeStyle;
}

interface FillStyle {
  type: 'solid' | 'gradient' | 'image' | 'none';
  color?: string; // hex
  opacity?: number;
  gradient?: GradientData;
}

interface StrokeStyle {
  color: string;
  width: number;
  dash: number[];
  cap: 'butt' | 'round' | 'square';
}
```

## Route Structure

| Route | Component | Auth | Description |
|-------|-----------|------|-------------|
| `/` | DashboardPage | Required | File list (optional) |
| `/editor/new` | EditorPage | Required | New document |
| `/editor/:id` | EditorPage | Required | Open existing document |

## Key Implementation Considerations

- Canvas rendering loop: use `requestAnimationFrame` with dirty flag optimization
- Viewport: maintain transform matrix (scale + translate) for zoom/pan
- Hit testing: convert mouse coords to canvas coords via inverse transform, then test against elements (bounding box for rect/ellipse, point-in-path for paths)
- Selection handles: render as HTML overlay (not canvas) for easier event handling
- Undo/redo: implement command pattern — each operation stores before/after state
- Text editing: use hidden `<textarea>` overlaid on canvas for text input
- Path editing: store as array of commands (moveTo, lineTo, bezierCurveTo)
- Performance: only re-render changed elements (maintain dirty set)
- Export to PNG: use `canvas.toBlob()` or `html-to-image` library
- Export to SVG: convert element model to SVG XML

## Performance Considerations

- Dirty rectangle tracking — only repaint changed areas
- OffscreenCanvas for static background (grid) — cache and reuse
- Limit render loop to 30fps when no interaction, 60fps during interaction
- Virtualize layer panel for documents with 100+ elements
- Debounce property panel updates (don't re-render on every pixel change during resize)
- Use Web Workers for heavy geometry calculations (boolean ops, complex hit tests)
- Throttle cursor/selection updates to state store (batch per frame)
- `React.memo` for all panel components and tool buttons
- Canvas resolution: render at devicePixelRatio for crisp display

## Deployment Strategy

1. **Vite build** → Vercel or Netlify
2. **No server required** for MVP (local storage for documents)
3. **Optional**: Supabase for document storage and collaboration
4. **Environment variables**: optional database URL for cloud saves
5. **CI/CD**: GitHub Actions → lint → build → deploy

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Architecture, geometry research, data model | 2 |
| Foundation | Canvas setup, viewport, zoom/pan | 2 |
| Shape Tools | Rectangle, ellipse, line creation + rendering | 2 |
| Selection | Click, shift-select, marquee, handles | 2 |
| Properties | Resize, rotate, move, snap | 2 |
| Color/Fill | Color picker, fill, stroke, opacity | 1.5 |
| Layers Panel | Layer tree, reorder, visibility, lock | 1.5 |
| Text Tool | Add, edit, font, alignment | 2 |
| Path Tool | Pen tool, bezier editing (advanced) | 3 |
| Undo/Redo | Command pattern, history stack | 1.5 |
| Export | PNG, SVG, JSON export | 1 |
| Polish | Grid, alignment, keyboard shortcuts, context menu | 2 |
| Deploy | Build, optimize, deploy | 1 |
| **Total** | | **~16-25 days** |

## Learning Resources

- [MDN Canvas API Guide](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- [Canvas 2D Rendering](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)
- [SVG Path Data](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths)
- [Bezier Curve Math](https://pomax.github.io/bezierinfo/)
- [Design Tool Architecture (Figma Blog)](https://www.figma.com/blog/how-figmas-multiplayer-technology-works/)
- [Zustand Documentation](https://docs.pmnd.rs/zustand/getting-started/introduction)
- [Command Pattern in TypeScript](https://refactoring.guru/design-patterns/command/typescript/example)
- [html-to-image Library](https://github.com/bubkoo/html-to-image)
