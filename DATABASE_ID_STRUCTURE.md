# Database ID Structure - Important Reference

## 📋 Overview

This document explains how IDs work in the Supplier Portal database, particularly the difference between `users.id` and `users.user_id`.

---

## 🔑 Users Table - Dual ID System

The `users` table has **two ID fields**:

### 1. `id` (INTEGER - SERIAL)
- **Type:** Auto-incrementing integer (1, 2, 3, ...)
- **Usage:** Internal database references
- **Used in:**
  - `price_history.created_by_purchaser_id`
  - `supplier_purchaser.supplier_id`
  - `supplier_purchaser.purchaser_id`

### 2. `user_id` (TEXT - UNIQUE)
- **Type:** Friendly text identifier (e.g., "SUP001", "PUR001")
- **Usage:** Application-level references
- **Used in:**
  - `products.fk_owned_by`
  - `orders.vendor_id`
  - `price_history.created_by_supplier_id`
  - Frontend/Backend logic

---

## 📊 Why Two IDs?

### Historical Reasons:
1. **`user_id` (TEXT)** was used first for human-readable IDs
2. **`id` (INTEGER)** is needed for efficient database joins
3. Both are maintained for backward compatibility

### Use Cases:

**Use `users.id` (INTEGER) when:**
- Creating foreign keys in junction tables
- Referencing purchasers in price_history
- Efficient database joins

**Use `users.user_id` (TEXT) when:**
- Displaying to users
- Frontend/Backend logic
- Referencing suppliers in products/orders

---

## 🗂️ Table-by-Table Breakdown

### 1. **users** Table
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,              -- Auto-increment: 1, 2, 3...
  user_id TEXT UNIQUE NOT NULL,       -- Friendly ID: SUP001, PUR001...
  email TEXT,
  role TEXT,
  ...
);
```

**Example Data:**
| id | user_id | email | role |
|----|---------|-------|------|
| 1 | SUP001 | supplier1@example.com | supplier |
| 2 | PUR001 | purchaser1@example.com | purchaser |
| 3 | AGENT001 | agent1@example.com | agent |

---

### 2. **products** Table
```sql
CREATE TABLE products (
  ...
  fk_owned_by TEXT NOT NULL,  -- References users.user_id (TEXT)
  ...
);
```

**Why TEXT?**
- Products are owned by suppliers
- Suppliers are identified by `user_id` (SUP001, SUP002, etc.)
- Makes queries more readable

**Example:**
```sql
-- Product owned by supplier SUP001
INSERT INTO products (fk_owned_by, ...) VALUES ('SUP001', ...);
```

---

### 3. **orders** Table
```sql
CREATE TABLE orders (
  ...
  vendor_id TEXT,  -- References users.user_id (TEXT)
  ...
);
```

**Why TEXT?**
- Orders are associated with suppliers (vendors)
- Synced from Metabase using supplier's user_id
- Consistent with products table

**Example:**
```sql
-- Order from supplier SUP001
INSERT INTO orders (vendor_id, ...) VALUES ('SUP001', ...);
```

---

### 4. **price_history** Table
```sql
CREATE TABLE price_history (
  ...
  created_by_supplier_id TEXT,        -- References users.user_id (TEXT)
  created_by_purchaser_id INTEGER,    -- References users.id (INTEGER)
  ...
);
```

**Why Mixed Types?**
- **Suppliers** use `user_id` (TEXT) for consistency with products/orders
- **Purchasers** use `id` (INTEGER) for efficient joins with supplier_purchaser table

**Example:**
```sql
-- Price change by supplier SUP001
INSERT INTO price_history (
  created_by_supplier_id,
  created_by_purchaser_id,
  ...
) VALUES (
  'SUP001',  -- Supplier's user_id (TEXT)
  NULL,      -- No purchaser involved
  ...
);

-- Price change by purchaser (id=2)
INSERT INTO price_history (
  created_by_supplier_id,
  created_by_purchaser_id,
  ...
) VALUES (
  NULL,      -- No supplier involved
  2,         -- Purchaser's id (INTEGER)
  ...
);
```

---

### 5. **supplier_purchaser** Table
```sql
CREATE TABLE supplier_purchaser (
  supplier_id INTEGER NOT NULL,    -- References users.id (INTEGER)
  purchaser_id INTEGER NOT NULL,   -- References users.id (INTEGER)
  ...
);
```

**Why INTEGER?**
- Junction table for many-to-many relationship
- Uses `id` for efficient joins
- Both sides use same type for consistency

**Example:**
```sql
-- Link supplier (id=1) with purchaser (id=2)
INSERT INTO supplier_purchaser (supplier_id, purchaser_id)
VALUES (1, 2);
```

---

## 🔍 How to Query

### Get Supplier's Products:
```sql
-- Using user_id (TEXT)
SELECT * FROM products WHERE fk_owned_by = 'SUP001';
```

### Get Purchaser's Suppliers:
```sql
-- Using id (INTEGER)
SELECT u.*
FROM users u
JOIN supplier_purchaser sp ON u.id = sp.supplier_id
WHERE sp.purchaser_id = 2;  -- Purchaser's id
```

### Get Price History by Supplier:
```sql
-- Using user_id (TEXT)
SELECT * FROM price_history
WHERE created_by_supplier_id = 'SUP001';
```

### Get Price History by Purchaser:
```sql
-- Using id (INTEGER)
SELECT * FROM price_history
WHERE created_by_purchaser_id = 2;
```

---

## 🔄 Converting Between IDs

### Get `id` from `user_id`:
```sql
SELECT id FROM users WHERE user_id = 'SUP001';
```

### Get `user_id` from `id`:
```sql
SELECT user_id FROM users WHERE id = 1;
```

### In Application Code (TypeScript):
```typescript
// Get purchaser's integer ID from user_id
async function getPurchaserIntegerId(userId: string): Promise<number | null> {
  const { data } = await supabase
    .from('users')
    .select('id')
    .eq('user_id', userId)
    .single();
  
  return data?.id || null;
}
```

---

## ⚠️ Important Notes

### When Creating Price History:
```typescript
// For suppliers: use user_id (TEXT)
await createPriceHistoryEntry(
  productId,
  variantId,
  oldPrice,
  newPrice,
  userFriendlyId,  // This is user_id (TEXT)
  null,            // No purchaser
  'pending'
);

// For purchasers: use id (INTEGER)
const purchaserIntId = await getPurchaserIntegerId(userId);
await createPriceHistoryEntry(
  productId,
  variantId,
  oldPrice,
  newPrice,
  null,            // No supplier
  purchaserIntId,  // This is id (INTEGER)
  'pending'
);
```

### When Querying Supplier-Purchaser Relationships:
```typescript
// Get purchaser's integer ID first
const purchaserIntId = await getPurchaserIntegerId(userId);

// Then query supplier_purchaser table
const { data: suppliers } = await supabase
  .from('supplier_purchaser')
  .select('supplier_id')
  .eq('purchaser_id', purchaserIntId);  // Use INTEGER id
```

---

## 📝 Summary

| Table | Field | Type | References |
|-------|-------|------|------------|
| users | id | INTEGER | - |
| users | user_id | TEXT | - |
| products | fk_owned_by | TEXT | users.user_id |
| orders | vendor_id | TEXT | users.user_id |
| price_history | created_by_supplier_id | TEXT | users.user_id |
| price_history | created_by_purchaser_id | INTEGER | users.id |
| supplier_purchaser | supplier_id | INTEGER | users.id |
| supplier_purchaser | purchaser_id | INTEGER | users.id |

---

## ✅ Best Practices

1. **Use `user_id` (TEXT) for:**
   - Supplier references in products/orders
   - Display to users
   - Frontend logic

2. **Use `id` (INTEGER) for:**
   - Purchaser references in price_history
   - Junction tables (supplier_purchaser)
   - Database joins

3. **Always convert when needed:**
   - Frontend typically uses `user_id`
   - Some backend operations need `id`
   - Use helper functions to convert

4. **Be consistent:**
   - Suppliers → `user_id` (TEXT)
   - Purchasers → `id` (INTEGER) in specific tables
   - Document which ID type you're using

---

## 🆘 Troubleshooting

### "Foreign key violation" error:
- Check if you're using the correct ID type
- Suppliers should use `user_id` (TEXT)
- Purchasers should use `id` (INTEGER) in supplier_purchaser

### "User not found" error:
- Verify you're querying the correct ID field
- Use `user_id` for text-based lookups
- Use `id` for integer-based lookups

### "Type mismatch" error:
- Ensure TEXT fields get TEXT values
- Ensure INTEGER fields get INTEGER values
- Convert between types when necessary

---

This dual-ID system maintains backward compatibility while providing efficient database operations. Always be aware of which ID type you're using!
