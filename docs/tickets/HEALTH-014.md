# HEALTH-014 — Food search API routes (auth + throttle)

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P0 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-013 |
| **Blocks** | HEALTH-015, HEALTH-016 |

## User story

As a logged-in user, I want to search foods from the diary without full page reloads, so that adding meals feels fast.

## Summary

Authenticated JSON endpoints for search and barcode lookup, throttled to protect OFF and server.

## Technical approach

### Controller: `Api\FoodSearchController`

| Method | Route | Response |
|--------|-------|----------|
| GET | `/api/foods/search` | `{ data: FoodProductData[] }` |
| GET | `/api/foods/barcode/{barcode}` | `{ data: FoodProductData \| null }` |

**Query params:**

- `q` — required for search, min 2 chars, max 100
- `page` — optional, default 1

### Throttle

```php
Route::middleware(['auth', 'throttle:30,1'])->prefix('api/foods')->group(...);
```

### JSON shape

```json
{
  "data": [{
    "name": "Greek yogurt",
    "brand": "Fage",
    "barcode": "123",
    "calories_per_100g": 97,
    "protein_per_100g": 9,
    "carbs_per_100g": 4,
    "fat_per_100g": 5,
    "serving_size_g": 150
  }]
}
```

### Implementation tasks

- [ ] Controller + invokable or thin methods
- [ ] Form request validation for `q`, barcode digits only
- [ ] Map DTO to array via API Resource optional
- [ ] 401 for guests
- [ ] 422 for invalid query
- [ ] 429 when throttled

## Acceptance criteria

- [ ] Search `?q=yogurt` returns JSON array
- [ ] Invalid barcode format → 422
- [ ] 31st request in minute → 429
- [ ] No OFF credentials exposed to client

## Test plan

`tests/Feature/FoodSearchApiTest.php` with Http::fake on OFF

## Definition of done

- [ ] Feature tests pass; usable from Alpine fetch in HEALTH-015
