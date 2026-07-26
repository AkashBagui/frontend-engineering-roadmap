# State Management Projects

## Project 1: E-Commerce Store

**Goal:** Build a full-featured e-commerce storefront using Redux Toolkit for client state and TanStack Query for server state.

### Tech Stack
- Redux Toolkit (client state: cart, auth, UI)
- TanStack Query (server state: products, orders, user data)
- React Router (URL state: product ID, category, page)
- TypeScript

### Requirements

#### Server State (TanStack Query)

| Query | Key | Stale Time | Notes |
|-------|-----|------------|-------|
| Product List | `['products', { category, sort, page }]` | 5 min | Paginated, sortable |
| Product Detail | `['products', id]` | 10 min | Single product view |
| Categories | `['categories']` | 30 min | Static, rarely changes |
| User Orders | `['orders', userId]` | 1 min | User-specific |
| Cart (server) | `['cart', userId]` | 0 (always fresh) | Synced with client |

#### Client State (Redux Toolkit)

**cartSlice:**
- `items: CartItem[]` — local cart items (synced to server on checkout)
- `isOpen: boolean` — cart drawer visibility
- Actions: `addItem`, `removeItem`, `updateQuantity`, `clearCart`
- Selectors: `selectCartTotal`, `selectCartCount`, `selectCartItems`

**uiSlice:**
- `searchOpen: boolean`
- `activeModal: string | null`
- `toastQueue: Toast[]`
- Actions: `openModal`, `closeModal`, `addToast`, `removeToast`

**authSlice:**
- `user: User | null`
- `token: string | null`
- Actions: `login`, `logout`, `updateProfile`

#### Features

1. **Product Listing with Pagination + Filters**
   - Category sidebar (URL state: `?category=`)
   - Price range filter (URL state: `?min=10&max=100`)
   - Sort dropdown (URL state: `?sort=price_asc`)
   - Paginated results with `keepPreviousData`

2. **Product Detail Page**
   - Fetch by ID from URL params
   - Image gallery (client state: selected image index)
   - Add to cart button with quantity selector
   - Related products (parallel query)

3. **Shopping Cart**
   - Zustand/RTK client-side cart
   - Persistent to localStorage
   - Cart badge in header (selectCartCount selector)
   - Cart drawer with item list, quantity controls, total
   - Checkout flow

4. **Checkout Flow**
   - Multi-step form (shipping → payment → review)
   - Form state managed with useReducer (complex local state)
   - Submit order mutation with optimistic update
   - Invalidate orders query on success

5. **Order History**
   - Infinite scroll with `useInfiniteQuery`
   - Status badges, cancellation mutation
   - Re-order functionality

### File Structure

```
src/
  app/
    store.ts                    — configureStore
    hooks.ts                    — useAppSelector, useAppDispatch
    queryClient.ts              — QueryClient config
  features/
    products/
      api.ts                    — getProducts, getProduct (TanStack Query endpoints)
      ProductList.tsx
      ProductDetail.tsx
      ProductCard.tsx
      Filters.tsx
    cart/
      cartSlice.ts              — RTK slice
      CartDrawer.tsx
      CartItem.tsx
      CheckoutForm.tsx
    orders/
      api.ts                    — getOrders, updateOrder
      OrderHistory.tsx
      OrderCard.tsx
    auth/
      authSlice.ts              — RTK slice
      LoginForm.tsx
      Profile.tsx
  components/
    ui/
      Modal.tsx
      Toast.tsx
      Pagination.tsx
      Skeleton.tsx
    layout/
      Header.tsx
      Sidebar.tsx
  types/
    index.ts
  utils/
    formatters.ts
```

### Evaluation Criteria

- Products load with loading skeletons
- Cart persists across sessions (localStorage middleware)
- Filter changes update URL without full page reload
- Checkout mutation invalidates cart and orders
- No unnecessary re-renders (verify with React DevTools)
- Cart badges update instantly when items change

---

## Project 2: Inventory Dashboard

**Goal:** Build an admin inventory dashboard using Zustand for real-time client state and Context for global UI preferences.

### Tech Stack
- Zustand (inventory state: real-time stock, filters, selection)
- Context API (theme, layout preferences)
- TanStack Query or SWR (server data: products, suppliers, reports)
- TypeScript

### Requirements

#### Real-Time Inventory (Zustand)

```ts
interface InventoryStore {
  // State
  products: Map<string, Product>;
  selectedIds: Set<string>;
  filter: FilterState;
  sort: SortState;
  viewMode: 'table' | 'grid';
  lastSync: number;

  // Actions
  selectProduct: (id: string) => void;
  selectAll: () => void;
  deselectAll: () => void;
  updateStock: (id: string, quantity: number) => void;
  setFilter: (filter: Partial<FilterState>) => void;
  setSort: (sort: SortState) => void;
  setViewMode: (mode: 'table' | 'grid') => void;
  bulkUpdate: (updates: StockUpdate[]) => void;
  syncWithServer: () => Promise<void>;
}
```

#### UI Context (Context API)

```tsx
interface UIContextValue {
  theme: 'light' | 'dark';
  setTheme: (t: 'light' | 'dark') => void;
  sidebarCollapsed: boolean;
  toggleSidebar: () => void;
  density: 'compact' | 'comfortable' | 'spacious';
  setDensity: (d: 'compact' | 'comfortable' | 'spacious') => void;
}
```

#### Features

1. **Inventory Table with Real-Time Updates**
   - Virtualized table (react-window) showing 1000+ products
   - Zustand store holds current view state
   - WebSocket or polling for stock updates
   - Highlight rows that changed since last sync

2. **Bulk Selection & Actions**
   - Select individual or all with shift-click range
   - Zustand `selectedIds` Set for O(1) operations
   - Bulk update stock, archive, or export
   - Confirmation modal before destructive actions

3. **Advanced Filtering & Sorting**
   - Multi-criteria filter (category, stock level, supplier, date range)
   - Server-side filtering with debounced API calls
   - Sort by any column (ascending/descending)
   - Persist filter preferences in Zustand with persist middleware

4. **Dashboard Stats**
   - Total products, low stock alerts, monthly trends
   - Auto-refresh every 30 seconds (SWR refetchInterval)
   - Sparkline charts for each product's stock history

5. **Theme & Layout Customization**
   - Dark/light theme via Context provider
   - Density settings saved to localStorage
   - Sidebar collapse/expand state
   - Column visibility toggles for table

### File Structure

```
src/
  stores/
    inventoryStore.ts          — Zustand store
    filterStore.ts             — Zustand filter state
  contexts/
    UIContext.tsx              — Theme, sidebar, density
  features/
    inventory/
      InventoryTable.tsx       — Virtualized table
      InventoryGrid.tsx        — Grid view
      ProductRow.tsx
      StockCell.tsx            — Real-time stock display
      BulkActions.tsx
      FilterPanel.tsx
      SortControls.tsx
      ColumnVisibility.tsx
    dashboard/
      StatsCards.tsx
      StockChart.tsx
      LowStockAlerts.tsx
    products/
      ProductDetail.tsx
      ProductForm.tsx
    settings/
      ThemeToggle.tsx
      DensitySelector.tsx
      LayoutSettings.tsx
  hooks/
    useWebSocket.ts            — Real-time stock updates
    useInventorySync.ts        — Background sync logic
    useKeyboardNavigation.ts   — Arrow key table navigation
  components/
    ui/
      DataTable.tsx
      VirtualizedList.tsx
      Sparkline.tsx
      Badge.tsx
    layout/
      Sidebar.tsx
      Header.tsx
      DashboardLayout.tsx
```

### Zustand Store Performance Considerations

```ts
// Efficient selector with structural sharing
const useSelectedCount = () =>
  useInventoryStore((s) => s.selectedIds.size);

// Avoid full state subscriptions
const useProductById = (id: string) =>
  useInventoryStore((s) => s.products.get(id));

// Batch updates with transaction
const stockUpdate = useInventoryStore((s) => s.bulkUpdate);
<button onClick={() => stockUpdate(selected)} />
```

### Evaluation Criteria

- Inventory table renders 10,000+ rows smoothly (virtualization)
- Real-time stock updates without page flicker
- Bulk select 1000+ items in under 100ms
- Theme change does not re-render inventory table
- Zustand selectors prevent unnecessary re-renders
- Filter state persists across page navigation
- WebSocket reconnection with exponential backoff
