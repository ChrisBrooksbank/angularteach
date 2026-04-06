# Deployment

[&larr; Advanced Patterns](18-advanced-patterns.md)

---

This guide covers building Angular apps for production, choosing a hosting strategy, and configuring CI/CD.

## Table of Contents

- [Production Build](#production-build)
- [Hosting Options](#hosting-options)
- [CI/CD](#cicd)
- [Environment Configuration](#environment-configuration)
- [Key Takeaways](#key-takeaways)

---

## Production Build

```bash
ng build
```

This outputs optimized files to `dist/my-app/`:

| Optimization | What It Does |
|-------------|-------------|
| AOT compilation | Templates compiled at build time (faster startup) |
| Tree shaking | Removes unused code |
| Minification | Reduces file size |
| Code splitting | Separate chunks for lazy routes |
| Cache busting | File hashes in names for cache invalidation |

### Build Output

```
dist/my-app/browser/
├── index.html
├── main-3a2b1c.js        # Main application code
├── polyfills-4d5e6f.js   # Browser polyfills
├── chunk-7g8h9i.js       # Lazy-loaded route chunk
├── styles-0a1b2c.css     # Compiled styles
└── assets/                # Static assets
```

### Analyzing the Build

```bash
# Generate build stats
ng build --stats-json

# Visualize bundle contents
npx webpack-bundle-analyzer dist/my-app/stats.json
```

---

## Hosting Options

### Static Hosting (CSR Only)

For client-side rendered apps (no SSR):

| Platform | Deploy Command |
|----------|---------------|
| **Netlify** | Drag `dist/my-app/browser/` to Netlify, or connect Git repo |
| **Vercel** | `vercel --prod` |
| **GitHub Pages** | `ng deploy --base-href=/repo-name/` (with `angular-cli-ghpages`) |
| **AWS S3 + CloudFront** | Upload to S3, serve via CloudFront |
| **Firebase Hosting** | `ng add @angular/fire` then `firebase deploy` |

**Important:** Configure the server to redirect all routes to `index.html` (SPA fallback):

```
# Netlify: _redirects file
/*    /index.html   200

# Nginx
location / {
    try_files $uri $uri/ /index.html;
}

# Apache: .htaccess
RewriteEngine On
RewriteRule ^(?!.*\.).*$ /index.html [L]
```

### SSR Hosting

For server-side rendered apps, you need a Node.js server:

| Platform | Notes |
|----------|-------|
| **Firebase App Hosting** | First-class Angular SSR support |
| **Vercel** | Auto-detects Angular SSR |
| **AWS Lambda + API Gateway** | Serverless SSR |
| **Google Cloud Run** | Containerized SSR |
| **Railway / Render** | Docker or Node.js hosting |
| **Self-hosted** | Run `node dist/my-app/server/server.mjs` |

```bash
# Build with SSR
ng build

# Run the server
node dist/my-app/server/server.mjs
```

---

## CI/CD

### GitHub Actions Example

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - run: npm ci

      - run: npm run lint

      - run: npm test -- --no-watch --coverage

      - run: npm run build

      - name: Upload coverage
        uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/
```

### With Deployment

```yaml
  deploy:
    needs: build-and-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - run: npm ci
      - run: npm run build

      # Example: deploy to Firebase
      - uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: ${{ secrets.GITHUB_TOKEN }}
          firebaseServiceAccount: ${{ secrets.FIREBASE_SERVICE_ACCOUNT }}
          channelId: live
```

---

## Environment Configuration

### Runtime Configuration (Recommended)

Load configuration at runtime instead of baking it into the build:

```typescript
// config.service.ts
@Injectable({ providedIn: 'root' })
export class ConfigService {
  private config = signal<AppConfig | null>(null);

  async load(): Promise<void> {
    const response = await fetch('/assets/config.json');
    this.config.set(await response.json());
  }

  get apiUrl(): string {
    return this.config()?.apiUrl ?? '';
  }
}
```

```json
// src/assets/config.json (swapped per environment during deployment)
{
  "apiUrl": "https://api.example.com",
  "features": {
    "darkMode": true
  }
}
```

### Build-Time Environment Files

Angular's `fileReplacements` swap files per build configuration:

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000'
};

// src/environments/environment.prod.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.example.com'
};
```

```json
// angular.json
{
  "configurations": {
    "production": {
      "fileReplacements": [{
        "replace": "src/environments/environment.ts",
        "with": "src/environments/environment.prod.ts"
      }]
    }
  }
}
```

```typescript
// Usage
import { environment } from '../environments/environment';
const API = environment.apiUrl;
```

---

## Deployment Checklist

| Step | Description |
|------|-------------|
| Run `ng build` | Production build with all optimizations |
| Run `ng test` | Ensure all tests pass |
| Run `ng lint` | Check for code quality issues |
| Check bundle size | Analyze and optimize if needed |
| Configure SPA fallback | Redirect all routes to `index.html` |
| Set security headers | CSP, HSTS, X-Frame-Options |
| Enable HTTPS | Required for PWAs and security |
| Configure caching | Cache static assets, not `index.html` |
| Set up monitoring | Error tracking, performance monitoring |

### Cache Strategy

```
# Static assets: cache for 1 year (filenames have hashes)
Cache-Control: public, max-age=31536000, immutable

# index.html: never cache (always get the latest)
Cache-Control: no-cache, no-store, must-revalidate
```

---

## Key Takeaways

- `ng build` produces optimized, tree-shaken, minified output
- **Static hosting** works for CSR apps; **Node.js hosting** required for SSR
- Always configure **SPA routing fallback** (redirect all paths to `index.html`)
- Use **runtime configuration** for environment-specific values when possible
- Set up **CI/CD** to lint, test, and build on every push
- Cache static assets aggressively; never cache `index.html`

---

## Free Resources

> **Official:** [Deployment Guide](https://angular.dev/tools/cli/deployment) — the Angular CLI deployment reference
>
> **YouTube:** [Deploy Angular to Firebase / Vercel / Netlify](https://www.youtube.com/@Fireship) — Fireship's quick deployment walkthroughs for common hosting platforms
>
> **Reference:** [Firebase Hosting Quickstart](https://firebase.google.com/docs/hosting/quickstart) — one of the easiest Angular deployment targets with great CLI integration (`ng deploy`)

---

**Related:**
- [Performance](16-performance.md) — build optimization strategies
- [SSR & Hydration](15-ssr-and-hydration.md) — deploying SSR applications
- [Security](17-security.md) — production security headers

---

[&larr; Advanced Patterns](18-advanced-patterns.md) | [Back to Home &uarr;](../README.md)
