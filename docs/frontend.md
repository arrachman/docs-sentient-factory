# 💻 Frontend Development Specification

> **Parent Docs:** [UI Design](ui-design.md), [Architecture](architecture.md), [BPA-1](bpa-1.md), [BPA-2](bpa-2.md), [BPA-3](bpa-3.md)
> **Last Sync:** 2024-12-27

---

## 1. Tech Stack & Setup

### 1.1 Technology Stack

> **📦 Metronic Local Path:** `metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite/`
> **Reference:** [Metronic Reference Guide](metronic-reference.md)

| Category   | Technology           | Version  | Purpose                |
| ---------- | -------------------- | -------- | ---------------------- |
| Framework  | React                | 19.1.1   | UI Library             |
| Language   | TypeScript           | 5.9.2    | Type Safety            |
| Build      | Vite                 | 7.1.3    | Build Tool             |
| Template   | Metronic             | 9.3.7    | Premium Admin Template |
| Styling    | Tailwind CSS         | 4.1.12   | CSS Framework          |
| State      | Zustand              | 4.x      | State Management       |
| API        | Tanstack React Query | 5.85.5   | Data Fetching          |
| Tables     | Tanstack React Table | 8.21.3   | Data Tables            |
| Forms      | React Hook Form      | 7.62.0   | Form Management        |
| Validation | Zod                  | 3.25.67  | Schema Validation      |
| Router     | React Router         | 7.8.2    | Navigation             |
| Charts     | ApexCharts           | 4.7.0    | Data Visualization     |
| Icons      | Lucide React         | 0.541.0  | Icon Library           |
| UI         | Radix UI             | 1.4.3    | Primitives             |
| Toast      | Sonner               | 2.0.7    | Notifications          |
| Animation  | Motion               | 12.23.12 | Animations             |

### 1.2 Project Setup

```bash
# Option 1: Copy from local Metronic (RECOMMENDED)
cp -r metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite frontend
cd frontend

# Install dependencies
npm install

# Development (runs on port 5173)
npm run dev

# Build
npm run build
```

```bash
# Option 2: Create fresh project and copy components
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install

# Copy Metronic components
cp -r ../metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite/src/components/ui src/components/
cp -r ../metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite/src/layouts/demo4 src/layouts/
cp -r ../metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite/src/hooks src/
cp -r ../metronic-v9.3.7/metronic-tailwind-react-starter/typescript/vite/src/css src/

# Install Metronic dependencies
npm install @tanstack/react-query @tanstack/react-table react-hook-form @hookform/resolvers zod
npm install tailwindcss lucide-react radix-ui sonner clsx tailwind-merge class-variance-authority
npm install apexcharts react-apexcharts motion date-fns
```


### 1.3 Folder Structure

```
src/
├── app/
│   ├── modules/           # Feature modules
│   │   ├── auth/          # Login, forgot password
│   │   ├── dashboard/     # Dashboard pages
│   │   ├── admin/         # User, Role management
│   │   ├── master/        # Customer, Item, Vendor
│   │   ├── sales/         # SO, DO
│   │   ├── procurement/   # PO
│   │   ├── production/    # WO
│   │   ├── inventory/     # Stock management
│   │   └── ai/            # AI Chat, Alerts, Reports
│   ├── routing/           # Route definitions
│   └── App.tsx
├── components/
│   ├── layout/            # Sidebar, Header, Footer
│   ├── form/              # Input, Select, DatePicker
│   ├── table/             # DataTable, Pagination
│   └── feedback/          # Modal, Toast, Alert
├── hooks/                 # Custom hooks
├── services/              # API services
├── stores/                # Zustand stores
├── types/                 # TypeScript types
├── utils/                 # Utility functions
└── config/                # App configuration
```

```env
# .env.development
VITE_API_URL=http://localhost:8000/api
VITE_WS_URL=ws://localhost:8000
VITE_APP_NAME=Sentient Factory

# .env.production
VITE_API_URL=https://api.sentient.local/api
VITE_WS_URL=wss://api.sentient.local
VITE_APP_NAME=Sentient Factory
```

### 1.5 Layout Choice: Layout 4 (Dark Sidebar Compact)

**Recommended Layout:** Metronic Starter Kit - **Layout 4**

**Kenapa Layout 4?**
- **Dark Sidebar** - Modern, industrial look cocok untuk manufacturing
- **Icon-only mode** - Compact sidebar, maximize content area
- **Expand on hover** - Lihat label saat hover, efisien
- **Professional appearance** - Enterprise-grade ERP feel
- **Better for widescreen** - Optimal di monitor besar pabrik

**Layout 4 Wireframe:**
```
┌────────────────────────────────────────────────────────────────┐
│  [≡]  🏭 SENTIENT FACTORY                       🔔 📩 👤 User  │
├──────┬─────────────────────────────────────────────────────────┤
│      │                                                         │
│  📊  │  MAIN CONTENT AREA (Maximized)                          │
│      │                                                         │
│  📦  │  ┌─────────────────────────────────────────────────────┐│
│      │  │                                                     ││
│  🚚  │  │  KPI Cards / Forms / Tables / Charts                ││
│      │  │                                                     ││
│  🛒  │  │                                                     ││
│      │  │                                                     ││
│  🏭  │  └─────────────────────────────────────────────────────┘│
│      │                                                         │
│  📈  │                                                         │
│      │                                                         │
│  🤖  │  ← AI Chat (expand on hover: "AI Assistant")            │
│      │                                                         │
│  ⚙️  │                                                         │
│      │                                                         │
└──────┴─────────────────────────────────────────────────────────┘
   ↑
  Dark sidebar
  Icon-only (60px)
  Expand on hover (240px)
```

**Layout 4 Features:**

| Feature        | Description                    |
| -------------- | ------------------------------ |
| Sidebar Width  | 60px collapsed, 240px expanded |
| Sidebar Theme  | Dark (`#0F172A` Slate-900)     |
| Menu Style     | Icon-only with tooltip         |
| Hover Behavior | Expand to show labels          |
| Header         | Fixed top, light background    |
| Content Area   | Full width minus sidebar       |

**Folder Structure for Layout 4:**
```
src/
├── app/
│   └── layouts/
│       └── layout4/                # Use Layout 4
│           ├── Layout4.tsx         # Main layout wrapper
│           ├── DarkSidebar.tsx     # Dark collapsible sidebar
│           ├── SidebarMenu.tsx     # Icon menu items
│           ├── Header.tsx          # Top header bar
│           └── Footer.tsx          # Optional footer
├── css/
│   └── sidebar-dark.css            # Dark sidebar styles
```

**Sidebar Menu Config:**
```typescript
// src/config/menuItems.ts

export const menuItems = [
  // Dashboard
  { 
    title: 'Dashboard',
    icon: 'chart-bar',
    path: '/dashboard',
    children: [
      { label: 'Overview', path: '/dashboard/overview' },
      { label: 'AI Insights', path: '/dashboard/insights' },
      { label: 'Alerts', path: '/dashboard/alerts' },
    ]
  },

  // Administrator
  {
    title: 'Administrator',
    icon: 'shield-check',
    path: '/admin',
    children: [
      { label: 'User Management', path: '/admin/users' },
      { label: 'Role & Permission', path: '/admin/roles' },
      { label: 'Menu Config', path: '/admin/menu' },
      { label: 'Audit Log', path: '/admin/audit' },
    ]
  },

  // Master Data
  {
    title: 'Master Data',
    icon: 'database',
    path: '/master',
    children: [
      { label: 'Customer', path: '/master/customers' },
      { label: 'Item / Product', path: '/master/items' },
      { label: 'Vendor', path: '/master/vendors' },
      { label: 'Machine', path: '/master/machines' },
      { label: 'Warehouse', path: '/master/warehouses' },
    ]
  },

  // Sales
  {
    title: 'Sales',
    icon: 'shopping-cart',
    path: '/sales',
    children: [
      { label: 'Sales Order', path: '/sales/orders' },
      { label: 'Delivery Order', path: '/sales/deliveries' },
      { label: 'Account Receivable', path: '/sales/ar' },
    ]
  },

  // Procurement
  {
    title: 'Procurement',
    icon: 'clipboard-list',
    path: '/procurement',
    children: [
      { label: 'Purchase Order', path: '/procurement/orders' },
      { label: 'Account Payable', path: '/procurement/ap' },
    ]
  },

  // Production
  {
    title: 'Production',
    icon: 'cog',
    path: '/production',
    children: [
      { label: 'Work Order', path: '/production/work-orders' },
      { label: 'Production Log', path: '/production/logs' },
      { label: 'Maintenance', path: '/production/maintenance' },
    ]
  },

  // Inventory
  {
    title: 'Inventory',
    icon: 'cube',
    path: '/inventory',
    children: [
      { label: 'Stock Overview', path: '/inventory/stock' },
      { label: 'Stock Movement', path: '/inventory/movements' },
      { label: 'Stock Opname', path: '/inventory/opname' },
      { label: 'Stock Transfer', path: '/inventory/transfer' },
    ]
  },

  // AI Assistant
  {
    title: 'AI Assistant',
    icon: 'sparkles',
    path: '/ai',
    children: [
      { label: 'Chat with AI', path: '/ai/chat' },
      { label: 'Reports', path: '/ai/reports' },
    ]
  },

  // Settings
  {
    title: 'Settings',
    icon: 'adjustments',
    path: '/settings',
    children: [
      { label: 'Profile', path: '/settings/profile' },
      { label: 'Change Password', path: '/settings/password' },
      { label: 'Preferences', path: '/settings/preferences' },
    ]
  }
];
```

**Dark Sidebar CSS:**
```css
/* src/css/sidebar-dark.css */

.sidebar-dark {
  background: linear-gradient(180deg, #0F172A 0%, #1E293B 100%);
  width: 60px;
  transition: width 0.3s ease;
}

.sidebar-dark:hover,
.sidebar-dark.expanded {
  width: 240px;
}

.sidebar-dark .menu-icon {
  color: #94A3B8;  /* Slate-400 */
}

.sidebar-dark .menu-icon:hover,
.sidebar-dark .menu-icon.active {
  color: #3B82F6;  /* Primary Light */
  background: rgba(59, 130, 246, 0.1);
}

.sidebar-dark .menu-label {
  opacity: 0;
  transition: opacity 0.2s ease;
}

.sidebar-dark:hover .menu-label,
.sidebar-dark.expanded .menu-label {
  opacity: 1;
}
```


### 1.6 Color Palette (Sentient Factory Theme)

**Theme:** Industrial Modern (Blue + Gray)

```css
/* src/css/config.ktui.css */

:root {
  /* ═══════════════════════════════════════════════════════════
     SENTIENT FACTORY COLOR PALETTE
     Theme: Industrial Modern
  ═══════════════════════════════════════════════════════════ */
  
  /* Primary - Industrial Blue (Main brand color) */
  --kt-primary: #1E40AF;           /* Blue-800 */
  --kt-primary-light: #3B82F6;     /* Blue-500 */
  --kt-primary-dark: #1E3A8A;      /* Blue-900 */
  
  /* Secondary - Slate Gray (Industrial feel) */
  --kt-secondary: #475569;         /* Slate-600 */
  --kt-secondary-light: #94A3B8;   /* Slate-400 */
  
  /* Success - Green (Approved, Completed) */
  --kt-success: #10B981;           /* Emerald-500 */
  --kt-success-light: #D1FAE5;     /* Emerald-100 */
  
  /* Warning - Amber (Pending, Attention) */
  --kt-warning: #F59E0B;           /* Amber-500 */
  --kt-warning-light: #FEF3C7;     /* Amber-100 */
  
  /* Danger - Red (Rejected, Error, Critical) */
  --kt-danger: #EF4444;            /* Red-500 */
  --kt-danger-light: #FEE2E2;      /* Red-100 */
  
  /* Info - Cyan (Informational, AI) */
  --kt-info: #06B6D4;              /* Cyan-500 */
  --kt-info-light: #CFFAFE;        /* Cyan-100 */
  
  /* Neutral - Gray Scale */
  --kt-gray-100: #F3F4F6;
  --kt-gray-200: #E5E7EB;
  --kt-gray-300: #D1D5DB;
  --kt-gray-400: #9CA3AF;
  --kt-gray-500: #6B7280;
  --kt-gray-600: #4B5563;
  --kt-gray-700: #374151;
  --kt-gray-800: #1F2937;
  --kt-gray-900: #111827;
  
  /* Dark Mode Background */
  --kt-dark-bg: #0F172A;           /* Slate-900 */
  --kt-dark-card: #1E293B;         /* Slate-800 */
}
```

**Color Usage Summary:**

| Color             | Hex Code  | Usage                         |
| ----------------- | --------- | ----------------------------- |
| **Primary**       | `#1E40AF` | Buttons, links, active states |
| **Primary Light** | `#3B82F6` | Hover states, accents         |
| **Secondary**     | `#475569` | Cancel buttons, muted text    |
| **Success**       | `#10B981` | Approved, completed, save     |
| **Warning**       | `#F59E0B` | Pending, attention needed     |
| **Danger**        | `#EF4444` | Delete, reject, errors        |
| **Info**          | `#06B6D4` | AI features, informational    |

### 1.7 Metronic Component Library (KtUI)

**Built-in Components to Use:**

| Component       | Metronic Class | Usage                              |
| --------------- | -------------- | ---------------------------------- |
| `KTDataTable`   | Built-in       | All list pages (SO, PO, Inventory) |
| `KTMenu`        | Built-in       | Sidebar navigation                 |
| `KTCard`        | Built-in       | Dashboard KPI cards, detail cards  |
| `KTModal`       | Built-in       | Create/Edit forms, confirmations   |
| `KTTabs`        | Built-in       | Detail page tab sections           |
| `KTSelect`      | Built-in       | Searchable dropdowns               |
| `KTDatepicker`  | Built-in       | Date inputs, date range filters    |
| `KTChartWidget` | Built-in       | Dashboard charts (ApexCharts)      |
| `KTAlert`       | Built-in       | Inline notifications               |
| `KTBadge`       | Built-in       | Status indicators                  |
| `KTButton`      | Built-in       | All button variants                |
| `KTInput`       | Built-in       | Form text inputs                   |
| `KTToast`       | Built-in       | Toast notifications                |

**Status Badge Configuration:**

```typescript
// src/config/statusColors.ts

export const statusColors = {
  // Document Status
  draft: { bg: 'bg-gray-100', text: 'text-gray-800', label: 'Draft' },
  pending: { bg: 'bg-yellow-100', text: 'text-yellow-800', label: 'Pending' },
  approved: { bg: 'bg-green-100', text: 'text-green-800', label: 'Approved' },
  in_progress: { bg: 'bg-blue-100', text: 'text-blue-800', label: 'In Progress' },
  completed: { bg: 'bg-emerald-100', text: 'text-emerald-800', label: 'Completed' },
  rejected: { bg: 'bg-red-100', text: 'text-red-800', label: 'Rejected' },
  cancelled: { bg: 'bg-gray-200', text: 'text-gray-600', label: 'Cancelled' },
  
  // Inventory Status
  in_stock: { bg: 'bg-green-100', text: 'text-green-800', label: 'In Stock' },
  low_stock: { bg: 'bg-yellow-100', text: 'text-yellow-800', label: 'Low Stock' },
  out_of_stock: { bg: 'bg-red-100', text: 'text-red-800', label: 'Out of Stock' },
  
  // User Status
  active: { bg: 'bg-green-100', text: 'text-green-800', label: 'Active' },
  inactive: { bg: 'bg-gray-100', text: 'text-gray-600', label: 'Inactive' },
  
  // Alert Severity
  high: { bg: 'bg-red-100', text: 'text-red-800', label: 'High' },
  medium: { bg: 'bg-yellow-100', text: 'text-yellow-800', label: 'Medium' },
  low: { bg: 'bg-blue-100', text: 'text-blue-800', label: 'Low' },
};

// Usage in component
<span className={`${statusColors[status].bg} ${statusColors[status].text} px-2 py-1 rounded-full text-xs font-medium`}>
  {statusColors[status].label}
</span>
```

---

## 2. Routing & Navigation

### 2.1 Route Definitions

```typescript
// src/app/routing/Routes.tsx

const routes = [
  // Public Routes
  { path: '/auth/login', element: <LoginPage />, public: true },
  { path: '/auth/forgot-password', element: <ForgotPasswordPage />, public: true },
  
  // Protected Routes - Dashboard
  { path: '/', element: <DashboardPage />, permission: 'dashboard.view' },
  { path: '/dashboard', element: <DashboardPage />, permission: 'dashboard.view' },
  
  // Protected Routes - Admin
  { path: '/admin/users', element: <UserListPage />, permission: 'user.view' },
  { path: '/admin/users/create', element: <UserFormPage />, permission: 'user.create' },
  { path: '/admin/users/:id', element: <UserFormPage />, permission: 'user.edit' },
  { path: '/admin/roles', element: <RoleListPage />, permission: 'role.view' },
  { path: '/admin/roles/:id', element: <RoleFormPage />, permission: 'role.edit' },
  { path: '/admin/audit', element: <AuditLogPage />, permission: 'audit.view' },
  
  // Protected Routes - Master Data
  { path: '/master/customers', element: <CustomerListPage />, permission: 'customer.view' },
  { path: '/master/customers/:id', element: <CustomerFormPage />, permission: 'customer.edit' },
  { path: '/master/items', element: <ItemListPage />, permission: 'item.view' },
  { path: '/master/items/:id', element: <ItemFormPage />, permission: 'item.edit' },
  { path: '/master/vendors', element: <VendorListPage />, permission: 'vendor.view' },
  
  // Protected Routes - Sales
  { path: '/sales/orders', element: <SalesOrderListPage />, permission: 'so.view' },
  { path: '/sales/orders/create', element: <SalesOrderFormPage />, permission: 'so.create' },
  { path: '/sales/orders/:id', element: <SalesOrderDetailPage />, permission: 'so.view' },
  { path: '/sales/orders/:id/edit', element: <SalesOrderFormPage />, permission: 'so.edit' },
  { path: '/sales/deliveries', element: <DeliveryOrderListPage />, permission: 'do.view' },
  { path: '/sales/deliveries/:id', element: <DeliveryOrderDetailPage />, permission: 'do.view' },
  
  // Protected Routes - Procurement
  { path: '/procurement/orders', element: <PurchaseOrderListPage />, permission: 'po.view' },
  { path: '/procurement/orders/create', element: <PurchaseOrderFormPage />, permission: 'po.create' },
  { path: '/procurement/orders/:id', element: <PurchaseOrderDetailPage />, permission: 'po.view' },
  
  // Protected Routes - Production
  { path: '/production/work-orders', element: <WorkOrderListPage />, permission: 'wo.view' },
  { path: '/production/work-orders/:id', element: <WorkOrderDetailPage />, permission: 'wo.view' },
  
  // Protected Routes - Inventory
  { path: '/inventory/stock', element: <StockListPage />, permission: 'inv.view' },
  { path: '/inventory/movements', element: <StockMovementPage />, permission: 'inv.view' },
  { path: '/inventory/adjustments', element: <StockAdjustmentPage />, permission: 'inv.adjust' },
  
  // Protected Routes - AI & Analytics
  { path: '/ai/chat', element: <AIChatPage />, permission: 'ai.chat' },
  { path: '/alerts', element: <AlertCenterPage />, permission: 'alert.view' },
  { path: '/reports', element: <ReportViewerPage />, permission: 'report.view' },
  
  // Protected Routes - Settings
  { path: '/settings/profile', element: <ProfilePage /> },
  { path: '/settings/password', element: <ChangePasswordPage /> },
];
```

### 2.2 Protected Route Component

```typescript
// src/components/auth/ProtectedRoute.tsx

interface ProtectedRouteProps {
  permission?: string;
  children: React.ReactNode;
}

export const ProtectedRoute: React.FC<ProtectedRouteProps> = ({
  permission,
  children
}) => {
  const { isAuthenticated, hasPermission } = useAuth();
  
  if (!isAuthenticated) {
    return <Navigate to="/auth/login" />;
  }
  
  if (permission && !hasPermission(permission)) {
    return <ForbiddenPage />;
  }
  
  return <>{children}</>;
};
```

### 2.3 Breadcrumb Config

```typescript
// src/config/breadcrumbs.ts

const breadcrumbConfig = {
  '/': [{ label: 'Dashboard', path: '/' }],
  '/sales/orders': [
    { label: 'Dashboard', path: '/' },
    { label: 'Sales Order', path: '/sales/orders' },
  ],
  '/sales/orders/create': [
    { label: 'Dashboard', path: '/' },
    { label: 'Sales Order', path: '/sales/orders' },
    { label: 'Create', path: null },
  ],
  '/sales/orders/:id': [
    { label: 'Dashboard', path: '/' },
    { label: 'Sales Order', path: '/sales/orders' },
    { label: ':id', path: null },  // Dynamic
  ],
};
```

---

## 3. Core Pages (BPA-1)

### 3.1 Login Page

**Route:** `/auth/login`
**Layout:** AuthLayout (centered, no sidebar)
**Reference:** [ui-design.md Section 4.1](ui-design.md#41-login-page)

```typescript
// Page Specification
interface LoginPageSpec {
  route: '/auth/login';
  layout: 'AuthLayout';
  components: ['LoginForm', 'Logo', 'Footer'];
  
  form: {
    fields: {
      username: { type: 'text', required: true, maxLength: 50 };
      password: { type: 'password', required: true };
      rememberMe: { type: 'checkbox' };
    };
    validation: 'loginSchema';
    submit: 'POST /api/auth/login';
  };
  
  actions: {
    onSuccess: 'redirect to dashboard';
    onError: 'show error toast';
    forgotPassword: 'navigate to /auth/forgot-password';
  };
}
```

**Form Validation:**
```typescript
const loginSchema = z.object({
  username: z.string().min(1, 'Username required'),
  password: z.string().min(1, 'Password required'),
  rememberMe: z.boolean().optional(),
});
```

---

### 3.2 Dashboard Page

**Route:** `/dashboard`
**Layout:** MainLayout (with sidebar)
**Reference:** [ui-design.md Section 2.5](ui-design.md#25-dashboard-template)

```typescript
// Page Specification
interface DashboardPageSpec {
  route: '/dashboard';
  layout: 'MainLayout';
  
  components: [
    'KPICardGrid',      // Revenue, Orders, OEE, Alerts
    'RevenueChart',     // Line chart - 30 days trend
    'ProductionChart',  // Bar chart - by machine
    'TopCustomersTable',// Top 5 customers
    'RecentActivityList', // Recent activities
  ];
  
  api: {
    kpi: 'GET /api/dashboard/kpi';
    revenueChart: 'GET /api/dashboard/revenue-trend';
    productionChart: 'GET /api/dashboard/production-by-machine';
    topCustomers: 'GET /api/dashboard/top-customers';
    recentActivity: 'GET /api/dashboard/recent-activity';
  };
  
  refresh: 'every 5 minutes';
}
```

**KPI Card Component:**
```typescript
interface KPICardProps {
  title: string;
  value: string | number;
  trend?: { value: number; direction: 'up' | 'down' };
  icon: React.ComponentType;
  color: 'primary' | 'success' | 'warning' | 'danger';
}
```

---

### 3.3 User Management Pages

**List Route:** `/admin/users`
**Form Route:** `/admin/users/:id`
**Reference:** [ui-design.md Section 4.2](ui-design.md#42-user-management)

```typescript
// List Page
interface UserListPageSpec {
  route: '/admin/users';
  layout: 'MainLayout';
  
  components: [
    'PageHeader',       // Title + Create button
    'FilterBar',        // Search, Role filter, Status filter
    'DataTable',        // User list
    'Pagination',
  ];
  
  table: {
    columns: ['username', 'email', 'role', 'status', 'lastLogin', 'actions'];
    sortable: ['username', 'email', 'role', 'lastLogin'];
    actions: ['view', 'edit', 'delete'];
  };
  
  api: {
    list: 'GET /api/users?page=&limit=&search=&role=&status=';
    delete: 'DELETE /api/users/:id';
  };
}

// Form Page
interface UserFormPageSpec {
  route: '/admin/users/:id';
  layout: 'MainLayout';
  
  form: {
    fields: {
      username: { type: 'text', required: true };
      email: { type: 'email', required: true };
      password: { type: 'password', required: 'onCreate' };
      role: { type: 'select', options: 'roles', required: true };
      status: { type: 'toggle', default: true };
    };
    
    validation: 'userSchema';
    submit: {
      create: 'POST /api/users';
      update: 'PUT /api/users/:id';
    };
  };
}
```

**User Form Schema:**
```typescript
const userSchema = z.object({
  username: z.string().min(3).max(50).regex(/^[a-zA-Z0-9_]+$/),
  email: z.string().email(),
  password: z.string().min(8).optional(),
  role: z.string().uuid(),
  status: z.boolean(),
});
```

---

### 3.4 Role Management Pages

**Route:** `/admin/roles`
**Reference:** [ui-design.md Section 4.3](ui-design.md#43-role-permission-matrix)

```typescript
interface RolePageSpec {
  route: '/admin/roles';
  layout: 'MainLayout';
  
  components: [
    'RoleSelector',      // Dropdown to select role
    'PermissionMatrix',  // Checkbox grid
    'SaveButton',
  ];
  
  permissionMatrix: {
    modules: ['Sales Order', 'Delivery Order', 'Purchase Order', ...];
    actions: ['create', 'read', 'update', 'delete', 'approve', 'export'];
  };
  
  api: {
    roles: 'GET /api/roles';
    permissions: 'GET /api/roles/:id/permissions';
    save: 'PUT /api/roles/:id/permissions';
  };
}
```

---

## 4. Transaction Pages (BPA-2)

### 4.1 Sales Order Pages

**List Route:** `/sales/orders`
**Create Route:** `/sales/orders/create`
**Detail Route:** `/sales/orders/:id`
**Reference:** [ui-design.md Section 6.1](ui-design.md#61-sales-order), [bpa-2.md](bpa-2.md#21-sales-order-so)

```typescript
// List Page
interface SOListPageSpec {
  route: '/sales/orders';
  
  components: [
    'PageHeader',
    'FilterBar',        // Search, Status, Date Range
    'DataTable',
    'Pagination',
  ];
  
  table: {
    columns: ['soNumber', 'customer', 'date', 'total', 'status', 'actions'];
    statusBadge: {
      draft: 'gray',
      pending: 'yellow',
      approved: 'green',
      in_progress: 'blue',
      completed: 'emerald',
      cancelled: 'red',
    };
  };
  
  api: 'GET /api/sales-orders';
}

// Form Page (Create/Edit)
interface SOFormPageSpec {
  route: '/sales/orders/create';
  
  sections: [
    'HeaderSection',    // Customer, Date, Reference
    'ItemsSection',     // Line items table (editable)
    'SummarySection',   // Subtotal, Discount, Tax, Total
    'NotesSection',     // Remarks
  ];
  
  form: {
    header: {
      customerId: { type: 'searchSelect', api: '/api/customers' };
      orderDate: { type: 'datePicker', default: 'today' };
      customerPO: { type: 'text', optional: true };
    };
    
    items: {
      itemId: { type: 'searchSelect', api: '/api/items' };
      qty: { type: 'number', min: 1 };
      price: { type: 'currency', autoFill: 'from item' };
      discount: { type: 'number', min: 0, max: 100 };
      subtotal: { type: 'currency', computed: true };
    };
    
    summary: {
      subtotal: 'sum of item subtotals';
      discountAmount: 'input or percentage';
      taxRate: { type: 'number', default: 11 };
      taxAmount: 'computed';
      total: 'computed';
    };
  };
  
  actions: {
    saveDraft: 'POST /api/sales-orders (status: draft)';
    submit: 'POST /api/sales-orders (status: pending)';
  };
}

// Detail Page
interface SODetailPageSpec {
  route: '/sales/orders/:id';
  
  components: [
    'PageHeader',       // SO Number, Actions (Edit, Print)
    'StatusProgress',   // Draft → Pending → Approved → ...
    'HeaderInfo',       // Customer, Date, Created By
    'ItemsTable',       // Read-only line items
    'SummaryCard',      // Totals
    'ActivityTimeline', // Status change history
  ];
  
  actions: {
    approve: 'POST /api/sales-orders/:id/approve';
    reject: 'POST /api/sales-orders/:id/reject';
    cancel: 'POST /api/sales-orders/:id/cancel';
    createDO: 'navigate to /sales/deliveries/create?soId=:id';
    print: 'generate PDF';
  };
}
```

**SO Form Validation:**
```typescript
const salesOrderSchema = z.object({
  customerId: z.string().uuid('Select customer'),
  orderDate: z.date(),
  customerPO: z.string().optional(),
  items: z.array(z.object({
    itemId: z.string().uuid(),
    qty: z.number().positive(),
    price: z.number().nonnegative(),
    discount: z.number().min(0).max(100),
  })).min(1, 'Add at least one item'),
  notes: z.string().optional(),
});
```

---

### 4.2 Delivery Order Pages

**Reference:** [ui-design.md Section 6.2](ui-design.md#62-delivery-order)

```typescript
interface DOFormPageSpec {
  route: '/sales/deliveries/create';
  
  form: {
    header: {
      soId: { type: 'searchSelect', api: '/api/sales-orders?status=approved' };
      shipDate: { type: 'datePicker' };
      driver: { type: 'text' };
      vehicle: { type: 'text' };
    };
    
    items: {
      // Auto-populated from SO
      soItemId: { readonly: true };
      orderedQty: { readonly: true };
      deliveredQty: { type: 'number', max: 'orderedQty - previousDelivered' };
    };
  };
  
  api: {
    create: 'POST /api/delivery-orders';
    complete: 'POST /api/delivery-orders/:id/complete';
  };
}
```

---

### 4.3 Purchase Order Pages

**Reference:** [ui-design.md Section 6.3](ui-design.md#63-purchase-order)

```typescript
interface POFormPageSpec {
  route: '/procurement/orders/create';
  
  form: {
    header: {
      vendorId: { type: 'searchSelect', api: '/api/vendors' };
      orderDate: { type: 'datePicker' };
      expectedDate: { type: 'datePicker' };
    };
    
    items: {
      itemId: { type: 'searchSelect', api: '/api/items' };
      qty: { type: 'number', min: 1 };
      price: { type: 'currency' };
    };
  };
  
  approvalFlow: {
    threshold1: { amount: 10000000, approver: 'manager' };
    threshold2: { amount: 100000000, approver: 'manager + finance' };
    threshold3: { amount: 'above 100M', approver: '+ director' };
  };
}
```

---

### 4.4 Work Order Pages

**Reference:** [ui-design.md Section 6.4](ui-design.md#64-work-order)

```typescript
interface WODetailPageSpec {
  route: '/production/work-orders/:id';
  
  components: [
    'StatusProgress',   // Created → Assigned → In Progress → Completed
    'HeaderInfo',       // Item, Machine, Target Qty
    'ProductionForm',   // Input: Good Qty, Reject Qty
    'YieldDisplay',     // Calculated yield percentage
    'MaterialConsumption', // BOM items used
  ];
  
  api: {
    start: 'POST /api/work-orders/:id/start';
    reportOutput: 'POST /api/work-orders/:id/output';
    complete: 'POST /api/work-orders/:id/complete';
  };
}
```

---

### 4.5 Inventory Pages

**Reference:** [ui-design.md Section 6.5](ui-design.md#65-inventory-stock)

```typescript
interface StockListPageSpec {
  route: '/inventory/stock';
  
  components: [
    'FilterBar',        // Item search, Warehouse, Status
    'StockTable',       // Item, WH, On Hand, Reserved, Available, Status
    'StockStatusIndicator',
  ];
  
  statusColors: {
    ok: 'green',        // Available > 20% of min
    low: 'yellow',      // Available <= 20%
    critical: 'red',    // Available <= 0
    oversold: 'red-bold', // Available < 0
  };
  
  api: 'GET /api/inventory?item=&warehouse=&status=';
}

interface StockAdjustmentPageSpec {
  route: '/inventory/adjustments';
  
  form: {
    itemId: { type: 'searchSelect' };
    warehouseId: { type: 'select' };
    adjustmentType: { type: 'select', options: ['increase', 'decrease'] };
    qty: { type: 'number' };
    reason: { type: 'select', options: ['damaged', 'expired', 'correction', 'other'] };
    notes: { type: 'textarea' };
  };
  
  api: 'POST /api/inventory/adjustments';
}
```

---

## 5. AI & Analytics Pages (BPA-3)

### 5.1 AI Chat Interface

**Route:** `/ai/chat`
**Reference:** [ui-design.md Section 7.2](ui-design.md#72-ai-chat-interface)

```typescript
interface AIChatPageSpec {
  route: '/ai/chat';
  
  components: [
    'ChatSidebar',      // Chat history list
    'ChatWindow',       // Messages
    'ChatInput',        // Message input + send
    'SourceCitation',   // Data source display
  ];
  
  features: {
    streaming: true;    // Show response as it generates
    markdown: true;     // Render markdown in responses
    charts: true;       // Render inline charts
    citations: true;    // Show data sources
  };
  
  api: {
    sendMessage: 'POST /api/ai/chat';
    getHistory: 'GET /api/ai/chat/history';
    newChat: 'POST /api/ai/chat/new';
  };
}
```

**Chat Message Type:**
```typescript
interface ChatMessage {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: Date;
  sources?: Array<{
    table: string;
    description: string;
  }>;
  confidence?: number;
  charts?: Array<{
    type: 'line' | 'bar' | 'pie';
    data: unknown;
  }>;
}
```

---

### 5.2 Alert Center

**Route:** `/alerts`
**Reference:** [ui-design.md Section 7.3](ui-design.md#73-alertnotification)

```typescript
interface AlertCenterPageSpec {
  route: '/alerts';
  
  components: [
    'FilterTabs',       // All, Unread, Critical, Acknowledged
    'AlertList',        // Alert cards
    'AlertDetail',      // Selected alert detail
  ];
  
  alertCard: {
    severity: 'high' | 'medium' | 'low';
    title: string;
    description: string;
    module: string;
    entity: string;
    timestamp: Date;
    acknowledged: boolean;
  };
  
  actions: {
    acknowledge: 'POST /api/alerts/:id/acknowledge';
    dismiss: 'DELETE /api/alerts/:id';
    viewEntity: 'navigate to related entity';
  };
}
```

---

### 5.3 Report Viewer

**Route:** `/reports`
**Reference:** [ui-design.md Section 7.4](ui-design.md#74-reports)

```typescript
interface ReportViewerPageSpec {
  route: '/reports';
  
  components: [
    'ReportSelector',   // Report type dropdown
    'ParameterForm',    // Dynamic params
    'GenerateButton',
    'ReportPreview',    // Table/Chart display
    'ExportButtons',    // PDF, Excel
  ];
  
  reports: [
    { id: 'sales-summary', params: ['dateFrom', 'dateTo', 'customerId'] },
    { id: 'po-status', params: ['dateFrom', 'dateTo', 'vendorId'] },
    { id: 'production-output', params: ['dateFrom', 'dateTo', 'machineId'] },
    { id: 'stock-balance', params: ['warehouseId', 'asOfDate'] },
    { id: 'ar-aging', params: ['asOfDate'] },
    { id: 'ap-aging', params: ['asOfDate'] },
  ];
  
  api: {
    generate: 'POST /api/reports/:type';
    export: 'GET /api/reports/:id/export?format=pdf|excel';
  };
}
```

---

## 6. Shared Components

### 6.1 Layout Components

```typescript
// MainLayout.tsx
interface MainLayoutProps {
  children: React.ReactNode;
}
// Includes: Sidebar, Header, Content Area, Footer

// Sidebar.tsx
interface SidebarProps {
  collapsed: boolean;
  menuItems: MenuItem[];
}

// Header.tsx
interface HeaderProps {
  showNotifications: boolean;
  user: User;
}

// Breadcrumb.tsx
interface BreadcrumbProps {
  items: Array<{ label: string; path: string | null }>;
}
```

### 6.2 Form Components

```typescript
// TextInput.tsx
interface TextInputProps {
  name: string;
  label: string;
  placeholder?: string;
  required?: boolean;
  error?: string;
  disabled?: boolean;
}

// SelectInput.tsx
interface SelectInputProps {
  name: string;
  label: string;
  options: Array<{ value: string; label: string }>;
  searchable?: boolean;
  clearable?: boolean;
}

// DatePicker.tsx
interface DatePickerProps {
  name: string;
  label: string;
  minDate?: Date;
  maxDate?: Date;
  format?: string;  // default: 'dd/MM/yyyy'
}

// CurrencyInput.tsx
interface CurrencyInputProps {
  name: string;
  label: string;
  currency?: string;  // default: 'IDR'
  min?: number;
  max?: number;
}

// FileUpload.tsx
interface FileUploadProps {
  name: string;
  label: string;
  accept?: string[];  // e.g., ['.pdf', '.jpg']
  maxSize?: number;   // in bytes
  multiple?: boolean;
}
```

### 6.3 Table Components

```typescript
// DataTable.tsx
interface DataTableProps<T> {
  columns: Column<T>[];
  data: T[];
  loading?: boolean;
  sortable?: boolean;
  selectable?: boolean;
  onRowClick?: (row: T) => void;
  pagination?: PaginationProps;
}

interface Column<T> {
  key: keyof T;
  header: string;
  sortable?: boolean;
  render?: (value: unknown, row: T) => React.ReactNode;
  width?: string;
}

// Pagination.tsx
interface PaginationProps {
  currentPage: number;
  totalPages: number;
  pageSize: number;
  totalItems: number;
  onPageChange: (page: number) => void;
  onPageSizeChange: (size: number) => void;
}

// StatusBadge.tsx
interface StatusBadgeProps {
  status: string;
  colorMap: Record<string, string>;
}
```

### 6.4 Feedback Components

```typescript
// Modal.tsx
interface ModalProps {
  open: boolean;
  onClose: () => void;
  title: string;
  size?: 'sm' | 'md' | 'lg' | 'xl';
  children: React.ReactNode;
  footer?: React.ReactNode;
}

// ConfirmDialog.tsx
interface ConfirmDialogProps {
  open: boolean;
  title: string;
  message: string;
  confirmLabel?: string;
  cancelLabel?: string;
  variant?: 'danger' | 'warning' | 'info';
  onConfirm: () => void;
  onCancel: () => void;
}

// Toast (via Zustand store)
interface Toast {
  id: string;
  type: 'success' | 'error' | 'warning' | 'info';
  title: string;
  message?: string;
  duration?: number;  // default: 5000ms
}
```

---

## 7. API Integration

> **📜 Referensi Utama:** [API Contract](api-contract.md) - Semua request/response format mengikuti contract ini.

### 7.1 TypeScript Types (from API Contract)

```typescript
// src/types/api.ts

/** Standard API Response */
export interface ApiResponse<T> {
  success: boolean;
  data: T;
  meta: {
    timestamp: string;
    requestId: string;
  };
}

/** Paginated Response */
export interface PaginatedResponse<T> extends ApiResponse<T[]> {
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

/** API Error Response */
export interface ApiError {
  success: false;
  error: {
    code: 'VALIDATION_ERROR' | 'UNAUTHORIZED' | 'FORBIDDEN' | 'NOT_FOUND' | 'CONFLICT' | 'BUSINESS_ERROR' | 'SERVER_ERROR';
    message: string;
    details?: Record<string, string[]>;
  };
}

/** Dropdown Option */
export interface Option {
  value: string;
  label: string;
  code?: string;
  color?: string;
}

/** Query Parameters */
export interface QueryParams {
  page?: number;
  limit?: number;
  search?: string;
  sort?: string;
  order?: 'asc' | 'desc';
  status?: string;
  [key: string]: any;
}
```

### 7.2 Domain Types

```typescript
// src/types/auth.ts

export interface User {
  uuid: string;
  username: string;
  email: string;
  role: Role;
  permissions: string[];
}

export interface Role {
  id?: number;
  code: string;
  name: string;
}

export interface LoginRequest {
  username: string;
  password: string;
}

export interface LoginResponse {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
  user: User;
}

// src/types/sales-order.ts

export type SalesOrderStatus = 'draft' | 'pending' | 'approved' | 'in_progress' | 'completed' | 'cancelled';

export interface SalesOrder {
  uuid: string;
  soNumber: string;
  customer: CustomerSummary;
  orderDate: string;
  deadlineDate?: string;
  customerPO?: string;
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
  statusColor: string;
  notes?: string;
  createdBy: UserSummary;
  createdAt: string;
  updatedAt?: string;
  timeline?: StatusTimeline[];
}

export interface SalesOrderItem {
  id: number;
  item: ItemSummary;
  qty: number;
  price: number;
  discountPct: number;
  subtotal: number;
  subtotalFormatted: string;
  deliveredQty: number;
}

export interface CreateSalesOrderRequest {
  customerId: string;
  orderDate: string;
  deadlineDate?: string;
  customerPO?: string;
  discountPct?: number;
  items: CreateSOItemRequest[];
  notes?: string;
}

export interface CreateSOItemRequest {
  itemId: string;
  qty: number;
  price: number;
  discountPct?: number;
}

// src/types/master.ts

export interface Customer {
  uuid: string;
  code: string;
  name: string;
  address?: string;
  phone?: string;
  email?: string;
  segment: 'VIP' | 'Regular';
  segmentLabel: string;
  creditLimit: number;
  creditLimitFormatted: string;
  lastOrderDate?: string;
}

export interface Item {
  uuid: string;
  code: string;
  name: string;
  category: string;
  stdCost: number;
  sellPrice: number;
  sellPriceFormatted: string;
  uom: string;
  minStock: number;
  currentStock?: number;
}

// src/types/ai.ts

export interface ChatRequest {
  message: string;
  sessionId?: string;
}

export interface ChatResponse {
  sessionId: string;
  answer: string;
  answerHtml: string;
  confidence: number;
  confidenceLabel: 'High' | 'Medium' | 'Low';
  sources: ChatSource[];
  charts?: ChartData[];
  tables?: TableData[];
  suggestedActions: SuggestedAction[];
  timestamp: string;
}

export interface ChatSource {
  table: string;
  period: string;
  recordCount?: number;
}

export interface SuggestedAction {
  label: string;
  action: string;
  params?: Record<string, any>;
}
```

### 7.3 API Client Setup

```typescript
// src/services/api.ts

import axios, { AxiosError, AxiosInstance, AxiosRequestConfig } from 'axios';
import { ApiError, ApiResponse } from '@/types/api';

const api: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor - add auth token & request ID
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('accessToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  config.headers['X-Request-ID'] = crypto.randomUUID();
  return config;
});

// Response interceptor - handle errors & token refresh
api.interceptors.response.use(
  (response) => response.data, // Return data directly
  async (error: AxiosError<ApiError>) => {
    const originalRequest = error.config as AxiosRequestConfig & { _retry?: boolean };
    
    // Handle 401 - try refresh token
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      try {
        const refreshToken = localStorage.getItem('refreshToken');
        const { data } = await axios.post(`${import.meta.env.VITE_API_URL}/auth/refresh`, {
          refreshToken
        });
        localStorage.setItem('accessToken', data.data.accessToken);
        return api(originalRequest);
      } catch {
        // Refresh failed - logout
        localStorage.removeItem('accessToken');
        localStorage.removeItem('refreshToken');
        window.location.href = '/auth/login';
      }
    }
    
    // Return error with proper structure
    return Promise.reject(error.response?.data || {
      success: false,
      error: { code: 'SERVER_ERROR', message: 'Network error' }
    });
  }
);

export default api;
```

### 7.4 API Services

```typescript
// src/services/auth.service.ts

import api from './api';
import { LoginRequest, LoginResponse, User } from '@/types/auth';
import { ApiResponse } from '@/types/api';

export const authService = {
  login: (data: LoginRequest) => 
    api.post<any, ApiResponse<LoginResponse>>('/auth/login', data),
  
  logout: () => 
    api.post<any, ApiResponse<{ message: string }>>('/auth/logout'),
  
  refresh: (refreshToken: string) => 
    api.post<any, ApiResponse<{ accessToken: string }>>('/auth/refresh', { refreshToken }),
  
  getMe: () => 
    api.get<any, ApiResponse<User>>('/auth/me'),
};

// src/services/dashboard.service.ts

import api from './api';

export const dashboardService = {
  getSummary: () => 
    api.get('/dashboard/summary'),
  
  getRevenueTrend: (period: string = '30d') => 
    api.get(`/dashboard/revenue-trend?period=${period}`),
  
  getTopCustomers: (limit: number = 5) => 
    api.get(`/dashboard/top-customers?limit=${limit}`),
};

// src/services/sales-order.service.ts

import api from './api';
import { CreateSalesOrderRequest, SalesOrder } from '@/types/sales-order';
import { ApiResponse, PaginatedResponse, QueryParams } from '@/types/api';

export const salesOrderService = {
  getAll: (params: QueryParams) => 
    api.get<any, PaginatedResponse<SalesOrder>>('/sales-orders', { params }),
  
  getByUuid: (uuid: string) => 
    api.get<any, ApiResponse<SalesOrder>>(`/sales-orders/${uuid}`),
  
  create: (data: CreateSalesOrderRequest) => 
    api.post<any, ApiResponse<SalesOrder>>('/sales-orders', data),
  
  update: (uuid: string, data: Partial<CreateSalesOrderRequest>) => 
    api.put<any, ApiResponse<SalesOrder>>(`/sales-orders/${uuid}`, data),
  
  submit: (uuid: string) => 
    api.post<any, ApiResponse<SalesOrder>>(`/sales-orders/${uuid}/submit`),
  
  approve: (uuid: string, notes?: string) => 
    api.post<any, ApiResponse<SalesOrder>>(`/sales-orders/${uuid}/approve`, { notes }),
  
  reject: (uuid: string, reason: string) => 
    api.post<any, ApiResponse<SalesOrder>>(`/sales-orders/${uuid}/reject`, { reason }),
  
  delete: (uuid: string) => 
    api.delete<any, ApiResponse<{ message: string }>>(`/sales-orders/${uuid}`),
};

// src/services/ai.service.ts

import api from './api';
import { ChatRequest, ChatResponse } from '@/types/ai';
import { ApiResponse } from '@/types/api';

export const aiService = {
  chat: (data: ChatRequest) => 
    api.post<any, ApiResponse<ChatResponse>>('/ai/chat', data),
  
  getChatHistory: (limit: number = 20) => 
    api.get(`/ai/chat/history?limit=${limit}`),
  
  getChatSession: (sessionId: string) => 
    api.get(`/ai/chat/${sessionId}`),
};

// src/services/options.service.ts

import api from './api';
import { Option } from '@/types/api';

export const optionsService = {
  getCustomers: (search?: string) => 
    api.get<any, { data: Option[] }>('/options/customers', { params: { search } }),
  
  getItems: (search?: string) => 
    api.get<any, { data: Option[] }>('/options/items', { params: { search } }),
  
  getRoles: () => 
    api.get<any, { data: Option[] }>('/options/roles'),
  
  getStatuses: (entity: string) => 
    api.get<any, { data: Option[] }>('/options/statuses', { params: { entity } }),
};
```

### 7.5 React Query Hooks

```typescript
// src/hooks/useAuth.ts

import { useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import { authService } from '@/services/auth.service';
import { useAuthStore } from '@/stores/authStore';
import { LoginRequest } from '@/types/auth';

export const useLogin = () => {
  const queryClient = useQueryClient();
  const setAuth = useAuthStore((state) => state.setAuth);
  
  return useMutation({
    mutationFn: (data: LoginRequest) => authService.login(data),
    onSuccess: (response) => {
      localStorage.setItem('accessToken', response.data.accessToken);
      localStorage.setItem('refreshToken', response.data.refreshToken);
      setAuth(response.data.user, response.data.accessToken);
      queryClient.invalidateQueries({ queryKey: ['me'] });
    },
  });
};

export const useMe = () => {
  return useQuery({
    queryKey: ['me'],
    queryFn: () => authService.getMe(),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
};

// src/hooks/useDashboard.ts

import { useQuery } from '@tanstack/react-query';
import { dashboardService } from '@/services/dashboard.service';

export const useDashboardSummary = () => {
  return useQuery({
    queryKey: ['dashboard', 'summary'],
    queryFn: () => dashboardService.getSummary(),
    refetchInterval: 60 * 1000, // Refresh every minute
  });
};

export const useRevenueTrend = (period: string = '30d') => {
  return useQuery({
    queryKey: ['dashboard', 'revenue-trend', period],
    queryFn: () => dashboardService.getRevenueTrend(period),
  });
};

// src/hooks/useSalesOrders.ts

import { useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import { salesOrderService } from '@/services/sales-order.service';
import { CreateSalesOrderRequest } from '@/types/sales-order';
import { QueryParams } from '@/types/api';
import { useToast } from '@/hooks/useToast';

export const useSalesOrders = (params: QueryParams) => {
  return useQuery({
    queryKey: ['sales-orders', params],
    queryFn: () => salesOrderService.getAll(params),
  });
};

export const useSalesOrder = (uuid: string) => {
  return useQuery({
    queryKey: ['sales-orders', uuid],
    queryFn: () => salesOrderService.getByUuid(uuid),
    enabled: !!uuid,
  });
};

export const useCreateSalesOrder = () => {
  const queryClient = useQueryClient();
  const { toast } = useToast();
  
  return useMutation({
    mutationFn: (data: CreateSalesOrderRequest) => salesOrderService.create(data),
    onSuccess: (response) => {
      queryClient.invalidateQueries({ queryKey: ['sales-orders'] });
      toast.success(`Sales Order ${response.data.soNumber} created!`);
    },
    onError: (error: any) => {
      toast.error(error.error?.message || 'Failed to create');
    },
  });
};

export const useApproveSalesOrder = () => {
  const queryClient = useQueryClient();
  const { toast } = useToast();
  
  return useMutation({
    mutationFn: ({ uuid, notes }: { uuid: string; notes?: string }) => 
      salesOrderService.approve(uuid, notes),
    onSuccess: (response) => {
      queryClient.invalidateQueries({ queryKey: ['sales-orders'] });
      toast.success(`${response.data.soNumber} approved!`);
    },
  });
};

// src/hooks/useAiChat.ts

import { useMutation, useQuery } from '@tanstack/react-query';
import { aiService } from '@/services/ai.service';
import { ChatRequest } from '@/types/ai';

export const useAiChat = () => {
  return useMutation({
    mutationFn: (data: ChatRequest) => aiService.chat(data),
  });
};

export const useChatHistory = () => {
  return useQuery({
    queryKey: ['ai', 'chat-history'],
    queryFn: () => aiService.getChatHistory(),
  });
};

// src/hooks/useOptions.ts

import { useQuery } from '@tanstack/react-query';
import { optionsService } from '@/services/options.service';

export const useCustomerOptions = (search?: string) => {
  return useQuery({
    queryKey: ['options', 'customers', search],
    queryFn: () => optionsService.getCustomers(search),
    staleTime: 5 * 60 * 1000,
  });
};

export const useItemOptions = (search?: string) => {
  return useQuery({
    queryKey: ['options', 'items', search],
    queryFn: () => optionsService.getItems(search),
    staleTime: 5 * 60 * 1000,
  });
};

export const useStatusOptions = (entity: string) => {
  return useQuery({
    queryKey: ['options', 'statuses', entity],
    queryFn: () => optionsService.getStatuses(entity),
    staleTime: 30 * 60 * 1000, // 30 minutes
  });
};
```

### 7.6 Usage Examples

```tsx
// pages/SalesOrderListPage.tsx

import { useSalesOrders, useApproveSalesOrder } from '@/hooks/useSalesOrders';
import { DataTable } from '@/components/table/DataTable';
import { StatusBadge } from '@/components/StatusBadge';
import { useState } from 'react';

export const SalesOrderListPage = () => {
  const [params, setParams] = useState({ page: 1, limit: 20 });
  const { data, isLoading, error } = useSalesOrders(params);
  const approveMutation = useApproveSalesOrder();

  const columns = [
    { key: 'soNumber', label: 'SO Number' },
    { key: 'customer.name', label: 'Customer' },
    { key: 'orderDate', label: 'Date' },
    { key: 'totalFormatted', label: 'Total', align: 'right' },
    { 
      key: 'status', 
      label: 'Status',
      render: (row) => <StatusBadge status={row.status} label={row.statusLabel} />
    },
    {
      key: 'actions',
      label: '',
      render: (row) => (
        <>
          <Link to={`/sales/orders/${row.uuid}`}>View</Link>
          {row.status === 'pending' && (
            <button onClick={() => approveMutation.mutate({ uuid: row.uuid })}>
              Approve
            </button>
          )}
        </>
      )
    }
  ];

  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;

  return (
    <DataTable
      columns={columns}
      data={data?.data || []}
      pagination={data?.pagination}
      onPageChange={(page) => setParams({ ...params, page })}
    />
  );
};

// pages/AiChatPage.tsx

import { useAiChat } from '@/hooks/useAiChat';
import { useState } from 'react';

export const AiChatPage = () => {
  const [message, setMessage] = useState('');
  const [sessionId, setSessionId] = useState<string>();
  const chatMutation = useAiChat();

  const handleSend = () => {
    chatMutation.mutate(
      { message, sessionId },
      {
        onSuccess: (response) => {
          setSessionId(response.data.sessionId);
          // Add to chat history...
        }
      }
    );
    setMessage('');
  };

  return (
    <div className="chat-container">
      {/* Chat messages */}
      {chatMutation.data && (
        <div className="ai-response">
          <div dangerouslySetInnerHTML={{ __html: chatMutation.data.data.answerHtml }} />
          {chatMutation.data.data.charts?.map((chart, i) => (
            <ChartRenderer key={i} chart={chart} />
          ))}
        </div>
      )}
      
      {/* Input */}
      <div className="chat-input">
        <input
          value={message}
          onChange={(e) => setMessage(e.target.value)}
          placeholder="Tanya AI..."
          onKeyPress={(e) => e.key === 'Enter' && handleSend()}
        />
        <button onClick={handleSend} disabled={chatMutation.isPending}>
          {chatMutation.isPending ? 'Thinking...' : 'Send'}
        </button>
      </div>
    </div>
  );
};
```

### 7.7 Error Handling

```typescript
// src/utils/errorHandler.ts

import { ApiError } from '@/types/api';

export const getErrorMessage = (error: ApiError): string => {
  if (error.error?.details) {
    // Validation errors - get first message
    const firstField = Object.keys(error.error.details)[0];
    return error.error.details[firstField][0];
  }
  return error.error?.message || 'An error occurred';
};

export const isValidationError = (error: ApiError): boolean => {
  return error.error?.code === 'VALIDATION_ERROR';
};

export const getFieldErrors = (error: ApiError): Record<string, string> => {
  if (!error.error?.details) return {};
  
  return Object.entries(error.error.details).reduce((acc, [field, messages]) => {
    acc[field] = messages[0];
    return acc;
  }, {} as Record<string, string>);
};
```


---

## 8. State Management

### 8.1 Store Structure

```typescript
// src/stores/index.ts

// Auth Store
interface AuthStore {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (credentials: LoginCredentials) => Promise<void>;
  logout: () => void;
  hasPermission: (permission: string) => boolean;
}

// UI Store
interface UIStore {
  sidebarCollapsed: boolean;
  theme: 'light' | 'dark';
  toggleSidebar: () => void;
  setTheme: (theme: 'light' | 'dark') => void;
}

// Toast Store
interface ToastStore {
  toasts: Toast[];
  addToast: (toast: Omit<Toast, 'id'>) => void;
  removeToast: (id: string) => void;
}

// Alert Store
interface AlertStore {
  unreadCount: number;
  fetchUnreadCount: () => Promise<void>;
}
```

### 8.2 Zustand Implementation

```typescript
// src/stores/authStore.ts

import { create } from 'zustand';
import { persist } from 'zustand/middleware';

export const useAuthStore = create<AuthStore>()(
  persist(
    (set, get) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      
      login: async (credentials) => {
        const response = await authService.login(credentials);
        set({
          user: response.user,
          token: response.token,
          isAuthenticated: true,
        });
      },
      
      logout: () => {
        set({ user: null, token: null, isAuthenticated: false });
        localStorage.removeItem('accessToken');
      },
      
      hasPermission: (permission) => {
        const { user } = get();
        return user?.permissions?.includes(permission) ?? false;
      },
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

---

## 9. Form Validation Summary

### 9.1 Validation Schemas

| Entity           | Schema File         | Key Rules                                           |
| ---------------- | ------------------- | --------------------------------------------------- |
| Login            | loginSchema         | username required, password required                |
| User             | userSchema          | username 3-50 chars, valid email, password 8+ chars |
| Customer         | customerSchema      | code unique, name required, valid phone             |
| Item             | itemSchema          | code unique, category required, price >= cost       |
| Sales Order      | salesOrderSchema    | customer required, min 1 item, qty > 0              |
| Purchase Order   | purchaseOrderSchema | vendor required, delivery date >= today             |
| Work Order       | workOrderSchema     | item required, target qty > 0, machine assigned     |
| Stock Adjustment | adjustmentSchema    | qty != 0, reason required                           |

### 9.2 Error Display Pattern

```typescript
// Form field with error
<TextInput
  name="username"
  label="Username"
  error={errors.username?.message}
  {...register('username')}
/>

// Toast for API errors
useEffect(() => {
  if (error) {
    toast.error({
      title: 'Error',
      message: error.message,
    });
  }
}, [error]);
```

---

## 10. Data References

For dummy data and sample values:
- **[System Analysis Section 1.8](system_analysis.md#_18-sample-data-dummy-data-untuk-uiux-team)**

For UI layouts and wireframes:
- **[UI Design Specification](ui-design.md)**

---

## 11. UI Kit & Component Standards

Berikut adalah daftar standar komponen UI yang disusun berdasarkan kebutuhan modul Sentient Factory. Komponen ini dibangun menggunakan **Metronic v9.3.7** sebagai basis dan **Apache ECharts** untuk visualisasi data.

### 11.1 Global Theme & Layout (Metronic Basis)

> Referensi: [UI Design](ui-design.md), [Frontend Spec Section 1.5](#15-layout-choice-layout-4-dark-sidebar-compact)

*   **Layout Setup:** Menggunakan **Layout 4 (Dark Sidebar Compact)**.
    *   Sidebar: Icon-only (collapsed), expand saat hover.
    *   Header: Light background, fixed top.
*   **Color Palette (Sentient Factory Theme):**
    *   `Primary`: `#1E40AF` (Blue 800 - Industrial Trust)
    *   `Success`: `#10B981` (Green - Approved/Safe)
    *   `Warning`: `#F59E0B` (Amber - Pending/Alert)
    *   `Danger`: `#EF4444` (Red - Reject/Critical)
    *   `Info`: `#06B6D4` (Cyan - AI Features)
*   **Typography:** Inter font family (Default Metronic).
*   **Icons:** Lucide React.

### 11.2 Data Visualization (ECharts Integration)

> Pengganti ApexCharts bawaan Metronic untuk use-case yang membutuhkan performa tinggi atau variasi khusus.

**A. Executive & Sales Charts**
*   [ ] **Financial Trend:** *Smoothed Line Chart* atau *Gradient Stacked Area* (Revenue Trend 30 hari).
*   [ ] **Target Achievement:** *Gauge Ring* atau *Liquid Fill* (% Pencapaian Target Sales).
*   [ ] **Customer Matrix:** *Bubble Chart* (X: Frekuensi, Y: Nominal, Size: Recency) untuk analisis RFM.

**B. Production & Ops Charts**
*   [ ] **OEE Monitor:** *Multi-Title Gauge* (Availability, Performance, Quality).
*   [ ] **Machine Schedule:** *Custom Profile / Gantt Chart* (Timeline: Running/Down/Idle).
*   [ ] **Output Quality:** *Mixed Line & Bar* (Bar: Total Output, Line: Reject Rate %).

**C. Inventory & Procurement Charts**
*   [ ] **Warehouse Density:** *Heatmap (Cartesian)* (Visualisasi kepadatan rak gudang).
*   [ ] **Stock Composition:** *Nightingale / Rose Chart* (Available vs Reserved vs Damaged).
*   [ ] **Vendor Scoring:** *Radar Chart* (Harga, Waktu, Kualitas).

### 11.3 Complex Business Components

**A. Advanced Data Table (TanStack Table)**
*   Fitur wajib: Sorting, Filtering, Pagination, Row Selection.
*   Sticky Header untuk tabel panjang.

**B. Status Badges**
*   Variasi warna sesuai status (Draft=Gray, Approved=Green, Rejected=Red, dll).
*   Lihat [Section 1.7 - Status Badge Configuration](#17-metronic-component-library-ktui).

**C. Transaction Form**
*   Struktur: Header (Info Utama) + Detail (Tabel Item) + Footer (Total/Tax).
*   Komponen *Editable Table Row* untuk input item pesanan.

**D. Form Inputs**
*   **Searchable Select**: Autocomplete untuk Customer/Item.
*   **Date Range Picker**: Untuk filter laporan.
*   **Currency Input**: Format IDR otomatis.

### 11.4 AI & Chat Interface Components

> Referensi: [BPA-3 (AI Assistant)](bpa-3.md)

*   [ ] **Chat Bubble Container**:
    *   User Bubble (Right aligned).
    *   AI Bubble (Left aligned, ikon robot).
*   [ ] **AI Loading State**:
    *   Animasi "Thinking" (dots) atau "Analyzing Data" (progress bar).
*   [ ] **Response Elements**:
    *   *Confidence Indicator* (Badge: High/Medium/Low).
    *   *Source Citation Widget* (Tampilan kecil: "Sumber: t_so, data kemarin").
    *   *Markdown Renderer* (untuk tabel/list).
*   [ ] **Insight Cards**:
    *   Card notifikasi anomali (Border merah/kuning) dengan tombol aksi cepat.

### 11.5 Utility & Feedback

*   [ ] **Toast Notification (Sonner)**: Feedback sukses/gagal.
*   [ ] **Modal/Dialog**: Konfirmasi Delete, Approval Note.
*   [ ] **Empty States**: Visual saat data kosong.
*   [ ] **Loading Spinner**: Overlay penuh atau tombol loading.

---

*Document Version: 1.1*
*Last Updated: 2026-01-10*
