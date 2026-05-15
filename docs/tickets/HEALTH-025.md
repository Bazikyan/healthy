# HEALTH-025 — Feature test suite (auth & policies)

| Field | Value |
|-------|-------|
| **Epic** | E6 — Quality & launch |
| **Priority** | P0 |
| **Estimate** | M (~1–2 days) |
| **Status** | backlog |
| **Spec** | [00-overview.md](../specs/00-overview.md) |
| **Depends on** | All core tickets (001–015, 021) |
| **Blocks** | — |

## User story

As a developer, I want automated tests for critical flows, so that regressions are caught before release.

## Summary

Consolidate and fill gaps in PHPUnit feature/unit tests; optional GitHub Actions workflow.

## Technical approach

### Required test files

| File | Covers |
|------|--------|
| `NutritionCalculatorTest` | HEALTH-003 |
| `OnboardingTest` | HEALTH-004 |
| `ProfileSettingsTest` | HEALTH-005 |
| `WeightEntryTest` | HEALTH-010 |
| `WeightStatsTest` | HEALTH-011 |
| `OpenFoodFactsClientTest` | HEALTH-013 |
| `FoodSearchApiTest` | HEALTH-014 |
| `DiaryTest` | HEALTH-015 |
| `DashboardTest` | HEALTH-021 |

### Cross-user authorization tests

Pattern:

```php
$userA = User::factory()->create();
$userB = User::factory()->create();
$entry = WeightEntry::factory()->for($userB)->create();
$this->actingAs($userA)->patch(route('weight.update', $entry))->assertNotFound();
```

Apply to: WeightEntry, MealEntry, MealItem, SavedFood

### CI: `.github/workflows/tests.yml`

```yaml
- uses: shivammathur/setup-php@v2
  with: { php-version: '8.2' }
- run: composer install
- run: cp .env.example .env && php artisan key:generate
- run: touch database/database.sqlite
- run: php artisan migrate --force
- run: php artisan test
```

### Implementation tasks

- [ ] Audit coverage gaps per ticket
- [ ] Add missing tests
- [ ] CI workflow
- [ ] Document `composer test` in README

## Acceptance criteria

- [ ] `php artisan test` green locally
- [ ] CI passes on push (if repo on GitHub)
- [ ] Every policy has at least one unauthorized test
- [ ] No tests hit real OFF API (Http::fake only)

## Definition of done

- [ ] Full suite green; CI configured or documented
