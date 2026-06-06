# Security

VendorBridge handles procurement records, vendor identity data, and approval decisions. Security is therefore a product requirement, not an afterthought.

## Security principles

- Backend enforcement is authoritative.
- Fail closed when there is uncertainty.
- Apply least privilege everywhere.
- Validate all input on the server.
- Keep audit records immutable.
- Never trust the client for authorization decisions.

## Authentication

- JWT access tokens are short-lived.
- Refresh tokens should be rotated and stored as secure httpOnly cookies.
- Passwords should be hashed with argon2id.
- Sign-in, refresh, and logout flows must be tracked.

## Authorization

- Route guards enforce coarse access control.
- Service-layer checks enforce ownership and workflow validity.
- Frontend role checks are only for UX, never for trust.
- Vendor users must only access their own records.

## Data protection

- Store only file metadata in PostgreSQL.
- Keep uploaded file content in Cloudinary.
- Do not log passwords, tokens, or sensitive tax data.
- Protect audit records from update and delete operations.

## Threat model

| Threat | Control |
|---|---|
| Credential stuffing | rate limit and failed-login cooldowns |
| Token theft | short-lived JWT access tokens and secure refresh cookies |
| Privilege escalation | RBAC plus ownership checks |
| IDOR | service-layer ownership filters |
| SQL injection | Prisma parameterized queries |
| XSS | React escaping and safe rendering patterns |
| CSRF | same-site cookies and explicit mutation requests |
| File abuse | type and size validation before upload |
| Audit tampering | immutable log controls in app and database |
| Replay of approval actions | workflow state checks and idempotency controls |

## API hardening

- Use a global validation pipe for request body and query validation.
- Reject unknown properties where possible.
- Keep API errors structured and predictable.
- Apply rate limiting to authentication endpoints.
- Expose only the minimum necessary headers and methods.

## Logging rules

- Use structured logs.
- Include request IDs for traceability.
- Log important business events, not secrets.
- Keep logs useful for audits without exposing credentials.

## Operational expectations

- Secrets live in environment variables or hosting secrets.
- Development and production environments should not share signing keys.
- Database migration roles should be separated from runtime roles.
- Audit verification should be part of release checks.

## What is intentionally out of scope for v1

- MFA
- SSO
- IP allowlists
- Hardware keys
- Field-level encryption for every sensitive value
- Full session management UI

These can be added later without changing the core workflow model.