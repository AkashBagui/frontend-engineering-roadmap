# Image Gallery

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build an image gallery application that displays images in a responsive grid layout, supports full-screen lightbox viewing, lazy loading, search/filter by tags, and infinite scroll pagination.

---

## Requirements

### Functional
- [ ] Display images in a responsive grid (masonry or uniform)
- [ ] Click image → open lightbox (full-screen overlay)
- [ ] Lightbox navigation (previous / next arrows, keyboard arrows)
- [ ] Close lightbox (Escape key, close button, click outside)
- [ ] Lazy load images as they scroll into view
- [ ] Infinite scroll (load more images when reaching bottom)
- [ ] Search images by title or tags
- [ ] Loading spinner / skeleton while images load
- [ ] Handle image load errors (broken image fallback)

### Non-Functional
- [ ] Smooth loading experience (no layout shift)
- [ ] Responsive (columns adjust to viewport width)
- [ ] Performant with 500+ images in grid
- [ ] Accessible lightbox with focus trapping

---

## Component Architecture

```
App
├── Header
│   ├── Title
│   └── SearchBar
├── GalleryGrid
│   └── ImageCard (×N) [virtualized window]
│       ├── Image (Lazy loaded)
│       ├── Title
│       ├── Tags
│       └── Overlay (hover effect)
├── Lightbox (conditional overlay)
│   ├── CloseButton
│   ├── PrevButton
│   ├── NextButton
│   ├── ImageContainer
│   │   └── Image (full size)
│   ├── ImageInfo (title, tags, index)
│   └── Backdrop (click to close)
└── StatusFooter
    ├── LoadMoreTrigger (IntersectionObserver target)
    └── LoadingSpinner
```

---

## Virtualization

Use **react-window** with `FixedSizeGrid` for uniform grid or custom for masonry. For infinite scroll with virtualization:

```js
// Approach: Virtualized list with dynamic row heights
// Each row contains N images (columns).
// As user scrolls, more items are loaded and appended.

import { FixedSizeGrid as Grid } from 'react-window';
import AutoSizer from 'react-virtualized-auto-sizer';
```

For non-virtualized (simpler): use CSS columns with `column-count` for masonry, and just infinite scroll.

---

## Image Optimization Techniques

| Technique | Implementation |
|-----------|----------------|
| **Lazy loading** | `loading="lazy"` attribute on `<img>` or Intersection Observer |
| **Blur placeholder** | Load tiny thumbnail (20px), blur it, swap on full load |
| **Responsive images** | `srcSet` with multiple resolutions |
| **CDN transforms** | Append `?w=400&q=75` to image URLs (Unsplash, Cloudinary) |
| **Preload adjacent** | When lightbox is open, preload next/prev images |

---

## State Management

```js
const [images, setImages] = useState([]);
const [page, setPage] = useState(1);
const [hasMore, setHasMore] = useState(true);
const [isLoading, setIsLoading] = useState(false);
const [searchTerm, setSearchTerm] = useState('');
const [lightboxIndex, setLightboxIndex] = useState(null);

// Each image object
{
  id: 'img_1',
  url: 'https://picsum.photos/id/1/800/600',
  thumbUrl: 'https://picsum.photos/id/1/400/300',
  title: 'Mountain view',
  tags: ['nature', 'landscape'],
  width: 800,
  height: 600
}
```

---

## Implementation Steps

1. Set up gallery layout with CSS Grid or CSS Columns for masonry
2. Fetch initial set of images (from API or mock data)
3. Build ImageCard component with aspect-ratio container
4. Implement lazy loading with IntersectionObserver or `loading="lazy"`
5. Implement search filter (filter images state by title/tags)
6. Implement infinite scroll: IntersectionObserver on sentinel element → fetch next page → append to images
7. Build Lightbox component (overlay, centered image, prev/next)
8. Add keyboard navigation in lightbox (ArrowLeft, ArrowRight, Escape)
9. Add focus trapping inside lightbox for accessibility
10. Handle loading states (skeleton while fetching)
11. Handle error states (broken image → show fallback placeholder)
12. Add responsive column count (2 cols mobile, 3 tablet, 4 desktop)

---

## Code Snippets

### Lazy Load with IntersectionObserver

```js
function LazyImage({ src, alt, placeholder }) {
  const imgRef = useRef(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const [isInView, setIsInView] = useState(false);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { rootMargin: '200px' }
    );
    if (imgRef.current) observer.observe(imgRef.current);
    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} className="image-wrapper">
      {isInView ? (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          className={isLoaded ? 'loaded' : 'loading'}
        />
      ) : (
        <div className="placeholder">{placeholder}</div>
      )}
    </div>
  );
}
```

### Infinite Scroll Hook

```js
function useInfiniteScroll(loadMore, hasMore, isLoading) {
  const sentinelRef = useRef(null);

  useEffect(() => {
    if (!sentinelRef.current) return;
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting && hasMore && !isLoading) {
          loadMore();
        }
      },
      { rootMargin: '300px' }
    );
    observer.observe(sentinelRef.current);
    return () => observer.disconnect();
  }, [loadMore, hasMore, isLoading]);

  return sentinelRef;
}
```

### Masonry Layout with CSS

```css
.masonry-grid {
  column-count: 4;
  column-gap: 16px;
}

.masonry-item {
  break-inside: avoid;
  margin-bottom: 16px;
}

@media (max-width: 768px) {
  .masonry-grid { column-count: 2; }
}
```

### Lightbox Focus Trap

```js
useEffect(() => {
  if (lightboxIndex === null) return;
  const lightbox = lightboxRef.current;
  const focusable = lightbox.querySelectorAll('button, [href], input, [tabindex]:not([tabindex="-1"])');
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  function handleTab(e) {
    if (e.key === 'Tab') {
      if (e.shiftKey && document.activeElement === first) {
        e.preventDefault();
        last.focus();
      } else if (!e.shiftKey && document.activeElement === last) {
        e.preventDefault();
        first.focus();
      }
    }
  }

  first?.focus();
  lightbox.addEventListener('keydown', handleTab);
  return () => lightbox.removeEventListener('keydown', handleTab);
}, [lightboxIndex]);
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Image load error | Show placeholder with icon ("Image not found") |
| Very slow network | Show skeleton; timeout fallback after 30s |
| No search results | Show "No images found" message with illustration |
| Lightbox on last image | Disable "Next" button; wrap-around (optional) |
| Rapid scroll (infinite load thrashing) | Debounce load; lock isLoading until fetch completes |
| Images with unknown dimensions | Use CSS aspect-ratio placeholder; wait for naturalWidth |
| Screen reader accessibility | Provide alt text; aria-label on lightbox buttons |

---

## Bonus Features

- [ ] **Image upload** with drag-and-drop zone
- [ ] **Image delete** (with confirmation)
- [ ] **Album/collection** grouping
- [ ] **Sort** by date, name, size
- [ ] **Zoom / pan** in lightbox
- [ ] **Slideshow mode** (auto-advance every 3s)
- [ ] **Download image** button in lightbox

---

## Common Interview Questions

1. **How do you implement a masonry layout?** — CSS `column-count` (simplest), or JS-based positioning that tracks column heights and places each item in the shortest column.

2. **How do you lazy load images efficiently?** — Use IntersectionObserver with a generous `rootMargin` (200-300px) to start loading before images enter the viewport. After loading, disconnect the observer.

3. **How do you prevent layout shift?** — Reserve space with a container that has the correct `aspect-ratio` or a fixed width/height placeholder before the image loads.

4. **How would you handle millions of images?** — Server-side pagination, virtualized grid (react-window), cached thumbnails, CDN for image delivery, blurhash placeholders.

---

## Resources

- [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [CSS Columns (masonry)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Columns)
- [react-window](https://react-window.vercel.app/)
