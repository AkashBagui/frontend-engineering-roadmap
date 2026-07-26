# E-Commerce Platform

## Project Overview

Build a full-featured e-commerce platform with product catalog, shopping cart, checkout flow, payment processing, admin panel, search/filter, reviews, and authentication. This project introduces complex state management with Redux Toolkit, payment integration with Stripe, server-side data fetching with TanStack Query, and role-based access control.

## Learning Objectives

- Complex state management with Redux Toolkit (cart, checkout, auth)
- Server state management with TanStack Query (products, inventory, orders)
- Payment integration with Stripe (Checkout, Webhooks, refunds)
- Role-based access control (admin vs customer)
- Image management and optimization
- Search and filtering architecture
- Cart persistence (localStorage + database sync)
- Order lifecycle management
- Admin dashboard and analytics
- Performance optimization for product catalogs

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | SSR for SEO, API routes, App Router |
| TypeScript | Language | Type safety across data models |
| Redux Toolkit | Client state | Cart, UI state, auth — predictable state container |
| TanStack Query | Server state | Caching, pagination, optimistic updates |
| Stripe | Payments | Checkout, webhooks, subscription support |
| Auth.js | Authentication | OAuth, credentials, session management |
| Prisma + Postgres | Database | Type-safe ORM, migrations |
| Tailwind CSS | Styling | Utility classes, responsive, dark mode |
| Cloudinary | Images | Optimized product images, transformations |
| Algolia / Meilisearch | Search | Typo-tolerant full-text search with faceting |

## Feature List

### MVP Features
- Product catalog with categories and subcategories
- Product detail pages with images, descriptions, variants
- Search with filters (price, category, rating, brand)
- Shopping cart with quantity management
- Checkout flow (address, shipping, payment)
- Stripe payment integration
- Order confirmation and history
- User authentication and profiles
- Admin dashboard (products, orders, users)
- Product reviews and ratings

### Advanced Features
- Wishlist functionality
- Related products / cross-sell recommendations
- Coupon and discount system
- Inventory management with low-stock alerts
- Product comparison tool
- Guest checkout
- Order tracking with status updates
- Multi-currency support
- Abandoned cart recovery emails
- Analytics dashboard (revenue, trends, top products)

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx                # Root layout, providers
│   ├── page.tsx                  # Home — featured, categories
│   ├── products/
│   │   ├── page.tsx              # Product listing + filters
│   │   └── [slug]/
│   │       └── page.tsx          # Product detail
│   ├── categories/
│   │   └── [slug]/
│   │       └── page.tsx          # Category page
│   ├── cart/
│   │   └── page.tsx              # Cart page
│   ├── checkout/
│   │   └── page.tsx              # Checkout flow (multi-step)
│   ├── account/
│   │   ├── page.tsx              # Dashboard
│   │   ├── orders/
│   │   │   └── [id]/
│   │   │       └── page.tsx      # Order detail
│   │   └── settings/
│   │       └── page.tsx          # Profile, addresses
│   ├── auth/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   ├── register/
│   │   │   └── page.tsx
│   │   └── forgot-password/
│   │       └── page.tsx
│   └── admin/
│       ├── layout.tsx            # Admin layout (protected)
│       ├── page.tsx              # Dashboard
│       ├── products/
│       │   ├── page.tsx          # Product management
│       │   └── [id]/
│       │       └── page.tsx      # Edit product
│       ├── orders/
│       │   └── page.tsx          # Order management
│       ├── categories/
│       │   └── page.tsx          # Category management
│       └── users/
│           └── page.tsx          # User management
├── components/
│   ├── layout/
│   │   ├── Header.tsx            # Search, cart icon, auth
│   │   ├── Footer.tsx
│   │   └── MobileNav.tsx
│   ├── product/
│   │   ├── ProductCard.tsx
│   │   ├── ProductGrid.tsx
│   │   ├── ProductImages.tsx     # Gallery + zoom
│   │   ├── ProductInfo.tsx
│   │   ├── VariantSelector.tsx
│   │   ├── ReviewList.tsx
│   │   └── ReviewForm.tsx
│   ├── cart/
│   │   ├── CartItem.tsx
│   │   ├── CartSummary.tsx
│   │   └── CartDrawer.tsx        {/* Slide-out cart */}
│   ├── checkout/
│   │   ├── ShippingForm.tsx
│   │   ├── PaymentForm.tsx       # Stripe Elements
│   │   ├── OrderReview.tsx
│   │   └── OrderConfirmation.tsx
│   ├── filters/
│   │   ├── SearchBar.tsx
│   │   ├── PriceRange.tsx
│   │   ├── CategoryFilter.tsx
│   │   └── SortSelect.tsx
│   └── admin/
│       ├── Sidebar.tsx
│       ├── StatsCard.tsx
│       ├── SalesChart.tsx
│       ├── RecentOrders.tsx
│       └── DataTable.tsx
├── store/
│   ├── index.ts                  # Redux store configuration
│   ├── slices/
│   │   ├── cartSlice.ts
│   │   ├── authSlice.ts
│   │   ├── uiSlice.ts
│   │   └── checkoutSlice.ts
│   └── hooks.ts                  # useAppDispatch, useAppSelector
├── hooks/
│   ├── useProducts.ts            # TanStack Query hooks
│   ├── useCart.ts
│   ├── useOrders.ts
│   └── useDebounce.ts
├── lib/
│   ├── stripe.ts                 # Stripe client + server
│   ├── prisma.ts                 # Database client
│   ├── auth.ts                   # Auth.js configuration
│   ├── utils.ts
│   └── constants.ts
├── app/api/
│   ├── products/                 # Product CRUD
│   ├── cart/                     # Cart sync
│   ├── checkout/                 # Checkout + payment
│   ├── orders/                   # Order management
│   ├── stripe/                   # Webhooks
│   ├── auth/                     # Auth routes
│   └── upload/                   # Image upload
└── types/
    ├── product.ts
    ├── cart.ts
    ├── order.ts
    └── user.ts
```

## State Management Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     REDUX STORE (Client State)                  │
├─────────────────┬─────────────────┬─────────────────┬──────────┤
│   cartSlice     │   authSlice     │    uiSlice      │checkout  │
│ - items[]       │ - user          │ - sidebarOpen   │Slice     │
│ - total         │ - session       │ - activeModal   │- step    │
│ - coupon         │ - loading       │ - toastQueue[]  │- shipping │
│ - addItem()     │ - login()       │ - openModal()   │- payment  │
│ - removeItem()  │ - logout()      │ - showToast()   │          │
│ - updateQty()   │                 │                 │          │
└─────────────────┴─────────────────┴─────────────────┴──────────┘
         │                  ▲                   │         ▲
         │                  │                   │         │
         ▼                  │                   ▼         │
┌─────────────────────────────────────────────────────────────────┐
│                  TanStack QUERY (Server State)                  │
├─────────────────┬─────────────────┬─────────────────┬──────────┤
│   products      │    orders       │    categories   │ reviews  │
│ - useProducts() │ - useOrders()   │ - useCategory()│ - use    │
│ - useProduct()  │ - useOrder()    │                 │Reviews() │
│ - prefetchPage  │ - createOrder   │                 │          │
│   ^             │   mutation      │                 │          │
│   │             │                 │                 │          │
│   └───── API Routes (REST) ─────┘                 │          │
│                                                     │          │
│   Cache strategies: staleTime, gcTime, refetchOn     │          │
└─────────────────────────────────────────────────────────────────┘
```

## Component Tree

```
<Providers>                    {/* Redux + Query + Auth + Theme */}
  <Header>
    <SearchBar />             {/* Algolia/Algolia instantsearch */}
    <NavLinks />
    <CartDrawer />            {/* Slide-out with CartItem list */}
    <AuthButtons />           {/* Login/Register or Avatar */}
  </Header>
  <main>{children}</main>
  <Footer />
</Providers>

ProductListingPage:
  <FilterSidebar>
    <CategoryFilter />
    <PriceRange />
    <RatingFilter />
    <BrandFilter />
    <ClearFilters />
  </FilterSidebar>
  <ProductGrid>
    <ProductCard />*          {/* Image, name, price, rating, add-to-cart */}
    <Pagination />
  </ProductGrid>

ProductDetailPage:
  <Breadcrumbs />
  <ProductImages>
    <MainImage />             {/* With zoom on hover */}
    <ThumbnailList />
  </ProductImages>
  <ProductInfo>
    <Title />
    <Price />
    <VariantSelector />       {/* Size, color, etc. */}
    <QuantitySelector />
    <AddToCartButton />
    <WishlistButton />
  </ProductInfo>
  <ProductTabs>
    <Description />
    <Specifications />
    <ReviewList>
      <ReviewItem />*
      <ReviewForm />
    </ReviewList>
  </ProductTabs>
  <RelatedProducts>
    <ProductCard />*
  </RelatedProducts>

CheckoutPage:
  <CheckoutSteps />           {/* Progress indicator */}
  <ShippingForm />            {/* Address + shipping method */}
  <PaymentForm />             {/* Stripe Elements */}
  <OrderReview />             {/* Items summary */}
  <PlaceOrderButton />
```

## Database Schema

```prisma
model User {
  id        String    @id @default(cuid())
  email     String    @unique
  name      String?
  password  String?   // Hashed
  role      Role      @default(CUSTOMER)
  cart      Cart?
  orders    Order[]
  reviews   Review[]
  addresses Address[]
  createdAt DateTime  @default(now())
}

enum Role { CUSTOMER ADMIN }

model Product {
  id          String    @id @default(cuid())
  slug        String    @unique
  name        String
  description String
  price       Decimal
  compareAt   Decimal?
  sku         String    @unique
  inventory   Int       @default(0)
  images      String[]  // URLs
  published   Boolean   @default(false)
  categoryId  String
  category    Category  @relation(fields: [categoryId], references: [id])
  variants    Variant[]
  reviews     Review[]
  orderItems  OrderItem[]
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt
}

model Variant {
  id        String  @id @default(cuid())
  name      String  // e.g. "Size M", "Red"
  sku       String  @unique
  price     Decimal?
  inventory Int     @default(0)
  productId String
  product   Product @relation(fields: [productId], references: [id])
}

model Category {
  id       String    @id @default(cuid())
  slug     String    @unique
  name     String
  parentId String?
  parent   Category? @relation("CategoryHierarchy", fields: [parentId], references: [id])
  children Category[] @relation("CategoryHierarchy")
  products Product[]
}

model Cart {
  id        String     @id @default(cuid())
  userId    String     @unique
  user      User       @relation(fields: [userId], references: [id])
  items     CartItem[]
  updatedAt DateTime   @updatedAt
}

model CartItem {
  id        String  @id @default(cuid())
  cartId    String
  cart      Cart    @relation(fields: [cartId], references: [id])
  productId String
  product   Product @relation(fields: [productId], references: [id])
  variantId String?
  quantity  Int     @default(1)
}

model Order {
  id          String       @id @default(cuid())
  userId      String
  user        User         @relation(fields: [userId], references: [id])
  items       OrderItem[]
  total       Decimal
  status      OrderStatus  @default(PENDING)
  shipping    Json?        // Address object
  paymentId   String?      // Stripe payment intent ID
  createdAt   DateTime     @default(now())
  updatedAt   DateTime     @updatedAt
}

enum OrderStatus { PENDING CONFIRMED PROCESSING SHIPPED DELIVERED CANCELLED REFUNDED }

model OrderItem {
  id        String  @id @default(cuid())
  orderId   String
  order     Order   @relation(fields: [orderId], references: [id])
  productId String
  product   Product @relation(fields: [productId], references: [id])
  variantId String?
  quantity  Int
  price     Decimal // Snapshot at purchase time
}

model Review {
  id        String   @id @default(cuid())
  rating    Int      // 1-5
  title     String?
  content   String?
  productId String
  product   Product  @relation(fields: [productId], references: [id])
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  createdAt DateTime @default(now())
}

model Address {
  id         String @id @default(cuid())
  userId     String
  user       User   @relation(fields: [userId], references: [id])
  label      String // Home, Work, etc.
  line1      String
  line2      String?
  city       String
  state      String
  zip        String
  country    String
  isDefault  Boolean @default(false)
}
```

## Route Structure

| Route | Type | Auth | Description |
|-------|------|------|-------------|
| `/` | SSG | — | Home page |
| `/products` | SSR | — | Product listing |
| `/products/[slug]` | SSR | — | Product detail |
| `/categories/[slug]` | SSR | — | Category page |
| `/cart` | Client | — | Cart page |
| `/checkout` | Client | Required | Multi-step checkout |
| `/account` | Client | Required | User dashboard |
| `/account/orders/[id]` | Client | Required | Order detail |
| `/account/settings` | Client | Required | Profile settings |
| `/auth/login` | Client | — | Login page |
| `/auth/register` | Client | — | Register page |
| `/admin` | Client | Admin | Admin dashboard |
| `/admin/products` | Client | Admin | Product management |
| `/admin/orders` | Client | Admin | Order management |
| `/admin/categories` | Client | Admin | Category management |
| `/admin/users` | Client | Admin | User management |

## Key Implementation Considerations

- Use Redux Toolkit for cart — persist cart to localStorage + sync with server on login
- Use TanStack Query for all server data — configure `staleTime` based on data freshness needs
- Implement Stripe Checkout for payments — handle redirect flow + webhook for confirmation
- Use optimistic updates for cart operations (add, remove, update quantity)
- Implement infinite scrolling or cursor-based pagination for product listing
- Use Next.js middleware for admin route protection
- Handle Stripe webhook idempotency (verify signature, handle duplicates)
- Implement proper error boundaries for checkout flow
- Use Server Components for product listing (SEO-friendly), Client Components for interactivity

## Performance Considerations

- Product listing: implement virtual scrolling for large catalogs (1000+ products)
- Product images: use Cloudinary transformations (responsive widths, WebP, lazy loading)
- Search: use Algolia or Meilisearch for typo-tolerant, instant search
- Cart: use localStorage as source of truth, sync to server on auth
- Code splitting: lazy load checkout components (heavy Stripe.js)
- Prefetch product detail pages on hover (TanStack Query `prefetchQuery`)
- Use React.lazy for admin panel (not needed for most users)
- Bundle analysis: monitor Redux + Stripe bundle size impact

## Deployment Strategy

1. **Vercel** for Next.js app (SSR + API routes)
2. **Neon.tech** or **Supabase** for Postgres database
3. **Stripe** for payment processing (test + live modes)
4. **Cloudinary** for image hosting and optimization
5. **Algolia** for search (optional, can start with Fuse.js)
6. **Environment variables**: database, Stripe keys, Auth.js secret, Cloudinary
7. **CI/CD**: GitHub Actions → lint → test → build → deploy to Vercel
8. **Monitoring**: Sentry for error tracking, Vercel Analytics for performance

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Data model design, user flows, wireframes | 2 |
| Foundation | Next.js setup, Redux store, Prisma schema, auth | 2 |
| Products | Listing, detail, search, filters, image gallery | 3 |
| Cart | Cart slice, UI, persistence, sync | 2 |
| Checkout | Multi-step flow, shipping, Stripe integration | 3 |
| Orders | Order creation, history, status management | 2 |
| Admin | Dashboard, product/order management | 3 |
| Reviews | Review CRUD, rating display, moderation | 1.5 |
| Polish | Performance, responsive, error states, loading | 2 |
| Deploy | CI/CD, Stripe webhooks, environment config | 1 |
| **Total** | | **~12-20 days** |

## Learning Resources

- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [TanStack Query in Next.js](https://tanstack.com/query/latest/docs/react/guides/ssr)
- [Stripe Checkout Integration](https://stripe.com/docs/payments/checkout)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [Algolia InstantSearch](https://www.algolia.com/doc/guides/building-search-ui/what-is-instantsearch/js/)
- [Auth.js with Next.js](https://authjs.dev/reference/nextjs)
- [Cloudinary Image Optimization](https://cloudinary.com/documentation/image_optimization)
