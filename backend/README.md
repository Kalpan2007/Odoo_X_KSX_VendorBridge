# VendorBridge Backend

The backend is the source of truth for authentication, role enforcement, procurement workflow state machines, audit logging, notifications, and report data.

See the top-level docs for the system view:

- [ARCHITECTURE.md](../ARCHITECTURE.md)
- [SYSTEM_FLOW.md](../SYSTEM_FLOW.md)
- [SECURITY.md](../SECURITY.md)

## Stack

- Node.js 20+
- NestJS 10
- Prisma 5
- PostgreSQL 16
- JWT RS256, argon2id, class-validator, nestjs-pino, Cloudinary, pdfkit

## Folder layout

```text
backend/
├─ prisma/                   schema, migrations, seed
├─ database/init/            database bootstrap SQL
├─ scripts/                  key generation, audit verification, health checks
├─ src/
│  ├─ common/                guards, pipes, filters, interceptors, utils
│  ├─ config/                typed env loader
│  ├─ health/                DB health endpoint
│  ├─ modules/               auth, users, vendors, rfq, quotations, approvals,
│  │                         purchase-orders, invoices, notifications, audit-logs,
│  │                         reports, files
│  └─ prisma/                Prisma service module
└─ docker-compose.yml        local PostgreSQL
```

## First-time setup

```powershell
cd backend
pnpm install
docker compose up -d
copy .env.example .env
pnpm keygen
pnpm prisma:migrate:deploy
pnpm prisma:seed
pnpm start:dev
```

API base: `http://localhost:4000/api/v1`

Swagger UI: `http://localhost:4000/api/v1/docs`

## Workflow integrity

The service layer owns state transitions and runs them inside Prisma transactions. The business change, audit log entry, and notifications must be written together so the workflow cannot become inconsistent.

Primary state chains:

```text
RFQ: Draft -> Published -> Closed | Cancelled
Quotation: Submitted -> Shortlisted -> Accepted | Rejected
Approval: Pending -> Approved | Rejected
Purchase Order: Generated -> Sent -> Delivered
Invoice: Pending -> Paid | Overdue
```

## Audit log immutability

Audit logs are protected by three layers:

1. The app exposes append/query-only audit services.
2. The database migration installs a trigger that blocks update, delete, and truncate operations.
3. The runtime DB role does not have write privileges on the audit table.

Verify it with:

```powershell
pnpm verify:audit
```

## Runtime conventions

- Base route: `/api/v1`
- Responses use a consistent JSON envelope.
- Pagination is required on list endpoints.
- Ownership checks happen in the service layer for vendor-scoped data.
- File metadata lives in PostgreSQL; the file itself lives in Cloudinary.

## Quality gates

- `pnpm typecheck`
- `pnpm build`
- `pnpm test`
- `pnpm test:e2e`

## Deployment note

The backend is designed to run as a single NestJS service behind a reverse proxy. The database user used by the runtime should be read-restricted; migrations should use a separate elevated role.
