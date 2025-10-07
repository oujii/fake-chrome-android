# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static HTML Progressive Web App (PWA) that simulates iOS Safari browser UI, designed for demonstration/mockup purposes. The project displays Swedish news website mockups (Sydsvenskan, Aftonbladet) with an authentic Safari mobile interface including scrolling behavior and bottom navigation bar animations.

## Architecture

### Core Structure
- **Pure HTML/CSS/JS**: No build tools or frameworks required
- **PWA-enabled**: Installable on iOS devices via manifest.json
- **Static deployment**: Served directly via Netlify

### Key Files
- `index.html` - Main page displaying Sydsvenskan front page with Safari UI
- `article.html` - Article detail page with back navigation
- `aftonbladet/index.html` - Separate Aftonbladet mockup page
- `manifest.json` - PWA configuration for Safari browser simulation
- `netlify.toml` - Deployment and routing configuration

### UI Components
All pages follow the same Safari mobile UI pattern:

1. **Content Area** - Full-height scrollable area with news website mockup
2. **Safari Bottom Bar** - Fixed position bar with two states:
   - **Expanded** (scroll position ≤ 30px): Shows URL bar + navigation icons
   - **Collapsed** (scroll position > 30px): Shows minimal URL text only

### Safari Bottom Bar Behavior
The bottom bar uses CSS transforms and transitions to mimic iOS Safari:
- Scroll detection via `window.scrollY`
- Class toggle on `.safari-bottom-bar` element
- Responsive height adjustments for different iPhone models
- URL bar opacity transition when collapsing

## Development

### Local Testing
```bash
# Serve locally (Python)
python3 -m http.server 8000

# Serve locally (Node.js)
npx http-server -p 8000
```

Visit: `http://localhost:8000`

### Deployment
Automatic deployment via Netlify on git push:
- Publish directory: `.` (root)
- No build command required
- Redirects configured for SPA-style routing

## PWA Configuration

### Manifest Details
- **App name**: "Safari" (simulates Safari browser)
- **Display mode**: `standalone` (fullscreen without browser chrome)
- **Theme colors**: Black (#000000) to match Safari dark theme
- **Icons**: 8 sizes from 72x72 to 512x512 (see `/icon-*.png`)

### iOS Meta Tags
Critical tags in HTML `<head>`:
```html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover">
```

## Assets

### Typography
- **SF Pro Text**: Custom font files (`sf-pro-text-*.woff`) for iOS-authentic appearance
- Fallbacks: `-apple-system, BlinkMacSystemFont, sans-serif`

### Images
- `.webp` format for optimized news images (`ettan.webp`, `artikel.webp`)
- `.svg` icons for navigation (back, forward, share, reload, etc.)
- `.png` fallbacks available

### Navigation Icons
- `back.svg` / `forward.svg` - Browser navigation
- `share.svg` - Share sheet
- `readlater.svg` - Reading list
- `viewtabs.svg` - Tab switcher
- `site settings.svg` - Site permissions
- `reload.svg` - Page refresh

## Responsive Design

Bottom bar heights adjust per iPhone model:
- **≤375px** (SE, mini): 140px expanded, 100px collapsed
- **376-414px** (standard): 140px expanded, 100px collapsed
- **≥415px** (Pro Max): 150px expanded, 110px collapsed

All measurements include home indicator spacing (`padding-bottom`).

## Implementation Notes

### Scroll Handler
Three event listeners ensure PWA compatibility:
```javascript
window.addEventListener('scroll', handleScroll, { passive: true });
document.addEventListener('scroll', handleScroll, { passive: true });
window.addEventListener('touchmove', handleScroll, { passive: true });
```

### Navigation
Simple `window.location.href` for page transitions:
- `index.html` → `article.html` (forward)
- `article.html` → `index.html` (back)

### Testing Functions
`forceHidden()` function in scripts for testing collapsed state without scrolling.

## Netlify Configuration

### Headers
- Manifest served as `application/manifest+json`
- Cache-busting headers for all resources (no-cache during development)

### Redirects
SPA-style redirect: `/* → /index.html` (status 200) ensures deep linking works in PWA mode.

## Project Purpose

This is a UI mockup/demonstration tool, not a functional browser. The project simulates Safari iOS UI for:
- Design presentations
- User interface testing
- Screenshot generation
- PWA behavior demonstrations
