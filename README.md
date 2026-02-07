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

- **[Developer](developer/)** — Technical documentation, architecture, system guides
- **[Product](product/)** — Game design, economy, roadmap
- **[Learn](learn/)** — Getting started guides
- **[Resources](resources/)** — Tech stack, changelog, known issues

## Quick Start

### For Agents
1. [Architecture Overview](developer/architecture.md)
2. [Multiplayer Architecture](developer/multiplayer.md)
3. [DevZone Editor](developer/devzone.md)
4. [Known Issues](resources/known-issues.md)

### For Developers
1. [Getting Started](learn/getting-started.md)
2. [Setup Guide](developer/setup-guide.md)
3. [Deployment Guide](developer/deployment.md)
4. [Coding Standards](developer/coding-standards.md)

## Current Status

**Phase M: Multiplayer Bug Fixes** — COMPLETE (February 2026)
**Next: Phase I (AWS Online Multiplayer Infrastructure)**

### Working Features
- Buy/sell/reroll cards from shop
- Play cards to board, sell from board or hand
- Refresh and freeze shop
- Upgrade tavern tier (1-6)
- 2-8 player games (configurable)
- AI opponents (Easy/Medium/Hard)
- Triple fusion: 3 copies = golden card + discovery
- Board reordering (click-to-swap)
- Tribe synergies with tiered thresholds (2/4/6)
- Cross-tribe combo bonuses
- Battlecry, Deathrattle, OnAttack, Taunt/Guardian abilities
- Event-driven UI with card tooltips
- Game Over screen with standings
- Online multiplayer via Photon PUN 2
- WebGL build deployed to AWS S3 + CloudFront
- Runtime JSON data loading from S3 (with built-in fallback)
- DevZone web editor (React + Express + AWS Lambda)

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

Last Updated: February 7, 2026
