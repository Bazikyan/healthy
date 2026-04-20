# Code quality (TypeScript, validation, errors)

## TypeScript
- Keep `strict: true` where possible.
- Avoid `any`; prefer `unknown` + validation/narrowing.

## Validation
- Validate all external inputs at the boundary:
  - backend: params/query/body validation on every route
  - frontend: user input validation in forms (zod + react-hook-form)

## Error handling
- Backend must return a consistent error shape:
  - `{ code, message, details? }`
- No leaking stack traces or internal DB errors to clients in production.

## Logging
- Log with context (request id/user id when available).
- Avoid logging secrets (tokens, passwords, full connection strings).
