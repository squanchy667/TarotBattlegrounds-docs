# Game Systems

> Index and overview of all game systems in Tarot Battlegrounds. Each system has its own detailed documentation.

## Architecture Pattern: Singleton-Events-SO Trinity

Tarot Battlegrounds uses three core patterns consistently:

1. **Singletons** — Manager classes (GameManager, TavernManager, SynergyManager) provide global access points
2. **Events** — All state changes propagate via C# events; UI subscribes and reacts (no polling)
3. **ScriptableObjects** — Data-driven configuration for cards, synergies, themes (editable without code)

## System Index

| System | File | Documentation |
|--------|------|---------------|
| Card System | `Card.cs`, `CardDatabase.cs`, `CardPoolInitializer.cs` | [card-system.md](card-system.md) |
| Combat System | `CombatManager.cs` | [combat-system.md](combat-system.md) |
| Game Manager | `GameManager.cs`, `GameConfig.cs` | [game-manager.md](game-manager.md) |
| Tavern System | `TavernManager.cs` | [tavern-system.md](tavern-system.md) |
| Ability System | `AbilityManager.cs`, `IAbility.cs`, `AbilityBase.cs` | [ability-system.md](ability-system.md) |
| Synergy System | `SynergyManager.cs`, `TribeSynergy.cs` | [synergy-system.md](synergy-system.md) |
| Multiplayer | `NetworkGameBridge.cs`, `PhotonConnector.cs`, `RoomManager.cs` | [multiplayer.md](multiplayer.md) |
| Runtime Data | `RuntimeDataLoader.cs`, `DataConfig.cs` | [runtime-data.md](runtime-data.md) |

## Supporting Systems

| System | File | Documentation |
|--------|------|---------------|
| AI Opponents | `AIController.cs`, `AITestRunner.cs` | Part of [game-manager.md](game-manager.md) |
| Theme System | `ThemeManager.cs`, `ThemeConfig.cs`, `IThemeable.cs` | [Skinning Guide](../SKINNING-GUIDE.md) |
| UI Coordination | `GameUIManager.cs` | Part of [game-manager.md](game-manager.md) |
| DevZone Editor | External React app | [devzone.md](devzone.md) |

## System Interactions

```
GameManager (game loop)
├── TavernManager (card pool, shop)
│   └── CardDatabase (card definitions)
├── CombatManager (battle simulation)
│   ├── AbilityManager (ability triggers)
│   └── SynergyManager (combat synergies)
├── Player (per-player state)
│   ├── SynergyManager (per-player snapshots)
│   └── AbilityManager (card abilities)
├── AIController (AI decision making)
└── GameUIManager (UI coordination)
    └── IThemeable components (theme-aware UI)
```

## Key Events

| Event | Source | Subscribers |
|-------|--------|-------------|
| `OnHandChanged` | Player | GameUIManager → HandUI |
| `OnBoardChanged` | Player | GameUIManager → BoardUI, ShopUI (cost recalc) |
| `OnCoinsChanged` | Player | GameUIManager (button states) |
| `OnTierChanged` | Player | GameUIManager → ShopUI |
| `OnHealthChanged` | Player | GameUIManager (health display) |
| `OnShopRefreshed` | Player | GameUIManager → ShopUI |
| `OnShopFreezeChanged` | Player | GameUIManager (freeze button) |
| `OnTripleDiscovery` | Player | DiscoveryUI |
| `OnCombatStart/End` | CombatManager | CombatLogUI |
| `OnGameOver` | GameManager | GameOverUI |
| `OnThemeChanged` | ThemeManager | All IThemeable components |

## Related Documentation

- [Architecture Overview](../architecture/system-overview.md) — system diagram and design patterns
- [Data Flow](../architecture/data-flow.md) — startup sequence and game loop
- [Coding Standards](coding-standards.md) — code conventions
- [Setup Guide](setup-guide.md) — development environment
