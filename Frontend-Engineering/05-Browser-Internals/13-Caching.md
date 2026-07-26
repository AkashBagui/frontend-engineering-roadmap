# Caching

## Overview

Browser caching is one of the most powerful performance optimizations. When done correctly, it eliminates entire network round trips, dramatically improving page load times. There are multiple cache layers in the browser.

## Cache Hierarchy

```mermaid
graph TD
    A[User Requests URL] --> B{In Service Worker?}
    B -->|Yes| C[Service Worker Cache]
    C --> D{Hit?}
    D -->|Yes| E[Return Cached Response]
    D -->|No| F
    
    B -->|No| F{In Memory Cache?}
    F -->|Yes| G[Return Immediately]
    F -->|No| H{In Disk Cache?}
    H -->|Yes| I[Read from Disk<br/>(async, slower than memory)]
    H -->|No| J[Network Request]
    
    J --> K{Has Cache-Control?}
    K -->|Fresh| L[Store in Disk Cache]
    K -->|Stale| M[Revalidate with Server]
    M --> N{Not Modified?}
    N -->|304| O[Use Stale + Update Cache]
    N -->|200| P[Use New Response]
    
    L --> Q[Also load into Memory<br/>if frequently used]
    O --> G
    P --> Q
    
    style E fill:#6f6,stroke:#333
    style G fill:#6f6,stroke:#333
    style J fill:#f99,stroke:#333
```

## Cache Types

### 1. Memory Cache

Stored in RAM. Fastest access, but cleared when the tab or browser process is closed.

```
┌─────────────────────────────────────────────┐
│ Memory Cache (RAM)                          │
│                                             │
│  Size: Limited (varies by browser)          │
│  Speed: ~0ms (instant)                      │
│  Persistence: Session only                  │
│  Priority: Checked first before disk cache  │
│                                             │
│  Stored:                                    │
│  ├─ Current page resources                  │
│  ├─ Inline scripts/styles                   │
│  └─ Recently fetched images                 │
└─────────────────────────────────────────────┘
```

### 2. Disk Cache

Stored on disk. Slower than memory cache but persists across browser sessions.

```
┌─────────────────────────────────────────────┐
│ Disk Cache (Chrome: Cache folder ~300 MB)   │
│                                             │
│  Location: %LOCALAPPDATA%\Google\Chrome\    │
│            User Data\Default\Cache          │
│                                             │
│  Size: Up to ~300 MB (configurable)         │
│  Speed: ~3-10ms (varies by disk type)       │
│  Persistence: Across sessions               │
│  Eviction: LRU (Least Recently Used)       │
│                                             │
│  Stored:                                    │
│  ├─ Images, CSS, JS files                   │
│  ├─ Fonts                                   │
│  └─ Media files (audio/video)               │
└─────────────────────────────────────────────┘
```

### 3. Service Worker Cache

Programmable cache via the Cache API. Full control over caching strategy.

```
┌─────────────────────────────────────────────┐
│ Service Worker Cache (JavaScript controlled) │
│                                             │
│  API: caches.open(), cache.add(),           │
│       cache.match()                         │
│                                             │
│  Size: Up to available disk space           │
│        (usually ~60% of disk per origin)    │
│                                             │
│  Persistence: Until manually cleared        │
│               or storage quota exceeded     │
│                                             │
│  Stored:                                    │
│  ├─ App shell (HTML, CSS, JS)               │
│  ├─ API responses (offline data)            │
│  └─ Images, fonts (dynamic caching)         │
└─────────────────────────────────────────────┘
```

### 4. HTTP Cache (Network Cache)

Controlled by HTTP headers. The browser respects server-side cache directives.

```
┌─────────────────────────────────────────────┐
│ HTTP Cache Layers                           │
│                                             │
│  Browser Cache                              │
│  ↓ (checked first)                          │
│  Proxy Cache (CDN, corporate proxy)         │
│  ↓                                          │
│  Origin Server Cache (server-side)          │
└─────────────────────────────────────────────┘
```

## HTTP Caching Headers

### Cache-Control

```http
# Strong caching (no request to server until max-age expires)
Cache-Control: public, max-age=31536000, immutable

# No caching (always revalidate)
Cache-Control: no-cache

# No storing at all (for sensitive data)
Cache-Control: no-store

# Must revalidate with server before using cached copy
Cache-Control: must-revalidate

# Stale content can be used for a limited time while revalidating
Cache-Control: max-age=3600, stale-while-revalidate=86400

# Stale content can be served if server is unreachable
Cache-Control: max-age=3600, stale-if-error=86400

# Private (only browser cache, not CDN/proxy)
Cache-Control: private, max-age=3600
```

### ETag and Last-Modified

```http
# Server response (first request)
HTTP/1.1 200 OK
Cache-Control: public, max-age=0
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Last-Modified: Wed, 21 Oct 2025 07:28:00 GMT

# Client revalidation (when cache is stale)
GET /resource HTTP/1.1
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
If-Modified-Since: Wed, 21 Oct 2025 07:28:00 GMT

# Server response (resource unchanged)
HTTP/1.1 304 Not Modified
# No body! Just headers.
```

### Expires (Legacy)

```http
# Older header, superseded by Cache-Control
Expires: Thu, 31 Dec 2025 23:59:59 GMT
```

## Caching Decision Flow

```mermaid
flowchart TD
    A[Response from Server] --> B{Has Cache-Control?}
    
    B -->|no-store| C[Don't store anywhere]
    B -->|no-cache| D[Store but always revalidate]
    B -->|max-age| E{Has ETag or<br/>Last-Modified?}
    
    E -->|Yes| F[Store with validation token]
    E -->|No| G[Store until max-age expires]
    
    F --> H[Cache fresh]
    G --> H
    
    H --> I[Request for same URL]
    I --> J{max-age expired?}
    
    J -->|No| K[Serve from cache<br/>(zero latency)]
    J -->|Yes| L{Has ETag?}
    
    L -->|Yes| M[GET / If-None-Match]
    L -->|No| N[GET full request]
    
    M --> O{Server responds}
    O -->|304| P[Serve cached version]
    O -->|200| Q[Use new response, update cache]
    N --> O
    
    style K fill:#6f6,stroke:#333
    style P fill:#6f6,stroke:#333
    style Q fill:#99f,stroke:#333
```

## Caching Strategies by Resource Type

### Static Assets (versioned)

```nginx
# Nginx configuration example
location /static/ {
    add_header Cache-Control "public, max-age=31536000, immutable";
    # Immutable means browser won't even revalidate on refresh
}
```

```html
<!-- Use content hash in filename for cache busting -->
<link rel="stylesheet" href="/static/styles.a1b2c3d4.css">
<script src="/static/app.e5f6g7h8.js"></script>
<img src="/static/logo.9i0j1k2l.webp">
```

**Strategy**: Cache forever (1 year), change URL when content changes.

### HTML (non-versioned)

```nginx
# Nginx
location / {
    add_header Cache-Control "public, max-age=0, must-revalidate";
}
```

**Strategy**: Always revalidate (ETag-based). HTML changes frequently.

### API Responses

```javascript
// Server response with caching
app.get('/api/users', (req, res) => {
  res.set('Cache-Control', 'public, max-age=60, stale-while-revalidate=300');
  res.json(users);
});
```

**Strategy**: Short max-age + stale-while-revalidate for fresh-but-forgiving caching.

### Images

```nginx
# Nginx
location /images/ {
    add_header Cache-Control "public, max-age=86400, stale-while-revalidate=604800";
}
```

**Strategy**: Cache for 1 day, serve stale for up to 7 days while revalidating.

## Cache Busting

When content changes, the browser needs to be forced to fetch the new version.

### 1. Filename Hash (Best)

```html
<!-- Build tool generates hash -->
<link rel="stylesheet" href="styles.8c3e4a2b.css">
<!-- After update: -->
<link rel="stylesheet" href="styles.f9d0e1c2.css">
```

### 2. Query String (Less Reliable)

```html
<link rel="stylesheet" href="styles.css?v=1.0.0">
<!-- Some proxies ignore query strings for caching -->
```

### 3. Fingerprinting (Webpack/Rollup)

```javascript
// webpack.config.js
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    chunkFilename: '[name].[contenthash].chunk.js'
  }
};
```

## Cache Performance Comparison

| Cache Layer | Latency | Capacity | First Load | Repeat Load |
|---|---|---|---|---|
| **No Cache** | 200-500ms (network) | N/A | 200-500ms | 200-500ms |
| **Memory Cache** | ~0ms | Session | N/A | 0ms |
| **Disk Cache (fresh)** | ~3-10ms | ~300 MB | N/A | 3-10ms |
| **Disk Cache (304 revalidated)** | ~50-150ms | N/A | N/A | 50-150ms |
| **Service Worker (cache first)** | ~0-5ms | Up to GBs | ~200-500ms | 0-5ms |

## Cache Size Management

### Checking Cache Usage

```javascript
// Estimate storage usage
async function checkCacheSize() {
  if ('storage' in navigator && 'estimate' in navigator.storage) {
    const estimate = await navigator.storage.estimate();
    console.log(`Used: ${(estimate.usage / 1024 / 1024).toFixed(2)} MB`);
    console.log(`Quota: ${(estimate.quota / 1024 / 1024).toFixed(2)} MB`);
    console.log(`Available: ${((estimate.quota - estimate.usage) / 1024 / 1024).toFixed(2)} MB`);
  }
}
```

### Cache Eviction (Service Worker)

```javascript
// Limit cache size to 50 entries
async function trimCache(cacheName, maxItems = 50) {
  const cache = await caches.open(cacheName);
  const keys = await cache.keys();
  
  if (keys.length > maxItems) {
    // Delete oldest entries (first in cache)
    const toDelete = keys.slice(0, keys.length - maxItems);
    await Promise.all(toDelete.map(key => cache.delete(key)));
    console.log(`Trimmed ${toDelete.length} old items from ${cacheName}`);
  }
}

// Usage in fetch handler
self.addEventListener('fetch', (event) => {
  event.respondWith(
    fetch(event.request).then(response => {
      const clone = response.clone();
      caches.open('dynamic').then(cache => {
        cache.put(event.request, clone);
        trimCache('dynamic', 50);
      });
      return response;
    })
  );
});
```

## Real-World Example: Optimal Cache Configuration

### Server (Express.js)

```javascript
const express = require('express');
const app = express();
const oneYear = 31536000;
const oneHour = 3600;

// Static assets with content hash in filename
app.use('/static', express.static('public/static', {
  maxAge: oneYear,
  immutable: true,  // Browser won't revalidate
  etag: false       // Not needed with immutable
}));

// Images with short cache + stale-while-revalidate
app.use('/images', express.static('public/images', {
  maxAge: oneHour,
  setHeaders: (res) => {
    res.set('Cache-Control', 'public, max-age=3600, stale-while-revalidate=604800');
  }
}));

// HTML - always revalidate
app.get('/', (req, res) => {
  res.set('Cache-Control', 'public, max-age=0, must-revalidate');
  res.sendFile('index.html');
});

// API - short cache
app.get('/api/posts', (req, res) => {
  res.set('Cache-Control', 'public, max-age=60');
  res.json(posts);
});
```

## Key Takeaways

- **Cache hierarchy**: Memory → Disk → Service Worker → HTTP Cache → Network
- **Memory cache** is fastest but cleared on tab close (cannot be controlled)
- **Disk cache** persists across sessions, managed by browser LRU
- **Service Worker cache** gives full programmatic control
- **Cache-Control** is the primary HTTP caching header
- **ETag/Last-Modified** enable efficient revalidation (304 responses)
- **Immutable flag** prevents unnecessary revalidation on page refresh
- **Content hashing** is the best cache-busting strategy
- **stale-while-revalidate** enables instant loading with background freshness
- **Monitoring cache size** prevents quota exceeded errors in Service Workers
