# HEALTH-017 — Manual food entry

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P1 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want to add food that is not in Open Food Facts, so that I can still track homemade or restaurant meals.

## Summary

Manual entry form in add-food FAB: name, optional brand, quantity, calories, P/C/F; `source = manual`.

## Technical approach

### UI

Third FAB option: “Manual entry” → form modal (no search step)

Fields:

- `name` (required)
- `brand` (optional)
- `quantity` + `quantity_unit` (default serving)
- `calories`, `protein_g`, `carbs_g`, `fat_g` (required, ≥ 0)

### Validation

`StoreManualFoodRequest` — same as store item with `source=manual`, no barcode required

### Implementation tasks

- [ ] Blade partial `manual-food-form.blade.php`
- [ ] Wire to existing `storeItem` endpoint
- [ ] Client-side optional macro sum hint (not enforced)

## Acceptance criteria

- [ ] Manual item saved with `source = manual`
- [ ] Appears in meal list with correct totals
- [ ] Negative macros rejected

## Test plan

Feature test: POST manual item, assert DB `source` enum

## Definition of done

- [ ] Manual path reachable from diary FAB
