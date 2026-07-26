# Tailwind CSS

## Overview

**Tailwind CSS** is a **utility-first** CSS framework that provides low-level utility classes to build designs directly in HTML. Instead of writing custom CSS, you compose styles from pre-built classes.

## Utility-First Philosophy

```html
<!-- Traditional CSS approach -->
<div class="card">
  <h2 class="card-title">Hello</h2>
  <p class="card-text">World</p>
</div>

<!-- Tailwind approach -->
<div class="rounded-lg border border-gray-200 p-6 shadow-sm hover:shadow-md transition-shadow">
  <h2 class="text-xl font-bold text-gray-900">Hello</h2>
  <p class="mt-2 text-gray-600">World</p>
</div>
```

**Benefits:**
- No naming things (no BEM/pollution)
- No context-switching between HTML and CSS
- Small CSS bundle (purging removes unused styles)
- Consistent design constraints

## Installation

### With Vite

```bash
npm install -D tailwindcss @tailwindcss/vite
```

```js
// vite.config.js
import tailwindcss from '@tailwindcss/vite'
export default { plugins: [tailwindcss()] }
```

```css
/* main.css */
@import "tailwindcss";
```

### With Next.js

```bash
npm install -D @tailwindcss/postcss postcss
```

```js
// postcss.config.mjs
export default { plugins: { '@tailwindcss/postcss': {} } }
```

```css
/* app/globals.css */
@import "tailwindcss";
```

### CDN (for prototyping only)

```html
<link href="https://cdn.jsdelivr.net/npm/tailwindcss@4/dist/base.min.css" rel="stylesheet">
```

## Common Utility Categories

### Layout

```html
<div class="container mx-auto px-4">       <!-- Centered container -->
<div class="flex items-center gap-4">       <!-- Flexbox -->
<div class="grid grid-cols-3 gap-6">        <!-- Grid -->
<div class="hidden md:block">              <!-- Responsive visibility -->
```

### Spacing

```html
<div class="p-4">         <!-- padding: 1rem -->
<div class="px-6">        <!-- padding-inline: 1.5rem -->
<div class="m-2">         <!-- margin: 0.5rem -->
<div class="mt-8">        <!-- margin-top: 2rem -->
<div class="gap-4">       <!-- gap: 1rem -->
<div class="space-y-2">   <!-- > * + * { margin-top: 0.5rem } -->
```

### Typography

```html
<p class="text-sm text-gray-600">Small gray text</p>
<h1 class="text-3xl font-bold tracking-tight">Heading</h1>
<p class="leading-relaxed text-center">Centered body</p>
<span class="text-xs uppercase font-mono">Monospace label</span>
```

### Colors

```html
<div class="bg-blue-500 text-white">Blue background</div>
<div class="bg-gray-100 text-gray-900 border border-gray-300">
<div class="bg-emerald-50 text-emerald-700 ring-2 ring-emerald-500">
```

### Sizing

```html
<div class="w-full h-64">          <!-- width: 100%, height: 256px -->
<div class="w-1/2">               <!-- width: 50% -->
<div class="max-w-4xl">           <!-- max-width: 56rem -->
<div class="min-h-screen">        <!-- min-height: 100vh -->
<div class="size-12">             <!-- width + height: 3rem (Tailwind 4) -->
```

### Borders and Shadows

```html
<div class="border border-gray-200 rounded-lg">
<div class="border-2 border-blue-500 rounded-full">
<div class="shadow-sm hover:shadow-md transition-shadow">
<div class="ring-2 ring-indigo-500 ring-offset-2">
```

### Interactivity

```html
<button class="hover:bg-blue-700 focus:outline-none focus:ring-2 active:scale-95 cursor-pointer">
<button class="disabled:opacity-50 disabled:cursor-not-allowed">
<a class="transition-colors hover:text-blue-600">
```

## Responsive Design in Tailwind

Tailwind uses a **mobile-first** breakpoint system:

| Prefix | Min Width | CSS Equivalent |
|--------|-----------|----------------|
| (none) | 0 | Base mobile styles |
| `sm:` | 640px | `@media (min-width: 640px)` |
| `md:` | 768px | `@media (min-width: 768px)` |
| `lg:` | 1024px | `@media (min-width: 1024px)` |
| `xl:` | 1280px | `@media (min-width: 1280px)` |
| `2xl:` | 1536px | `@media (min-width: 1536px)` |

```html
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  <div class="col-span-1 md:col-span-2 lg:col-span-1">
</div>
```

## Dark Mode

```html
<div class="bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100">
<div class="dark:bg-gray-900 dark:text-white">
```

Enable via `@media` (default) or class-based:

```js
// tailwind.config.js — for v3
module.exports = { darkMode: 'class' }
```

```html
<html class="dark">
```

## Custom Config (Tailwind v4)

Tailwind v4 uses CSS-first configuration:

```css
@import "tailwindcss";

@theme {
  --color-primary: #007bff;
  --color-primary-dark: #0056b3;
  --font-display: "Inter", sans-serif;
  --breakpoint-xs: 480px;
  --spacing-18: 4.5rem;
}
```

### Tailwind v3 config (legacy)

```js
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{html,js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: { 500: "#007bff", 700: "#0056b3" },
      },
      fontFamily: { display: ["Inter", "sans-serif"] },
      spacing: { 18: "4.5rem" },
    },
  },
};
```

## Pros and Cons

| Pros | Cons |
|------|------|
| Rapid prototyping | HTML can look verbose/cluttered |
| Consistent design system | Learning curve for utility names |
| No naming overhead | Not semantic (no meaningful class names) |
| Small production CSS (purging) | Harder to read for non-Tailwind devs |
| Responsive built-in | Can encourage over-engineering |
| Excellent documentation | Limited by framework constraints |
| First-class dark mode | Version upgrades can break syntax |

## Tailwind vs Traditional CSS

| Aspect | Tailwind | Traditional CSS |
|--------|----------|----------------|
| File size | Small (purged) | Depends on code |
| Development speed | Fast (no context switch) | Slower (HTML ↔ CSS) |
| Readability | Verbose HTML | Clean HTML |
| Customizability | Configuration file | Unlimited |
| Team onboarding | Must learn utilities | Standard CSS |
| Design consistency | Built-in design system | Manual enforcement |
| Refactoring | No name collisions | Naming concerns |
| Reusable components | Component framework needed | CSS classes work directly |

## Best Practices

```html
<!-- ✅ Good: extract into components -->
<!-- React -->
<Button variant="primary" size="lg" />

<!-- ✅ Good: use @apply for repeated patterns (use sparingly) -->
<style>
  .btn-primary {
    @apply inline-flex items-center px-4 py-2 bg-blue-600 text-white
           rounded-lg hover:bg-blue-700 transition-colors;
  }
</style>

<!-- ❌ Avoid: deeply nested groups -->
<div class="[&_div_p]:text-sm [&_div_p]:text-gray-500">
  <!-- Use component extraction instead -->
</div>

<!-- ✅ Do: use arbitrary values when needed -->
<div class="w-[calc(100%-2rem)]">  <!-- Tailwind v3+ -->
<div class="top-[37px]">
```

## Tailwind v4 Highlights (2025+)

- **CSS-first config** — no JavaScript config file needed
- **`@import "tailwindcss"`** — simpler setup
- **`@theme` directive** — define design tokens in CSS
- **`size-*`** shorthand for `width` + `height`
- **`field-sizing`** utilities for form elements
- Better dark mode and container queries support
- Improved performance with incremental builds

## Resources

- [Tailwind Documentation](https://tailwindcss.com/docs)
- [Tailwind Play](https://play.tailwindcss.com) — online playground
- [Tailwind UI](https://tailwindui.com) — component library (paid)
- [DaisyUI](https://daisyui.com) — component library on top of Tailwind
- [Preline](https://preline.co) — free Tailwind components
