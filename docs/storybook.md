# 📚 Storybook Component Library

> **Purpose:** Visual documentation dan testing untuk UI components
> **Framework:** React + Storybook 7.x + Metronic
> **Last Updated:** 2024-12-27

---

## 1. Setup

### 1.1 Installation

```bash
# Initialize Storybook
npx storybook@latest init

# Install addons
npm install -D @storybook/addon-essentials
npm install -D @storybook/addon-a11y
npm install -D @storybook/addon-interactions
npm install -D @storybook/testing-library
npm install -D @storybook/addon-designs

# Start Storybook
npm run storybook
```

### 1.2 Configuration

```typescript
// .storybook/main.ts

import type { StorybookConfig } from '@storybook/react-vite';

const config: StorybookConfig = {
  stories: ['../src/**/*.stories.@(js|jsx|ts|tsx|mdx)'],
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-a11y',
    '@storybook/addon-interactions',
    '@storybook/addon-designs',
  ],
  framework: {
    name: '@storybook/react-vite',
    options: {},
  },
  docs: {
    autodocs: 'tag',
  },
};

export default config;
```

```typescript
// .storybook/preview.tsx

import type { Preview } from '@storybook/react';
import '../src/index.css';  // Metronic styles

const preview: Preview = {
  parameters: {
    actions: { argTypesRegex: '^on[A-Z].*' },
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/,
      },
    },
    backgrounds: {
      default: 'light',
      values: [
        { name: 'light', value: '#ffffff' },
        { name: 'dark', value: '#1e1e2d' },  // Metronic dark
      ],
    },
  },
};

export default preview;
```

---

## 2. Component Stories

### 2.1 Story File Location

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.stories.tsx
│   ├── StatusBadge/
│   │   ├── StatusBadge.tsx
│   │   └── StatusBadge.stories.tsx
│   └── DataTable/
│       ├── DataTable.tsx
│       └── DataTable.stories.tsx
```

### 2.2 Button Stories

```typescript
// src/components/Button/Button.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'success', 'danger', 'warning'],
    },
    size: {
      control: 'select',
      options: ['sm', 'md', 'lg'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Primary Button',
  },
};

export const Secondary: Story = {
  args: {
    variant: 'secondary',
    children: 'Secondary Button',
  },
};

export const Success: Story = {
  args: {
    variant: 'success',
    children: 'Approve',
  },
};

export const Danger: Story = {
  args: {
    variant: 'danger',
    children: 'Delete',
  },
};

export const Loading: Story = {
  args: {
    variant: 'primary',
    children: 'Saving...',
    isLoading: true,
  },
};

export const Disabled: Story = {
  args: {
    variant: 'primary',
    children: 'Disabled',
    disabled: true,
  },
};
```

### 2.3 StatusBadge Stories

```typescript
// src/components/StatusBadge/StatusBadge.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { StatusBadge } from './StatusBadge';

const meta: Meta<typeof StatusBadge> = {
  title: 'Components/StatusBadge',
  component: StatusBadge,
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof StatusBadge>;

export const Draft: Story = {
  args: {
    status: 'draft',
    label: 'Draft',
  },
};

export const Pending: Story = {
  args: {
    status: 'pending',
    label: 'Pending Approval',
  },
};

export const Approved: Story = {
  args: {
    status: 'approved',
    label: 'Approved',
  },
};

export const Cancelled: Story = {
  args: {
    status: 'cancelled',
    label: 'Cancelled',
  },
};

export const AllStatuses: Story = {
  render: () => (
    <div className="flex gap-2">
      <StatusBadge status="draft" label="Draft" />
      <StatusBadge status="pending" label="Pending" />
      <StatusBadge status="approved" label="Approved" />
      <StatusBadge status="in_progress" label="In Progress" />
      <StatusBadge status="completed" label="Completed" />
      <StatusBadge status="cancelled" label="Cancelled" />
    </div>
  ),
};
```

### 2.4 DataTable Stories

```typescript
// src/components/DataTable/DataTable.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { DataTable } from './DataTable';

const meta: Meta<typeof DataTable> = {
  title: 'Components/DataTable',
  component: DataTable,
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof DataTable>;

const sampleData = [
  { uuid: '1', soNumber: 'SO-202412-0001', customer: 'PT ABC', total: 6500000, status: 'approved' },
  { uuid: '2', soNumber: 'SO-202412-0002', customer: 'CV XYZ', total: 3200000, status: 'pending' },
  { uuid: '3', soNumber: 'SO-202412-0003', customer: 'PT DEF', total: 8900000, status: 'draft' },
];

const columns = [
  { key: 'soNumber', label: 'SO Number' },
  { key: 'customer', label: 'Customer' },
  { key: 'total', label: 'Total', align: 'right' as const },
  { key: 'status', label: 'Status' },
];

export const Default: Story = {
  args: {
    columns,
    data: sampleData,
  },
};

export const Empty: Story = {
  args: {
    columns,
    data: [],
  },
};

export const Loading: Story = {
  args: {
    columns,
    data: [],
    isLoading: true,
  },
};

export const WithPagination: Story = {
  args: {
    columns,
    data: sampleData,
    pagination: {
      page: 1,
      limit: 20,
      total: 150,
      totalPages: 8,
    },
  },
};
```

### 2.5 Form Components

```typescript
// src/components/Form/TextInput.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { TextInput } from './TextInput';

const meta: Meta<typeof TextInput> = {
  title: 'Forms/TextInput',
  component: TextInput,
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof TextInput>;

export const Default: Story = {
  args: {
    name: 'username',
    label: 'Username',
    placeholder: 'Enter username',
  },
};

export const Required: Story = {
  args: {
    name: 'email',
    label: 'Email',
    required: true,
  },
};

export const WithError: Story = {
  args: {
    name: 'email',
    label: 'Email',
    error: 'Invalid email format',
  },
};

export const Disabled: Story = {
  args: {
    name: 'email',
    label: 'Email',
    disabled: true,
    value: 'readonly@example.com',
  },
};
```

```typescript
// src/components/Form/SelectInput.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { SelectInput } from './SelectInput';

const meta: Meta<typeof SelectInput> = {
  title: 'Forms/SelectInput',
  component: SelectInput,
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof SelectInput>;

const customerOptions = [
  { value: '1', label: 'PT ABC Manufacturing' },
  { value: '2', label: 'CV XYZ Trading' },
  { value: '3', label: 'PT DEF Industries' },
];

export const Default: Story = {
  args: {
    name: 'customerId',
    label: 'Customer',
    options: customerOptions,
  },
};

export const Searchable: Story = {
  args: {
    name: 'customerId',
    label: 'Customer',
    options: customerOptions,
    searchable: true,
  },
};

export const WithError: Story = {
  args: {
    name: 'customerId',
    label: 'Customer',
    options: customerOptions,
    error: 'Customer is required',
  },
};
```

---

## 3. Page Stories

### 3.1 Login Page

```typescript
// src/pages/auth/LoginPage.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { LoginPage } from './LoginPage';
import { within, userEvent } from '@storybook/testing-library';
import { expect } from '@storybook/jest';

const meta: Meta<typeof LoginPage> = {
  title: 'Pages/Auth/LoginPage',
  component: LoginPage,
  tags: ['autodocs'],
  parameters: {
    layout: 'fullscreen',
  },
};

export default meta;
type Story = StoryObj<typeof LoginPage>;

export const Default: Story = {};

export const WithCredentials: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    
    await userEvent.type(canvas.getByLabelText('Username'), 'admin');
    await userEvent.type(canvas.getByLabelText('Password'), 'Password123!');
    
    expect(canvas.getByDisplayValue('admin')).toBeInTheDocument();
  },
};

export const ValidationErrors: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    
    await userEvent.click(canvas.getByRole('button', { name: /login/i }));
    
    expect(canvas.getByText('Username required')).toBeInTheDocument();
  },
};
```

### 3.2 Sales Order Form

```typescript
// src/pages/sales/SalesOrderForm.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';
import { SalesOrderForm } from './SalesOrderForm';

const meta: Meta<typeof SalesOrderForm> = {
  title: 'Pages/Sales/SalesOrderForm',
  component: SalesOrderForm,
  tags: ['autodocs'],
  parameters: {
    layout: 'padded',
    design: {
      type: 'figma',
      url: 'https://www.figma.com/file/xxx/SalesOrderForm',
    },
  },
};

export default meta;
type Story = StoryObj<typeof SalesOrderForm>;

export const Create: Story = {
  args: {
    mode: 'create',
  },
};

export const Edit: Story = {
  args: {
    mode: 'edit',
    initialData: {
      uuid: '123',
      soNumber: 'SO-202412-0001',
      customerId: '1',
      orderDate: '2024-12-27',
      items: [
        { itemId: '1', qty: 100, price: 65000 },
      ],
    },
  },
};

export const ReadOnly: Story = {
  args: {
    mode: 'view',
    initialData: {
      uuid: '123',
      soNumber: 'SO-202412-0001',
      status: 'approved',
    },
  },
};
```

---

## 4. Design Tokens

### 4.1 Colors

```typescript
// src/stories/DesignTokens.stories.tsx

import type { Meta, StoryObj } from '@storybook/react';

const ColorPalette = () => (
  <div className="grid grid-cols-5 gap-4">
    <div className="space-y-2">
      <div className="h-20 w-20 bg-orange-500 rounded"></div>
      <p className="text-sm">Primary<br/>#F97316</p>
    </div>
    <div className="space-y-2">
      <div className="h-20 w-20 bg-slate-700 rounded"></div>
      <p className="text-sm">Industrial<br/>#334155</p>
    </div>
    <div className="space-y-2">
      <div className="h-20 w-20 bg-emerald-500 rounded"></div>
      <p className="text-sm">Success<br/>#10B981</p>
    </div>
    <div className="space-y-2">
      <div className="h-20 w-20 bg-amber-500 rounded"></div>
      <p className="text-sm">Warning<br/>#F59E0B</p>
    </div>
    <div className="space-y-2">
      <div className="h-20 w-20 bg-red-500 rounded"></div>
      <p className="text-sm">Danger<br/>#EF4444</p>
    </div>
  </div>
);

const meta: Meta = {
  title: 'Design System/Colors',
};

export default meta;

export const Palette: StoryObj = {
  render: () => <ColorPalette />,
};
```

---

## 5. Accessibility Testing

```typescript
// Storybook a11y addon checks:
// - Color contrast
// - ARIA labels
// - Keyboard navigation
// - Focus management

// All stories automatically tested for:
// - [x] Color contrast ratio ≥ 4.5:1
// - [x] Interactive elements have accessible names
// - [x] Focus visible on all controls
// - [x] No duplicate IDs
```

---

## 6. Commands

```bash
# Development
npm run storybook

# Build static site
npm run build-storybook

# Visual testing
npm run test-storybook

# Deploy to Chromatic (optional)
npx chromatic --project-token=xxx
```

---

## 7. Component Checklist

| Component   | Story | a11y | Tests |
| ----------- | ----- | ---- | ----- |
| Button      | ✅     | ✅    | ✅     |
| TextInput   | ✅     | ✅    | ✅     |
| SelectInput | ✅     | ✅    | ✅     |
| DatePicker  | ✅     | ✅    | ✅     |
| DataTable   | ✅     | ✅    | ✅     |
| StatusBadge | ✅     | ✅    | ✅     |
| Modal       | ✅     | ✅    | ✅     |
| Toast       | ✅     | ✅    | ✅     |
| Sidebar     | ✅     | ✅    | ✅     |
| Header      | ✅     | ✅    | ✅     |

---

## 8. Access

```
Development: http://localhost:6006
Deployed: https://storybook.sentient.local
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
