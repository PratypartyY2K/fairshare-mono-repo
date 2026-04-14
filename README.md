# Fairshare

Fairshare is an explainable expense ledger for groups, built around transparency, determinism, and auditability.

Most expense-sharing apps stop at balances. Fairshare is designed to answer the harder questions: why a balance exists, which events produced it, and how it changed over time. The project treats shared expenses as a ledgered system rather than a lightweight calculator.

## What Makes It Different

- Explainable balances: users can inspect the expenses and transfers that contribute to a net position.
- Auditability: expense lifecycle changes and confirmed transfers are preserved as events.
- Deterministic accounting: money values use explicit precision and stable rounding behavior.
- Idempotent operations: expense creation and settlement confirmation are safe to retry without duplicate side effects.

## Core Concepts

### Ledger-First Accounting

Balances are derived from recorded state transitions, not stored as opaque totals. Expenses, updates, voids, and confirmed transfers all contribute to the current ledger.

### Explainability

Each balance can be broken down into its contributing expenses, participant shares, and transfers. The system is intended to make outcomes inspectable, not just correct.

### Event History

Mutations produce auditable history such as `ExpenseCreated`, `ExpenseUpdated`, `ExpenseVoided`, and `TransferConfirmed`. That keeps the model useful for debugging, trust, and future analytics.

### Deterministic Money Handling

Amounts are normalized to fixed precision, rounding behavior is explicit, and leftover cents are distributed predictably. The goal is to avoid hidden drift and inconsistent recomputation.

## Repository Structure

This repository contains:

```text
fairshare-mono-repo/
├── fairshare-backend/   Spring Boot service for groups, expenses, ledgers, and settlements
└── fairshare-frontend/  Next.js app for group management, ledger views, and history
```

Additional project docs:

- Backend setup and API notes: [fairshare-backend/README.md](/Users/pratyushkumar/Desktop/Pratyush/faireshare-mono-repo/fairshare-backend/README.md)
- Frontend setup and local workflow: [fairshare-frontend/README.md](/Users/pratyushkumar/Desktop/Pratyush/faireshare-mono-repo/fairshare-frontend/README.md)

## Stack

- Backend: Java 21, Spring Boot 3.5.7, Maven, Spring Data JPA, Flyway, PostgreSQL, H2 for tests
- Frontend: Next.js 16.1.6, React 19, TypeScript 5, Tailwind CSS 4

## Getting Started

### 1. Create the local database

```bash
createdb fairshare
psql -c "CREATE USER fairshare_user WITH PASSWORD 'fairshare_pass';"
psql -c "GRANT ALL PRIVILEGES ON DATABASE fairshare TO fairshare_user;"
```

### 2. Start the backend

```bash
cd fairshare-backend
mvn spring-boot:run
```

The backend runs on `http://localhost:8080`. Swagger UI is available at `http://localhost:8080/swagger`.

### 3. Start the frontend

```bash
cd fairshare-frontend
npm install
printf "NEXT_PUBLIC_API_BASE_URL=http://localhost:8080\n" > .env.local
npm run dev
```

The frontend runs on `http://localhost:3000`.

If you prefer to proxy requests through Next.js rewrites instead of calling the backend directly from the browser, use:

```bash
NEXT_PUBLIC_API_BASE_URL=http://localhost:3000/api
BACKEND_URL=http://localhost:8080
```

## Product Scope

### Groups And Members

- Create groups
- Add members
- Rename groups
- Browse groups with filtering, sorting, and pagination

### Expenses

- Create, edit, and delete expenses
- Support equal, exact amount, percentage, and share-based split modes
- Persist split details with deterministic rounding behavior

### Settlements And Ledger

- Compute suggested settlements from the current ledger
- Confirm settlements with idempotency support
- View current balances and owes relationships

### Explainability And History

- Inspect per-user ledger explanations
- View confirmed transfer history
- View expense event history and lifecycle changes

## Authentication Status

The backend supports header-based actor identity via `X-User-Id`. In local development, auth is currently optional so the main flows are easy to run without extra setup.

Production-style user authentication is not implemented yet. Planned direction includes passwordless login, invite-based onboarding, and guest-to-user account claiming.

## Design Principles

- Transparency over convenience
- Determinism over heuristics
- Auditability over hidden mutation
- Clarity over black-box optimization

## Verification

Backend test suite:

```bash
cd fairshare-backend
./mvnw test
```

Frontend checks:

```bash
cd fairshare-frontend
npm run lint
npm run build -- --webpack
```

## Notes

The repository still includes `.gitmodules` because the project was originally split across separate repos. The codebase is organized and documented as a single portfolio project, but the submodule metadata has not been removed yet.
