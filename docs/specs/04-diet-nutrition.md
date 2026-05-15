# Spec 04 — Diet & Nutrition (Open Food Facts)

## Objective

Log meals with food search and barcode lookup; track daily calories and macros vs profile targets.

## Data models

### `meal_entries`

| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| user_id | bigint FK | |
| date | date | index with user_id |
| meal_type | enum | breakfast, lunch, dinner, snack |
| timestamps | | |

**Constraint:** `unique(user_id, date, meal_type)` — one slot per meal per day

### `meal_items`

| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| meal_entry_id | bigint FK | cascade delete |
| name | string | |
| brand | string nullable | |
| quantity | decimal(8,2) | e.g. 150 |
| quantity_unit | string | g, ml, serving |
| calories | decimal(8,2) | for logged quantity |
| protein_g | decimal(8,2) | |
| carbs_g | decimal(8,2) | |
| fat_g | decimal(8,2) | |
| off_product_id | string nullable | |
| barcode | string nullable | |
| source | enum | open_food_facts, manual |
| timestamps | | |

### `saved_foods`

| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| user_id | bigint FK | |
| name, brand, barcode | | |
| default_quantity, quantity_unit | | |
| calories, protein_g, carbs_g, fat_g | | per default serving |
| timestamps | | |

### `food_search_cache` (optional)

| Column | Type | Notes |
|--------|------|-------|
| cache_key | string unique | hash of query or barcode |
| response_json | json | |
| expires_at | timestamp | TTL 24h |

## `OpenFoodFactsClient`

**Location:** `App\Services\OpenFoodFactsClient`

| Method | OFF endpoint | Returns |
|--------|--------------|---------|
| `getByBarcode(string $barcode)` | `GET /api/v2/product/{barcode}` | DTO or null |
| `search(string $query, int $page = 1)` | search API | Collection of DTOs |

**DTO fields:** name, brand, barcode, calories_per_100g, protein_per_100g, carbs_per_100g, fat_per_100g, serving_size_g (if available)

**Behavior:**

- HTTP timeout 10s
- On 404 / incomplete nutriments: return null + user message
- Map OFF nutriments keys (`energy-kcal_100g`, `proteins_100g`, etc.)

### Scaling to logged quantity

```php
$factor = $quantityG / 100;
$calories = $caloriesPer100g * $factor;
```

If user selects “1 serving” and OFF provides serving size, use that.

## Internal JSON routes (for Alpine)

| Method | URI | Purpose |
|--------|-----|---------|
| GET | /api/foods/search?q= | Search OFF (auth) |
| GET | /api/foods/barcode/{code} | Product by barcode |
| POST | /diary/meals/{meal}/items | Add item |
| DELETE | /diary/items/{item} | Remove item |

## UI: Diary page (`/diary`)

- Date picker (default today)
- Sections per meal_type with items list and subtotals
- **FAB / + menu:** Search food | Scan barcode | Manual entry
- **Search modal:** debounced search, select product → quantity step → add
- **Barcode:** camera (HTTPS) + manual input fallback
- **Manual form:** name, calories, P/C/F, quantity
- **Daily summary bar:** calories and macros vs targets (progress bars)
- **Copy yesterday** button: duplicate previous day’s meal_items to today (new meal_entries)

## Saved foods

- “Save to favorites” on item detail
- Favorites list in add-food flow for one-tap add

## Acceptance criteria

- Search returns results from OFF; selecting adds item with correct scaled macros
- Barcode scan adds product or shows not found
- Manual entry works when OFF has no data
- Daily totals = sum of all meal_items for date
- Denormalized macros on meal_items unchanged if OFF updates later
- Rate limit: max 30 search requests per minute per user (middleware or throttle)

## Tests

- `OpenFoodFactsClientTest`: mock HTTP, parse sample JSON fixtures
- `MealItemTest`: add item updates daily totals
- `FoodSearchTest`: throttle, auth required
- `CopyYesterdayTest`: copies structure, not same IDs
