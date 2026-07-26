# Cheat Sheet — Frontend Mindset

## Quick Reference: Key Terms & Concepts

### A

| Term | Definition |
|------|------------|
| **A/B Testing** | Showing two variants of a UI to different users to measure which performs better |
| **Accessibility (a11y)** | Designing products usable by people with disabilities |
| **Affordance** | Visual cue that suggests how an element can be interacted with (e.g., button looks clickable) |
| **AJAX** | Asynchronous JavaScript and XML — technique for updating parts of a page without full reload |
| **API** | Application Programming Interface — how frontend communicates with backend |
| **API Gateway** | Single entry point that routes requests to appropriate microservices |
| **Async** | Non-blocking execution — operations that don't block the main thread |
| **Atomic Design** | Methodology for creating design systems (atoms → molecules → organisms → templates → pages) |

### B

| Term | Definition |
|------|------------|
| **Babel** | JavaScript transpiler — converts modern JS/TS to browser-compatible code |
| **Bandwidth** | Maximum data transfer rate of a network connection |
| **Blocking** | Resource that prevents rendering or script execution until fully loaded |
| **Bundle** | Single file (or set of files) combining all application code for the browser |
| **Bundle Analyzer** | Tool that visualizes bundle contents to find optimization opportunities |

### C

| Term | Definition |
|------|------------|
| **CDN** | Content Delivery Network — geographically distributed servers that serve static assets |
| **CLS** | Cumulative Layout Shift — Core Web Vital measuring visual stability |
| **CORS** | Cross-Origin Resource Sharing — browser security mechanism for cross-origin requests |
| **Critical Rendering Path** | Sequence of steps browser takes to render a page (HTML → CSSOM → Render Tree → Layout → Paint) |
| **CSR** | Client-Side Rendering — JS runs in browser to build UI |
| **CSSOM** | CSS Object Model — tree representation of parsed CSS |
| **CSP** | Content Security Policy — HTTP header that prevents XSS and other code injection attacks |

### D

| Term | Definition |
|------|------------|
| **DNS** | Domain Name System — translates domain names (example.com) to IP addresses |
| **DOM** | Document Object Model — tree representation of parsed HTML |
| **DPR** | Device Pixel Ratio — ratio of physical pixels to CSS pixels |
| **DX** | Developer Experience — how enjoyable/efficient it is to develop in a codebase |

### E

| Term | Definition |
|------|------------|
| **E2E** | End-to-End testing — tests that simulate real user flows |
| **ESBuild** | Extremely fast JavaScript bundler written in Go |
| **ESM** | ES Modules — native JavaScript module system (import/export) |
| **Event Loop** | Browser mechanism that handles async operations (callbacks, promises, microtasks) |

### F

| Term | Definition |
|------|------------|
| **FCP** | First Contentful Paint — time when first text/image is painted |
| **FID** | First Input Delay — time from user's first interaction to browser response |
| **Figma** | Collaborative design tool used for UI/UX design |
| **Flexbox** | CSS one-dimensional layout model for distributing space among items |
| **FOUT** | Flash of Unstyled Text — text briefly appears in fallback font before custom font loads |

### G

| Term | Definition |
|------|------------|
| **Gestalt Principles** | Psychological principles describing how humans perceive visual groups (proximity, similarity, closure) |
| **Grid** | CSS two-dimensional layout system for rows and columns |
| **GZIP** | Compression algorithm commonly used to reduce file sizes over network |

### H

| Term | Definition |
|------|------------|
| **HMR** | Hot Module Replacement — updates modules in the browser without full page reload |
| **Hydration** | Process of attaching event listeners and state to server-rendered HTML |
| **HTTP** | HyperText Transfer Protocol — protocol for web communication |

### I

| Term | Definition |
|------|------------|
| **IIFE** | Immediately Invoked Function Expression — function that runs as soon as it's defined |
| **ISR** | Incremental Static Regeneration — update static pages without rebuilding the entire site |
| **i18n** | Internationalization — designing apps for multiple languages/locales |

### J

| Term | Definition |
|------|------------|
| **JAMstack** | Architecture: JavaScript, APIs, Markup — static sites with dynamic APIs |
| **JIT** | Just-In-Time compilation — compiling code at runtime (used by V8, Tailwind JIT) |
| **JWT** | JSON Web Token — compact, self-contained token format for authentication |

### L

| Term | Definition |
|------|------------|
| **LCP** | Largest Contentful Paint — time when largest visible element renders |
| **Lighthouse** | Automated tool for auditing performance, accessibility, SEO |
| **Lazy Loading** | Deferring loading of resources until they're needed (e.g., images below the fold) |

### M

| Term | Definition |
|------|------------|
| **MVC** | Model-View-Controller — architectural pattern separating data, UI, and logic |
| **MVI** | Model-View-Intent — unidirectional data flow pattern |
| **Monolith** | Application where all components are deployed as a single unit |
| **Microservices** | Application where components are deployed independently as separate services |
| **MVP** | Minimum Viable Product — smallest version that delivers value and validates learning |

### N

| Term | Definition |
|------|------------|
| **N+1 Problem** | Performance issue where code makes N+1 queries instead of 1 (common with GraphQL/REST) |
| **NPM** | Node Package Manager — package manager for JavaScript |
| **N-Tier** | Architecture with multiple layers (client → API → service → database) |

### P

| Term | Definition |
|------|------------|
| **Paint** | Browser step that fills pixels (text, colors, images) |
| **Polyfill** | Code that implements a modern feature in older browsers |
| **Preload** | `<link rel="preload">` — hint to browser to fetch resource early |
| **Progressive Enhancement** | Strategy where basic content works everywhere, enhanced for capable browsers |
| **Promise** | Object representing eventual completion (or failure) of an async operation |

### R

| Term | Definition |
|------|------------|
| **Render Tree** | Combined DOM + CSSOM — only visible elements |
| **Reflow** | Recalculation of element positions and sizes (layout) |
| **Repaint** | Re-drawing of pixels when visual changes don't affect layout |
| **RFC** | Request For Comments — technical document proposing a change |
| **RTT** | Round Trip Time — time for a packet to travel to server and back |

### S

| Term | Definition |
|------|------------|
| **SEO** | Search Engine Optimization — making content rank well in search results |
| **SPA** | Single Page Application — app that loads once and updates dynamically |
| **SSG** | Static Site Generation — HTML pre-built at build time |
| **SSR** | Server-Side Rendering — HTML generated on server per request |
| **SWC** | Speedy Web Compiler — Rust-based JS/TS transpiler |
| **Signals** | Fine-grained reactivity primitives (used by Solid, Angular, Qwik) |
| **Semantic HTML** | Using HTML elements according to their meaning (header, nav, article, aside) |

### T

| Term | Definition |
|------|------------|
| **TBT** | Total Blocking Time — sum of all long tasks (>50ms) between FCP and TTI |
| **TCP** | Transmission Control Protocol — reliable, connection-oriented transport |
| **TLS** | Transport Layer Security — encryption protocol for secure communication |
| **TTFB** | Time To First Byte — time until server starts responding |
| **TTI** | Time to Interactive — when page is fully interactive |
| **Tree Shaking** | Removing unused code from bundles during build |
| **TypeScript** | Type-safe superset of JavaScript that compiles to plain JS |

### V

| Term | Definition |
|------|------------|
| **Virtual DOM** | Lightweight JavaScript representation of the real DOM (used by React) |
| **Vite** | Fast build tool and dev server using ESM |
| **Vue** | Progressive JavaScript framework for building UIs |

### W

| Term | Definition |
|------|------------|
| **WCAG** | Web Content Accessibility Guidelines — standards for accessible web content |
| **Webpack** | Module bundler that processes JS, CSS, images, and assets |
| **WebSocket** | Protocol for full-duplex, real-time communication |
| **Web Vitals** | Google's set of metrics for user experience quality |

### X-Z

| Term | Definition |
|------|------------|
| **XSS** | Cross-Site Scripting — injecting malicious scripts into web pages |
| **Zustand** | Small, fast state management library for React |

## Common Acronyms

| Acronym | Full Form | Context |
|---------|-----------|---------|
| **API** | Application Programming Interface | How software components communicate |
| **CDN** | Content Delivery Network | Fast static asset delivery |
| **CLI** | Command Line Interface | Terminal-based tools |
| **CMS** | Content Management System | Manage content (WordPress, Sanity) |
| **CORS** | Cross-Origin Resource Sharing | Browser security mechanism |
| **CSP** | Content Security Policy | Injection attack prevention |
| **CSR** | Client-Side Rendering | Rendering in browser |
| **CSS** | Cascading Style Sheets | Visual styling |
| **DOM** | Document Object Model | HTML structure in memory |
| **DNS** | Domain Name System | URL → IP translation |
| **DX** | Developer Experience | How devs feel about the tooling |
| **E2E** | End-to-End | Full user flow testing |
| **ESM** | ES Modules | Standard JS module system |
| **FCP** | First Contentful Paint | Initial render timing |
| **FID** | First Input Delay | Interaction responsiveness |
| **HMR** | Hot Module Replacement | Live code updates |
| **HTML** | HyperText Markup Language | Web page structure |
| **HTTP** | HyperText Transfer Protocol | Web communication protocol |
| **HTTPS** | HTTP Secure | Encrypted web communication |
| **i18n** | Internationalization | Multi-language support |
| **JS** | JavaScript | Web programming language |
| **JSON** | JavaScript Object Notation | Data interchange format |
| **JWT** | JSON Web Token | Authentication token |
| **LCP** | Largest Contentful Paint | Key render timing metric |
| **MVC** | Model-View-Controller | Architecture pattern |
| **MVP** | Minimum Viable Product | Lean product development |
| **NPM** | Node Package Manager | JS package management |
| **REST** | Representational State Transfer | API design style |
| **RTT** | Round Trip Time | Network latency measure |
| **SEO** | Search Engine Optimization | Search ranking improvement |
| **SPA** | Single Page Application | Client-rendered app |
| **SQL** | Structured Query Language | Database query language |
| **SSG** | Static Site Generation | Pre-built HTML pages |
| **SSR** | Server-Side Rendering | Server-generated HTML |
| **TBT** | Total Blocking Time | Main thread blocking sum |
| **TCP** | Transmission Control Protocol | Reliable network transport |
| **TLS** | Transport Layer Security | Encryption protocol |
| **TS** | TypeScript | Typed JavaScript superset |
| **TTFB** | Time To First Byte | Server response timing |
| **TTI** | Time to Interactive | Full interactivity timing |
| **UI** | User Interface | Visual design |
| **UX** | User Experience | Overall experience design |
| **WCAG** | Web Content Accessibility Guidelines | Accessibility standards |
| **WASM** | WebAssembly | Low-level binary format for web |

## Performance Budget Quick Reference

| Metric | Good | Needs Work | Poor |
|--------|------|-----------|------|
| **FCP** | ≤ 1.8s | 1.8s–3.0s | > 3.0s |
| **LCP** | ≤ 2.5s | 2.5s–4.0s | > 4.0s |
| **FID** | ≤ 100ms | 100ms–300ms | > 300ms |
| **TBT** | ≤ 200ms | 200ms–600ms | > 600ms |
| **CLS** | ≤ 0.1 | 0.1–0.25 | > 0.25 |
| **TTI** | ≤ 3.8s | 3.8s–7.3s | > 7.3s |
| **Bundle (JS)** | ≤ 200KB | 200KB–500KB | > 500KB |
| **Image weight** | ≤ 100KB | 100KB–500KB | > 500KB |

## CSS Layout Quick Reference

| Technique | Use When | Key Properties |
|-----------|----------|---------------|
| **Flexbox** | 1D layout (row or column) | `display: flex; gap; justify-content; align-items` |
| **Grid** | 2D layout (rows and columns) | `display: grid; grid-template-columns; gap` |
| **Position** | Overlap or fixed elements | `position: absolute/relative/fixed; top; left` |
| **Float** | Text wrapping around images (legacy) | `float: left/right; clear` |
| **Table** | Tabular data only | `display: table;` |

## Accessibility Quick Reference

```html
<!-- ARIA landmarks -->
<header role="banner">...</header>
<nav role="navigation" aria-label="Main">...</nav>
<main role="main">...</main>
<aside role="complementary">...</aside>
<footer role="contentinfo">...</footer>

<!-- Form accessibility -->
<label for="email">Email</label>
<input id="email" type="email" required aria-describedby="email-hint">
<p id="email-hint">We'll never share your email</p>

<!-- Dynamic content -->
<div role="alert" aria-live="polite">Item saved!</div>
```

## Quick Debugging Commands

```javascript
// Check what's in an object
console.dir(element);

// Measure performance
console.time('operation');
doSomething();
console.timeEnd('operation');

// Table view
console.table(users);

// Trace function calls
console.trace('Where am I?');

// Group logs
console.group('User Data');
console.log('Name:', user.name);
console.log('Email:', user.email);
console.groupEnd();

// Style console
console.log('%cHello', 'color: blue; font-size: 20px; font-weight: bold');

// Count occurrences
console.count('button clicked');
```
