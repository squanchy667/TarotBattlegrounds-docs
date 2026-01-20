# Game Manager

## Overview

**GameManager.cs** is the core orchestrator of the game loop, managing phase transitions, turn progression, and coordinating between subsystems.

---

## Key Responsibilities

1. Initialize game state
2. Run main game loop (Recruit → Combat → Repeat)
3. Track turn number
4. Coordinate TavernManager, CombatManager, BoardManager
5. Check win conditions

---

## Core Methods

### Start()
```csharp
void Start()
{
    InitializePlayers();
    StartCoroutine(GameLoop());
}
```

### GameLoop()
```csharp
IEnumerator GameLoop()
{
    while (!IsGameOver())
    {
        yield return StartCoroutine(RecruitPhase());
        yield return StartCoroutine(CombatPhase());
        NextTurn();
    }
    EndGame();
}
```

### RecruitPhase()
- Reset gold to expected amount
- Refresh tavern shop
- Wait for player actions (buy/sell/reroll)
- Transition to combat

### CombatPhase()
- Simulate battle between boards
- Apply damage to players
- Check for eliminations

---

## State Management

- **currentTurn**: int tracking turn number
- **playerHealth**: int (starts at 40)
- **enemyHealth**: int (starts at 40)
- **currentPhase**: enum (Recruit/Combat)

---

## Related Documentation

- [Architecture](architecture.md)
- [Combat System](combat-system.md)
- [Tavern System](tavern-system.md)
