# GitHub Setup Guide

## 📋 Overview

This guide will help you push the Supplier Portal project to GitHub.

**Your GitHub Username:** `wqrmhd177`

---

## 🚀 Quick Setup Commands

### Step 1: Initialize Git Repository

Open terminal/PowerShell in the project root directory and run:

```bash
# Navigate to project directory
cd "C:\Users\Admin\Downloads\Supplier Portal\Supplier Portal"

# Initialize git repository
git init

# Add all files to staging
git add .

# Create initial commit
git commit -m "Initial commit: Supplier Portal with database migration setup"
```

---

### Step 2: Create Repository on GitHub

1. Go to: https://github.com/new
2. Repository name: `supplier-portal` (or your preferred name)
3. Description: `Zambeel Supplier Portal - B2B platform for supplier management`
4. Choose: **Private** (recommended) or Public
5. **DO NOT** initialize with README, .gitignore, or license
6. Click **Create repository**

---

### Step 3: Connect to GitHub and Push

After creating the repository on GitHub, run these commands:

```bash
# Add remote repository (replace 'supplier-portal' with your repo name if different)
git remote add origin https://github.com/wqrmhd177/supplier-portal.git

# Rename branch to main (if needed)
git branch -M main

# Push to GitHub
git push -u origin main
```

---

## 🔐 Authentication Options

### Option A: Personal Access Token (Recommended)

1. Go to: https://github.com/settings/tokens
2. Click **Generate new token** → **Generate new token (classic)**
3. Name: `Supplier Portal`
4. Expiration: Choose duration (90 days recommended)
5. Select scopes: ✅ **repo** (all sub-options)
6. Click **Generate token**
7. **Copy the token immediately** (you won't see it again!)

When pushing, use:
- Username: `wqrmhd177`
- Password: `your_personal_access_token`

### Option B: GitHub CLI (Alternative)

```bash
# Install GitHub CLI (if not installed)
# Download from: https://cli.github.com/

# Authenticate
gh auth login

# Create and push repository
gh repo create supplier-portal --private --source=. --remote=origin --push
```

---

## 📝 Complete Step-by-Step Commands

```bash
# 1. Navigate to project directory
cd "C:\Users\Admin\Downloads\Supplier Portal\Supplier Portal"

# 2. Initialize git
git init

# 3. Configure git (if not already configured)
git config user.name "wqrmhd177"
git config user.email "your-email@example.com"

# 4. Add all files
git add .

# 5. Create initial commit
git commit -m "Initial commit: Supplier Portal with database migration setup"

# 6. Add remote (after creating repo on GitHub)
git remote add origin https://github.com/wqrmhd177/supplier-portal.git

# 7. Rename branch to main
git branch -M main

# 8. Push to GitHub
git push -u origin main
```

---

## 🔒 Important: Protect Sensitive Files

Before pushing, ensure sensitive files are not included:

### Create `.gitignore` file:

```bash
# Create .gitignore in project root
echo "# Environment variables
.env
.env.local
.env.production
*.env

# Dependencies
node_modules/
venv/
__pycache__/
*.pyc

# Build outputs
.next/
dist/
build/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*

# Database
*.db
*.sqlite

# Secrets
secrets/
credentials.json" > .gitignore
```

Then run:

```bash
# Add .gitignore
git add .gitignore
git commit -m "Add .gitignore for sensitive files"
git push
```

---

## 🔐 Secure Your Repository

### 1. Add GitHub Secrets (for GitHub Actions)

Go to: `https://github.com/wqrmhd177/supplier-portal/settings/secrets/actions`

Add these secrets:
- `SUPABASE_URL` = `https://puoedxxoxyrdlesdghyp.supabase.co`
- `SUPABASE_SERVICE_ROLE_KEY` = `your_service_role_key`

### 2. Remove Sensitive Data from Git History (if accidentally committed)

```bash
# If you accidentally committed .env files, remove them:
git rm --cached .env.local
git rm --cached frontend/.env.local
git rm --cached backend/.env.local
git commit -m "Remove sensitive environment files"
git push
```

---

## 📦 Repository Structure

Your repository will include:

```
supplier-portal/
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── .env.local.new (template)
├── backend/
│   ├── main.py
│   ├── complete_database_setup.sql
│   ├── migrate_database.py
│   ├── requirements.txt
│   └── .env.local.new (template)
├── .gitignore
├── README_DATABASE_MIGRATION.md
├── FINAL_DATABASE_STRUCTURE.md
├── DATABASE_MIGRATION_GUIDE.md
├── QUICK_SETUP.md
└── ... (other documentation files)
```

---

## 🔄 Future Updates

After initial push, use these commands for updates:

```bash
# Check status
git status

# Add changes
git add .

# Commit changes
git commit -m "Description of changes"

# Push to GitHub
git push
```

---

## 🌿 Branch Management (Optional)

For feature development:

```bash
# Create new branch
git checkout -b feature/new-feature

# Make changes and commit
git add .
git commit -m "Add new feature"

# Push branch
git push -u origin feature/new-feature

# Create pull request on GitHub
# Then merge via GitHub UI
```

---

## 🛠️ Troubleshooting

### Issue: "remote origin already exists"

```bash
# Remove existing remote
git remote remove origin

# Add correct remote
git remote add origin https://github.com/wqrmhd177/supplier-portal.git
```

### Issue: "failed to push some refs"

```bash
# Pull first (if repository has content)
git pull origin main --allow-unrelated-histories

# Then push
git push -u origin main
```

### Issue: Authentication failed

```bash
# Use Personal Access Token instead of password
# Or use GitHub CLI:
gh auth login
```

### Issue: Large files error

```bash
# If you have large files, use Git LFS
git lfs install
git lfs track "*.pdf"
git lfs track "*.zip"
git add .gitattributes
git commit -m "Add Git LFS tracking"
```

---

## 📋 Checklist

Before pushing:

- [ ] Created `.gitignore` file
- [ ] Removed `.env.local` files from git (keep `.env.local.new` templates)
- [ ] Verified no sensitive data in code
- [ ] Created repository on GitHub
- [ ] Configured git user name and email
- [ ] Generated Personal Access Token (if needed)
- [ ] Ready to push!

After pushing:

- [ ] Verified repository is private (if needed)
- [ ] Added GitHub Secrets for Actions
- [ ] Updated README with project info
- [ ] Invited collaborators (if any)
- [ ] Set up branch protection rules (optional)

---

## 🎯 Quick Command Summary

```bash
# One-time setup
cd "C:\Users\Admin\Downloads\Supplier Portal\Supplier Portal"
git init
git add .
git commit -m "Initial commit: Supplier Portal"
git remote add origin https://github.com/wqrmhd177/supplier-portal.git
git branch -M main
git push -u origin main

# Future updates
git add .
git commit -m "Your commit message"
git push
```

---

## 📞 Need Help?

- GitHub Docs: https://docs.github.com/
- Git Docs: https://git-scm.com/doc
- GitHub CLI: https://cli.github.com/manual/

---

## 🎉 You're Done!

Your project will be available at:
`https://github.com/wqrmhd177/supplier-portal`

---

**Note:** Remember to keep your `.env.local` files and Personal Access Token secure and never commit them to the repository!
