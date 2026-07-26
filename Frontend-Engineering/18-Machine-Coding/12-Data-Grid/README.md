# Data Grid

**Difficulty:** Hard | **Est. Time:** 60–90 min

---

## Problem Statement

Build a high-performance data grid component capable of rendering large datasets (10,000+ rows). The grid must support sorting, filtering, pagination, column resizing, row selection, and custom cell renderers.

---

## Requirements

### Functional
- [ ] Render a grid with rows and columns from a dataset
- [ ] Sort by any column (ascending / descending / none)
- [ ] Filter rows by column values (text match, numeric range, select)
- [ ] Pagination (client-side or server-side)
- [ ] Column resizing via drag
- [ ] Row selection (single and multi-select with checkbox)
- [ ] Custom cell renderers (e.g., status badge, progress bar, link)
- [ ] Column definitions (header name, accessor, renderer, width, sortable)

### Non-Functional
- [ ] Handle 10,000+ rows with smooth scrolling (virtualization)
- [ ] Column virtualization for many columns
- [ ] Sort + filter operations complete within 50ms on 10k rows
- [ ] No layout shifts during data updates
- [ ] Accessible (keyboard navigation, ARIA roles for grid)

---

## Component Architecture

```
App
├── DataGrid
│   ├── GridToolbar
│   │   ├── GlobalSearchInput
│   │   ├── ColumnVisibilityDropdown
│   │   └── ExportButton (CSV)
│   ├── GridHeader
│   │   └── HeaderRow
│   │       └── HeaderCell (×N)
│   │           ├── ColumnLabel
│   │           ├── SortIndicator (▲ / ▼)
│   │           ├── FilterIcon → FilterDropdown
│   │           └── ResizeHandle
│   ├── GridBody (virtualized)
│   │   └── Row (×N) [virtualized window]
│   │       ├── SelectionCheckbox
│   │       └── Cell (×N)
│   │           ├── DefaultCellRenderer
│   │           └── CustomCellRenderer (component)
│   └── GridFooter
│       ├── PaginationControls
│       │   ├── PageSizeSelector
│       │   ├── PageButtons
│       │   └── RowCountLabel
│       └── SelectionInfo ("5 of 100 selected")
```

---

## Virtualization Approaches

| Library | Best For |
|---------|----------|
| **react-window** (`FixedSizeList`, `VariableSizeList`) | Simple virtualized list/rows only |
| **react-virtuoso** | Auto-sized rows, sticky headers, better DX |
| **@tanstack/react-virtual** | Headless, works with any UI |
| **AG Grid** | Full-featured, production grid (but heavy) |

**For interview:** react-window + manual header sync (lighter) or @tanstack/react-table for logic + react-window for rendering.

---

## Implementation with @tanstack/react-table + react-window

```jsx
import { useReactTable, getCoreRowModel, getSortedRowModel, getFilteredRowModel, getPaginationRowModel, flexRender } from '@tanstack/react-table';
import { FixedSizeList as List } from 'react-window';
```

---

## State Management

```js
const [data, setData] = useState([]);
const [columnDefs, setColumnDefs] = useState(initialColumnDefs);
const [sorting, setSorting] = useState([]);
const [filtering, setFiltering] = useState('');
const [columnFilters, setColumnFilters] = useState([]);
const [pagination, setPagination] = useState({ pageIndex: 0, pageSize: 50 });
const [rowSelection, setRowSelection] = useState({});
const [columnSizing, setColumnSizing] = useState({});
```

### Column Definition Shape

```js
const columns = [
  {
    id: 'name',
    header: 'Name',
    accessorKey: 'name',
    size: 200,
    enableSorting: true,
    enableColumnFilter: true,
    cell: ({ getValue }) => <span className="name-cell">{getValue()}</span>,
  },
  {
    id: 'status',
    header: 'Status',
    accessorKey: 'status',
    size: 120,
    cell: ({ getValue }) => {
      const status = getValue();
      return <StatusBadge status={status} />;
    },
  },
  {
    id: 'progress',
    header: 'Progress',
    accessorKey: 'progress',
    size: 150,
    cell: ({ getValue }) => <ProgressBar value={getValue()} />,
  },
];
```

---

## Implementation Steps

1. Set up @tanstack/react-table with basic columns and data
2. Render table header with column headers (with sort indicators)
3. Render table body using react-window `FixedSizeList`
4. Implement sorting: click header → cycle asc/desc/none (use `getSortedRowModel`)
5. Implement column filtering (text input per column, or global search)
6. Implement pagination (page buttons, page size selector)
7. Implement column resizing (mousedown on handle → drag → update column width)
8. Implement row selection (checkbox column, shift-click for range)
9. Add custom cell renderers (StatusBadge, ProgressBar, Link, Avatar)
10. Sync scroll between header and body (fixed header + virtualized body)
11. Add keyboard navigation (arrow keys, Tab, Space for selection)
12. Handle edge cases: empty dataset, loading state, all rows selected

---

## Code Snippets

### TanStack Table Setup

```js
const table = useReactTable({
  data,
  columns,
  state: { sorting, columnFilters, pagination, rowSelection, columnSizing },
  onSortingChange: setSorting,
  onColumnFiltersChange: setColumnFilters,
  onPaginationChange: setPagination,
  onRowSelectionChange: setRowSelection,
  onColumnSizingChange: setColumnSizing,
  getCoreRowModel: getCoreRowModel(),
  getSortedRowModel: getSortedRowModel(),
  getFilteredRowModel: getFilteredRowModel(),
  getPaginationRowModel: getPaginationRowModel(),
  enableRowSelection: true,
  enableMultiRowSelection: true,
  enableColumnResizing: true,
  columnResizeMode: 'onChange',
});
```

### Virtualized Row Renderer

```jsx
const { rows } = table.getRowModel();

<List
  height={600}
  itemCount={rows.length}
  itemSize={48}
  outerRef={listRef}
  onScroll={handleSyncScroll}
>
  {({ index, style }) => {
    const row = rows[index];
    return (
      <div style={style} role="row" selected={row.getIsSelected()}>
        {row.getVisibleCells().map(cell => (
          <div
            key={cell.id}
            style={{ width: cell.column.getSize() }}
            role="cell"
          >
            {flexRender(cell.column.columnDef.cell, cell.getContext())}
          </div>
        ))}
      </div>
    );
  }}
</List>
```

### Column Resize Handler

```js
function handleColumnResize(columnId, delta) {
  setColumnSizing(prev => ({
    ...prev,
    [columnId]: Math.max(60, (prev[columnId] || 150) + delta)
  }));
}

// In HeaderCell:
// mousedown on resize handle → track mousemove delta → update column width
```

### Global Search Filter

```js
const [globalFilter, setGlobalFilter] = useState('');

table.setGlobalFilter(globalFilter);

// Add to table options:
// getFilteredRowModel: getFilteredRowModel(),
// globalFilterFn: 'includesString',
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Empty dataset | Show "No data" message with optional illustration |
| All rows filtered out | Show "No results match your filters" with clear filters button |
| Very wide columns | Horizontal scroll; sticky first column (optional) |
| Very long cell content | Truncate with ellipsis; show tooltip on hover |
| Rapid sort/filter changes | Debounce filter input; use useMemo for filtered data |
| Column resize below minimum | Clamp column width to minimum (60px default) |
| Shift-click row selection | Track last clicked index; select range from last to current |
| Async data loading | Show skeleton rows while loading |

---

## Bonus Features

- [ ] **Column reorder** via drag (dnd-kit horizontal sortable)
- [ ] **Column pinning** (sticky left/right columns)
- [ ] **Row expansion** (expandable detail rows)
- [ ] **Inline editing** (double-click cell → input)
- [ ] **Export to CSV** (generate from visible columns)
- [ ] **Server-side sorting / filtering / pagination** (API-driven)
- [ ] **Row grouping / aggregation** (group by column, show subtotals)
- [ ] **Dark mode** + theme support

---

## Common Interview Questions

1. **Why is virtualization important for data grids?** — Rendering 10,000+ DOM nodes for a table causes slow paint, high memory usage, and janky scrolling. Virtualization renders only visible rows (~20-30) and recycles DOM nodes as user scrolls.

2. **How does TanStack Table compare to AG Grid?** — TanStack Table is headless (no built-in UI), lightweight (~15KB), and composable. AG Grid is full-featured with built-in UI, themes, but ~500KB+. For interviews, TanStack Table shows deeper understanding.

3. **How do you handle column resizing without layout shift?** — Use fixed-width columns with `table-layout: fixed` on the table. Each column has a width from state. On resize, update the width in state and re-render.

4. **How to implement shift-click range selection?** — Track `lastClickedRowIndex`. When user shift-clicks a row, calculate the range from `lastClickedRowIndex` to the clicked row index. Select all rows in between.

5. **How do you sync header scroll with body scroll?** — Use a shared scroll state. The header is a separate fixed element synced to the body's scroll position via `onScroll` event or a shared `scrollLeft` ref.

---

## Resources

- [@tanstack/react-table docs](https://tanstack.com/table/v8)
- [react-window](https://react-window.vercel.app/)
- [AG Grid (for reference)](https://www.ag-grid.com/)
