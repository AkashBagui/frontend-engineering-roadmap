# Project Structure

## Scalable Folder Structures

The way you organize your frontend project has a direct impact on developer velocity, code discoverability, and maintainability.

## Type-Based Structure (Flat)

Organized by technical role of the file.

```
src/
  components/
    Button.tsx
    Card.tsx
    Header.tsx
    Modal.tsx
  pages/
    Home.tsx
    About.tsx
    Products.tsx
  hooks/
    useAuth.ts
    useDebounce.ts
    useMediaQuery.ts
  utils/
    formatDate.ts
    api.ts
    validators.ts
  types/
    user.ts
    product.ts
  styles/
    globals.css
    variables.css
```

**Pros**: Simple, good for small projects (< 5 pages).
**Cons**: Doesn't scale — files become hard to find, no domain grouping.

## Feature-Based Structure (Scalable)

Organized by business domain / feature.

```
src/
  features/
    auth/
      api/
        login.ts
        register.ts
      components/
        LoginForm.tsx
        RegisterForm.tsx
        OAuthButtons.tsx
      hooks/
        useAuth.ts
        useSession.ts
      types/
        index.ts
      utils/
        session.ts
      index.ts                # Public API
    products/
      api/
        getProducts.ts
        createProduct.ts
      components/
        ProductList.tsx
        ProductCard.tsx
        ProductFilter.tsx
      hooks/
        useProducts.ts
        useProductFilters.ts
      types/
        index.ts
      utils/
        productHelpers.ts
    cart/
      components/
        CartDrawer.tsx
        CartItem.tsx
      hooks/
        useCart.ts
      store/
        cartStore.ts           # Zustand/RTK slice
      types/
        index.ts
  shared/
    ui/                        # Design system
      Button/
        Button.tsx
        Button.test.tsx
        Button.stories.tsx
        index.ts
      Modal/
        Modal.tsx
        index.ts
    lib/
      api.ts                   # Axios/fetch wrapper
      constants.ts
      types.ts
    utils/
      formatDate.ts
      validators.ts
  app/                         # App setup
    Router.tsx
    Store.tsx
    Providers.tsx
  layouts/
    MainLayout.tsx
    AuthLayout.tsx
  pages/                       # Page-level routing
    HomePage.tsx
    ProductPage.tsx
    CartPage.tsx
```

## Colocation

Keep files that change together close together.

```
❌ BAD — Everything separated by type:
src/
  components/ProductCard.tsx
  hooks/useProduct.ts
  styles/product-card.css
  tests/ProductCard.test.tsx
  stories/ProductCard.stories.tsx

✅ GOOD — Colocated by feature:
src/features/products/
  components/ProductCard.tsx
  components/ProductCard.test.tsx
  components/ProductCard.stories.tsx
  hooks/useProduct.ts
  styles.css              # Or CSS modules in component
```

## Barrel Files

Re-export from feature modules to create clean public APIs.

```ts
// src/features/products/index.ts
export { ProductList } from './components/ProductList';
export { ProductCard } from './components/ProductCard';
export { useProducts } from './hooks/useProducts';
export type { Product, ProductFilters } from './types';
```

```tsx
// Using the public API
import { ProductList, useProducts } from '@/features/products';
import type { Product } from '@/features/products';
```

**Barrel file warning**: Large barrel files can hurt tree-shaking. Use them at feature level, not as one giant `index.ts`.

## Small App Structure (< 10 pages)

```
src/
  components/
    Button.tsx
    Card.tsx
    Layout.tsx
  pages/
    Home.tsx
    About.tsx
    Contact.tsx
  hooks/
    useAuth.ts
    useFetch.ts
  utils/
    api.ts
    helpers.ts
  App.tsx
  main.tsx
```

## Medium App Structure (10-50 pages)

```
src/
  features/
    auth/
    dashboard/
    products/
    settings/
  shared/
    ui/
    lib/
    utils/
  app/
    router.tsx
    store.ts
    providers.tsx
  layouts/
  pages/
  styles/
```

## Large App Structure (50+ pages, multiple teams)

```
src/
  features/
    auth/
    billing/
    dashboard/
    reports/
    team/
    projects/
    integrations/
    settings/
    admin/
  shared/
    ui/              # Design system
    lib/             # API client, config
    utils/           # Pure utility functions
    hooks/           # Shared hooks
    types/           # Shared TypeScript types
    constants/       # App-wide constants
  app/               # App shell
    providers.tsx
    router.tsx
    store.ts
    error-boundary.tsx
  layouts/           # Page layouts
  middleware/        # Auth guards, feature flags
  config/            # Environment config, feature flags
  workers/           # Web Workers
  i18n/              # Internationalization
  test/              # Test utilities, mocks
    setup.ts
    mocks/
    utils/
```

## Monorepo Structure (Multiple Applications)

```
packages/
  ui/                # Shared design system
    src/
      Button.tsx
      Modal.tsx
    package.json
  config/            # Shared configs
    eslint/
    typescript/
    tailwind/
  utils/             # Shared utilities
    date.ts
    format.ts
apps/
  web/               # Main web app
    src/
      features/
      app/
    package.json
  admin/             # Admin dashboard
    src/
      features/
      app/
    package.json
  docs/              # Documentation site
    src/
    package.json
  mobile-api/        # API for mobile apps
    src/
    package.json
```

## Key Principles

1. **Structure by domain, not by type** — Group related components, hooks, and utilities together.
2. **Colocate by change frequency** — Files that change together should be close together.
3. **Public API per feature** — Each feature exports a clean interface via `index.ts`.
4. **Shared code evolves carefully** — Shared utilities should be generic; avoid leaky abstractions.
5. **Keep the root shallow** — More than 5-7 root folders suggests over-organization.
6. **Absolute imports** — Use path aliases (`@/features/products`) over relative imports (`../../../features/products`).

```json
// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"],
      "@features/*": ["./src/features/*"],
      "@shared/*": ["./src/shared/*"]
    }
  }
}
```

## Summary

Start with a simple feature-based structure and evolve as the app grows. The right structure reduces cognitive load, makes code reviews easier, and keeps the codebase maintainable as the team scales.
