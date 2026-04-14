# Fairshare Monorepo

Fairshare is a full-stack group expense tracker with explainable ledgers, settlement suggestions, and an audit trail for expense and transfer history.

This repository contains:

- `fairshare-backend`: Spring Boot API, PostgreSQL in local/dev, H2 in tests
- `fairshare-frontend`: Next.js web app for group management, expenses, settlements, and history

The repo is wired as a monorepo with backend and frontend directories present locally. `.gitmodules` is still checked in because the project was originally split across submodule-backed repos.

## Current Stack

- Backend: Java 21, Spring Boot 3.5.7, Maven, Spring Data JPA, Flyway, PostgreSQL
- Frontend: Next.js 16.1.6, React 19, TypeScript 5, Tailwind CSS 4

## Fastest Way To Run The Project

1. Start PostgreSQL and create the local database:

```bash
createdb fairshare
psql -c "CREATE USER fairshare_user WITH PASSWORD 'fairshare_pass';"
psql -c "GRANT ALL PRIVILEGES ON DATABASE fairshare TO fairshare_user;"
```

2. Start the backend:

```bash
cd fairshare-backend
mvn spring-boot:run
```

The backend runs on `http://localhost:8080`.

3. Start the frontend in a second terminal:

```bash
cd fairshare-frontend
npm install
printf "NEXT_PUBLIC_API_BASE_URL=http://localhost:8080\n" > .env.local
npm run dev
```

The frontend runs on `http://localhost:3000`.

## Alternate Frontend API Setup

If you want the browser to call the backend directly, keep:

```bash
NEXT_PUBLIC_API_BASE_URL=http://localhost:8080
```

If you want requests to go through Next.js rewrites instead, use:

```bash
NEXT_PUBLIC_API_BASE_URL=http://localhost:3000/api
```

The frontend rewrite target defaults to `http://localhost:8080` and can be overridden with `BACKEND_URL`.

## Core Product Scope

- Create users and groups
- Add group members
- Create and update expenses with multiple split modes
- Compute ledgers and settlement suggestions
- Confirm settlements with idempotency support
- Inspect historical transfers and expense events
- Explain per-user ledger balances

## Repository Guide

- Root guide: this file
- Backend setup and API notes: [fairshare-backend/README.md](/Users/pratyushkumar/Desktop/Pratyush/faireshare-mono-repo/fairshare-backend/README.md)
- Frontend setup and local workflow: [fairshare-frontend/README.md](/Users/pratyushkumar/Desktop/Pratyush/faireshare-mono-repo/fairshare-frontend/README.md)

## Verification Commands

Backend:

```bash
cd fairshare-backend
mvn test
```

Frontend:

```bash
cd fairshare-frontend
npm run build
```

## What Matters Next

If your goal is to finish this project quickly, the shortest path is:

1. Keep the backend and frontend both runnable from local defaults.
2. Avoid more infra churn until the core user flows are stable.
3. Use the backend tests and frontend production build as the minimum release gate.
