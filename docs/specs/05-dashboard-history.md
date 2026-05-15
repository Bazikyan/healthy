# Spec 05 — Dashboard, History, Export, Offline UX

## Objective

Unified home view, historical day review, data export, and graceful offline behavior.

## Dashboard (`/dashboard`)

### Widgets

1. **Nutrition today** — calorie ring or bars (cal / protein / carbs / fat vs targets)
2. **Meals shortcut** — list today’s meals or “Add food” if empty
3. **Weight** — latest + Δ7d + link to `/weight`; CTA if not logged today
4. **Streak** — consecutive days with ≥1 weight entry OR ≥1 meal item (configurable: either counts)

### Streak algorithm

```php
// Walk backward from today until gap
$streak = 0;
for ($d = today(); hasActivity($user, $d); $d->subDay()) $streak++;
```

## History (`/history`)

- **List view:** last 30 days default, paginated
- Each row: date, weight, calorie total, macro summary
- **Day detail** `/history/{date}`: weight entry + all meals/items, edit links

## Export (`GET /settings/export`)

- CSV download, auth required
- Files or single ZIP:
  - `weight.csv`: date, weight_kg, note
  - `food_log.csv`: date, meal_type, name, quantity, calories, protein_g, carbs_g, fat_g
- Filename: `healthy-export-{Y-m-d}.csv`

## Offline UX (v1)

| Scenario | Behavior |
|----------|----------|
| View dashboard (cached) | Show last rendered HTML from SW or stale page |
| POST weight/food offline | Show toast: “Offline — try again when connected” |
| Navigate uncached route | offline.blade.php |

No IndexedDB write queue in v1 (document as v2).

## Acceptance criteria

- Dashboard loads in <2s on 3G (no blocking OFF calls; totals from DB only)
- History day detail matches diary for that date
- Export contains only requesting user’s data
- Streak displays 0 for new user

## Tests

- `DashboardTest`: authenticated user sees targets
- `ExportTest`: CSV rows match seeded data
- `HistoryTest`: cannot view other user’s date via URL manipulation
