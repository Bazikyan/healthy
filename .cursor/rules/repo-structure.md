# Repo structure (must follow)

## Top-level directories
- `frontend/`: Next.js UI only
- `backend/`: Fastify API only
- `docs/`: documentation only
- `scripts/`: developer scripts only

## Hard boundaries
- Do not import any code across `frontend/` and `backend/`.
- Communication between frontend and backend must be HTTP calls only.

## API contracts (OpenAPI)
- Backend is the source of truth for request/response schemas.
- Backend must generate an OpenAPI spec.
- Frontend must use generated client/types from the OpenAPI spec.
- Avoid duplicating DTO types by hand. If you must do it temporarily, leave a TODO to replace with generated types.

## Backend layering
- `backend/src/routes/**`: register routes
- `backend/src/controllers/**`: HTTP glue only (thin)
- `backend/src/services/**`: business logic
- `backend/src/repos/**`: Prisma DB access only
- `backend/src/schemas/**`: schema definitions used for validation + OpenAPI generation

Rules:
- Controllers may call services, never call repos directly.
- Services may call repos.
- Repos only talk to Prisma.

## Frontend layering
- `frontend/src/features/**`: feature modules (meals/insights/progress)
- `frontend/src/components/**`: reusable UI components
- `frontend/src/lib/api/**`: API client (generated) + helpers

Rules:
- Feature modules use `src/lib/api/**` for server calls.
- UI components stay presentation-focused.

## Secrets
- Never commit `.env`, `.env.local`, or any credential files.
- Maintain `.env.example` in each app.
