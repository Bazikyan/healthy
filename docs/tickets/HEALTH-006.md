# HEALTH-006 — Configure vite-plugin-pwa and manifest

| Field | Value |
|-------|-------|
| **Epic** | E2 — PWA shell |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [02-pwa-shell.md](../specs/02-pwa-shell.md) |
| **Depends on** | HEALTH-001 |
| **Blocks** | HEALTH-008, HEALTH-024 |

## User story

As a mobile user, I want to install the app on my home screen, so that it feels like a native app.

## Summary

Integrate `vite-plugin-pwa` to generate web manifest, service worker, and precached assets at build time.

## Technical approach

### Install

```bash
npm i -D vite-plugin-pwa
```

### `vite.config.js` changes

```js
import { VitePWA } from 'vite-plugin-pwa';

VitePWA({
  registerType: 'autoUpdate',
  includeAssets: ['favicon.ico', 'icons/*.png'],
  manifest: {
    name: 'Healthy',
    short_name: 'Healthy',
    description: 'Track weight and nutrition',
    theme_color: '#0d9488',
    background_color: '#ffffff',
    display: 'standalone',
    start_url: '/',
    scope: '/',
    icons: [
      { src: '/icons/icon-192.png', sizes: '192x192', type: 'image/png' },
      { src: '/icons/icon-512.png', sizes: '512x512', type: 'image/png' },
      { src: '/icons/icon-512.png', sizes: '512x512', type: 'image/png', purpose: 'maskable' },
    ],
  },
  workbox: {
    globPatterns: ['**/*.{js,css,html,ico,png,svg,woff2}'],
    navigateFallback: null,
  },
})
```

### Icons

- Add `public/icons/icon-192.png`, `icon-512.png` (placeholder brand color acceptable)
- Add `apple-touch-icon` link in layout (HEALTH-007)

### Layout meta

```html
<meta name="theme-color" content="#0d9488">
<link rel="manifest" href="/build/manifest.webmanifest">
```

Note: Vite PWA injects registration in `app.js` — add to `resources/js/app.js`:

```js
import { registerSW } from 'virtual:pwa-register';
registerSW({ immediate: true });
```

### Implementation tasks

- [ ] Install plugin and configure manifest
- [ ] Generate placeholder icons (or use simple SVG→PNG script)
- [ ] Verify `npm run build` outputs `sw.js` / `workbox-*.js` in `public/build`
- [ ] Test SW registers in DevTools → Application

## Acceptance criteria

- [ ] Lighthouse PWA audit: installable, has manifest and SW
- [ ] Manifest `display: standalone`, `start_url: /`
- [ ] Theme/background colors match Tailwind primary palette
- [ ] Production build registers service worker without console errors

## Test plan

| Test | Type |
|------|------|
| Build succeeds | CI script `npm run build` |
| Manifest JSON valid | Optional PHP/Node parse test post-build |

## Edge cases & notes

- **HTTPS required** for SW in production; local `php artisan serve` + Vite dev may not register SW — test with `npm run build` + serve public
- `registerType: 'prompt'` alternative if you want update UX later
- Do not cache authenticated HTML aggressively yet (HEALTH-008)

## QA checklist (manual)

- [ ] Chrome DevTools → Application → Manifest shows correct fields
- [ ] Android: “Install app” appears
- [ ] iOS: Add to Home Screen manual test documented

## Definition of done

- [ ] Plugin configured; icons committed; build green
