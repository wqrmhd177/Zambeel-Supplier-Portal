# Zambeel Supplier Portal

A comprehensive B2B supplier management platform built with Next.js, Python, and Supabase.

## 📋 Overview

The Supplier Portal is a full-stack application that enables suppliers to manage their products, track orders, and handle pricing workflows with an integrated approval system.

## ✨ Features

- **Multi-Role User Management** - Suppliers, Purchasers, Agents, and Admins
- **Product Management** - Create and manage products with multiple variants
- **Order Tracking** - Real-time order synchronization from Metabase
- **Price Change Approval Workflow** - Agents approve/reject supplier price changes
- **Multi-Currency Support** - USD, PKR, AED, EUR, GBP, and more
- **Dashboard & Analytics** - Real-time statistics and revenue charts
- **Historical Pricing** - Track price changes over time

## 🏗️ Architecture

### Frontend
- **Framework:** Next.js 14
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **Charts:** Recharts
- **Database Client:** Supabase JS

### Backend
- **Language:** Python 3.8+
- **Database:** Supabase (PostgreSQL)
- **Data Sync:** Metabase API integration
- **Automation:** GitHub Actions (hourly sync)

## 📊 Database Structure

**4 Core Tables:**
1. **users** - User accounts with purchaser relationships
2. **products** - Product catalog with variants
3. **orders** - Orders synced from Metabase
4. **price_history** - Price changes and approval workflow

See `FINAL_DATABASE_STRUCTURE.md` for detailed schema information.

## 🚀 Getting Started

### Prerequisites

- Node.js 18.x or higher
- Python 3.8 or higher
- Supabase account

### Installation

#### 1. Clone the repository

```bash
git clone https://github.com/wqrmhd177/supplier-portal.git
cd supplier-portal
```

#### 2. Setup Database

```bash
# Run the SQL setup script in Supabase SQL Editor
# File: backend/complete_database_setup.sql
```

#### 3. Configure Environment Variables

**Frontend:**
```bash
cd frontend
cp .env.local.new .env.local
# Edit .env.local with your Supabase credentials
```

**Backend:**
```bash
cd backend
cp .env.local.new .env.local
# Edit .env.local with your Supabase service role key
```

#### 4. Install Dependencies

**Frontend:**
```bash
cd frontend
npm install
```

**Backend:**
```bash
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

#### 5. Run the Application

**Frontend:**
```bash
cd frontend
npm run dev
```
Access at: http://localhost:3000

**Backend (Order Sync):**
```bash
cd backend
python main.py
```

## 📁 Project Structure

```
supplier-portal/
├── frontend/               # Next.js frontend application
│   ├── src/
│   │   ├── app/           # Next.js app router pages
│   │   ├── components/    # React components
│   │   ├── lib/           # Utility functions and helpers
│   │   └── hooks/         # Custom React hooks
│   └── package.json
│
├── backend/               # Python backend scripts
│   ├── main.py           # Order sync script
│   ├── complete_database_setup.sql
│   ├── migrate_database.py
│   └── requirements.txt
│
├── FINAL_DATABASE_STRUCTURE.md
├── DATABASE_MIGRATION_GUIDE.md
├── QUICK_SETUP.md
└── README.md
```

## 🔐 Environment Variables

### Frontend (.env.local)
```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```

### Backend (.env.local)
```env
SUPABASE_URL=your_supabase_url
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
```

## 🔄 Automated Order Sync

The project includes GitHub Actions workflow for automated order synchronization:

- **Frequency:** Every hour
- **Source:** Metabase API
- **Destination:** Supabase database

### Setup GitHub Actions

1. Go to Repository Settings → Secrets and variables → Actions
2. Add secrets:
   - `SUPABASE_URL`
   - `SUPABASE_SERVICE_ROLE_KEY`

## 👥 User Roles

- **Supplier** - Manage products, view orders, request price changes
- **Purchaser** - View products from assigned suppliers, view orders
- **Agent** - Approve/reject price change requests
- **Admin** - Full access to all features

## 📖 Documentation

- **`FINAL_DATABASE_STRUCTURE.md`** - Complete database schema
- **`DATABASE_MIGRATION_GUIDE.md`** - Migration instructions
- **`QUICK_SETUP.md`** - Fast setup guide
- **`GITHUB_SETUP.md`** - GitHub deployment guide
- **`DATABASE_ID_STRUCTURE.md`** - ID system explanation

## 🛠️ Development

### Available Scripts

**Frontend:**
```bash
npm run dev      # Start development server
npm run build    # Build for production
npm start        # Start production server
npm run lint     # Run ESLint
```

**Backend:**
```bash
python main.py   # Run order sync
python migrate_database.py  # Migrate data
```

## 🧪 Testing

After setup, verify:

- [ ] Database has 4 tables (users, products, orders, price_history)
- [ ] Frontend connects to Supabase
- [ ] Backend syncs orders successfully
- [ ] Login functionality works
- [ ] Products display correctly
- [ ] Price approval workflow functions

## 🚢 Deployment

### Frontend (Vercel)

1. Connect repository to Vercel
2. Add environment variables
3. Deploy

### Backend (GitHub Actions)

Automatically runs via GitHub Actions workflow.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is proprietary and confidential.

## 📞 Support

For issues or questions:
- Check documentation in the repository
- Review troubleshooting sections in guides
- Contact the development team

## 🙏 Acknowledgments

- Built for Zambeel
- Powered by Supabase
- Data integration with Metabase

---

**Version:** 1.0.0  
**Last Updated:** February 2026
