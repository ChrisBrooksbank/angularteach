# Performance

[&larr; SSR & Hydration](15-ssr-and-hydration.md) | [Next: Security &rarr;](17-security.md)

---

Angular provides many built-in tools for building fast applications. This guide covers the most impactful optimizations, from quick wins to advanced techniques.

## Table of Contents

- [Performance Checklist](#performance-checklist)
- [Lazy Loading](#lazy-loading)
- [Change Detection Optimization](#change-detection-optimization)
- [Image Optimization](#image-optimization)
- [Bundle Size](#bundle-size)
- [Runtime Performance](#runtime-performance)
- [Key Takeaways](#key-takeaways)

---

## Performance Checklist

Quick reference for the highest-impact optimizations:

| Optimization | Impact | Effort | Guide Section |
|-------------|--------|--------|---------------|
| Lazy load routes | High | Low | [Lazy Loading](#lazy-loading) |
| Use `@defer` for heavy components | High | Low | [Lazy Loading](#defer-blocks) |
| OnPush change detection | High | Low | [Change Detection](#change-detection-optimization) |
| Use signals | High | Medium | [Change Detection](#use-signals) |
| `NgOptimizedImage` | Medium | Low | [Image Optimization](#image-optimization) |
| `trackBy` in `@for` | Medium | Low | [Runtime Performance](#track-expressions) |
| Preload lazy routes | Medium | Low | [Lazy Loading](#preloading-strategies) |
| SSR / SSG | High | Medium | [SSR & Hydration](15-ssr-and-hydration.md) |
| Zoneless | Medium | Medium | [Change Detection](13-change-detection.md#zoneless-angular) |

---

## Lazy Loading

### Route-Level Lazy Loading

Only load the JavaScript for a route when the user navigates to it:

```typescript
export const routes: Routes = [
  { path: '', component: HomeComponent },  // eagerly loaded
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.routes').then(m => m.adminRoutes)
  },
  {
    path: 'profile',
    loadComponent: () => import('./profile.component').then(m => m.ProfileComponent)
  }
];
```

### @defer Blocks

Lazy load individual components within a page:

```html
<!-- Only loads the chart component when it scrolls into view -->
@defer (on viewport) {
  <app-analytics-chart [data]="chartData" />
} @placeholder {
  <div style="height: 400px" class="chart-placeholder">
    Chart loads when visible
  </div>
}

<!-- Load comments when user clicks "Show Comments" -->
@defer (on interaction) {
  <app-comments [postId]="postId" />
} @placeholder {
  <button>Show Comments</button>
}
```

See [Control Flow — @defer](04-control-flow.md#defer--lazy-loading-blocks) for full details.

### Preloading Strategies

Load lazy chunks in the background after the initial load:

```typescript
import { provideRouter, withPreloading, PreloadAllModules } from '@angular/router';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes, withPreloading(PreloadAllModules))
  ]
};
```

| Strategy | Behavior |
|----------|----------|
| `NoPreloading` (default) | Only load when navigated to |
| `PreloadAllModules` | Preload all lazy routes after initial load |
| Custom | Preload based on your own logic |

---

## Change Detection Optimization

### Use OnPush

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
```

OnPush skips components whose inputs haven't changed. See [Change Detection](13-change-detection.md) for details.

### Use Signals

Signals enable fine-grained updates — Angular only re-evaluates the specific template bindings that depend on changed signals:

```typescript
// ✅ Only updates the <span> when count changes
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <h1>{{ title() }}</h1>
    <span>{{ count() }}</span>
  `
})
export class MyComponent {
  title = signal('Dashboard');
  count = signal(0);
}
```

### Avoid Expensive Template Expressions

```typescript
// ❌ BAD: Method called on every change detection cycle
template: `<p>{{ getFilteredItems().length }} items</p>`

// ✅ GOOD: Computed signal, recalculated only when dependencies change
filteredItems = computed(() => 
  this.items().filter(i => i.active)
);
template: `<p>{{ filteredItems().length }} items</p>`
```

---

## Image Optimization

### NgOptimizedImage

Angular's built-in image directive optimizes loading:

```typescript
import { NgOptimizedImage } from '@angular/common';

@Component({
  imports: [NgOptimizedImage],
  template: `
    <!-- Optimized image with required width/height -->
    <img ngSrc="/assets/hero.jpg" width="1200" height="600" priority />
    
    <!-- Lazy-loaded image (default) -->
    <img ngSrc="/assets/product.jpg" width="400" height="300" />
    
    <!-- Responsive with srcset -->
    <img ngSrc="/assets/photo.jpg" width="800" height="600"
         sizes="(max-width: 768px) 100vw, 50vw" />
  `
})
```

| Feature | What It Does |
|---------|-------------|
| `priority` | Eager-loads above-the-fold images (adds `fetchpriority="high"`) |
| `loading="lazy"` | Default for non-priority images |
| `width` / `height` | Prevents layout shift (CLS) |
| Automatic `srcset` | Generates responsive sizes when using a CDN loader |

### Image Loader

For CDN-hosted images:

```typescript
import { provideImageKitLoader } from '@angular/common';
// or: provideCloudflareLoader, provideCloudinaryLoader, provideImgixLoader

export const appConfig: ApplicationConfig = {
  providers: [
    provideImageKitLoader('https://ik.imagekit.io/your-id')
  ]
};
```

---

## Bundle Size

### Analyze Your Bundle

```bash
ng build --stats-json
npx webpack-bundle-analyzer dist/my-app/stats.json
```

### Tips for Smaller Bundles

| Technique | Description |
|-----------|-------------|
| Lazy load routes | Only load what's needed |
| Tree-shake unused code | Angular's build system does this automatically |
| Avoid barrel exports | `import { X } from './utils'` can pull in everything |
| Use lightweight alternatives | e.g., `date-fns` instead of `moment` |
| Check for duplicates | Multiple versions of the same library |

### Production Build

```bash
ng build
# Automatically enables:
# - AOT compilation
# - Tree shaking
# - Minification
# - Dead code elimination
```

---

## Runtime Performance

### Track Expressions

Always use meaningful `track` in `@for` loops:

```html
<!-- ✅ Track by unique ID — Angular reuses DOM elements -->
@for (user of users(); track user.id) {
  <app-user-card [user]="user" />
}

<!-- ❌ Track by index — poor performance on reordering -->
@for (user of users(); track $index) {
  <app-user-card [user]="user" />
}
```

### Virtual Scrolling

For long lists, render only visible items:

```typescript
import { CdkVirtualScrollViewport, CdkFixedSizeVirtualScroll, CdkVirtualForOf } from '@angular/cdk/scrolling';

@Component({
  imports: [CdkVirtualScrollViewport, CdkFixedSizeVirtualScroll, CdkVirtualForOf],
  template: `
    <cdk-virtual-scroll-viewport itemSize="48" class="list">
      <div *cdkVirtualFor="let item of items" class="item">
        {{ item.name }}
      </div>
    </cdk-virtual-scroll-viewport>
  `,
  styles: [`.list { height: 400px; }`]
})
export class VirtualListComponent {
  items = Array.from({ length: 10000 }, (_, i) => ({ name: `Item ${i}` }));
}
```

### Pure Pipes Over Methods

```typescript
// ❌ Method runs every change detection cycle
template: `{{ formatCurrency(price) }}`

// ✅ Pure pipe only runs when input changes
template: `{{ price | currency }}`
```

See [Directives & Pipes](06-directives-and-pipes.md#pure-vs-impure-pipes) for pipe purity.

---

## Key Takeaways

- **Lazy load routes** and use **`@defer`** for heavy components
- **OnPush + signals** = optimal change detection
- Use **`computed()`** instead of methods in templates
- **`NgOptimizedImage`** for image loading performance
- **Virtual scrolling** for long lists
- **Analyze your bundle** to find and fix size issues
- Most optimizations are **low effort, high impact** — start with the checklist

---

## Free Resources

> **Official:** [Performance Best Practices](https://angular.dev/best-practices/performance) — the Angular team's own optimization guide
>
> **YouTube:** [How to Make Your Angular App Fast](https://www.youtube.com/@JoshuaMorony) — Joshua Morony on practical performance patterns including `@defer`, signals, and avoiding unnecessary re-renders
>
> **YouTube:** [Angular Performance — Best Practices](https://www.youtube.com/@DecodedFrontend) — Decoded Frontend covers OnPush, `track`, lazy loading, and bundle analysis
>
> **Tool:** [Angular DevTools](https://chrome.google.com/webstore/detail/angular-devtools/) — the official Chrome extension for profiling change detection and inspecting the component tree

---

**Related:**
- [Change Detection](13-change-detection.md) — deep dive into OnPush and zoneless
- [Control Flow — @defer](04-control-flow.md#defer--lazy-loading-blocks) — lazy template blocks
- [Routing — Lazy Loading](08-routing.md#lazy-loading) — route-level code splitting
- [SSR & Hydration](15-ssr-and-hydration.md) — server-side rendering for faster loads

---

[&larr; SSR & Hydration](15-ssr-and-hydration.md) | [Next: Security &rarr;](17-security.md)
