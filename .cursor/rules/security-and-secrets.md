# Security + secrets

## Secrets
- Never commit `.env`, `.env.local`, or credential files.
- Only commit `.env.example` templates.
- Do not print secrets in logs.

## Auth & session tokens (when implemented)
- Store secrets in environment variables (JWT secret, cookie secret, etc.).
- Use httpOnly cookies for refresh/session where possible.
- Enforce CORS intentionally (explicit origins in dev/prod).

## Data safety
- Treat user health/diet data as sensitive:
  - minimize data collected
  - avoid exporting/debug endpoints in production
