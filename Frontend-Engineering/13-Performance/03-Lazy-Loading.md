# Lazy Loading

## What is Lazy Loading?

Lazy loading defers the loading of non-critical resources until they are needed, typically when they enter the viewport or when the user is about to interact with them.

## Image Lazy Loading

### Native Lazy Loading

The simplest approach — built into browsers.

```html
<img src="large-photo.jpg" loading="lazy" alt="Photo" />
<iframe src="embed.html" loading="lazy"></iframe>
```

**Browser Support**: All modern browsers support `loading="lazy"`.

**Important**: Never set `loading="lazy"` on the LCP image — use `loading="eager"` or omit the attribute.

### IntersectionObserver

For more control over when and how images load.

```tsx
import { useEffect, useRef, useState } from 'react';

function LazyImage({ src, alt, placeholder, ...props }) {
  const imgRef = useRef(null);
  const [loaded, setLoaded] = useState(false);
  const [inView, setInView] = useState(false);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setInView(true);
          observer.disconnect();
        }
      },
      {
        rootMargin: '200px',  // Start loading 200px before viewport
        threshold: 0.01,
      }
    );

    if (imgRef.current) observer.observe(imgRef.current);
    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} className="image-wrapper" style={{ minHeight: 300 }}>
      {!loaded && placeholder && (
        <div className="image-placeholder">{placeholder}</div>
      )}
      {inView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setLoaded(true)}
          className={loaded ? 'image-loaded' : 'image-loading'}
          {...props}
        />
      )}
    </div>
  );
}
```

## LQIP — Low Quality Image Placeholders

Show a tiny blurry version of the image while the full version loads.

```tsx
function LQIPImage({ src, placeholderBase64, alt }) {
  const [fullLoaded, setFullLoaded] = useState(false);

  return (
    <div style={{ position: 'relative', width: '100%', aspectRatio: '16/9' }}>
      {/* Tiny blurred placeholder */}
      <img
        src={placeholderBase64}
        alt=""
        style={{
          position: 'absolute',
          width: '100%',
          height: '100%',
          objectFit: 'cover',
          filter: 'blur(20px)',
          transform: 'scale(1.1)',
          transition: 'opacity 0.5s',
          opacity: fullLoaded ? 0 : 1,
        }}
      />
      {/* Full quality image */}
      <img
        src={src}
        alt={alt}
        loading="lazy"
        style={{
          position: 'absolute',
          width: '100%',
          height: '100%',
          objectFit: 'cover',
        }}
        onLoad={() => setFullLoaded(true)}
      />
    </div>
  );
}
```

### Blur-up with Next.js

```tsx
import Image from 'next/image';
import blurData from './hero.jpg?base64';  // or use placeholder="blur"

<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  placeholder="blur"           // Auto-generated blur placeholder
  blurDataURL="data:image/webp;base64,..."  // Or custom tiny image
/>
```

## Component Lazy Loading with IntersectionObserver

```tsx
import { useEffect, useRef, useState, lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function LazySection() {
  const sectionRef = useRef(null);
  const [shouldLoad, setShouldLoad] = useState(false);

  useEffect(() => {
    const obs = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setShouldLoad(true);
          obs.disconnect();
        }
      },
      { rootMargin: '100px' }
    );

    if (sectionRef.current) obs.observe(sectionRef.current);
    return () => obs.disconnect();
  }, []);

  return (
    <section ref={sectionRef} style={{ minHeight: 200 }}>
      {shouldLoad ? (
        <Suspense fallback={<HeavySkeleton />}>
          <HeavyComponent />
        </Suspense>
      ) : (
        <HeavySkeleton />
      )}
    </section>
  );
}
```

## Component Lazy Visibility Hook

```tsx
// hooks/useVisibility.ts
export function useVisibility(options?: IntersectionObserverInit) {
  const ref = useRef(null);
  const [isVisible, setIsVisible] = useState(false);

  useEffect(() => {
    const el = ref.current;
    if (!el) return;

    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        setIsVisible(true);
        observer.unobserve(el);
      }
    }, { rootMargin: '100px', ...options });

    observer.observe(el);
    return () => observer.disconnect();
  }, []);

  return { ref, isVisible };
}

// Usage
function CommentsSection({ postId }) {
  const { ref, isVisible } = useVisibility({ rootMargin: '200px' });

  return (
    <div ref={ref}>
      {isVisible ? <CommentsList postId={postId} /> : <CommentsSkeleton />}
    </div>
  );
}
```

## React.lazy + IntersectionObserver

Combined approach for component code + data loading:

```tsx
const Comments = lazy(() => import('./Comments'));

function LazyComments() {
  const { ref, isVisible } = useVisibility();

  return (
    <div ref={ref}>
      {isVisible && (
        <Suspense fallback={<CommentsSkeleton />}>
          <Comments />
        </Suspense>
      )}
    </div>
  );
}
```

## When to Lazy Load

| Resource | Strategy | Example |
|----------|----------|---------|
| Below-fold images | `loading="lazy"` | Blog images, gallery |
| Below-fold components | IntersectionObserver | Comments, related posts |
| Heavy modals/dialogs | On interaction (React.lazy) | Settings, export dialogs |
| Third-party embeds | On scroll into view | YouTube, Twitter, maps |
| Analytics scripts | After initial paint | GA, Mixpanel |
| Chat widgets | After user interaction | Intercom, Drift |

## Anti-Patterns

```tsx
// ❌ BAD: Lazy loading the LCP element
<Image src="/hero.jpg" alt="Hero" loading="lazy" />

// ✅ GOOD: Eager load above-fold content
<Image src="/hero.jpg" alt="Hero" loading="eager" priority />

// ❌ BAD: No placeholder space (causes CLS)
<img src="photo.jpg" loading="lazy" />

// ✅ GOOD: Reserve space
<img src="photo.jpg" loading="lazy" width="800" height="600" />

// ❌ BAD: Lazy loading everything "just in case"
// Only lazy-load below-fold content
```

## Performance Impact

```
Metric          | Eager Load  | Lazy Load
────────────────┼─────────────┼─────────────
Initial HTML    | 500 KB      | 500 KB
Initial JS      | 300 KB      | 150 KB
Initial Images  | 2 MB        | 200 KB (only above-fold)
Onload Time     | 4.2s        | 1.8s
LCP             | 2.1s        | 1.5s
Total Loaded    | 5 MB        | 5 MB (eventually)
```

## Summary

Lazy loading reduces initial page weight and speeds up initial render. Use native `loading="lazy"` for images, IntersectionObserver for components and embeds, and `React.lazy()` for code splitting. Never lazy-load above-fold content.
