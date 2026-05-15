# HEALTH-009 — weight_entries migration and model

| Field | Value |
|-------|-------|
| **Epic** | E3 — Weight tracking |
| **Priority** | P0 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [03-weight-tracking.md](../specs/03-weight-tracking.md) |
| **Depends on** | HEALTH-002 |
| **Blocks** | HEALTH-010, HEALTH-011, HEALTH-021, HEALTH-022, HEALTH-023 |

## User story

As a user, I want my daily weight stored in the database, so that I can track trends over time.

## Summary

Create `weight_entries` table, model, factory, policy, and user relationship.

## Technical approach

### Migration

```php
Schema::create('weight_entries', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->cascadeOnDelete();
    $table->date('logged_at');
    $table->decimal('weight_kg', 5, 2);
    $table->string('note', 500)->nullable();
    $table->timestamps();
    $table->unique(['user_id', 'logged_at']);
    $table->index(['user_id', 'logged_at']);
});
```

### Model: `App\Models\WeightEntry`

- `fillable`: logged_at, weight_kg, note
- Casts: `logged_at` → date, `weight_kg` → decimal:2
- `belongsTo(User::class)`

### User relationship

```php
public function weightEntries(): HasMany
{
    return $this->hasMany(WeightEntry::class)->orderByDesc('logged_at');
}
```

### Policy: `WeightEntryPolicy`

- `view`, `update`, `delete`: `$entry->user_id === $user->id`
- Register in `AuthServiceProvider`

### Factory

- Random weight 60–120 kg, `logged_at` → today minus random days

### Implementation tasks

- [ ] Migration + model + factory
- [ ] Policy + `WeightEntryPolicy` registration
- [ ] `WeightEntry::scopeForUser($query, User $user)`

## Acceptance criteria

- [ ] Unique constraint prevents two entries same user same day
- [ ] Cascade delete with user
- [ ] Policy denies other users (tested in HEALTH-010)

## Test plan

`tests/Unit/WeightEntryModelTest.php` — factory, relationship, unique constraint violation

## Definition of done

- [ ] Migration run; model + policy + factory ready for HEALTH-010
