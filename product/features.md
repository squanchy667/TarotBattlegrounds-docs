# Features

> Consolidated feature overview for Tarot Battlegrounds. See game-design/ docs for detailed mechanics.

## Core Gameplay

- **Auto-battler loop** — Recruit phase (buy/sell/reroll/position) → Combat phase (automated battles) → repeat
- **2-8 players** — 1 human + AI fill, or multiplayer with human + AI mix
- **6-tier progression** — Cards unlock as players upgrade their tavern
- **Last player standing** — Elimination at 0 health, ranked by elimination order

## Card System

- **30 base cards** across 6 tiers (5 per tier), expandable via DevZone
- **4 tribes** — Pentacles (economy), Cups (healing), Swords (aggro), Wands (buffs)
- **Multi-tribe cards** — Dual and triple tribe cards for synergy flexibility
- **Abilities** — Battlecry, Deathrattle, OnAttack, Guardian (Taunt), Aegis (Shield)
- **Triple/Fusion** — 3 copies merge into golden card (doubled stats) + Discovery reward

See: [Card System](../developer/card-system.md), [Core Gameplay](game-design/core-gameplay.md)

## Synergy System

- **Tiered thresholds** — 2/4/6 tribe members activate escalating bonuses
- **Cross-tribe combos** — 4 combo pairs (Pentacles+Cups, Cups+Wands, Swords+Pentacles, Wands+Swords)
- **Per-player snapshots** — Immutable synergy calculations prevent multiplayer bugs
- **Data-driven** — Synergies defined via ScriptableObjects, editable without code changes

See: [Synergy System](../developer/synergy-system.md), [Tarot Mechanics](game-design/tarot-mechanics.md)

## Combat

- **Turn-based auto-battle** — Alternating attacks, left-to-right board order
- **Death queue** — Cascading deathrattles with proper ordering
- **Round-robin matchmaking** — Avoids recent opponents
- **Damage formula** — Winner's tavern tier + surviving unit count

See: [Combat System](../developer/combat-system.md)

## AI Opponents

- **3 difficulty levels** — Easy (random), Medium (balanced), Hard (synergy-aware)
- **Full recruit phase AI** — Buy, sell, reroll, upgrade, position decisions
- **Balance-tested** — 100-game batch simulations verify Hard > Medium > Easy

## Theme System

- **100% theme-agnostic engine** — Cards = ID + Stats + AbilityType + TribeTag[]
- **ScriptableObject themes** — ThemeConfig holds colors, fonts, sprites, tribe data
- **Runtime hot-swap** — All UI implements IThemeable, OnThemeChanged event
- **Zero code changes** — New skin = card database + art assets + UI theme

See: [Skinning Guide](../SKINNING-GUIDE.md)

## Multiplayer

- **Photon PUN 2** — Host-authoritative networking with WebSocketSecure for WebGL
- **State sync** — Coins, tier, health, upgrade cost, board state via RPCs
- **ParrelSync tested** — 8 multiplayer bugs found and fixed in Phase M

See: [Multiplayer](../developer/multiplayer.md)

## DevZone Editor

- **Web-based editor** — React + Vite + Tailwind frontend
- **Live data pipeline** — Edit cards/synergies/config → publish → game loads on next startup
- **AWS backend** — Express API on Lambda, DynamoDB for versioning, S3 for game JSON

See: [DevZone Editor](../developer/devzone.md)

## Deployment

- **WebGL on AWS** — S3 + CloudFront (~$0.15/mo)
- **Runtime data from S3** — cards.json, synergies.json, config.json
- **Fallback** — Built-in CardDatabase if S3 unreachable

See: [Deployment Guide](../developer/deployment.md)

## Planned Features (Phase I)

- AWS online multiplayer (Cognito auth, WebSocket API, DynamoDB profiles)
- Server-side combat validation (Lambda)
- Matchmaking queue
- Reconnection handling
- Leaderboard system

See: [Roadmap](roadmap.md), [PLAN.md](../PLAN.md)
