# HEALTH-022 — History list and day detail

| Field | Value |
|-------|-------|
| **Epic** | E5 — Dashboard, history, export |
| **Priority** | P1 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [05-dashboard-history.md](../specs/05-dashboard-history.md) |
| **Depends on** | HEALTH-010, HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want to browse past days and see what I logged, so that I can review my history.

## Summary

`/history` paginated list of days with summary stats; `/history/{date}` detail with weight + meals.

## Technical approach

### Controller: `HistoryController`

| Method | Route |
|--------|-------|
| index | GET `/history` |
| show | GET `/history/{date}` — `date` format Y-m-d |

### Index query

Aggregate per day for user:

```sql
SELECT date, weight_kg, SUM(calories) as calories, ...
FROM (... meal_items joined meals ...) 
GROUP BY date
ORDER BY date DESC
LIMIT 30 per page
```

Implement via Eloquent subqueries or `DB::table` for performance.

### Show page

- Weight entry for date (if any)
- All meal sections + items (read-only with edit links to diary/weight)

### UI

- List: date, weight, calorie total, macro mini summary
- Tap row → day detail
- Link from Profile or dashboard “View history”

### Implementation tasks

- [ ] Controller + views
- [ ] Pagination (LengthAwarePaginator)
- [ ] Invalid date format → 404
- [ ] Future dates → 404

## Acceptance criteria

- [ ] Lists only current user's data
- [ ] Day detail matches diary for that date
- [ ] Empty days omitted from list OR shown with “—” (product: **omit days with no activity**)
- [ ] Edit links go to diary with `?date=` pre-filled

## Test plan

`tests/Feature/HistoryTest.php`

## Definition of done

- [ ] History navigable on mobile
