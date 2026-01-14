# 🧪 QA Test Documentation

> **Parent Docs:** [BPA-1](bpa-1.md), [BPA-2](bpa-2.md), [BPA-3](bpa-3.md)
> **Last Sync:** 2024-12-27

---

## 1. Test Case Standards

### 1.1 Test Case ID Format

| Module          | Prefix   | Example      |
| --------------- | -------- | ------------ |
| Authentication  | TC-AUTH  | TC-AUTH-001  |
| User Management | TC-USER  | TC-USER-001  |
| Role/Permission | TC-ROLE  | TC-ROLE-001  |
| Sales Order     | TC-SO    | TC-SO-001    |
| Delivery Order  | TC-DO    | TC-DO-001    |
| Purchase Order  | TC-PO    | TC-PO-001    |
| Work Order      | TC-WO    | TC-WO-001    |
| Inventory       | TC-INV   | TC-INV-001   |
| AI Chat         | TC-AI    | TC-AI-001    |
| Alert           | TC-ALERT | TC-ALERT-001 |
| Report          | TC-RPT   | TC-RPT-001   |
| Edge Case       | TC-EDGE  | TC-EDGE-001  |

### 1.2 Priority Levels

| Priority          | Description                  | SLA                      |
| ----------------- | ---------------------------- | ------------------------ |
| **P1 - Critical** | Core functionality, blocking | Must pass 100%           |
| **P2 - High**     | Important features           | Must pass before release |
| **P3 - Medium**   | Standard features            | Should pass              |
| **P4 - Low**      | Nice-to-have                 | Can defer                |

### 1.3 Test Status

| Status      | Symbol | Description           |
| ----------- | ------ | --------------------- |
| Not Started | ⬜      | Test not executed     |
| In Progress | 🟡      | Currently testing     |
| Passed      | ✅      | Test passed           |
| Failed      | ❌      | Test failed           |
| Blocked     | 🔴      | Cannot execute        |
| Skipped     | ⏭️      | Intentionally skipped |

### 1.4 Test Type

| Type        | Description               |
| ----------- | ------------------------- |
| Functional  | Feature works as expected |
| Integration | Components work together  |
| Security    | No vulnerabilities        |
| Performance | Meets speed requirements  |
| UI/UX       | Visual and usability      |
| Regression  | No new bugs introduced    |

---

## 2. Core System Test Cases (BPA-1)

### 2.1 Authentication

#### TC-AUTH-001: Valid Login

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P1 - Critical                                             |
| Type          | Functional                                                |

**Preconditions:**
- User account exists and active
- User is on login page

**Steps:**
1. Enter valid username: `john.doe`
2. Enter valid password: `Password123!`
3. Click Login button

**Expected Result:**
- Redirect to Dashboard
- Session token created
- User name displayed in header

**Edge Cases:**
- Login with email instead of username
- Login with extra whitespace in username

---

#### TC-AUTH-002: Invalid Password

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P1 - Critical                                             |
| Type          | Functional, Security                                      |

**Preconditions:**
- User account exists

**Steps:**
1. Enter valid username: `john.doe`
2. Enter wrong password: `wrongpassword`
3. Click Login button

**Expected Result:**
- Stay on login page
- Show error: "Invalid username or password"
- Do NOT reveal which field is wrong

**Edge Cases:**
- Empty password field
- Password with SQL injection attempt
- Password over max length

---

#### TC-AUTH-003: Account Lockout

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P1 - Critical                                             |
| Type          | Security                                                  |

**Preconditions:**
- User account exists

**Steps:**
1. Enter wrong password 5 times consecutively

**Expected Result:**
- Account locked for 15 minutes
- Show message: "Account locked. Try again in 15 minutes."
- Log security event

**Edge Cases:**
- Lockout across different browsers/IPs
- Lockout reset after success

---

#### TC-AUTH-004: Session Timeout

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P2 - High                                                 |
| Type          | Security                                                  |

**Preconditions:**
- User logged in

**Steps:**
1. Leave session idle for 30 minutes
2. Attempt any action

**Expected Result:**
- Redirect to login page
- Show message: "Session expired. Please login again."

**Edge Cases:**
- Activity in another tab extends session
- Remember me option behavior

---

#### TC-AUTH-005: Logout

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P1 - Critical                                             |
| Type          | Functional, Security                                      |

**Steps:**
1. Click user menu
2. Click Logout

**Expected Result:**
- Session destroyed
- Redirect to login page
- Cannot access protected pages via back button

---

#### TC-AUTH-006: Forgot Password

| Field         | Value                                                     |
| ------------- | --------------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.1](bpa-1.md#21-login--authentication) |
| Priority      | P2 - High                                                 |
| Type          | Functional                                                |

**Steps:**
1. Click "Forgot Password" on login page
2. Enter registered email
3. Submit

**Expected Result:**
- Email sent with reset link
- Link expires in 1 hour
- Link single-use only

**Edge Cases:**
- Non-existent email (show same success message for security)
- Request multiple times (rate limit)

---

### 2.2 User Management

#### TC-USER-001: Create User

| Field         | Value                                               |
| ------------- | --------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.2](bpa-1.md#22-user-management) |
| Priority      | P1 - Critical                                       |
| Type          | Functional                                          |

**Preconditions:**
- Logged in as Admin
- On User Management page

**Steps:**
1. Click "Create User"
2. Fill: Username `newuser`, Email `new@email.com`, Role `Staff`
3. Submit

**Expected Result:**
- User created successfully
- Appears in user list
- Initial password sent to email

**Edge Cases:**
- Duplicate username
- Invalid email format
- Username with special characters

---

#### TC-USER-002: Edit User

| Field         | Value                                               |
| ------------- | --------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.2](bpa-1.md#22-user-management) |
| Priority      | P2 - High                                           |
| Type          | Functional                                          |

**Steps:**
1. Click Edit on existing user
2. Change email to `updated@email.com`
3. Save

**Expected Result:**
- User updated
- Audit log created

**Edge Cases:**
- Edit own account
- Change to existing email

---

#### TC-USER-003: Deactivate User

| Field         | Value                                               |
| ------------- | --------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.2](bpa-1.md#22-user-management) |
| Priority      | P2 - High                                           |
| Type          | Functional                                          |

**Steps:**
1. Toggle user status to Inactive

**Expected Result:**
- User cannot login
- Existing sessions terminated

**Edge Cases:**
- Deactivate last admin (should prevent)
- Deactivate own account

---

#### TC-USER-004: Search & Filter Users

| Field         | Value                                               |
| ------------- | --------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.2](bpa-1.md#22-user-management) |
| Priority      | P3 - Medium                                         |
| Type          | Functional                                          |

**Steps:**
1. Enter search: "john"
2. Filter by Role: "Admin"

**Expected Result:**
- List filtered correctly
- Pagination works with filters

---

### 2.3 Role & Permission

#### TC-ROLE-001: Create Role

| Field         | Value                                                |
| ------------- | ---------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.3](bpa-1.md#23-role--permission) |
| Priority      | P1 - Critical                                        |
| Type          | Functional                                           |

**Steps:**
1. Navigate to Role Management
2. Click Create Role
3. Name: "Sales Supervisor"
4. Assign permissions: SO (CRUD), DO (Read), Customer (Read)
5. Save

**Expected Result:**
- Role created
- Can be assigned to users

**Edge Cases:**
- Duplicate role name
- No permissions selected

---

#### TC-ROLE-002: Permission Matrix

| Field         | Value                                                |
| ------------- | ---------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.3](bpa-1.md#23-role--permission) |
| Priority      | P1 - Critical                                        |
| Type          | Functional                                           |

**Steps:**
1. Create user with "Sales" role (SO: CRUD, PO: None)
2. Login as that user
3. Try to access Purchase Order

**Expected Result:**
- SO menu visible and accessible
- PO menu hidden or shows "Access Denied"
- Cannot access PO via direct URL

---

#### TC-ROLE-003: Role Hierarchy

| Field         | Value                                                |
| ------------- | ---------------------------------------------------- |
| BPA Reference | [bpa-1.md Section 2.3](bpa-1.md#23-role--permission) |
| Priority      | P2 - High                                            |
| Type          | Functional                                           |

**Steps:**
1. Assign Manager role (inherits Staff permissions)
2. Verify all inherited permissions work

**Expected Result:**
- All base permissions active
- Additional manager permissions active

---

---

## 3. Transaction Test Cases (BPA-2)

### 3.1 Sales Order (SO)

#### TC-SO-001: Create Sales Order

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P1 - Critical                                      |
| Type          | Functional                                         |

**Preconditions:**
- Customer master data exists
- Item master data exists
- User has SO Create permission

**Steps:**
1. Click Create SO
2. Select Customer: "PT ABC Manufacturing"
3. Add Item: "Widget Pro X1", Qty: 100, Price: 65,000
4. Save as Draft

**Expected Result:**
- SO Number auto-generated: SO-YYYYMM-XXXX
- Status: Draft
- Total calculated: Rp 6,500,000

**Test Data:**
```json
{
  "customer_id": 1,
  "items": [
    {"item_id": 1, "qty": 100, "price": 65000}
  ]
}
```

**Edge Cases:**
- Zero quantity
- Negative price
- Item out of stock
- Inactive customer

---

#### TC-SO-002: Submit SO for Approval

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P1 - Critical                                      |
| Type          | Functional                                         |

**Preconditions:**
- SO in Draft status

**Steps:**
1. Open Draft SO
2. Click Submit for Approval

**Expected Result:**
- Status changes to Pending
- Notification sent to approver
- User cannot edit

**Edge Cases:**
- Submit empty SO (no items)
- Submit SO with deleted customer

---

#### TC-SO-003: Approve SO

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P1 - Critical                                      |
| Type          | Functional                                         |

**Preconditions:**
- SO in Pending status
- User has Approve permission

**Steps:**
1. Open Pending SO
2. Click Approve

**Expected Result:**
- Status changes to Approved
- Stock reserved
- Ready for DO creation

**Edge Cases:**
- Insufficient stock after approval
- Approve own SO (depends on policy)

---

#### TC-SO-004: Reject SO

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P2 - High                                          |
| Type          | Functional                                         |

**Steps:**
1. Open Pending SO
2. Click Reject
3. Enter reason: "Price too low"

**Expected Result:**
- Status changes to Rejected
- Reason saved
- Creator notified

---

#### TC-SO-005: Cancel SO

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P2 - High                                          |
| Type          | Functional                                         |

**Steps:**
1. Open Approved SO (no DO created yet)
2. Click Cancel
3. Confirm

**Expected Result:**
- Status: Cancelled
- Reserved stock released
- Cannot be edited

**Edge Cases:**
- Cancel SO with partial DO
- Cancel SO with completed DO (should prevent)

---

#### TC-SO-006: SO Pricing Validation

| Field         | Value                                              |
| ------------- | -------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.1](bpa-2.md#21-sales-order-so) |
| Priority      | P1 - Critical                                      |
| Type          | Functional                                         |

**Steps:**
1. Create SO with price below cost

**Expected Result:**
- Warning shown: "Price below cost"
- Require manager approval

---

### 3.2 Delivery Order (DO)

#### TC-DO-001: Create DO from SO

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.2](bpa-2.md#22-delivery-order-do) |
| Priority      | P1 - Critical                                         |
| Type          | Functional                                            |

**Preconditions:**
- SO in Approved status
- Stock available

**Steps:**
1. Open Approved SO
2. Click Create DO
3. Enter Ship Date, Driver, Vehicle
4. Save

**Expected Result:**
- DO created linked to SO
- DO status: Pending
- Stock moved to Reserved -> Shipped

**Edge Cases:**
- Partial delivery (only some items)
- Multiple DO from single SO

---

#### TC-DO-002: Complete Delivery

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.2](bpa-2.md#22-delivery-order-do) |
| Priority      | P1 - Critical                                         |
| Type          | Functional                                            |

**Steps:**
1. Open DO in Shipped status
2. Upload POD (Proof of Delivery)
3. Click Mark as Delivered

**Expected Result:**
- Status: Delivered
- SO updated (partial/complete)
- Ready for invoicing

---

### 3.3 Purchase Order (PO)

#### TC-PO-001: Create PO

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.3](bpa-2.md#23-purchase-order-po) |
| Priority      | P1 - Critical                                         |
| Type          | Functional                                            |

**Steps:**
1. Click Create PO
2. Select Vendor: "PT Baja Makmur"
3. Add Item: "Raw Material A", Qty: 500, Price: 10,000
4. Save

**Expected Result:**
- PO Number: PO-YYYYMM-XXXX
- Status: Draft
- Total: Rp 5,000,000

---

#### TC-PO-002: PO Approval Matrix

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.3](bpa-2.md#23-purchase-order-po) |
| Priority      | P1 - Critical                                         |
| Type          | Functional                                            |

**Scenarios:**

| Amount         | Required Approver |
| -------------- | ----------------- |
| Up to Rp 10jt  | Manager           |
| Rp 10-100jt    | Manager + Finance |
| Above Rp 100jt | + Director        |

**Edge Cases:**
- PO exactly at threshold
- PO amount changed after approval
- Approver on leave

---

#### TC-PO-003: Receive Goods

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.3](bpa-2.md#23-purchase-order-po) |
| Priority      | P1 - Critical                                         |
| Type          | Functional                                            |

**Steps:**
1. Open Approved PO
2. Click Receive
3. Enter received qty (partial ok)
4. Save

**Expected Result:**
- Inventory increased
- GRN (Goods Received Note) created
- PO status updated (Partial/Complete)

**Edge Cases:**
- Receive more than ordered
- Receive damaged goods

---

### 3.4 Work Order (WO)

#### TC-WO-001: Create Work Order

| Field         | Value                                             |
| ------------- | ------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.4](bpa-2.md#24-work-order-wo) |
| Priority      | P1 - Critical                                     |
| Type          | Functional                                        |

**Steps:**
1. Create WO for Item: Widget Pro X1
2. Set target qty: 1000
3. Select Machine: MCH-001
4. Save

**Expected Result:**
- WO Number generated
- Status: Created
- Materials reserved

---

#### TC-WO-002: Start Production

| Field         | Value                                             |
| ------------- | ------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.4](bpa-2.md#24-work-order-wo) |
| Priority      | P1 - Critical                                     |
| Type          | Functional                                        |

**Steps:**
1. Open WO
2. Click Start Production
3. Assign operator

**Expected Result:**
- Status: In Progress
- Start time recorded
- Machine status updated

---

#### TC-WO-003: Report Production Output

| Field         | Value                                             |
| ------------- | ------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.4](bpa-2.md#24-work-order-wo) |
| Priority      | P1 - Critical                                     |
| Type          | Functional                                        |

**Steps:**
1. Enter output: Good: 980, Reject: 20
2. Save

**Expected Result:**
- Yield calculated: 98%
- FG inventory increased
- Reject recorded

**Edge Cases:**
- Output > target qty
- 100% reject

---

### 3.5 Inventory

#### TC-INV-001: Stock Inquiry

| Field         | Value                                                    |
| ------------- | -------------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.5](bpa-2.md#25-inventory-management) |
| Priority      | P1 - Critical                                            |
| Type          | Functional                                               |

**Steps:**
1. Search item: "Widget Pro X1"
2. View by warehouse

**Expected Result:**
- Show: On Hand, Reserved, Available
- Show stock status indicator

---

#### TC-INV-002: Stock Adjustment

| Field         | Value                                                    |
| ------------- | -------------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.5](bpa-2.md#25-inventory-management) |
| Priority      | P2 - High                                                |
| Type          | Functional                                               |

**Steps:**
1. Select item and warehouse
2. Enter adjustment: -10 (reason: Damaged)
3. Approve adjustment

**Expected Result:**
- Stock reduced
- Audit log created
- Reason recorded

**Edge Cases:**
- Adjust below zero
- Large adjustment without approval

---

#### TC-INV-003: Low Stock Alert

| Field         | Value                                                    |
| ------------- | -------------------------------------------------------- |
| BPA Reference | [bpa-2.md Section 2.5](bpa-2.md#25-inventory-management) |
| Priority      | P2 - High                                                |
| Type          | Functional                                               |

**Steps:**
1. Reduce stock below reorder point

**Expected Result:**
- Alert generated
- Notification to purchaser

---

---

## 4. AI & Analytics Test Cases (BPA-3)

### 4.1 AI Chat

#### TC-AI-001: Basic Query

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-3.md Section 2.1](bpa-3.md#21-ai-chat-assistant) |
| Priority      | P2 - High                                             |
| Type          | Functional                                            |

**Steps:**
1. Open AI Chat
2. Ask: "Berapa total sales bulan ini?"

**Expected Result:**
- AI responds with data from database
- Shows confidence score
- Cites data source

---

#### TC-AI-002: Out of Scope Query

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-3.md Section 2.1](bpa-3.md#21-ai-chat-assistant) |
| Priority      | P2 - High                                             |
| Type          | Functional                                            |

**Steps:**
1. Ask: "Siapa presiden Indonesia?"

**Expected Result:**
- AI politely declines
- Suggests business-related questions

---

#### TC-AI-003: Data Privacy

| Field         | Value                                                 |
| ------------- | ----------------------------------------------------- |
| BPA Reference | [bpa-3.md Section 2.1](bpa-3.md#21-ai-chat-assistant) |
| Priority      | P1 - Critical                                         |
| Type          | Security                                              |

**Steps:**
1. Ask about data user doesn't have permission to

**Expected Result:**
- AI respects permission boundaries
- Does not expose restricted data

---

### 4.2 Alerts & Notifications

#### TC-ALERT-001: Low Stock Alert

| Field         | Value                                                   |
| ------------- | ------------------------------------------------------- |
| BPA Reference | [bpa-3.md Section 2.2](bpa-3.md#22-alert--notification) |
| Priority      | P2 - High                                               |
| Type          | Functional                                              |

**Trigger:**
- Stock falls below reorder point

**Expected Result:**
- Alert created with High severity
- Notification sent to subscribed users
- Appears in dashboard

---

#### TC-ALERT-002: Alert Acknowledgment

| Field         | Value                                                   |
| ------------- | ------------------------------------------------------- |
| BPA Reference | [bpa-3.md Section 2.2](bpa-3.md#22-alert--notification) |
| Priority      | P3 - Medium                                             |
| Type          | Functional                                              |

**Steps:**
1. Click Acknowledge on alert
2. Add note

**Expected Result:**
- Alert marked as acknowledged
- Timestamp and user recorded

---

### 4.3 Reports

#### TC-RPT-001: Generate Sales Report

| Field         | Value                                                  |
| ------------- | ------------------------------------------------------ |
| BPA Reference | [bpa-3.md Section 2.4](bpa-3.md#24-reports--analytics) |
| Priority      | P3 - Medium                                            |
| Type          | Functional                                             |

**Steps:**
1. Select Report: Sales Summary
2. Set date range: This month
3. Click Generate

**Expected Result:**
- Report generated
- Can export to PDF/Excel

---

---

## 5. Edge Cases & Negative Tests

### 5.1 Security Tests

#### TC-EDGE-001: SQL Injection

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Security      |

**Steps:**
1. In login username field, enter: `admin'; DROP TABLE users;--`

**Expected Result:**
- Login fails safely
- No database damage
- Input sanitized

---

#### TC-EDGE-002: XSS Attack

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Security      |

**Steps:**
1. In any text field, enter: `<script>alert('XSS')</script>`
2. Save and view

**Expected Result:**
- Script not executed
- Content escaped/sanitized

---

#### TC-EDGE-003: CSRF Protection

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Security      |

**Steps:**
1. Copy form action URL
2. Try to submit from external page

**Expected Result:**
- Request rejected (invalid CSRF token)

---

#### TC-EDGE-004: Unauthorized Access

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Security      |

**Steps:**
1. Login as Staff user
2. Try to access Admin URL directly: `/admin/users`

**Expected Result:**
- 403 Forbidden
- Logged as security event

---

#### TC-EDGE-005: API Rate Limiting

| Priority | P2 - High |
| -------- | --------- |
| Type     | Security  |

**Steps:**
1. Make 100 API requests in 1 minute

**Expected Result:**
- After threshold, return 429 Too Many Requests
- Client must wait before retry

---

### 5.2 Data Validation

#### TC-EDGE-010: Max Length Input

| Priority | P2 - High  |
| -------- | ---------- |
| Type     | Functional |

**Steps:**
1. Enter 1000 characters in Name field (max 255)

**Expected Result:**
- Validation error shown
- Data not saved

---

#### TC-EDGE-011: Special Characters

| Priority | P2 - High  |
| -------- | ---------- |
| Type     | Functional |

**Steps:**
1. Enter: `<>&"'`

**Expected Result:**
- Characters properly escaped
- Displayed correctly

---

#### TC-EDGE-012: Unicode Input

| Priority | P3 - Medium |
| -------- | ----------- |
| Type     | Functional  |

**Steps:**
1. Enter: `日本語テスト 🎉 مرحبا`

**Expected Result:**
- Saved and displayed correctly

---

#### TC-EDGE-013: Required Field Empty

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Functional    |

**Steps:**
1. Leave required field empty
2. Submit

**Expected Result:**
- Validation error shown
- Form not submitted
- Focus on first error field

---

### 5.3 Concurrency Tests

#### TC-EDGE-020: Concurrent Edit

| Priority | P2 - High   |
| -------- | ----------- |
| Type     | Integration |

**Steps:**
1. User A opens SO#1 for edit
2. User B opens same SO#1 for edit
3. User A saves
4. User B saves

**Expected Result:**
- User B gets conflict warning
- Option to reload or merge

---

#### TC-EDGE-021: Race Condition - Stock

| Priority | P1 - Critical |
| -------- | ------------- |
| Type     | Integration   |

**Steps:**
1. Stock = 10 units
2. User A creates SO for 8 units
3. User B creates SO for 5 units (simultaneously)

**Expected Result:**
- One succeeds, one fails (insufficient stock)
- No overselling

---

### 5.4 Performance Tests

#### TC-EDGE-030: Large Dataset

| Priority | P2 - High   |
| -------- | ----------- |
| Type     | Performance |

**Steps:**
1. Load SO list with 10,000 records

**Expected Result:**
- Page loads within 3 seconds
- Pagination works

---

#### TC-EDGE-031: Bulk Operation

| Priority | P2 - High   |
| -------- | ----------- |
| Type     | Performance |

**Steps:**
1. Import 1000 items via CSV

**Expected Result:**
- Process completes
- Progress indicator shown
- No timeout

---

#### TC-EDGE-032: Response Time

| Priority | P2 - High   |
| -------- | ----------- |
| Type     | Performance |

**Criteria:**

| Operation       | Max Response Time |
| --------------- | ----------------- |
| Page Load       | 2 seconds         |
| API Call        | 500ms             |
| Search          | 1 second          |
| Report Generate | 10 seconds        |

---

---

## 6. Test Summary Matrix

### 6.1 Test Coverage by Module

| Module          | Total TC | P1     | P2     | P3    | Status |
| --------------- | -------- | ------ | ------ | ----- | ------ |
| Authentication  | 6        | 4      | 2      | 0     | ⬜      |
| User Management | 4        | 2      | 2      | 0     | ⬜      |
| Role/Permission | 3        | 2      | 1      | 0     | ⬜      |
| Sales Order     | 6        | 4      | 2      | 0     | ⬜      |
| Delivery Order  | 2        | 2      | 0      | 0     | ⬜      |
| Purchase Order  | 3        | 2      | 1      | 0     | ⬜      |
| Work Order      | 3        | 3      | 0      | 0     | ⬜      |
| Inventory       | 3        | 1      | 2      | 0     | ⬜      |
| AI Chat         | 3        | 1      | 2      | 0     | ⬜      |
| Alerts          | 2        | 0      | 1      | 1     | ⬜      |
| Reports         | 1        | 0      | 0      | 1     | ⬜      |
| Edge Cases      | 15       | 6      | 8      | 1     | ⬜      |
| **TOTAL**       | **51**   | **27** | **21** | **3** | -      |

### 6.2 Test Execution Tracking

| Sprint   | Scope         | Planned | Executed | Passed | Failed |
| -------- | ------------- | ------- | -------- | ------ | ------ |
| Sprint 1 | Auth + User   | 10      | -        | -      | -      |
| Sprint 2 | SO + DO       | 8       | -        | -      | -      |
| Sprint 3 | PO + WO + Inv | 9       | -        | -      | -      |
| Sprint 4 | AI + Reports  | 6       | -        | -      | -      |
| Sprint 5 | Edge Cases    | 15      | -        | -      | -      |
| UAT      | All           | 51      | -        | -      | -      |

### 6.3 Bug Summary Template

| Bug ID  | TC Reference | Severity | Summary | Status |
| ------- | ------------ | -------- | ------- | ------ |
| BUG-001 | TC-AUTH-002  | Major    | -       | -      |
| BUG-002 | TC-SO-003    | Minor    | -       | -      |

---

## 7. Test Data Requirements

### 7.1 Master Data

Refer to [System Analysis Section 1.8](system_analysis.md#_18-sample-data-dummy-data-untuk-uiux-team) for dummy data.

### 7.2 User Accounts for Testing

| Username      | Role    | Purpose                |
| ------------- | ------- | ---------------------- |
| admin_test    | Admin   | Full access testing    |
| manager_test  | Manager | Approval testing       |
| staff_test    | Staff   | Limited access testing |
| readonly_test | Viewer  | Read-only testing      |

### 7.3 Test Environment

| Environment | URL                    | Purpose                |
| ----------- | ---------------------- | ---------------------- |
| DEV         | localhost:3000         | Development testing    |
| STAGING     | staging.sentient.local | Pre-production         |
| PROD        | app.sentient.local     | Production (read-only) |

---

## 8. E2E Testing (Playwright)

### 8.1 Setup

```bash
# Install Playwright
npm init playwright@latest

# Install dependencies
npm install -D @playwright/test
npx playwright install
```

### 8.2 Configuration

```typescript
// playwright.config.ts

import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### 8.3 Test Fixtures

```typescript
// e2e/fixtures/auth.fixture.ts

import { test as base, expect } from '@playwright/test';

type AuthFixtures = {
  authenticatedPage: any;
};

export const test = base.extend<AuthFixtures>({
  authenticatedPage: async ({ page }, use) => {
    // Login before test
    await page.goto('/auth/login');
    await page.fill('[name="username"]', 'admin');
    await page.fill('[name="password"]', 'Password123!');
    await page.click('button[type="submit"]');
    await page.waitForURL('/dashboard');
    
    await use(page);
  },
});

export { expect };
```

### 8.4 E2E Test Scenarios

#### E2E-001: Complete Login Flow

```typescript
// e2e/auth/login.spec.ts

import { test, expect } from '@playwright/test';

test.describe('Authentication', () => {
  test('should login successfully', async ({ page }) => {
    await page.goto('/auth/login');
    
    // Fill credentials
    await page.fill('[name="username"]', 'admin');
    await page.fill('[name="password"]', 'Password123!');
    
    // Submit
    await page.click('button[type="submit"]');
    
    // Verify redirect
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('[data-testid="user-menu"]')).toContainText('admin');
  });

  test('should show error for invalid credentials', async ({ page }) => {
    await page.goto('/auth/login');
    
    await page.fill('[name="username"]', 'admin');
    await page.fill('[name="password"]', 'wrongpassword');
    await page.click('button[type="submit"]');
    
    await expect(page.locator('.error-message')).toBeVisible();
    await expect(page).toHaveURL('/auth/login');
  });

  test('should logout successfully', async ({ page }) => {
    // Login first
    await page.goto('/auth/login');
    await page.fill('[name="username"]', 'admin');
    await page.fill('[name="password"]', 'Password123!');
    await page.click('button[type="submit"]');
    await page.waitForURL('/dashboard');
    
    // Logout
    await page.click('[data-testid="user-menu"]');
    await page.click('[data-testid="logout-btn"]');
    
    await expect(page).toHaveURL('/auth/login');
  });
});
```

#### E2E-002: Sales Order Creation Flow

```typescript
// e2e/sales/create-so.spec.ts

import { test, expect } from '../fixtures/auth.fixture';

test.describe('Sales Order', () => {
  test('should create sales order successfully', async ({ authenticatedPage: page }) => {
    // Navigate to SO
    await page.click('[data-testid="menu-sales"]');
    await page.click('[data-testid="menu-sales-orders"]');
    
    // Click create
    await page.click('[data-testid="btn-create-so"]');
    
    // Fill form
    await page.selectOption('[name="customerId"]', { label: 'PT ABC Manufacturing' });
    await page.fill('[name="orderDate"]', '2024-12-27');
    
    // Add item
    await page.click('[data-testid="btn-add-item"]');
    await page.selectOption('[name="items.0.itemId"]', { label: 'Widget Pro X1' });
    await page.fill('[name="items.0.qty"]', '100');
    await page.fill('[name="items.0.price"]', '65000');
    
    // Save
    await page.click('[data-testid="btn-save"]');
    
    // Verify
    await expect(page.locator('.toast-success')).toBeVisible();
    await expect(page.locator('[data-testid="so-number"]')).toContainText('SO-');
  });

  test('should submit SO for approval', async ({ authenticatedPage: page }) => {
    // Navigate to draft SO
    await page.goto('/sales/orders');
    await page.click('[data-testid="so-row-draft"]:first-child');
    
    // Submit
    await page.click('[data-testid="btn-submit"]');
    await page.click('[data-testid="confirm-yes"]');
    
    // Verify status change
    await expect(page.locator('[data-testid="so-status"]')).toContainText('Pending');
  });

  test('should approve SO', async ({ authenticatedPage: page }) => {
    // Navigate to pending SO
    await page.goto('/sales/orders?status=pending');
    await page.click('[data-testid="so-row"]:first-child');
    
    // Approve
    await page.click('[data-testid="btn-approve"]');
    
    // Verify
    await expect(page.locator('[data-testid="so-status"]')).toContainText('Approved');
  });
});
```

#### E2E-003: AI Chat Flow

```typescript
// e2e/ai/chat.spec.ts

import { test, expect } from '../fixtures/auth.fixture';

test.describe('AI Chat', () => {
  test('should send message and receive response', async ({ authenticatedPage: page }) => {
    // Navigate to AI Chat
    await page.click('[data-testid="menu-ai-chat"]');
    
    // Send message
    await page.fill('[data-testid="chat-input"]', 'Berapa total revenue bulan ini?');
    await page.click('[data-testid="btn-send"]');
    
    // Wait for response (AI may take time)
    await page.waitForSelector('[data-testid="ai-response"]', { timeout: 30000 });
    
    // Verify response
    await expect(page.locator('[data-testid="ai-response"]')).toBeVisible();
    await expect(page.locator('[data-testid="confidence-indicator"]')).toBeVisible();
  });

  test('should show suggested actions', async ({ authenticatedPage: page }) => {
    await page.click('[data-testid="menu-ai-chat"]');
    
    await page.fill('[data-testid="chat-input"]', 'Tampilkan top 5 customer');
    await page.click('[data-testid="btn-send"]');
    
    await page.waitForSelector('[data-testid="ai-response"]', { timeout: 30000 });
    
    await expect(page.locator('[data-testid="suggested-actions"]')).toBeVisible();
  });
});
```

### 8.5 Visual Regression Testing

```typescript
// e2e/visual/dashboard.spec.ts

import { test, expect } from '../fixtures/auth.fixture';

test('dashboard should match snapshot', async ({ authenticatedPage: page }) => {
  await page.goto('/dashboard');
  await page.waitForLoadState('networkidle');
  
  // Wait for data to load
  await page.waitForSelector('[data-testid="kpi-cards"]');
  
  await expect(page).toHaveScreenshot('dashboard.png', {
    maxDiffPixels: 100,
  });
});

test('sales order list should match snapshot', async ({ authenticatedPage: page }) => {
  await page.goto('/sales/orders');
  await page.waitForSelector('[data-testid="so-table"]');
  
  await expect(page).toHaveScreenshot('so-list.png');
});
```

---

## 9. Performance Testing

### 9.1 Load Testing with k6

```javascript
// k6/load-test.js

import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 20 },   // Ramp up
    { duration: '1m', target: 20 },    // Hold
    { duration: '30s', target: 50 },   // Spike
    { duration: '30s', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],  // 95% under 500ms
    http_req_failed: ['rate<0.01'],    // Error rate < 1%
  },
};

const BASE_URL = 'http://localhost:8000/api';
let authToken = '';

export function setup() {
  // Login to get token
  const loginRes = http.post(`${BASE_URL}/auth/login`, JSON.stringify({
    username: 'admin',
    password: 'Password123!'
  }), {
    headers: { 'Content-Type': 'application/json' },
  });
  
  return { token: JSON.parse(loginRes.body).data.accessToken };
}

export default function(data) {
  const headers = {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${data.token}`,
  };

  // Test dashboard
  const dashRes = http.get(`${BASE_URL}/dashboard/summary`, { headers });
  check(dashRes, {
    'dashboard status 200': (r) => r.status === 200,
    'dashboard response time < 500ms': (r) => r.timings.duration < 500,
  });

  // Test sales orders list
  const soRes = http.get(`${BASE_URL}/sales-orders?page=1&limit=20`, { headers });
  check(soRes, {
    'SO list status 200': (r) => r.status === 200,
  });

  sleep(1);
}
```

### 9.2 Benchmarks

| Endpoint               | P50    | P95    | P99    | Target |
| ---------------------- | ------ | ------ | ------ | ------ |
| `/dashboard/summary`   | <100ms | <300ms | <500ms | ✅      |
| `/sales-orders` (list) | <200ms | <500ms | <1s    | ✅      |
| `/sales-orders/:id`    | <100ms | <300ms | <500ms | ✅      |
| `/ai/chat`             | <5s    | <10s   | <15s   | ⚠️      |

### 9.3 Run Performance Tests

```bash
# Install k6
brew install k6

# Run load test
k6 run k6/load-test.js

# Run with HTML report
k6 run --out json=results.json k6/load-test.js
```

---

## 10. CI Integration

### 10.1 GitHub Actions for E2E

```yaml
# .github/workflows/e2e.yml

name: E2E Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install Playwright Browsers
        run: npx playwright install --with-deps
      
      - name: Start services
        run: docker-compose -f docker-compose.test.yml up -d
      
      - name: Wait for services
        run: npx wait-on http://localhost:3000 http://localhost:8000/health
      
      - name: Run E2E tests
        run: npx playwright test
      
      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

### 10.2 Test Reports

```bash
# Generate HTML report
npx playwright show-report

# Generate JUnit for CI
npx playwright test --reporter=junit

# Generate both
npx playwright test --reporter=html,junit
```

---

*Document Version: 1.1*
*Last Updated: 2024-12-27*

