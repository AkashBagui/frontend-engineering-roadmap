# Projects

## Project 1: Rendering Visualizer Using Chrome DevTools

### Goal

Build a tool that visualizes each stage of the browser rendering pipeline in real-time, then use Chrome DevTools to inspect, measure, and create performance reports.

### Part A: Visual Indicators

Create an HTML page that shows which rendering phases are triggered:

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      font-family: system-ui, sans-serif;
      background: #1a1a2e;
      color: #eee;
      margin: 0;
      padding: 20px;
    }
    .controls { margin-bottom: 20px; }
    button {
      padding: 10px 20px;
      margin: 5px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
    }
    .btn-reflow { background: #e74c3c; color: white; }
    .btn-repaint { background: #f39c12; color: white; }
    .btn-composite { background: #27ae60; color: white; }
    .btn-gc { background: #8e44ad; color: white; }
    .monitor {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
      margin: 20px 0;
    }
    .metric {
      background: #16213e;
      padding: 15px;
      border-radius: 8px;
      text-align: center;
    }
    .metric-value {
      font-size: 2em;
      font-weight: bold;
      margin: 5px 0;
    }
    .metric-label { font-size: 0.9em; color: #888; }
    .layer-indicator {
      width: 100%;
      height: 100px;
      background: repeating-linear-gradient(
        45deg,
        #2d2d5e,
        #2d2d5e 10px,
        #1a1a3e 10px,
        #1a1a3e 20px
      );
      border: 2px solid #4a4a8a;
      border-radius: 8px;
      position: relative;
    }
    .layer {
      position: absolute;
      background: rgba(100, 200, 255, 0.2);
      border: 1px solid rgba(100, 200, 255, 0.5);
      border-radius: 4px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: transform 0.1s, background 0.1s;
    }
    .test-box {
      width: 100px;
      height: 100px;
      background: #e74c3c;
      margin: 20px;
      display: inline-block;
      transition: all 0.3s;
    }
    .badge {
      display: inline-block;
      padding: 2px 8px;
      border-radius: 10px;
      font-size: 12px;
      margin: 2px;
    }
    .badge-layout { background: #e74c3c; }
    .badge-paint { background: #f39c12; }
    .badge-composite { background: #27ae60; }
  </style>
</head>
<body>
  <h1>Rendering Pipeline Visualizer</h1>
  
  <div class="controls">
    <button class="btn-reflow" onclick="triggerReflow()">Trigger Reflow (Layout)</button>
    <button class="btn-repaint" onclick="triggerRepaint()">Trigger Repaint</button>
    <button class="btn-composite" onclick="triggerComposite()">Composite Only</button>
    <button class="btn-gc" onclick="triggerGC()">Force GC</button>
  </div>
  
  <div class="monitor">
    <div class="metric">
      <div class="metric-value" id="layoutCount">0</div>
      <div class="metric-label">Layouts</div>
    </div>
    <div class="metric">
      <div class="metric-value" id="paintCount">0</div>
      <div class="metric-label">Paints</div>
    </div>
    <div class="metric">
      <div class="metric-value" id="compositeCount">0</div>
      <div class="metric-label">Composites</div>
    </div>
    <div class="metric">
      <div class="metric-value" id="fpsCounter">60</div>
      <div class="metric-label">FPS</div>
    </div>
  </div>
  
  <div class="layer-indicator" id="layerIndicator">
    <div class="layer" id="testBox">Test Box</div>
  </div>
  
  <div id="eventLog" style="
    background: #16213e;
    padding: 10px;
    border-radius: 8px;
    height: 200px;
    overflow-y: auto;
    font-family: monospace;
    font-size: 12px;
    margin-top: 20px;
  "></div>

  <script>
    let layoutCount = 0, paintCount = 0, compositeCount = 0;
    let frameCount = 0, lastFpsTime = performance.now();
    let testBox = document.getElementById('testBox');
    let log = document.getElementById('eventLog');
    
    function logEvent(message, type) {
      const entry = document.createElement('div');
      entry.innerHTML = `<span class="badge badge-${type}">${type}</span> ${message}`;
      log.appendChild(entry);
      log.scrollTop = log.scrollHeight;
    }
    
    function triggerReflow() {
      layoutCount++;
      document.getElementById('layoutCount').textContent = layoutCount;
      logEvent(`Changed width to ${100 + Math.random() * 200}px`, 'layout');
      // This triggers layout
      testBox.style.width = `${100 + Math.random() * 200}px`;
      testBox.style.height = `${100 + Math.random() * 200}px`;
      // Read forces sync layout
      const h = testBox.offsetHeight;
    }
    
    function triggerRepaint() {
      paintCount++;
      document.getElementById('paintCount').textContent = paintCount;
      logEvent('Changed background color', 'paint');
      // Only triggers repaint (no layout)
      testBox.style.backgroundColor = 
        `hsl(${Math.random() * 360}, 70%, 50%)`;
    }
    
    function triggerComposite() {
      compositeCount++;
      document.getElementById('compositeCount').textContent = compositeCount;
      logEvent('Applied transform', 'composite');
      // Composite only — no layout, no paint
      testBox.style.transform = 
        `translate(${Math.random() * 200}px, ${Math.random() * 200}px)`;
    }
    
    function triggerGC() {
      logEvent('Forcing garbage collection...', 'composite');
      // Create and discard objects
      for (let i = 0; i < 100000; i++) {
        const temp = { data: new Array(100).fill('x') };
      }
      if (window.gc) {
        window.gc();
        logEvent('GC completed', 'composite');
      } else {
        logEvent('Open Chrome with --enable-precise-memory-info flag', 'composite');
      }
    }
    
    // FPS counter
    function updateFPS() {
      frameCount++;
      const now = performance.now();
      if (now - lastFpsTime >= 1000) {
        document.getElementById('fpsCounter').textContent = frameCount;
        frameCount = 0;
        lastFpsTime = now;
      }
      requestAnimationFrame(updateFPS);
    }
    requestAnimationFrame(updateFPS);
  </script>
</body>
</html>
```

### Part B: DevTools Inspection Tasks

After creating the page, perform these exercises:

#### Exercise 1: Identify Rendering Stages

1. Open Chrome DevTools → **Performance** → Record
2. Click "Trigger Reflow (Layout)" 3 times
3. Click "Trigger Repaint" 3 times
4. Click "Composite Only" 3 times
5. Stop recording

**Expected Performance Panel View:**

```
Performance Recording:
┌──────────────────────────────────────────────────┐
│ Main ────────────────────────────────────────    │
│  Layout  Layout  Layout  Paint  Paint  Paint     │
│  ──────  ──────  ──────  ─────  ─────  ─────    │
│  [██]    [██]    [██]    [█]    [█]    [█]       │
│                                                    │
│  Composite  Composite  Composite                   │
│  ─────────  ─────────  ─────────                  │
│  [░░]       [░░]       [░░]                        │
│                                                    │
│ Note: Composite operations are much shorter       │
│ than Layout events!                                │
└──────────────────────────────────────────────────┘
```

**Questions to answer:**
- How does the duration of Layout compare to Paint?
- How does Composite compare to Layout and Paint?
- What happens on the "Forced GC" button?

#### Exercise 2: Paint Flashing

1. DevTools → Rendering → Check **"Paint flashing"**
2. Click "Trigger Repaint"
3. Watch green rectangles flash over the button and test box

**Record observations:**
- Which area flashes green for each operation?
- How does the flash area differ between layout, repaint, and composite?

#### Exercise 3: Layout Shift Analysis

1. DevTools → Rendering → Check **"Layout Shift Regions"**
2. Click "Trigger Reflow" rapidly
3. Observe blue highlighted regions

**Record layout shifts:**
- What is the CLS score for repeated layout triggers?
- How could you avoid these layout shifts in production?

#### Exercise 4: Layer Borders

1. DevTools → Rendering → Check **"Layer borders"**
2. Observe the test box

**Observe:**
- Does the test box have a compositing layer (blue border)?
- Apply `will-change: transform` to the test box — does it get a layer?

### Part C: Performance Report Template

Create a report template for documenting findings:

```markdown
# Rendering Pipeline Analysis Report

## Environment
- Browser: Chrome 128
- OS: Windows 11
- Screen: 1920 × 1080 @ 60Hz

## Test Results

| Operation | Layout? | Paint? | Composite? | Avg Duration |
|---|---|---|---|---|
| Change `width` | Yes | Yes | Yes | 2.3ms |
| Change `color` | No | Yes | Yes | 0.8ms |
| Change `transform` | No | No | Yes | 0.1ms |
| Add DOM element | Yes | Yes | Yes | 3.1ms |

## Flame Graph Observations
- Layout events took X% of total time
- Paint events took Y% of total time  
- Composite events took Z% of total time

## Recommendations
1. Replace `position: absolute + left` with `transform: translateX()` for animations
2. Use `will-change: transform` sparingly
3. Batch DOM reads and writes
```

## Project 2: Service Worker Caching Project

### Goal

Build a progressive web app that implements multiple caching strategies and demonstrates offline capabilities.

### Project Setup

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>PWA Caching Demo</title>
  <link rel="stylesheet" href="styles.css">
  <link rel="manifest" href="manifest.json">
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
  <h1>PWA Caching Strategies</h1>
  
  <div class="controls">
    <button onclick="loadPage('/')">Load Homepage</button>
    <button onclick="loadPage('/api/posts')">Load API Data</button>
    <button onclick="loadImage()">Load Image</button>
    <button onclick="clearAllCaches()">Clear All Caches</button>
    <button onclick="showCacheContents()">Show Cache Contents</button>
  </div>
  
  <div id="output"></div>
  <div id="cacheInfo"></div>
  
  <script src="app.js"></script>
</body>
</html>
```

### Part A: Service Worker with Multiple Strategies

```javascript
// sw.js
const CACHE_NAMES = {
  static: 'static-v1',
  images: 'images-v1',
  api: 'api-v1',
  dynamic: 'dynamic-v1'
};

const STATIC_ASSETS = [
  '/',
  '/styles.css',
  '/app.js',
  '/offline.html',
  '/images/placeholder.svg'
];

// INSTALL: Precache app shell
self.addEventListener('install', (event) => {
  self.skipWaiting();
  event.waitUntil(
    caches.open(CACHE_NAMES.static).then(cache => {
      console.log('[SW] Precaching static assets');
      return cache.addAll(STATIC_ASSETS);
    })
  );
});

// ACTIVATE: Clean old caches
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then(keys => {
      return Promise.all(
        keys.filter(k => !Object.values(CACHE_NAMES).includes(k))
          .map(k => {
            console.log('[SW] Deleting old cache:', k);
            return caches.delete(k);
          })
      );
    }).then(() => clients.claim())
  );
});

// FETCH: Strategy selection based on request type
self.addEventListener('fetch', (event) => {
  const { request } = event;
  const url = new URL(request.url);
  
  // Strategy 1: Cache First (static assets)
  if (STATIC_ASSETS.includes(url.pathname) || 
      request.destination === 'style' ||
      request.destination === 'script') {
    event.respondWith(cacheFirst(request));
    return;
  }
  
  // Strategy 2: Network First (API calls)
  if (url.pathname.startsWith('/api/')) {
    event.respondWith(networkFirst(request));
    return;
  }
  
  // Strategy 3: Stale While Revalidate (images)
  if (request.destination === 'image') {
    event.respondWith(staleWhileRevalidate(request));
    return;
  }
  
  // Strategy 4: Network Only (analytics, auth)
  if (url.hostname === 'analytics.example.com') {
    event.respondWith(fetch(request));
    return;
  }
  
  // Default: Cache First with network fallback
  event.respondWith(cacheFirst(request));
});

// Implementation: Cache First
async function cacheFirst(request) {
  const cached = await caches.match(request);
  if (cached) {
    console.log('[SW] Cache hit:', request.url);
    return cached;
  }
  
  try {
    const response = await fetch(request);
    const cache = await caches.open(CACHE_NAMES.dynamic);
    cache.put(request, response.clone());
    return response;
  } catch (error) {
    console.log('[SW] Network failed, returning offline page:', request.url);
    return caches.match('/offline.html');
  }
}

// Implementation: Network First
async function networkFirst(request) {
  try {
    const response = await fetch(request);
    const cache = await caches.open(CACHE_NAMES.api);
    cache.put(request, response.clone());
    console.log('[SW] Network response cached:', request.url);
    return response;
  } catch (error) {
    const cached = await caches.match(request);
    if (cached) {
      console.log('[SW] Network failed, serving cached response:', request.url);
      return cached;
    }
    return new Response(JSON.stringify({ error: 'Offline' }), {
      status: 503,
      headers: { 'Content-Type': 'application/json' }
    });
  }
}

// Implementation: Stale While Revalidate
async function staleWhileRevalidate(request) {
  const cache = await caches.open(CACHE_NAMES.images);
  const cached = await cache.match(request);
  
  const fetchPromise = fetch(request).then(response => {
    cache.put(request, response.clone());
    return response;
  }).catch(() => cached);
  
  return cached || fetchPromise;
}

// Listen for messages from the client
self.addEventListener('message', (event) => {
  if (event.data === 'CLEAR_CACHES') {
    Promise.all(
      Object.values(CACHE_NAMES).map(name => caches.delete(name))
    ).then(() => {
      event.ports[0].postMessage('Caches cleared');
    });
  }
  
  if (event.data === 'GET_CACHE_INFO') {
    caches.keys().then(keys => {
      Promise.all(keys.map(async name => {
        const cache = await caches.open(name);
        const requests = await cache.keys();
        return { name, size: requests.length, urls: requests.map(r => r.url) };
      })).then(info => {
        event.ports[0].postMessage(info);
      });
    });
  }
});
```

### Part B: Client-Side App

```javascript
// app.js
const output = document.getElementById('output');
const cacheInfo = document.getElementById('cacheInfo');

// Register Service Worker
async function registerSW() {
  if (!('serviceWorker' in navigator)) {
    output.innerHTML = '<p style="color:red">Service Workers not supported</p>';
    return;
  }
  
  try {
    const registration = await navigator.serviceWorker.register('/sw.js');
    output.innerHTML = '<p style="color:green">✅ Service Worker registered</p>';
    console.log('SW registered:', registration.scope);
    
    // Check for updates
    registration.addEventListener('updatefound', () => {
      output.innerHTML += '<p>🔄 New SW version found, updating...</p>';
    });
  } catch (error) {
    output.innerHTML = `<p style="color:red">❌ SW registration failed: ${error}</p>`;
  }
}

// Load content via fetch (intercepted by SW)
async function loadPage(url) {
  output.innerHTML = '<p>Loading...</p>';
  
  try {
    const start = performance.now();
    const response = await fetch(url);
    const text = await response.text();
    const duration = performance.now() - start;
    
    output.innerHTML = `
      <p>✅ Loaded: ${url}</p>
      <p>From: ${response.headers.get('X-Cache') || 'unknown'}</p>
      <p>Duration: ${duration.toFixed(2)}ms</p>
      <p>Status: ${response.status}</p>
    `;
  } catch (error) {
    output.innerHTML = `<p style="color:red">❌ Failed: ${error}</p>`;
  }
}

// Load image
async function loadImage() {
  const imgUrl = '/images/photo.jpg';
  output.innerHTML = '<p>Loading image...</p>';
  
  const img = new Image();
  const start = performance.now();
  
  img.onload = () => {
    const duration = performance.now() - start;
    output.innerHTML = `
      <p>✅ Image loaded</p>
      <p>Duration: ${duration.toFixed(2)}ms</p>
      <img src="${imgUrl}" style="max-width: 400px">
    `;
  };
  
  img.onerror = () => {
    output.innerHTML = '<p style="color:red">❌ Image failed to load</p>';
  };
  
  img.src = imgUrl;
}

// Clear all caches
async function clearAllCaches() {
  if (!navigator.serviceWorker.controller) {
    output.innerHTML = '<p>No active SW to communicate with</p>';
    return;
  }
  
  const channel = new MessageChannel();
  channel.port1.onmessage = (event) => {
    output.innerHTML = `<p>🗑️ ${event.data}</p>`;
  };
  
  navigator.serviceWorker.controller.postMessage('CLEAR_CACHES', [channel.port2]);
}

// Show cache contents
async function showCacheContents() {
  if (!navigator.serviceWorker.controller) {
    output.innerHTML = '<p>No active SW</p>';
    return;
  }
  
  const channel = new MessageChannel();
  channel.port1.onmessage = (event) => {
    const caches = event.data;
    cacheInfo.innerHTML = '<h3>Cache Storage</h3>';
    
    for (const cache of caches) {
      cacheInfo.innerHTML += `
        <div class="cache-entry">
          <strong>${cache.name}</strong> (${cache.size} items)
          <ul>${cache.urls.slice(0, 10).map(u => 
            `<li>${u}</li>`
          ).join('')}</ul>
          ${cache.urls.length > 10 ? `<p>...and ${cache.urls.length - 10} more</p>` : ''}
        </div>
      `;
    }
  };
  
  navigator.serviceWorker.controller.postMessage('GET_CACHE_INFO', [channel.port2]);
}

// Check online/offline status
window.addEventListener('online', () => {
  output.innerHTML += '<p style="color:green">🌐 Back online!</p>';
});

window.addEventListener('offline', () => {
  output.innerHTML += '<p style="color:red">📡 Offline mode</p>';
});

// Initialize
registerSW();
```

### Part C: Offline Test Checklist

1. **Open the app** and verify SW registration
2. **Go offline** (DevTools → Network → Offline)
3. **Reload the page** — does it load from cache?
4. **Click "Load API Data"** — shows cached or error response?
5. **Click "Load Image"** — shows cached image?
6. **Go back online** — verify network-first strategy works again
7. **Check cache contents** with "Show Cache Contents" button

### Part D: Performance Comparison

| Strategy | Online Load Time | First Offline Load | Subsequent Offline Load |
|---|---|---|---|
| Cache First | 5ms (instant) | 5ms (instant) | 5ms (instant) |
| Network First | 200-500ms (network) | N/A (shows cache) | N/A (shows cache) |
| Stale While Revalidate | 5ms + bg update | 5ms (stale cached) | 5ms (stale cached) |
| Network Only | 200-500ms | Error | Error |

## Deliverables

1. **Rendering Visualizer** — HTML file with DevTools inspection report
2. **Service Worker PWA** — Full offline-capable app with multiple caching strategies
3. **Performance Report** — Document findings from DevTools analysis
4. **Cache Strategy Decision Tree** — Flowchart for choosing the right caching strategy
