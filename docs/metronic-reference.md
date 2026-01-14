# 📦 Metronic v9.3.7 Local Reference

> **Location:** `/metronic-v9.3.7/`
> **Last Updated:** 2024-12-27

---

## 1. Package Overview

Metronic v9.3.7 menggunakan **Tailwind CSS** (bukan Bootstrap).

```
metronic-v9.3.7/
├── metronic-tailwind-html-demos/          # HTML version
├── metronic-tailwind-html-starter-kit/    # HTML starter
├── metronic-tailwind-react-demos/         # ⭐ RECOMMENDED
├── metronic-tailwind-react-starter-kit/   # React starter
├── metronic-tailwind-react-concepts/      # App concepts
├── metronic-tailwind-nextjs-landings/     # Landing pages
└── figma/                                 # Figma designs
```

---

## 2. Recommended Package: React + Vite + TypeScript

**Path:** `metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/`

### 2.1 Quick Start

```bash
# Navigate to Vite version
cd metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite

# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build
```

### 2.2 Tech Stack (from package.json)

| Package              | Version  | Purpose         |
| -------------------- | -------- | --------------- |
| React                | 19.1.1   | UI Library      |
| TypeScript           | 5.9.2    | Type Safety     |
| Vite                 | 7.1.3    | Build Tool      |
| Tailwind CSS         | 4.1.12   | Styling         |
| React Router         | 7.8.2    | Routing         |
| Tanstack React Query | 5.85.5   | Data Fetching   |
| Tanstack React Table | 8.21.3   | Data Tables     |
| React Hook Form      | 7.62.0   | Form Management |
| Zod                  | 3.25.67  | Validation      |
| ApexCharts           | 4.7.0    | Charts          |
| Radix UI             | 1.4.3    | Primitives      |
| Lucide React         | 0.541.0  | Icons           |
| Motion               | 12.23.12 | Animations      |
| Sonner               | 2.0.7    | Toasts          |
| Supabase             | 2.56.0   | Auth (optional) |

---

## 3. Folder Structure

```bash
metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/
├── src/
│   ├── App.tsx                 # Main app
│   ├── main.tsx                # Entry point
│   ├── auth/                   # Auth pages & logic
│   ├── components/             # UI Components
│   │   ├── common/             # Common utilities
│   │   ├── keenicons/          # Keen icons
│   │   ├── supabase/           # Supabase auth
│   │   └── ui/                 # ⭐ 78 UI Components
│   ├── config/                 # App configuration
│   ├── css/                    # Stylesheets
│   ├── hooks/                  # Custom hooks
│   ├── i18n/                   # Internationalization
│   ├── layouts/                # ⭐ 10 Demo Layouts
│   │   ├── demo1/              # Classic sidebar
│   │   ├── demo2/              # Horizontal menu
│   │   ├── demo3/              # Minimal
│   │   ├── demo4/              # ⭐ Dark sidebar compact
│   │   ├── demo5/              # Wide sidebar
│   │   ├── demo6/              # Topbar focus
│   │   ├── demo7/              # Dual sidebar
│   │   ├── demo8/              # Light sidebar
│   │   ├── demo9/              # Compact
│   │   └── demo10/             # Modern
│   ├── lib/                    # Library utilities
│   ├── pages/                  # ⭐ 564 Page files
│   ├── partials/               # Reusable partials
│   ├── providers/              # Context providers
│   └── routing/                # Route definitions
├── public/                     # Static assets
├── package.json
├── vite.config.ts
└── tsconfig.json
```

---

## 4. Layout Demo 4 (Recommended for Sentient Factory)

**Path:** `src/layouts/demo4/`

### 4.1 Files

```
src/layouts/demo4/
├── layout.tsx                  # Main layout wrapper
└── components/
    ├── header.tsx              # Top header
    ├── sidebar.tsx             # Dark sidebar
    ├── footer.tsx              # Footer
    └── toolbar.tsx             # Page toolbar
```

### 4.2 Layout Features

| Feature       | Value                          |
| ------------- | ------------------------------ |
| Sidebar Width | 290px (collapsed: icon-only)   |
| Header Height | 60px                           |
| Background    | Muted (bg-muted)               |
| Content Area  | Rounded card with border       |
| Responsive    | Mobile header, desktop sidebar |

### 4.3 Usage

```tsx
// From: src/layouts/demo4/layout.tsx

import { Demo4Layout } from '@/layouts/demo4/layout';

// In routing
<Route element={<Demo4Layout />}>
  <Route path="dashboard" element={<DashboardPage />} />
</Route>
```

---

## 5. UI Components (78 Components)

**Path:** `src/components/ui/`

### 5.1 Form Components

| Component   | File              | Description             |
| ----------- | ----------------- | ----------------------- |
| Button      | `button.tsx`      | 13KB, multiple variants |
| Input       | `input.tsx`       | 5.8KB, text input       |
| Textarea    | `textarea.tsx`    | Text area               |
| Select      | `select.tsx`      | 8KB, dropdown           |
| Checkbox    | `checkbox.tsx`    | Checkbox input          |
| Radio Group | `radio-group.tsx` | Radio buttons           |
| Switch      | `switch.tsx`      | Toggle switch           |
| Datefield   | `datefield.tsx`   | Date picker             |
| Form        | `form.tsx`        | Form wrapper            |

### 5.2 Data Display

| Component       | File                  | Description           |
| --------------- | --------------------- | --------------------- |
| Data Grid       | `data-grid.tsx`       | Table with pagination |
| Data Grid Table | `data-grid-table.tsx` | 16KB, advanced table  |
| Table           | `table.tsx`           | Basic table           |
| Badge           | `badge.tsx`           | 9KB, status badges    |
| Avatar          | `avatar.tsx`          | User avatars          |
| Card            | `card.tsx`            | Card container        |
| Progress        | `progress.tsx`        | Progress bar          |

### 5.3 Navigation

| Component       | File                  | Description         |
| --------------- | --------------------- | ------------------- |
| Accordion Menu  | `accordion-menu.tsx`  | 13KB, sidebar menu  |
| Tabs            | `tabs.tsx`            | 6KB, tab navigation |
| Breadcrumb      | `breadcrumb.tsx`      | Breadcrumb trail    |
| Navigation Menu | `navigation-menu.tsx` | Nav menu            |
| Stepper         | `stepper.tsx`         | 11KB, wizard steps  |

### 5.4 Feedback

| Component    | File               | Description         |
| ------------ | ------------------ | ------------------- |
| Alert        | `alert.tsx`        | 9KB, alert messages |
| Alert Dialog | `alert-dialog.tsx` | Confirm dialogs     |
| Dialog       | `dialog.tsx`       | Modal dialogs       |
| Sheet        | `sheet.tsx`        | Slide-over panels   |
| Drawer       | `drawer.tsx`       | Drawer panels       |
| Sonner       | `sonner.tsx`       | Toast notifications |
| Tooltip      | `tooltip.tsx`      | Tooltips            |

### 5.5 Advanced

| Component | File           | Description        |
| --------- | -------------- | ------------------ |
| Kanban    | `kanban.tsx`   | 14KB, kanban board |
| Sortable  | `sortable.tsx` | 19KB, drag & drop  |
| Chart     | `chart.tsx`    | 9KB, chart wrapper |
| Tree      | `tree.tsx`     | Tree view          |
| Calendar  | `calendar.tsx` | Calendar picker    |
| Command   | `command.tsx`  | Command palette    |

---

## 6. Pages Directory

**Path:** `src/pages/` (564 files)

### Key Page Folders

| Folder             | Contents              |
| ------------------ | --------------------- |
| `account/`         | User account pages    |
| `authentication/`  | Login, register, etc. |
| `dashboards/`      | Dashboard variants    |
| `public-profile/`  | Profile pages         |
| `network/`         | Network/social pages  |
| `store-client/`    | E-commerce pages      |
| `user-management/` | User CRUD pages       |

---

## 7. Hooks

**Path:** `src/hooks/`

| Hook                | Purpose                 |
| ------------------- | ----------------------- |
| `use-body-class.ts` | Add CSS classes to body |
| `use-menu.ts`       | Menu navigation         |
| `use-mobile.ts`     | Mobile detection        |
| `use-scroll-spy.ts` | Scroll spy navigation   |

---

## 8. Configuration

**Path:** `src/config/`

| File             | Purpose            |
| ---------------- | ------------------ |
| `menu.config.ts` | Sidebar menu items |
| (others)         | App settings       |

---

## 9. How to Use for Sentient Factory

### 9.1 Copy Demo 4 Layout

```bash
# Copy layout files
cp -r metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/src/layouts/demo4 \
      frontend/src/layouts/

# Copy UI components
cp -r metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/src/components/ui \
      frontend/src/components/
```

### 9.2 Copy Required Files

```bash
# Hooks
cp -r metronic-v9.3.7/.../src/hooks frontend/src/

# CSS
cp -r metronic-v9.3.7/.../src/css frontend/src/

# Providers
cp -r metronic-v9.3.7/.../src/providers frontend/src/

# Config
cp metronic-v9.3.7/.../src/config/menu.config.ts frontend/src/config/
```

### 9.3 Update Dependencies

Ensure `package.json` has these dependencies:

```json
{
  "dependencies": {
    "react": "^19.1.1",
    "react-dom": "^19.1.1",
    "react-router-dom": "^7.8.2",
    "@tanstack/react-query": "^5.85.5",
    "@tanstack/react-table": "^8.21.3",
    "react-hook-form": "^7.62.0",
    "@hookform/resolvers": "^5.2.1",
    "zod": "^3.25.67",
    "tailwindcss": "^4.1.12",
    "apexcharts": "^4.7.0",
    "react-apexcharts": "^1.7.0",
    "lucide-react": "^0.541.0",
    "sonner": "^2.0.7",
    "radix-ui": "^1.4.3",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.3.1",
    "class-variance-authority": "^0.7.1"
  }
}
```

---

## 10. Quick Reference Paths

| Resource           | Local Path                                                       |
| ------------------ | ---------------------------------------------------------------- |
| **React + Vite**   | `metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite/` |
| **Layout Demo 4**  | `.../src/layouts/demo4/`                                         |
| **UI Components**  | `.../src/components/ui/`                                         |
| **Pages Examples** | `.../src/pages/`                                                 |
| **Hooks**          | `.../src/hooks/`                                                 |
| **Menu Config**    | `.../src/config/menu.config.ts`                                  |
| **CSS Styles**     | `.../src/css/`                                                   |
| **Figma Designs**  | `metronic-v9.3.7/figma/`                                         |
| **HTML Demos**     | `metronic-v9.3.7/metronic-tailwind-html-demos/`                  |

---

## 11. Run Demo Locally

```bash
# Navigate
cd metronic-v9.3.7/metronic-tailwind-react-demos/typescript/vite

# Install
npm install

# Run (default port 5173)
npm run dev

# Open browser
open http://localhost:5173
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
