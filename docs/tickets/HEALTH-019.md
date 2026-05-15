# HEALTH-019 — Copy yesterday's meals

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P2 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want to copy yesterday's meals to today, so that I don't re-enter the same breakfast when I eat the same thing.

## Summary

Button on diary page duplicates all `meal_items` from previous calendar day into today, creating new `meal_entries` and items.

## Technical approach

### Action: `DiaryController@copyYesterday`

```php
Route::post('/diary/copy-yesterday', ...)->name('diary.copy-yesterday');
```

### Service: `DiaryService::copyDay(User $user, Carbon $from, Carbon $to)`

1. Load all meal_entries + items for `$from`
2. If `$to` already has any meal_items → **abort with 409** or return confirmation flag (product decision: **confirm dialog** — if items exist, show “Today already has entries. Replace?” with `?force=1`)

3. For each source meal:
   - `findOrCreateSlot($user, $to, $mealType)`
   - Replicate each item (new IDs, same macros)

### UI

- Button: “Copy yesterday” near date picker
- Alpine confirm if today non-empty

### Implementation tasks

- [ ] Service method + controller
- [ ] Button + confirm modal
- [ ] Flash: “Copied X items from {date}”

## Acceptance criteria

- [ ] Empty today → copies all meals/items
- [ ] Today has data → requires confirmation
- [ ] Source day empty → message “Nothing to copy”
- [ ] New items independent (delete copy doesn't affect yesterday)

## Test plan

`tests/Feature/CopyYesterdayTest.php`

## Definition of done

- [ ] Feature test for happy path and empty source
