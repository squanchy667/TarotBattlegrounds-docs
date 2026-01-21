# Tarot Battlegrounds Documentation

> Comprehensive documentation for Tarot Battlegrounds - A tarot-themed auto-battler game inspired by Hearthstone Battlegrounds

[![Sprint 1](https://img.shields.io/badge/Sprint-1%20Complete-green)]()
[![Unity](https://img.shields.io/badge/Unity-2023%20LTS-black)]()

## 📖 About

This documentation serves as a centralized knowledge base for the Tarot Battlegrounds project, designed for agents, developers, and contributors.

## 🎮 Project Overview

**Tarot Battlegrounds** is a 4-player auto-battler with:
- Tarot card theme (suits as tribes)
- Recruit phase (buy/sell/position cards)
- Combat phase (auto-battles)
- Progression system (tiers unlock over time)

## 📂 Documentation

- **[Developer](developer/)** - Technical documentation
- **[Product](product/)** - Game design and roadmap
- **[Learn](learn/)** - Getting started guides
- **[Resources](resources/)** - Tech stack, issues, changelog

## 🚀 Quick Start

### For Agents
1. [Architecture Overview](developer/architecture.md)
2. [Current Status](resources/changelog.md)
3. [Known Issues](resources/known-issues.md)

### For Developers
1. [Getting Started](learn/getting-started.md)
2. [Setup Guide](developer/setup-guide.md)
3. [Coding Standards](developer/coding-standards.md)

## 📊 Current Status

**Sprint 1: UI System Overhaul** ✅ Complete (January 21, 2026)

### Working Features
- ✅ Buy cards from shop
- ✅ Play cards to board  
- ✅ Sell cards from board
- ✅ Refresh shop (reroll)
- ✅ Upgrade tavern tier
- ✅ Switch between players
- ✅ Combat execution
- ✅ Turn progression
- ✅ Event-driven UI system

### In Progress
- 🔄 Health display fix
- 🔄 Sell from hand
- 🔄 UI polish

## 🏗️ Architecture Highlights

The project uses an **event-driven UI architecture**:

```
Player State Change → Event Fired → UI Subscribes → UI Updates
```

Key events:
- `OnHandChanged`, `OnBoardChanged`, `OnCoinsChanged`
- `OnTierChanged`, `OnHealthChanged`, `OnShopRefreshed`
- `OnCombatLogEntry` for real-time combat display

## 🔗 Links

- Main Repo: [TarotBattlegrounds-POC](https://github.com/squanchy667/TarotBattlegrounds-POC)
- Unity: 2023 LTS
- Network: Mirror (Phase 5)

---

Last Updated: January 21, 2026
