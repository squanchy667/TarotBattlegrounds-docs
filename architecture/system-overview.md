# System Overview

## System Architecture

Tarot Battlegrounds uses an event-driven, singleton-based architecture built in Unity 2022.3.48f1 with C#.

```
┌─────────────────────────────────────────────────────────┐
│                      Unity Game                          │
│                                                          │
│  ┌──────────────┐   ┌──────────────┐   ┌─────────────┐ │
│  │  GameManager  │──>│    Player     │──>│  TavernMgr  │ │
│  │  (singleton)  │   │  (per-player) │   │ (singleton) │ │
│  └──────┬───────┘   └──────┬───────┘   └──────┬──────┘ │
│         │                  │                    │        │
│  ┌──────▼───────┐   ┌──────▼───────┐   ┌──────▼──────┐ │
│  │ CombatManager │   │ AbilityMgr   │   │CardDatabase │ │
│  │   (static)    │   │   (static)   │   │  (static)   │ │
│  └──────┬───────┘   └──────────────┘   └─────────────┘ │
│         │                                                │
│  ┌──────▼───────┐   ┌──────────────┐   ┌─────────────┐ │
│  │ SynergyMgr   │   │ GameUIManager│   │  ThemeMgr   │ │
│  │ (singleton)  │   │  (singleton) │   │  (static)   │ │
│  └──────────────┘   └──────────────┘   └─────────────┘ │
│                                                          │
│  ┌──────────────────────────────────────────────────────┤
│  │              Networking Layer (Photon PUN 2)          │
│  │  PhotonConnector │ NetworkGameBridge │ RoomManager   │
│  │  NetworkGameSetup│ NetworkCardData   │ CardLookup    │
│  └──────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────────────────────────────────────────┤
│  │           Runtime Data Layer (optional)               │
│  │  RuntimeDataLoader │ DataConfig │ CardPoolInitializer │
│  └──────────────────────────────────────────────────────┘
└─────────────────────────────────────────────────────────┘
```

## Core Design Patterns

### Event-Driven UI

All UI updates flow through C# events. Player state changes fire events; UI subscribes and reacts.

```
Player State Change  →  Event Fired  →  UI Subscribes  →  UI Updates
```

Key events on `Player`:
- `OnHandChanged` — hand contents changed
- `OnBoardChanged` — board contents changed
- `OnCoinsChanged` — gold amount changed
- `OnTierChanged` — tavern tier changed
- `OnHealthChanged` — player health changed
- `OnShopRefreshed` — shop cards refreshed
- `OnShopFreezeChanged` — freeze toggled
- `OnTripleDiscovery` — triple fusion triggered discovery

Static event: `Player.OnAnyPlayerStateChanged` for global listeners.

### Singleton Managers

| Manager | Type | Responsibility |
|---------|------|----------------|
| `GameManager` | MonoBehaviour singleton | Game loop, phases, player lifecycle |
| `TavernManager` | MonoBehaviour singleton | Card pool, shop generation, discovery |
| `SynergyManager` | MonoBehaviour singleton | Tribe counting, synergy effects |
| `CombatManager` | Static class | Battle simulation |
| `AbilityManager` | Static class | Ability registration and triggering |
| `CardDatabase` | Static class | Card definitions (30 cards, 6 tiers) |
| `ThemeManager` | Static class | Tribe names, colors, text |
| `GameUIManager` | MonoBehaviour singleton | UI coordination |

### Per-Player Synergy Snapshots (M1 Fix)

To avoid multiplayer bugs where players overwrite each other's synergy state, all synergy calculations use immutable snapshots:

```csharp
var snapshot = SynergyManager.Instance.CalculateSynergies(player.board);
SynergyManager.Instance.TriggerSynergies(trigger, board, player, snapshot);
int bonus = SynergyManager.Instance.GetSellBonus(card, snapshot);
```

The deprecated global-state methods (`UpdateTribeCounts`, `GetTribeCount`, etc.) are marked `[Obsolete]` with hard errors.

## File Organization

```
Assets/Scripts/
├── Abilities/          # IAbility interface, AbilityManager, concrete abilities
├── Cards/              # Card ScriptableObject, CardDatabase, CardPoolInitializer
├── Data/               # RuntimeDataLoader, DataConfig, RuntimeCardImageLoader
├── Network/            # PhotonConnector, NetworkGameBridge, RoomManager, etc.
├── Synergies/          # SynergyManager, TribeSynergy, SynergyTestData
├── UI/                 # ShopUI, HandUI, BoardUI, GameUIManager, etc.
├── Player.cs           # Player state and actions
├── GameManager.cs      # Game loop and phase management
├── TavernManager.cs    # Card pool and shop
├── CombatManager.cs    # Battle simulation
├── GameConfig.cs       # Static game configuration
├── ThemeManager.cs     # Theme/skin system
└── AIController.cs     # AI opponent logic
```

## Key Architectural Decisions

1. **ScriptableObject cards**: Cards are `ScriptableObject` instances, cloned when bought/played to avoid shared mutation.
2. **Static managers for stateless logic**: `CombatManager`, `AbilityManager`, `CardDatabase` are static classes since they don't need MonoBehaviour lifecycle.
3. **Host-authoritative multiplayer**: Only the host runs game logic. Clients send actions and receive state broadcasts.
4. **Runtime data fallback**: If JSON fetch fails, the game falls back to built-in `CardDatabase` definitions. The game always works offline.
5. **Event-driven UI**: No polling. UI components subscribe to player events and refresh only when state changes.

## Related Documentation

- [Data Flow](data-flow.md) — startup sequence, game loop, multiplayer sync
- [Game Manager](../developer/game-manager.md) — game loop details
- [Multiplayer](../developer/multiplayer.md) — networking architecture
- [Card System](../developer/card-system.md) — card data model
