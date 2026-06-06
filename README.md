<p align="center">
<img src="https://img.shields.io/badge/🏛️-VendorBridge-1a365d?style=for-the-badge&labelColor=1a365d" alt="VendorBridge" />
</p>

<h1 align="center">VendorBridge — Workflow-Driven Procurement & Vendor Management ERP</h1>

<p align="center">
<strong>Enforce procurement integrity: RFQs → Quotations → Approvals → Purchase Orders → Invoices — auditable by design.</strong>
</p>

<p align="center">
<img src="https://img.shields.io/badge/Backend-NestJS-EE3A8C?style=flat-square&logo=nestjs&logoColor=white" alt="NestJS" />
<img src="https://img.shields.io/badge/Frontend-Next.js-000000?style=flat-square&logo=next.js&logoColor=white" alt="Next.js" />
<img src="https://img.shields.io/badge/DB-PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white" alt="Postgres" />
<img src="https://img.shields.io/badge/ORM-Prisma-2D3748?style=flat-square&logo=prisma&logoColor=white" alt="Prisma" />
<img src="https://img.shields.io/badge/Files-Cloudinary-3448c5?style=flat-square&logo=cloudinary&logoColor=white" alt="Cloudinary" />
</p>

---

## 📋 Table of Contents

<details>
<summary>Click to expand</summary>

- [🎯 Overview](#-overview)
- [✨ Key Features](#-key-features)
- [🏗️ Architecture At-a-Glance](#️-architecture-at-a-glance)
- [🔄 Data Flow Diagrams](#-data-flow-diagrams)
- [📦 Quick Start (Dev)](#-quick-start-dev)
- [📡 API Endpoints (Selected)](#-api-endpoints-selected)
- [🛠️ Tech Stack](#️-tech-stack)
- [🗂 Project Structure](#-project-structure)
- [🔐 Security & Compliance](#-security--compliance)
- [🧪 Testing & CI](#-testing--ci)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

</details>

---

## 🎯 Overview

VendorBridge models procurement as an explicit, validated workflow rather than ad-hoc CRUD forms. The system enforces business rules at each transition, provides immutable audit trails for compliance, and keeps vendor-scoped data strictly isolated.

High-level flow (simplified):

```mermaid
flowchart LR
	Vendor[Vendor]
	RFQ[RFQ]
	Quotation[Quotation]
	Approval[Approval]
	PO[Purchase Order]
	Invoice[Invoice]
	Audit[(Audit Log)]
	Reports[Reports & Analytics]

	Vendor --> RFQ --> Quotation --> Approval --> PO --> Invoice --> Audit --> Reports
```

Core principles

- Workflow first: every state transition is validated server-side.
- Traceability: immutable audit logs on every critical action.
- Ownership: vendor and company data segmented by role and tenant.
- Predictability: deterministic error codes for UI and automation.

---

## ✨ Key Features

- RFQ lifecycle: draft → publish → close/cancel with strict deadline enforcement.
- Vendor quotation submission with attachments (Cloudinary storage, DB metadata only).
- Approval workflows with required remarks on rejection and manager escalation.
- Purchase order generation and invoice lifecycle tied to approvals.
- Immutable audit log with DB triggers and app-level enforcement.
- Role-based access (Admin, Procurement Officer, Vendor, Manager).
- Reporting and dashboards read from transactional data (materialized views optional).
- Secure file uploads, checksum-based deduplication.
- Test coverage: unit, integration (Jest + Supertest), and e2e smoke flows.

---

## 🏗️ Architecture At-a-Glance

```mermaid
flowchart TB
	Users[Users: Admin / Officer / Manager / Vendor]
	UI[Next.js Frontend]
	API[NestJS API]
	DB[(PostgreSQL 16)]
	Cloud[(Cloudinary)]

	Users --> UI --> API
	API --> DB
	API --> Cloud
```

Key notes:

- Monolithic modular architecture (modular monolith) using NestJS.
- Prisma ORM for schema & migrations; PostgreSQL is the transactional store.
- Cloudinary stores binary content; DB stores metadata and publicIds.
- JWT RS256 for access tokens and rotated refresh tokens in httpOnly cookies.

---

## 🔄 Data Flow Diagrams

See [DFDs.md](DFDs.md) for Level 0–3 diagrams, event catalog, and implementation checklists.

Also refer to [SYSTEM_FLOW.md](SYSTEM_FLOW.md) for state machines and user journeys.

---

## 📦 Quick Start (Dev)

Prerequisites

- Node.js 20+ and pnpm
- Docker Desktop (for Postgres compose)

Backend (development)

```powershell
cd backend
pnpm install
docker compose up -d
pnpm prisma:generate
pnpm prisma:migrate:deploy
pnpm prisma:seed
pnpm start:dev
```

API base: `http://localhost:4000/api/v1`

Frontend (development)

```powershell
cd frontend
pnpm install
pnpm dev
```

UI: `http://localhost:3000`

Windows helper (starts both)

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run-all.ps1
```

---

## 📡 API Endpoints (Selected)

Authentication

```http
POST /api/v1/auth/register
POST /api/v1/auth/login
GET  /api/v1/auth/me
```

RFQs & Quotations

```http
POST   /api/v1/rfqs
POST   /api/v1/rfqs/:id/publish
GET    /api/v1/rfqs/:id
POST   /api/v1/quotations
GET    /api/v1/quotations?rfqId=...
```

Approvals & Orders

```http
POST   /api/v1/approvals
POST   /api/v1/purchase-orders
POST   /api/v1/invoices/:id/send
```

Files

```http
POST   /api/v1/files/upload
GET    /api/v1/files/:id
```

Error handling: responses include a machine-friendly `code` field (e.g., `BR-002`). See [DFDs.md](DFDs.md#appendix-common-error-codes-suggested).

---

## 🛠️ Tech Stack

- Frontend: Next.js (App Router), React, TypeScript, Tailwind CSS
- Backend: NestJS, TypeScript, Prisma ORM, PostgreSQL 16
- Auth: JWT (RS256), refresh tokens in httpOnly cookies
- Files: Cloudinary (external storage)
- Testing: Jest, Supertest
- Tooling: pnpm, Docker Compose, ESLint, Prettier

---

## 🗂 Project Structure

```text
backend/   # NestJS API, Prisma schema, migrations
frontend/  # Next.js app (App Router)
doc/       # Product and architecture docs
docs/      # Engineering docs (test plan, deployment)
scripts/   # Helper scripts (Windows)
```

Specific important files:

- `doc/ARCHITECTURE.md` — architecture reference
- `DFDs.md` — data flow diagrams (top-level)
- `SYSTEM_FLOW.md` — workflow state machines and sequences
- `backend/prisma/schema.prisma` — canonical data model

---

## 🔐 Security & Compliance

- Password hashing: Argon2
- Token signing: JWT RS256 with rotating refresh tokens
- Input validation: class-validator + Zod where applicable
- Audit logs: immutable, write-only via application + DB triggers
- File validation: MIME type, checksum, and size limits
- Secrets: store in environment variables and secrets manager in production

---

## 🧪 Testing & CI

Run backend tests:

```bash
cd backend
pnpm test
```

Integration & e2e: use a test database (Docker) and run smoke tests that cover complete flows: create RFQ → submit quotation → approve → generate PO → invoice.

CI pipelines should include linting, unit tests, and a lightweight integration check against a disposable DB container.

---

## 🤝 Contributing

Please follow the contribution workflow:

1. Fork the repository
2. Create a branch: `git checkout -b feature/awesome`
3. Run tests and linters locally
4. Open a PR describing changes and linking docs

Code style: ESLint + Prettier enforced via pre-commit hooks.

---

## 📄 License

This project is licensed under the MIT License — see the `LICENSE` file for details.

---

If you'd like, I can now:

- commit this README and push to `Arya` (existing branch), or
- create a new branch for the README change, commit, and push, or
- regenerate a README specifically tailored to a release or presentation (shorter executive summary).

Which option do you prefer? 

---

## ✅ Expanded Reference (Added Details)

The sections below extend the quick start with concrete config examples, API payload samples, event catalog pointers, and a PR checklist to make contributor onboarding frictionless.

### Badges (CI / Coverage / License)

Add these badges to the top of the file when CI is enabled:

```md
![CI](https://img.shields.io/github/actions/workflow/status/yourorg/yourrepo/ci.yml?branch=Arya)
![Coverage](https://img.shields.io/codecov/c/gh/yourorg/yourrepo)
![License](https://img.shields.io/badge/License-MIT-blue)
```

### Environment examples

Backend `.env` (complete example)

```env
# Database
DATABASE_URL="postgresql://vendorbridge:secret@localhost:5432/vendorbridgedb?schema=public"

# JWT (RS256)
JWT_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----"
JWT_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----\n...\n-----END PUBLIC KEY-----"
ACCESS_TOKEN_EXPIRY=15m
REFRESH_TOKEN_EXPIRY=30d

# Cloudinary
CLOUDINARY_CLOUD_NAME=example
CLOUDINARY_API_KEY=123456789
CLOUDINARY_API_SECRET=abcdef

# App
PORT=4000
NODE_ENV=development
```

Frontend `.env` (Vite)

```env
VITE_API_BASE_URL=http://localhost:4000/api/v1
```

AI POC `.env`

```env
HUGGINGFACE_HUB_API_TOKEN=your-token
MODEL_NAME=google/flan-t5-small
AI_SERVICE_PORT=8001
```

### Docker Compose (Postgres snippet)

```yaml
version: '3.8'
services:
	postgres:
		image: postgres:16
		environment:
			POSTGRES_USER: vendorbridge
			POSTGRES_PASSWORD: secret
			POSTGRES_DB: vendorbridgedb
		ports:
			- 5432:5432
		volumes:
			- pgdata:/var/lib/postgresql/data

volumes:
	pgdata:
```

### Sample API payloads

Create RFQ (POST /api/v1/rfqs)

```json
{
	"title": "Office Stationery Q3",
	"description": "Request for quotes for office supplies",
	"deadline": "2026-07-15T12:00:00Z",
	"vendors": ["vendorCompanyId1", "vendorCompanyId2"],
	"attachments": ["fileId1", "fileId2"]
}
```

Submit Quotation (POST /api/v1/quotations)

```json
{
	"rfqId": "rfq-uuid",
	"vendorCompanyId": "vendorCompanyId1",
	"items": [ { "desc": "A4 paper", "qty": 10, "unitPrice": 50 } ],
	"total": 500,
	"notes": "Delivery in 3 working days"
}
```

### Event / Audit catalog (core events)

The application emits structured audit events for each business-significant action. Use the following event names in logs and downstream integrations:

- `RFQ_CREATED` — payload: `{ rfqId, actorId, vendorCount }`
- `RFQ_PUBLISHED` — payload: `{ rfqId, publishedAt }`
- `QUOTATION_SUBMITTED` — payload: `{ quotationId, rfqId, vendorCompanyId }`
- `APPROVAL_CREATED` — payload: `{ approvalId, caseId, decision }`
- `PO_GENERATED` — payload: `{ poId, approvalId, total }`
- `INVOICE_CREATED` — payload: `{ invoiceId, poId, amount }`

Audit entries must include: `event`, `actorId`, `actorRole`, `resourceId`, `before`, `after`, `metadata`, `requestId`, `timestamp`.

### Error codes (recommended)

Return a machine-friendly error structure: `{ code: string, message: string, details?: any }`.

Examples:

- `BR-001` RFQ_MISSING_VENDOR — 400
- `BR-002` RFQ_DEADLINE_PASSED — 409
- `BR-003` OWNERSHIP_DENIED — 403
- `BR-004` INVALID_STATE_TRANSITION — 409

### PR checklist (required for docs & code PRs)

- [ ] Branch from `main` or `Arya` as appropriate
- [ ] Run linting and formatting: `pnpm lint` / `pnpm format`
- [ ] Run unit tests for changed modules
- [ ] Add or update documentation (`doc/` or top-level MD)
- [ ] Ensure migrations are included for schema changes
- [ ] Add integration test or explain why not applicable

### Troubleshooting

- DB connection errors: verify `DATABASE_URL` and that Docker Compose is running.
- File upload failures: check Cloudinary credentials and upload size limits.
- Authentication errors: verify JWT keys and clock skew between services.

### CI / CD notes

Recommend a GitHub Actions workflow that runs on PRs:

- `pnpm install`
- `pnpm lint`
- `pnpm test` (unit)
- lightweight integration: spin up Postgres container, run smoke e2e

### Maintainers

- Primary: Arya (you) — owner
- Backend: Backend Team / maintainer@example.com
- Frontend: Frontend Team / frontend@example.com

---

If this looks good I will commit these extended changes and push to the `Arya` branch.


## Product vision

VendorBridge replaces fragmented procurement workflows with a single authoritative system that enforces business rules and records intent. The system treats procurement as a sequence of guarded transitions (not merely CRUD), ensuring correctness, auditability, and operational safety.

Primary users

- Procurement Officer — creates RFQs, assigns vendors, evaluates quotations, and initiates POs.
- Vendor — views assigned RFQs and submits quotations and attachments before deadlines.
- Manager / Approver — reviews procurement requests and either approves or rejects them with mandatory remarks on rejection.
- Admin — manages users, roles, vendor records, and system-level reporting access.

## Repository layout

The repository holds the backend API, frontend UI, and the canonical documents needed for implementation, review, and operations.

Top-level structure

- `backend/` — NestJS API (TypeScript) with Prisma ORM and PostgreSQL migrations.
- `frontend/` — Next.js App Router UI (React + TypeScript).
- `doc/` — Product, architecture, security, and module specifications (authoritative design docs).
- `docs/` — Engineering supporting documents (test plan, deployment notes).
- `scripts/` — Convenience scripts for local development on Windows.


## Recommended reading order

1. [ARCHITECTURE.md](ARCHITECTURE.md) — high-level architecture and components.
2. [SYSTEM_FLOW.md](SYSTEM_FLOW.md) — user journeys and state machines for procurement flows.
3. [DFDs.md](DFDs.md) — data flow diagrams, event catalog, and persistence model.
4. [features.md](features.md) — feature inventory and screen-to-role mapping.
5. [SECURITY.md](SECURITY.md) — security controls, threat model, and operational guidance.

For deeper reference, consult `doc/` for module-level specifications and `docs/` for engineering process artifacts.

## Quick start (development)

Prerequisites

- Node.js 20+ (LTS), pnpm
- Docker (for running PostgreSQL in compose)

Backend (development)

```powershell
cd backend
pnpm install
docker compose up -d
pnpm prisma:generate
pnpm prisma:migrate:deploy
pnpm prisma:seed
pnpm start:dev
```

API: `http://localhost:4000/api/v1`
Swagger: `http://localhost:4000/api/v1/docs`

Frontend (development)

```powershell
cd frontend
pnpm install
pnpm dev
```

UI: `http://localhost:3000`

Quick helper (Windows)

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run-all.ps1
```

This helper starts backend and frontend in separate windows and clears common ports.

## Architecture at a glance

```mermaid
flowchart TB
	Users[Users: Admin / Officer / Manager / Vendor]
	UI[Next.js Frontend]
	API[NestJS API]
	DB[(PostgreSQL 16)]
	Cloud[(Cloudinary)]

	Users --> UI --> API
	API --> DB
	API --> Cloud
```

## Source of truth

Code (the backend and schema) is the canonical source of truth for business rules. Documents describe intent, rationale, and examples; when a conflict exists, the implementation and migration history in `backend/prisma/migrations/` take precedence.

Primary artifacts

- `backend/prisma/schema.prisma` — authoritative data model
- `doc/` — design and module-level specs
- `docs/` — engineering and operations artifacts

## Stack overview

- Frontend: Next.js (App Router), React, TypeScript, Tailwind, shadcn/ui, React Hook Form, Zod, TanStack Query
- Backend: NestJS, TypeScript, Prisma ORM, PostgreSQL 16, JWT (RS256), argon2id password hashing
- Files: Cloudinary (uploads stored as external assets; DB stores metadata/pointers)
- Tooling: pnpm, Docker Compose, Jest, Supertest, ESLint, Prettier

## Delivery principles

- Workflow correctness over cosmetic improvements.
- Business rules implemented server-side; UI is a thin client.
- Immutable audit logs for compliance and traceability.
- Strict ownership and role-based access controls.
- Small, testable modules with clear responsibilities.

## Project boundaries

VendorBridge intentionally focuses on procurement workflows and avoids feature creep. It is not:

- a microservices-first architecture (v1 is a modular monolith)
- a full ERP for inventory/accounting
- a real-time websocket system in initial releases

Instead, VendorBridge provides a stable, auditable, and extensible procurement core.

---

If you want, I will commit these README changes and push them to a new branch `Arya` along with the repository markdown files. Confirm and I'll proceed (or I can proceed now). 
