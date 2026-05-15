# Health PWA — Technical Specification Overview

## Product summary

Personal health tracker (Laravel 12 PWA) for weight goals and daily nutrition. Single-user accounts; all data private and scoped by `user_id`.

## Stack

| Component | Technology |
|-----------|------------|
| Backend | Laravel 12, PHP 8.2+ |
| Auth | Laravel Breeze (Blade) |
| Frontend | Blade, Alpine.js, Tailwind 4, Vite 7 |
| PWA | vite-plugin-pwa |
| Charts | Chart.js |
| Food data | Open Food Facts API v2 |
| Barcode | @zxing/browser or html5-qrcode |

## Epics (spec documents)

| Spec | Scope |
|------|--------|
| [01-foundation.md](./01-foundation.md) | Auth, profile, goals, TDEE, onboarding |
| [02-pwa-shell.md](./02-pwa-shell.md) | Manifest, service worker, layout, bottom nav |
| [03-weight-tracking.md](./03-weight-tracking.md) | Weight log, charts, goal progress |
| [04-diet-nutrition.md](./04-diet-nutrition.md) | Meals, OFF API, barcode, daily totals |
| [05-dashboard-history.md](./05-dashboard-history.md) | Dashboard, history, export, offline UX |

## Cross-cutting requirements

### Authorization

- Every query/mutation filters by `auth()->id()`.
- Policies on `WeightEntry`, `MealEntry`, `MealItem`, `SavedFood`.
- Return 404 (not 403) for other users’ resource IDs to avoid enumeration.

### Validation

- Server-side Form Requests for all writes.
- Dates: user timezone from profile or browser default (store UTC in DB, display local).

### Non-goals (v1)

- Coach/sharing, social, AI meal plans, exercise/water/sleep, native apps.

### Compliance copy

- Footer on authenticated pages: “Not medical advice. Consult a healthcare professional.”

## Database entities (summary)

```
users
user_profiles (1:1 users)
weight_entries (user_id, logged_at unique)
meal_entries (user_id, date, meal_type)
meal_items (meal_entry_id)
saved_foods (user_id)
food_search_cache (optional, global or keyed by query)
```

## API surface (internal)

- Server-rendered routes only for v1 (no public REST API).
- Optional JSON endpoints for food search/barcode (AJAX from Alpine) returning DTOs, auth required.

## Definition of done (project)

- [ ] All v1 tickets closed
- [ ] Feature tests pass for auth, profile, weight, meals, policies
- [ ] PWA installable; Lighthouse PWA basics pass
- [ ] README documents env setup and `composer run dev`
