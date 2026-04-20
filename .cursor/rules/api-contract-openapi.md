# API contract (OpenAPI + Orval)

## Contract source of truth
- The **backend** is the source of truth for request/response shapes.
- The backend must **emit an OpenAPI spec** (JSON).
- The **frontend must generate** API types/client from that OpenAPI spec using **Orval**.

## Rules
- Do **not** hand-write duplicate DTO types in `frontend/` and `backend/`.
  - If a temporary manual type is unavoidable, add a TODO to remove it once Orval generation is wired.
- Every new backend endpoint must define:
  - **request validation** schema (params/query/body as applicable)
  - **response schema(s)** for success + errors
  - a **stable error shape**, e.g. `{ code: string, message: string, details?: unknown }`

## Fastify best practice
- Use `@fastify/swagger` + `@fastify/swagger-ui` to expose and export OpenAPI.
- Prefer schema-first routes (route `schema` object is required for inclusion in OpenAPI output).

## Orval best practice
- Generated code is an **artifact**: commit policy is optional, but it must be reproducible from the spec.
- Generated code should live under `frontend/src/lib/api/generated/` (or similar) and should not be edited by hand.
