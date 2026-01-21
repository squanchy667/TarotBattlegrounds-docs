# Architecture Overview

## System Design

Tarot Battlegrounds uses a modular, manager-based architecture with an event-driven UI system:

```
GameManager (Core Loop)
    ↓
├─→ TavernManager (Shop/Economy)
├─→ CombatManager (Battle Simulation)  ──→ Combat Events
├─→ BoardManager (Card State)
└─→ Player (State + Events)  ──→ UI Event System
         ↓
    GameUIManager (UI Orchestration)
         ↓
    ├─→ ShopUI
    ├─→ HandUI
    ├─→ BoardUI
    └─→ CombatLogUI (NEW)
         ↓
    Card ScriptableObjects (Data)
```

## Key Components

### GameManager
- Game loop orchestration
- Phase transitions (Recruit ↔ Combat)
- Turn progression
- Player initialization

### TavernManager
- Shop generation
- Buy/sell/reroll mechanics
- Gold economy
- Tier unlocking

### CombatManager
- Battle simulation
- Turn-based attacks
- Damage calculation
- Win/loss determination
- **Combat event logging** (NEW)

### Player
- Player state (coins, tier, health, hand, board)
- **Event system for UI updates** (NEW)
- Card operations (buy, sell, play)
- Tier upgrade logic

### Card System
- ScriptableObject-based
- Tier 1-6 system
- Tribe mechanics (Pentacles, Cups, Swords, Wands)
- Effect types (Summoning, LastReading, Guardian, Aegis, Echo)

## UI Architecture (NEW - January 2026)

### Event-Driven Design

The UI uses an event subscription model instead of polling:

```
Player State Change
    ↓
Player fires event (OnCoinsChanged, OnHandChanged, etc.)
    ↓
GameUIManager receives event
    ↓
Updates relevant UI panel
```

### Player Events
- `OnHandChanged` - Hand cards modified
- `OnBoardChanged` - Board cards modified  
- `OnCoinsChanged` - Coin count changed
- `OnTierChanged` - Tavern tier upgraded
- `OnHealthChanged` - Player health changed
- `OnShopRefreshed` - Shop contents changed

### Combat Events
- `OnCombatStart` - Battle begins
- `OnCombatLogEntry` - Each combat action (attack, death, etc.)
- `OnCombatEnd` - Battle results

### UI Components
- **GameUIManager** - Central hub, subscribes to player events, manages button states
- **ShopUI** - Displays available cards, handles selection
- **HandUI** - Shows cards in hand
- **BoardUI** - Shows played cards on board
- **CombatLogUI** - Real-time combat visualization (NEW)
- **CardDisplayUI** - Individual card rendering

## Design Patterns

- **Manager Pattern**: Centralized subsystem control
- **Observer Pattern**: Event-driven UI updates (NEW)
- **ScriptableObjects**: Data-driven card definitions
- **Coroutines**: Phase-based state machine
- **Cloning**: Safe combat simulation

## File Structure

```
Assets/Scripts/
├── GameManager.cs
├── TavernManager.cs
├── CombatManager.cs
├── BoardManager.cs
├── Player.cs
├── Card.cs
├── UI/
│   ├── GameUIManager.cs
│   ├── ShopUI.cs
│   ├── HandUI.cs
│   ├── BoardUI.cs
│   ├── CombatLogUI.cs (NEW)
│   └── CardDisplayUI.cs
└── Tests/
    ├── CardSystemTests.cs
    ├── CombatTests.cs
    ├── EconomyTests.cs
    └── EdgeCaseTests.cs
```

For more details, see the main GitHub repository.
