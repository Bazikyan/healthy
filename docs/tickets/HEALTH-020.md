# HEALTH-020 — food_search_cache (optional)

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P2 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-013 |
| **Blocks** | — |

## User story

As the system, I want to cache Open Food Facts responses, so that repeated searches are faster and we reduce API load.

## Summary

DB table `food_search_cache` with hashed keys, JSON payload, 24h TTL. Wrap `OpenFoodFactsClient` with cache decorator or inline checks.

## Technical approach

### Migration

```php
$table->string('cache_key')->unique(); // sha256 of "search:{q}:{page}" or "barcode:{code}"
$table->json('response_json');
$table->timestamp('expires_at');
```

### Repository: `FoodSearchCacheRepository`

- `get(string $key): ?array`
- `put(string $key, array $data, int $ttlSeconds = 86400): void`
- Prune expired via scheduled command optional: `php artisan cache:prune-off` daily

### Integration

In `OpenFoodFactsClient` or `CachedOpenFoodFactsClient`:

```php
if ($cached = $this->cache->get($key)) return $this->map($cached);
$response = $this->http->get(...);
$this->cache->put($key, $response->json());
```

### Implementation tasks

- [ ] Migration + repository
- [ ] Wire into client
- [ ] Unit test: second call does not hit HTTP (Http::fake assert sent once)

## Acceptance criteria

- [ ] Identical search within 24h returns cached data
- [ ] Expired cache refreshes from OFF
- [ ] Barcode and search keys separate

## Definition of done

- [ ] Tests pass; no stale cache beyond TTL
