# Database Migration Guide

## Overview

This guide will help you migrate from your current Supabase database to the new database.

---

## New Database Credentials

**Supabase URL:** `https://puoedxxoxyrdlesdghyp.supabase.co`

**API Key (Anon):** `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InB1b2VkeHhveHlyZGxlc2RnaHlwIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE1NzI3NTksImV4cCI6MjA4NzE0ODc1OX0.oZEZjOLwzk4UBMpuGFyQVm3njX4zFH5sEA8WgXiC-tw`

**Service Role Key:** *(You'll need to get this from your Supabase dashboard)*

---

## Migration Steps

### Step 1: Create Database Schema

1. Go to your new Supabase dashboard: https://puoedxxoxyrdlesdghyp.supabase.co
2. Navigate to **SQL Editor**
3. Open the file: `backend/complete_database_setup.sql`
4. Copy the entire contents
5. Paste into the SQL Editor
6. Click **Run**

**Expected Result:**
```
Success. Database setup complete!
```

---

### Step 2: Update Environment Variables

#### Frontend Environment Variables

Update `frontend/.env.local`:

```env
NEXT_PUBLIC_SUPABASE_URL=https://puoedxxoxyrdlesdghyp.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InB1b2VkeHhveHlyZGxlc2RnaHlwIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE1NzI3NTksImV4cCI6MjA4NzE0ODc1OX0.oZEZjOLwzk4UBMpuGFyQVm3njX4zFH5sEA8WgXiC-tw
```

#### Backend Environment Variables

Update `backend/.env.local`:

```env
SUPABASE_URL=https://puoedxxoxyrdlesdghyp.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key_here
```

**To get your Service Role Key:**
1. Go to Supabase Dashboard → Settings → API
2. Copy the `service_role` key (under "Project API keys")
3. Paste it in the `.env.local` file

---

### Step 3: Migrate Existing Data (Optional)

If you have existing data in your old database that needs to be migrated:

#### Option A: Manual Export/Import (Recommended for small datasets)

1. **Export from old database:**
   ```sql
   -- In old database SQL Editor
   COPY (SELECT * FROM users) TO STDOUT WITH CSV HEADER;
   COPY (SELECT * FROM products) TO STDOUT WITH CSV HEADER;
   COPY (SELECT * FROM orders) TO STDOUT WITH CSV HEADER;
   COPY (SELECT * FROM price_history) TO STDOUT WITH CSV HEADER;
   ```

2. **Import to new database:**
   - Use Supabase Dashboard → Table Editor → Import CSV
   - Or use SQL Editor with INSERT statements

#### Option B: Database Dump/Restore (For large datasets)

1. **Export from old database:**
   ```bash
   # Using pg_dump (requires PostgreSQL client)
   pg_dump -h old-db-host -U postgres -d postgres --table=users --table=products --table=orders --table=price_history > backup.sql
   ```

2. **Import to new database:**
   ```bash
   psql -h puoedxxoxyrdlesdghyp.supabase.co -U postgres -d postgres < backup.sql
   ```

#### Option C: Programmatic Migration (Python Script)

Create a migration script:

```python
# migrate_data.py
from supabase import create_client
import os

# Old database
OLD_URL = "old_supabase_url"
OLD_KEY = "old_service_role_key"
old_db = create_client(OLD_URL, OLD_KEY)

# New database
NEW_URL = "https://puoedxxoxyrdlesdghyp.supabase.co"
NEW_KEY = "new_service_role_key"
new_db = create_client(NEW_URL, NEW_KEY)

# Migrate users
print("Migrating users...")
users = old_db.table('users').select('*').execute()
for user in users.data:
    new_db.table('users').insert(user).execute()
print(f"Migrated {len(users.data)} users")

# Migrate products
print("Migrating products...")
products = old_db.table('products').select('*').execute()
for product in products.data:
    new_db.table('products').insert(product).execute()
print(f"Migrated {len(products.data)} products")

# Migrate orders
print("Migrating orders...")
orders = old_db.table('orders').select('*').execute()
for order in orders.data:
    new_db.table('orders').insert(order).execute()
print(f"Migrated {len(orders.data)} orders")

# Migrate price_history
print("Migrating price_history...")
price_history = old_db.table('price_history').select('*').execute()
for entry in price_history.data:
    new_db.table('price_history').insert(entry).execute()
print(f"Migrated {len(price_history.data)} price history entries")

print("Migration complete!")
```

Run the script:
```bash
python migrate_data.py
```

---

### Step 4: Update GitHub Actions Secrets

If you're using GitHub Actions for automated order sync:

1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Update the following secrets:
   - `SUPABASE_URL` = `https://puoedxxoxyrdlesdghyp.supabase.co`
   - `SUPABASE_SERVICE_ROLE_KEY` = `your_new_service_role_key`

---

### Step 5: Test the Migration

#### Test Frontend:

1. Start the frontend:
   ```bash
   cd frontend
   npm run dev
   ```

2. Test key features:
   - [ ] Login functionality
   - [ ] View products
   - [ ] Create new product
   - [ ] View orders
   - [ ] Price change approval workflow
   - [ ] Dashboard statistics

#### Test Backend:

1. Run the sync script manually:
   ```bash
   cd backend
   python main.py
   ```

2. Verify:
   - [ ] Orders are synced from Metabase
   - [ ] Historical prices are calculated correctly
   - [ ] No errors in console

---

### Step 6: Deploy Changes

#### Deploy Frontend:

If using Vercel:
1. Update environment variables in Vercel dashboard
2. Redeploy the application

If using other hosting:
1. Update environment variables
2. Rebuild and redeploy

#### Deploy Backend:

GitHub Actions will automatically use the new secrets on the next scheduled run.

---

## Rollback Plan

If you need to rollback to the old database:

1. Revert environment variables to old database credentials
2. Restart frontend and backend
3. No data loss (old database remains unchanged)

---

## Verification Checklist

After migration, verify the following:

### Database Structure:
- [ ] All tables created successfully
- [ ] All indexes created
- [ ] All constraints in place
- [ ] All views created

### Data Migration (if applicable):
- [ ] All users migrated
- [ ] All products migrated
- [ ] All orders migrated
- [ ] All price history migrated
- [ ] Data integrity verified (counts match)

### Application Functionality:
- [ ] Users can login
- [ ] Products display correctly
- [ ] Orders display correctly
- [ ] Price changes work
- [ ] Approval workflow works
- [ ] Dashboard statistics accurate
- [ ] Search and filters work
- [ ] CSV export works

### Backend Sync:
- [ ] Orders sync from Metabase
- [ ] Historical pricing works
- [ ] No errors in logs

---

## Troubleshooting

### Issue: "relation does not exist" error

**Solution:** Run the database setup script again

### Issue: "permission denied" error

**Solution:** Check that you're using the service role key for backend operations

### Issue: Data not appearing in frontend

**Solution:** 
1. Check browser console for errors
2. Verify environment variables are correct
3. Clear browser cache and cookies
4. Check RLS policies in Supabase dashboard

### Issue: Orders not syncing

**Solution:**
1. Verify backend environment variables
2. Check GitHub Actions logs
3. Run sync script manually to see errors

---

## Support

If you encounter issues during migration:

1. Check the browser console for frontend errors
2. Check backend logs for sync errors
3. Verify all environment variables are correct
4. Ensure service role key has proper permissions
5. Check Supabase dashboard for database errors

---

## Post-Migration Cleanup

After successful migration and verification:

1. **Keep old database for 30 days** (backup period)
2. Monitor new database for any issues
3. After 30 days, you can safely delete the old database
4. Update documentation with new database info

---

## Database Schema Reference

### Core Tables:
- `users` - User accounts (suppliers, purchasers, agents, admins)
- `products` - Product catalog with variants
- `orders` - Orders synced from Metabase
- `price_history` - Price changes and approvals
- `supplier_purchaser` - Purchaser-supplier relationships

### Optional Tables:
- `sessions` - User authentication sessions
- `notifications` - User notifications
- `audit_log` - Audit trail

### Views:
- `product_summary` - Product statistics
- `supplier_order_stats` - Order statistics by supplier
- `pending_price_changes` - Pending price change requests

---

## Estimated Migration Time

- **Database setup:** 5 minutes
- **Environment variable updates:** 5 minutes
- **Data migration (if needed):** 30 minutes - 2 hours (depending on data size)
- **Testing:** 30 minutes
- **Deployment:** 15 minutes

**Total:** 1-3 hours

---

## Success Criteria

Migration is successful when:

✅ All tables created in new database  
✅ Environment variables updated  
✅ Data migrated (if applicable)  
✅ Frontend works correctly  
✅ Backend sync works  
✅ All features tested and working  
✅ No errors in logs  

---

**Good luck with your migration! 🚀**
