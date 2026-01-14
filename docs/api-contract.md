# 📜 API Contract Specification

> **Purpose:** Contract antara Backend & Frontend
> **Format:** REST JSON API
> **Base URL:** `http://localhost:8000/api`
> **Last Updated:** 2024-12-27

---

## 1. General Conventions

### 1.1 Request Headers

| Header          | Required    | Description            |
| --------------- | ----------- | ---------------------- |
| `Content-Type`  | Yes         | `application/json`     |
| `Authorization` | Conditional | `Bearer {accessToken}` |
| `X-Request-ID`  | Optional    | UUID for tracing       |

### 1.2 Response Format

**Success Response:**
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "timestamp": "2024-12-27T10:00:00Z",
    "requestId": "550e8400-e29b-41d4-a716-446655440000"
  }
}
```

**Paginated Response:**
```json
{
  "success": true,
  "data": [ ... ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  },
  "meta": { ... }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": {
      "customerId": ["Customer is required"],
      "items": ["At least one item is required"]
    }
  },
  "meta": { ... }
}
```

### 1.3 Error Codes

| HTTP | Code                  | Description              |
| ---- | --------------------- | ------------------------ |
| 400  | `VALIDATION_ERROR`    | Input validation failed  |
| 401  | `UNAUTHORIZED`        | Missing or invalid token |
| 403  | `FORBIDDEN`           | No permission            |
| 404  | `NOT_FOUND`           | Resource not found       |
| 409  | `CONFLICT`            | Duplicate or conflict    |
| 422  | `BUSINESS_ERROR`      | Business rule violated   |
| 500  | `SERVER_ERROR`        | Internal error           |
| 503  | `SERVICE_UNAVAILABLE` | AI/External service down |

### 1.4 Common Query Parameters

| Param    | Type   | Description              | Example            |
| -------- | ------ | ------------------------ | ------------------ |
| `page`   | number | Page number (1-based)    | `?page=1`          |
| `limit`  | number | Items per page (max 100) | `?limit=20`        |
| `search` | string | Search keyword           | `?search=widget`   |
| `sort`   | string | Sort field               | `?sort=created_at` |
| `order`  | string | Sort direction           | `?order=desc`      |
| `status` | string | Filter by status         | `?status=approved` |

---

## 2. Authentication

### 2.1 Login

```
POST /api/auth/login
```

**Request:**
```json
{
  "username": "john.doe",
  "password": "Password123!"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 86400,
    "user": {
      "uuid": "550e8400-e29b-41d4-a716-446655440000",
      "username": "john.doe",
      "email": "john@sentient.local",
      "role": {
        "code": "ADMIN",
        "name": "Administrator"
      },
      "permissions": ["dashboard.view", "so.read", "so.create", ...]
    }
  }
}
```

**Error (401):**
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid username or password"
  }
}
```

### 2.2 Refresh Token

```
POST /api/auth/refresh
```

**Request:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 86400
  }
}
```

### 2.3 Logout

```
POST /api/auth/logout
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "Logged out successfully"
  }
}
```

### 2.4 Get Current User

```
GET /api/auth/me
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "uuid": "550e8400-e29b-41d4-a716-446655440000",
    "username": "john.doe",
    "email": "john@sentient.local",
    "role": {
      "code": "ADMIN",
      "name": "Administrator"
    },
    "permissions": ["dashboard.view", "so.read", ...]
  }
}
```

---

## 3. Dashboard

### 3.1 Get Summary

```
GET /api/dashboard/summary
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "revenue": {
      "value": 5234000000,
      "formatted": "Rp 5.234.000.000",
      "change": 12.5,
      "trend": "up"
    },
    "pendingOrders": {
      "value": 23,
      "change": -2,
      "trend": "down"
    },
    "productionOEE": {
      "value": 87.5,
      "change": 3.2,
      "trend": "up"
    },
    "activeAlerts": {
      "value": 5,
      "critical": 1,
      "warning": 4
    }
  }
}
```

### 3.2 Get Revenue Trend

```
GET /api/dashboard/revenue-trend?period=30d
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "labels": ["2024-11-28", "2024-11-29", ...],
    "datasets": [
      {
        "label": "Revenue",
        "data": [150000000, 180000000, ...]
      },
      {
        "label": "Target",
        "data": [160000000, 160000000, ...]
      }
    ]
  }
}
```

### 3.3 Get Top Customers

```
GET /api/dashboard/top-customers?limit=5
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "uuid": "...",
      "name": "PT ABC Manufacturing",
      "revenue": 890000000,
      "revenueFormatted": "Rp 890.000.000",
      "orderCount": 15
    },
    {
      "uuid": "...",
      "name": "CV XYZ Trading",
      "revenue": 756000000,
      "revenueFormatted": "Rp 756.000.000",
      "orderCount": 12
    }
  ]
}
```

---

## 4. Users

### 4.1 List Users

```
GET /api/users?page=1&limit=20&search=john
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "uuid": "550e8400-e29b-41d4-a716-446655440000",
      "username": "john.doe",
      "email": "john@sentient.local",
      "role": {
        "code": "ADMIN",
        "name": "Administrator"
      },
      "isActive": true,
      "lastLogin": "2024-12-27T08:00:00Z",
      "createdAt": "2024-01-15T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 25,
    "totalPages": 2
  }
}
```

### 4.2 Get User by UUID

```
GET /api/users/:uuid
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "uuid": "550e8400-e29b-41d4-a716-446655440000",
    "username": "john.doe",
    "email": "john@sentient.local",
    "role": {
      "id": 1,
      "code": "ADMIN",
      "name": "Administrator"
    },
    "isActive": true,
    "lastLogin": "2024-12-27T08:00:00Z",
    "createdAt": "2024-01-15T10:00:00Z",
    "updatedAt": "2024-12-20T14:30:00Z"
  }
}
```

### 4.3 Create User

```
POST /api/users
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "username": "jane.doe",
  "email": "jane@sentient.local",
  "password": "Password123!",
  "roleId": 3,
  "isActive": true
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "uuid": "550e8400-e29b-41d4-a716-446655440001",
    "username": "jane.doe",
    "email": "jane@sentient.local",
    "role": {
      "code": "MANAGER",
      "name": "Manager"
    },
    "isActive": true,
    "createdAt": "2024-12-27T10:00:00Z"
  }
}
```

### 4.4 Update User

```
PUT /api/users/:uuid
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "email": "jane.updated@sentient.local",
  "roleId": 2,
  "isActive": true
}
```

### 4.5 Delete User (Soft)

```
DELETE /api/users/:uuid
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "message": "User deleted successfully"
  }
}
```

---

## 5. Sales Orders

### 5.1 List Sales Orders

```
GET /api/sales-orders?page=1&limit=20&status=approved
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "uuid": "550e8400-e29b-41d4-a716-446655440010",
      "soNumber": "SO-202412-0001",
      "customer": {
        "uuid": "...",
        "name": "PT ABC Manufacturing"
      },
      "orderDate": "2024-12-27",
      "deadlineDate": "2025-01-03",
      "total": 6854250,
      "totalFormatted": "Rp 6.854.250",
      "status": "approved",
      "statusLabel": "Approved",
      "statusColor": "success",
      "itemCount": 3,
      "createdBy": "john.doe",
      "createdAt": "2024-12-27T10:00:00Z"
    }
  ],
  "pagination": { ... }
}
```

### 5.2 Get Sales Order Detail

```
GET /api/sales-orders/:uuid
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "uuid": "550e8400-e29b-41d4-a716-446655440010",
    "soNumber": "SO-202412-0001",
    "customer": {
      "uuid": "...",
      "code": "CUST-001",
      "name": "PT ABC Manufacturing",
      "address": "Jl. Industri No. 123, Bekasi",
      "phone": "021-88881111"
    },
    "orderDate": "2024-12-27",
    "deadlineDate": "2025-01-03",
    "customerPO": "PO-CUST-123",
    "items": [
      {
        "id": 1,
        "item": {
          "uuid": "...",
          "code": "ITM-001",
          "name": "Widget Pro X1"
        },
        "qty": 100,
        "price": 65000,
        "priceFormatted": "Rp 65.000",
        "discountPct": 0,
        "subtotal": 6500000,
        "subtotalFormatted": "Rp 6.500.000",
        "deliveredQty": 0
      }
    ],
    "subtotal": 6500000,
    "discountPct": 5,
    "discountAmount": 325000,
    "taxPct": 11,
    "taxAmount": 679250,
    "total": 6854250,
    "totalFormatted": "Rp 6.854.250",
    "status": "approved",
    "statusLabel": "Approved",
    "notes": "Urgent delivery",
    "approvedBy": {
      "uuid": "...",
      "username": "manager"
    },
    "approvedAt": "2024-12-27T11:00:00Z",
    "createdBy": {
      "uuid": "...",
      "username": "john.doe"
    },
    "createdAt": "2024-12-27T10:00:00Z",
    "updatedAt": "2024-12-27T11:00:00Z",
    "timeline": [
      {
        "status": "draft",
        "timestamp": "2024-12-27T10:00:00Z",
        "user": "john.doe"
      },
      {
        "status": "pending",
        "timestamp": "2024-12-27T10:30:00Z",
        "user": "john.doe"
      },
      {
        "status": "approved",
        "timestamp": "2024-12-27T11:00:00Z",
        "user": "manager"
      }
    ]
  }
}
```

### 5.3 Create Sales Order

```
POST /api/sales-orders
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "customerId": "550e8400-e29b-41d4-a716-446655440001",
  "orderDate": "2024-12-27",
  "deadlineDate": "2025-01-03",
  "customerPO": "PO-CUST-123",
  "discountPct": 5,
  "items": [
    {
      "itemId": "550e8400-e29b-41d4-a716-446655440002",
      "qty": 100,
      "price": 65000,
      "discountPct": 0
    }
  ],
  "notes": "Urgent delivery"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "uuid": "550e8400-e29b-41d4-a716-446655440010",
    "soNumber": "SO-202412-0001",
    "status": "draft",
    ...
  }
}
```

### 5.4 Update Sales Order

```
PUT /api/sales-orders/:uuid
Authorization: Bearer {accessToken}
```

**Request:** (Same as create, only draft status allowed)

### 5.5 Submit for Approval

```
POST /api/sales-orders/:uuid/submit
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "uuid": "...",
    "status": "pending",
    "statusLabel": "Pending Approval"
  }
}
```

### 5.6 Approve Sales Order

```
POST /api/sales-orders/:uuid/approve
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "notes": "Approved as per policy"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "uuid": "...",
    "status": "approved",
    "approvedAt": "2024-12-27T11:00:00Z"
  }
}
```

### 5.7 Reject Sales Order

```
POST /api/sales-orders/:uuid/reject
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "reason": "Credit limit exceeded"
}
```

---

## 6. Master Data

### 6.1 Customers

```
GET /api/customers?page=1&limit=20&segment=VIP
```

**Response Item:**
```json
{
  "uuid": "...",
  "code": "CUST-001",
  "name": "PT ABC Manufacturing",
  "address": "Jl. Industri No. 123, Bekasi",
  "phone": "021-88881111",
  "email": "purchasing@abc.com",
  "segment": "VIP",
  "segmentLabel": "VIP Customer",
  "creditLimit": 500000000,
  "creditLimitFormatted": "Rp 500.000.000",
  "lastOrderDate": "2024-12-20"
}
```

### 6.2 Items

```
GET /api/items?page=1&category=Finished+Goods
```

**Response Item:**
```json
{
  "uuid": "...",
  "code": "ITM-001",
  "name": "Widget Pro X1",
  "category": "Finished Goods",
  "stdCost": 45000,
  "sellPrice": 65000,
  "sellPriceFormatted": "Rp 65.000",
  "uom": "PCS",
  "minStock": 100,
  "currentStock": 500
}
```

### 6.3 Vendors

```
GET /api/vendors
```

**Response Item:**
```json
{
  "uuid": "...",
  "code": "VND-001",
  "name": "PT Baja Makmur",
  "address": "Jl. Industri Berat No. 1, Surabaya",
  "phone": "031-55551111",
  "otdScore": 95.5,
  "qualityScore": 98.0
}
```

---

## 7. AI Chat

### 7.1 Send Message

```
POST /api/ai/chat
Authorization: Bearer {accessToken}
```

**Request:**
```json
{
  "message": "Berapa total revenue bulan ini?",
  "sessionId": "optional-session-uuid"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "sessionId": "550e8400-e29b-41d4-a716-446655440020",
    "answer": "Total revenue bulan Desember 2024 adalah **Rp 5.234.000.000**, naik 12.5% dari bulan November.",
    "answerHtml": "<p>Total revenue bulan Desember 2024 adalah <strong>Rp 5.234.000.000</strong>...</p>",
    "confidence": 0.95,
    "confidenceLabel": "High",
    "sources": [
      {
        "table": "t_so",
        "period": "December 2024",
        "recordCount": 156
      }
    ],
    "charts": [
      {
        "type": "line",
        "title": "Revenue Trend",
        "data": {
          "labels": ["Week 1", "Week 2", "Week 3", "Week 4"],
          "datasets": [
            {
              "label": "Revenue",
              "data": [1200000000, 1350000000, 1400000000, 1284000000]
            }
          ]
        }
      }
    ],
    "tables": [
      {
        "title": "Top 5 Customers",
        "headers": ["Customer", "Revenue", "Orders"],
        "rows": [
          ["PT ABC Manufacturing", "Rp 890.000.000", "15"],
          ["CV XYZ Trading", "Rp 756.000.000", "12"]
        ]
      }
    ],
    "suggestedActions": [
      {
        "label": "Lihat detail breakdown",
        "action": "drill_down",
        "params": { "type": "revenue_by_customer" }
      },
      {
        "label": "Export ke PDF",
        "action": "export",
        "params": { "format": "pdf" }
      }
    ],
    "timestamp": "2024-12-27T10:00:00Z"
  }
}
```

### 7.2 Get Chat History

```
GET /api/ai/chat/history?limit=20
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "sessionId": "...",
      "title": "Revenue bulan ini",
      "lastMessage": "Total revenue bulan Desember...",
      "messageCount": 5,
      "createdAt": "2024-12-27T10:00:00Z",
      "updatedAt": "2024-12-27T10:15:00Z"
    }
  ]
}
```

### 7.3 Get Chat Session

```
GET /api/ai/chat/:sessionId
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "sessionId": "...",
    "messages": [
      {
        "role": "user",
        "content": "Berapa revenue bulan ini?",
        "timestamp": "2024-12-27T10:00:00Z"
      },
      {
        "role": "assistant",
        "content": "Total revenue bulan Desember...",
        "confidence": 0.95,
        "sources": [...],
        "timestamp": "2024-12-27T10:00:05Z"
      }
    ]
  }
}
```

---

## 8. Alerts

### 8.1 List Alerts

```
GET /api/alerts?status=unread&severity=high
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "uuid": "...",
      "type": "LowStock",
      "typeLabel": "Low Stock Alert",
      "typeIcon": "inventory",
      "severity": "high",
      "severityLabel": "High",
      "severityColor": "danger",
      "title": "Low Stock Alert: Steel Plate 3mm",
      "description": "Stock level at 140 units, below minimum 50.",
      "entity": {
        "type": "Item",
        "id": "...",
        "name": "Steel Plate 3mm"
      },
      "isRead": false,
      "isResolved": false,
      "createdAt": "2024-12-27T09:00:00Z"
    }
  ],
  "pagination": { ... }
}
```

### 8.2 Get Unread Count

```
GET /api/alerts/unread-count
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "total": 5,
    "critical": 1,
    "high": 2,
    "medium": 2,
    "low": 0
  }
}
```

### 8.3 Acknowledge Alert

```
POST /api/alerts/:uuid/acknowledge
Authorization: Bearer {accessToken}
```

---

## 9. Dropdown Options

### 9.1 Get Customers (Dropdown)

```
GET /api/options/customers?search=abc
Authorization: Bearer {accessToken}
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    { "value": "uuid-1", "label": "PT ABC Manufacturing", "code": "CUST-001" },
    { "value": "uuid-2", "label": "CV ABC Trading", "code": "CUST-005" }
  ]
}
```

### 9.2 Get Items (Dropdown)

```
GET /api/options/items?search=widget
```

### 9.3 Get Roles (Dropdown)

```
GET /api/options/roles
```

### 9.4 Get Status Options

```
GET /api/options/statuses?entity=sales-order
```

**Response (200):**
```json
{
  "success": true,
  "data": [
    { "value": "draft", "label": "Draft", "color": "secondary" },
    { "value": "pending", "label": "Pending Approval", "color": "warning" },
    { "value": "approved", "label": "Approved", "color": "success" },
    { "value": "cancelled", "label": "Cancelled", "color": "danger" }
  ]
}
```

---

## 10. TypeScript Interfaces

Frontend dapat menggunakan interfaces berikut:

```typescript
// types/api.ts

// Base Response
interface ApiResponse<T> {
  success: boolean;
  data: T;
  meta: {
    timestamp: string;
    requestId: string;
  };
}

interface PaginatedResponse<T> extends ApiResponse<T[]> {
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

interface ApiError {
  success: false;
  error: {
    code: string;
    message: string;
    details?: Record<string, string[]>;
  };
}

// Auth
interface LoginRequest {
  username: string;
  password: string;
}

interface LoginResponse {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
  user: User;
}

interface User {
  uuid: string;
  username: string;
  email: string;
  role: Role;
  permissions: string[];
}

interface Role {
  id?: number;
  code: string;
  name: string;
}

// Sales Order
interface SalesOrder {
  uuid: string;
  soNumber: string;
  customer: CustomerSummary;
  orderDate: string;
  deadlineDate?: string;
  items: SalesOrderItem[];
  subtotal: number;
  discountPct: number;
  discountAmount: number;
  taxPct: number;
  taxAmount: number;
  total: number;
  totalFormatted: string;
  status: SalesOrderStatus;
  statusLabel: string;
  notes?: string;
  createdBy: UserSummary;
  createdAt: string;
}

interface SalesOrderItem {
  id: number;
  item: ItemSummary;
  qty: number;
  price: number;
  discountPct: number;
  subtotal: number;
  deliveredQty: number;
}

type SalesOrderStatus = 'draft' | 'pending' | 'approved' | 'in_progress' | 'completed' | 'cancelled';

// Master Data
interface Customer {
  uuid: string;
  code: string;
  name: string;
  address?: string;
  phone?: string;
  email?: string;
  segment: 'VIP' | 'Regular';
  creditLimit: number;
}

interface Item {
  uuid: string;
  code: string;
  name: string;
  category: string;
  stdCost: number;
  sellPrice: number;
  uom: string;
  minStock: number;
}

// Dropdown Option
interface Option {
  value: string;
  label: string;
  code?: string;
  color?: string;
}

// AI Chat
interface ChatRequest {
  message: string;
  sessionId?: string;
}

interface ChatResponse {
  sessionId: string;
  answer: string;
  answerHtml: string;
  confidence: number;
  sources: ChatSource[];
  charts?: ChartData[];
  tables?: TableData[];
  suggestedActions: SuggestedAction[];
}

interface ChatSource {
  table: string;
  period: string;
  recordCount?: number;
}

interface SuggestedAction {
  label: string;
  action: string;
  params?: Record<string, any>;
}
```

---

## 11. API Versioning

### 11.1 Version Header

API version dikomunikasikan via header untuk backward compatibility:

```http
X-API-Version: 2024-12-27
```

| Header             | Description                               |
| ------------------ | ----------------------------------------- |
| `X-API-Version`    | Date-based version (YYYY-MM-DD)           |
| `X-API-Deprecated` | Set to `true` if using deprecated version |
| `X-API-Sunset`     | Date when this version will be removed    |

### 11.2 Version Response

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "timestamp": "2024-12-27T10:00:00Z",
    "requestId": "550e8400-e29b-41d4-a716-446655440000",
    "apiVersion": "2024-12-27"
  }
}
```

### 11.3 Deprecation Policy

| Phase            | Timeline   | Action                                       |
| ---------------- | ---------- | -------------------------------------------- |
| **Announcement** | T-6 months | New version released, old version deprecated |
| **Warning**      | T-3 months | `X-API-Deprecated: true` header added        |
| **Sunset**       | T-0        | Old version returns `410 Gone`               |

### 11.4 Breaking Changes Log

| Version      | Date    | Breaking Changes |
| ------------ | ------- | ---------------- |
| `2024-12-27` | Current | Initial release  |

### 11.5 Migration Guide Template

Saat ada breaking change:

```markdown
## Migration from YYYY-MM-DD to YYYY-MM-DD

### Breaking Changes

1. **Endpoint renamed**
   - Before: `GET /api/orders`
   - After: `GET /api/sales-orders`

2. **Field renamed**
   - Before: `total_amount`
   - After: `total`

3. **Field removed**
   - `legacy_field` removed, use `new_field` instead

### Migration Steps

1. Update API base URL if changed
2. Update request/response types
3. Test all endpoints
4. Remove deprecated field usage
```

### 11.6 Health Check with Version

```
GET /api/health
```

```json
{
  "status": "healthy",
  "version": "2024-12-27",
  "uptime": "24h 30m",
  "services": {
    "database": "healthy",
    "redis": "healthy",
    "nats": "healthy",
    "ai": "healthy"
  }
}
```

---

*Document Version: 1.1*
*Last Updated: 2024-12-27*

