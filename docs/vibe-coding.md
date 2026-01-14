# 🎯 Vibe Coding Guidelines

> **Purpose:** Rules untuk AI coding assistant agar menghasilkan code yang konsisten dengan dokumentasi.
> **Last Updated:** 2024-12-27

---

## 1. Golden Rules

### 🔴 WAJIB BACA SEBELUM CODING

```
SEBELUM menulis code apapun, BACA dulu:
1. docs/api-contract.md     → Request/Response schemas
2. docs/frontend.md         → Component structure, hooks
3. docs/backend.md          → NestJS patterns, guards
4. docs/database.md         → Table structure, constraints
```

### ⚡ Core Principles

| Principle               | Rule                                                |
| ----------------------- | --------------------------------------------------- |
| **Contract First**      | Jangan create endpoint tanpa cek api-contract.md    |
| **Type Safety**         | Copy TypeScript types dari docs, jangan create baru |
| **Pattern Consistency** | Ikuti pattern yang sudah ada di docs                |
| **No Magic Values**     | Semua konstanta ada di master data                  |

---

## 2. Frontend Coding Rules

### 2.1 File Locations

```typescript
// CORRECT ✅
src/types/api.ts          // Copy dari api-contract.md Section 10
src/types/sales-order.ts  // Copy dari api-contract.md
src/services/api.ts       // Copy dari frontend.md Section 7.3
src/hooks/useSalesOrders.ts // Copy dari frontend.md Section 7.5

// WRONG ❌
// Jangan buat types baru tanpa cek docs dulu
// Jangan hardcode API responses
```

### 2.2 Component Naming

```typescript
// Pattern dari frontend.md
[Module][Entity][Action].tsx

// Examples:
SalesOrderListPage.tsx
SalesOrderDetailPage.tsx
SalesOrderCreateForm.tsx
SalesOrderApproveDialog.tsx
```

### 2.3 Hook Patterns

```typescript
// SELALU gunakan pattern ini (dari frontend.md Section 7.5)

// Query (GET)
const { data, isLoading, error } = useSalesOrders(params);

// Mutation (POST/PUT/DELETE)
const mutation = useCreateSalesOrder();
mutation.mutate(data, {
  onSuccess: () => toast.success('Success!'),
  onError: (error) => toast.error(error.error?.message),
});
```

### 2.4 Form Validation

```typescript
// SELALU gunakan Zod schema (dari frontend.md Section 9)
// Schema HARUS match dengan API contract

const schema = z.object({
  customerId: z.string().uuid(),  // Match api-contract.md
  orderDate: z.string(),
  items: z.array(itemSchema).min(1),
});
```

---

## 3. Backend Coding Rules

### 3.1 Module Structure

```
// Pattern dari backend.md Section 1.3
src/modules/[entity]/
├── [entity].module.ts
├── [entity].controller.ts
├── [entity].service.ts
├── [entity].repository.ts
├── dto/
│   ├── create-[entity].dto.ts
│   ├── update-[entity].dto.ts
│   └── query-[entity].dto.ts
└── entities/
    └── [entity].entity.ts
```

### 3.2 Controller Pattern

```typescript
// WAJIB ikuti response format dari api-contract.md

@Controller('sales-orders')
export class SalesOrdersController {
  @Get()
  @UseGuards(JwtAuthGuard, PermissionsGuard)
  @Permissions('so.read')  // Permission dari security.md
  async findAll(@Query() query: QueryDto) {
    const result = await this.service.findAll(query);
    return {
      success: true,
      data: result.data,
      pagination: result.pagination,
    };
  }
}
```

### 3.3 Error Response

```typescript
// WAJIB match format di api-contract.md Section 1.3

throw new BadRequestException({
  success: false,
  error: {
    code: 'VALIDATION_ERROR',
    message: 'Validation failed',
    details: { customerId: ['Customer is required'] },
  },
});
```

### 3.4 Permission Names

```typescript
// AMBIL dari security.md Section 3.2
// Format: [module].[action]

'so.read'      // Sales Order - Read
'so.create'    // Sales Order - Create
'so.update'    // Sales Order - Update
'so.delete'    // Sales Order - Delete
'so.approve'   // Sales Order - Approve
```

---

## 4. Database Rules

### 4.1 Table Naming

```sql
-- Dari database.md Section 1

m_[entity]     -- Master/Dimension tables
t_[entity]     -- Transaction tables
t_[entity]_d   -- Transaction detail tables
ai_[entity]    -- AI-related tables
log_[entity]   -- Audit/Log tables
```

### 4.2 Column Patterns

```sql
-- WAJIB untuk semua tables (dari database.md)

id BIGSERIAL PRIMARY KEY,        -- Internal ID
uuid UUID DEFAULT gen_random_uuid(), -- External ID (API)
created_at TIMESTAMPTZ DEFAULT NOW(),
created_by BIGINT,
updated_at TIMESTAMPTZ,
updated_by BIGINT,
is_active BOOLEAN DEFAULT TRUE,  -- For master tables
deleted_at TIMESTAMPTZ,          -- Soft delete
```

### 4.3 Foreign Keys

```sql
-- SELALU gunakan internal ID untuk FK (bukan UUID)
cust_id BIGINT REFERENCES m_customer(id),

-- NEVER expose internal ID ke API
-- Gunakan UUID untuk external reference
```

---

## 5. API Development Rules

### 5.1 Endpoint Naming

```
// WAJIB ikuti pattern dari api-contract.md

GET    /api/[entities]              -- List
GET    /api/[entities]/:uuid        -- Get one
POST   /api/[entities]              -- Create
PUT    /api/[entities]/:uuid        -- Update
DELETE /api/[entities]/:uuid        -- Delete

// Custom actions
POST   /api/[entities]/:uuid/[action]
```

### 5.2 Query Parameters

```typescript
// Standard query params (dari api-contract.md Section 1.4)

interface QueryDto {
  page?: number;      // Default: 1
  limit?: number;     // Default: 20, Max: 100
  search?: string;
  sort?: string;
  order?: 'asc' | 'desc';
  status?: string;
}
```

### 5.3 Response Headers

```typescript
// Dari api-contract.md Section 11

// Set version header
res.setHeader('X-API-Version', '2024-12-27');

// Set request ID
res.setHeader('X-Request-ID', requestId);
```

---

## 6. AI Service Rules

### 6.1 Prompt Templates

```python
# WAJIB gunakan template dari python-ai.md Section 7

SYSTEM_PROMPT = """
Anda adalah ARIA, AI Assistant untuk Sentient Factory.
[Copy dari master_prompting.md]
"""
```

### 6.2 Tool Usage

```python
# HANYA gunakan tools yang sudah defined di python-ai.md

AVAILABLE_TOOLS = [
    "sql_query",      # SELECT only, no mutations
    "rag_search",     # Vector search
    "calculator",     # Math operations
    "alert_trigger",  # Create alerts
]
```

### 6.3 Safety Guards

```python
# WAJIB implement guards dari python-ai.md Section 8

- SQL Injection prevention
- SELECT-only queries
- No PII in responses
- HR data anonymization
```

---

## 7. Testing Rules

### 7.1 Test File Location

```typescript
// Unit tests
src/modules/[entity]/__tests__/[entity].service.spec.ts

// E2E tests
e2e/[module]/[scenario].spec.ts
```

### 7.2 Test ID Format

```typescript
// Dari qa.md Section 1.1

TC-[MODULE]-[NUMBER]

// Examples:
TC-AUTH-001  // Login test
TC-SO-001    // Create SO test
TC-AI-001    // AI chat test
```

### 7.3 data-testid Convention

```typescript
// WAJIB untuk semua interactive elements

data-testid="btn-[action]"          // btn-create, btn-submit
data-testid="input-[field]"         // input-username
data-testid="[entity]-row"          // so-row, customer-row
data-testid="[entity]-status"       // so-status
data-testid="menu-[module]"         // menu-sales
```

---

## 8. Common Mistakes to AVOID

### ❌ DON'T

```typescript
// 1. Don't create new types without checking docs
interface MyCustomType { } // ❌ Check api-contract.md first

// 2. Don't hardcode values
const TAX = 0.11; // ❌ Should be from config/master data

// 3. Don't skip permissions
@Get() // ❌ Missing guards
async findAll() { }

// 4. Don't expose internal IDs
return { id: 123 }; // ❌ Should be uuid

// 5. Don't use different response format
return { status: 'ok', result: data }; // ❌ Use success/data/meta
```

### ✅ DO

```typescript
// 1. Copy types from docs
import { SalesOrder } from '@/types/sales-order'; // ✅ From api-contract.md

// 2. Use config/master data
const taxRate = await this.configService.get('TAX_RATE'); // ✅

// 3. Always add guards
@UseGuards(JwtAuthGuard, PermissionsGuard)
@Permissions('so.read')
async findAll() { } // ✅

// 4. Return UUID
return { uuid: entity.uuid }; // ✅

// 5. Use standard response format
return { success: true, data, meta }; // ✅
```

---

## 9. Quick Reference Commands

```bash
# Start development
docker-compose -f docker-compose.dev.yml up -d
npm run dev                    # Frontend
npm run start:dev              # Backend
python -m app.main             # AI Service

# Database
docker exec -it sentient-postgres psql -U postgres sentient_factory

# Run tests
npm run test                   # Unit tests
npx playwright test            # E2E tests
k6 run k6/load-test.js         # Load tests

# Generate types from API
# (Copy from api-contract.md Section 10)
```

---

## 10. Documentation References

| Topic         | Document                                |
| ------------- | --------------------------------------- |
| API Schemas   | [api-contract.md](docs/api-contract.md) |
| Frontend Code | [frontend.md](docs/frontend.md)         |
| Backend Code  | [backend.md](docs/backend.md)           |
| Database DDL  | [database.md](docs/database.md)         |
| AI Prompts    | [python-ai.md](docs/python-ai.md)       |
| Test Cases    | [qa.md](docs/qa.md)                     |
| Security      | [security.md](docs/security.md)         |
| Deployment    | [deployment.md](docs/deployment.md)     |

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
