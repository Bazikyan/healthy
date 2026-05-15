# HEALTH-010 — Weight log CRUD and upsert-by-day

| Field | Value |
|-------|-------|
| **Epic** | E3 — Weight tracking |
| **Priority** | P0 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [03-weight-tracking.md](../specs/03-weight-tracking.md) |
| **Depends on** | HEALTH-009, HEALTH-007 |
| **Blocks** | HEALTH-011, HEALTH-021, HEALTH-022 |

## User story

As a user, I want to log my weight for any day and change it if I made a mistake, so that my history stays accurate.

## Summary

`/weight` page with create form, recent entries list, edit/delete. POST for existing `logged_at` updates row (upsert).

## Technical approach

### Controller: `WeightController`

| Method | Route | Behavior |
|--------|-------|----------|
| index | GET `/weight` | Form + paginated list (30) |
| store | POST `/weight` | `updateOrCreate(['user_id','logged_at'], [...])` |
| edit | GET `/weight/{entry}/edit` | Edit form |
| update | PATCH `/weight/{entry}` | Authorize, update |
| destroy | DELETE `/weight/{entry}` | Authorize, delete |

### Form requests

**StoreWeightRequest:**

- `logged_at`: required, date, before_or_equal:today (optional: allow future — default deny)
- `weight_kg`: required, numeric, between:30,300
- `note`: nullable, string, max:500

### Upsert logic

```php
WeightEntry::updateOrCreate(
    ['user_id' => auth()->id(), 'logged_at' => $request->date('logged_at')],
    ['weight_kg' => $request->weight_kg, 'note' => $request->note]
);
```

### View: `resources/views/weight/index.blade.php`

- Quick form: date (default today), weight input (kg), note
- Table: date, weight, note, actions (edit/delete)
- Use `layouts/app` + bottom nav

### Implementation tasks

- [ ] Controller + requests + routes (middleware: auth, onboarding)
- [ ] Policy on edit/update/delete
- [ ] Blade views: index, edit
- [ ] Flash messages on success
- [ ] Return 404 for other user's entry ID

## Acceptance criteria

- [ ] Log weight today → appears in list
- [ ] Log again same day → updates, not duplicate
- [ ] Edit changes weight and note
- [ ] Delete removes entry
- [ ] Guest → redirect login
- [ ] User A cannot edit User B entry (404)

## Test plan

`tests/Feature/WeightEntryTest.php`:

- create, upsert same day, update, delete, unauthorized 404

## Edge cases & notes

- Display kg with one decimal; input step 0.1
- Optional: imperial display v2 — store kg only
- On first onboarding, optional seed today's weight from `starting_weight_kg`

## Definition of done

- [ ] Feature tests pass; page usable on mobile
