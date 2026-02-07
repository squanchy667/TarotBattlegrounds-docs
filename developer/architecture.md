# Architecture Overview

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

## Data Flow

### Game Startup

```
1. RuntimeDataLoader.Start()
   └─ Fetches cards.json, synergies.json, config.json from S3 (if configured)

2. CardPoolInitializer.Start()
   ├─ Waits for RuntimeDataLoader if needed
   ├─ Calls CardDatabase.GenerateAllCards()
   │   └─ Uses runtime data if loaded, else built-in 30 cards
   ├─ Assigns cards to TavernManager.masterCards
   ├─ Applies runtime config (tierCopies, shopSizes)
   └─ Initializes SynergyManager

3. GameManager.Start()
   ├─ Reads GameConfig (player count, AI difficulty, game mode)
   ├─ Applies runtime config (recruitTimer, startingHealth)
   ├─ Spawns player objects (EnsurePlayerCount)
   ├─ Initializes players and AI controllers
   └─ Starts GameLoop coroutine (offline) or waits for network setup (online)
```

### Game Loop (Host-Authoritative)

```
GameLoop:
  while players alive:
    1. RecruitPhase (timed)
       ├─ Grant gold (min(3 + turn-1, 10))
       ├─ Reduce upgrade costs by 1
       ├─ Refresh shops for alive players
       ├─ AI executes turns immediately
       ├─ Human player acts (buy/sell/play/reroll/upgrade/freeze)
       └─ Ends when timer expires or all players ready

    2. CombatPhase
       ├─ Generate pairwise matchups (avoid recent opponents)
       ├─ SimulateBattle for each pair
       │   ├─ Clone boards
       │   ├─ Trigger StartOfCombat synergies
       │   ├─ Alternating attacks with abilities
       │   └─ Process deaths (death queue with cascades)
       ├─ Apply damage to losers
       ├─ Track eliminations
       └─ Check for game over (1 or 0 players remaining)
```

### Multiplayer Architecture

```
Host (MasterClient)                    Clients
─────────────────                      ───────
Runs full game logic                   Receives state via RPCs
GameLoop coroutine                     No game loop
AI controllers for bots                Sends actions to host
Broadcasts state changes               Applies network state
Authoritative decisions                Renders received state
```

See [Multiplayer Architecture](multiplayer.md) for full details.

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
