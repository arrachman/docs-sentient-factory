# 📋 BPA-1: Core System & Authentication

> **Business Process Analysis - Phase 1**
> Target: UI/UX & Frontend Team

---

## 1. Overview

BPA-1 mencakup modul **inti sistem** yang harus dikembangkan pertama sebelum modul lainnya.

| Modul                  | Priority | Kompleksitas |
| ---------------------- | -------- | ------------ |
| Login & Authentication | P0       | Medium       |
| Dashboard Overview     | P0       | Medium       |
| User Management        | P0       | Medium       |
| Role & Permission      | P0       | High         |
| Menu Management        | P1       | Medium       |
| Profile & Settings     | P1       | Low          |

---

## 2. Login & Authentication

### 2.1 User Story

```
AS A user
I WANT TO login dengan username/email dan password
SO THAT I can access the system securely
```

### 2.2 Acceptance Criteria

- [ ] User dapat login dengan username ATAU email
- [ ] Tampilkan error jika username/password salah
- [ ] Lock account setelah 5x gagal login (30 menit)
- [ ] Tampilkan "Remember Me" checkbox
- [ ] Redirect ke Dashboard setelah login sukses
- [ ] Session timeout setelah 30 menit idle

### 2.3 UI Specifications

| Elemen              | Spesifikasi                                  |
| ------------------- | -------------------------------------------- |
| **Layout**          | Center screen, card dengan logo              |
| **Logo**            | Sentient Factory logo, 120px                 |
| **Input Username**  | Icon user, placeholder "Username atau Email" |
| **Input Password**  | Icon lock, show/hide toggle                  |
| **Button Login**    | Primary color, full width                    |
| **Forgot Password** | Link ke halaman reset                        |

### 2.4 Wireframe Description

```
┌─────────────────────────────────┐
│                                 │
│         [LOGO]                  │
│     Sentient Factory            │
│                                 │
│  ┌─────────────────────────┐   │
│  │ 👤 Username atau Email  │   │
│  └─────────────────────────┘   │
│                                 │
│  ┌─────────────────────────┐   │
│  │ 🔒 Password        👁️   │   │
│  └─────────────────────────┘   │
│                                 │
│  ☐ Remember me                  │
│                                 │
│  ┌─────────────────────────┐   │
│  │        LOGIN            │   │
│  └─────────────────────────┘   │
│                                 │
│     Forgot Password?            │
│                                 │
└─────────────────────────────────┘
```

### 2.5 API Endpoints

| Method | Endpoint                    | Request                          | Response                    |
| ------ | --------------------------- | -------------------------------- | --------------------------- |
| POST   | `/api/auth/login`           | `{username, password, remember}` | `{token, user, expires_at}` |
| POST   | `/api/auth/logout`          | `{token}`                        | `{success}`                 |
| POST   | `/api/auth/forgot-password` | `{email}`                        | `{success, message}`        |
| POST   | `/api/auth/reset-password`  | `{token, new_password}`          | `{success}`                 |

---

## 3. Dashboard Overview

### 3.1 User Story

```
AS A manager
I WANT TO see key metrics at a glance
SO THAT I can quickly assess business health
```

### 3.2 Dashboard Widgets

| Widget                  | Data Source        | Refresh  |
| ----------------------- | ------------------ | -------- |
| **Total Revenue (MTD)** | t_so               | Realtime |
| **Pending Orders**      | t_so (status=Open) | Realtime |
| **Production OEE**      | t_wo, t_shift_log  | 10 min   |
| **Active Alerts**       | ai_alert           | Realtime |
| **Top 5 Customers**     | t_so, m_cust       | Daily    |
| **Revenue Trend**       | t_so (30 days)     | Hourly   |

### 3.3 Layout Structure

```
┌─────────────────────────────────────────────────────────────┐
│ [≡] Sentient Factory                    🔔 3  👤 John Doe ▼│
├──────────┬──────────────────────────────────────────────────┤
│ SIDEBAR  │                                                  │
│          │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐    │
│ Dashboard│  │Revenue │ │Pending │ │OEE     │ │Alerts  │    │
│ Sales    │  │Rp 5.2B │ │45      │ │87.5%   │ │🔴 3    │    │
│ Purchase │  └────────┘ └────────┘ └────────┘ └────────┘    │
│ Prod     │                                                  │
│ Inventory│  ┌─────────────────────────────────────────────┐ │
│ Finance  │  │         Revenue Trend (30 Days)             │ │
│ AI Chat  │  │  [=====Chart Area=====]                     │ │
│ Settings │  └─────────────────────────────────────────────┘ │
│          │                                                  │
│          │  ┌──────────────────┐ ┌──────────────────┐      │
│          │  │ Top Customers    │ │ Recent Activity  │      │
│          │  │ 1. PT ABC...     │ │ • SO#123 created │      │
│          │  │ 2. CV XYZ...     │ │ • PO#456 approved│      │
│          │  └──────────────────┘ └──────────────────┘      │
└──────────┴──────────────────────────────────────────────────┘
```

### 3.4 API Endpoints

| Method | Endpoint                         | Response                                 |
| ------ | -------------------------------- | ---------------------------------------- |
| GET    | `/api/dashboard/summary`         | `{revenue, pending_orders, oee, alerts}` |
| GET    | `/api/dashboard/revenue-trend`   | `{data: [{date, amount}]}`               |
| GET    | `/api/dashboard/top-customers`   | `{data: [{id, name, revenue}]}`          |
| GET    | `/api/dashboard/recent-activity` | `{data: [{type, message, time}]}`        |

---

## 4. User Management

### 4.1 User Story

```
AS A system admin
I WANT TO manage user accounts (CRUD)
SO THAT I can control who has access to the system
```

### 4.2 Features

| Feature                 | Description                             | Priority |
| ----------------------- | --------------------------------------- | -------- |
| **List Users**          | Table dengan search, filter, pagination | P0       |
| **Create User**         | Form create user baru                   | P0       |
| **Edit User**           | Form edit user existing                 | P0       |
| **Delete User**         | Soft delete dengan konfirmasi           | P0       |
| **Reset Password**      | Admin reset password user               | P0       |
| **Activate/Deactivate** | Toggle status user                      | P0       |
| **View Sessions**       | Lihat active sessions user              | P1       |
| **Force Logout**        | Paksa logout user tertentu              | P1       |

### 4.3 List View Columns

| Column     | Sortable | Filterable   |
| ---------- | -------- | ------------ |
| Username   | ✅        | ✅            |
| Email      | ✅        | ✅            |
| Role       | ✅        | ✅ (dropdown) |
| Status     | ✅        | ✅ (dropdown) |
| Last Login | ✅        | ❌            |
| Actions    | ❌        | ❌            |

### 4.4 Create/Edit Form Fields

| Field    | Type     | Validation             | Required        |
| -------- | -------- | ---------------------- | --------------- |
| Username | Text     | Min 4 char, unique     | ✅               |
| Email    | Email    | Valid email, unique    | ✅               |
| Password | Password | Min 8 char, complexity | ✅ (create only) |
| Role     | Dropdown | From adm_role          | ✅               |
| Status   | Toggle   | Active/Inactive        | ✅               |

### 4.5 API Endpoints

| Method | Endpoint                        | Description            |
| ------ | ------------------------------- | ---------------------- |
| GET    | `/api/users`                    | List users (paginated) |
| GET    | `/api/users/:id`                | Get user detail        |
| POST   | `/api/users`                    | Create user            |
| PUT    | `/api/users/:id`                | Update user            |
| DELETE | `/api/users/:id`                | Soft delete user       |
| POST   | `/api/users/:id/reset-password` | Reset password         |
| POST   | `/api/users/:id/toggle-status`  | Activate/Deactivate    |

---

## 5. Role & Permission Management

### 5.1 User Story

```
AS A system admin
I WANT TO define roles and assign permissions
SO THAT I can control access to features per role
```

### 5.2 Features

| Feature               | Description                            |
| --------------------- | -------------------------------------- |
| **List Roles**        | Table of all roles                     |
| **Create Role**       | Form create role baru                  |
| **Edit Role**         | Form edit role + permission assignment |
| **Delete Role**       | Soft delete (jika tidak ada user)      |
| **Permission Matrix** | Checkbox matrix: Role x Permission     |

### 5.3 Permission Matrix UI

```
┌─────────────────────────────────────────────────────────────┐
│ Role: Sales Manager                              [SAVE]     │
├─────────────────────────────────────────────────────────────┤
│ Module        │ Create │ Read │ Update │ Delete │ Approve  │
├───────────────┼────────┼──────┼────────┼────────┼──────────┤
│ Sales Order   │   ☑️   │  ☑️  │   ☑️   │   ☐   │    ☑️    │
│ Delivery Order│   ☐    │  ☑️  │   ☐    │   ☐   │    ☐     │
│ Purchase Order│   ☐    │  ☑️  │   ☐    │   ☐   │    ☐     │
│ Inventory     │   ☐    │  ☑️  │   ☐    │   ☐   │    ☐     │
│ Finance       │   ☐    │  ☑️  │   ☐    │   ☐   │    ☐     │
│ Admin         │   ☐    │  ☐   │   ☐    │   ☐   │    ☐     │
└───────────────┴────────┴──────┴────────┴────────┴──────────┘
```

---

## 6. Menu Management

### 6.1 User Story

```
AS A system admin
I WANT TO configure sidebar menus dynamically
SO THAT I can control navigation per role without code changes
```

### 6.2 Features

| Feature               | Description                         |
| --------------------- | ----------------------------------- |
| **Menu Tree**         | Drag-drop reorder menu items        |
| **Create Menu**       | Form create menu baru               |
| **Edit Menu**         | Edit name, icon, path, parent       |
| **Assign to Role**    | Checkbox: which roles see this menu |
| **Toggle Visibility** | Enable/disable menu item            |

### 6.3 Menu Item Form

| Field      | Type        | Description                   |
| ---------- | ----------- | ----------------------------- |
| Name       | Text        | Display name di sidebar       |
| Icon       | Icon Picker | FontAwesome/Material icon     |
| Path       | Text        | URL path (e.g., /sales-order) |
| Parent     | Dropdown    | Parent menu (NULL = root)     |
| Sort Order | Number      | Urutan tampil                 |
| Permission | Dropdown    | Permission code required      |
| Is Active  | Toggle      | Show/hide                     |

---

## 7. Profile & Settings

### 7.1 Features

| Feature             | Description                    |
| ------------------- | ------------------------------ |
| **View Profile**    | Lihat data diri sendiri        |
| **Edit Profile**    | Update nama, email, foto       |
| **Change Password** | Ganti password sendiri         |
| **Preferences**     | Theme, language, notifications |

### 7.2 Change Password Form

| Field            | Validation              |
| ---------------- | ----------------------- |
| Current Password | Must match              |
| New Password     | Min 8 char, complexity  |
| Confirm Password | Must match new password |

---

## 8. Technical Notes for Frontend

### 8.1 State Management
- Use **Redux/Zustand** for global state (user, permissions, menus)
- Cache permissions on login, re-fetch on token refresh

### 8.2 Route Guard
```typescript
// Pseudo-code
function ProtectedRoute({ permission, children }) {
  const { permissions } = useAuth();
  if (!permissions.includes(permission)) {
    return <AccessDenied />;
  }
  return children;
}
```

### 8.3 Menu Rendering
- Fetch menu tree from `/api/menus` on login
- Store in Redux/Context
- Render sidebar dynamically based on menu tree

---

## 9. Deliverables Checklist

- [ ] Login Page (Responsive)
- [ ] Forgot Password Page
- [ ] Reset Password Page
- [ ] Dashboard with 6 widgets
- [ ] User List Page
- [ ] User Create/Edit Form
- [ ] Role List Page
- [ ] Role Create/Edit with Permission Matrix
- [ ] Menu Management Page
- [ ] Profile Page
- [ ] Change Password Modal
- [ ] Settings/Preferences Page

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
