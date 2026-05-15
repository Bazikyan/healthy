# HEALTH-015 — Diary page and add food (search flow)

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P0 |
| **Estimate** | L (~3–5 days) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-012, HEALTH-014, HEALTH-007 |
| **Blocks** | HEALTH-016, HEALTH-017, HEALTH-018, HEALTH-019, HEALTH-021 |

## User story

As a user, I want to log what I eat today organized by meal, so that I can track calories and macros against my daily target.

## Summary

Main diary UI at `/diary` with date picker, four meal sections, food search modal (Alpine + fetch), quantity step, and daily summary bars.

## Technical approach

### Controller: `DiaryController`

| Method | Route | Purpose |
|--------|-------|---------|
| index | GET `/diary?date=YYYY-MM-DD` | Show day view |
| storeItem | POST `/diary/meals/{meal}/items` | Add item |
| destroyItem | DELETE `/diary/items/{item}` | Remove |

### Store item request

```php
'name', 'brand' => nullable,
'quantity', 'quantity_unit' => in:g,ml,serving,
'calories', 'protein_g', 'carbs_g', 'fat_g',
'barcode', 'off_product_id' => nullable,
'source' => in:open_food_facts,manual,
```

Server recalculates macros from OFF DTO if client sends per-100g + quantity (trust server over client for OFF).

### View structure

`resources/views/diary/index.blade.php`:

- Header: date picker (`<input type="date">` submits GET)
- **Daily summary component** — 4 progress bars vs profile targets
- **Meal sections** — foreach MealType, list items, subtotal, “Add food” button
- **Alpine modal `addFoodModal`** — steps: `search` → `quantity` → `confirm`

### Search flow (Alpine)

```js
async search() {
  const res = await fetch(`/api/foods/search?q=${encodeURIComponent(this.q)}`, {
    headers: { 'Accept': 'application/json', 'X-CSRF-TOKEN': ... }
  });
}
```

Debounce 300ms, min 2 chars.

### Quantity step

- Input: grams (default) or servings if `serving_size_g` known
- Live preview calories/macros
- POST to `storeItem`

### Service: `App\Services\DiaryService`

```php
public function daySummary(User $user, Carbon $date): DaySummaryDto;
public function addItem(MealEntry $meal, array $data): MealItem;
```

### Implementation tasks

- [ ] DiaryController + DiaryService
- [ ] Blade + Alpine components: `daily-summary`, `meal-section`, `food-search-modal`
- [ ] CSRF for fetch POST/DELETE
- [ ] Meal auto-create via `findOrCreateSlot` on add
- [ ] Empty states per meal
- [ ] Flash on add/delete

## Acceptance criteria

- [ ] Switch date shows correct day's meals
- [ ] Search → select → quantity → item appears under meal
- [ ] Daily totals update (server-rendered refresh or Turbo)
- [ ] Delete item updates totals
- [ ] Macros stored on `meal_items` row (denormalized)
- [ ] Only own meals accessible

## Test plan

`tests/Feature/DiaryTest.php`:

- view diary, add item, delete item, totals match sum
- wrong user meal → 404

## Edge cases

- Timezone: “today” = user profile timezone
- Meal with 0 items — hide subtotal or show 0
- Very long food names — truncate display

## Definition of done

- [ ] Core diary loop works on mobile
- [ ] Feature tests for add/delete/totals
