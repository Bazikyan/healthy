# HEALTH-002 — user_profiles migration and model

| Field | Value |
|-------|-------|
| **Epic** | E1 — Foundation |
| **Priority** | P0 |
| **Estimate** | S (~3–4h) |
| **Status** | backlog |
| **Spec** | [01-foundation.md](../specs/01-foundation.md) |
| **Depends on** | HEALTH-001 |
| **Blocks** | HEALTH-003, HEALTH-004, HEALTH-005, HEALTH-009, HEALTH-012, HEALTH-021 |

## User story

As a user, I want my body metrics and goals stored in the database, so that the app can calculate daily nutrition targets.

## Summary

Create the `user_profiles` table (1:1 with `users`), Eloquent model, enum casts, factory, and `User::profile()` relationship.

## Technical approach

### Migration: `create_user_profiles_table`

```php
Schema::create('user_profiles', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->cascadeOnDelete();
    $table->string('sex'); // male, female
    $table->date('date_of_birth');
    $table->decimal('height_cm', 5, 2);
    $table->decimal('starting_weight_kg', 5, 2);
    $table->string('activity_level');
    $table->string('goal_type'); // lose, maintain, gain
    $table->decimal('target_weight_kg', 5, 2)->nullable();
    $table->decimal('weekly_rate_kg', 3, 2)->nullable();
    $table->unsignedInteger('calorie_target')->default(0);
    $table->unsignedInteger('protein_target_g')->default(0);
    $table->unsignedInteger('carbs_target_g')->default(0);
    $table->unsignedInteger('fat_target_g')->default(0);
    $table->unsignedTinyInteger('macro_split_protein')->default(30);
    $table->unsignedTinyInteger('macro_split_carbs')->default(40);
    $table->unsignedTinyInteger('macro_split_fat')->default(30);
    $table->timestamp('onboarding_completed_at')->nullable();
    $table->string('timezone')->default('UTC');
    $table->timestamps();
    $table->unique('user_id');
});
```

### Enums (PHP 8.1+ backed enums recommended)

- `App\Enums\Sex`: `Male`, `Female`
- `App\Enums\ActivityLevel`: `Sedentary`, `Light`, `Moderate`, `Active`, `VeryActive`
- `App\Enums\GoalType`: `Lose`, `Maintain`, `Gain`

### Model: `App\Models\UserProfile`

- `fillable` for all user-editable columns
- Casts: dates, decimals, enums
- `belongsTo(User::class)`
- Placeholder method `recalculateTargets(): void` (implemented in HEALTH-003)

### Model: `App\Models\User`

```php
public function profile(): HasOne
{
    return $this->hasOne(UserProfile::class);
}
```

### Factory: `UserProfileFactory`

- Sensible defaults (moderate activity, maintain goal, completed onboarding)
- State: `incomplete()` → `onboarding_completed_at = null`

### Implementation tasks

- [ ] Create migration and run `php artisan migrate`
- [ ] Create enums under `app/Enums/`
- [ ] Create `UserProfile` model + factory
- [ ] Update `User` model with `profile()` relationship
- [ ] Optional: create empty profile on user registration (defer to HEALTH-004 if preferred)

## Acceptance criteria

- [ ] Schema matches [spec 01](../specs/01-foundation.md) column list
- [ ] Deleting user cascades delete to profile
- [ ] Factory creates valid profile for use in tests
- [ ] `User::factory()->hasProfile()->create()` pattern works

## Test plan

| Test | Type | File |
|------|------|------|
| `test_user_has_one_profile` | unit | `tests/Unit/UserProfileTest.php` |
| `test_deleting_user_deletes_profile` | feature | same |

## Edge cases & notes

- Do **not** put profile fields on `users` table — keeps auth separate from health data
- `timezone` used later for “today” in diary; default `UTC` until user sets browser TZ in onboarding

## Definition of done

- [ ] Migration applied locally
- [ ] Model + factory + relationship tested
