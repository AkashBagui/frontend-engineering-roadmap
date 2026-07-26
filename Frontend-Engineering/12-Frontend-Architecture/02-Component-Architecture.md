# Component Architecture

## Component Hierarchy

A well-designed component hierarchy follows a tree structure where parent components pass data and behavior down to children.

```
App
├── Layout
│   ├── Header
│   │   ├── Logo
│   │   ├── Navigation
│   │   │   └── NavLink (×N)
│   │   └── AuthMenu
│   │       └── UserAvatar
│   ├── Sidebar
│   │   └── SidebarNav
│   │       └── SidebarLink (×N)
│   └── MainContent
│       ├── Breadcrumbs
│       └── RouterOutlet
│           ├── ProductListPage
│           │   ├── ProductFilters
│           │   ├── ProductGrid
│           │   │   └── ProductCard (×N)
│           │   └── Pagination
│           └── ProductDetailPage
│               ├── ProductImages
│               ├── ProductInfo
│               └── RelatedProducts
│                   └── ProductCard (×N)
```

## Smart vs Dumb Components (Container/Presentational)

| Smart (Container) | Dumb (Presentational) |
|-------------------|----------------------|
| Knows about state | Renders props only |
| Handles data fetching | Emits events upwards |
| Contains business logic | Pure rendering |
| Can be connected to stores | No dependencies |
| Harder to reuse | Highly reusable |

```tsx
// Smart Component — Connected to state
// features/products/ProductListContainer.tsx
import { useProducts } from './hooks/useProducts';
import { ProductGrid } from './components/ProductGrid';

export function ProductListContainer() {
  const { data, isLoading, error, filters, setFilters } = useProducts();

  if (isLoading) return <ProductGridSkeleton />;
  if (error) return <ErrorBanner message={error.message} />;

  return (
    <div>
      <ProductFilters filters={filters} onChange={setFilters} />
      <ProductGrid products={data.items} />
      <Pagination
        page={data.page}
        totalPages={data.totalPages}
        onChange={(p) => setFilters({ page: p })}
      />
    </div>
  );
}

// Dumb Component — Pure rendering
// features/products/components/ProductGrid.tsx
interface ProductGridProps {
  products: Product[];
}

export function ProductGrid({ products }: ProductGridProps) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

## Composition Patterns

### 1. Component Composition (Children)

```tsx
interface CardProps {
  title: string;
  children: React.ReactNode;
  footer?: React.ReactNode;
}

export function Card({ title, children, footer }: CardProps) {
  return (
    <div className="card">
      <div className="card-header">{title}</div>
      <div className="card-body">{children}</div>
      {footer && <div className="card-footer">{footer}</div>}
    </div>
  );
}

// Usage
<Card
  title="User Profile"
  footer={<Button onClick={handleSave}>Save</Button>}
>
  <UserForm user={user} />
</Card>
```

### 2. Slot Pattern (Named Children)

```tsx
interface PageLayoutProps {
  header: React.ReactNode;
  sidebar?: React.ReactNode;
  children: React.ReactNode;
}

export function PageLayout({ header, sidebar, children }: PageLayoutProps) {
  return (
    <div className="page-layout">
      <header>{header}</header>
      {sidebar && <aside>{sidebar}</aside>}
      <main>{children}</main>
    </div>
  );
}

// Usage
<PageLayout
  header={<PageHeader title="Products" actions={<AddButton />} />}
  sidebar={<ProductFilters />}
>
  <ProductGrid products={products} />
</PageLayout>
```

### 3. Render Props

```tsx
interface DataLoaderProps<T> {
  fetcher: () => Promise<T>;
  children: (props: { data: T; loading: boolean; error: Error | null }) => React.ReactNode;
}

export function DataLoader<T>({ fetcher, children }: DataLoaderProps<T>) {
  // ...fetching logic
  return <>{children({ data, loading, error })}</>;
}

// Usage
<DataLoader fetcher={fetchProducts}>
  {({ data, loading }) =>
    loading ? <Skeleton /> : <ProductGrid products={data} />
  }
</DataLoader>
```

### 4. Compound Components

```tsx
interface TabsContextType {
  activeTab: string;
  setActiveTab: (tab: string) => void;
}

const TabsContext = createContext<TabsContextType | null>(null);

function Tabs({ defaultValue, children }: { defaultValue: string; children: React.ReactNode }) {
  const [activeTab, setActiveTab] = useState(defaultValue);
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }: { children: React.ReactNode }) {
  return <div className="tab-list" role="tablist">{children}</div>;
}

function Tab({ value, children }: { value: string; children: React.ReactNode }) {
  const ctx = useContext(TabsContext)!;
  return (
    <button
      role="tab"
      aria-selected={ctx.activeTab === value}
      onClick={() => ctx.setActiveTab(value)}
    >
      {children}
    </button>
  );
}

function TabPanel({ value, children }: { value: string; children: React.ReactNode }) {
  const ctx = useContext(TabsContext)!;
  if (ctx.activeTab !== value) return null;
  return <div role="tabpanel">{children}</div>;
}

Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panel = TabPanel;

// Usage
<Tabs defaultValue="details">
  <Tabs.List>
    <Tabs.Tab value="details">Details</Tabs.Tab>
    <Tabs.Tab value="shipping">Shipping</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel value="details"><ProductDetails /></Tabs.Panel>
  <Tabs.Panel value="shipping"><ShippingInfo /></Tabs.Panel>
</Tabs>
```

## Component API Design

### Props Interface Guidelines

```tsx
// GOOD: Explicit types, sensible defaults, required marked clearly
interface ButtonProps {
  /** The visual variant of the button */
  variant: 'primary' | 'secondary' | 'ghost' | 'danger';
  /** Button size */
  size?: 'sm' | 'md' | 'lg';
  /** Full width button */
  fullWidth?: boolean;
  /** Loading state */
  loading?: boolean;
  /** Disabled state */
  disabled?: boolean;
  /** Icon to show before text */
  leftIcon?: React.ReactNode;
  /** Click handler */
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
  children: React.ReactNode;
}

export function Button({
  variant = 'primary',
  size = 'md',
  fullWidth = false,
  loading = false,
  disabled = false,
  leftIcon,
  onClick,
  children,
}: ButtonProps) {
  return (
    <button
      className={clsx('btn', `btn--${variant}`, `btn--${size}`, {
        'btn--full': fullWidth,
        'btn--loading': loading,
      })}
      disabled={disabled || loading}
      onClick={onClick}
    >
      {loading && <Spinner />}
      {leftIcon && <span className="btn__icon">{leftIcon}</span>}
      {children}
    </button>
  );
}
```

### Forwarding Refs

```tsx
export const Input = forwardRef<HTMLInputElement, InputProps>(
  function Input({ label, error, ...props }, ref) {
    return (
      <div className="input-field">
        {label && <label>{label}</label>}
        <input ref={ref} className={error ? 'input--error' : ''} {...props} />
        {error && <span className="input__error">{error}</span>}
      </div>
    );
  }
);
```

## Storybook Integration

```tsx
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'UI/Button',
  component: Button,
  argTypes: {
    variant: { control: 'select', options: ['primary', 'secondary', 'ghost', 'danger'] },
    size: { control: 'select', options: ['sm', 'md', 'lg'] },
    loading: { control: 'boolean' },
    disabled: { control: 'boolean' },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Click Me',
  },
};

export const WithIcon: Story = {
  args: {
    variant: 'secondary',
    leftIcon: <span>→</span>,
    children: 'Next Step',
  },
};

export const Loading: Story = {
  args: {
    variant: 'primary',
    loading: true,
    children: 'Saving...',
  },
};
```

## Summary

Good component architecture separates concerns (smart vs dumb), uses composition for flexibility, designs explicit prop interfaces with sensible defaults, and documents components in Storybook. This makes the codebase maintainable, testable, and scalable.
