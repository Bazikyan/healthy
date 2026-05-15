# HEALTH-026 — README and environment setup

| Field | Value |
|-------|-------|
| **Epic** | E6 — Quality & launch |
| **Priority** | P1 |
| **Estimate** | S (~2h) |
| **Status** | backlog |
| **Spec** | [00-overview.md](../specs/00-overview.md) |
| **Depends on** | HEALTH-001 |
| **Blocks** | — |

## User story

As a developer cloning the repo, I want clear setup instructions, so that I can run the app locally quickly.

## Summary

Replace default Laravel README with project-specific docs: requirements, install, dev server, PWA testing, env vars.

## Technical approach

### README sections

1. **About** — Health PWA, weight + nutrition tracking
2. **Requirements** — PHP 8.2, Composer, Node 20+, SQLite/MySQL
3. **Quick start**

```bash
composer install
cp .env.example .env
php artisan key:generate
touch database/database.sqlite
php artisan migrate
npm install
composer run dev
```

4. **Environment variables**

| Variable | Purpose |
|----------|---------|
| `APP_URL` | Must be HTTPS for PWA camera |
| `DB_*` | Database |
| `OFF_BASE_URL` | Open Food Facts base URL |

5. **Running tests** — `php artisan test`
6. **PWA** — build, install, offline QA link to `docs/qa/pwa-offline.md`
7. **Barcode / HTTPS** — ngrok or Valet secure note
8. **Disclaimer** — not medical advice

### `.env.example` updates

- Comment blocks for OFF, mail
- SQLite as default

### Implementation tasks

- [ ] Rewrite README.md
- [ ] Update `.env.example`
- [ ] Remove irrelevant Laravel marketing boilerplate or shorten

## Acceptance criteria

- [ ] New developer can boot app following README only
- [ ] `composer run dev` documented
- [ ] Medical disclaimer mentioned

## Definition of done

- [ ] README committed
