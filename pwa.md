# PWA (Progressive Web App) Implementation Guide

## Overview

This document provides a comprehensive technical guide for implementing PWA functionality with Netlify deployment, specifically designed for Next.js applications. Use this as a blueprint for replicating PWA functionality in new projects.

## Technical Architecture

### Core Components

1. **Next.js Configuration** (`next.config.ts`)
2. **Web App Manifest** (`public/manifest.json`)
3. **HTML Meta Tags** (`app/layout.tsx`)
4. **Netlify Configuration** (`netlify.toml`)
5. **Icon Assets** (`public/icon-*.png`)

## Implementation Steps

### 1. Next.js Configuration (`next.config.ts`)

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: 'export',              // Static export for Netlify
  trailingSlash: true,           // Ensures proper routing
  basePath: process.env.NODE_ENV === 'production' ? '' : '',
  images: {
    unoptimized: true,           // Required for static export
    remotePatterns: [            // Allow external image sources
      {
        protocol: 'https',
        hostname: 'placehold.co',
        port: '',
        pathname: '/**',
      },
      {
        protocol: 'https',
        hostname: 'img.icons8.com',
        port: '',
        pathname: '/**',
      },
    ],
    dangerouslyAllowSVG: true,
    contentDispositionType: 'attachment',
  },
};

export default nextConfig;
```

**Key Settings:**
- `output: 'export'` - Generates static files for Netlify
- `images.unoptimized: true` - Disables Next.js image optimization (required for static export)
- `trailingSlash: true` - Ensures consistent routing

### 2. Web App Manifest (`public/manifest.json`)

```json
{
  "name": "Your App Name",
  "short_name": "AppName",
  "description": "Your app description",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#101828",
  "theme_color": "#101828",
  "orientation": "portrait",
  "scope": "/",
  "categories": ["security", "utilities"],
  "icons": [
    {
      "src": "/icon-72x72.png",
      "sizes": "72x72",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-96x96.png",
      "sizes": "96x96",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-128x128.png",
      "sizes": "128x128",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-144x144.png",
      "sizes": "144x144",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-152x152.png",
      "sizes": "152x152",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "/icon-384x384.png",
      "sizes": "384x384",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}
```

**Critical Properties:**
- `display: "standalone"` - Removes browser UI
- `purpose: "any maskable"` - For adaptive icons on Android
- Complete icon set (72x72 to 512x512)

### 3. HTML Meta Tags (`app/layout.tsx`)

```typescript
export const metadata: Metadata = {
  title: "Your App Name",
  description: "Your app description",
  manifest: "/manifest.json",
  themeColor: "#101828",
  viewport: "width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no",
  icons: {
    icon: [
      { url: "/icon-192x192.png", sizes: "192x192", type: "image/png" },
      { url: "/icon-512x512.png", sizes: "512x512", type: "image/png" }
    ],
    apple: [
      { url: "/icon-152x152.png", sizes: "152x152", type: "image/png" }
    ]
  },
  appleWebApp: {
    capable: true,
    title: "Your App Name",
    statusBarStyle: "black-translucent"
  },
  other: {
    "mobile-web-app-capable": "yes",
    "apple-mobile-web-app-capable": "yes",
    "apple-mobile-web-app-status-bar-style": "black-translucent"
  }
};
```

**Essential Meta Tags:**
- `manifest: "/manifest.json"` - Links to PWA manifest
- `viewport` with `user-scalable=no` - Prevents zooming (app-like behavior)
- `appleWebApp.capable: true` - Enables iOS PWA features
- `statusBarStyle: "black-translucent"` - iOS status bar styling

### 4. Netlify Configuration (`netlify.toml`)

```toml
[build]
  publish = "out"                    # Next.js export directory
  command = "npm run build"          # Build command

[build.environment]
  NODE_VERSION = "18"                # Node.js version

[[headers]]
  for = "/manifest.json"
  [headers.values]
    Content-Type = "application/manifest+json"

[[headers]]
  for = "/*"
  [headers.values]
    Cache-Control = "no-cache, no-store, must-revalidate"
    Pragma = "no-cache"
    Expires = "0"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

**Key Configuration:**
- `publish = "out"` - Points to Next.js export directory
- Manifest MIME type header
- Cache-busting headers for development
- SPA redirect rule for client-side routing

### 5. Package.json Scripts

```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build --turbopack",
    "start": "next start",
    "lint": "eslint"
  }
}
```

## Icon Requirements

### Required Icon Sizes
Create icons in the following sizes and place in `public/`:

- `icon-72x72.png`
- `icon-96x96.png`
- `icon-128x128.png`
- `icon-144x144.png`
- `icon-152x152.png`
- `icon-192x192.png`
- `icon-384x384.png`
- `icon-512x512.png`

### Icon Generation Command
```bash
# Use this Node.js script to generate all icon sizes from a source image
node -e "
const { createCanvas, loadImage } = require('canvas');
const fs = require('fs');

const sizes = [72, 96, 128, 144, 152, 192, 384, 512];

loadImage('source-icon.png').then(image => {
  sizes.forEach(size => {
    const canvas = createCanvas(size, size);
    const ctx = canvas.getContext('2d');
    ctx.drawImage(image, 0, 0, size, size);
    const buffer = canvas.toBuffer('image/png');
    fs.writeFileSync(\`public/icon-\${size}x\${size}.png\`, buffer);
    console.log(\`Generated icon-\${size}x\${size}.png\`);
  });
});
"
```

## Deployment Workflow

### 1. Build Process
```bash
npm run build          # Generates static files in /out
```

### 2. Netlify Deploy
1. Connect GitHub repository to Netlify
2. Set build command: `npm run build`
3. Set publish directory: `out`
4. Deploy automatically on git push

### 3. Verification Steps
1. **Desktop**: Visit site, check for "Install app" option in browser
2. **Mobile iOS**: Safari → Share → "Add to Home Screen"
3. **Mobile Android**: Chrome → Menu → "Install app"
4. **Lighthouse PWA Audit**: Score should be 100/100

## Offline Functionality (Optional Enhancement)

### Service Worker Implementation
```javascript
// public/sw.js
const CACHE_NAME = 'app-v1';
const urlsToCache = [
  '/',
  '/static/js/bundle.js',
  '/static/css/main.css',
  '/manifest.json'
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});
```

### Register Service Worker
```typescript
// app/layout.tsx - Add to useEffect
useEffect(() => {
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/sw.js');
  }
}, []);
```

## Troubleshooting Common Issues

### 1. PWA Not Installing
- Check manifest.json is accessible at `/manifest.json`
- Verify all required icon sizes exist
- Ensure `display: "standalone"` in manifest
- Check browser console for manifest errors

### 2. Icons Not Showing
- Verify icon file paths in manifest.json
- Check icon files exist in `public/` directory
- Ensure correct MIME type: `image/png`

### 3. Routing Issues on Netlify
- Verify redirect rule in netlify.toml: `from = "/*" to = "/index.html"`
- Check `trailingSlash: true` in next.config.ts

### 4. Build Failures
- Ensure `output: 'export'` in next.config.ts
- Set `images.unoptimized: true` for static export
- Remove server-side only features (API routes, etc.)

## Project Checklist

When implementing PWA in a new project, ensure:

- [ ] `next.config.ts` configured with `output: 'export'`
- [ ] `manifest.json` created with app metadata
- [ ] All 8 icon sizes generated and placed in `public/`
- [ ] HTML meta tags added to layout
- [ ] `netlify.toml` configured for deployment
- [ ] Build script uses Turbopack: `next build --turbopack`
- [ ] Test PWA installation on mobile and desktop
- [ ] Lighthouse PWA audit passes

## Performance Considerations

### Static Export Benefits
- **Faster Loading**: Pre-rendered HTML
- **CDN Distribution**: Netlify's global CDN
- **Offline Capable**: All assets cached locally
- **No Server Dependency**: Pure static hosting

### Film/TV Production Benefits
- **Deterministic Playback**: No network dependencies
- **Instant Loading**: Critical for filming schedules
- **Reliable Performance**: No server downtimes
- **Easy Handoff**: Single URL deployment

## Security Considerations

### Content Security Policy (Optional)
```html
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; img-src 'self' data: https:; script-src 'self' 'unsafe-inline';">
```

### HTTPS Requirement
- PWAs require HTTPS in production
- Netlify provides automatic HTTPS
- Development can use HTTP on localhost

---

## Claude AI Instructions

When implementing PWA functionality in a new project based on this guide:

1. **Copy Configuration Files**: Start with next.config.ts, manifest.json, and netlify.toml
2. **Generate Icons**: Create all 8 required icon sizes using the provided Node.js script
3. **Update Metadata**: Modify app/layout.tsx with project-specific metadata
4. **Test Installation**: Verify PWA installation works on mobile and desktop
5. **Deploy to Netlify**: Connect repository and verify deployment
6. **Lighthouse Audit**: Ensure PWA score is 100/100

**Critical Success Factors:**
- All icon sizes must exist
- Manifest.json must be valid JSON
- Static export must be enabled
- Netlify redirects must handle SPA routing
- HTTPS must be enabled in production

This implementation ensures reliable, offline-capable PWA functionality suitable for professional film/TV production environments.