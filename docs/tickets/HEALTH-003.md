# HEALTH-003 — NutritionCalculator service

| Field | Value |
|-------|-------|
| **Epic** | E1 — Foundation |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [01-foundation.md](../specs/01-foundation.md) |
| **Depends on** | HEALTH-002 |
| **Blocks** | HEALTH-004, HEALTH-005, HEALTH-021 |

## User story

As a user, I want calorie and macro targets calculated from my body data and goal, so that I know how much to eat each day without manual math.

## Summary

Pure PHP service implementing Mifflin–St Jeor BMR, TDEE with activity multipliers, calorie adjustment for lose/gain, and macro gram targets from percentage split.

## Technical approach

### Class: `App\Support\NutritionCalculator`

```php
final class NutritionCalculator
{
    public function bmr(Sex $sex, float $weightKg, float $heightCm, int $ageYears): float;
    public function tdee(float $bmr, ActivityLevel $level): float;
    public function calorieTarget(float $tdee, GoalType $goal, ?float $weeklyRateKg, float $bmr): int;
    public function macros(int $calories, int $proteinPct, int $carbsPct, int $fatPct): MacroTargets;
    public function ageFromDateOfBirth(Carbon $dob): int;
}
```

### DTO: `App\Support\MacroTargets`

```php
readonly class MacroTargets {
    public function __construct(
        public int $calories,
        public int $proteinG,
        public int $carbsG,
        public int $fatG,
    ) {}
}
```

### Formulas (from spec)

**BMR (Mifflin–St Jeor):**

- Male: `10×W + 6.25×H − 5×age + 5`
- Female: `10×W + 6.25×H − 5×age − 161`

**Activity multipliers:** 1.2, 1.375, 1.55, 1.725, 1.9

**Calorie adjustment:**

- maintain → TDEE
- lose → TDEE − (`weekly_rate_kg × 7700 / 7`)
- gain → TDEE + (`weekly_rate_kg × 7700 / 7`)

**Safety floor (lose only):** `max(calculated, bmr × 1.1)`

**Macros:**

- protein_g = round(calories × protein% / 100 / 4)
- carbs_g = round(calories × carbs% / 100 / 4)
- fat_g = round(calories × fat% / 100 / 9)

### `UserProfile::recalculateTargets()`

```php
public function recalculateTargets(NutritionCalculator $calc): void
{
    $age = $calc->ageFromDateOfBirth($this->date_of_birth);
    $bmr = $calc->bmr($this->sex, $this->starting_weight_kg, $this->height_cm, $age);
    $tdee = $calc->tdee($bmr, $this->activity_level);
    $this->calorie_target = $calc->calorieTarget($tdee, $this->goal_type, $this->weekly_rate_kg, $bmr);
    $macros = $calc->macros($this->calorie_target, $this->macro_split_protein, ...);
    $this->protein_target_g = $macros->proteinG;
    // ...
    $this->save();
}
```

### Implementation tasks

- [ ] Create `NutritionCalculator` + `MacroTargets`
- [ ] Wire into `UserProfile::recalculateTargets()`
- [ ] Register as singleton in `AppServiceProvider` if needed
- [ ] Unit tests with fixed inputs and expected outputs

## Acceptance criteria

- [ ] Male 30y, 80kg, 180cm, sedentary → BMR ~1780 (±1 due to rounding)
- [ ] Lose 0.5 kg/week reduces calories by ~550/day from TDEE
- [ ] Lose goal never sets calories below BMR × 1.1
- [ ] Maintain ignores `weekly_rate_kg`
- [ ] Macro split 30/40/30 sums to ~100% of calories (rounding tolerance ±5 kcal)

## Test plan

| Test case | Expected behavior |
|-----------|-------------------|
| Female vs male same stats | Female BMR lower |
| Each activity level | TDEE increases monotonically |
| Gain with 0.25 kg/week | Calories above TDEE |
| Extreme lose rate 1.0 kg/week | Clamped to floor |
| Macros 30/40/30 on 2000 kcal | ~150g P, 200g C, 67g F |

File: `tests/Unit/NutritionCalculatorTest.php`

## Edge cases & notes

- Use **starting_weight_kg** for BMR at onboarding; later consider latest weight (v2)
- `weekly_rate_kg` required when goal is lose/gain; validation in Form Request (HEALTH-004)
- Round macro grams to integers for display; store as int on profile

## Definition of done

- [ ] Unit tests cover all goal types and floor clamp
- [ ] `recalculateTargets()` persists to DB
