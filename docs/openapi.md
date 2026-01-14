# 📋 OpenAPI Specification

> **Purpose:** Auto-generated API documentation from api-contract.md
> **Format:** OpenAPI 3.0.3
> **Base URL:** http://localhost:8000/api
> **Last Updated:** 2024-12-27

---

## NestJS Swagger Setup

```bash
# Install dependencies
npm install @nestjs/swagger swagger-ui-express
```

```typescript
// src/main.ts

import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Swagger Configuration
  const config = new DocumentBuilder()
    .setTitle('Sentient Factory API')
    .setDescription('AI-Powered Manufacturing ERP API')
    .setVersion('2024-12-27')
    .addBearerAuth({
      type: 'http',
      scheme: 'bearer',
      bearerFormat: 'JWT',
    })
    .addTag('Auth', 'Authentication endpoints')
    .addTag('Dashboard', 'Dashboard KPIs')
    .addTag('Users', 'User management')
    .addTag('Roles', 'Role & permissions')
    .addTag('Customers', 'Customer master data')
    .addTag('Items', 'Item master data')
    .addTag('Vendors', 'Vendor master data')
    .addTag('Sales Orders', 'Sales order management')
    .addTag('Delivery Orders', 'Delivery order management')
    .addTag('Purchase Orders', 'Purchase order management')
    .addTag('Work Orders', 'Production work orders')
    .addTag('Inventory', 'Stock management')
    .addTag('AI Chat', 'AI assistant chat')
    .addTag('Alerts', 'System notifications')
    .addTag('Reports', 'Report generation')
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api/docs', app, document);

  await app.listen(8000);
}
bootstrap();
```

---

## OpenAPI 3.0.3 Schema

```yaml
# openapi.yaml - Generate via: npm run swagger:export

openapi: 3.0.3
info:
  title: Sentient Factory API
  description: AI-Powered Manufacturing ERP API
  version: 2024-12-27
  contact:
    name: Sentient Factory Team
    email: api@sentient.local

servers:
  - url: http://localhost:8000/api
    description: Development
  - url: https://api.sentient.local/api
    description: Production

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    # Base Response
    ApiResponse:
      type: object
      properties:
        success:
          type: boolean
        data:
          type: object
        meta:
          type: object
          properties:
            timestamp:
              type: string
              format: date-time
            requestId:
              type: string
              format: uuid

    # Error Response
    ApiError:
      type: object
      properties:
        success:
          type: boolean
          example: false
        error:
          type: object
          properties:
            code:
              type: string
              enum: [VALIDATION_ERROR, UNAUTHORIZED, FORBIDDEN, NOT_FOUND, CONFLICT, BUSINESS_ERROR, SERVER_ERROR]
            message:
              type: string
            details:
              type: object
              additionalProperties:
                type: array
                items:
                  type: string

    # Pagination
    Pagination:
      type: object
      properties:
        page:
          type: integer
        limit:
          type: integer
        total:
          type: integer
        totalPages:
          type: integer

    # Auth
    LoginRequest:
      type: object
      required:
        - username
        - password
      properties:
        username:
          type: string
          example: john.doe
        password:
          type: string
          format: password
          example: Password123!

    LoginResponse:
      type: object
      properties:
        accessToken:
          type: string
        refreshToken:
          type: string
        expiresIn:
          type: integer
        user:
          $ref: '#/components/schemas/User'

    User:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        username:
          type: string
        email:
          type: string
          format: email
        role:
          $ref: '#/components/schemas/Role'
        permissions:
          type: array
          items:
            type: string

    Role:
      type: object
      properties:
        id:
          type: integer
        code:
          type: string
        name:
          type: string

    # Sales Order
    SalesOrder:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        soNumber:
          type: string
          example: SO-202412-0001
        customer:
          $ref: '#/components/schemas/CustomerSummary'
        orderDate:
          type: string
          format: date
        deadlineDate:
          type: string
          format: date
        items:
          type: array
          items:
            $ref: '#/components/schemas/SalesOrderItem'
        subtotal:
          type: number
        discountPct:
          type: number
        discountAmount:
          type: number
        taxPct:
          type: number
        taxAmount:
          type: number
        total:
          type: number
        totalFormatted:
          type: string
        status:
          type: string
          enum: [draft, pending, approved, in_progress, completed, cancelled]
        statusLabel:
          type: string
        notes:
          type: string
        createdBy:
          $ref: '#/components/schemas/UserSummary'
        createdAt:
          type: string
          format: date-time

    SalesOrderItem:
      type: object
      properties:
        id:
          type: integer
        item:
          $ref: '#/components/schemas/ItemSummary'
        qty:
          type: integer
        price:
          type: number
        discountPct:
          type: number
        subtotal:
          type: number
        deliveredQty:
          type: integer

    CreateSalesOrderRequest:
      type: object
      required:
        - customerId
        - orderDate
        - items
      properties:
        customerId:
          type: string
          format: uuid
        orderDate:
          type: string
          format: date
        deadlineDate:
          type: string
          format: date
        customerPO:
          type: string
        discountPct:
          type: number
        items:
          type: array
          items:
            $ref: '#/components/schemas/CreateSOItemRequest'
          minItems: 1
        notes:
          type: string

    CreateSOItemRequest:
      type: object
      required:
        - itemId
        - qty
        - price
      properties:
        itemId:
          type: string
          format: uuid
        qty:
          type: integer
          minimum: 1
        price:
          type: number
          minimum: 0
        discountPct:
          type: number

    # Master Data
    Customer:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        code:
          type: string
        name:
          type: string
        address:
          type: string
        phone:
          type: string
        email:
          type: string
          format: email
        segment:
          type: string
          enum: [VIP, Regular]
        creditLimit:
          type: number
        creditLimitFormatted:
          type: string

    CustomerSummary:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        code:
          type: string
        name:
          type: string

    Item:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        code:
          type: string
        name:
          type: string
        category:
          type: string
        stdCost:
          type: number
        sellPrice:
          type: number
        uom:
          type: string
        minStock:
          type: integer
        currentStock:
          type: integer

    ItemSummary:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        code:
          type: string
        name:
          type: string

    UserSummary:
      type: object
      properties:
        uuid:
          type: string
          format: uuid
        username:
          type: string

    # AI Chat
    ChatRequest:
      type: object
      required:
        - message
      properties:
        message:
          type: string
        sessionId:
          type: string
          format: uuid

    ChatResponse:
      type: object
      properties:
        sessionId:
          type: string
          format: uuid
        answer:
          type: string
        answerHtml:
          type: string
        confidence:
          type: number
        confidenceLabel:
          type: string
          enum: [High, Medium, Low]
        sources:
          type: array
          items:
            $ref: '#/components/schemas/ChatSource'
        charts:
          type: array
          items:
            type: object
        tables:
          type: array
          items:
            type: object
        suggestedActions:
          type: array
          items:
            $ref: '#/components/schemas/SuggestedAction'
        timestamp:
          type: string
          format: date-time

    ChatSource:
      type: object
      properties:
        table:
          type: string
        period:
          type: string
        recordCount:
          type: integer

    SuggestedAction:
      type: object
      properties:
        label:
          type: string
        action:
          type: string
        params:
          type: object

    # Dropdown Option
    Option:
      type: object
      properties:
        value:
          type: string
        label:
          type: string
        code:
          type: string
        color:
          type: string

paths:
  # Auth
  /auth/login:
    post:
      tags: [Auth]
      summary: Login
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/ApiResponse'
                  - properties:
                      data:
                        $ref: '#/components/schemas/LoginResponse'
        '401':
          description: Invalid credentials
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ApiError'

  /auth/me:
    get:
      tags: [Auth]
      summary: Get current user
      security:
        - bearerAuth: []
      responses:
        '200':
          description: Success

  # Sales Orders
  /sales-orders:
    get:
      tags: [Sales Orders]
      summary: List sales orders
      security:
        - bearerAuth: []
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
        - name: status
          in: query
          schema:
            type: string
        - name: search
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Success

    post:
      tags: [Sales Orders]
      summary: Create sales order
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateSalesOrderRequest'
      responses:
        '201':
          description: Created

  /sales-orders/{uuid}:
    get:
      tags: [Sales Orders]
      summary: Get sales order detail
      security:
        - bearerAuth: []
      parameters:
        - name: uuid
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Success

  /sales-orders/{uuid}/approve:
    post:
      tags: [Sales Orders]
      summary: Approve sales order
      security:
        - bearerAuth: []
      parameters:
        - name: uuid
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Approved

  # AI Chat
  /ai/chat:
    post:
      tags: [AI Chat]
      summary: Send message to AI
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ChatRequest'
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/ApiResponse'
                  - properties:
                      data:
                        $ref: '#/components/schemas/ChatResponse'
```

---

## Export Commands

```bash
# Export from NestJS Swagger
npm run swagger:export

# Validate OpenAPI spec
npx @redocly/cli lint openapi.yaml

# Generate client SDK
npx @openapitools/openapi-generator-cli generate \
  -i openapi.yaml \
  -g typescript-axios \
  -o ./generated/api-client
```

---

## Swagger UI Access

```
Development: http://localhost:8000/api/docs
Staging: https://staging.sentient.local/api/docs
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
