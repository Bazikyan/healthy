# HEALTH-021 — Dashboard page

| Field | Value |
|-------|-------|
| **Epic** | E5 — Dashboard, history, export |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [05-dashboard-history.md](../specs/05-dashboard-history.md) |
| **Depends on** | HEALTH-004, HEALTH-010, HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want a home screen showing today's progress, so that I see weight and nutrition status at a glance.

## Summary

`/dashboard` replaces Breeze default with widgets: nutrition progress, meals shortcut, weight snippet, logging streak.

## Technical approach

### Controller: `DashboardController@index`

Load in single query batch:

- `UserProfile` targets
- `DiaryService::daySummary(today)`
- Latest `WeightEntry` + delta 7d via `WeightStats`
- `StreakService::current(User $user)`

### Service: `App\Support\StreakService`

```php
// Count consecutive days backward from today where:
// exists weight_entry OR exists any meal_item on that date
public function current(User $user): int;
```

### View: `resources/views/dashboard/index.blade.php`

Widgets (Blade components):

1. `x-nutrition-summary` — 4 progress bars (cal, P, C, F)
2. `x-meals-today` — list meal types with item count or “Add food”
3. `x-weight-snippet` — latest kg, Δ7d, CTA if not logged today
4. `x-streak-badge` — “🔥 5 day streak” (no emoji if prefer text)

### Routes

```php
Route::get('/dashboard', [DashboardController::class, 'index'])
    ->middleware(['auth', 'onboarding'])
    ->name('dashboard');
```

Redirect `/` → `/dashboard` for auth users.

### Implementation tasks

- [ ] StreakService + unit tests
- [ ] DashboardController
- [ ] Widget components
- [ ] No HTTP calls to OFF on dashboard

## Acceptance criteria

- [ ] Shows today's calorie/macro progress vs profile targets
- [ ] Weight widget links to `/weight`
- [ ] Meals widget links to `/diary`
- [ ] Streak = 0 for brand new user
- [ ] Page loads without N+1 (eager load items)

## Test plan

`tests/Feature/DashboardTest.php` — seeded user sees correct totals

## Definition of done

- [ ] Dashboard is default post-login landing
