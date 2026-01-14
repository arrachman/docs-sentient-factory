# 🔧 Backend Development Specification

> **Parent Docs:** [Architecture](architecture.md), [System Analysis](system_analysis.md), [BPA-1](bpa-1.md), [BPA-2](bpa-2.md), [BPA-3](bpa-3.md)
> **Last Sync:** 2024-12-27

---

## 1. Tech Stack & Setup

### 1.1 Technology Stack

| Category   | Technology      | Version | Purpose                 |
| ---------- | --------------- | ------- | ----------------------- |
| Framework  | NestJS          | 10.x    | Backend framework       |
| Language   | TypeScript      | 5.x     | Type safety             |
| Database   | PostgreSQL      | 15.x    | Primary database        |
| ORM        | TypeORM         | 0.3.x   | Database access         |
| Auth       | Passport + JWT  | -       | Authentication          |
| Validation | class-validator | -       | DTO validation          |
| Messaging  | NATS            | 2.x     | Python AI communication |
| API Docs   | Swagger         | -       | OpenAPI documentation   |
| Cache      | Redis           | 7.x     | Session & caching       |

### 1.2 Project Setup

```bash
# Create NestJS project
npx @nestjs/cli new sentient-backend

# Install dependencies
npm install @nestjs/typeorm typeorm pg
npm install @nestjs/passport passport passport-jwt
npm install @nestjs/jwt bcrypt class-validator class-transformer
npm install @nestjs/swagger swagger-ui-express
npm install @nestjs/microservices nats
npm install @nestjs/cache-manager cache-manager-redis-store redis

# Development
npm run start:dev

# Build
npm run build
```

### 1.3 Folder Structure

```
src/
├── main.ts                    # App entry
├── app.module.ts              # Root module
├── common/
│   ├── decorators/            # Custom decorators
│   │   ├── current-user.decorator.ts
│   │   └── permissions.decorator.ts
│   ├── guards/                # Auth guards
│   │   ├── jwt-auth.guard.ts
│   │   └── permissions.guard.ts
│   ├── interceptors/          # Response interceptors
│   ├── filters/               # Exception filters
│   ├── pipes/                 # Validation pipes
│   └── dto/                   # Shared DTOs
├── config/                    # Configuration
│   ├── database.config.ts
│   ├── jwt.config.ts
│   └── nats.config.ts
├── modules/
│   ├── auth/                  # Authentication
│   ├── users/                 # User management
│   ├── roles/                 # Role & permissions
│   ├── dashboard/             # Dashboard KPIs
│   ├── customers/             # Master customer
│   ├── items/                 # Master item
│   ├── vendors/               # Master vendor
│   ├── sales-orders/          # Sales order
│   ├── delivery-orders/       # Delivery order
│   ├── purchase-orders/       # Purchase order
│   ├── work-orders/           # Work order
│   ├── inventory/             # Inventory
│   ├── ai/                    # AI Chat integration
│   ├── alerts/                # Notifications
│   └── reports/               # Report generation
└── database/
    ├── entities/              # TypeORM entities
    ├── migrations/            # DB migrations
    └── seeds/                 # Initial data
```

### 1.4 Environment Config

```env
# .env
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sentient_factory
DB_USER=postgres
DB_PASS=secret

# JWT
JWT_SECRET=your-super-secret-key
JWT_EXPIRES_IN=1d
JWT_REFRESH_SECRET=your-refresh-secret
JWT_REFRESH_EXPIRES_IN=7d

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# NATS (Python AI Service)
NATS_URL=nats://localhost:4222

# App
APP_PORT=8000
APP_ENV=development
```

---

## 2. Authentication & Authorization

### 2.1 JWT Flow

```mermaid
sequenceDiagram
    participant Client
    participant NestJS
    participant DB
    
    Client->>NestJS: POST /api/auth/login {username, password}
    NestJS->>DB: Find user by username
    DB-->>NestJS: User data
    NestJS->>NestJS: Verify password (bcrypt)
    NestJS->>NestJS: Generate JWT tokens
    NestJS-->>Client: {accessToken, refreshToken, user}
    
    Client->>NestJS: GET /api/protected (Bearer token)
    NestJS->>NestJS: Validate JWT
    NestJS-->>Client: Protected resource
```

### 2.2 Auth Module

```typescript
// src/modules/auth/auth.service.ts

@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
  ) {}

  async validateUser(username: string, password: string): Promise<User> {
    const user = await this.usersService.findByUsername(username);
    if (!user || !user.is_active) {
      throw new UnauthorizedException('Invalid credentials');
    }
    
    const isMatch = await bcrypt.compare(password, user.password_hash);
    if (!isMatch) {
      throw new UnauthorizedException('Invalid credentials');
    }
    
    return user;
  }

  async login(user: User) {
    const payload = { 
      sub: user.uuid, 
      username: user.username,
      permissions: await this.getPermissions(user.role_id)
    };
    
    return {
      accessToken: this.jwtService.sign(payload),
      refreshToken: this.jwtService.sign(payload, { 
        secret: process.env.JWT_REFRESH_SECRET,
        expiresIn: '7d' 
      }),
      user: new UserResponseDto(user),
    };
  }
}
```

### 2.3 JWT Strategy

```typescript
// src/modules/auth/strategies/jwt.strategy.ts

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: JwtPayload) {
    return { 
      uuid: payload.sub, 
      username: payload.username,
      permissions: payload.permissions
    };
  }
}
```

### 2.4 Permission Guard (RBAC)

```typescript
// src/common/guards/permissions.guard.ts

@Injectable()
export class PermissionsGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredPermissions = this.reflector.getAllAndOverride<string[]>(
      'permissions',
      [context.getHandler(), context.getClass()]
    );
    
    if (!requiredPermissions) {
      return true;
    }
    
    const { user } = context.switchToHttp().getRequest();
    return requiredPermissions.some(perm => 
      user.permissions?.includes(perm)
    );
  }
}

// Usage in controller
@Get()
@UseGuards(JwtAuthGuard, PermissionsGuard)
@Permissions('so.read')
findAll() {
  return this.salesOrderService.findAll();
}
```

### 2.5 Custom Decorators

```typescript
// src/common/decorators/current-user.decorator.ts
export const CurrentUser = createParamDecorator(
  (data: string, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return data ? request.user?.[data] : request.user;
  },
);

// src/common/decorators/permissions.decorator.ts
export const Permissions = (...permissions: string[]) => 
  SetMetadata('permissions', permissions);

// Usage
@Get('profile')
getProfile(@CurrentUser() user: JwtPayload) {
  return this.usersService.findByUuid(user.uuid);
}
```

---

## 3. API Endpoints

### 3.1 Auth Module

| Method | Endpoint                    | Description               | Auth |
| ------ | --------------------------- | ------------------------- | ---- |
| POST   | `/api/auth/login`           | Login                     | No   |
| POST   | `/api/auth/logout`          | Logout (invalidate token) | Yes  |
| POST   | `/api/auth/refresh`         | Refresh token             | Yes  |
| POST   | `/api/auth/forgot-password` | Request reset link        | No   |
| POST   | `/api/auth/reset-password`  | Reset with token          | No   |

**Login Request:**
```json
{
  "username": "john.doe",
  "password": "Password123!"
}
```

**Login Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "uuid": "550e8400-e29b-41d4-a716-446655440000",
    "username": "john.doe",
    "email": "john@example.com",
    "role": "Admin"
  }
}
```

### 3.2 Dashboard Module

| Method | Endpoint                         | Description       | Permission     |
| ------ | -------------------------------- | ----------------- | -------------- |
| GET    | `/api/dashboard/summary`         | KPI summary       | dashboard.view |
| GET    | `/api/dashboard/revenue-trend`   | 30-day revenue    | dashboard.view |
| GET    | `/api/dashboard/top-customers`   | Top 5 customers   | dashboard.view |
| GET    | `/api/dashboard/recent-activity` | Recent activities | dashboard.view |

**Summary Response:**
```json
{
  "revenue": 5234000000,
  "revenueChange": 12.5,
  "pendingOrders": 23,
  "productionOEE": 87.5,
  "activeAlerts": 5
}
```

### 3.3 Users Module

| Method | Endpoint           | Description      | Permission  |
| ------ | ------------------ | ---------------- | ----------- |
| GET    | `/api/users`       | List users       | user.read   |
| GET    | `/api/users/:uuid` | Get user by UUID | user.read   |
| POST   | `/api/users`       | Create user      | user.create |
| PUT    | `/api/users/:uuid` | Update user      | user.update |
| DELETE | `/api/users/:uuid` | Soft delete user | user.delete |

### 3.4 Roles Module

| Method | Endpoint                     | Description               | Permission  |
| ------ | ---------------------------- | ------------------------- | ----------- |
| GET    | `/api/roles`                 | List roles                | role.read   |
| GET    | `/api/roles/:id`             | Get role with permissions | role.read   |
| POST   | `/api/roles`                 | Create role               | role.create |
| PUT    | `/api/roles/:id/permissions` | Update permissions        | role.update |

### 3.5 Sales Order Module

| Method | Endpoint                          | Description          | Permission |
| ------ | --------------------------------- | -------------------- | ---------- |
| GET    | `/api/sales-orders`               | List with pagination | so.read    |
| GET    | `/api/sales-orders/:uuid`         | Get detail           | so.read    |
| POST   | `/api/sales-orders`               | Create SO            | so.create  |
| PUT    | `/api/sales-orders/:uuid`         | Update SO            | so.update  |
| POST   | `/api/sales-orders/:uuid/submit`  | Submit for approval  | so.update  |
| POST   | `/api/sales-orders/:uuid/approve` | Approve SO           | so.approve |
| POST   | `/api/sales-orders/:uuid/reject`  | Reject SO            | so.approve |
| DELETE | `/api/sales-orders/:uuid`         | Cancel SO            | so.delete  |

**Create SO Request:**
```json
{
  "customerId": "550e8400-e29b-41d4-a716-446655440001",
  "orderDate": "2024-12-27",
  "customerPO": "PO-CUST-123",
  "items": [
    {
      "itemId": "550e8400-e29b-41d4-a716-446655440002",
      "qty": 100,
      "price": 65000,
      "discountPct": 5
    }
  ],
  "notes": "Urgent delivery"
}
```

**SO Response:**
```json
{
  "uuid": "550e8400-e29b-41d4-a716-446655440010",
  "soNumber": "SO-202412-0001",
  "customer": {
    "uuid": "...",
    "name": "PT ABC Manufacturing"
  },
  "orderDate": "2024-12-27",
  "status": "draft",
  "items": [...],
  "subtotal": 6175000,
  "discountAmount": 0,
  "taxAmount": 679250,
  "total": 6854250,
  "createdBy": "john.doe",
  "createdAt": "2024-12-27T10:00:00Z"
}
```

### 3.6 Delivery Order Module

| Method | Endpoint                             | Description       | Permission |
| ------ | ------------------------------------ | ----------------- | ---------- |
| GET    | `/api/delivery-orders`               | List DOs          | do.read    |
| GET    | `/api/delivery-orders/:uuid`         | Get DO detail     | do.read    |
| POST   | `/api/delivery-orders`               | Create DO from SO | do.create  |
| PUT    | `/api/delivery-orders/:uuid/ship`    | Mark as shipped   | do.update  |
| PUT    | `/api/delivery-orders/:uuid/deliver` | Mark as delivered | do.update  |

### 3.7 Purchase Order Module

| Method | Endpoint                             | Description    | Permission |
| ------ | ------------------------------------ | -------------- | ---------- |
| GET    | `/api/purchase-orders`               | List POs       | po.read    |
| GET    | `/api/purchase-orders/:uuid`         | Get PO detail  | po.read    |
| POST   | `/api/purchase-orders`               | Create PO      | po.create  |
| PUT    | `/api/purchase-orders/:uuid`         | Update PO      | po.update  |
| POST   | `/api/purchase-orders/:uuid/approve` | Approve PO     | po.approve |
| POST   | `/api/purchase-orders/:uuid/receive` | Record receipt | po.update  |

### 3.8 Work Order Module

| Method | Endpoint                          | Description      | Permission |
| ------ | --------------------------------- | ---------------- | ---------- |
| GET    | `/api/work-orders`                | List WOs         | wo.read    |
| GET    | `/api/work-orders/:uuid`          | Get WO detail    | wo.read    |
| POST   | `/api/work-orders`                | Create WO        | wo.create  |
| POST   | `/api/work-orders/:uuid/start`    | Start production | wo.update  |
| POST   | `/api/work-orders/:uuid/output`   | Log output       | wo.update  |
| POST   | `/api/work-orders/:uuid/complete` | Complete WO      | wo.update  |

### 3.9 Inventory Module

| Method | Endpoint                         | Description      | Permission   |
| ------ | -------------------------------- | ---------------- | ------------ |
| GET    | `/api/inventory`                 | Stock overview   | inv.read     |
| GET    | `/api/inventory/:itemId/history` | Stock card       | inv.read     |
| POST   | `/api/inventory/adjustments`     | Stock adjustment | inv.adjust   |
| POST   | `/api/inventory/transfers`       | Stock transfer   | inv.transfer |
| GET    | `/api/inventory/low-stock`       | Low stock items  | inv.read     |

---

## 4. Python AI Integration

### 4.1 Architecture

```mermaid
graph LR
    Client[Frontend] --> Nest[NestJS API]
    Nest --> NATS[NATS Message Broker]
    NATS --> Python[Python AI Service]
    Python --> LLM[Qwen-72B LLM]
    Python --> DB[(PostgreSQL)]
```

### 4.2 NATS Configuration

```typescript
// src/config/nats.config.ts

export const natsConfig = {
  transport: Transport.NATS,
  options: {
    servers: [process.env.NATS_URL || 'nats://localhost:4222'],
    queue: 'ai_requests',
  },
};

// src/app.module.ts
@Module({
  imports: [
    ClientsModule.register([
      {
        name: 'AI_SERVICE',
        ...natsConfig,
      },
    ]),
  ],
})
```

### 4.3 AI Service Client

```typescript
// src/modules/ai/ai.service.ts

@Injectable()
export class AiService {
  constructor(
    @Inject('AI_SERVICE') private aiClient: ClientProxy,
  ) {}

  async chat(userId: string, message: string): Promise<AiResponse> {
    const pattern = 'ai.chat';
    const payload = {
      userId,
      message,
      timestamp: new Date().toISOString(),
    };

    return firstValueFrom(
      this.aiClient.send<AiResponse>(pattern, payload).pipe(
        timeout(30000), // 30s timeout
        catchError(err => {
          throw new ServiceUnavailableException('AI Service unavailable');
        }),
      ),
    );
  }

  async getInsights(): Promise<InsightResponse[]> {
    return firstValueFrom(
      this.aiClient.send('ai.insights', {}).pipe(timeout(10000)),
    );
  }
}
```

### 4.4 AI Chat Endpoint

| Method | Endpoint               | Description        | Permission |
| ------ | ---------------------- | ------------------ | ---------- |
| POST   | `/api/ai/chat`         | Send message to AI | ai.chat    |
| GET    | `/api/ai/chat/history` | Get chat history   | ai.chat    |
| POST   | `/api/ai/chat/new`     | Start new session  | ai.chat    |
| GET    | `/api/ai/insights`     | Get AI insights    | ai.view    |

**Chat Request:**
```json
{
  "message": "Berapa total revenue bulan ini?",
  "sessionId": "optional-session-uuid"
}
```

**Chat Response:**
```json
{
  "answer": "Total revenue bulan Desember 2024 adalah Rp 5,234,000,000, naik 12.5% dari bulan lalu.",
  "confidence": 0.95,
  "sources": [
    {"table": "t_so", "period": "Dec 2024"}
  ],
  "charts": [
    {
      "type": "line",
      "data": {...}
    }
  ],
  "suggestedActions": [
    "Lihat detail breakdown per customer",
    "Compare dengan target"
  ]
}
```

### 4.5 Alert Endpoints

| Method | Endpoint                      | Description        | Permission   |
| ------ | ----------------------------- | ------------------ | ------------ |
| GET    | `/api/alerts`                 | List alerts        | alert.read   |
| GET    | `/api/alerts/unread`          | Unread count       | alert.read   |
| POST   | `/api/alerts/:id/acknowledge` | Acknowledge        | alert.update |
| PUT    | `/api/alerts/settings`        | Update preferences | alert.update |

---

## 5. Database Patterns

### 5.1 Entity Example (Dual ID)

```typescript
// src/database/entities/sales-order.entity.ts

@Entity('t_so')
export class SalesOrder {
  @PrimaryGeneratedColumn('increment')
  id: number;  // Internal (fast JOINs)

  @Column({ type: 'uuid', unique: true, default: () => 'gen_random_uuid()' })
  uuid: string;  // External (API exposure)

  @Column()
  so_number: string;

  @ManyToOne(() => Customer)
  @JoinColumn({ name: 'cust_id' })
  customer: Customer;

  @Column({ type: 'date' })
  order_dt: Date;

  @Column({ type: 'numeric', precision: 15, scale: 2 })
  total_amt: number;

  @Column({ default: 'draft' })
  status: string;

  @OneToMany(() => SalesOrderDetail, detail => detail.salesOrder)
  details: SalesOrderDetail[];

  @CreateDateColumn()
  created_at: Date;

  @Column()
  created_by: number;

  @UpdateDateColumn()
  updated_at: Date;

  @DeleteDateColumn()
  deleted_at: Date;
}
```

### 5.2 Repository Pattern

```typescript
// src/modules/sales-orders/sales-orders.repository.ts

@Injectable()
export class SalesOrdersRepository {
  constructor(
    @InjectRepository(SalesOrder)
    private repo: Repository<SalesOrder>,
  ) {}

  async findByUuid(uuid: string): Promise<SalesOrder> {
    const order = await this.repo.findOne({
      where: { uuid },
      relations: ['customer', 'details', 'details.item'],
    });
    
    if (!order) {
      throw new NotFoundException('Sales Order not found');
    }
    
    return order;
  }

  async findAll(query: QueryDto): Promise<PaginatedResult<SalesOrder>> {
    const qb = this.repo.createQueryBuilder('so')
      .leftJoinAndSelect('so.customer', 'customer')
      .where('so.deleted_at IS NULL');

    if (query.search) {
      qb.andWhere('so.so_number ILIKE :search', { 
        search: `%${query.search}%` 
      });
    }

    if (query.status) {
      qb.andWhere('so.status = :status', { status: query.status });
    }

    const [data, total] = await qb
      .skip((query.page - 1) * query.limit)
      .take(query.limit)
      .orderBy('so.created_at', 'DESC')
      .getManyAndCount();

    return { data, total, page: query.page, limit: query.limit };
  }
}
```

### 5.3 Query Patterns

```typescript
// Complex query with internal ID (fast)
async getOrderWithDetails(orderId: number) {
  return this.dataSource.query(`
    SELECT 
      so.*,
      json_agg(sod.*) as details
    FROM t_so so
    LEFT JOIN t_so_detail sod ON so.id = sod.so_id
    WHERE so.id = $1
    GROUP BY so.id
  `, [orderId]);
}

// API lookup by UUID, then use internal ID
async findByUuidThenJoin(uuid: string) {
  const { id } = await this.repo.findOne({ 
    where: { uuid }, 
    select: ['id'] 
  });
  return this.getOrderWithDetails(id);
}
```

---

## 6. Postman Collection

### 6.1 Collection Structure

```
Sentient Factory API/
├── 📁 Auth
│   ├── Login
│   ├── Refresh Token
│   ├── Logout
│   └── Forgot Password
├── 📁 Dashboard
│   ├── Get Summary
│   ├── Revenue Trend
│   ├── Top Customers
│   └── Recent Activity
├── 📁 Users
│   ├── List Users
│   ├── Get User
│   ├── Create User
│   ├── Update User
│   └── Delete User
├── 📁 Roles
│   ├── List Roles
│   ├── Get Role
│   ├── Create Role
│   └── Update Permissions
├── 📁 Master Data
│   ├── 📁 Customers
│   ├── 📁 Items
│   └── 📁 Vendors
├── 📁 Sales
│   ├── 📁 Sales Orders
│   │   ├── List SO
│   │   ├── Get SO
│   │   ├── Create SO
│   │   ├── Update SO
│   │   ├── Submit SO
│   │   ├── Approve SO
│   │   └── Reject SO
│   └── 📁 Delivery Orders
├── 📁 Procurement
│   └── 📁 Purchase Orders
├── 📁 Production
│   └── 📁 Work Orders
├── 📁 Inventory
│   ├── Stock Overview
│   ├── Stock Card
│   ├── Adjustment
│   └── Transfer
├── 📁 AI
│   ├── Chat
│   ├── Chat History
│   └── Insights
└── 📁 Reports
    ├── Sales Summary
    ├── PO Status
    └── Stock Balance
```

### 6.2 Environment Variables

```json
{
  "id": "sentient-env",
  "name": "Sentient Factory",
  "values": [
    { "key": "baseUrl", "value": "http://localhost:8000/api" },
    { "key": "token", "value": "" },
    { "key": "refreshToken", "value": "" }
  ]
}
```

### 6.3 Auto-Set Token Script

```javascript
// Add to Login request "Tests" tab
if (pm.response.code === 200) {
  const response = pm.response.json();
  pm.environment.set("token", response.accessToken);
  pm.environment.set("refreshToken", response.refreshToken);
}
```

### 6.4 Auth Header Pre-request

```javascript
// Add to collection "Pre-request Script"
pm.request.headers.add({
  key: "Authorization",
  value: "Bearer " + pm.environment.get("token")
});
```

---

## 7. Error Handling

### 7.1 Standard Error Response

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "details": {
    "customerId": ["Customer is required"],
    "items": ["At least one item is required"]
  },
  "timestamp": "2024-12-27T10:00:00Z",
  "path": "/api/sales-orders"
}
```

### 7.2 Exception Filter

```typescript
// src/common/filters/http-exception.filter.ts

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    let status = HttpStatus.INTERNAL_SERVER_ERROR;
    let message = 'Internal server error';
    let details = null;

    if (exception instanceof HttpException) {
      status = exception.getStatus();
      const exResponse = exception.getResponse();
      message = typeof exResponse === 'string' 
        ? exResponse 
        : (exResponse as any).message;
      details = (exResponse as any).details;
    }

    response.status(status).json({
      statusCode: status,
      error: HttpStatus[status],
      message,
      details,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

### 7.3 HTTP Status Codes

| Code | Usage                         |
| ---- | ----------------------------- |
| 200  | Success                       |
| 201  | Created                       |
| 400  | Validation error              |
| 401  | Unauthorized                  |
| 403  | Forbidden (no permission)     |
| 404  | Not found                     |
| 409  | Conflict (duplicate)          |
| 422  | Business rule violation       |
| 500  | Server error                  |
| 503  | Service unavailable (AI down) |

---

## 8. Vault & Secret Management

### 8.1 Overview

Gunakan **HashiCorp Vault** untuk menyimpan secrets dengan aman. Tidak ada secrets di `.env` atau code.

```mermaid
graph LR
    App[NestJS Backend] --> Vault[HashiCorp Vault]
    Vault --> Secrets[(Encrypted Secrets)]
    App --> |env: VAULT_ADDR, VAULT_TOKEN| Vault
```

### 8.2 Required Secrets

| Category     | Secret Key           | Description               | Example                |
| ------------ | -------------------- | ------------------------- | ---------------------- |
| **Database** | `DB_HOST`            | PostgreSQL host           | localhost              |
|              | `DB_PORT`            | PostgreSQL port           | 5432                   |
|              | `DB_NAME`            | Database name             | sentient_factory       |
|              | `DB_USER`            | Database user             | postgres               |
|              | `DB_PASS`            | Database password         | ********               |
| **Redis**    | `REDIS_HOST`         | Redis host                | localhost              |
|              | `REDIS_PORT`         | Redis port                | 6379                   |
|              | `REDIS_PASS`         | Redis password (optional) | ********               |
| **JWT**      | `JWT_SECRET`         | Access token secret       | (256-bit random)       |
|              | `JWT_REFRESH_SECRET` | Refresh token secret      | (256-bit random)       |
|              | `JWT_EXPIRES_IN`     | Token expiry              | 1d                     |
| **NATS**     | `NATS_URL`           | Message broker URL        | nats://localhost:4222  |
|              | `NATS_USER`          | NATS username (optional)  | sentient               |
|              | `NATS_PASS`          | NATS password (optional)  | ********               |
| **AI/LLM**   | `GEMINI_API_KEY`     | Google Gemini API key     | AIzaSy...              |
|              | `OLLAMA_HOST`        | Local LLM host            | localhost:11434        |
|              | `LLM_PROVIDER`       | gemini / ollama           | gemini                 |
| **Email**    | `SMTP_HOST`          | SMTP server               | smtp.gmail.com         |
|              | `SMTP_PORT`          | SMTP port                 | 587                    |
|              | `SMTP_USER`          | SMTP username             | noreply@sentient.local |
|              | `SMTP_PASS`          | SMTP password             | ********               |
| **WhatsApp** | `WA_PHONE_ID`        | WhatsApp Business ID      | 123456789              |
|              | `WA_ACCESS_TOKEN`    | Meta API token            | EAAx...                |
| **App**      | `APP_SECRET`         | App encryption key        | (256-bit random)       |
|              | `CORS_ORIGINS`       | Allowed origins           | http://localhost:3000  |

### 8.3 Vault Setup (Development)

```bash
# Install Vault (Mac)
brew install vault

# Start dev server (WARNING: data tidak persist)
vault server -dev

# Export address & token
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN='hvs.xxxxxxxxxxxx'

# Enable KV secrets engine
vault secrets enable -path=sentient kv-v2
```

### 8.4 Store Secrets

```bash
# Store Database secrets
vault kv put sentient/database \
  host=localhost \
  port=5432 \
  name=sentient_factory \
  user=postgres \
  pass=your-db-password

# Store JWT secrets
vault kv put sentient/jwt \
  secret=$(openssl rand -base64 32) \
  refresh_secret=$(openssl rand -base64 32) \
  expires_in=1d

# Store AI secrets
vault kv put sentient/ai \
  gemini_api_key=AIzaSyB6mT5YVLmjSgYiWTCJmZfXpOmcv0GtSBo \
  llm_provider=gemini \
  ollama_host=localhost:11434

# Store Redis secrets
vault kv put sentient/redis \
  host=localhost \
  port=6379

# Store NATS secrets
vault kv put sentient/nats \
  url=nats://localhost:4222

# Store Email secrets
vault kv put sentient/email \
  host=smtp.gmail.com \
  port=587 \
  user=noreply@sentient.local \
  pass=your-smtp-password

# Store App secrets
vault kv put sentient/app \
  secret=$(openssl rand -base64 32) \
  cors_origins=http://localhost:3000
```

### 8.5 NestJS Vault Integration

```bash
# Install Vault client
npm install node-vault @nestjs/config
```

```typescript
// src/config/vault.config.ts

import * as vault from 'node-vault';

export class VaultService {
  private client: vault.client;
  private secrets: Record<string, any> = {};

  async initialize() {
    this.client = vault({
      apiVersion: 'v1',
      endpoint: process.env.VAULT_ADDR || 'http://127.0.0.1:8200',
      token: process.env.VAULT_TOKEN,
    });

    // Load all secrets
    await this.loadSecrets('database');
    await this.loadSecrets('jwt');
    await this.loadSecrets('redis');
    await this.loadSecrets('nats');
    await this.loadSecrets('ai');
    await this.loadSecrets('email');
    await this.loadSecrets('app');
  }

  private async loadSecrets(path: string) {
    try {
      const result = await this.client.read(`sentient/data/${path}`);
      this.secrets[path] = result.data.data;
    } catch (error) {
      console.error(`Failed to load secrets from ${path}:`, error.message);
    }
  }

  get(path: string, key: string): string {
    return this.secrets[path]?.[key] || '';
  }

  // Convenience getters
  get database() {
    return {
      host: this.get('database', 'host'),
      port: parseInt(this.get('database', 'port')) || 5432,
      name: this.get('database', 'name'),
      user: this.get('database', 'user'),
      pass: this.get('database', 'pass'),
    };
  }

  get jwt() {
    return {
      secret: this.get('jwt', 'secret'),
      refreshSecret: this.get('jwt', 'refresh_secret'),
      expiresIn: this.get('jwt', 'expires_in') || '1d',
    };
  }

  get ai() {
    return {
      provider: this.get('ai', 'llm_provider'),
      geminiKey: this.get('ai', 'gemini_api_key'),
      ollamaHost: this.get('ai', 'ollama_host'),
    };
  }
}

export const vaultService = new VaultService();
```

### 8.6 Usage in Modules

```typescript
// src/config/database.config.ts

import { vaultService } from './vault.config';

export const databaseConfig = async () => {
  await vaultService.initialize();
  const db = vaultService.database;
  
  return {
    type: 'postgres',
    host: db.host,
    port: db.port,
    database: db.name,
    username: db.user,
    password: db.pass,
    entities: [__dirname + '/../**/*.entity{.ts,.js}'],
    synchronize: false,
  };
};

// src/app.module.ts
@Module({
  imports: [
    TypeOrmModule.forRootAsync({
      useFactory: databaseConfig,
    }),
  ],
})
export class AppModule {}
```

### 8.7 Production Setup (Docker)

```yaml
# docker-compose.yml

version: '3.8'
services:
  vault:
    image: vault:1.15
    cap_add:
      - IPC_LOCK
    ports:
      - "8200:8200"
    environment:
      VAULT_DEV_ROOT_TOKEN_ID: "sentient-root-token"
      VAULT_DEV_LISTEN_ADDRESS: "0.0.0.0:8200"
    volumes:
      - vault-data:/vault/data

  backend:
    build: .
    environment:
      VAULT_ADDR: http://vault:8200
      VAULT_TOKEN: sentient-root-token
    depends_on:
      - vault

volumes:
  vault-data:
```

### 8.8 Minimal .env (Only Vault Connection)

```env
# .env (Production - minimal)
# Only Vault connection info, no actual secrets!

VAULT_ADDR=http://vault:8200
VAULT_TOKEN=hvs.xxxxxxxxxxxx

# App metadata (non-sensitive)
APP_PORT=8000
APP_ENV=production
NODE_ENV=production
```

### 8.9 Secret Rotation

```bash
# Rotate JWT secret
vault kv put sentient/jwt \
  secret=$(openssl rand -base64 32) \
  refresh_secret=$(openssl rand -base64 32) \
  expires_in=1d

# Rotate DB password
vault kv put sentient/database \
  host=localhost \
  port=5432 \
  name=sentient_factory \
  user=postgres \
  pass=$(openssl rand -base64 24)

# Then restart backend to pick up new secrets
docker-compose restart backend
```

### 8.10 Vault UI Access

```bash
# Open Vault UI
open http://localhost:8200/ui

# Login with token
# Token: hvs.xxxxxxxxxxxx (from VAULT_TOKEN)
```

---

## 9. API Summary

### Total Endpoints: 76+

| Module          | Endpoints |
| --------------- | --------- |
| Auth            | 5         |
| Dashboard       | 4         |
| Users           | 5         |
| Roles           | 4         |
| Customers       | 5         |
| Items           | 5         |
| Vendors         | 5         |
| Sales Orders    | 8         |
| Delivery Orders | 5         |
| Purchase Orders | 7         |
| Work Orders     | 6         |
| Inventory       | 6         |
| AI Chat         | 4         |
| Alerts          | 4         |
| Reports         | 3         |

---

*Document Version: 1.1*
*Last Updated: 2024-12-27*

