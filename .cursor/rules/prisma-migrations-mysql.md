# Prisma + MySQL migrations

## Environment / connection
- Use `DATABASE_URL` (MySQL) from `backend/.env`.
- Never hardcode credentials in code or commit real `.env` files.

## Development workflow
- Use `prisma migrate dev` for local development.
- Keep migrations committed to git (they are part of the app history).

## Production workflow
- Use `prisma migrate deploy` in production/CI.
- Do **not** run `migrate dev` against production databases.

## Safe schema changes
- Prefer additive, backward-compatible migrations when possible.
- For breaking changes (renames/type changes), use an expand-contract approach:
  - expand (add new column/table)
  - backfill
  - switch code
  - contract (drop old column) in a later migration
