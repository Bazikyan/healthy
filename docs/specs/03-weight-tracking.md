# Spec 03 — Weight Tracking

## Objective

Log daily weight, view trends, and see progress toward target weight.

## Data model: `weight_entries`

| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| user_id | bigint FK | index |
| logged_at | date | unique with user_id |
| weight_kg | decimal(5,2) | > 20, < 500 |
| note | string nullable | max 500 |
| timestamps | | |

**Constraint:** `unique(user_id, logged_at)`

## Routes

| Method | URI | Action |
|--------|-----|--------|
| GET | /weight | index: chart + list + quick form |
| POST | /weight | store (today or chosen date) |
| GET | /weight/{entry}/edit | edit |
| PATCH | /weight/{entry} | update |
| DELETE | /weight/{entry} | destroy |

## UI

### Weight page

- **Quick log form:** date (default today), weight, optional note
- **Chart.js line chart:** weight vs date
- **Range tabs:** 7d | 30d | 90d | All
- **Stats row:** latest, Δ vs 7 days ago, remaining to target (from profile)

### Dashboard snippet (Spec 05)

- Latest weight, sparkline or “log today” CTA if missing today

## Calculations

```php
// Progress to goal
$remaining = $profile->target_weight_kg - $latest->weight_kg; // sign depends on goal_type

// 7-day change
$delta = $latest->weight_kg - $entrySevenDaysAgo?->weight_kg;
```

Optional linear trend: least-squares on last N points for dashed projection line (display only).

## Authorization

- `WeightEntryPolicy`: view/update/delete only owner

## Acceptance criteria

- One entry per calendar day per user; second POST same day updates existing (upsert UX)
- Chart updates after log without full page break (Turbo/Livewire optional; full redirect OK v1)
- Cannot access another user’s entry ID
- Empty state: prompt to log first weight

## Tests

- `WeightEntryTest`: CRUD, unique per day, policy
- `WeightChartTest`: API returns correct JSON series for date range (if JSON endpoint added)
