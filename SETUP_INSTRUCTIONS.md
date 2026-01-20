# TarotBattlegrounds-docs Setup Instructions

## 📦 What You Have

A complete documentation repository structure based on AlephFi/docs with:
- ✅ 25+ markdown files covering all aspects of the project
- ✅ Developer documentation (architecture, systems, guides)
- ✅ Product documentation (game design, roadmap)
- ✅ Learning resources and references
- ✅ Git setup scripts for easy initialization

---

## 🚀 Quick Start (5 minutes)

### Step 1: Create GitHub Repository

1. Go to **https://github.com/new**
2. Fill in:
   - **Repository name**: `TarotBattlegrounds-docs`
   - **Description**: `Documentation repository for Tarot Battlegrounds - A tarot-themed auto-battler game`
   - **Visibility**: **Public** (recommended) or Private
   - **DO NOT** check "Add a README file"
   - **DO NOT** add .gitignore or license (we have them)
3. Click **"Create repository"**

---

### Step 2: Initialize and Push (Choose Your OS)

#### 🐧 Linux / 🍎 Mac

```bash
# Navigate to the docs folder
cd TarotBattlegrounds-docs

# Run the setup script
./setup-docs.sh

# Connect to your GitHub repo
git remote add origin https://github.com/squanchy667/TarotBattlegrounds-docs.git

# Push to GitHub
git branch -M main
git push -u origin main
```

#### 🪟 Windows

```batch
REM Navigate to the docs folder
cd TarotBattlegrounds-docs

REM Run the setup script
setup-docs.bat

REM Connect to your GitHub repo
git remote add origin https://github.com/squanchy667/TarotBattlegrounds-docs.git

REM Push to GitHub
git branch -M main
git push -u origin main
```

---

### Step 3: Verify

1. Go to **https://github.com/squanchy667/TarotBattlegrounds-docs**
2. You should see:
   - 📁 All folders (developer, product, learn, resources)
   - 📄 README.md displaying
   - 📄 SUMMARY.md for GitBook navigation

---

## 🌐 Optional: Enable GitHub Pages (Web Hosting)

Make your docs accessible as a website!

1. Go to your repository on GitHub
2. Click **Settings** → **Pages** (left sidebar)
3. Under **"Source"**:
   - Select branch: **main**
   - Select folder: **/ (root)**
4. Click **Save**
5. Wait 1-2 minutes
6. Your docs will be live at: `https://squanchy667.github.io/TarotBattlegrounds-docs/`

---

## 📚 Optional: GitBook Integration

For a beautiful documentation website:

1. Go to **https://www.gitbook.com/**
2. Sign up / Log in
3. **Import from GitHub**:
   - Select `squanchy667/TarotBattlegrounds-docs`
4. GitBook will automatically use `SUMMARY.md` for navigation
5. Your docs will have a professional, searchable interface

---

## 📂 Repository Structure

```
TarotBattlegrounds-docs/
├── .gitbook/
│   └── assets/              # Images and assets
├── developer/               # Technical documentation
│   ├── architecture.md
│   ├── card-system.md
│   ├── tavern-system.md
│   ├── combat-system.md
│   ├── game-manager.md
│   ├── coding-standards.md
│   └── setup-guide.md
├── product/                 # Game design docs
│   ├── game-design/
│   │   ├── core-gameplay.md
│   │   ├── tarot-mechanics.md
│   │   ├── economy.md
│   │   └── combat-rules.md
│   └── roadmap/
│       ├── phase-3-combat.md
│       ├── phase-4-ui.md
│       └── phase-5-multiplayer.md
├── learn/                   # Getting started guides
│   ├── getting-started.md
│   ├── unity-basics.md
│   └── tarot-primer.md
├── resources/               # Reference materials
│   ├── tech-stack.md
│   ├── known-issues.md
│   ├── changelog.md
│   └── references.md
├── README.md                # Main overview
├── SUMMARY.md               # GitBook navigation
├── setup-docs.sh            # Linux/Mac setup script
├── setup-docs.bat           # Windows setup script
└── SETUP_INSTRUCTIONS.md    # This file
```

---

## 🔄 Keeping Docs Updated

### After Completing a Feature

```bash
# Edit the relevant docs
nano developer/card-system.md

# Stage and commit
git add developer/card-system.md
git commit -m "docs: Update card system with new abilities"

# Push to GitHub
git push
```

### After Completing a Phase

```bash
# Update roadmap
nano product/roadmap/phase-3-combat.md

# Update changelog
nano resources/changelog.md

# Update known issues
nano resources/known-issues.md

# Commit all changes
git add .
git commit -m "docs: Phase 3 complete - Combat system finalized"
git push
```

---

## 🤖 For Agents

### Quick Reference Files

When an agent needs to understand the project, direct them to:

1. **Architecture**: `developer/architecture.md`
2. **Current Work**: `product/roadmap/phase-3-combat.md`
3. **Known Issues**: `resources/known-issues.md`

### Agent Workflow

```markdown
User: "Help me add a new card to the game"

Agent:
1. Read: developer/card-system.md
2. Read: developer/architecture.md
3. Provide solution based on docs
```

---

## 🎯 Benefits

### For You
- ✅ Single source of truth
- ✅ Easy onboarding for contributors
- ✅ Clear project status tracking
- ✅ Professional presentation

### For Agents
- ✅ Fast context loading (no full codebase needed)
- ✅ Structured information retrieval
- ✅ Clear architectural understanding
- ✅ Accurate answers without hallucination

### For Future Contributors
- ✅ Comprehensive getting started guide
- ✅ Clear coding standards
- ✅ Documented design decisions
- ✅ Known issues and solutions

---

## ❓ Troubleshooting

### "fatal: not a git repository"
Run the setup script first: `./setup-docs.sh` or `setup-docs.bat`

### "Permission denied" on setup script
```bash
chmod +x setup-docs.sh
./setup-docs.sh
```

### "remote origin already exists"
```bash
git remote remove origin
git remote add origin https://github.com/squanchy667/TarotBattlegrounds-docs.git
```

### Can't push to GitHub
Make sure you're authenticated:
```bash
# Check remote
git remote -v

# If using HTTPS, you may need a Personal Access Token
# Go to: GitHub Settings → Developer settings → Personal access tokens
```

---

## 📞 Need Help?

- **GitHub Issues**: Create an issue in the main repo
- **Email**: [Your email if you want to add it]
- **Discord**: [Your Discord if you have a server]

---

## 🎉 You're All Set!

Your documentation is now:
- ✅ Version controlled on GitHub
- ✅ Accessible to agents and contributors
- ✅ Ready for GitBook or GitHub Pages
- ✅ Easy to maintain and update

**Next Steps**:
1. ✨ Keep docs updated as you develop
2. 🔗 Link this repo in your main project README
3. 🤖 Use this as a reference for agent interactions
4. 📢 Share with contributors

---

**Happy Documenting! 📚🃏✨**
