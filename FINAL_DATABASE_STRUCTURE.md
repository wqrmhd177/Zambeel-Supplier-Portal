# Final Database Structure - 4 Tables

## ✅ Confirmed Structure

Based on your existing database, the Supplier Portal uses **4 core tables only**.

---

## 📊 Database Tables

### 1. **users** Table

Stores all user accounts with purchaser relationships built-in.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,              -- Auto-increment: 1, 2, 3...
  user_id TEXT UNIQUE NOT NULL,       -- Friendly ID: SUP001, PUR001, AGENT001
  email TEXT UNIQUE NOT NULL,
  password TEXT,
  role TEXT NOT NULL,                 -- supplier, purchaser, agent, admin
  owner_name TEXT,
  store_name TEXT,
  phone_number TEXT,
  city TEXT,
  country TEXT,
  currency TEXT DEFAULT 'USD',
  bank_name TEXT,
  bank_account_number TEXT,
  bank_account_title TEXT,
  bank_country TEXT,
  profile_picture TEXT,
  purchaser_id INTEGER,               -- ⭐ KEY: References users.id of purchaser
  onboarded BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

**Key Points:**
- **`id`** (INTEGER): Auto-increment, used in `price_history.created_by_purchaser_id`
- **`user_id`** (TEXT): Friendly ID, used in `products.fk_owned_by` and `orders.vendor_id`
- **`purchaser_id`** (INTEGER): For suppliers only - references `users.id` of their purchaser
- **No separate junction table** - relationship stored directly in users table

**Example Data:**
| id | user_id | role | purchaser_id | Meaning |
|----|---------|------|--------------|---------|
| 1 | PUR001 | purchaser | NULL | Purchaser (no purchaser_id) |
| 2 | SUP001 | supplier | 1 | Supplier managed by purchaser id=1 |
| 3 | SUP002 | supplier | 1 | Another supplier managed by purchaser id=1 |
| 4 | PUR002 | purchaser | NULL | Another purchaser |
| 5 | SUP003 | supplier | 4 | Supplier managed by purchaser id=4 |

---

### 2. **products** Table

Stores product catalog with variants.

```sql
CREATE TABLE products (
  id BIGSERIAL PRIMARY KEY,
  product_id BIGINT NOT NULL,
  variant_id BIGINT UNIQUE NOT NULL,
  product_title TEXT NOT NULL,
  fk_owned_by TEXT NOT NULL,          -- References users.user_id (TEXT)
  image JSONB,
  size TEXT,
  color TEXT,
  company_sku TEXT,
  variant_selling_price DECIMAL(10, 2),
  variant_stock INTEGER,
  status TEXT DEFAULT 'active',
  category JSONB,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

**Key Points:**
- **`fk_owned_by`** references `users.user_id` (TEXT) - the supplier's friendly ID
- One row per variant
- Same `product_id` groups variants together

---

### 3. **orders** Table

Stores orders synced from Metabase.

```sql
CREATE TABLE orders (
  id BIGSERIAL PRIMARY KEY,
  order_id BIGINT NOT NULL,
  vendor_id TEXT,                     -- References users.user_id (TEXT)
  order_date TIMESTAMPTZ,
  phone TEXT,
  country TEXT,
  title TEXT,
  sku TEXT NOT NULL,
  quantity INTEGER DEFAULT 1,
  supplier_selling_price DECIMAL(10, 2),
  total_payable DECIMAL(10, 2),
  status TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(order_id, sku)
);
```

**Key Points:**
- **`vendor_id`** references `users.user_id` (TEXT) - the supplier's friendly ID
- **`supplier_selling_price`** stores historical price at time of order
- Composite unique key on `(order_id, sku)`

---

### 4. **price_history** Table

Tracks price changes and approval workflow.

```sql
CREATE TABLE price_history (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id BIGINT NOT NULL,
  variant_id BIGINT NOT NULL,
  previous_price DECIMAL(10, 2) NOT NULL,
  updated_price DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  created_by_supplier_id TEXT,        -- References users.user_id (TEXT)
  created_by_purchaser_id INTEGER,    -- References users.id (INTEGER)
  status TEXT DEFAULT 'pending',
  reviewed_at TIMESTAMPTZ,
  reviewed_by TEXT,
  notes TEXT
);
```

**Key Points:**
- **`created_by_supplier_id`** (TEXT): References `users.user_id` for suppliers
- **`created_by_purchaser_id`** (INTEGER): References `users.id` for purchasers
- **`status`**: 'pending', 'approved', or 'rejected'

---

## 🔄 Relationships

### Supplier-Purchaser Relationship

**Stored in `users` table via `purchaser_id` column:**

```sql
-- Get all suppliers for a purchaser (id=1)
SELECT * FROM users 
WHERE purchaser_id = 1 AND role = 'supplier';

-- Get purchaser for a supplier (user_id='SUP001')
SELECT p.* FROM users u
JOIN users p ON u.purchaser_id = p.id
WHERE u.user_id = 'SUP001';
```

**No junction table needed!**

---

## 🔑 ID Usage Summary

| Context | Use `users.id` (INTEGER) | Use `users.user_id` (TEXT) |
|---------|--------------------------|----------------------------|
| Supplier's purchaser | ✅ `users.purchaser_id` | ❌ |
| Price history (purchaser) | ✅ `created_by_purchaser_id` | ❌ |
| Price history (supplier) | ❌ | ✅ `created_by_supplier_id` |
| Product owner | ❌ | ✅ `products.fk_owned_by` |
| Order vendor | ❌ | ✅ `orders.vendor_id` |
| Display to users | ❌ | ✅ Always use user_id |

---

## 📝 Common Queries

### Get Suppliers for a Purchaser

```sql
-- Using purchaser's integer ID
SELECT * FROM users 
WHERE purchaser_id = 1 
AND role = 'supplier';
```

### Get Purchaser for a Supplier

```sql
-- Using supplier's user_id
SELECT p.* FROM users u
JOIN users p ON u.purchaser_id = p.id
WHERE u.user_id = 'SUP001';
```

### Get Products for a Supplier

```sql
-- Using supplier's user_id
SELECT * FROM products 
WHERE fk_owned_by = 'SUP001';
```

### Get Orders for a Supplier

```sql
-- Using supplier's user_id
SELECT * FROM orders 
WHERE vendor_id = 'SUP001';
```

### Get Price History by Supplier

```sql
-- Using supplier's user_id
SELECT * FROM price_history 
WHERE created_by_supplier_id = 'SUP001';
```

### Get Price History by Purchaser

```sql
-- Using purchaser's integer ID
SELECT * FROM price_history 
WHERE created_by_purchaser_id = 1;
```

---

## ⚠️ Important Notes

### 1. No `supplier_purchaser` Junction Table
- Relationship is stored in `users.purchaser_id`
- Simpler structure, fewer joins
- Direct foreign key relationship

### 2. Dual ID System in Users Table
- **`id`** (INTEGER): For database relationships
- **`user_id`** (TEXT): For application logic and display

### 3. Purchaser ID is NULL for Non-Suppliers
- Only suppliers have `purchaser_id` set
- Purchasers, agents, and admins have `purchaser_id = NULL`

---

## ✅ Verification

After running the setup script, verify:

```sql
-- Should return 4
SELECT COUNT(*) FROM information_schema.tables 
WHERE table_schema = 'public' 
AND table_type = 'BASE TABLE'
AND table_name IN ('users', 'products', 'orders', 'price_history');

-- Verify users table has purchaser_id column
SELECT column_name, data_type 
FROM information_schema.columns 
WHERE table_name = 'users' 
AND column_name = 'purchaser_id';

-- Should return: purchaser_id | integer
```

---

## 🎯 Summary

**4 Tables:**
1. ✅ users (with `purchaser_id` column)
2. ✅ products
3. ✅ orders
4. ✅ price_history

**No Extra Tables:**
- ❌ supplier_purchaser (relationship in users table)
- ❌ sessions
- ❌ notifications
- ❌ audit_log

**Key Feature:**
- Supplier-purchaser relationship stored directly in `users.purchaser_id`
- Simpler structure matching your existing database

---

This is the **final, correct structure** matching your existing database! 🎉
