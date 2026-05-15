# HEALTH-011 — Weight chart and stats

| Field | Value |
|-------|-------|
| **Epic** | E3 — Weight tracking |
| **Priority** | P1 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [03-weight-tracking.md](../specs/03-weight-tracking.md) |
| **Depends on** | HEALTH-010 |
| **Blocks** | HEALTH-021 |

## User story

As a user, I want to see my weight trend on a chart, so that I can visualize progress toward my goal.

## Summary

Add Chart.js line chart to `/weight` with range filters (7/30/90/all) and stats row: latest, 7-day delta, remaining to target.

## Technical approach

### Install

```bash
npm i chart.js
```

Import in `resources/js/weight-chart.js` or inline in weight page via `@vite`.

### Data endpoint (choose one)

**Option A — inline JSON in Blade:**

```php
$entries = auth()->user()->weightEntries()->where('logged_at', '>=', $since)->get();
// pass to @json in view
```

**Option B — JSON API:**

`GET /weight/chart-data?range=30` → `{ labels: [], weights: [] }`

Recommend **Option A** for v1 simplicity.

### Stats service: `App\Support\WeightStats`

```php
public function latest(User $user): ?WeightEntry;
public function deltaDays(User $user, int $days): ?float;
public function remainingToTarget(User $user, UserProfile $profile, float $currentKg): ?float;
```

**Remaining:** sign depends on goal — lose: `current - target`, gain: `target - current`

### Chart UI

- Alpine tabs or links: `?range=7|30|90|all`
- Chart.js config: line, tension 0.3, y-axis min/max padded
- Empty state: illustration + “Log your first weight”

### Optional projection

Dashed line: linear regression on last 14 points — label “Trend (estimate only)”

### Implementation tasks

- [ ] Install Chart.js
- [ ] `WeightStats` helper
- [ ] Update weight index view with chart canvas + range switcher
- [ ] Stats cards above chart

## Acceptance criteria

- [ ] Chart shows all entries in selected range
- [ ] 7d/30d/90d/all filters change data
- [ ] Empty state when no entries
- [ ] “Remaining to goal” hidden if maintain or no target
- [ ] Stats: Δ7d = null if no entry 7 days ago

## Test plan

| Test | File |
|------|------|
| `WeightStatsTest` unit | delta, remaining |
| Feature: weight page loads 200 | `WeightChartTest.php` |

## QA checklist

- [ ] Chart readable on 375px width
- [ ] Single data point renders without error

## Definition of done

- [ ] Chart renders with seeded data; tests pass
