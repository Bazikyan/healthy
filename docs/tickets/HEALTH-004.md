# HEALTH-004 — Onboarding wizard

| Field | Value |
|-------|-------|
| **Epic** | E1 — Foundation |
| **Priority** | P0 |
| **Estimate** | M (~1–2 days) |
| **Status** | backlog |
| **Spec** | [01-foundation.md](../specs/01-foundation.md) |
| **Depends on** | HEALTH-002, HEALTH-003 |
| **Blocks** | HEALTH-021 |

## User story

As a new user, I want a guided setup after registration, so that the app knows my goals before I use tracking features.

## Summary

Three-step onboarding wizard with session or query param step tracking. Middleware blocks dashboard/diary/weight until `onboarding_completed_at` is set.

## Technical approach

### Middleware: `EnsureOnboardingComplete`

- If `auth()->user()->profile?->onboarding_completed_at` is null → redirect `route('onboarding.show')`
- Exclude: onboarding routes, logout, profile delete
- Register on route group in `bootstrap/app.php` or `RouteServiceProvider`

### Controller: `OnboardingController`

| Method | Action |
|--------|--------|
| `show(Request $request)` | Render current step (1–3) |
| `store(StoreOnboardingRequest $request)` | Validate all steps, create/update profile, recalculate targets, set `onboarding_completed_at`, redirect dashboard |

### Alternative: multi-request flow

Store step 1–2 in session; final POST commits. Simpler v1: **single form with 3 Blade partials** and Alpine `x-show` for steps, one POST at end.

### Form requests

- `OnboardingStepRequest` or single `StoreOnboardingRequest`:
  - `sex`: required, in:male,female
  - `date_of_birth`: required, date, before:today, after:1900-01-01
  - `height_cm`: required, numeric, min:100, max:250
  - `starting_weight_kg`: required, numeric, min:30, max:300
  - `activity_level`: required, enum
  - `goal_type`: required, in:lose,maintain,gain
  - `target_weight_kg`: required_if:goal_type,lose,gain
  - `weekly_rate_kg`: required_if:goal_type,lose,gain; numeric; min:0.1; max:1.0 (lose) or 0.5 (gain)

### Views

- `resources/views/onboarding/show.blade.php`
- Steps: `_step-about`, `_step-goal`, `_step-review`
- Review step displays read-only TDEE, calories, macros from server-side preview (POST step 2 to preview OR compute in `show` when session has step 1–2 data)

### Routes

```php
Route::middleware(['auth'])->group(function () {
    Route::get('/onboarding', [OnboardingController::class, 'show'])->name('onboarding.show');
    Route::post('/onboarding', [OnboardingController::class, 'store'])->name('onboarding.store');
});
```

### Implementation tasks

- [ ] Create middleware + register alias `onboarding`
- [ ] Create controller + form request
- [ ] Build 3-step UI with progress indicator
- [ ] On store: `UserProfile::updateOrCreate(['user_id' => $id], [...])` + `recalculateTargets()` + `onboarding_completed_at = now()`
- [ ] Apply middleware to `dashboard`, `diary`, `weight`, `settings` routes (add as routes are created)

## Acceptance criteria

- [ ] New registrant hitting `/dashboard` → redirected to `/onboarding`
- [ ] Completing onboarding → lands on `/dashboard` with profile populated
- [ ] Review step shows calorie_target and macro grams
- [ ] Invalid `weekly_rate_kg` for maintain goal ignored/nullable
- [ ] Returning user with completed onboarding never sees wizard

## Test plan

| Test | File |
|------|------|
| `test_guest_cannot_access_onboarding` | `tests/Feature/OnboardingTest.php` |
| `test_incomplete_user_redirected_from_dashboard` | same |
| `test_complete_onboarding_creates_profile` | same |
| `test_validation_rejects_future_dob` | same |

## Edge cases & notes

- User registers but closes browser mid-onboarding → profile incomplete, middleware catches on next login
- Detect browser timezone via JS optional field `timezone` on final submit
- Use `starting_weight_kg` as initial weight; HEALTH-010 may auto-create first `weight_entry` for today (optional nice-to-have)

## QA checklist (manual)

- [ ] Register → auto redirect onboarding
- [ ] Step back/forward preserves input (Alpine or session)
- [ ] Submit with missing target weight on “lose” shows validation error

## Definition of done

- [ ] Feature tests pass
- [ ] Middleware applied to all authenticated app routes except onboarding
