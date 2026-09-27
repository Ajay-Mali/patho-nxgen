# Patho NXGen Platform - User Credentials & Environment Reference

This document provides credentials for local development accounts, Keycloak multi-tenant realms, and infrastructure services.

> [!NOTE]
> All tenant and platform user accounts share the default development password: **`Password123!`**

---

## 1. Keycloak Master Administration

- **Console URL**: `http://localhost:8081`
- **Admin Username**: `admin`
- **Admin Password**: `admin`

---

## 2. Platform Realm Accounts (`pathonxgen-platform`)

| Role | Username | Email | Password |
| :--- | :--- | :--- | :--- |
| **Super Admin** | `superadmin` | `superadmin@platform.com` | `Password123!` |
| **Platform Admin** | `platformadmin` | `admin@platform.com` | `Password123!` |
| **Support Engineer** | `supportengineer` | `support@platform.com` | `Password123!` |

---

## 3. Tenant Realm Accounts

### Demo Pathology Labs (`pathonxgen-tenant-demo`)
- **Tenant Admin**: `demo_admin` (`admin@demo-lab.com`) | Password: `Password123!`
- **Senior Pathologist**: `demo_pathologist` (`pathologist@demo-lab.com`) | Password: `Password123!`

### Acme Diagnostics (`pathonxgen-tenant-acme`)
- **Tenant Admin**: `acme_admin` (`admin@acme-diag.com`) | Password: `Password123!`
- **Lab Technician**: `acme_tech` (`tech@acme-diag.com`) | Password: `Password123!`

### Metro Care Diagnostics (`pathonxgen-tenant-metro`)
- **Tenant Admin**: `metro_admin` (`admin@metro-care.com`) | Password: `Password123!`
- **Consulting Doctor**: `metro_doctor` (`doctor@metro-care.com`) | Password: `Password123!`

---

## 4. Local Infrastructure Service Credentials

| Service | Port / URL | Database / Scope | Credentials |
| :--- | :--- | :--- | :--- |
| **PostgreSQL 16** | `localhost:5433` | Database: `patho_nxgen` | Username: `patho_app`<br>Password: `patho_pass` |
| **Keycloak OIDC** | `http://localhost:8081` | Realm: `pathonxgen-tenant-<slug>` | Admin: `admin` / `admin` |
| **RabbitMQ** | API: `localhost:5672`<br>Management UI: `http://localhost:15672` | Virtual Host: `/` | Username: `guest`<br>Password: `guest` |
| **MinIO (S3 Storage)** | API: `http://localhost:9000`<br>Console: `http://localhost:9001` | Bucket: `patho-nxgen-documents` | Username: `minioadmin`<br>Password: `minioadmin` |
| **Redis 7** | `localhost:6379` | Cache DB: `0` | No Password |
