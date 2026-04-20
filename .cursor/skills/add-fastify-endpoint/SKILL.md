# Skill: Add Fastify endpoint (OpenAPI-first)

## Goal
Add a new backend endpoint in `backend/` that is fully documented by OpenAPI and follows the repo layering rules.

## Inputs to provide
- HTTP method + path (e.g. `POST /meals`)
- Purpose / feature name (e.g. `meals`)
- Auth requirement (public / authenticated)
- Request shape (params/query/body)
- Response shape(s)
- Error cases (code + message)

## Must-follow rules
- Route handlers are thin.
- Validation and response schemas are defined on the route.
- Business logic goes in `backend/src/services/**`.
- DB access goes in `backend/src/repos/**` (Prisma only).
- Endpoint must appear in the OpenAPI spec.

## Output checklist
- New/updated files under:
  - `backend/src/routes/**`
  - `backend/src/controllers/**`
  - `backend/src/services/**`
  - `backend/src/repos/**` (only if DB is needed)
  - `backend/src/schemas/**` (if you keep schemas separate)
- OpenAPI schema includes:
  - `summary`, `tags`
  - request validation
  - response schemas for success + error
- Minimal tests if the repo has a test harness (optional).

