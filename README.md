# Patho NXGen Platform - Foundational Scaffold

Multi-tenant SaaS platform for pathology laboratories. This is the
foundational backend + frontend scaffold generated from the Business
Model Documentation and Enterprise Architecture Guide.

## Structure

```
patho-nxgen/
├── backend/                Spring Boot 3.5 / Java 21 modular monolith
│   └── src/main/java/com/pathonxgen/
│       ├── config/          Security, JPA auditing, OpenAPI, Redis/RabbitMQ/MinIO beans
│       ├── core/
│       │   ├── tenant/      Schema-per-tenant routing (TenantContext, TenantFilter, TenantRoutingDataSource)
│       │   ├── security/    Keycloak JWT -> Spring Security role mapping
│       │   └── exception/   Global exception handler
│       ├── common/          BaseEntity, ApiResponse, PageResponse
│       └── modules/         One package per business capability:
│           patient, laboratory (sample lifecycle), billing, inventory,
│           crm, finance, reporting, notification, scheduler,
│           integration, auth, tenantadmin (platform control plane)
│   └── src/main/resources/
│       ├── application.yml            Base config (env-var driven)
│       ├── application-{dev,prod}.yml
│       └── db/migration/
│           ├── public/                 Flyway migrations for the shared public schema
│           └── tenant/                 Template applied per-tenant at provisioning time
│
├── frontend/                React 19 / TypeScript / Vite
│   └── src/
│       ├── app/              Router, TanStack Query client
│       ├── core/
│       │   ├── auth/         Keycloak integration (realm-per-tenant)
│       │   ├── tenant/       Tenant context accessor
│       │   ├── api/          Axios client with JWT interceptor
│       │   └── routing/      ProtectedRoute
│       ├── shared/           Layout, shared components/hooks/utils
│       ├── features/         One folder per module (patient is fully implemented as the reference pattern)
│       ├── store/            Zustand: auth + UI state only (no server data)
│       └── services/, types/
│
├── infra/nginx/nginx.conf   Reverse proxy routing /api -> backend, / -> frontend
└── docker-compose.yml        Postgres, Redis, RabbitMQ, MinIO, Keycloak, backend, frontend, nginx
```

## What is fully implemented (reference pattern to copy for other modules)

- **Patient module** (backend): entity, repository, service, controller, DTOs, validation, RBAC.
- **Sample lifecycle** (backend): explicit state-machine service enforcing the
  Collection -> Barcode -> Dispatch -> Processing -> QC -> Verification ->
  Approval -> Report Delivery flow, publishing domain events to RabbitMQ.
- **Tenant provisioning** (backend): saga skeleton with TODOs marking where
  Keycloak realm creation, per-tenant Flyway migration, and event
  publication plug in.
- **Patient module** (frontend): types, Axios API layer, TanStack Query
  hooks, AG Grid list page, React Hook Form + Zod register form.
- **Auth**: Keycloak realm-per-tenant login, JWT attached via Axios
  interceptor, role-based route/button gating.

## What is stubbed (same shape, less depth)

Billing, Inventory, CRM, Finance, Reporting, Notification, Scheduler,
Integration modules have working entity/repository/service/controller
skeletons on the backend but thin business logic - and placeholder
folders on the frontend - ready to be filled in module by module using
the Patient/Sample pattern above.

## Running locally

```bash
docker compose up -d postgres redis rabbitmq minio keycloak
cd backend && ./mvnw spring-boot:run
cd frontend && npm install && npm run dev
```

Keycloak needs a `patho-<tenant>` realm and a `patho-nxgen-frontend`
public client configured with the JWT claim mapper for `tenant` and
`realm_access.roles` before end-to-end login works.

## Notes on decisions carried over from the architecture guide

- **Tenant isolation**: schema-per-tenant (Postgres `tenant_<slug>` schemas),
  per the Enterprise Architecture Guide - routed via
  `TenantRoutingDataSource` + Hibernate's `SCHEMA` multi-tenancy strategy,
  not row-level security.
- **Modular monolith**, not microservices - each module is a Java package
  under `modules/`, not a separate deployable.
- **MRN / invoice number generation** currently uses an in-memory counter
  as a placeholder; the tenant-schema migration already creates
  `mrn_seq` / `invoice_seq` Postgres sequences to replace it before
  production use (see comments in `PatientService` / `InvoiceService`).
