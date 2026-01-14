# 📋 BPA-3: AI & Analytics Modules

> **Business Process Analysis - Phase 3**
> Target: UI/UX & Frontend Team

---

## 1. Overview

BPA-3 mencakup modul **AI-powered features** yang memberikan nilai tambah intelligence pada sistem.

| Modul                    | Priority | Kompleksitas |
| ------------------------ | -------- | ------------ |
| AI Chat Assistant        | P0       | High         |
| Alert & Notification     | P0       | Medium       |
| AI Insights Dashboard    | P1       | High         |
| Reports & Analytics      | P1       | Medium       |
| Executive Hub (WhatsApp) | P2       | High         |
| Audit Log Viewer         | P1       | Low          |

---

## 2. AI Chat Assistant

### 2.1 User Story

```
AS A manager
I WANT TO chat with AI about business data
SO THAT I can get insights without writing queries or waiting for reports
```

### 2.2 Features

| Feature                | Description                            |
| ---------------------- | -------------------------------------- |
| **Free Text Query**    | Tanya dalam bahasa natural             |
| **Contextual Answers** | AI menjawab berdasarkan data real-time |
| **Source Citation**    | Tampilkan data source (tabel, periode) |
| **Suggested Actions**  | AI menyarankan next steps              |
| **Chat History**       | Simpan percakapan sebelumnya           |
| **Export Answer**      | Copy/export jawaban ke clipboard/PDF   |

### 2.3 Example Interactions

| User Query                        | AI Response Type                  |
| --------------------------------- | --------------------------------- |
| "Berapa revenue bulan ini?"       | Number + comparison to last month |
| "Kenapa margin turun?"            | Root cause analysis + chart       |
| "Customer mana yang telat bayar?" | Table of AR overdue               |
| "Prediksi penjualan Q1 2025"      | Forecast chart + confidence       |
| "Buat laporan DO minggu ini"      | Auto-generate report              |

### 2.4 Chat UI Layout

```
┌─────────────────────────────────────────────────────────────┐
│ 🤖 AI Assistant                              [New Chat] [⋮] │
├────────────────────────────────────┬────────────────────────┤
│ CHAT HISTORY                       │                        │
│ ────────────────                   │                        │
│ 📄 Revenue analysis (Today)        │   Welcome! Saya AI     │
│ 📄 Customer overdue (Yesterday)    │   Assistant Anda.      │
│ 📄 Production issue (2 days ago)   │                        │
│                                    │   Tanyakan apa saja    │
│                                    │   tentang bisnis Anda. │
│                                    │                        │
│                                    │ ┌────────────────────┐ │
│                                    │ │ 💡 Suggested:      │ │
│                                    │ │ • Revenue hari ini │ │
│                                    │ │ • Status PO pending│ │
│                                    │ │ • Anomali terbaru  │ │
│                                    │ └────────────────────┘ │
│                                    │                        │
├────────────────────────────────────┴────────────────────────┤
│ 💬 [Ketik pertanyaan Anda...                      ] [Send] │
└─────────────────────────────────────────────────────────────┘
```

### 2.5 AI Response Components

| Component       | Description                                  |
| --------------- | -------------------------------------------- |
| **Text Answer** | Paragraf penjelasan AI                       |
| **Data Table**  | Tabel data jika ada list                     |
| **Chart**       | Visualisasi jika relevan                     |
| **Source**      | "Data from t_so, period: Dec 2024"           |
| **Confidence**  | 🟢 High / 🟡 Medium / 🔴 Low                    |
| **Actions**     | Buttons: "Export", "Drill Down", "Follow Up" |

### 2.6 API Endpoints

| Method | Endpoint               | Description                 |
| ------ | ---------------------- | --------------------------- |
| POST   | `/api/ai/chat`         | Send query, get AI response |
| GET    | `/api/ai/chat/history` | Get chat history            |
| GET    | `/api/ai/chat/:id`     | Get specific conversation   |
| DELETE | `/api/ai/chat/:id`     | Delete conversation         |
| GET    | `/api/ai/suggestions`  | Get suggested queries       |

### 2.7 Request/Response Example

```json
// Request
{
  "query": "Kenapa margin bulan ini turun?",
  "context": {
    "user_role": "Sales Manager",
    "current_page": "dashboard"
  }
}

// Response
{
  "answer": "Margin bulan ini turun 15% karena...",
  "data": {
    "type": "chart",
    "chartType": "bar",
    "data": [...]
  },
  "sources": ["t_so", "m_item"],
  "confidence": 0.85,
  "actions": [
    {"label": "Lihat Detail", "action": "drill_down"},
    {"label": "Export", "action": "export_pdf"}
  ]
}
```

---

## 3. Alert & Notification

### 3.1 User Story

```
AS A manager
I WANT TO receive alerts for anomalies
SO THAT I can take action before problems escalate
```

### 3.2 Alert Types

| Alert Type               | Trigger              | Channel                    |
| ------------------------ | -------------------- | -------------------------- |
| **Production Anomaly**   | Reject rate > 5%     | Dashboard, WhatsApp        |
| **Cashflow Critical**    | Balance < threshold  | Dashboard, WhatsApp, Email |
| **PO Overdue**           | Expected date passed | Dashboard                  |
| **SO Pending Approval**  | > 24 hours pending   | Dashboard                  |
| **Low Stock**            | Below minimum level  | Dashboard                  |
| **Customer Payment Due** | 7 days before due    | Dashboard                  |

### 3.3 Notification Center UI

```
┌───────────────────────────────────────┐
│ 🔔 Notifications                [Mark All Read]
├───────────────────────────────────────┤
│ 🔴 NEW                                │
│ ┌─────────────────────────────────┐   │
│ │ ⚠️ High Reject Rate             │   │
│ │ Machine MCH-003 reject 8.5%     │   │
│ │ 10 minutes ago        [View]    │   │
│ └─────────────────────────────────┘   │
│                                       │
│ ┌─────────────────────────────────┐   │
│ │ 💰 Payment Reminder             │   │
│ │ PT ABC - Rp 150jt due in 3 days │   │
│ │ 1 hour ago            [View]    │   │
│ └─────────────────────────────────┘   │
│                                       │
│ 📖 EARLIER                            │
│ ┌─────────────────────────────────┐   │
│ │ ✅ PO Approved                  │   │
│ │ PO-2024-0456 approved by Dir.   │   │
│ │ Yesterday              [View]   │   │
│ └─────────────────────────────────┘   │
└───────────────────────────────────────┘
```

### 3.4 Alert Configuration

Users should be able to configure their alert preferences:

| Setting     | Options                          |
| ----------- | -------------------------------- |
| Alert Types | Checkbox per type                |
| Channels    | Dashboard, Email, WhatsApp       |
| Quiet Hours | Time range to mute alerts        |
| Threshold   | Custom thresholds per alert type |

---

## 4. AI Insights Dashboard

### 4.1 User Story

```
AS A decision maker
I WANT TO see AI-generated insights
SO THAT I can quickly identify issues and opportunities
```

### 4.2 Insight Cards

| Insight Type         | Example                                          |
| -------------------- | ------------------------------------------------ |
| **Anomaly Detected** | "Revenue drop 20% compared to last week"         |
| **Trend Analysis**   | "Customer A orders increasing 15% MoM"           |
| **Prediction**       | "Stock Item X will run out in 5 days"            |
| **Recommendation**   | "Consider reordering Item Y from Vendor B"       |
| **Risk Alert**       | "3 POs from Vendor C delayed - vendor risk high" |

### 4.3 Dashboard Layout

```
┌─────────────────────────────────────────────────────────────┐
│ 🧠 AI Insights                               Filter: [All ▼]│
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ ┌──────────────────────────────┐ ┌──────────────────────────┐
│ │ 🔴 CRITICAL                  │ │ 🟡 WARNING               │
│ │                              │ │                          │
│ │ Cash balance projected to    │ │ 5 customers have overdue │
│ │ go negative in 7 days.       │ │ payments totaling Rp 2B. │
│ │                              │ │                          │
│ │ [View Details]  [Dismiss]    │ │ [View List]   [Dismiss]  │
│ └──────────────────────────────┘ └──────────────────────────┘
│                                                             │
│ ┌──────────────────────────────┐ ┌──────────────────────────┐
│ │ 🟢 OPPORTUNITY               │ │ 📊 TREND                 │
│ │                              │ │                          │
│ │ Customer XYZ order pattern   │ │ Production OEE improved  │
│ │ suggests upsell opportunity. │ │ 5% this month.           │
│ │                              │ │                          │
│ │ [View Analysis] [Dismiss]    │ │ [View Chart]  [Dismiss]  │
│ └──────────────────────────────┘ └──────────────────────────┘
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 4.4 Insight Lifecycle

```
[AI Generated] → [Active] → [Viewed] → [Actioned/Dismissed]
```

---

## 5. Reports & Analytics

### 5.1 Features

| Feature                   | Description                                |
| ------------------------- | ------------------------------------------ |
| **Pre-built Reports**     | Standard reports (Sales, Production, etc.) |
| **Custom Report Builder** | Drag-drop fields, filters                  |
| **Scheduled Reports**     | Auto-generate & email on schedule          |
| **Export Options**        | PDF, Excel, CSV                            |
| **Share Report**          | Generate shareable link                    |

### 5.2 Pre-built Report List

| Report            | Category    | Parameters           |
| ----------------- | ----------- | -------------------- |
| Sales Summary     | Sales       | Date range, Customer |
| Sales by Customer | Sales       | Date range           |
| Sales by Item     | Sales       | Date range, Category |
| PO Status Report  | Procurement | Date range, Vendor   |
| Production Output | Production  | Date range, Machine  |
| Stock Balance     | Inventory   | Warehouse, Category  |
| AR Aging          | Finance     | As of date           |
| AP Aging          | Finance     | As of date           |

### 5.3 Report Viewer UI

```
┌─────────────────────────────────────────────────────────────┐
│ 📊 Sales Summary Report                                     │
├─────────────────────────────────────────────────────────────┤
│ Period: [01/12/2024] to [27/12/2024]   Customer: [All ▼]   │
│                                        [Generate] [Export ▼]│
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Total Revenue: Rp 5,234,000,000                           │
│  Total Orders: 156                                          │
│  Avg Order Value: Rp 33,551,282                            │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐
│  │     [==========Revenue Chart by Week==========]         │
│  └─────────────────────────────────────────────────────────┘
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐
│  │ Top 10 Customers                                        │
│  ├───────────────────────────────┬─────────────────────────┤
│  │ Customer                      │ Revenue                 │
│  ├───────────────────────────────┼─────────────────────────┤
│  │ PT ABC Manufacturing          │ Rp 890,000,000         │
│  │ CV XYZ Trading                │ Rp 756,000,000         │
│  │ ...                           │ ...                     │
│  └───────────────────────────────┴─────────────────────────┘
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 6. Audit Log Viewer

### 6.1 User Story

```
AS A system admin
I WANT TO view all user activities
SO THAT I can audit actions for compliance and security
```

### 6.2 Features

| Feature              | Description                       |
| -------------------- | --------------------------------- |
| **Log List**         | Paginated, searchable log entries |
| **Filter by User**   | See actions of specific user      |
| **Filter by Action** | CREATE, UPDATE, DELETE, LOGIN     |
| **Filter by Entity** | Sales Order, User, etc.           |
| **Date Range**       | Filter by date/time               |
| **View Detail**      | See before/after values           |

### 6.3 Log Entry Display

| Field      | Description                                   |
| ---------- | --------------------------------------------- |
| Timestamp  | When action occurred                          |
| User       | Who performed action                          |
| Action     | CREATE, UPDATE, DELETE, LOGIN, LOGOUT, EXPORT |
| Entity     | Table/module affected                         |
| Entity ID  | Record ID                                     |
| Changes    | JSON diff (before/after)                      |
| IP Address | User's IP                                     |

### 6.4 Table View

```
┌─────────────────────────────────────────────────────────────────────┐
│ 📝 Audit Log                                                        │
├─────────────────────────────────────────────────────────────────────┤
│ [Search...] [User ▼] [Action ▼] [Entity ▼] [Date From] [Date To]   │
├─────────────────────────────────────────────────────────────────────┤
│ Timestamp           │ User   │ Action │ Entity  │ ID    │ Details  │
├─────────────────────┼────────┼────────┼─────────┼───────┼──────────┤
│ 27/12/24 14:30:25  │ john   │ UPDATE │ t_so    │ 12345 │ [View]   │
│ 27/12/24 14:28:10  │ jane   │ CREATE │ t_po    │ 456   │ [View]   │
│ 27/12/24 14:25:00  │ admin  │ DELETE │ m_cust  │ 789   │ [View]   │
│ 27/12/24 14:20:00  │ john   │ LOGIN  │ -       │ -     │ [View]   │
└─────────────────────┴────────┴────────┴─────────┴───────┴──────────┘
```

---

## 7. Common AI UI Components

### 7.1 Loading States

| State      | Display                           |
| ---------- | --------------------------------- |
| Thinking   | Animated dots "AI is thinking..." |
| Analyzing  | Progress bar with steps           |
| Generating | Typewriter effect for text        |

### 7.2 Error States

| Error     | Message                                    |
| --------- | ------------------------------------------ |
| Timeout   | "AI took too long. Please try again."      |
| No Data   | "Not enough data to answer this question." |
| Ambiguous | "Please be more specific. Did you mean..." |

### 7.3 Confidence Indicator

```
🟢 High (> 80%)    - AI is confident in this answer
🟡 Medium (50-80%) - Answer may need verification
🔴 Low (< 50%)     - Take this with caution
```

---

## 8. Deliverables Checklist

### AI Chat
- [ ] Chat interface (full page)
- [ ] Chat sidebar (mini version)
- [ ] Chat history panel
- [ ] AI response components (text, table, chart)
- [ ] Suggested queries
- [ ] Export/copy functionality

### Alerts & Notifications
- [ ] Notification dropdown in header
- [ ] Notification center page
- [ ] Alert configuration settings
- [ ] Push notification integration

### AI Insights
- [ ] Insights dashboard
- [ ] Insight cards (Critical, Warning, Opportunity, Trend)
- [ ] Insight detail modal
- [ ] Dismiss/action tracking

### Reports
- [ ] Report list page
- [ ] Report viewer with filters
- [ ] Export functionality (PDF, Excel)
- [ ] Scheduled report config

### Audit Log
- [ ] Audit log list page
- [ ] Filter panel
- [ ] Log detail modal (before/after diff)

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
