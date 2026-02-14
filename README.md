# Fairshare Mono Repository

A comprehensive expense tracking and settlement system designed for groups to manage shared costs fairly and efficiently. This mono-repository contains both the backend API and frontend web application as Git submodules.

## 🎯 Overview

**Fairshare** is a full-stack application that helps groups track shared expenses, calculate fair splits, and determine optimal settlement transfers. Whether you're sharing an apartment, traveling with friends, or managing group activities, Fairshare makes it easy to track who owes what and minimize the number of payments needed to settle up.

### Key Features
- 📊 **Smart Expense Tracking**: Support for multiple split modes (equal, exact amounts, percentages, weighted shares)
- 💰 **Intelligent Settlements**: Minimizes the number of transfers needed to settle group balances
- 📝 **Comprehensive Audit Trail**: Full history of all expenses, edits, and confirmed transfers
- 🔍 **Detailed Explanations**: See exactly how ledger balances are calculated
- 👥 **Multi-Group Support**: Manage expenses across different groups simultaneously
- 🔄 **Idempotent Operations**: Prevent duplicate entries with built-in idempotency support

## 🏗️ Repository Structure

This is a mono-repository that uses Git submodules to organize the backend and frontend:

```
fairshare-mono-repo/
├── fairshare-backend/     # Spring Boot API (Java 21)
├── fairshare-frontend/    # Next.js web app (TypeScript)
└── README.md             # This file
```

## 📦 Submodules

### Backend (fairshare-backend)
**Repository**: https://github.com/PratypartyY2K/fairshare-backend.git

A robust REST API built with Spring Boot that handles all business logic, data persistence, and calculations.

#### Technology Stack
- **Language**: Java 21
- **Framework**: Spring Boot 3.4.1
- **Build Tool**: Maven 3
- **Database**: PostgreSQL (production), H2 (testing)
- **API Documentation**: Springdoc OpenAPI/Swagger
- **ORM**: Spring Data JPA with Hibernate

#### Core Features
- **Group Management**: Create groups, add members, manage metadata
- **Expense Tracking**: Create, update, void expenses with flexible split modes:
  - Equal splits (divide evenly)
  - Exact amounts (specify each person's share)
  - Percentage splits (e.g., 50%, 30%, 20%)
  - Weighted shares (e.g., 2:1:1 ratio)
- **Ledger Calculation**: Real-time net balance computation for each group member
- **Settlement Algorithm**: Deterministic algorithm that minimizes transaction count while maintaining stable ordering
- **Confirmed Transfers**: Track completed payments with full audit trail
- **Event Log**: Complete history of all mutations (creates, updates, voids, confirmations)
- **Detailed Explanations**: Breakdown of how each person's balance is calculated

#### API Endpoints
- **Health**: `GET /`, `GET /health`
- **Groups**: `POST /groups`, `GET /groups`, `PATCH /groups/{groupId}`, `POST /groups/{groupId}/members`
- **Expenses**: `POST /groups/{groupId}/expenses`, `PATCH /groups/{groupId}/expenses/{expenseId}`, `DELETE /groups/{groupId}/expenses/{expenseId}`
- **Ledger**: `GET /groups/{groupId}/ledger`
- **Settlements**: `GET /groups/{groupId}/settlements`, `POST /groups/{groupId}/settlements/confirm`
- **Transfers**: `GET /groups/{groupId}/confirmed-transfers`
- **Owes**: `GET /groups/{groupId}/owes?fromUserId=X&toUserId=Y`
- **Events**: `GET /groups/{groupId}/events`
- **Explanations**: `GET /groups/{groupId}/explanations/ledger`

#### Running the Backend
```bash
cd fairshare-backend

# Build
mvn clean package

# Run
mvn spring-boot:run

# Run tests
mvn test
```

**Default Configuration**:
- Port: 8080
- Database: `jdbc:postgresql://localhost:5432/fairshare`
- Credentials: `fairshare_user` / `fairshare_pass`
- Swagger UI: http://localhost:8080/swagger

**Prerequisites**: Java 21, Maven, PostgreSQL

---

### Frontend (fairshare-frontend)
**Repository**: https://github.com/PratypartyY2K/fairshare-frontend.git

A modern, responsive web application built with Next.js that provides an intuitive interface for managing shared expenses.

#### Technology Stack
- **Framework**: Next.js 16.1.4 (App Router)
- **Language**: TypeScript 5
- **UI Library**: React 19.2.3
- **Styling**: Tailwind CSS 4
- **Runtime**: Node.js 18+

#### User Interface
The application is organized into intuitive pages and tabs:

**Home Page** (`/`):
- Create new groups
- Rename existing groups
- List all groups with pagination and filtering
- Sort by name or member count
- Check API connection status

**Group Detail Page** (`/groups/{groupId}`):
Organized into 5 tabs for different functions:

1. **Members Tab**: 
   - Rename group
   - Add new members
   - View all members in the group

2. **Expenses Tab**:
   - Add new expenses with flexible split modes
   - Edit existing expenses
   - Delete/void expenses
   - View all expense history

3. **Settle Tab**:
   - View settlement suggestions (optimal transfer plan)
   - Confirm transfers (with idempotency protection)
   - View current ledger snapshot
   - Look up specific "who owes whom" amounts

4. **Explain Tab**:
   - Detailed per-member ledger breakdown
   - See contributions, expenses paid, and received payments
   - Understand how each person's balance is calculated

5. **History Tab**:
   - View confirmed transfers (paginated)
   - Browse complete expense event audit log
   - Track all changes and updates

#### Running the Frontend
```bash
cd fairshare-frontend

# Install dependencies
npm install

# Set up environment
echo "NEXT_PUBLIC_API_BASE_URL=http://localhost:8080" > .env.local

# Run development server
npm run dev

# Build for production
npm run build

# Run production server
npm start

# Lint code
npm run lint
```

**Default Configuration**:
- Development port: 3000
- Production API: Configure via `NEXT_PUBLIC_API_BASE_URL` environment variable

**Prerequisites**: Node.js 18+, Running backend API

## 🚀 Getting Started

### Initial Setup

1. **Clone the repository with submodules**:
   ```bash
   git clone --recurse-submodules https://github.com/PratypartyY2K/fairshare-mono-repo.git
   cd fairshare-mono-repo
   ```

   If you already cloned without submodules:
   ```bash
   git submodule update --init --recursive
   ```

2. **Set up PostgreSQL** (for backend):
   ```bash
   # Create database
   createdb fairshare
   
   # Create user
   psql -c "CREATE USER fairshare_user WITH PASSWORD 'fairshare_pass';"
   psql -c "GRANT ALL PRIVILEGES ON DATABASE fairshare TO fairshare_user;"
   ```

3. **Start the Backend**:
   ```bash
   cd fairshare-backend
   mvn spring-boot:run
   ```
   
   The API will be available at http://localhost:8080
   Swagger documentation at http://localhost:8080/swagger

4. **Start the Frontend**:
   ```bash
   cd ../fairshare-frontend
   npm install
   echo "NEXT_PUBLIC_API_BASE_URL=http://localhost:8080" > .env.local
   npm run dev
   ```
   
   The web app will be available at http://localhost:3000

### Quick Start (Development)

For rapid development, you can run both services:

```bash
# Terminal 1 - Backend
cd fairshare-backend && mvn spring-boot:run

# Terminal 2 - Frontend  
cd fairshare-frontend && npm run dev
```

## 🧪 Testing

### Backend Tests
```bash
cd fairshare-backend
mvn test
```

The backend includes comprehensive integration tests covering:
- Group management
- Expense creation and mutations
- Settlement calculations
- Pagination and filtering
- API endpoint validation

### Frontend Linting
```bash
cd fairshare-frontend
npm run lint
```

## 📚 Documentation

- **Backend API Documentation**: Access Swagger UI at http://localhost:8080/swagger when the backend is running
- **Backend README**: See `fairshare-backend/README.md` for detailed API documentation
- **Frontend README**: See `fairshare-frontend/README.md` for detailed component and feature documentation

## 🔄 Updating Submodules

To update submodules to their latest versions:

```bash
# Update all submodules to latest commit on their tracked branch
git submodule update --remote

# Or update a specific submodule
git submodule update --remote fairshare-backend
git submodule update --remote fairshare-frontend

# Commit the submodule updates
git add .
git commit -m "Update submodules to latest versions"
```

## 🛠️ Technology Summary

| Component | Languages | Frameworks | Key Libraries |
|-----------|-----------|------------|---------------|
| **Backend** | Java 21 | Spring Boot 3.4.1 | Spring Data JPA, PostgreSQL, Swagger |
| **Frontend** | TypeScript 5 | Next.js 16.1.4, React 19.2.3 | Tailwind CSS 4 |
| **Build Tools** | - | Maven 3, npm | - |
| **Database** | SQL | PostgreSQL | H2 (testing) |

## 📋 Requirements

- **Backend**: Java 21, Maven 3, PostgreSQL
- **Frontend**: Node.js 18+, npm
- **Both**: Git (for version control)

## 🤝 Contributing

When contributing to this mono-repository:

1. Make changes to the appropriate submodule repository
2. Update the submodule reference in this mono-repo
3. Ensure both backend and frontend remain compatible
4. Test the integration between services

## 📄 License

Please refer to the individual submodule repositories for license information.

## 🔗 Related Links

- **Backend Repository**: https://github.com/PratypartyY2K/fairshare-backend
- **Frontend Repository**: https://github.com/PratypartyY2K/fairshare-frontend