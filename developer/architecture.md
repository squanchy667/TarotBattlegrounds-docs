# Architecture Overview

## System Design

Tarot Battlegrounds uses a modular, manager-based architecture:

```
GameManager (Core Loop)
    ↓
    ├─→ TavernManager (Shop/Economy)
    ├─→ CombatManager (Battle Simulation)  
    └─→ BoardManager (Card State)
         ↓
    Card ScriptableObjects (Data)
```

## Key Components

### GameManager
- Game loop orchestration
- Phase transitions (Recruit ↔ Combat)
- Turn progression

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

### Card System
- ScriptableObject-based
- Tier 1-6 system
- Tribe mechanics (Pentacles, Cups, Swords, Wands)

## Design Patterns

- **Manager Pattern**: Centralized subsystem control
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
└── Card.cs
```

For more details, see the main GitHub repository.
