# Health PWA — Ticket Index

Each ticket lives in its own file with full implementation detail.

**Legend:** P0 = blocker · P1 = MVP · P2 = post-MVP · Estimate: S (≤4h) · M (1–2d) · L (3–5d)

## Epic E1 — Foundation

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-001 | Install Laravel Breeze (Blade) | P0 | S | [HEALTH-001.md](./HEALTH-001.md) |
| HEALTH-002 | user_profiles migration and model | P0 | S | [HEALTH-002.md](./HEALTH-002.md) |
| HEALTH-003 | NutritionCalculator service | P0 | M | [HEALTH-003.md](./HEALTH-003.md) |
| HEALTH-004 | Onboarding wizard | P0 | M | [HEALTH-004.md](./HEALTH-004.md) |
| HEALTH-005 | Profile settings page | P1 | S | [HEALTH-005.md](./HEALTH-005.md) |

## Epic E2 — PWA shell

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-006 | vite-plugin-pwa and manifest | P0 | M | [HEALTH-006.md](./HEALTH-006.md) |
| HEALTH-007 | App layout and bottom navigation | P0 | M | [HEALTH-007.md](./HEALTH-007.md) |
| HEALTH-008 | Offline fallback and SW caching | P1 | M | [HEALTH-008.md](./HEALTH-008.md) |

## Epic E3 — Weight tracking

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-009 | weight_entries migration and model | P0 | S | [HEALTH-009.md](./HEALTH-009.md) |
| HEALTH-010 | Weight log CRUD and upsert-by-day | P0 | M | [HEALTH-010.md](./HEALTH-010.md) |
| HEALTH-011 | Weight chart and stats | P1 | M | [HEALTH-011.md](./HEALTH-011.md) |

## Epic E4 — Diet & nutrition

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-012 | Meal migrations and models | P0 | S | [HEALTH-012.md](./HEALTH-012.md) |
| HEALTH-013 | OpenFoodFactsClient + tests | P0 | M | [HEALTH-013.md](./HEALTH-013.md) |
| HEALTH-014 | Food search API routes | P0 | S | [HEALTH-014.md](./HEALTH-014.md) |
| HEALTH-015 | Diary page and search flow | P0 | L | [HEALTH-015.md](./HEALTH-015.md) |
| HEALTH-016 | Barcode scan UI | P1 | M | [HEALTH-016.md](./HEALTH-016.md) |
| HEALTH-017 | Manual food entry | P1 | S | [HEALTH-017.md](./HEALTH-017.md) |
| HEALTH-018 | Saved foods (favorites) | P2 | M | [HEALTH-018.md](./HEALTH-018.md) |
| HEALTH-019 | Copy yesterday's meals | P2 | S | [HEALTH-019.md](./HEALTH-019.md) |
| HEALTH-020 | food_search_cache | P2 | S | [HEALTH-020.md](./HEALTH-020.md) |

## Epic E5 — Dashboard, history, export

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-021 | Dashboard page | P0 | M | [HEALTH-021.md](./HEALTH-021.md) |
| HEALTH-022 | History list and day detail | P1 | M | [HEALTH-022.md](./HEALTH-022.md) |
| HEALTH-023 | CSV data export | P2 | S | [HEALTH-023.md](./HEALTH-023.md) |
| HEALTH-024 | Offline POST error handling | P2 | S | [HEALTH-024.md](./HEALTH-024.md) |

## Epic E6 — Quality & launch

| ID | Title | Priority | Est. | File |
|----|-------|----------|------|------|
| HEALTH-025 | Feature test suite | P0 | M | [HEALTH-025.md](./HEALTH-025.md) |
| HEALTH-026 | README and environment setup | P1 | S | [HEALTH-026.md](./HEALTH-026.md) |
| HEALTH-027 | Medical disclaimer footer | P1 | S | [HEALTH-027.md](./HEALTH-027.md) |

## Sprint plan

| Sprint | Tickets |
|--------|---------|
| 1 | 001 → 002 → 003 → 004 → 006 → 007 |
| 2 | 009 → 010 → 011 → 012 → 013 → 014 |
| 3 | 015 → 016 → 017 → 021 |
| 4 | 005, 008, 018–020, 022–027 |

**MVP:** through 021 + 025 + 026 + 027 · **Post-MVP:** 018–020, 022–024
