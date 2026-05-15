# HEALTH-018 — Saved foods (favorites)

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P2 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want to save foods I eat often, so that I can log them again with one tap.

## Summary

`saved_foods` table and UI to save from meal item or OFF result, list favorites in add-food flow, quick-add to selected meal.

## Technical approach

### Migration: `saved_foods`

| Column | Type |
|--------|------|
| user_id | FK |
| name, brand | string |
| barcode | nullable |
| default_quantity | decimal |
| quantity_unit | string |
| calories, protein_g, carbs_g, fat_g | decimals (per default serving) |
| off_product_id | nullable |

### Controller: `SavedFoodController`

| Method | Route |
|--------|-------|
| index | GET `/saved-foods` (JSON for modal or settings page) |
| store | POST `/saved-foods` |
| destroy | DELETE `/saved-foods/{savedFood}` |
| quickAdd | POST `/saved-foods/{savedFood}/add-to-meal` body: `meal_entry_id` |

### UI integration

- On meal item row: star icon “Save to favorites”
- Add-food modal tab: “Favorites” list
- Tap favorite → confirm quantity (default pre-filled) → POST quickAdd

### Implementation tasks

- [ ] Migration + model + policy
- [ ] Store from existing MealItem (copy macros)
- [ ] Favorites tab in add-food modal
- [ ] quickAdd creates new MealItem under target meal

## Acceptance criteria

- [ ] Save favorite from logged item
- [ ] Quick-add creates item with same macros
- [ ] User cannot access another user's favorites
- [ ] Delete favorite works

## Test plan

`tests/Feature/SavedFoodTest.php`

## Definition of done

- [ ] Favorites usable from diary without search
