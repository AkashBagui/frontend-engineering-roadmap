# Spreadsheet

**Difficulty:** Hard | **Est. Time:** 60–90 min

---

## Problem Statement

Build a spreadsheet application with a grid of cells that support editing, basic formulas (SUM, AVERAGE, MIN, MAX), cell selection, and efficient rendering for large datasets.

---

## Requirements

### Functional
- [ ] Render a grid with rows and columns (e.g., A–Z columns, 1–100 rows)
- [ ] Click a cell to select it; show active cell highlight
- [ ] Edit cell content inline (input appears on click or double-click)
- [ ] Cell data persists in memory (grid state)
- [ ] Basic formulas: `=SUM(A1:A5)`, `=AVG(B1:B10)`, `=MIN(C1:C5)`, `=MAX(D1:D5)`
- [ ] Formula results update when referenced cells change
- [ ] Column resize via drag

### Non-Functional
- [ ] Handle 10,000+ cells without jank (virtualization)
- [ ] Formula parsing errors don't crash the app
- [ ] Keyboard navigation (arrow keys, Tab, Enter)
- [ ] Copy-paste support (basic)

---

## Component Architecture

```
App
├── SpreadsheetToolbar
│   ├── CellReference (shows "A1")
│   ├── FormulaBar (shows cell content / formula)
│   └── FunctionButtons (auto SUM, etc.)
├── SpreadsheetGrid
│   ├── GridHeader
│   │   ├── RowNumbers (1, 2, 3…)
│   │   └── ColumnHeaders (A, B, C…)
│   └── GridBody (virtualized)
│       └── Cell (×N) [virtualized window]
│           ├── CellDisplay (shows value)
│           └── CellEditor (input, shown when editing)
└── StatusBar
    └── Cell count, selection summary (SUM of selected)
```

---

## Performance Considerations

| Technique | Why |
|-----------|-----|
| **Virtualization** (react-window) | Only render visible cells; DOM stays under 200 nodes |
| **Memoized cell values** | Recalculate only cells whose dependencies changed |
| **Column-based rendering** | Group cells by column for efficient layout |
| **Canvas rendering** (advanced) | For 100k+ cells, skip DOM entirely |

---

## State Management

```js
const gridState = {
  data: {
    'A1': { raw: 'Hello', value: 'Hello', formula: null },
    'A2': { raw: '=SUM(B1:B5)', value: 42, formula: 'SUM(B1:B5)', dependencies: ['B1','B2','B3','B4','B5'] }
  },
  columns: { A: { width: 100 }, B: { width: 120 } },
  rows: 100,
  selectedCell: 'A1',
  editingCell: null,
  selectionRange: null // { start: 'A1', end: 'C5' }
};
```

---

## Formula Parsing Approach

```
1. Parse formula string into AST
   =SUM(B1:B5)
     └─ FunctionCall: name=SUM, args=[RangeRef: B1→B5]

2. Resolve range references to individual cells
   RangeRef B1:B5 → [B1, B2, B3, B4, B5]

3. Evaluate each referenced cell → get values
   [10, 20, 30, 40, 50]

4. Apply function
   SUM([10,20,30,40,50]) → 150

5. Cache result; track dependencies for reactivity
   A2 dependsOn → [B1,B2,B3,B4,B5]
```

### Dependency Graph for Reactivity

```js
// Topological sort to evaluate cells in order
// When B1 changes, invalidate all dependents transitively
const dependents = {
  'B1': ['A2', 'C3'],  // Cells that depend on B1
  'B2': ['A2']
};
```

---

## Implementation Steps

1. Create grid layout with CSS Grid or HTML table
2. Render column headers (A–Z, AA–ZZ) and row numbers
3. Build Cell component: display mode and edit mode
4. Implement cell selection (click to select, arrow keys to navigate)
5. Implement cell editing (double-click or Enter → input)
6. Build formula bar (shows raw content of active cell)
7. Implement formula parser (tokenizer → AST → evaluator)
8. Wire dependency tracking (when a cell changes, recalculate dependents)
9. Add column resize (mousedown on header edge → drag → update width)
10. Add virtualization (react-window FixedSizeGrid or custom)
11. Handle edge cases: circular references, parse errors, empty cells

---

## Code Snippets

### Simple Formula Parser

```js
function evaluateFormula(formula, getCellValue) {
  const match = formula.match(/^=(\w+)\((.+)\)$/);
  if (!match) return { error: '#INVALID_FORMULA' };

  const [, fnName, argsStr] = match;
  const args = argsStr.split(',').map(arg => arg.trim());
  const values = args.flatMap(arg => {
    const rangeMatch = arg.match(/^([A-Z]+)(\d+):([A-Z]+)(\d+)$/);
    if (rangeMatch) {
      const [, colStart, rowStart, colEnd, rowEnd] = rangeMatch;
      return expandRange(colStart, parseInt(rowStart), colEnd, parseInt(rowEnd)).map(getCellValue);
    }
    return [getCellValue(arg)];
  });

  const fn = FUNCTIONS[fnName.toUpperCase()];
  if (!fn) return { error: '#UNKNOWN_FUNCTION' };

  const numericValues = values.filter(v => typeof v === 'number');
  if (numericValues.length === 0) return { error: '#VALUE!' };
  return { value: fn(numericValues) };
}

const FUNCTIONS = {
  SUM: vals => vals.reduce((a, b) => a + b, 0),
  AVG: vals => vals.reduce((a, b) => a + b, 0) / vals.length,
  MIN: vals => Math.min(...vals),
  MAX: vals => Math.max(...vals),
  COUNT: vals => vals.length,
};
```

### Circular Reference Detection

```js
function hasCircularDependency(cellId, formula, gridData) {
  const visited = new Set();
  function dfs(current) {
    if (current === cellId) return true;
    if (visited.has(current)) return false;
    visited.add(current);
    const cell = gridData[current];
    if (cell?.dependencies) {
      for (const dep of cell.dependencies) {
        if (dfs(dep)) return true;
      }
    }
    return false;
  }
  return dfs(cellId);
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Circular formula | Detect before evaluating; show `#CIRCULAR` error |
| Division by zero | Return `#DIV/0!` |
| Invalid cell reference | Show `#REF!` |
| Empty cell in formula range | Treat as 0 for math functions, skip for COUNT |
| Cell reference after column insert | Update all formula references (advanced) |
| Very large grid (1M cells) | Canvas rendering; web workers for calculation |

---

## Bonus Features

- [ ] **Cell formatting** (bold, italic, color, alignment)
- [ ] **Multi-cell selection** (click-drag across cells)
- [ ] **Copy / Paste** between cells
- [ ] **Undo / Redo**
- [ ] **Charts** from selected data range
- [ ] **CSV export / import**
- [ ] **Formula autocomplete** (type `=S` → suggest SUM)

---

## Common Interview Questions

1. **How do you handle formula recalculation efficiently?** — Use a directed acyclic graph (DAG) of dependencies. Topologically sort and only recalculate affected cells when a cell changes.

2. **Why virtualization for a spreadsheet?** — A 100×100 grid = 10,000 DOM nodes. Rendering all would cause lag. Virtualization renders ~50 visible cells and recycles DOM nodes.

3. **How does cell reference parsing work?** — Column letters convert to numeric index (A=1, B=2, …, Z=26, AA=27). Row numbers are numeric. A1 → {col: 0, row: 0}. Range A1:B3 expands to [A1,A2,A3,B1,B2,B3].

4. **How do you prevent infinite loops with circular references?** — Track visited cells during evaluation. If you revisit a cell already in the evaluation stack, return an error.

---

## Resources

- [react-window FixedSizeGrid](https://react-window.vercel.app/#/api/FixedSizeGrid)
- [Formula parser reference (Excel)](https://support.microsoft.com/en-us/office/excel-functions-alphabetical-b3944572-255d-4efb-bb96-c6d90033e188)
