# Frontend (Next.js)

## What it is

Mobile-first Next.js app that:
- logs meals
- shows daily totals
- shows progress charts over time
- shows insights (unhealthy patterns + suggestions)

## Tech

- Next.js (App Router)
- Tailwind CSS + shadcn/ui
- react-hook-form + zod (forms & validation)
- recharts (progress charts)
- TanStack Query (recommended) for API fetching/caching

## Environment variables

Create `frontend/.env.local`:

```bash
NEXT_PUBLIC_API_BASE_URL=http://localhost:3001
```

## Run locally (npm workspaces)

From repo root:

```bash
npm run -w frontend dev
```

Or from `frontend/`:

```bash
npm run dev
```

## API contract

Frontend should not “guess” request/response shapes. This repo uses **OpenAPI**:
- backend emits an OpenAPI spec
- frontend generates the API client/types from that spec
