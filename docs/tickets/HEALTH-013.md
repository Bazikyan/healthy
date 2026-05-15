# HEALTH-013 — OpenFoodFactsClient + fixture tests

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | — |
| **Blocks** | HEALTH-014, HEALTH-015, HEALTH-016, HEALTH-020 |

## User story

As a developer, I want a reliable client for Open Food Facts, so that food search and barcode lookup work consistently.

## Summary

HTTP service wrapping OFF API v2 with DTO mapping, timeouts, and unit tests using recorded JSON fixtures.

## Technical approach

### DTO: `App\Data\FoodProductData`

```php
readonly class FoodProductData {
    public function __construct(
        public string $name,
        public ?string $brand,
        public ?string $barcode,
        public ?string $offProductId,
        public ?float $caloriesPer100g,
        public ?float $proteinPer100g,
        public ?float $carbsPer100g,
        public ?float $fatPer100g,
        public ?float $servingSizeG,
    ) {}
    public function scaleToGrams(float $grams): ScaledNutrition;
}
```

### Client: `App\Services\OpenFoodFactsClient`

| Method | URL |
|--------|-----|
| `getByBarcode(string $barcode)` | `GET https://world.openfoodfacts.org/api/v2/product/{barcode}.json` |
| `search(string $query, int $page = 1)` | `GET https://world.openfoodfacts.org/cgi/search.pl?search_terms=...&json=1` or v2 search |

**Config** `config/openfoodfacts.php`:

```php
return ['base_url' => env('OFF_BASE_URL', 'https://world.openfoodfacts.org'), 'timeout' => 10];
```

### Nutrient mapping

| OFF field | Internal |
|-----------|----------|
| `product.nutriments.energy-kcal_100g` | caloriesPer100g |
| `product.nutriments.proteins_100g` | proteinPer100g |
| `product.nutriments.carbohydrates_100g` | carbsPer100g |
| `product.nutriments.fat_100g` | fatPer100g |
| `product.serving_quantity` | servingSizeG |

Return `null` if `status !== 1` or missing energy.

### Fixtures

- `tests/Fixtures/off/product-found.json`
- `tests/Fixtures/off/product-not-found.json`
- `tests/Fixtures/off/search-results.json`

Use `Http::fake()` in tests.

### Implementation tasks

- [ ] Client + DTO + ScaledNutrition helper
- [ ] Config file
- [ ] Bind interface in service provider for testing
- [ ] Unit tests with Http::fake

## Acceptance criteria

- [ ] Valid barcode fixture → FoodProductData with macros
- [ ] 404 fixture → null
- [ ] Search returns array of DTOs (max 20)
- [ ] Timeout throws catchable exception → logged, null returned to UI
- [ ] User-Agent header set per OFF guidelines: `HealthyApp/1.0 (contact@example.com)`

## Edge cases

- Products with kJ only — convert if needed
- `energy-kcal_100g` missing but `energy_100g` present
- Non-food products — filter in search results

## Definition of done

- [ ] Unit tests pass without network
