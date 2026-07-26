# Virtualization

## What is Virtualization?

Virtualization (windowing) renders only the visible portion of a large list. Instead of rendering 10,000 rows, it renders only ~20 (the visible ones + some buffer), replacing DOM nodes as the user scrolls.

## Why Virtualization Matters

```
Without virtualization:
  10,000 rows × 50 DOM nodes each = 500,000 DOM nodes
  Memory: ~500 MB
  Render time: several seconds
  Scroll performance: ~5 FPS

With virtualization:
  ~30 rows visible = ~1,500 DOM nodes
  Memory: ~5 MB
  Render time: < 100ms
  Scroll performance: 60 FPS
```

## react-window

### FixedSizeList

All items have the same height.

```tsx
import { FixedSizeList } from 'react-window';

interface Product {
  id: string;
  name: string;
  price: number;
}

function VirtualizedProductList({ products }: { products: Product[] }) {
  const Row = ({ index, style }: { index: number; style: React.CSSProperties }) => {
    const product = products[index];
    return (
      <div style={style} className="product-row">
        <span>{product.name}</span>
        <span>${product.price}</span>
      </div>
    );
  };

  return (
    <FixedSizeList
      height={600}           // Container height
      width="100%"           // Container width
      itemCount={products.length}
      itemSize={80}          // Each row is 80px tall
      overscanCount={5}      // Extra rows rendered above/below
    >
      {Row}
    </FixedSizeList>
  );
}
```

### VariableSizeList

Items have varying heights.

```tsx
import { VariableSizeList } from 'react-window';

function VariableProductList({ products }: { products: Product[] }) {
  const listRef = useRef(null);

  // Dynamic height based on content
  const getItemSize = (index: number) => {
    const product = products[index];
    const baseHeight = 80;
    const descriptionHeight = product.description
      ? Math.ceil(product.description.length / 50) * 20
      : 0;
    return baseHeight + descriptionHeight;
  };

  const Row = ({ index, style }) => {
    const product = products[index];
    return (
      <div style={style}>
        <h3>{product.name}</h3>
        <p>{product.description}</p>
        <span>${product.price}</span>
      </div>
    );
  };

  return (
    <VariableSizeList
      ref={listRef}
      height={600}
      width="100%"
      itemCount={products.length}
      itemSize={getItemSize}
      estimatedItemSize={100}
      overscanCount={3}
    >
      {Row}
    </VariableSizeList>
  );
}
```

## react-virtuoso

Higher-level library with more features out of the box.

```tsx
import { Virtuoso } from 'react-virtuoso';

function VirtuosoProductList({ products }: { products: Product[] }) {
  return (
    <Virtuoso
      style={{ height: '600px' }}
      totalCount={products.length}
      itemContent={(index) => {
        const product = products[index];
        return (
          <ProductCard key={product.id} product={product} />
        );
      }}
      components={{
        Header: () => <div className="list-header">Products</div>,
        Footer: () => <div className="list-footer">End of list</div>,
        EmptyPlaceholder: () => <div>No products found</div>,
      }}
      increaseViewportBy={{ top: 200, bottom: 200 }}
    />
  );
}
```

### Infinite Scroll with Virtuoso

```tsx
function InfiniteProductList() {
  const [products, setProducts] = useState<Product[]>([]);
  const [hasMore, setHasMore] = useState(true);
  const [page, setPage] = useState(1);

  const loadMore = useCallback(async () => {
    const newProducts = await fetchProducts(page);
    setProducts(prev => [...prev, ...newProducts]);
    setHasMore(newProducts.length > 0);
    setPage(p => p + 1);
  }, [page]);

  return (
    <Virtuoso
      style={{ height: '600px' }}
      totalCount={products.length}
      itemContent={index => <ProductCard product={products[index]} />}
      endReached={loadMore}
      components={{
        Footer: () => hasMore ? <LoadingSpinner /> : <EndOfList />,
      }}
    />
  );
}
```

## AG Grid

For complex data grids with sorting, filtering, grouping, and editing.

```tsx
import { AgGridReact } from 'ag-grid-react';
import 'ag-grid-community/styles/ag-grid.css';
import 'ag-grid-community/styles/ag-theme-alpine.css';

function ProductDataGrid({ products }: { products: Product[] }) {
  const columnDefs = [
    { field: 'name', headerName: 'Product Name', sortable: true, filter: true },
    { field: 'category', headerName: 'Category', sortable: true, filter: true },
    {
      field: 'price',
      headerName: 'Price',
      sortable: true,
      filter: 'agNumberColumnFilter',
      valueFormatter: (params) => `$${params.value.toFixed(2)}`,
    },
    { field: 'stock', headerName: 'In Stock', sortable: true },
    {
      field: 'actions',
      headerName: '',
      cellRenderer: (params) => (
        <button onClick={() => handleEdit(params.data.id)}>Edit</button>
      ),
    },
  ];

  return (
    <div className="ag-theme-alpine" style={{ height: '600px', width: '100%' }}>
      <AgGridReact
        rowData={products}
        columnDefs={columnDefs}
        pagination={true}
        paginationPageSize={50}
        rowSelection="multiple"
        enableCellTextSelection={true}
        animateRows={true}
      />
    </div>
  );
}
```

## Performance Comparison

| Library | Use Case | Performance | Features | Bundle Size |
|---------|----------|-------------|----------|-------------|
| **react-window** | Simple lists, uniform/variable height | Excellent | Minimal | ~5 KB |
| **react-virtuoso** | Complex lists, infinite scroll, chat | Excellent | Rich | ~15 KB |
| **AG Grid** | Spreadsheets, data grids, CRUD | Good (Enterprise) | Full-featured | ~500 KB |
| **react-virtualized** | Legacy (predecessor to react-window) | Good | Many | ~30 KB |

## When NOT to Virtualize

- Lists with fewer than 50 items (DOM is fast enough)
- Mobile lists with simple rendering
- When each item has unpredictable dynamic height with no estimation possible
- When you need CSS animations on mount (items animate in)

## Scroll to Index

```tsx
import { useRef } from 'react';
import { FixedSizeList } from 'react-window';

function ScrollableList({ products }) {
  const listRef = useRef(null);

  const scrollToProduct = (index) => {
    listRef.current?.scrollToItem(index, 'center');
  };

  return (
    <div>
      <button onClick={() => scrollToProduct(100)}>
        Go to product #100
      </button>

      <FixedSizeList
        ref={listRef}
        height={600}
        itemCount={products.length}
        itemSize={80}
      >
        {Row}
      </FixedSizeList>
    </div>
  );
}
```

## Real-World Scenario: Chat Application

```tsx
import { Virtuoso } from 'react-virtuoso';

function ChatWindow({ messages }) {
  const virtuosoRef = useRef(null);

  // Auto-scroll to bottom on new messages
  useEffect(() => {
    virtuosoRef.current?.scrollToIndex({ index: messages.length - 1 });
  }, [messages.length]);

  return (
    <Virtuoso
      ref={virtuosoRef}
      style={{ height: '100%' }}
      totalCount={messages.length}
      itemContent={(index) => (
        <MessageBubble message={messages[index]} />
      )}
      followOutput="smooth"
      initialTopMostItemIndex={messages.length - 1}
      components={{
        Header: () => <LoadOlderMessagesButton />,
      }}
    />
  );
}
```

## Summary

Virtualization is essential for rendering large lists (500+ items) without performance degradation. Choose react-window for simple lists, react-virtuoso for feature-rich scenarios (infinite scroll, chat), and AG Grid for complex data grids. Always measure performance before and after virtualization to confirm improvement.
