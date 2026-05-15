# HEALTH-007 — App layout and bottom navigation

| Field | Value |
|-------|-------|
| **Epic** | E2 — PWA shell |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [02-pwa-shell.md](../specs/02-pwa-shell.md) |
| **Depends on** | HEALTH-001 |
| **Blocks** | HEALTH-010, HEALTH-015, HEALTH-021, HEALTH-027 |

## User story

As a mobile user, I want quick navigation between main sections, so that I can log food and weight without digging through menus.

## Summary

Create `layouts/app.blade.php` with fixed bottom navigation (4 tabs), safe-area padding, and main content area. Extend Breeze layout or replace `layouts/app` from Breeze.

## Technical approach

### Layout structure

```
┌─────────────────────────┐
│  Header (optional title)│
├─────────────────────────┤
│                         │
│  {{ $slot }} / @yield    │
│                         │
├─────────────────────────┤
│  Bottom nav (4 tabs)    │
│  safe-area-inset-bottom │
└─────────────────────────┘
```

### Component: `resources/views/components/bottom-nav.blade.php`

| Tab | Label | Route | `routeIs` pattern |
|-----|-------|-------|-------------------|
| Home | Home | `dashboard` | `dashboard` |
| Diary | Diary | `diary.index` | `diary.*` |
| Weight | Weight | `weight.index` | `weight.*` |
| Profile | Profile | `settings.profile` | `settings.*` |

Use Heroicons (SVG inline) or simple Unicode icons for v1.

### Tailwind patterns

```html
<nav class="fixed bottom-0 inset-x-0 border-t bg-white pb-[env(safe-area-inset-bottom)]">
  <div class="grid grid-cols-4 h-16">...</motion.div>
</nav>
<main class="pb-20 min-h-screen">...</main>
```

### Head meta (layout)

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<link rel="apple-touch-icon" href="/icons/icon-192.png">
```

### Routes to stub (if not yet implemented)

- `Route::view('/diary', 'diary.index')->name('diary.index');` — replaced in HEALTH-015
- `Route::view('/weight', 'weight.index')->name('weight.index');` — replaced in HEALTH-010

### Implementation tasks

- [ ] Create `layouts/app.blade.php` extending Vite assets
- [ ] Create `bottom-nav` component with active states
- [ ] Hide bottom nav on `guest` layout (login/register)
- [ ] Apply layout to dashboard, settings, placeholder diary/weight views
- [ ] Page title slot: `<x-slot name="header">Today</x-slot>`

## Acceptance criteria

- [ ] All 4 tabs navigate without full layout break
- [ ] Active tab highlighted on current section
- [ ] Content not hidden behind nav (`pb-20` on main)
- [ ] Works on iPhone with home indicator (safe area)

## Test plan

Manual QA only for layout; optional Dusk later.

## Edge cases & notes

- Desktop: bottom nav acceptable for v1; optional `md:sidebar` in v2
- Unauthenticated users use Breeze `layouts/guest` only

## Definition of done

- [ ] App layout used by all authenticated main pages
- [ ] Guest pages have no bottom nav
