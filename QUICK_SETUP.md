# Quick Setup Guide - New Database

This guide provides a streamlined process to set up your new database.

---

## 🚀 Quick Start (5 Minutes)

### Step 1: Get Your Service Role Key

1. Go to: https://puoedxxoxyrdlesdghyp.supabase.co
2. Navigate to **Settings** → **API**
3. Copy the **service_role** key (under "Project API keys")
4. Keep it handy for the next steps

---

### Step 2: Create Database Schema

1. In Supabase Dashboard, go to **SQL Editor**
2. Click **New Query**
3. Copy the contents of `backend/complete_database_setup.sql`
4. Paste into the editor
5. Click **Run** (or press Ctrl+Enter)

✅ You should see: "Success. Database setup complete!"

---

### Step 3: Update Environment Variables

#### For Frontend:

Create or update `frontend/.env.local`:

```bash
# Copy the template
cp frontend/.env.local.new frontend/.env.local
```

The file should contain:
```env
NEXT_PUBLIC_SUPABASE_URL=https://puoedxxoxyrdlesdghyp.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InB1b2VkeHhveHlyZGxlc2RnaHlwIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE1NzI3NTksImV4cCI6MjA4NzE0ODc1OX0.oZEZjOLwzk4UBMpuGFyQVm3njX4zFH5sEA8WgXiC-tw
```

#### For Backend:

Create or update `backend/.env.local`:

```bash
# Copy the template
cp backend/.env.local.new backend/.env.local
```

Then edit `backend/.env.local` and replace `your_service_role_key_here` with your actual service role key:

```env
SUPABASE_URL=https://puoedxxoxyrdlesdghyp.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your_actual_service_role_key
SUPABASE_KEY=your_actual_service_role_key
```

---

### Step 4: Test the Setup

#### Test Frontend:

```bash
cd frontend
npm install  # If not already installed
npm run dev
```

Open http://localhost:3000 in your browser.

#### Test Backend:

```bash
cd backend
pip install -r requirements.txt  # If not already installed
python main.py
```

You should see orders syncing from Metabase.

---

## 📊 Optional: Migrate Existing Data

If you have data in your old database that needs to be migrated:

### Option 1: Automated Migration (Recommended)

```bash
cd backend
python migrate_database.py
```

Follow the prompts:
1. Enter your OLD Supabase URL
2. Enter your OLD Service Role Key
3. Enter your NEW Service Role Key
4. Confirm migration
5. Wait for completion

### Option 2: Manual Migration

See the full guide in `DATABASE_MIGRATION_GUIDE.md`

---

## ✅ Verification Checklist

After setup, verify:

- [ ] Database schema created (8 tables)
- [ ] Frontend `.env.local` updated
- [ ] Backend `.env.local` updated
- [ ] Frontend runs without errors
- [ ] Backend syncs orders successfully
- [ ] Can login to the application
- [ ] Can view products/orders

---

## 🔧 Update GitHub Actions (For Automated Sync)

1. Go to your GitHub repository
2. **Settings** → **Secrets and variables** → **Actions**
3. Update:
   - `SUPABASE_URL` = `https://puoedxxoxyrdlesdghyp.supabase.co`
   - `SUPABASE_SERVICE_ROLE_KEY` = `your_service_role_key`

---

## 🆘 Troubleshooting

### "Cannot connect to database"
- Verify your service role key is correct
- Check that you're using the right URL

### "Table does not exist"
- Run the database setup script again
- Check SQL Editor for any errors

### "Permission denied"
- Make sure you're using the **service_role** key (not anon key) for backend
- Check RLS policies in Supabase dashboard

### Frontend shows no data
- Clear browser cache
- Check browser console for errors
- Verify environment variables are loaded (restart dev server)

---

## 📚 Additional Resources

- **Full Migration Guide:** `DATABASE_MIGRATION_GUIDE.md`
- **Database Schema:** `backend/complete_database_setup.sql`
- **Project Documentation:** See the detailed document shared earlier

---

## 🎉 You're Done!

Your new database is ready to use. The setup should take about 5-10 minutes.

**Next Steps:**
1. Test all features thoroughly
2. Migrate data if needed
3. Update production environment variables
4. Monitor for any issues

**Need Help?** Check the troubleshooting section or review the full migration guide.
