# Tarot Battlegrounds Documentation

> Comprehensive documentation for Tarot Battlegrounds — A tarot-themed auto-battler game inspired by Hearthstone Battlegrounds

[![Phase M](https://img.shields.io/badge/Phase-M%20Complete-green)]()
[![Unity](https://img.shields.io/badge/Unity-2022.3.48f1-black)]()
[![Photon](https://img.shields.io/badge/Multiplayer-Photon%20PUN%202-blue)]()
[![Live](https://img.shields.io/badge/Play-Live%20on%20AWS-orange)]()

## About

This documentation serves as a centralized knowledge base for the Tarot Battlegrounds project, designed for agents, developers, and contributors.

## Project Overview

**Tarot Battlegrounds** is a 2-8 player auto-battler featuring:
- Tarot card theme (4 suits as tribes: Pentacles, Cups, Swords, Wands)
- Recruit phase (buy/sell/reroll/freeze/position cards)
- Combat phase (automated turn-based battles)
- 6-tier progression system with triple/golden card mechanics
- Tribe synergies with tiered bonuses and cross-tribe combos
- Online multiplayer via Photon PUN 2
- WebGL deployment on AWS S3 + CloudFront
- DevZone web editor for live game data editing

## Documentation

- **[Architecture](architecture/system-overview.md)** — System overview, data flow, design patterns
- **[Developer](developer/game-systems.md)** — Technical documentation, system guides, setup
- **[Product](product/features.md)** — Features, game design, roadmap
- **[Testing](testing/test-plan.md)** — Test plan and strategies
- **[Learn](learn/getting-started.md)** — Getting started guides
- **[Resources](resources/tech-stack.md)** — Tech stack, changelog, known issues

## Quick Start

### For Agents
1. [System Overview](architecture/system-overview.md)
2. [Game Systems](developer/game-systems.md)
3. [Multiplayer Architecture](developer/multiplayer.md)
4. [Task Board](TASK_BOARD.md)
5. [Known Issues](resources/known-issues.md)

### For Developers
1. [Getting Started](learn/getting-started.md)
2. [Setup Guide](developer/setup-guide.md)
3. [Coding Standards](developer/coding-standards.md)
4. [Deployment Guide](developer/deployment.md)

## Current Status

**Production Audit Complete** — February 24, 2026 | Branch: `tarot-skin`
**Upgrade Phases II-VII**: DONE (150/192 tasks)
**Quality Gate**: 240 unit tests, 3 audit rounds (38 bugs fixed), triple-agent verification

### What to Expect

This is a **fully playable single-player POC** against AI opponents. The game loop is complete: recruit cards, build synergies, fight auto-battles, upgrade your tavern, and climb toward victory.

**What works well:**
- Complete recruit-phase economy (buy/sell/reroll/freeze/upgrade)
- 91 cards across 6 tribes with 16 ability types
- 12 hero powers with unique playstyles
- Combat with Windfury, Venomous, Reborn, Aegis, Cleave, Deathrattle, Aura
- Triple fusion into golden cards with discovery rewards
- 3 AI difficulty levels (Easy/Medium/Hard)
- 2-8 player game configurations
- WebGL deployment on AWS (play in browser)

**Known limitations (see [Known Issues](resources/known-issues.md)):**
- Online multiplayer infrastructure is scaffolded but not production-ready (Phase I endpoints exist but need deployment)
- Some synergy triggers (OnBuy, Passive) are defined but not wired to call sites
- AI does not use hero powers
- Balance is functional but not tournament-ready (synergy stacking favors Stars/Swords)
- Some spec mismatches between PLAN.md and implementation (Cups T6, Swords T6, Wands T2)

### Working Features
- Buy/sell/reroll cards from shop
- Play cards to board, sell from board or hand
- Refresh and freeze shop
- Upgrade tavern tier (1-6)
- 2-8 player games (configurable)
- AI opponents (Easy/Medium/Hard)
- Triple fusion: 3 copies = golden card + discovery
- Board reordering (click-to-swap)
- 6 tribes: Swords, Cups, Wands, Pentacles, Stars, Coins
- Tribe synergies with tiered thresholds (2/4/6) + cross-tribe combos
- 16 ability types (Battlecry, Deathrattle, OnAttack, OnAllyDeath, OnAllySummoned, OnSell, Aura, Reborn, Windfury, Venomous, SummonToken, StealBuff, GainArmor, BuffAllTribes, RandomTransform, Taunt)
- 12 hero powers (Midas Touch, Empower, Healer, Arcane Bolt, Fortify, Reroller, Life Tap, Blade Master, War Chief, Tactician, Economist, Recruiter)
- Combat animation + VFX system with replay
- Event-driven UI with card tooltips, hover zoom, drag-and-drop
- Game Over screen with standings
- Online multiplayer scaffolding via Photon PUN 2
- WebGL build deployed to AWS S3 + CloudFront
- Runtime JSON data loading from S3 (with built-in fallback)
- DevZone web editor (React + Express + AWS Lambda)

### Test Coverage (240 tests)
- **AbilityTests.cs**: 90 tests across 16 abilities
- **HeroPowerTests.cs**: 118 tests across 12 hero powers
- **SynergyTests.cs**: 65 tests (4 original tribes + Stars + Coins)
- **CombatTests.cs**: 17 tests (combat, deathrattle cascade, golden cards)
- **AIBattleTests.cs**: 15+ tests (game completion, balance, difficulty)
- **CardSystemTests.cs, EconomyTests.cs, EdgeCaseTests.cs**: Core system tests

### Architecture Highlights

```
Player State Change -> Event Fired -> UI Subscribes -> UI Updates
```

Key events: `OnHandChanged`, `OnBoardChanged`, `OnCoinsChanged`, `OnTierChanged`, `OnHealthChanged`, `OnShopRefreshed`, `OnTripleDiscovery`

```
[DevZone Web Editor] -> [Express API on Lambda] -> [S3: game data JSON]
                                                         |
[Unity WebGL Game]  <---- fetches cards.json, synergies.json, config.json at startup
```

## Repos

| Repo | Description |
|------|-------------|
| TarotBattlegrounds-POC | Main Unity game project |
| TarotBattlegrounds-docs | This documentation repo |
| tarot-devzone | Web-based game data editor (React + Express) |

## Tech Stack

- **Engine**: Unity 2022.3.48f1
- **Language**: C#
- **Networking**: Photon PUN 2 (WebSocketSecure for WebGL)
- **Hosting**: AWS S3 + CloudFront (~$0.15/mo)
- **DevZone**: React + Vite + Tailwind (frontend), Express + Lambda (backend)
- **Data**: DynamoDB (users/versions), S3 (game JSON + card images)

---

Last Updated: February 24, 2026
