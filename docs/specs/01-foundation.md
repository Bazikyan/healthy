# Spec 01 — Foundation (Auth, Profile, Goals)

## Objective

Authenticated users complete onboarding with body metrics and goals; system computes calorie and macro targets.

## Dependencies

- Fresh Laravel 12 app
- Laravel Breeze (Blade stack)

## Data model: `user_profiles`

| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| user_id | bigint FK unique | cascade delete |
| sex | enum: male, female | For BMR formula |
| date_of_birth | date | Age derived |
| height_cm | decimal(5,2) | > 0 |
| starting_weight_kg | decimal(5,2) | Set at onboarding |
| activity_level | enum | sedentary, light, moderate, active, very_active |
| goal_type | enum | lose, maintain, gain |
| target_weight_kg | decimal(5,2) nullable | Required if lose/gain |
| weekly_rate_kg | decimal(3,2) nullable | e.g. 0.5; cap 1.0 lose / 0.5 gain |
| calorie_target | int | Computed, stored |
| protein_target_g | int | Computed or overridden |
| carbs_target_g | int | Computed or overridden |
| fat_target_g | int | Computed or overridden |
| macro_split_protein | tinyint | % default 30 |
| macro_split_carbs | tinyint | % default 40 |
| macro_split_fat | tinyint | % default 30 |
| onboarding_completed_at | timestamp nullable | |
| timezone | string default UTC | |
| timestamps | | |

## `NutritionCalculator` service

**Location:** `App\Support\NutritionCalculator`

### BMR (Mifflin–St Jeor)

- Male: `10 * weight + 6.25 * height - 5 * age + 5`
- Female: `10 * weight + 6.25 * height - 5 * age - 161`

### Activity multipliers

| Level | Multiplier |
|-------|------------|
| sedentary | 1.2 |
| light | 1.375 |
| moderate | 1.55 |
| active | 1.725 |
| very_active | 1.9 |

`TDEE = BMR * multiplier`

### Calorie adjustment

| goal_type | Adjustment |
|-----------|------------|
| maintain | TDEE |
| lose | TDEE - (weekly_rate_kg * 7700 / 7) |
| gain | TDEE + (weekly_rate_kg * 7700 / 7) |

Clamp lose target: not below BMR * 1.1 (safety floor).

### Macros

From `calorie_target` and split percentages:

- protein_g = (calories * protein%) / 4
- carbs_g = (calories * carbs%) / 4
- fat_g = (calories * fat%) / 9

Recalculate on profile update; optional `recalculateTargets()` artisan command not required v1.

## Routes

| Method | URI | Name | Action |
|--------|-----|------|--------|
| GET | /onboarding | onboarding.show | Multi-step wizard (guest if incomplete) |
| POST | /onboarding | onboarding.store | Save profile, redirect dashboard |
| GET | /settings/profile | settings.profile | Edit profile |
| PATCH | /settings/profile | settings.profile.update | |
| GET | /settings/account | settings.account | Password, delete account |

## Onboarding wizard (3 steps)

1. **About you** — sex, DOB, height, current weight
2. **Activity & goal** — activity level, goal type, target weight, weekly rate (if lose/gain)
3. **Review** — show computed TDEE, calories, macros; confirm

Redirect to `/onboarding` if `onboarding_completed_at` is null (middleware `EnsureOnboardingComplete`).

## Acceptance criteria

- New user registers → forced through onboarding → lands on dashboard
- Profile update recalculates stored targets
- Invalid weekly_rate rejected (validation rules)
- User can delete account (Breeze or custom) and all related rows cascade

## Tests

- `NutritionCalculatorTest` (unit): BMR/TDEE/macro edge cases
- `OnboardingTest` (feature): complete flow, middleware redirect
- `ProfileUpdateTest`: changing goal recalculates targets
