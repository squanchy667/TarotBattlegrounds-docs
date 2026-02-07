# Getting Started

## Quick Overview

**Tarot Battlegrounds** is a Tarot-themed auto-battler inspired by Hearthstone Battlegrounds.

- **Status**: Fully playable (solo + online multiplayer)
- **Tech**: Unity 2022.3.48f1 + C# + Photon PUN 2
- **Players**: 2-8 (human + AI fill)
- **Live**: Deployed on AWS (S3 + CloudFront)

## For Developers

1. Read [Setup Guide](../developer/setup-guide.md)
2. Clone the repo and open `TarotBattlegrounds-POC/TarotBattlegrounds-POC/` in Unity 2022.3.48f1
3. Open the `MainMenu` scene and press Play
4. Check [Known Issues](../resources/known-issues.md)

## For Agents

Read these in order:
1. [Architecture](../developer/architecture.md) — system overview and file structure
2. [Game Manager](../developer/game-manager.md) — game loop and phases
3. [Card System](../developer/card-system.md) — cards, tiers, tribes
4. [Combat System](../developer/combat-system.md) — battle simulation
5. [Synergy System](../developer/synergy-system.md) — tribe bonuses
6. [Known Issues](../resources/known-issues.md) — active bugs

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Recruit Phase** | Buy, sell, position cards (timed) |
| **Combat Phase** | Auto-battles between paired players |
| **Tribes** | Pentacles, Cups, Swords, Wands (synergy bonuses at 2/4/6) |
| **Tiers** | 1-6 card power levels, unlocked by upgrading tavern |
| **Golden Cards** | 3 copies merge into doubled-stats golden + discovery reward |
| **Abilities** | Battlecry, Deathrattle, OnAttack, Guardian (Taunt) |
| **Synergies** | Tribe-based bonuses: economy, healing, aggro, buffs |

## Game Flow

```
Main Menu
  ├── Solo Game (4/6/8 players, Easy/Medium/Hard AI)
  └── Multiplayer (Photon lobby, human + AI fill)
        ↓
  Recruit Phase (buy/sell/position cards, upgrade tavern)
        ↓
  Combat Phase (auto-battle against paired opponent)
        ↓
  Repeat until one player remains
        ↓
  Game Over (standings, Play Again / Quit)
```

## Project Structure

The Unity project is **nested**:
```
TarotBattlegrounds-POC/           # Git root
├── TarotBattlegrounds-POC/       # Unity project root
│   ├── Assets/
│   │   ├── Scripts/              # All game code
│   │   ├── Cards/                # Card ScriptableObject
│   │   ├── Scenes/               # MainMenu, Game
│   │   └── Editor/               # Build scripts, setup tools
│   └── ProjectSettings/
├── AWS/                          # Deployment scripts
└── WebGLBuild/                   # Build output
```

## Related Documentation

- [Architecture](../developer/architecture.md)
- [Tech Stack](../resources/tech-stack.md)
- [Changelog](../resources/changelog.md)
