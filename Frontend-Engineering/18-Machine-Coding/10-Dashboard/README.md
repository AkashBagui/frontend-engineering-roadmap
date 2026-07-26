# Dashboard

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build a customizable dashboard with draggable and resizable widgets. Widgets display different types of data (charts, stats, tables). Users can add, remove, arrange widgets and persist their layout.

---

## Requirements

### Functional
- [ ] Dashboard grid with widgets arranged in a layout
- [ ] Widget types: StatCard (number), LineChart, BarChart, Table, RecentActivity
- [ ] Drag widgets to reorder / reposition
- [ ] Resize widgets (by dragging edge/corner handle)
- [ ] Add a new widget from a widget picker
- [ ] Remove a widget (close button)
- [ ] Customize widget settings (e.g., data range, chart type)
- [ ] Auto-refresh data at configurable intervals (30s, 60s, 5min)
- [ ] Persist layout and widget config to LocalStorage

### Non-Functional
- [ ] Smooth drag and resize animations
- [ ] Responsive (stack widgets on mobile)
- [ ] No overlapping on drag (auto-arrange or collision detection)
- [ ] Debounce layout persistence

---

## Component Architecture

```
App
├── DashboardHeader
│   ├── Title
│   ├── AddWidgetButton → WidgetPicker
│   ├── RefreshIntervalSelector
│   └── SaveLayoutButton
├── DashboardGrid
│   ├── GridLayout (react-grid-layout)
│   │   └── WidgetWrapper (×N) [draggable + resizable]
│   │       ├── WidgetToolbar
│   │       │   ├── WidgetTitle
│   │       │   ├── SettingsButton → WidgetSettings
│   │       │   └── RemoveButton
│   │       └── WidgetContent (switches by type)
│   │           ├── StatCard
│   │           ├── LineChart
│   │           ├── BarChart
│   │           ├── DataTable
│   │           └── ActivityFeed
└── WidgetPicker (modal)
    └── WidgetTypeCard (×N)
```

---

## Chart Library Choices

| Library | Pros | Cons |
|---------|------|------|
| **Recharts** | React-native, declarative, good defaults | Limited customization |
| **Chart.js** (react-chartjs-2) | Mature, performant, large ecosystem | Not React-native API |
| **Nivo** | Great for React, SSR-friendly, beautiful | Smaller community |
| **D3.js** | Maximum control, any viz possible | Steep learning curve, verbose |

**For interview:** Recharts (easiest to set up quickly).

---

## Layout Grid

Use **react-grid-layout** (most popular for dashboards):

```jsx
import GridLayout from 'react-grid-layout';
import 'react-grid-layout/css/styles.css';

<GridLayout
  className="layout"
  layout={layout}
  cols={12}
  rowHeight={100}
  width={1200}
  onLayoutChange={handleLayoutChange}
  draggableHandle=".widget-drag-handle"
  isResizable
  compactType="vertical"
>
  {widgets.map(w => (
    <div key={w.id} data-grid={{ x: w.x, y: w.y, w: w.w, h: w.h }}>
      <Widget widget={w} />
    </div>
  ))}
</GridLayout>
```

---

## State Management

```js
const [widgets, setWidgets] = useState([]);    // widget data (type, config, data)
const [layout, setLayout] = useState([]);       // { i: 'widget_1', x, y, w, h }
const [refreshInterval, setRefreshInterval] = useState(60000);
```

### Widget Shape

```js
{
  id: 'widget_1',
  type: 'stat-card',    // 'stat-card' | 'line-chart' | 'bar-chart' | 'table' | 'activity'
  title: 'Total Users',
  config: {
    dataKey: 'users',
    color: '#4f46e5',
    compareToPrevious: true
  },
  data: { value: 12450, change: 12.5 }
}
```

### Normalized Layout

```js
const [layout, setLayout] = useState([
  { i: 'widget_1', x: 0, y: 0, w: 3, h: 2 },
  { i: 'widget_2', x: 3, y: 0, w: 6, h: 4 },
]);
```

---

## Implementation Steps

1. Set up react-grid-layout with a 12-column grid
2. Create WidgetWrapper with drag handle and resize handle
3. Build widget types: StatCard (number + icon), Chart (Recharts), Table, ActivityFeed
4. Implement widget picker modal (grid of available types)
5. Implement add widget: pick type → generate ID → add to widgets + layout
6. Implement remove widget: confirm → remove from both arrays
7. Implement widget settings: inline panel or modal per widget
8. Implement data refresh: setInterval that re-fetches data for all widgets
9. Persist layout + widget config to LocalStorage
10. Add empty state ("Add your first widget")
11. Handle responsive: fewer columns on mobile, stacked layout

---

## Code Snippets

### Auto-Refresh Hook

```js
function useAutoRefresh(fetchData, interval) {
  useEffect(() => {
    fetchData(); // initial fetch
    const id = setInterval(fetchData, interval);
    return () => clearInterval(id);
  }, [fetchData, interval]);
}
```

### Persist Layout with Debounce

```js
const debouncedSave = useCallback(
  debounce((newLayout) => {
    localStorage.setItem('dashboard-layout', JSON.stringify(newLayout));
  }, 500),
  []
);

function handleLayoutChange(newLayout) {
  setLayout(newLayout);
  debouncedSave(newLayout);
}
```

### Widget Resolver (dynamic component)

```js
const WIDGET_MAP = {
  'stat-card': StatCard,
  'line-chart': LineChartWidget,
  'bar-chart': BarChartWidget,
  'table': DataTableWidget,
  'activity': ActivityFeedWidget,
};

function Widget({ widget }) {
  const Component = WIDGET_MAP[widget.type];
  if (!Component) return <div>Unknown widget type</div>;
  return <Component config={widget.config} data={widget.data} />;
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Empty dashboard | Show "Add your first widget" CTA |
| Widget with no data | Show "No data available" placeholder |
| Layout overflow | react-grid-layout handles auto-arrange; use vertical compact |
| Rapid add/remove | Batch layout updates; use layout state as source of truth |
| Refresh during editing | Pause auto-refresh when widget settings modal is open |
| Mobile layout | Switch to single-column layout; widgets stack vertically |
| Layout restoration failure | Catch JSON.parse errors; fall back to default layout |

---

## Bonus Features

- [ ] **Full-screen widget** (expand to fill viewport)
- [ ] **Widget clone** (duplicate with same config)
- [ ] **Dashboard themes** (light/dark)
- [ ] **Multiple dashboards** (tabbed or sidebar)
- [ ] **CSV export** of widget data
- [ ] **Widget snapshot** (download as PNG)
- [ ] **Real-time updates** via WebSocket

---

## Common Interview Questions

1. **How does react-grid-layout work?** — It uses CSS transforms for positioning, calculates layout using a packed grid algorithm (similar to Masonry). Items are placed on a grid with column/row coordinates.

2. **How do you persist widget state?** — Store two things: layout (position/size) and widget config (type, settings). Serialize to JSON in LocalStorage. On mount, parse and restore.

3. **How do you handle data fetching for multiple widgets?** — Each widget independently fetches its data. Use a shared `fetchData` function per widget type. Auto-refresh uses a single setInterval that triggers all fetches.

4. **How to make the dashboard extensible?** — Use a widget registry (object mapping type → component). New widget types just need a new entry in the registry. Plugin architecture for advanced cases.

---

## Resources

- [react-grid-layout](https://github.com/react-grid-layout/react-grid-layout)
- [Recharts](https://recharts.org/)
- [use-debounce](https://www.npmjs.com/package/use-debounce)
