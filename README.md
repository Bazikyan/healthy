# Healthy — Health & Diet Tracker (Monorepo)

Mobile-first web app to:
- Log meals
- Estimate calories
- Track daily eating habits
- Detect unhealthy patterns
- Suggest better habits
- View progress over time

## Repo structure

- `frontend/` — Next.js (mobile-first UI)
- `backend/` — Fastify API (Node.js)
- `docs/` — documentation (optional)
- `scripts/` — dev scripts (optional)

## Stack (decided)

- **Frontend**: Next.js + Tailwind CSS + shadcn/ui
- **Backend**: Fastify (TypeScript)
- **DB**: MySQL
- **ORM / Migrations**: Prisma
- **API contract**: OpenAPI (backend emits spec, frontend generates types/client)
- **Monorepo tooling**: npm workspaces (single `package-lock.json` at repo root)

## Prereqs

- Node: `v18.16.0`
- npm: `9.5.1`
- MySQL: 8+

## Setup (npm workspaces)

1. Install dependencies:

```bash
npm install
```

2. Create backend env file:

- Copy `backend/.env.example` → `backend/.env`
- Set `DATABASE_URL` for MySQL

3. Create the database and run migrations:

```bash
npm run -w backend prisma:migrate
```

4. Start backend (in one terminal):

```bash
npm run -w backend dev
```

5. Start frontend (in another terminal):

```bash
npm run -w frontend dev
```

## Docs

- Frontend: `frontend/README.md`
- Backend: `backend/README.md`
