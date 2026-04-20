# Skill: Generate Orval client from backend OpenAPI

## Goal
Wire up and/or run Orval so the frontend uses generated API types/client from the backend OpenAPI spec.

## Inputs to provide
- Where the backend OpenAPI JSON is written (file path)
- Desired output path in frontend (e.g. `frontend/src/lib/api/generated/`)
- Client style: `fetch` or `react-query` (TanStack Query)
- Base URL strategy (runtime env var recommended)

## Must-follow rules
- Backend is the OpenAPI source of truth.
- Generated output is not edited by hand.
- Keep frontend imports confined to `frontend/src/lib/api/**`.

## Output checklist
- Orval config file (committed)
- `frontend/src/lib/api/generated/**` output
- A small wrapper that sets base URL from `NEXT_PUBLIC_API_BASE_URL` (if needed)
- npm scripts to regenerate client consistently

