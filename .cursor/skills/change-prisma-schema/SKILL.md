# Skill: Change Prisma schema safely (MySQL)

## Goal
Make a schema change in `backend/prisma/schema.prisma` and produce a safe migration workflow.

## Inputs to provide
- Models/fields to add/change/remove
- Whether the change is additive (safe) or breaking (requires expand/contract)
- Any backfill needed

## Must-follow rules
- Use `prisma migrate dev` in development.
- Use `prisma migrate deploy` in production/CI.
- Prefer additive migrations; for breaking changes, use expand-contract.
- Do not run destructive operations blindly; plan data migration steps.

## Output checklist
- Updated `backend/prisma/schema.prisma`
- New migration files (via Prisma migrate)
- Code updates in:
  - `backend/src/repos/**`
  - `backend/src/services/**`
  - affected API endpoints + schemas

