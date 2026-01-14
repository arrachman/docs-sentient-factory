# 🗄️ Database Scripts

> **Parent Docs:** [System Analysis](system_analysis.md), [Backend](backend.md)
> **Database:** PostgreSQL 15+
> **Last Sync:** 2024-12-27

---

## 1. DDL (Data Definition Language)

### 1.1 Extensions & Schema

```sql
-- Enable required extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- Create schema
CREATE SCHEMA IF NOT EXISTS sentient;
SET search_path TO sentient, public;
```

### 1.2 Master Tables

```sql
-- ═══════════════════════════════════════════════════════════
-- MASTER TABLES (m_*)
-- ═══════════════════════════════════════════════════════════

-- Role Table
CREATE TABLE m_role (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    permissions JSONB DEFAULT '[]',
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT
);

-- User Table
CREATE TABLE m_user (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role_id BIGINT REFERENCES m_role(id),
    is_active BOOLEAN DEFAULT TRUE,
    last_login TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Customer Table
CREATE TABLE m_cust (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(200) NOT NULL,
    addr TEXT,
    phone VARCHAR(20),
    email VARCHAR(100),
    segment VARCHAR(20) DEFAULT 'Regular', -- VIP, Regular
    credit_lim NUMERIC(15,2) DEFAULT 0,
    last_order_dt DATE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Item Table
CREATE TABLE m_item (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(200) NOT NULL,
    category VARCHAR(50),
    std_cost NUMERIC(15,2) DEFAULT 0,
    sell_price NUMERIC(15,2) DEFAULT 0,
    uom VARCHAR(20) DEFAULT 'PCS',
    min_stock NUMERIC(15,2) DEFAULT 0,
    lead_time_days INT DEFAULT 7,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Vendor Table
CREATE TABLE m_vendor (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(200) NOT NULL,
    addr TEXT,
    phone VARCHAR(20),
    email VARCHAR(100),
    bank_acc VARCHAR(50),
    otd_score NUMERIC(5,2) DEFAULT 100, -- On-Time Delivery %
    quality_score NUMERIC(5,2) DEFAULT 100,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Machine Table
CREATE TABLE m_machine (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    location VARCHAR(100),
    cap_per_hr NUMERIC(10,2) DEFAULT 0,
    run_hours NUMERIC(10,2) DEFAULT 0,
    status VARCHAR(20) DEFAULT 'Active', -- Active, Maintenance, Inactive
    last_maint_dt DATE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Warehouse Table
CREATE TABLE m_wh (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    zone VARCHAR(10),
    capacity_m3 NUMERIC(10,2) DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Employee Table
CREATE TABLE m_emp (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    dept VARCHAR(50),
    position VARCHAR(50),
    shift VARCHAR(10) DEFAULT '1',
    user_id BIGINT REFERENCES m_user(id),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);
```

### 1.3 Transaction Tables

```sql
-- ═══════════════════════════════════════════════════════════
-- TRANSACTION TABLES (t_*)
-- ═══════════════════════════════════════════════════════════

-- Sales Order Header
CREATE TABLE t_so (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    so_number VARCHAR(20) UNIQUE NOT NULL,
    cust_id BIGINT REFERENCES m_cust(id),
    order_dt DATE NOT NULL DEFAULT CURRENT_DATE,
    deadline_dt DATE,
    subtotal NUMERIC(15,2) DEFAULT 0,
    disc_pct NUMERIC(5,2) DEFAULT 0,
    disc_amt NUMERIC(15,2) DEFAULT 0,
    tax_pct NUMERIC(5,2) DEFAULT 11,
    tax_amt NUMERIC(15,2) DEFAULT 0,
    total_amt NUMERIC(15,2) DEFAULT 0,
    status VARCHAR(20) DEFAULT 'Draft', -- Draft, Pending, Approved, InProgress, Completed, Cancelled
    notes TEXT,
    approved_by BIGINT REFERENCES m_user(id),
    approved_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Sales Order Detail
CREATE TABLE t_so_detail (
    id BIGSERIAL PRIMARY KEY,
    so_id BIGINT REFERENCES t_so(id) ON DELETE CASCADE,
    item_id BIGINT REFERENCES m_item(id),
    qty NUMERIC(15,2) NOT NULL,
    price NUMERIC(15,2) NOT NULL,
    disc_pct NUMERIC(5,2) DEFAULT 0,
    subtotal NUMERIC(15,2) NOT NULL,
    delivered_qty NUMERIC(15,2) DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Delivery Order Header
CREATE TABLE t_do (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    do_number VARCHAR(20) UNIQUE NOT NULL,
    so_id BIGINT REFERENCES t_so(id),
    ship_dt DATE NOT NULL DEFAULT CURRENT_DATE,
    driver VARCHAR(100),
    vehicle VARCHAR(50),
    status VARCHAR(20) DEFAULT 'Pending', -- Pending, Shipped, Delivered, Cancelled
    delivered_at TIMESTAMPTZ,
    pod_file VARCHAR(255), -- Proof of Delivery
    notes TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Delivery Order Detail
CREATE TABLE t_do_detail (
    id BIGSERIAL PRIMARY KEY,
    do_id BIGINT REFERENCES t_do(id) ON DELETE CASCADE,
    so_detail_id BIGINT REFERENCES t_so_detail(id),
    item_id BIGINT REFERENCES m_item(id),
    qty NUMERIC(15,2) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Purchase Order Header
CREATE TABLE t_po (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    po_number VARCHAR(20) UNIQUE NOT NULL,
    vendor_id BIGINT REFERENCES m_vendor(id),
    order_dt DATE NOT NULL DEFAULT CURRENT_DATE,
    expected_dt DATE,
    subtotal NUMERIC(15,2) DEFAULT 0,
    tax_pct NUMERIC(5,2) DEFAULT 11,
    tax_amt NUMERIC(15,2) DEFAULT 0,
    total_amt NUMERIC(15,2) DEFAULT 0,
    status VARCHAR(20) DEFAULT 'Draft', -- Draft, PendingMgr, PendingDir, Approved, Ordered, PartialRcv, Received, Cancelled
    notes TEXT,
    approved_by BIGINT REFERENCES m_user(id),
    approved_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Purchase Order Detail
CREATE TABLE t_po_detail (
    id BIGSERIAL PRIMARY KEY,
    po_id BIGINT REFERENCES t_po(id) ON DELETE CASCADE,
    item_id BIGINT REFERENCES m_item(id),
    qty NUMERIC(15,2) NOT NULL,
    price NUMERIC(15,2) NOT NULL,
    subtotal NUMERIC(15,2) NOT NULL,
    received_qty NUMERIC(15,2) DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Work Order
CREATE TABLE t_wo (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    wo_number VARCHAR(20) UNIQUE NOT NULL,
    item_id BIGINT REFERENCES m_item(id),
    machine_id BIGINT REFERENCES m_machine(id),
    target_qty NUMERIC(15,2) NOT NULL,
    good_qty NUMERIC(15,2) DEFAULT 0,
    reject_qty NUMERIC(15,2) DEFAULT 0,
    yield_pct NUMERIC(5,2) DEFAULT 0,
    scheduled_dt DATE,
    start_dt TIMESTAMPTZ,
    end_dt TIMESTAMPTZ,
    status VARCHAR(20) DEFAULT 'Scheduled', -- Scheduled, InProgress, Completed, Cancelled
    operator_id BIGINT REFERENCES m_emp(id),
    notes TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    deleted_at TIMESTAMPTZ,
    deleted_by BIGINT
);

-- Inventory Stock
CREATE TABLE t_inv (
    id BIGSERIAL PRIMARY KEY,
    item_id BIGINT REFERENCES m_item(id),
    wh_id BIGINT REFERENCES m_wh(id),
    qty_on_hand NUMERIC(15,2) DEFAULT 0,
    qty_reserved NUMERIC(15,2) DEFAULT 0,
    qty_available NUMERIC(15,2) GENERATED ALWAYS AS (qty_on_hand - qty_reserved) STORED,
    last_move_dt DATE,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT,
    UNIQUE(item_id, wh_id)
);

-- AR (Account Receivable)
CREATE TABLE t_ar (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    so_id BIGINT REFERENCES t_so(id),
    cust_id BIGINT REFERENCES m_cust(id),
    inv_number VARCHAR(30),
    inv_dt DATE,
    amt NUMERIC(15,2) NOT NULL,
    due_dt DATE,
    paid_dt DATE,
    paid_amt NUMERIC(15,2) DEFAULT 0,
    status VARCHAR(20) DEFAULT 'Open', -- Open, Partial, Paid, Overdue
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by BIGINT,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    updated_by BIGINT
);
```

### 1.4 AI & Audit Tables

```sql
-- ═══════════════════════════════════════════════════════════
-- AI & AUDIT TABLES
-- ═══════════════════════════════════════════════════════════

-- AI Alert
CREATE TABLE ai_alert (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    alert_type VARCHAR(50) NOT NULL, -- LowStock, Overdue, HighReject, MachineDown
    severity VARCHAR(20) DEFAULT 'medium', -- low, medium, high, critical
    entity_type VARCHAR(50), -- SO, PO, WO, Customer, Machine
    entity_id BIGINT,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    is_read BOOLEAN DEFAULT FALSE,
    is_resolved BOOLEAN DEFAULT FALSE,
    acknowledged_by BIGINT REFERENCES m_user(id),
    acknowledged_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Audit Log
CREATE TABLE log_audit (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT REFERENCES m_user(id),
    action VARCHAR(20) NOT NULL, -- CREATE, UPDATE, DELETE, LOGIN, LOGOUT
    entity_type VARCHAR(50) NOT NULL,
    entity_id BIGINT,
    old_values JSONB,
    new_values JSONB,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Status History Log
CREATE TABLE log_status_history (
    id BIGSERIAL PRIMARY KEY,
    entity_type VARCHAR(50) NOT NULL, -- SO, DO, PO, WO
    entity_id BIGINT NOT NULL,
    old_status VARCHAR(30),
    new_status VARCHAR(30) NOT NULL,
    changed_by BIGINT REFERENCES m_user(id),
    changed_at TIMESTAMPTZ DEFAULT NOW(),
    duration_min INT, -- Duration in previous status
    notes TEXT
);

-- AI Chat History
CREATE TABLE ai_chat (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    user_id BIGINT REFERENCES m_user(id),
    session_id UUID NOT NULL,
    role VARCHAR(20) NOT NULL, -- user, assistant
    content TEXT NOT NULL,
    sources JSONB,
    confidence NUMERIC(3,2),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_so_status ON t_so(status);
CREATE INDEX idx_so_cust ON t_so(cust_id);
CREATE INDEX idx_so_date ON t_so(order_dt);
CREATE INDEX idx_po_status ON t_po(status);
CREATE INDEX idx_wo_status ON t_wo(status);
CREATE INDEX idx_inv_item ON t_inv(item_id);
CREATE INDEX idx_alert_type ON ai_alert(alert_type);
CREATE INDEX idx_alert_unread ON ai_alert(is_read) WHERE is_read = FALSE;
CREATE INDEX idx_audit_entity ON log_audit(entity_type, entity_id);
CREATE INDEX idx_chat_session ON ai_chat(session_id);
```

---

## 2. Functions

### 2.1 Auto Generate Code

```sql
-- ═══════════════════════════════════════════════════════════
-- FUNCTION: Generate Document Code
-- Usage: gen_doc_code('SO') → 'SO-202412-0001'
-- ═══════════════════════════════════════════════════════════

CREATE OR REPLACE FUNCTION gen_doc_code(prefix VARCHAR)
RETURNS VARCHAR AS $$
DECLARE
    year_month VARCHAR;
    last_num INT;
    new_code VARCHAR;
    table_name VARCHAR;
    code_field VARCHAR;
BEGIN
    year_month := TO_CHAR(CURRENT_DATE, 'YYYYMM');
    
    -- Determine table and field
    CASE prefix
        WHEN 'SO' THEN table_name := 't_so'; code_field := 'so_number';
        WHEN 'DO' THEN table_name := 't_do'; code_field := 'do_number';
        WHEN 'PO' THEN table_name := 't_po'; code_field := 'po_number';
        WHEN 'WO' THEN table_name := 't_wo'; code_field := 'wo_number';
        ELSE RAISE EXCEPTION 'Unknown prefix: %', prefix;
    END CASE;
    
    -- Get last number for this month
    EXECUTE format(
        'SELECT COALESCE(MAX(CAST(SPLIT_PART(%I, ''-'', 3) AS INT)), 0) 
         FROM %I WHERE %I LIKE $1',
        code_field, table_name, code_field
    ) INTO last_num USING prefix || '-' || year_month || '-%';
    
    -- Generate new code
    new_code := prefix || '-' || year_month || '-' || LPAD((last_num + 1)::TEXT, 4, '0');
    
    RETURN new_code;
END;
$$ LANGUAGE plpgsql;
```

### 2.2 Auto Update Timestamp

```sql
-- ═══════════════════════════════════════════════════════════
-- FUNCTION: Auto Update Timestamp
-- ═══════════════════════════════════════════════════════════

CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### 2.3 Log Status Change

```sql
-- ═══════════════════════════════════════════════════════════
-- FUNCTION: Log Status Change
-- ═══════════════════════════════════════════════════════════

CREATE OR REPLACE FUNCTION log_status_change()
RETURNS TRIGGER AS $$
DECLARE
    duration INT;
BEGIN
    IF OLD.status IS DISTINCT FROM NEW.status THEN
        -- Calculate duration in previous status
        duration := EXTRACT(EPOCH FROM (NOW() - OLD.updated_at)) / 60;
        
        INSERT INTO log_status_history (
            entity_type, entity_id, old_status, new_status, 
            changed_by, duration_min
        )
        VALUES (
            TG_TABLE_NAME, NEW.id, OLD.status, NEW.status,
            NEW.updated_by, duration
        );
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### 2.4 Calculate SO Total

```sql
-- ═══════════════════════════════════════════════════════════
-- FUNCTION: Recalculate SO Total
-- ═══════════════════════════════════════════════════════════

CREATE OR REPLACE FUNCTION recalc_so_total(so_id_param BIGINT)
RETURNS VOID AS $$
DECLARE
    subtotal_val NUMERIC;
    disc_val NUMERIC;
    tax_val NUMERIC;
    total_val NUMERIC;
    disc_pct_val NUMERIC;
    tax_pct_val NUMERIC;
BEGIN
    -- Get discount and tax percentage
    SELECT disc_pct, tax_pct INTO disc_pct_val, tax_pct_val
    FROM t_so WHERE id = so_id_param;
    
    -- Calculate subtotal from details
    SELECT COALESCE(SUM(subtotal), 0) INTO subtotal_val
    FROM t_so_detail WHERE so_id = so_id_param;
    
    -- Calculate amounts
    disc_val := subtotal_val * disc_pct_val / 100;
    tax_val := (subtotal_val - disc_val) * tax_pct_val / 100;
    total_val := subtotal_val - disc_val + tax_val;
    
    -- Update SO header
    UPDATE t_so SET
        subtotal = subtotal_val,
        disc_amt = disc_val,
        tax_amt = tax_val,
        total_amt = total_val
    WHERE id = so_id_param;
END;
$$ LANGUAGE plpgsql;
```

### 2.5 Check Stock Availability

```sql
-- ═══════════════════════════════════════════════════════════
-- FUNCTION: Check Stock Availability
-- ═══════════════════════════════════════════════════════════

CREATE OR REPLACE FUNCTION check_stock(
    item_id_param BIGINT,
    wh_id_param BIGINT,
    qty_needed NUMERIC
)
RETURNS BOOLEAN AS $$
DECLARE
    available NUMERIC;
BEGIN
    SELECT qty_available INTO available
    FROM t_inv
    WHERE item_id = item_id_param AND wh_id = wh_id_param;
    
    IF available IS NULL THEN
        RETURN FALSE;
    END IF;
    
    RETURN available >= qty_needed;
END;
$$ LANGUAGE plpgsql;
```

---

## 3. Triggers

```sql
-- ═══════════════════════════════════════════════════════════
-- TRIGGERS: Auto Update Timestamps
-- ═══════════════════════════════════════════════════════════

CREATE TRIGGER tr_user_updated BEFORE UPDATE ON m_user 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_cust_updated BEFORE UPDATE ON m_cust 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_item_updated BEFORE UPDATE ON m_item 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_vendor_updated BEFORE UPDATE ON m_vendor 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_machine_updated BEFORE UPDATE ON m_machine 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_so_updated BEFORE UPDATE ON t_so 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_po_updated BEFORE UPDATE ON t_po 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER tr_wo_updated BEFORE UPDATE ON t_wo 
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

-- ═══════════════════════════════════════════════════════════
-- TRIGGERS: Log Status Changes
-- ═══════════════════════════════════════════════════════════

CREATE TRIGGER tr_so_status AFTER UPDATE ON t_so 
FOR EACH ROW EXECUTE FUNCTION log_status_change();

CREATE TRIGGER tr_do_status AFTER UPDATE ON t_do 
FOR EACH ROW EXECUTE FUNCTION log_status_change();

CREATE TRIGGER tr_po_status AFTER UPDATE ON t_po 
FOR EACH ROW EXECUTE FUNCTION log_status_change();

CREATE TRIGGER tr_wo_status AFTER UPDATE ON t_wo 
FOR EACH ROW EXECUTE FUNCTION log_status_change();
```

---

## 4. Seed Data

### 4.1 Roles & Users

```sql
-- ═══════════════════════════════════════════════════════════
-- SEED: Roles
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_role (id, code, name, permissions) VALUES
(1, 'ADMIN', 'Administrator', 
 '["*"]'),
(2, 'DIRECTOR', 'Director', 
 '["dashboard.*","so.*","po.approve","wo.*","report.*","ai.*"]'),
(3, 'MANAGER', 'Manager', 
 '["dashboard.view","so.*","do.*","po.*","wo.*","inv.*","report.view"]'),
(4, 'STAFF', 'Staff', 
 '["dashboard.view","so.read","so.create","so.update","do.read","do.create","inv.view"]'),
(5, 'OPERATOR', 'Operator', 
 '["wo.read","wo.update","inv.view"]');

-- Reset sequence
SELECT setval('m_role_id_seq', (SELECT MAX(id) FROM m_role));

-- ═══════════════════════════════════════════════════════════
-- SEED: Users (password: Password123!)
-- Hash: bcrypt($2a$10$...)
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_user (id, username, email, password_hash, role_id, is_active, created_by) VALUES
(1, 'admin', 'admin@sentient.local', 
 '$2a$10$rQnM1VJvXq7x1hZf0HxOB.Wh5J8L9M0N1P2Q3R4S5T6U7V8W9X0Y1Z', 1, TRUE, 1),
(2, 'director', 'director@sentient.local', 
 '$2a$10$rQnM1VJvXq7x1hZf0HxOB.Wh5J8L9M0N1P2Q3R4S5T6U7V8W9X0Y1Z', 2, TRUE, 1),
(3, 'manager', 'manager@sentient.local', 
 '$2a$10$rQnM1VJvXq7x1hZf0HxOB.Wh5J8L9M0N1P2Q3R4S5T6U7V8W9X0Y1Z', 3, TRUE, 1),
(4, 'sales', 'sales@sentient.local', 
 '$2a$10$rQnM1VJvXq7x1hZf0HxOB.Wh5J8L9M0N1P2Q3R4S5T6U7V8W9X0Y1Z', 4, TRUE, 1),
(5, 'operator', 'operator@sentient.local', 
 '$2a$10$rQnM1VJvXq7x1hZf0HxOB.Wh5J8L9M0N1P2Q3R4S5T6U7V8W9X0Y1Z', 5, TRUE, 1);

SELECT setval('m_user_id_seq', (SELECT MAX(id) FROM m_user));
```

### 4.2 Master Data

```sql
-- ═══════════════════════════════════════════════════════════
-- SEED: Customers
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_cust (id, code, name, addr, phone, segment, credit_lim, created_by) VALUES
(1, 'CUST-001', 'PT ABC Manufacturing', 'Jl. Industri No. 123, Bekasi', '021-88881111', 'VIP', 500000000, 1),
(2, 'CUST-002', 'CV XYZ Trading', 'Jl. Raya Bogor No. 456', '021-88882222', 'Regular', 100000000, 1),
(3, 'CUST-003', 'PT Maju Jaya Sentosa', 'Kawasan Industri MM2100, Cikarang', '021-88883333', 'VIP', 750000000, 1),
(4, 'CUST-004', 'UD Sentosa Abadi', 'Jl. Mangga Dua No. 789', '021-88884444', 'Regular', 50000000, 1),
(5, 'CUST-005', 'PT Global Tech Indonesia', 'BSD City, Tangerang', '021-88885555', 'VIP', 1000000000, 1);

SELECT setval('m_cust_id_seq', (SELECT MAX(id) FROM m_cust));

-- ═══════════════════════════════════════════════════════════
-- SEED: Items
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_item (id, code, name, category, std_cost, sell_price, uom, min_stock, created_by) VALUES
(1, 'ITM-001', 'Widget Pro X1', 'Finished Goods', 45000, 65000, 'PCS', 100, 1),
(2, 'ITM-002', 'Widget Pro X2', 'Finished Goods', 55000, 80000, 'PCS', 100, 1),
(3, 'ITM-003', 'Gear Assembly A', 'Component', 15000, 25000, 'PCS', 200, 1),
(4, 'ITM-004', 'Gear Assembly B', 'Component', 18000, 30000, 'PCS', 200, 1),
(5, 'ITM-005', 'Steel Plate 2mm', 'Raw Material', 85000, 100000, 'SHEET', 50, 1),
(6, 'ITM-006', 'Steel Plate 3mm', 'Raw Material', 120000, 140000, 'SHEET', 50, 1),
(7, 'ITM-007', 'Bolt M8x20', 'Consumable', 500, 1000, 'PCS', 1000, 1),
(8, 'ITM-008', 'Nut M8', 'Consumable', 300, 600, 'PCS', 1000, 1),
(9, 'ITM-009', 'Lubricant Oil 5L', 'Consumable', 75000, 95000, 'CAN', 20, 1),
(10, 'ITM-010', 'Packing Box Large', 'Packaging', 8000, 12000, 'PCS', 100, 1);

SELECT setval('m_item_id_seq', (SELECT MAX(id) FROM m_item));

-- ═══════════════════════════════════════════════════════════
-- SEED: Vendors
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_vendor (id, code, name, addr, phone, otd_score, quality_score, created_by) VALUES
(1, 'VND-001', 'PT Baja Makmur', 'Jl. Industri Berat No. 1, Surabaya', '031-55551111', 95.5, 98.0, 1),
(2, 'VND-002', 'CV Logam Jaya', 'Jl. Metalurgi No. 45, Semarang', '024-55552222', 88.0, 92.5, 1),
(3, 'VND-003', 'PT Komponen Prima', 'Kawasan Industri Jababeka, Cikarang', '021-55553333', 92.0, 96.0, 1);

SELECT setval('m_vendor_id_seq', (SELECT MAX(id) FROM m_vendor));

-- ═══════════════════════════════════════════════════════════
-- SEED: Machines
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_machine (id, code, name, location, cap_per_hr, run_hours, status, created_by) VALUES
(1, 'MCH-001', 'CNC Milling Machine #1', 'Production Hall A', 50, 12500, 'Active', 1),
(2, 'MCH-002', 'CNC Milling Machine #2', 'Production Hall A', 50, 10200, 'Active', 1),
(3, 'MCH-003', 'Laser Cutting Machine', 'Production Hall B', 30, 8500, 'Active', 1);

SELECT setval('m_machine_id_seq', (SELECT MAX(id) FROM m_machine));

-- ═══════════════════════════════════════════════════════════
-- SEED: Warehouses
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_wh (id, code, name, zone, capacity_m3, created_by) VALUES
(1, 'WH-A1', 'Gudang Utama A1', 'A', 5000, 1),
(2, 'WH-B1', 'Gudang Material B1', 'B', 3000, 1);

SELECT setval('m_wh_id_seq', (SELECT MAX(id) FROM m_wh));

-- ═══════════════════════════════════════════════════════════
-- SEED: Employees
-- ═══════════════════════════════════════════════════════════

INSERT INTO m_emp (id, code, name, dept, position, shift, user_id, created_by) VALUES
(1, 'EMP-001', 'Budi Santoso', 'Production', 'Operator', '1', 5, 1),
(2, 'EMP-002', 'Agus Wijaya', 'Production', 'Operator', '2', NULL, 1),
(3, 'EMP-003', 'Siti Rahayu', 'Warehouse', 'Staff', '1', NULL, 1);

SELECT setval('m_emp_id_seq', (SELECT MAX(id) FROM m_emp));
```

### 4.3 Inventory Stock

```sql
-- ═══════════════════════════════════════════════════════════
-- SEED: Inventory Stock
-- ═══════════════════════════════════════════════════════════

INSERT INTO t_inv (item_id, wh_id, qty_on_hand, qty_reserved, last_move_dt, updated_by) VALUES
-- Finished Goods di WH-A1
(1, 1, 500, 50, CURRENT_DATE, 1),   -- Widget Pro X1
(2, 1, 350, 30, CURRENT_DATE, 1),   -- Widget Pro X2
-- Components di WH-A1
(3, 1, 1200, 100, CURRENT_DATE, 1), -- Gear Assembly A
(4, 1, 800, 50, CURRENT_DATE, 1),   -- Gear Assembly B
-- Raw Materials di WH-B1
(5, 2, 200, 20, CURRENT_DATE, 1),   -- Steel Plate 2mm
(6, 2, 150, 10, CURRENT_DATE, 1),   -- Steel Plate 3mm
-- Consumables di WH-B1
(7, 2, 5000, 0, CURRENT_DATE, 1),   -- Bolt M8x20
(8, 2, 5000, 0, CURRENT_DATE, 1),   -- Nut M8
(9, 2, 50, 5, CURRENT_DATE, 1),     -- Lubricant Oil
(10, 2, 300, 0, CURRENT_DATE, 1);   -- Packing Box

```

### 4.4 Sample Transactions

```sql
-- ═══════════════════════════════════════════════════════════
-- SEED: Sales Orders
-- ═══════════════════════════════════════════════════════════

INSERT INTO t_so (id, so_number, cust_id, order_dt, deadline_dt, subtotal, disc_pct, disc_amt, tax_pct, tax_amt, total_amt, status, created_by) VALUES
(1, 'SO-202412-0001', 1, '2024-12-20', '2024-12-27', 6500000, 5, 325000, 11, 679250, 6854250, 'Approved', 4),
(2, 'SO-202412-0002', 3, '2024-12-22', '2024-12-29', 16000000, 0, 0, 11, 1760000, 17760000, 'InProgress', 4),
(3, 'SO-202412-0003', 2, '2024-12-26', '2025-01-03', 2400000, 0, 0, 11, 264000, 2664000, 'Draft', 4);

SELECT setval('t_so_id_seq', (SELECT MAX(id) FROM t_so));

INSERT INTO t_so_detail (so_id, item_id, qty, price, disc_pct, subtotal) VALUES
-- SO-202412-0001
(1, 1, 100, 65000, 0, 6500000),
-- SO-202412-0002
(2, 1, 150, 65000, 0, 9750000),
(2, 2, 78, 80000, 0, 6240000),
-- SO-202412-0003
(3, 3, 80, 25000, 0, 2000000),
(3, 4, 40, 10000, 0, 400000);

-- ═══════════════════════════════════════════════════════════
-- SEED: Purchase Orders
-- ═══════════════════════════════════════════════════════════

INSERT INTO t_po (id, po_number, vendor_id, order_dt, expected_dt, subtotal, tax_pct, tax_amt, total_amt, status, created_by) VALUES
(1, 'PO-202412-0001', 1, '2024-12-18', '2024-12-25', 17000000, 11, 1870000, 18870000, 'Received', 3),
(2, 'PO-202412-0002', 3, '2024-12-23', '2024-12-30', 4500000, 11, 495000, 4995000, 'Approved', 3);

SELECT setval('t_po_id_seq', (SELECT MAX(id) FROM t_po));

INSERT INTO t_po_detail (po_id, item_id, qty, price, subtotal, received_qty) VALUES
-- PO-202412-0001 (Received)
(1, 5, 100, 85000, 8500000, 100),
(1, 6, 70, 120000, 8400000, 70),
-- PO-202412-0002 (Pending)
(2, 3, 300, 15000, 4500000, 0);

-- ═══════════════════════════════════════════════════════════
-- SEED: Work Orders
-- ═══════════════════════════════════════════════════════════

INSERT INTO t_wo (id, wo_number, item_id, machine_id, target_qty, good_qty, reject_qty, yield_pct, scheduled_dt, status, operator_id, created_by) VALUES
(1, 'WO-202412-0001', 1, 1, 200, 195, 5, 97.5, '2024-12-26', 'Completed', 1, 3),
(2, 'WO-202412-0002', 2, 2, 150, 0, 0, 0, '2024-12-27', 'Scheduled', NULL, 3);

SELECT setval('t_wo_id_seq', (SELECT MAX(id) FROM t_wo));

-- ═══════════════════════════════════════════════════════════
-- SEED: Sample Alerts
-- ═══════════════════════════════════════════════════════════

INSERT INTO ai_alert (alert_type, severity, entity_type, entity_id, title, description) VALUES
('LowStock', 'high', 'Item', 6, 'Low Stock Alert: Steel Plate 3mm', 'Stock level at 140 units, below minimum 50. Consider reordering.'),
('Overdue', 'medium', 'SO', 1, 'SO Approaching Deadline', 'SO-202412-0001 deadline in 1 day. Current status: Approved.'),
('HighReject', 'low', 'WO', 1, 'Reject Rate Normal', 'WO-202412-0001 completed with 2.5% reject rate.');
```

---

## 5. Usage Examples

### 5.1 Generate Document Code

```sql
-- Generate new SO number
SELECT gen_doc_code('SO'); -- Returns: SO-202412-0004

-- Generate new PO number
SELECT gen_doc_code('PO'); -- Returns: PO-202412-0003
```

### 5.2 Check Stock

```sql
-- Check if 100 units of item 1 available in warehouse 1
SELECT check_stock(1, 1, 100); -- Returns: TRUE

-- Check if 1000 units available
SELECT check_stock(1, 1, 1000); -- Returns: FALSE
```

### 5.3 View Status History

```sql
-- View SO status changes
SELECT 
    entity_type,
    entity_id,
    old_status,
    new_status,
    changed_at,
    duration_min
FROM log_status_history
WHERE entity_type = 't_so'
ORDER BY changed_at DESC;
```

### 5.4 Dashboard Queries

```sql
-- Revenue this month
SELECT 
    SUM(total_amt) as revenue,
    COUNT(*) as order_count
FROM t_so
WHERE status IN ('Approved', 'InProgress', 'Completed')
  AND order_dt >= DATE_TRUNC('month', CURRENT_DATE);

-- Top 5 customers by revenue
SELECT 
    c.name,
    SUM(s.total_amt) as total_revenue,
    COUNT(s.id) as order_count
FROM t_so s
JOIN m_cust c ON s.cust_id = c.id
WHERE s.status IN ('Approved', 'InProgress', 'Completed')
  AND s.order_dt >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY c.id, c.name
ORDER BY total_revenue DESC
LIMIT 5;

-- Low stock items
SELECT 
    i.code,
    i.name,
    inv.qty_available,
    i.min_stock
FROM t_inv inv
JOIN m_item i ON inv.item_id = i.id
WHERE inv.qty_available <= i.min_stock
ORDER BY (inv.qty_available - i.min_stock) ASC;
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
