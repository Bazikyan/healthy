# HEALTH-005 — Profile settings page

| Field | Value |
|-------|-------|
| **Epic** | E1 — Foundation |
| **Priority** | P1 |
| **Estimate** | S (~3–4h) |
| **Status** | backlog |
| **Spec** | [01-foundation.md](../specs/01-foundation.md) |
| **Depends on** | HEALTH-004 |
| **Blocks** | — |

## User story

As a user, I want to update my goals and body metrics after onboarding, so that my calorie targets stay accurate as my plan changes.

## Summary

Settings page at `/settings/profile` to edit profile fields, optional macro split overrides, and trigger target recalculation. Reuse validation from onboarding.

## Technical approach

### Controller: `ProfileSettingsController`

- `edit()` — form with current `UserProfile` values + display current targets
- `update(UpdateProfileRequest $request)` — save, `recalculateTargets()`, flash success

### Routes

```php
Route::get('/settings/profile', ...)->name('settings.profile');
Route::patch('/settings/profile', ...)->name('settings.profile.update');
```

### View: `resources/views/settings/profile.blade.php`

Sections:

1. Body metrics (sex, DOB, height, starting weight — note: changing weight here updates profile baseline, not daily log)
2. Activity & goal (same fields as onboarding)
3. Macro split (optional advanced: 3 number inputs summing to 100)
4. Read-only summary: current calorie_target, P/C/F grams

### `UpdateProfileRequest`

- Same rules as onboarding
- Optional: `macro_split_*` must sum to 100

### Implementation tasks

- [ ] Controller + form request
- [ ] Blade form with `@error` display
- [ ] Link from bottom nav “Profile” tab
- [ ] Breeze account settings (`/profile` Breeze route) — either merge or link “Password & security” to Breeze's profile page

## Acceptance criteria

- [ ] PATCH updates DB and recalculates targets
- [ ] Changing goal from maintain → lose requires target_weight and weekly_rate
- [ ] Success message shown after save
- [ ] Breeze password update / delete account still accessible

## Test plan

| Test | File |
|------|------|
| `test_user_can_update_profile` | `tests/Feature/ProfileSettingsTest.php` |
| `test_update_recalculates_calorie_target` | same |
| `test_user_cannot_update_another_users_profile` | same (404 on wrong user — N/A if only own form) |

## Edge cases & notes

- Clarify UX copy: “Starting weight” vs weight log — avoid user confusion
- If macro splits edited, recalculate macro grams only (calories unchanged) OR full recalc — **spec: full recalc from calorie_target**

## Definition of done

- [ ] Settings reachable from Profile tab
- [ ] Feature test for recalculation on goal change
