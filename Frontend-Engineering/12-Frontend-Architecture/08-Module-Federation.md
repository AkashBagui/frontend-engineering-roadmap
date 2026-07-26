# Module Federation

## What is Module Federation?

Module Federation is a Webpack 5 feature that allows a JavaScript application to **dynamically load code from another application** at runtime. It is the primary technical enabler for micro-frontends.

## Core Concepts

```mermaid
flowchart LR
    subgraph "Host Application"
        HOST[Shell / Host]
        HOST_SHARED[Shared: React, UI Lib]
    end
    
    subgraph "Remote Application A"
        RA[Products App]
        RA_EXP[Exposes: ProductList, ProductDetail]
        RA_SHARED[Shared: React, UI Lib]
    end
    
    subgraph "Remote Application B"
        RB[Cart App]
        RB_EXP[Exposes: CartDrawer, CartSummary]
        RB_SHARED[Shared: React, UI Lib]
    end
    
    HOST -->|"import('products/ProductList')"| RA
    HOST -->|"import('cart/CartDrawer')"| RB
    HOST_SHARED -->|"Singleton"| RA_SHARED
    HOST_SHARED -->|"Singleton"| RB_SHARED
```

## Host Configuration

```ts
// apps/shell/webpack.config.ts
import { ModuleFederationPlugin } from 'webpack.container';

const config = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'shell',
      filename: 'remoteEntry.js',
      
      // Remotes that this host consumes
      remotes: {
        products: 'products@http://localhost:3001/remoteEntry.js',
        cart: 'cart@http://localhost:3002/remoteEntry.js',
        checkout: 'checkout@http://localhost:3003/remoteEntry.js',
      },
      
      // Components this host exposes
      exposes: {
        './Navigation': './src/components/Navigation',
        './AuthProvider': './src/providers/AuthProvider',
      },
      
      // Shared dependencies
      shared: {
        react: {
          singleton: true,
          requiredVersion: '^19.0.0',
          eager: true,
        },
        'react-dom': {
          singleton: true,
          requiredVersion: '^19.0.0',
          eager: true,
        },
        'react-router-dom': {
          singleton: true,
          requiredVersion: '^7.0.0',
        },
        '@acme/ui': {
          singleton: true,
          requiredVersion: '^1.0.0',
        },
      },
    }),
  ],
};
```

## Remote Configuration

```ts
// apps/products/webpack.config.ts
import { ModuleFederationPlugin } from 'webpack.container';

const config = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'products',
      filename: 'remoteEntry.js',
      
      exposes: {
        './ProductList': './src/components/ProductList',
        './ProductDetail': './src/components/ProductDetail',
        './ProductSearch': './src/components/ProductSearch',
      },
      
      remotes: {
        shell: 'shell@http://localhost:3000/remoteEntry.js',
      },
      
      shared: {
        react: { singleton: true, requiredVersion: '^19.0.0' },
        'react-dom': { singleton: true, requiredVersion: '^19.0.0' },
        '@acme/ui': { singleton: true, requiredVersion: '^1.0.0' },
      },
    }),
  ],
};
```

## Dynamic Loading in Host

```tsx
// apps/shell/src/App.tsx
import React, { Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

// Static import (determined at build time)
const ProductList = React.lazy(() => import('products/ProductList'));
const CartDrawer = React.lazy(() => import('cart/CartDrawer'));

export function App() {
  return (
    <div>
      <Navigation />
      <Suspense fallback={<PageSkeleton />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/products" element={<ProductList />} />
          <Route path="/cart" element={<CartDrawer />} />
        </Routes>
      </Suspense>
    </div>
  );
}
```

### Dynamic Remote Loading

For truly runtime-determined remotes (e.g., based on tenant):

```tsx
// apps/shell/src/loadRemote.ts
import { init, loadRemote } from '@module-federation/runtime';

// Initialize MF runtime
init({
  name: 'shell',
  remotes: [
    {
      name: 'products',
      entry: 'http://localhost:3001/remoteEntry.js',
    },
  ],
});

// Load remote dynamically
export async function loadRemoteComponent<T>(
  remoteName: string,
  componentPath: string
): Promise<React.ComponentType<T>> {
  const factory = await loadRemote(`${remoteName}/${componentPath}`);
  return factory();
}

// Usage in component
function LazyProductList() {
  const [Comp, setComp] = useState<React.ComponentType | null>(null);

  useEffect(() => {
    loadRemoteComponent('products', 'ProductList').then(setComp);
  }, []);

  if (!Comp) return <Skeleton />;
  return <Comp category="electronics" />;
}
```

## Shared Dependencies Strategy

```mermaid
flowchart TD
    A[Webpack encounters shared dep] --> B{Version satisfies<br/>all consumers?}
    B -->|Yes| C[Load single instance]
    B -->|No| D{Singleton required?}
    D -->|Yes| E[Use highest satisfying<br/>version]
    D -->|No| F[Load multiple instances<br/>(one per consumer)]
    
    E --> G[Warning: version mismatch]
    F --> H[Increased bundle size]
```

### Version Conflicts

```ts
shared: {
  react: {
    singleton: true,
    requiredVersion: '^18.2.0 || ^19.0.0',
    // If shell has React 19, all remotes must be compatible
  },
  'lodash': {
    singleton: false,  // Each MFE can have its own lodash version
  },
}
```

## Integration with Vite

Use `@module-federation/vite` for Vite projects:

```ts
// vite.config.ts (Remote)
import { federation } from '@module-federation/vite';

export default defineConfig({
  plugins: [
    federation({
      name: 'products',
      filename: 'remoteEntry.js',
      exposes: {
        './ProductList': './src/components/ProductList.tsx',
      },
      shared: ['react', 'react-dom'],
    }),
  ],
});
```

## Deployment Architecture

```mermaid
flowchart TD
    subgraph "CI/CD Pipeline"
        BUILD_SHELL["Build Shell"]
        BUILD_PRODUCTS["Build Products MFE"]
        BUILD_CART["Build Cart MFE"]
    end
    
    subgraph "S3 / Static Hosting"
        SHELL_S3["s3://app/shell/"]
        PROD_S3["s3://app/products/"]
        CART_S3["s3://app/cart/"]
    end
    
    subgraph "CDN"
        CDN_SHELL["cdn.app.com/shell/*"]
        CDN_PROD["cdn.app.com/products/*"]
        CDN_CART["cdn.app.com/cart/*"]
    end
    
    subgraph "Browser"
        BROWSER["Host loads shell →<br/>shell loads remoteEntry.js →<br/>remotes load components"]
    end
    
    BUILD_SHELL --> SHELL_S3 --> CDN_SHELL
    BUILD_PRODUCTS --> PROD_S3 --> CDN_PROD
    BUILD_CART --> CART_S3 --> CDN_CART
    
    CDN_SHELL --> BROWSER
    CDN_PROD --> BROWSER
    CDN_CART --> BROWSER
```

## Integration Flow at Runtime

```mermaid
sequenceDiagram
    participant User
    participant Shell
    participant ProductsCDN as Products MFE CDN
    participant CartCDN as Cart MFE CDN

    User->>Shell: Visits /products
    Shell->>ProductsCDN: Fetch products/remoteEntry.js
    ProductsCDN-->>Shell: Module registry + shared deps config
    Shell->>Shell: Check shared deps (React, etc.)
    Shell->>ProductsCDN: Fetch ProductList chunk
    ProductsCDN-->>Shell: React.lazy resolves
    Shell->>User: Render ProductList component
    
    User->>Shell: Navigates to /cart
    Shell->>CartCDN: Fetch cart/remoteEntry.js
    CartCDN-->>Shell: Module registry
    Shell->>CartCDN: Fetch CartDrawer chunk
    CartCDN-->>Shell: Component loaded
    Shell->>User: Render CartDrawer
```

## Fallback Pattern

```tsx
// apps/shell/src/RemoteWrapper.tsx
export function RemoteWrapper({ fallback, children }) {
  const [error, setError] = useState(false);

  if (error) {
    return fallback || <div>This section is unavailable</div>;
  }

  return (
    <ErrorBoundary
      onError={() => setError(true)}
      fallback={fallback}
    >
      <Suspense fallback={<RemoteSkeleton />}>
        {children}
      </Suspense>
    </ErrorBoundary>
  );
}

// Usage
<RemoteWrapper fallback={<ProductListFallback />}>
  <ProductList />
</RemoteWrapper>
```

## Summary

| Aspect | Module Federation |
|--------|------------------|
| **Runtime loading** | Dynamic, lazy-loaded from remote URLs |
| **Shared deps** | Singleton or multiple instances |
| **Version management** | Required version ranges, fallbacks |
| **Build tool** | Webpack 5 (primary), Vite (via plugin) |
| **Scoping** | CSS scoping needed manually (CSS Modules/CSS-in-JS) |
| **Error handling** | Error boundaries required per remote |
| **Performance** | Network overhead per remote, caching via CDN |

Module Federation is the production-grade solution for micro-frontends. It enables independent deployment and technology diversity but requires careful dependency management and error handling.
