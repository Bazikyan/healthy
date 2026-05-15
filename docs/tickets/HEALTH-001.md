# HEALTH-001 — Install Laravel Breeze (Blade)

| Field | Value |
|-------|-------|
| **Epic** | E1 — Foundation |
| **Priority** | P0 |
| **Estimate** | S (~2–4h) |
| **Status** | backlog |
| **Spec** | [01-foundation.md](../specs/01-foundation.md) |
| **Depends on** | — |
| **Blocks** | HEALTH-002, HEALTH-006, HEALTH-007, HEALTH-026 |

## User story

As a new user, I want to register and log in securely, so that my health data is private to my account.

## Summary

Install Laravel Breeze with the Blade stack (not Livewire/Inertia). This provides authentication views, Tailwind styling, Alpine.js, and session-based auth aligned with the server-rendered PWA approach.

## Technical approach

### Commands

```bash
composer require laravel/breeze --dev
php artisan breeze:install blade
npm install && npm run build
php artisan migrate
```

### Routes expected after install

| Method | URI | Purpose |
|--------|-----|---------|
| GET/POST | `/register` | Registration |
| GET/POST | `/login` | Login |
| POST | `/logout` | Logout |
| GET/POST | `/forgot-password` | Reset request |
| GET/POST | `/reset-password/{token}` | Reset form |
| GET | `/dashboard` | Post-login landing (customize later) |

Email verification: **skip for v1** unless explicitly enabled in Breeze install options.

### Files created by Breeze (reference)

- `app/Http/Controllers/Auth/*`
- `resources/views/auth/*`
- `resources/views/layouts/guest.blade.php`, `navigation.blade.php`
- `routes/auth.php` (included from `web.php`)
- `tests/Feature/Auth/*`

### Customization in this ticket

- [ ] Redirect authenticated users from `/` to `/dashboard`
- [ ] Redirect guests from `/dashboard` to `/login`
- [ ] Keep default Breeze styling for now (HEALTH-007 will extend layout)

### Implementation tasks

- [ ] Run Breeze install with Blade stack
- [ ] Commit `composer.json`, `package.json`, lock files, new views/controllers
- [ ] Verify SQLite works: `touch database/database.sqlite`, set `DB_CONNECTION=sqlite` in `.env`
- [ ] Run `php artisan test` — Breeze auth tests should pass
- [ ] Run `npm run build` without errors

## Acceptance criteria

- [ ] User can register with name, email, password
- [ ] User can log in and log out
- [ ] Password reset flow sends mail (or logs to `storage/logs` in local with `MAIL_MAILER=log`)
- [ ] `@auth` / `@guest` directives work in Blade
- [ ] No duplicate auth implementations (Fortify-only without views is out of scope)

## Test plan

| Test | Type | Notes |
|------|------|-------|
| Breeze `RegistrationTest` | feature | Ships with Breeze |
| Breeze `AuthenticationTest` | feature | Login/logout |
| Breeze `PasswordResetTest` | feature | Optional if mail configured |

## Edge cases & notes

- **PHP 8.2+** required per `composer.json`
- Do not install Inertia/React — conflicts with PWA Blade plan
- `composer run dev` script already exists; verify it starts server + Vite after Breeze

## QA checklist (manual)

- [ ] Register new user in browser
- [ ] Log out and log back in
- [ ] Request password reset; confirm notification or log entry

## Definition of done

- [ ] Breeze committed; CI/local `php artisan test` green
- [ ] `npm run build` succeeds
