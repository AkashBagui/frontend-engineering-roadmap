# Frontend Architecture Projects

## Project: Refactor E-Commerce Application into Scalable Architecture

**Goal:** Take an existing e-commerce application (from Section 10 Projects) and refactor it to implement scalable architecture patterns including feature-based structure, component hierarchy, design system usage, and Clean Architecture principles.

### Phase 1: Directory Structure Refactor

Transform the flat structure into a feature-based architecture.

#### Before (Flat Structure)
```
src/
  components/
    Button.tsx
    ProductCard.tsx
    CartItem.tsx
    Header.tsx
    Modal.tsx
  pages/
    Home.tsx
    Products.tsx
    Cart.tsx
  hooks/
    useProducts.ts
    useCart.ts
  utils/
    api.ts
    format.ts
  App.tsx
```

#### After (Feature-Based)

```
src/
  features/
    products/
      api/
        getProducts.ts
        getProduct.ts
      components/
        ProductCard.tsx
        ProductGrid.tsx
        ProductFilters.tsx
        ProductDetail.tsx
        ProductReviews.tsx
      hooks/
        useProducts.ts
        useProductFilters.ts
      types/
        index.ts
      utils/
        formatters.ts
      index.ts
    cart/
      components/
        CartDrawer.tsx
        CartItem.tsx
        CartSummary.tsx
        CartBadge.tsx
      hooks/
        useCart.ts
      store/
        cartStore.ts
      types/
        index.ts
      index.ts
    checkout/
      components/
        ShippingForm.tsx
        PaymentForm.tsx
        OrderReview.tsx
        OrderConfirmation.tsx
      hooks/
        useCheckout.ts
      api/
        submitOrder.ts
      types/
        index.ts
      index.ts
    auth/
      components/
        LoginForm.tsx
        RegisterForm.tsx
        UserMenu.tsx
      hooks/
        useAuth.ts
      api/
        login.ts
        register.ts
      types/
        index.ts
      index.ts
  shared/
    ui/
      Button/
        Button.tsx
        Button.module.css
        Button.test.tsx
        Button.stories.tsx
        index.ts
      Input/
        ...
      Modal/
        ...
      Badge/
        ...
      Skeleton/
        ...
    lib/
      apiClient.ts
      constants.ts
      queryClient.ts
    utils/
      formatCurrency.ts
      validators.ts
      cn.ts
    hooks/
      useDebounce.ts
      useMediaQuery.ts
      useLocalStorage.ts
  layouts/
    MainLayout.tsx
    AuthLayout.tsx
    CheckoutLayout.tsx
  pages/
    HomePage.tsx
    ProductListingPage.tsx
    ProductDetailPage.tsx
    CartPage.tsx
    CheckoutPage.tsx
    LoginPage.tsx
    RegisterPage.tsx
    OrderConfirmationPage.tsx
  app/
    App.tsx
    Providers.tsx
    Router.tsx
    error-boundary.tsx
```

### Phase 2: Component Hierarchy Refactor

Establish a clear smart/dumb component separation.

#### Requirements

1. **Every feature exports a clean public API** via `index.ts`:
   ```ts
   // features/products/index.ts
   export { ProductCard, ProductGrid, ProductFilters } from './components';
   export { useProducts, useProductFilters } from './hooks';
   export type { Product, ProductFilters as ProductFilterType } from './types';
   ```

2. **No feature imports directly from another feature's internals:**
   ```tsx
   // ❌ BAD
   import { ProductCard } from '../products/components/ProductCard';

   // ✅ GOOD
   import { ProductCard } from '@/features/products';
   ```

3. **Containers handle state, presentational components handle rendering:**
   ```tsx
   // features/cart/containers/CartContainer.tsx
   import { useCart } from '../hooks/useCart';
   import { CartDrawer } from '../components/CartDrawer';

   export function CartContainer() {
     const { items, total, addItem, removeItem, isLoading } = useCart();

     return (
       <CartDrawer
         items={items}
         total={total}
         onAddItem={addItem}
         onRemoveItem={removeItem}
         isLoading={isLoading}
       />
     );
   }

   // features/cart/components/CartDrawer.tsx
   interface CartDrawerProps {
     items: CartItem[];
     total: Money;
     onAddItem: (id: string) => void;
     onRemoveItem: (id: string) => void;
     isLoading: boolean;
   }

   export function CartDrawer({ items, total, onAddItem, onRemoveItem, isLoading }: CartDrawerProps) {
     if (isLoading) return <CartSkeleton />;
     if (items.length === 0) return <EmptyCart />;
     return (
       <div>
         {items.map(item => <CartItem key={item.id} item={item} onAdd={onAddItem} onRemove={onRemoveItem} />)}
         <CartSummary total={total} />
       </div>
     );
   }
   ```

### Phase 3: Design System Documentation

Create a living design system with Storybook.

#### Component Inventory

Document every component in the `shared/ui` directory:

```
shared/ui/
  Button/
    Button.tsx
    Button.module.css
    Button.test.tsx
    Button.stories.tsx    ← Required for every component
    index.ts
  Input/
    Input.tsx
    Input.stories.tsx
    Input.test.tsx
    index.ts
  Modal/
    Modal.tsx
    Modal.stories.tsx
    Modal.test.tsx
    useModal.ts
    index.ts
  Select/
    ...
  Table/
    ...
  Badge/
    ...
  Skeleton/
    ...
  Spinner/
    ...
```

#### Storybook Stories

Every component must have stories covering:
- Default state
- All variants
- Loading state
- Error state (if applicable)
- Empty state (if applicable)
- Disabled state (if applicable)
- Accessibility tests

```tsx
// Button.stories.tsx
const meta = {
  title: 'Primitives/Button',
  component: Button,
  parameters: { layout: 'centered' },
  tags: ['autodocs'],
  argTypes: {
    variant: { control: 'select', options: ['primary', 'secondary', 'ghost', 'danger'] },
    size: { control: 'select', options: ['sm', 'md', 'lg'] },
  },
};

export const Primary = { args: { variant: 'primary', children: 'Submit' } };
export const Secondary = { args: { variant: 'secondary', children: 'Cancel' } };
export const Loading = { args: { variant: 'primary', loading: true, children: 'Loading' } };
export const Disabled = { args: { variant: 'primary', disabled: true, children: 'Disabled' } };
export const WithIcon = { args: { variant: 'primary', leftIcon: <Icon />, children: 'Save' } };
```

#### Design Token Documentation

```md
# Design Tokens

## Colors
| Token | Value | Usage |
|-------|-------|-------|
| --color-primary-500 | #3b82f6 | Primary buttons, links |
| --color-primary-600 | #2563eb | Hover state |
| --color-success | #22c55e | Success states |
| --color-error | #ef4444 | Error states |

## Typography
| Token | Value | Usage |
|-------|-------|-------|
| --font-sans | Inter, system-ui | Body text |
| --text-base | 1rem | Default body |
| --text-lg | 1.125rem | Section headings |

## Spacing
| Token | Value |
|-------|-------|
| --space-4 | 1rem |
| --space-8 | 2rem |
```

### Phase 4: Clean Architecture for Business Logic

Refactor complex business logic into domain entities and use cases.

#### Identify Business Logic

```ts
// Before — Logic scattered in components
function CartItem({ item }) {
  const discount = item.price > 100 ? item.price * 0.1 : 0;
  const shipping = item.weight > 10 ? 15 : 5;
  const tax = (item.price - discount) * 0.08;
  const total = item.price - discount + shipping + tax;

  return <div>{/* render */}</div>;
}
```

```ts
// After — Domain entity encapsulates business rules
// domain/entities/CartItem.ts
export class CartItem {
  constructor(
    public readonly product: Product,
    public readonly quantity: number,
  ) {}

  get subtotal(): Money {
    return this.product.price.multiply(this.quantity);
  }

  get discount(): Money {
    return this.product.price.greaterThan(100)
      ? this.product.price.multiply(0.1)
      : new Money(0);
  }

  get shipping(): Money {
    return this.product.weight > 10
      ? new Money(15)
      : new Money(5);
  }

  get tax(): Money {
    return this.subtotal.subtract(this.discount).multiply(0.08);
  }

  get total(): Money {
    return this.subtotal
      .subtract(this.discount)
      .add(this.shipping)
      .add(this.tax);
  }
}
```

#### Use Case Isolation

```ts
// application/use-cases/ApplyPromoCodeUseCase.ts
export class ApplyPromoCodeUseCase {
  constructor(
    private readonly cartRepo: CartRepository,
    private readonly promoRepo: PromoRepository,
  ) {}

  async execute(userId: string, code: string): Promise<Cart> {
    const cart = await this.cartRepo.get(userId);
    if (!cart) throw new Error('Cart not found');

    const promo = await this.promoRepo.findByCode(code);
    if (!promo) throw new Error('Invalid promo code');
    if (!promo.isValid()) throw new Error('Promo code expired');
    if (!promo.appliesTo(cart)) throw new Error('Promo does not apply');

    cart.applyPromotion(promo);
    await this.cartRepo.save(userId, cart);

    return cart;
  }
}
```

### Phase 5: Performance Architecture

#### Code Splitting by Feature

```tsx
// app/Router.tsx
import { lazy } from 'react';
import { Routes, Route } from 'react-router-dom';

const HomePage = lazy(() => import('@/pages/HomePage'));
const ProductListingPage = lazy(() => import('@/pages/ProductListingPage'));
const ProductDetailPage = lazy(() => import('@/pages/ProductDetailPage'));
const CartPage = lazy(() => import('@/pages/CartPage'));
const CheckoutPage = lazy(() => import('@/pages/CheckoutPage'));
```

#### State Architecture

```
State Classification:
├── Server State (TanStack Query)
│   ├── Product list — staleTime: 5min
│   ├── Product detail — staleTime: 10min
│   ├── Orders — staleTime: 30s
│   └── User profile — staleTime: 5min
├── Client State (Zustand)
│   ├── Cart — persistence: localStorage
│   ├── UI — modals, sidebar, toasts
│   └── Filters — temporary
├── URL State
│   ├── Product ID: /products/:id
│   ├── Category: ?category=electronics
│   ├── Page: ?page=2
│   └── Search: ?q=query
└── Component State
    ├── Form inputs
    ├── Accordion open/close
    └── Tooltip visibility
```

### Phase 6: Testing Architecture

```
src/
  features/products/
    components/
      __tests__/
        ProductCard.test.tsx
        ProductGrid.test.tsx
        ProductFilters.test.tsx
      ProductCard.tsx
      ProductGrid.tsx

  domain/
    entities/
      __tests__/
        Cart.test.ts        ← Pure logic, no mocking needed
        Money.test.ts
        Product.test.ts
    value-objects/
      __tests__/
        Money.test.ts

  application/
    use-cases/
      __tests__/
        AddToCartUseCase.test.ts   ← Mock repositories
        CheckoutUseCase.test.ts
```

### Deliverables Checklist

- [ ] Directory structure follows feature-based architecture
- [ ] All features have clean public API via `index.ts`
- [ ] Smart/dumb component separation implemented
- [ ] Design system with Storybook: at least 10 components documented
- [ ] Design tokens documented and used across all components
- [ ] Business logic extracted to domain entities
- [ ] Use cases handle complex operations
- [ ] Code splitting at route level
- [ ] State management follows the state classification table
- [ ] Barrel files optimized for tree-shaking
- [ ] No circular dependencies between features
- [ ] Tests for domain entities (no mocking needed)
- [ ] Tests for use cases (repositories mocked)
- [ ] Tests for UI components (interactions verified)
