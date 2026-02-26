---
name: Price History & Approval System Implementation
overview: ""
todos:
  - id: 034f61de-a6b6-4680-8fe3-1dc585d83adf
    content: Create price_history table SQL script with proper indexes and constraints
    status: pending
  - id: 3e3ddf9b-fb9c-46ce-b321-738fcb8c93bb
    content: Create approval_requests table SQL script for tracking price/delist requests
    status: pending
  - id: 10d83684-3a91-4422-91fa-011ce7a9cc71
    content: Update products table to replace 'inactive' with 'delisted' status
    status: pending
  - id: 5b65bb51-8237-412d-a691-75d08d3af548
    content: Add order_amount column to orders table for historical pricing
    status: pending
  - id: fe076dc0-f6c6-4a0c-a719-1e750527bf06
    content: Create database trigger to automatically manage price history entries
    status: pending
  - id: 116467ce-6204-4bd1-928a-404d17b6c94d
    content: Create Python script to initialize price history for existing products
    status: pending
  - id: c2a19134-ea90-4ce9-82dc-a46782307c80
    content: Modify backend sync script to populate order_amount from price history
    status: pending
  - id: 9ae6fe15-2a94-4f12-bffb-d36f252473fa
    content: Create frontend helper functions for price history operations
    status: pending
  - id: 162d9206-e438-41dd-b5ab-1eca800680a0
    content: Create frontend helper functions for approval request operations
    status: pending
  - id: 967599dd-7665-4f26-95e3-b34fe16e191c
    content: Modify product edit page to create approval requests for price changes
    status: pending
  - id: 9b781783-233a-4baa-aa11-0cfb4652135b
    content: Add delist request button and logic to product edit page
    status: pending
  - id: 55939f67-b953-48c3-bdbe-2140c268b8a0
    content: Update products page to show 'delisted' instead of 'inactive'
    status: pending
  - id: 3e42fa62-c24b-45f3-9f96-41a8c879a092
    content: Update listings page to display delisted products with badge
    status: pending
  - id: d1fd7f28-bae1-4ae2-83a3-8a385cc365ce
    content: Create new approvals page for listing team to review requests
    status: pending
  - id: 4f876b51-912d-4558-8df8-5df8d50dbe1d
    content: Add approvals menu item to sidebar for listing role
    status: pending
  - id: b5076ade-3124-4c24-ab1f-cc3dad10dd55
    content: Update orders page to display order_amount instead of total_payable
    status: pending
  - id: f892788b-572e-44fd-9924-61d8a1e1a7b4
    content: Test complete price change approval workflow end-to-end
    status: pending
  - id: b91b6648-8aca-4c87-8dea-6226d94d8a70
    content: Test complete delist approval workflow end-to-end
    status: pending
---

# Price History & Approval System Implementation

## Overview

Implement a comprehensive price history tracking system that records product price changes over time, enabling orders to display the correct supplier price at the time of order placement. Add an approval workflow for price changes and product delisting requests that require listing team approval before taking effect.

## Database Changes

### 1. Create Price History Table

**File:** `backend/create_price_history_table.sql`

Create a new table to track all price changes:

- `id` (BIGSERIAL PRIMARY KEY)
- `variant_id` (BIGINT, references products.variant_id)
- `product_id` (BIGINT, references products.product_id)
- `price` (DECIMAL(10, 2)) - the supplier selling price
- `effective_from` (TIMESTAMPTZ) - when this price became active
- `effective_to` (TIMESTAMPTZ, nullable) - when this price stopped being active (NULL for current price)
- `created_by` (TEXT) - user_id who made the change
- `created_at` (TIMESTAMPTZ)

Indexes:

- `idx_price_history_variant_id`
- `idx_price_history_product_id`
- `idx_price_history_effective_dates` (for date range queries)

### 2. Create Approval Requests Table

**File:** `backend/create_approval_requests_table.sql`

Create a table to track approval requests:

- `id` (BIGSERIAL PRIMARY KEY)
- `request_type` (TEXT) - 'price_change' or 'delist'
- `product_id` (BIGINT)
- `variant_id` (BIGINT, nullable)
- `requested_by` (TEXT) - user_id of requester
- `requested_at` (TIMESTAMPTZ)
- `status` (TEXT) - 'pending', 'approved', 'rejected'
- `reviewed_by` (TEXT, nullable) - user_id of listing team member
- `reviewed_at` (TIMESTAMPTZ, nullable)
- `review_notes` (TEXT, nullable)
- `old_value` (JSONB) - stores old price or status
- `new_value` (JSONB) - stores new price or status
- `metadata` (JSONB) - additional context (product title, etc.)

Indexes:

- `idx_approval_requests_status`
- `idx_approval_requests_requested_by`
- `idx_approval_requests_product_id`

### 3. Update Products Table Status Values

**File:** `backend/update_products_status.sql`

- Update existing products with `status = 'inactive'` to `status = 'delisted'`
- Add check constraint to ensure status is one of: 'active', 'delisted'

### 4. Add Order Amount Column to Orders Table

**File:** `backend/add_order_amount_to_orders.sql`

- Add `order_amount` (DECIMAL(10, 2)) column to orders table
- This will store the supplier selling price at the time of order
- Update existing orders to populate this field (if possible from Metabase data)

## Backend Changes

### 5. Update Orders Sync Script

**File:** `backend/main.py`

Modify the sync script to:

- When syncing orders, look up the product price at the time of `order_date`
- Query `price_history` table to find the price that was effective at `order_date`
- If no price history exists for that date, use current `variant_selling_price` from products table
- Store the calculated price in `order_amount` column
- Match orders to products using `sku` = `company_sku`

Logic:

```python
def get_price_at_date(sku, order_date):
    # Query price_history for variant with company_sku = sku
    # WHERE effective_from <= order_date AND (effective_to IS NULL OR effective_to > order_date)
    # If found, return price
    # Else, return current variant_selling_price from products table
```

### 6. Create Price History Initialization Script

**File:** `backend/initialize_price_history.py`

One-time script to:

- Fetch all products with their current prices
- Create initial price history entries with `effective_from = created_at` (or current timestamp)
- Set `effective_to = NULL` (current price)

## Frontend Changes

### 7. Update Product Edit Page - Price Change Approval

**File:** `frontend/src/app/products/edit/[id]/page.tsx`

When a price is changed:

- Detect if `variant_selling_price` has changed from the original value
- If changed, create an approval request instead of directly updating the product
- Show message: "Price change submitted for approval. The listing team will review your request."
- Store the request in `approval_requests` table with status 'pending'
- Do NOT update the product price immediately

### 8. Update Product Edit Page - Delist Functionality

**File:** `frontend/src/app/products/edit/[id]/page.tsx`

Add "Request Delist" button:

- When clicked, create an approval request with type 'delist'
- Store current status as `old_value` and 'delisted' as `new_value`
- Show message: "Delist request submitted for approval."
- Do NOT change product status immediately

### 9. Update Products Page - Replace Inactive with Delisted

**File:** `frontend/src/app/products/page.tsx`

- Change status badge from "Inactive" to "Delisted"
- Update filter logic to show 'delisted' products
- Products with status 'delisted' should still appear in the supplier's product list but with a clear "Delisted" badge

### 10. Update Listings Page - Show Delisted Products

**File:** `frontend/src/app/listings/page.tsx`

- Show products with status 'delisted' with a distinct badge
- Agents can still view delisted products but they're clearly marked

### 11. Create Approvals Page for Listing Team

**File:** `frontend/src/app/approvals/page.tsx`

New page accessible only to users with role 'listing':

- Shows all pending approval requests in a table
- Columns: Request Type, Product Title, Requested By, Requested Date, Old Value, New Value, Actions
- For price changes: show old price → new price
- For delist: show "Active → Delisted"
- Actions: "Approve" and "Reject" buttons
- Add optional review notes field
- Filter options: All / Pending / Approved / Rejected

When "Approve" is clicked:

- Update `approval_requests` table: set status = 'approved', reviewed_by, reviewed_at
- Apply the change:
  - For price changes: Update product price AND create price history entry (close old entry with effective_to, create new entry)
  - For delist: Update product status to 'delisted'
- Show success message

When "Reject" is clicked:

- Update `approval_requests` table: set status = 'rejected', reviewed_by, reviewed_at, review_notes
- Do NOT apply any changes to the product
- Show success message

### 12. Add Approvals to Sidebar Navigation

**File:** `frontend/src/components/Sidebar.tsx`

- Add "Approvals" menu item for users with role 'listing'
- Icon: CheckCircle or ClipboardCheck
- Path: `/approvals`

### 13. Update Orders Page - Show Order Amount

**File:** `frontend/src/app/orders/page.tsx`

- Replace `total_payable` with `order_amount` in the display
- Column header: "Supplier Price (PKR)"
- This shows the price the supplier was selling at when the order was placed
- Keep `total_payable` as a separate column if needed (customer's total payment)

### 14. Create Price History Trigger Function

**File:** `backend/create_price_history_trigger.sql`

Create a database trigger/function:

- When a product price is updated (after approval), automatically create price history entry
- Close previous price history entry by setting `effective_to = NOW()`
- Create new price history entry with `effective_from = NOW()`, `effective_to = NULL`

## Helper Functions

### 15. Create Price History Helper

**File:** `frontend/src/lib/priceHistoryHelpers.ts`

Functions:

- `getPriceAtDate(variantId, date)` - fetch price from price_history for a specific date
- `getCurrentPrice(variantId)` - fetch current price (effective_to IS NULL)
- `createPriceHistoryEntry(variantId, price, effectiveFrom, createdBy)` - create new entry
- `closePriceHistoryEntry(variantId, effectiveTo)` - close current entry

### 16. Create Approval Request Helper

**File:** `frontend/src/lib/approvalHelpers.ts`

Functions:

- `createPriceChangeRequest(productId, variantId, oldPrice, newPrice, requestedBy)` - create approval request
- `createDelistRequest(productId, requestedBy)` - create delist approval request
- `approveRequest(requestId, reviewedBy, notes)` - approve and apply changes
- `rejectRequest(requestId, reviewedBy, notes)` - reject request
- `fetchPendingRequests()` - get all pending requests for listing team

## Testing & Validation

### Key Test Cases:

1. Create a product with initial price → verify price history entry created
2. Request price change → verify approval request created, price NOT changed immediately
3. Listing team approves price change → verify price updated, price history updated
4. Place order → verify order_amount reflects current price
5. Change price again → place new order → verify old orders show old price, new orders show new price
6. Request delist → verify approval request created, status NOT changed immediately
7. Listing team approves delist → verify product status changed to 'delisted'
8. View delisted product in listings → verify it appears with "Delisted" badge

## Migration Strategy

1. Run SQL scripts in order:

   - `create_price_history_table.sql`
   - `create_approval_requests_table.sql`
   - `update_products_status.sql`
   - `add_order_amount_to_orders.sql`
   - `create_price_history_trigger.sql`

2. Run initialization script:

   - `python initialize_price_history.py` (creates initial price history for all existing products)

3. Deploy frontend changes (all pages updated together)

4. Update backend sync script to populate order_amount

5. Test thoroughly in staging environment before production deployment

## Notes

- Price history is append-only (never delete entries)
- All price changes must go through approval workflow
- Orders sync script will backfill `order_amount` for historical orders using price history
- If no price history exists for an order date, use current price as fallback
- Delisted products remain visible to suppliers and listing team but not to customers (future feature)