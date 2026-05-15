# HEALTH-012 — Meal migrations and models

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P0 |
| **Estimate** | S (~3h) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-002 |
| **Blocks** | HEALTH-015, HEALTH-018, HEALTH-019, HEALTH-021, HEALTH-023 |

## User story

As a user, I want meals and food items stored separately, so that I can log multiple foods per breakfast/lunch/dinner/snack.

## Summary

Create `meal_entries` and `meal_items` tables, enums, models, policies, factories, and helper to get or create meal slot for a date.

## Technical approach

### Enum: `MealType`

`Breakfast`, `Lunch`, `Dinner`, `Snack`

### Enum: `FoodSource`

`OpenFoodFacts`, `Manual`

### Migration: `meal_entries`

- `unique(['user_id', 'date', 'meal_type'])`

### Migration: `meal_items`

- FK `meal_entry_id` cascade on delete

### Models

**MealEntry:**

```php
public function items(): HasMany;
public function totals(): array; // sum calories, protein_g, etc.
public static function findOrCreateSlot(User $user, Carbon $date, MealType $type): self;
```

**MealItem:** belongs to MealEntry

### Policies

- `MealEntryPolicy`, `MealItemPolicy` — access via meal's user_id

### Implementation tasks

- [ ] Migrations + enums + models
- [ ] Factories with items relationship
- [ ] `DailyNutritionSummary` value object (optional): aggregate items for user+date

## Acceptance criteria

- [ ] One meal slot per type per day per user
- [ ] Deleting meal deletes items
- [ ] `findOrCreateSlot` idempotent

## Test plan

`tests/Unit/MealEntryTest.php` — unique constraint, totals calculation

## Definition of done

- [ ] Schema migrated; models ready for diary UI
