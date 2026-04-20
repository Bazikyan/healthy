# Backend (Fastify + Prisma + MySQL)

## What it is

Fastify API providing (v1 goals):
- meals CRUD (log meals + entries)
- daily aggregates for charts
- insights endpoint for unhealthy pattern detection + suggestions

## Why Fastify

Fastify is a Node.js web framework (similar role to Express) designed for:
- performance
- a clean **plugin architecture**
- strong TypeScript support
- structured APIs that stay maintainable as the app grows

What that means in practice:
- Routes are registered as plugins (easy feature modules)
- Cross-cutting concerns live in plugins/hooks (auth, logging, db, config)
- Request handling is fast and predictable

## Why Prisma (with MySQL)

Prisma provides:
- a schema file (`prisma/schema.prisma`) that describes your MySQL models
- migrations to evolve the DB safely over time
- a generated, type-safe client you use in code (e.g. `prisma.meal.create(...)`)

Typical workflow:
1. Edit `prisma/schema.prisma`
2. Run a migration
3. Use Prisma Client in services/repos

Prisma is a great fit here because the data model evolves quickly (foods, tags, goals, insights).

## Node / npm compatibility

- Node `v18.16.0`: compatible with Fastify + Prisma
- npm `9.5.1`: fine for npm workspaces

## Environment variables

Create `backend/.env` (copy from `backend/.env.example`):

```bash
DATABASE_URL="mysql://USER:PASSWORD@HOST:3306/DB_NAME"
PORT=3001
NODE_ENV=development
```

## Prisma (npm scripts)

From repo root (recommended with workspaces):

```bash
npm run -w backend prisma:generate
npm run -w backend prisma:migrate
npm run -w backend prisma:studio
```

## Run locally (npm workspaces)

From repo root:

```bash
npm run -w backend dev
```

## Suggested backend structure

- `src/server.ts` (bootstrap)
- `src/plugins/` (prisma, config, auth)
- `src/routes/` (HTTP routes)
- `src/services/` (business logic)
- `src/repos/` (DB access via Prisma)

## API contract sharing

This repo uses **OpenAPI** for frontend/backend contract sharing:
- backend emits an OpenAPI spec (source of truth)
- frontend generates the API client/types from that spec
