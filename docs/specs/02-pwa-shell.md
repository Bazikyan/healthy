# Spec 02 — PWA Shell & App Layout

## Objective

Installable, mobile-first shell with bottom navigation and offline-friendly app assets.

## Dependencies

- Spec 01 (auth layouts exist from Breeze)

## Packages

```bash
npm i -D vite-plugin-pwa
```

## Web App Manifest

| Field | Value |
|-------|-------|
| name | Healthy (or product name from env) |
| short_name | Healthy |
| start_url | / |
| display | standalone |
| background_color | theme background |
| theme_color | primary brand color |
| icons | 192x192, 512x512 PNG in `public/icons/` |

## Service worker strategy

- **Precache:** built CSS/JS, manifest, icons, offline fallback view
- **NetworkFirst:** HTML document routes (or StaleWhileRevalidate for navigations)
- **Runtime cache:** optional for OFF API responses (short TTL, max entries)

## Offline fallback page

- Route/view: `resources/views/offline.blade.php`
- Message: “You’re offline. Some features need a connection.”
- Link to retry

## Layout: `layouts/app.blade.php`

### Mobile bottom nav (authenticated)

| Tab | Route | Icon |
|-----|-------|------|
| Home | `/dashboard` | home |
| Diary | `/diary` | book |
| Weight | `/weight` | scale |
| Profile | `/settings/profile` | user |

- Fixed bottom bar, `padding-bottom: env(safe-area-inset-bottom)`
- Active state via `request()->routeIs()`

### Desktop

- Same nav or left sidebar ≥768px (team choice: bottom nav acceptable on desktop for v1)

## Meta tags (layout head)

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="theme-color" content="...">
<link rel="apple-touch-icon" href="...">
```

## Vite config changes

- Register `VitePWA` plugin with manifest + workbox options
- `registerType: 'autoUpdate'` or prompt user on update

## Acceptance criteria

- Lighthouse: installable, has manifest, service worker registered
- Add to Home Screen works on iOS Safari and Android Chrome (manual QA checklist)
- Offline: visiting cached route shows shell; uncached POST shows friendly error
- Bottom nav visible on all main app pages; hidden on auth guest pages

## Tests

- Manual QA checklist documented in ticket HEALTH-008
- Optional: Pest test that manifest JSON is valid (parse `public/build/manifest.webmanifest` after build)
