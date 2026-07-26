# HTML Media

## Audio

The `<audio>` element embeds sound content in a web page.

```html
<audio controls preload="metadata">
    <source src="song.mp3" type="audio/mpeg">
    <source src="song.ogg" type="audio/ogg">
    <source src="song.wav" type="audio/wav">
    Your browser does not support the audio element.
</audio>
```

### Attributes

| Attribute | Values | Description |
|-----------|--------|-------------|
| `controls` | boolean | Shows play/pause, volume, progress |
| `autoplay` | boolean | Starts playing automatically (⚠️ avoid) |
| `loop` | boolean | Loops the audio |
| `muted` | boolean | Starts muted |
| `preload` | `none`, `metadata`, `auto` | How much to load before play |
| `src` | URL | Direct audio source (use `<source>` instead) |

### Supported Formats

```
┌─────────┬──────────┬─────────────┬──────────────┐
│  Format │  Codec   │   MIME      │ Browser Support │
├─────────┼──────────┼─────────────┼──────────────┤
│ MP3     │ MP3 AAC  │ audio/mpeg  │ ✅ All        │
│ OGG     │ Vorbis   │ audio/ogg   │ ✅ Most       │
│ WAV     │ PCM      │ audio/wav   │ ✅ All        │
│ AAC     │ AAC      │ audio/aac   | ✅ Most       │
│ FLAC    │ FLAC     │ audio/flac  │ ⚠️ Limited    │
└─────────┴──────────┴─────────────┴──────────────┘
```

## Video

The `<video>` element embeds video content.

```html
<video controls width="640" height="360" poster="thumbnail.jpg">
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    <source src="video.ogv" type="video/ogg">
    <track kind="subtitles" src="subtitles_en.vtt" srclang="en" label="English">
    <track kind="captions" src="captions_en.vtt" srclang="en" label="English Captions">
    Your browser does not support the video element.
</video>
```

### Attributes

| Attribute | Values | Description |
|-----------|--------|-------------|
| `controls` | boolean | Shows playback controls |
| `autoplay` | boolean | Starts playing automatically (⚠️ use with `muted`) |
| `loop` | boolean | Loops the video |
| `muted` | boolean | Starts muted |
| `poster` | URL | Thumbnail image before play |
| `preload` | `none`, `metadata`, `auto` | How much to buffer |
| `width`/`height` | pixels | Display dimensions |
| `playsinline` | boolean | Play inline on iOS (no fullscreen) |

### Supported Formats

```
┌──────────┬──────────────┬─────────────────────┬──────────────┐
│  Format  │    Codec     │       MIME          │ Browser      │
├──────────┼──────────────┼─────────────────────┼──────────────┤
│ MP4      │ H.264 + AAC  │ video/mp4           │ ✅ All       │
│ WebM     │ VP8/VP9      │ video/webm          │ ✅ Most      │
│ OGG      │ Theora       │ video/ogg           │ ⚠️ Limited   │
│ AV1      │ AV1          │ video/mp4           │ ⚠️ Newer     │
└──────────┴──────────────┴─────────────────────┴──────────────┘
```

### Best Practice: Provide Multiple Formats

```html
<video controls>
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    <p>
        Your browser doesn't support video.
        <a href="video.mp4">Download the video</a> instead.
    </p>
</video>
```

## Picture Element

The `<picture>` element provides **art direction** and **resolution switching**.

```
                    ┌──────────────┐
                    │  <picture>   │
                    └──────┬───────┘
                           │
             ┌─────────────┼──────────────┐
             │             │              │
        ┌────▼───┐   ┌────▼───┐     ┌────▼───┐
        │<source>│   │<source>│     │ <img>  │
        │ media  │   │ media  │     │(fallback)
        │= (min- │   │= (min- │     └────────┘
        │ width: │   │ width: │
        │ 1200px)│   │ 800px) │
        └────────┘   └────────┘
```

### Art Direction (different crops for different screens)

```html
<picture>
    <source media="(min-width: 1200px)" srcset="hero-wide.jpg">
    <source media="(min-width: 800px)" srcset="hero-tablet.jpg">
    <source media="(min-width: 400px)" srcset="hero-mobile.jpg">
    <img src="hero-fallback.jpg"
         alt="Hero banner showing our product"
         loading="lazy">
</picture>
```

### Resolution Switching (same image, different sizes)

```html
<img src="photo-800.jpg"
     srcset="photo-400.jpg 400w,
             photo-800.jpg 800w,
             photo-1200.jpg 1200w"
     sizes="(max-width: 600px) 100vw,
            (max-width: 1200px) 50vw,
            800px"
     alt="Landscape photograph"
     loading="lazy">
```

| Attribute | Purpose |
|-----------|---------|
| `srcset` | List of image files with their widths (w) or densities (x) |
| `sizes` | Media condition + slot width — tells browser which image to pick |
| `loading="lazy"` | Defers loading until near viewport |

## Track Element

Used for subtitles, captions, and descriptions.

```html
<video controls>
    <source src="lecture.mp4" type="video/mp4">
    <track kind="subtitles" src="lecture-en.vtt"
           srclang="en" label="English" default>
    <track kind="subtitles" src="lecture-es.vtt"
           srclang="es" label="Spanish">
    <track kind="captions" src="lecture-captions.vtt"
           srclang="en" label="English Captions">
    <track kind="descriptions" src="lecture-desc.vtt"
           srclang="en" label="Audio Description">
    <track kind="chapters" src="lecture-chapters.vtt"
           srclang="en" label="Chapters">
</video>
```

### `kind` values

| Value | Purpose |
|-------|---------|
| `subtitles` | Translation of dialogue |
| `captions` | Same-language transcription + sound effects |
| `descriptions` | Audio description for blind users |
| `chapters` | Navigation markers |
| `metadata` | Machine-readable data |

### VTT File Format

```vtt
WEBVTT

00:00:00.000 --> 00:00:05.000
Welcome to this lecture on HTML media.

00:00:05.000 --> 00:00:10.000
Today we'll cover audio, video, and picture elements.

00:00:10.000 --> 00:00:15.000
<v Jane>Let's start with the audio element.</v>

00:00:15.000 --> 00:00:20.000 align:end
First, let's discuss supported formats.
```

## Responsive Media

### Fluid Video Container

```css
.video-wrapper {
    position: relative;
    padding-bottom: 56.25%; /* 16:9 aspect ratio */
    height: 0;
    overflow: hidden;
}

.video-wrapper video {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
```

### Fluid Images

```css
img, video, picture {
    max-width: 100%;
    height: auto;
}
```

## Accessibility Considerations

### Audio

```html
<!-- Provide transcript -->
<audio controls>
    <source src="podcast.mp3" type="audio/mpeg">
</audio>
<div class="transcript">
    <h3>Transcript</h3>
    <p>Full text of the audio...</p>
</div>
```

### Video

```html
<!-- Always provide captions -->
<video controls>
    <source src="tutorial.mp4" type="video/mp4">
    <track kind="captions" src="tutorial-captions.vtt"
           srclang="en" label="English" default>
</video>
```

### Accessibility Checklist

- ✅ Provide **captions** for video
- ✅ Provide **transcripts** for audio
- ✅ Use **descriptive alt text** for `<img>`
- ✅ Use **ARIA labels** for custom media controls
- ✅ Don't **autoplay** (or use `muted` if you must)
- ✅ Ensure **keyboard operability** of controls
- ✅ Use **sufficient color contrast** for UI controls
- ✅ Test with **screen readers**

## Real-World Example: Media Gallery

```html
<section aria-labelledby="gallery-heading">
    <h2 id="gallery-heading">Product Showcase</h2>

    <picture>
        <source media="(min-width: 800px)" srcset="product-hd.jpg">
        <source media="(min-width: 400px)" srcset="product-sd.jpg">
        <img src="product.jpg" alt="Our premium product on display"
             loading="lazy" width="800" height="600">
    </picture>

    <div class="media-row">
        <video controls width="400" poster="video-poster.jpg">
            <source src="demo.mp4" type="video/mp4">
            <source src="demo.webm" type="video/webm">
            <track kind="captions" src="demo-captions.vtt"
                   srclang="en" label="English">
            <a href="demo.mp4">Download demo video</a>
        </video>

        <audio controls>
            <source src="testimonial.mp3" type="audio/mpeg">
            <a href="testimonial.mp3">Download testimonial</a>
        </audio>
    </div>
</section>
```

## Key Takeaways

1. Always provide **multiple formats** for `<audio>` and `<video>`.
2. Use `<picture>` for **art direction** (different crops per screen).
3. Use `srcset` + `sizes` for **responsive images**.
4. Always include **captions and transcripts** for accessibility.
5. Use `loading="lazy"` for below-the-fold images.
6. Avoid `autoplay` unless video is muted and short.

---

**Next:** [07-SVG.md](07-SVG.md) — Scalable Vector Graphics in HTML.
