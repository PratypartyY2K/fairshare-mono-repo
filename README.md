# Fairshare

Fairshare is a group expense tracker built around a real ledger.

Most expense apps stop at "Alice owes Bob $12." Fairshare keeps the history that produced that number: who paid, how the split was computed, what got edited, and which transfers were confirmed later.

## What Makes It Different

- Explainable balances: a user's position can be traced back to expenses, shares, and confirmed transfers.
- Auditability: edits and voids are recorded as events instead of disappearing into the latest state.
- Deterministic accounting: amounts are rounded once, stored at scale 2, and leftover cents are assigned predictably.
- Idempotent operations: expense creation and settlement confirmation can be retried without double-applying writes.

## Core Concepts

### Ledger-First Accounting

Balances are derived from recorded changes, not stored as a free-floating total. Expenses, updates, voids, and confirmed transfers all feed the ledger.

### Explainability

Each balance can be broken down into the expense rows and transfers behind it. That matters when a split looks wrong and someone needs to debug the history instead of trusting the UI.

### Event History

Writes produce events like `ExpenseCreated`, `ExpenseUpdated`, `ExpenseVoided`, and `TransferConfirmed`. I kept the event model narrow on purpose: enough history to debug state changes without turning the app into a full event-sourced system.

### Deterministic Money Handling

Amounts are normalized to fixed precision, rounding is explicit, and leftover cents are distributed in a stable order. That avoids the usual "why did this recompute to a different cent value?" problem.

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

The backend runs on `http://localhost:8080`. Swagger UI is at `http://localhost:8080/swagger`.

### 3. Start the frontend

```bash
cd fairshare-frontend
npm install
printf "NEXT_PUBLIC_API_BASE_URL=http://localhost:8080\n" > .env.local
npm run dev
```

The frontend runs on `http://localhost:3000`.

If you want the frontend to proxy requests through Next.js instead of calling the backend directly from the browser, use:

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
- Store split results with fixed rounding rules so the same expense does not drift across recomputations

### Settlements And Ledger

- Compute suggested settlements from current ledger balances
- Confirm settlements with idempotency support
- View current balances and owes relationships

### Explainability And History

- Inspect per-user ledger explanations
- View confirmed transfer history
- View expense event history and lifecycle changes

## Authentication Status

The backend supports header-based actor identity via `X-User-Id`. In local development, auth is optional so the main flows work without bootstrapping a real identity layer.

Real user auth is not implemented yet. The planned path is passwordless login, invite links, and guest-to-user claiming, but none of that is in this repo today.

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

The repo still includes `.gitmodules` because the project started as separate backend and frontend repos. The code is treated like one project now, but the submodule metadata is still there.
