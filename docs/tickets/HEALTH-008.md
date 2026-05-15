# HEALTH-008 — Offline fallback page and SW caching

| Field | Value |
|-------|-------|
| **Epic** | E2 — PWA shell |
| **Priority** | P1 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [02-pwa-shell.md](../specs/02-pwa-shell.md) |
| **Depends on** | HEALTH-006 |
| **Blocks** | HEALTH-024 |

## User story

As a user with poor connectivity, I want the app to show a helpful message when offline, so that I understand why actions fail.

## Summary

Add offline fallback view, tune Workbox strategies for app shell vs dynamic HTML, document QA steps for airplane mode.

## Technical approach

### Offline view

- `resources/views/offline.blade.php` — minimal layout, no bottom nav
- Copy: “You’re offline. Check your connection and try again.”
- Button: `window.location.reload()`

### Workbox config (in `vite.config.js`)

```js
workbox: {
  navigateFallback: '/offline',
  navigateFallbackDenylist: [/^\/api/, /^\/login/, /^\/register/],
  runtimeCaching: [
    {
      urlPattern: /^https:\/\/world\.openfoodfacts\.org\/.*/i,
      handler: 'NetworkFirst',
      options: { cacheName: 'off-api', expiration: { maxEntries: 50, maxAgeSeconds: 86400 } },
    },
  ],
}
```

### Laravel route

```php
Route::view('/offline', 'offline')->name('offline');
```

Ensure offline page is precached in `globPatterns` or `additionalManifestEntries`.

### Implementation tasks

- [ ] Create offline blade view + route
- [ ] Configure `navigateFallback` for document requests
- [ ] Precache `/offline` HTML or inline in SW
- [ ] Document iOS A2HS + Android install in README (HEALTH-026)
- [ ] QA checklist file: `docs/qa/pwa-offline.md`

## Acceptance criteria

- [ ] Airplane mode: navigating to uncached URL shows offline page
- [ ] Cached CSS/JS still load for previously visited pages
- [ ] `/api/*` not served stale offline cache as success (NetworkFirst fails clearly)
- [ ] README includes PWA test steps

## Test plan

Manual QA per `docs/qa/pwa-offline.md`:

1. Load app online, visit dashboard
2. Enable airplane mode
3. Navigate to new route → offline fallback
4. Disable airplane mode → reload works

## Edge cases & notes

- Authenticated HTML caching is tricky — v1: fallback only, not full offline dashboard
- Service worker update may require hard refresh during dev

## Definition of done

- [ ] Offline page works on built assets (`npm run build`)
- [ ] QA doc committed
