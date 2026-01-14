# 🚀 Development Execution Plan

> **Purpose:** Step-by-step guide untuk AI assistant agar bisa coding secara autonomous
> **Mode:** Autopilot - AI dapat bekerja tanpa supervisi manusia
> **AI Provider:** ☁️ Google Gemini (Development) → 🖥️ Local Ollama (Production)
> **Last Updated:** 2024-12-27

---

## ⚠️ Current Status: Pre-Production Mode

**Hardware Status:**
- 🔄 Mac Mini M4 Pro 64GB: **Pre-Order** (belum tersedia)
- ☁️ Gunakan **Google Gemini** untuk development sementara

**AI Configuration:**
```env
# .env (Development Mode - Using Gemini)
LLM_PROVIDER=gemini
GEMINI_API_KEY=AIzaSyB6mT5YVLmjSgYiWTCJmZfXpOmcv0GtSBo
GEMINI_MODEL=gemini-2.0-flash-exp
```

**Reference:** [ai-gemini.md](ai-gemini.md) untuk setup lengkap Gemini

---

## 📋 Pre-Flight Checklist

Sebelum mulai, AI HARUS memastikan:

```bash
# 1. Check if Docker running
docker ps

# 2. Check if PostgreSQL ready
docker exec sentient-postgres pg_isready

# 3. Check if Redis ready
docker exec sentient-redis redis-cli ping

# 4. Check Node version
node -v  # Should be 20+

# 5. Check Python version
python --version  # Should be 3.11+

# 6. Test Gemini API (Development)
python -c "
import google.generativeai as genai
genai.configure(api_key='YOUR_KEY')
model = genai.GenerativeModel('gemini-2.0-flash-exp')
print(model.generate_content('Hello').text)
"
```

---

## 🎯 Execution Phases

| Phase   | Task                 | Duration | Dependency | AI Provider |
| ------- | -------------------- | -------- | ---------- | ----------- |
| **0**   | Infrastructure Setup | 15 min   | None       | -           |
| **1**   | Database Setup       | 30 min   | Phase 0    | -           |
| **2**   | Backend Foundation   | 2 hr     | Phase 1    | -           |
| **3**   | Backend Modules      | 4 hr     | Phase 2    | -           |
| **3.5** | AI Service Setup     | 1 hr     | Phase 2    | ☁️ Gemini    |
| **4**   | Frontend Foundation  | 2 hr     | Phase 2    | -           |
| **5**   | Frontend Pages       | 4 hr     | Phase 4    | -           |
| **6**   | Integration Testing  | 2 hr     | Phase 3, 5 | ☁️ Gemini    |
| **7**   | QA & Polish          | 2 hr     | Phase 6    | -           |

**Total Estimated: ~17 hours**

---

## Phase 0: Infrastructure Setup (Native - No Docker)

> **⚠️ RAM-Friendly:** Native installation untuk laptop 8GB RAM
> **Platform:** macOS with Homebrew

### 0.1 Create Project Structure

```bash
# Create main directories
mkdir -p sentient-factory/{frontend,backend,ai-service}
cd sentient-factory

# Initialize git
git init
cat > .gitignore << 'EOF'
node_modules/
.env
dist/
build/
__pycache__/
*.pyc
venv/
.DS_Store
*.log
EOF
```

### 0.2 Install PostgreSQL (Native)

```bash
# Install via Homebrew
brew install postgresql@15

# Start PostgreSQL service
brew services start postgresql@15

# Verify installation
psql --version

# Create database
createdb sentient_factory

# Test connection
psql -d sentient_factory -c "SELECT version();"
```

**PostgreSQL Config:**
- Host: `localhost`
- Port: `5432`
- Database: `sentient_factory`
- User: Your Mac username (no password needed for local)

### 0.3 Install Redis (Native)

```bash
# Install via Homebrew
brew install redis

# Start Redis service
brew services start redis

# Verify Redis is running
redis-cli ping
# Should return: PONG

# Test connection
redis-cli info server
```

**Redis Config:**
- Host: `localhost`
- Port: `6379`

### 0.4 Install NATS (Native)

```bash
# Install NATS server
brew install nats-server

# Start NATS in background
nats-server -js -m 8222 &

# Verify NATS is running
curl http://localhost:8222/varz

# Check JetStream
nats-server -js -m 8222 --version
```

**NATS Config:**
- Client Port: `4222`
- HTTP Monitor: `8222`

**Alternative: Run NATS in terminal**
```bash
# Keep terminal open
nats-server -js -m 8222
```

### 0.5 Verify All Services

```bash
# Check PostgreSQL
psql -d sentient_factory -c "SELECT 1;"

# Check Redis
redis-cli ping

# Check NATS
curl http://localhost:8222/healthz
```

**✅ Checkpoint 0:** All services running natively (PostgreSQL, Redis, NATS)

---

### 0.6 Service Management

**Stop Services:**
```bash
# PostgreSQL
brew services stop postgresql@15

# Redis
brew services stop redis

# NATS (if running as background process)
pkill nats-server
```

**Start Services:**
```bash
brew services start postgresql@15
brew services start redis
nats-server -js -m 8222 &
```

**Check Services Status:**
```bash
brew services list
ps aux | grep nats-server
```



---

## Phase 1: Database Setup

### 1.1 Connect to PostgreSQL

```bash
# Connect to database (native installation)
psql -d sentient_factory

# Or specify full connection
psql -h localhost -p 5432 -d sentient_factory
```


### 1.2 Run DDL Scripts

**Copy ENTIRE content from [docs/database.md](database.md) Section 1 (DDL)**

Run in order:
1. Master Tables (m_*)
2. Transaction Tables (t_*)
3. AI Tables (ai_*)
4. Audit Tables (log_*)

```sql
-- From database.md Section 1.1 Master Tables
-- Copy and run ALL CREATE TABLE statements

-- 1. m_role
CREATE TABLE m_role ( ... );

-- 2. m_user
CREATE TABLE m_user ( ... );

-- Continue with ALL tables from database.md...
```

### 1.3 Create Functions

```sql
-- From database.md Section 2
-- Copy and run ALL functions

CREATE OR REPLACE FUNCTION fn_generate_so_number() ...
CREATE OR REPLACE FUNCTION fn_generate_po_number() ...
-- etc.
```

### 1.4 Create Triggers

```sql
-- From database.md Section 3
-- Copy and run ALL triggers

CREATE TRIGGER trg_so_audit ...
CREATE TRIGGER trg_update_timestamp ...
-- etc.
```

### 1.5 Insert Seed Data

```sql
-- From database.md Section 4
-- Copy and run ALL INSERT statements

INSERT INTO m_role VALUES ...
INSERT INTO m_user VALUES ...
INSERT INTO m_customer VALUES ...
-- etc.
```

### 1.6 Verify Database

```sql
-- Check all tables created
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'public' 
ORDER BY table_name;

-- Check seed data
SELECT COUNT(*) as roles FROM m_role;
SELECT COUNT(*) as users FROM m_user;
SELECT COUNT(*) as customers FROM m_customer;
SELECT COUNT(*) as items FROM m_item;
```

**✅ Checkpoint 1:** All tables exist, seed data loaded

---

## Phase 2: Backend Foundation

### 2.1 Initialize NestJS

```bash
cd backend

# Create NestJS project
npx @nestjs/cli new . --package-manager npm --skip-git

# Install core dependencies
npm install @nestjs/config @nestjs/typeorm typeorm pg
npm install @nestjs/passport passport passport-jwt @nestjs/jwt
npm install @nestjs/swagger swagger-ui-express
npm install class-validator class-transformer
npm install bcrypt uuid
npm install -D @types/passport-jwt @types/bcrypt
```

### 2.2 Project Structure

```bash
# Create folder structure (from backend.md Section 1.3)
mkdir -p src/{common,config,modules}
mkdir -p src/common/{decorators,filters,guards,interceptors,pipes}
mkdir -p src/modules/{auth,users,roles,customers,items,vendors}
mkdir -p src/modules/{sales-orders,delivery-orders,purchase-orders,work-orders}
mkdir -p src/modules/{inventory,ai-chat,alerts,dashboard,reports}
```

### 2.3 Environment Config

```bash
# Create .env file
cat > .env << 'EOF'
# App
APP_PORT=8000
APP_ENV=development
NODE_ENV=development

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sentient_factory
DB_USER=postgres
DB_PASS=secret

# JWT
JWT_SECRET=sentient-factory-secret-key-change-in-production
JWT_EXPIRES_IN=1h
JWT_REFRESH_SECRET=sentient-factory-refresh-secret
JWT_REFRESH_EXPIRES_IN=7d

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# NATS
NATS_URL=nats://localhost:4222
EOF
```

### 2.4 Database Configuration

```typescript
// src/config/database.config.ts
// Copy from backend.md Section 1.4

import { TypeOrmModuleOptions } from '@nestjs/typeorm';

export const databaseConfig: TypeOrmModuleOptions = {
  type: 'postgres',
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT, 10),
  username: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_NAME,
  entities: [__dirname + '/../**/*.entity{.ts,.js}'],
  synchronize: false,  // Use migrations instead
  logging: process.env.NODE_ENV === 'development',
};
```

### 2.5 App Module Setup

```typescript
// src/app.module.ts
// Copy structure from backend.md

import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';
import { databaseConfig } from './config/database.config';

// Import all modules...

@Module({
  imports: [
    ConfigModule.forRoot({ isGlobal: true }),
    TypeOrmModule.forRoot(databaseConfig),
    // ... other modules
  ],
})
export class AppModule {}
```

### 2.6 Auth Module

**Reference:** [backend.md Section 2](backend.md#_2-authentication-authorization)

Create files:
- `src/modules/auth/auth.module.ts`
- `src/modules/auth/auth.controller.ts`
- `src/modules/auth/auth.service.ts`
- `src/modules/auth/jwt.strategy.ts`
- `src/modules/auth/dto/login.dto.ts`

### 2.7 Common Guards & Decorators

Create:
- `src/common/guards/jwt-auth.guard.ts`
- `src/common/guards/permissions.guard.ts`
- `src/common/decorators/permissions.decorator.ts`
- `src/common/decorators/current-user.decorator.ts`

### 2.8 Swagger Setup

```typescript
// src/main.ts
// Copy from openapi.md Section 1

import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Swagger config...
  
  await app.listen(8000);
}
```

### 2.9 Test Backend Runs

```bash
npm run start:dev

# Should see:
# [Nest] Application running on http://localhost:8000
```

**✅ Checkpoint 2:** Backend starts, Swagger at http://localhost:8000/api/docs

---

## Phase 3: Backend Modules

### 3.1 Entity Creation

For each module, create entity matching database.md:

```typescript
// Example: src/modules/customers/entities/customer.entity.ts

import { Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity('m_customer')
export class Customer {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ type: 'uuid', default: () => 'gen_random_uuid()' })
  uuid: string;

  @Column({ length: 20, unique: true })
  code: string;

  @Column({ length: 100 })
  name: string;
  
  // ... rest from database.md
}
```

### 3.2 Module Priority Order

Create modules in this order (dependency-based):

```
1. users (depends on: roles)
2. roles 
3. customers
4. items
5. vendors
6. sales-orders (depends on: customers, items, users)
7. delivery-orders (depends on: sales-orders)
8. purchase-orders (depends on: vendors, items)
9. work-orders (depends on: items)
10. inventory (depends on: items)
11. dashboard (depends on: all)
12. ai-chat
13. alerts
14. reports
```

### 3.3 For Each Module, Create:

```
src/modules/[entity]/
├── [entity].module.ts
├── [entity].controller.ts      # From api-contract.md
├── [entity].service.ts
├── [entity].repository.ts
├── dto/
│   ├── create-[entity].dto.ts  # From api-contract.md
│   ├── update-[entity].dto.ts
│   └── query-[entity].dto.ts
└── entities/
    └── [entity].entity.ts      # From database.md
```

### 3.4 Response Format

ALL controllers MUST return format from api-contract.md:

```typescript
// Success
return {
  success: true,
  data: result,
  meta: {
    timestamp: new Date().toISOString(),
    requestId: req.headers['x-request-id'],
  }
};

// Paginated
return {
  success: true,
  data: items,
  pagination: {
    page,
    limit,
    total,
    totalPages: Math.ceil(total / limit),
  }
};
```

### 3.5 Test Each Module

```bash
# After each module, test with curl or Postman

# Login
curl -X POST http://localhost:8000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"Password123!"}'

# Get token and use for subsequent requests
TOKEN="eyJ..."

# Test endpoint
curl http://localhost:8000/api/customers \
  -H "Authorization: Bearer $TOKEN"
```

**✅ Checkpoint 3:** All CRUD endpoints working, Swagger shows all routes

---

## Phase 3.5: AI Service Setup (Gemini)

> **Reference:** [ai-gemini.md](ai-gemini.md)
> **Provider:** Google Gemini (temporary until Mac Mini M4 arrives)

### 3.5.1 Create AI Service Directory

```bash
cd sentient-factory/ai-service

# Create Python project structure
mkdir -p app/{agent,tools,config}
touch app/__init__.py app/main.py
touch app/agent/__init__.py app/agent/orchestrator.py app/agent/llm_client.py
touch app/tools/__init__.py app/tools/sql_tool.py app/tools/rag_tool.py
touch app/config/__init__.py app/config/settings.py
```

### 3.5.2 Setup Python Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Mac/Linux

# Install dependencies
pip install google-generativeai langchain-google-genai
pip install langchain langchain-core langchain-community
pip install pydantic pydantic-settings python-dotenv
pip install nats-py asyncpg chromadb
pip install fastapi uvicorn
```

### 3.5.3 Environment Configuration

```bash
# Create .env file
cat > .env << 'EOF'
# AI Provider (gemini for development, ollama for production)
LLM_PROVIDER=gemini

# Gemini Configuration (Development)
GEMINI_API_KEY=AIzaSyB6mT5YVLmjSgYiWTCJmZfXpOmcv0GtSBo
GEMINI_MODEL=gemini-2.0-flash-exp
GEMINI_TEMPERATURE=0.1
GEMINI_MAX_TOKENS=4096

# Ollama Configuration (Production - when Mac Mini arrives)
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=sentient-brain

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sentient_factory
DB_USER=postgres
DB_PASS=secret

# NATS
NATS_URL=nats://localhost:4222

# ChromaDB
CHROMA_HOST=localhost
CHROMA_PORT=8000
EOF
```

### 3.5.4 LLM Client Factory

```python
# app/agent/llm_client.py

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_community.chat_models import ChatOllama
from app.config.settings import settings

def get_llm():
    """
    Factory function to get LLM based on configuration.
    Easily switch between Gemini (dev) and Ollama (prod).
    """
    if settings.LLM_PROVIDER == "gemini":
        return ChatGoogleGenerativeAI(
            model=settings.GEMINI_MODEL,
            google_api_key=settings.GEMINI_API_KEY,
            temperature=settings.GEMINI_TEMPERATURE,
            max_output_tokens=settings.GEMINI_MAX_TOKENS,
        )
    else:  # ollama
        return ChatOllama(
            model=settings.OLLAMA_MODEL,
            base_url=settings.OLLAMA_BASE_URL,
            temperature=settings.GEMINI_TEMPERATURE,
        )
```

### 3.5.5 Test Gemini Connection

```bash
# Test basic connection
python -c "
import google.generativeai as genai
from dotenv import load_dotenv
import os

load_dotenv()
genai.configure(api_key=os.getenv('GEMINI_API_KEY'))

model = genai.GenerativeModel('gemini-2.0-flash-exp')
response = model.generate_content('Jelaskan revenue dalam bahasa Indonesia')
print(response.text)
"
```

### 3.5.6 Test LangChain Integration

```bash
python -c "
from langchain_google_genai import ChatGoogleGenerativeAI
from dotenv import load_dotenv
import os

load_dotenv()

llm = ChatGoogleGenerativeAI(
    model='gemini-2.0-flash-exp',
    google_api_key=os.getenv('GEMINI_API_KEY'),
    temperature=0.1,
)

response = llm.invoke('Halo, siapa kamu?')
print(response.content)
"
```

**✅ Checkpoint 3.5:** Gemini API responding, LangChain integration working

---

## Phase 4: Frontend Foundation

> **📦 Metronic Reference:** [metronic-reference.md](metronic-reference.md)

### 4.1 Setup from Local Metronic (RECOMMENDED)

```bash
# Navigate to project root
cd sentient-factory

# Option A: Copy entire Metronic Vite demo
cp -r ../metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite frontend

cd frontend
npm install
npm run dev  # Opens at http://localhost:5173
```

### 4.2 Alternative: Fresh Project + Copy Components

```bash
# ... (existing content)
```

### 4.3 Menu & Routing Customization (New)

> **Goal:** Clean up default Metronic items and set up Sentient Factory navigation.

1. **Backup:** `cp src/config/menu.config.tsx src/config/menu.config.backup.tsx`
2. **Define Structure:**
   - **Dashboard** (`/`)
   - **Core Modules**
     - Customers (`/customers`)
     - Items (`/products`)
     - Transactions (`/sales`)
   - **Settings**
     - User Management (`/users`)
     - AI Config (`/settings/ai`)
3. **Clean Routing:** Remove unused demo routes in `AppRoutingSetup`.

### 4.4 Metronic Folder Structure (Local Reference)

```bash
# Create fresh Vite project
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install

# Copy Metronic components from local folder
METRONIC_SRC="../metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/src"

# Copy UI components (78 components)
cp -r $METRONIC_SRC/components/ui src/components/

# Copy Demo 4 layout (dark sidebar)
cp -r $METRONIC_SRC/layouts/demo4 src/layouts/

# Copy hooks
cp -r $METRONIC_SRC/hooks src/

# Copy CSS
cp -r $METRONIC_SRC/css src/

# Copy providers
cp -r $METRONIC_SRC/providers src/

# Copy config
cp $METRONIC_SRC/config/menu.config.ts src/config/

# Install Metronic dependencies
npm install @tanstack/react-query@5.85.5 @tanstack/react-table@8.21.3
npm install react-hook-form@7.62.0 @hookform/resolvers@5.2.1 zod@3.25.67
npm install react-router-dom@7.8.2
npm install tailwindcss@4.1.12 lucide-react@0.541.0 radix-ui@1.4.3
npm install sonner@2.0.7 clsx@2.1.1 tailwind-merge@3.3.1 class-variance-authority@0.7.1
npm install apexcharts@4.7.0 react-apexcharts@1.7.0 motion@12.23.12 date-fns@4.1.0
npm install zustand
```

### 4.3 Metronic Folder Structure (Local Reference)

```
metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/src/
├── components/
│   └── ui/                      # 78 UI components
│       ├── button.tsx           # 13KB
│       ├── data-grid-table.tsx  # 16KB
│       ├── badge.tsx            # 9KB
│       └── ...
├── layouts/
│   └── demo4/                   # ⭐ Dark sidebar layout
│       ├── layout.tsx           # Main wrapper
│       └── components/          # header, sidebar, footer
├── pages/                       # 564 page examples
├── hooks/                       # 10 custom hooks
├── css/                         # Stylesheets
└── config/                      # Menu config
```

### 4.4 Project Structure (Sentient Factory)

### 4.4 TypeScript Types

```typescript
// src/types/api.ts
// Copy ENTIRE Section 10 from api-contract.md

export interface ApiResponse<T> {
  success: boolean;
  data: T;
  meta: { ... };
}

// ... all types from api-contract.md
```

### 4.5 API Client Setup

```typescript
// src/services/api.ts
// Copy from frontend.md Section 7.3

import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8000/api',
});

// Add interceptors for auth, error handling
// Copy from frontend.md
```

### 4.6 Auth Store (Zustand)

```typescript
// src/stores/authStore.ts
// Copy from frontend.md Section 8

import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  accessToken: string | null;
  // ...
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      // ... implementation
    }),
    { name: 'auth-storage' }
  )
);
```

### 4.7 Router Setup

```typescript
// src/App.tsx
// Copy routing structure from frontend.md Section 2

import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/auth/login" element={<LoginPage />} />
        <Route path="/" element={<AuthLayout />}>
          <Route path="dashboard" element={<DashboardPage />} />
          {/* ... more routes from frontend.md */}
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

### 4.8 Test Frontend Runs

```bash
npm run dev

# Should open at http://localhost:5173
```

**✅ Checkpoint 4:** Frontend starts, routing works, can see login page

---

## Phase 5: Frontend Pages

### 5.1 Page Priority Order

Create pages in this order:

```
1. auth/LoginPage
2. dashboard/DashboardPage
3. sales/SalesOrderListPage
4. sales/SalesOrderDetailPage
5. sales/SalesOrderFormPage
6. procurement/POListPage
7. production/WOListPage
8. inventory/StockListPage
9. ai/ChatPage
10. settings/UserListPage
```

### 5.2 For Each Page, Create:

1. **React Query Hook** (from frontend.md Section 7.5)
2. **Page Component** (from frontend.md Section 3-5)
3. **Form Schema** (from frontend.md Section 9)

### 5.3 Shared Components

Create reusable components (from frontend.md Section 6):

- `components/common/DataTable.tsx`
- `components/common/StatusBadge.tsx`
- `components/common/LoadingSpinner.tsx`
- `components/common/EmptyState.tsx`
- `components/common/ConfirmDialog.tsx`
- `components/forms/TextInput.tsx`
- `components/forms/SelectInput.tsx`
- `components/forms/DatePicker.tsx`

### 5.4 API Hook Pattern

```typescript
// src/hooks/useSalesOrders.ts
// Copy pattern from frontend.md Section 7.5

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { salesOrderService } from '@/services/sales-order.service';

export function useSalesOrders(params: QueryParams) {
  return useQuery({
    queryKey: ['sales-orders', params],
    queryFn: () => salesOrderService.list(params),
  });
}

export function useCreateSalesOrder() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: salesOrderService.create,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['sales-orders'] });
    },
  });
}
```

**✅ Checkpoint 5:** All pages render, forms work, data displays

---

## Phase 6: Integration Testing

### 6.1 E2E Test Setup

```bash
cd frontend

# Install Playwright
npm init playwright@latest

# Install test dependencies
npm install -D @playwright/test
npx playwright install
```

### 6.2 Run E2E Tests

```typescript
// e2e/auth/login.spec.ts
// Copy from qa.md Section 8.4

import { test, expect } from '@playwright/test';

test('should login successfully', async ({ page }) => {
  await page.goto('/auth/login');
  await page.fill('[name="username"]', 'admin');
  await page.fill('[name="password"]', 'Password123!');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

### 6.3 Run Tests

```bash
# Run all E2E tests
npx playwright test

# Run with UI
npx playwright test --ui
```

### 6.4 Backend Tests

```bash
cd ../backend

# Run unit tests
npm run test

# Run e2e tests
npm run test:e2e
```

**✅ Checkpoint 6:** All critical paths tested, no major failures

---

## Phase 7: QA & Polish

### 7.1 Test Checklist

Reference: [qa.md](qa.md) for all test cases

```
□ TC-AUTH-001: Valid Login
□ TC-AUTH-002: Invalid Password
□ TC-AUTH-003: Account Lockout
□ TC-SO-001: Create Sales Order
□ TC-SO-003: Approve SO
□ TC-AI-001: Basic Query
... (continue with all P1 test cases)
```

### 7.2 Performance Check

```bash
# Run k6 load test
# Copy from qa.md Section 9

k6 run k6/load-test.js
```

### 7.3 Security Check

```bash
# Run npm audit
npm audit

# Check for vulnerabilities
npx snyk test
```

### 7.4 Build Production

```bash
# Frontend
cd frontend
npm run build

# Backend
cd ../backend
npm run build
```

**✅ Checkpoint 7:** All builds pass, no critical issues

---

## 🎯 Success Criteria

### Must Have (Phase Complete):
- [ ] All Docker services running
- [ ] Database with all tables and seed data
- [ ] Backend API responding at :8000
- [ ] Frontend loading at :5173
- [ ] Login flow working
- [ ] CRUD for Sales Orders working
- [ ] Basic AI chat responding

### Nice to Have:
- [ ] All modules implemented
- [ ] E2E tests passing
- [ ] Production build ready

---

## 🔄 Rollback Procedures

### If Database Fails:
```bash
# Drop and recreate
docker exec sentient-postgres psql -U postgres -c "DROP DATABASE sentient_factory;"
docker exec sentient-postgres psql -U postgres -c "CREATE DATABASE sentient_factory;"
# Re-run DDL from Phase 1
```

### If Backend Fails:
```bash
cd backend
rm -rf node_modules dist
npm install
npm run start:dev
```

### If Frontend Fails:
```bash
cd frontend
rm -rf node_modules dist
npm install
npm run dev
```

---

## 📞 Handoff Notes

When human wakes up, AI should report:
1. Which phase completed
2. Any blockers encountered
3. Test results summary
4. Next steps needed

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
*Estimated Autonomous Run Time: 16 hours*
