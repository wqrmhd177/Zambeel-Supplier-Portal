# Database Migration Checklist

Use this checklist to track your migration progress.

---

## 📋 Pre-Migration Checklist

- [ ] Read **NEW_DATABASE_SETUP_SUMMARY.md**
- [ ] Choose migration approach (Quick Setup vs Full Migration)
- [ ] Backup current database (if needed)
- [ ] Get new Service Role Key from Supabase Dashboard
- [ ] Review all files created for migration

---

## 🗄️ Database Setup

- [ ] Open new Supabase Dashboard: https://puoedxxoxyrdlesdghyp.supabase.co
- [ ] Navigate to SQL Editor
- [ ] Copy contents of `backend/complete_database_setup.sql`
- [ ] Paste into SQL Editor
- [ ] Run the script (Ctrl+Enter)
- [ ] Verify success message appears
- [ ] Check that all 8 tables were created
- [ ] Check that all 3 views were created
- [ ] Verify indexes were created (check table details)

---

## 🔧 Environment Variables

### Frontend:
- [ ] Copy `frontend/.env.local.new` to `frontend/.env.local`
- [ ] Verify `NEXT_PUBLIC_SUPABASE_URL` is correct
- [ ] Verify `NEXT_PUBLIC_SUPABASE_ANON_KEY` is correct

### Backend:
- [ ] Copy `backend/.env.local.new` to `backend/.env.local`
- [ ] Update `SUPABASE_URL` with new URL
- [ ] Update `SUPABASE_SERVICE_ROLE_KEY` with your actual key
- [ ] Update `SUPABASE_KEY` with your actual key
- [ ] Verify Metabase URL is still correct

---

## 📊 Data Migration (Optional)

### If Using Automated Script:
- [ ] Navigate to backend folder: `cd backend`
- [ ] Run migration script: `python migrate_database.py`
- [ ] Enter OLD Supabase URL when prompted
- [ ] Enter OLD Service Role Key when prompted
- [ ] Enter NEW Service Role Key when prompted
- [ ] Confirm migration (type 'yes')
- [ ] Wait for completion
- [ ] Review migration summary
- [ ] Check for any errors

### If Migrating Manually:
- [ ] Export users table from old database
- [ ] Import users to new database
- [ ] Export products table from old database
- [ ] Import products to new database
- [ ] Export orders table from old database
- [ ] Import orders to new database
- [ ] Export price_history table from old database
- [ ] Import price_history to new database
- [ ] Export supplier_purchaser table from old database
- [ ] Import supplier_purchaser to new database

---

## 🧪 Testing

### Frontend Testing:
- [ ] Navigate to frontend folder: `cd frontend`
- [ ] Install dependencies (if needed): `npm install`
- [ ] Start dev server: `npm run dev`
- [ ] Open http://localhost:3000 in browser
- [ ] Test login functionality
- [ ] Test dashboard loads correctly
- [ ] Test products page displays
- [ ] Test orders page displays
- [ ] Test product creation
- [ ] Test price change workflow
- [ ] Test approvals page (for agents)
- [ ] Test search and filters
- [ ] Test CSV export
- [ ] Check browser console for errors
- [ ] Verify statistics are accurate

### Backend Testing:
- [ ] Navigate to backend folder: `cd backend`
- [ ] Install dependencies (if needed): `pip install -r requirements.txt`
- [ ] Run sync script: `python main.py`
- [ ] Verify orders are fetched from Metabase
- [ ] Verify historical prices are calculated
- [ ] Check for any errors in console
- [ ] Verify orders appear in database
- [ ] Check that supplier_selling_price is populated

---

## 🔍 Verification

### Database Verification:
- [ ] Check users table has records (if migrated)
- [ ] Check products table has records (if migrated)
- [ ] Check orders table has records
- [ ] Check price_history table structure is correct
- [ ] Verify all indexes exist
- [ ] Verify all constraints are in place
- [ ] Check views return data correctly

### Data Integrity:
- [ ] Compare record counts (old vs new)
- [ ] Spot-check sample records for accuracy
- [ ] Verify relationships between tables
- [ ] Check that no data was corrupted
- [ ] Verify timestamps are preserved
- [ ] Check that IDs are consistent

### Functionality Verification:
- [ ] Users can login successfully
- [ ] Products display with correct prices
- [ ] Orders show historical prices
- [ ] Price changes create pending requests
- [ ] Agents can approve/reject price changes
- [ ] Dashboard statistics are accurate
- [ ] Search functionality works
- [ ] Filters work correctly
- [ ] CSV export works
- [ ] Multi-currency displays correctly

---

## 🚀 Deployment

### Update GitHub Actions:
- [ ] Go to GitHub repository
- [ ] Navigate to Settings → Secrets and variables → Actions
- [ ] Update `SUPABASE_URL` secret
- [ ] Update `SUPABASE_SERVICE_ROLE_KEY` secret
- [ ] Test manual workflow trigger
- [ ] Verify next scheduled run works

### Update Production (if applicable):
- [ ] Update production environment variables
- [ ] Redeploy frontend application
- [ ] Verify production deployment successful
- [ ] Test production site functionality
- [ ] Monitor logs for errors
- [ ] Check that orders sync correctly

---

## 📝 Post-Migration

### Documentation:
- [ ] Update internal documentation with new credentials
- [ ] Document any issues encountered
- [ ] Note any customizations made
- [ ] Update team on migration completion

### Monitoring:
- [ ] Monitor application for 24-48 hours
- [ ] Check error logs daily
- [ ] Verify automated sync runs successfully
- [ ] Monitor database performance
- [ ] Check for any user-reported issues

### Cleanup:
- [ ] Keep old database active for 30 days (backup period)
- [ ] After 30 days, archive old database
- [ ] Remove old environment variables from local machines
- [ ] Update any documentation referencing old database

---

## ✅ Migration Complete

- [ ] All checklist items completed
- [ ] No critical errors in logs
- [ ] All features tested and working
- [ ] Team notified of migration
- [ ] Documentation updated

---

## 🆘 Rollback Plan (If Needed)

If you need to rollback:

- [ ] Stop all applications
- [ ] Revert environment variables to old database
- [ ] Restart frontend and backend
- [ ] Verify old database is still accessible
- [ ] Test functionality with old database
- [ ] Document reason for rollback
- [ ] Plan next migration attempt

---

## 📊 Migration Statistics

**Start Date:** _______________  
**Completion Date:** _______________  
**Duration:** _______________  

**Records Migrated:**
- Users: _______________
- Products: _______________
- Orders: _______________
- Price History: _______________
- Other: _______________

**Issues Encountered:** _______________

**Resolution:** _______________

---

## 📞 Support Contacts

**Technical Issues:**
- Check troubleshooting sections in guides
- Review Supabase documentation
- Check application logs

**Database Issues:**
- Supabase Dashboard: https://puoedxxoxyrdlesdghyp.supabase.co
- Supabase Docs: https://supabase.com/docs

---

## 🎉 Success!

Once all items are checked, your migration is complete!

**Next Steps:**
1. Monitor for 24-48 hours
2. Keep old database for 30 days
3. Update team documentation
4. Celebrate! 🎊

---

**Notes:**

_Use this space to document any specific issues, customizations, or important information about your migration._

_______________________________________________

_______________________________________________

_______________________________________________

_______________________________________________
