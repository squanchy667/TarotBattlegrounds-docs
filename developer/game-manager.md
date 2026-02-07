# Game Manager

## Overview

`GameManager` is a MonoBehaviour singleton that orchestrates the game loop, manages players, handles phase transitions, and coordinates AI and multiplayer.

## Responsibilities

- Game loop (Recruit / Combat cycle)
- Player initialization and lifecycle
- Phase transitions with timer
- AI controller management
- Matchmaking (pairwise battle generation)
- Elimination tracking and game over
- Runtime config application
- Network integration (online mode)

## Game Phases

```csharp
public enum GamePhase { Recruit, Combat }
```

### Recruit Phase

1. Grant gold: `min(3 + (turn - 1), 10)` per player
2. Reduce upgrade costs by 1 for all alive players (turn > 1)
3. Refresh shops (unless frozen)
4. AI players execute turns immediately and auto-ready
5. Human player acts (buy/sell/play/upgrade/reroll/freeze)
6. Phase ends when timer expires OR all alive players click "End Turn"

Timer duration: 35 seconds (configurable via runtime config).

### Combat Phase

1. List all alive players
2. Check for game over (1 player = winner)
3. Generate pairwise battle matchups
4. Simulate each battle via `CombatManager`
5. Apply damage to losers
6. Track eliminations
7. Check for game over again
8. 5-second delay before next recruit phase

## Player Management

### Initialization

```csharp
void Start()
{
    playerCount = GameConfig.PlayerCount;
    ApplyRuntimeConfig();
    EnsurePlayerCount(playerCount);  // Spawn if needed
    InitializePlayers();
    InitializeAI();
    StartCoroutine(GameLoop());
}
```

### Dynamic Player Spawning

If the scene has fewer Player objects than `playerCount`, GameManager instantiates additional ones from `playerPrefab`. Unused players (beyond `playerCount`) are deactivated.

### Player Health

Health is tracked in `playerHealths` list (separate from `Player.Health` for host authority). Starting health: 40 (configurable via runtime config).

## Elimination & Game Over

Players are eliminated when health reaches 0. Elimination order is tracked for standings.

```csharp
public struct GameOverData
{
    public int winnerPlayerIndex;    // -1 if all eliminated
    public List<int> standings;      // Ordered by placement
    public int totalTurns;
}

public static event Action<GameOverData> OnGameOver;
```

Standings order: Winner first, then eliminated players in reverse order (last eliminated = 2nd place).

## AI Integration

Non-human players get `AIController` components with configurable difficulty (Easy/Medium/Hard). AI executes at the start of each recruit phase and immediately marks ready.

## Multiplayer Mode

In online mode (`GameConfig.CurrentGameMode == Multiplayer`):

- **Host** (MasterClient): Runs full game loop, manages all state
- **Clients**: Receive state via Photon RPCs, send actions to host

Host broadcasts phase changes, player state, combat results, timer updates, and game over.

See [Multiplayer Architecture](multiplayer.md) for details.

## Runtime Config

If `RuntimeDataLoader` has loaded config from S3, `ApplyRuntimeConfig()` overrides `recruitTimer` and `startingHealth`. Each player also receives runtime config for upgrade costs.

## Ready System

Players signal readiness via `PlayerReadyForCombat(playerIndex)`. The recruit phase ends early only when ALL alive players are ready.

## Related Documentation

- [Architecture](architecture.md) — system overview
- [Combat System](combat-system.md) — battle simulation
- [Tavern System](tavern-system.md) — shop economy
- [Multiplayer Architecture](multiplayer.md) — network integration
