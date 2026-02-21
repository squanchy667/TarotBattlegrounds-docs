# Data Flow

## Game Startup

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

## Game Loop (Host-Authoritative)

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

## Multiplayer State Sync

```
Host (MasterClient)                    Clients
─────────────────                      ───────
Runs full game logic                   Receives state via RPCs
GameLoop coroutine                     No game loop
AI controllers for bots                Sends actions to host
Broadcasts state changes               Applies network state
Authoritative decisions                Renders received state
```

### NetworkPlayerState Sync Fields
- `coins`, `tier`, `health`, `upgradeCost`
- Board card state (card IDs, stats, positions)
- Phase and turn changes via `RPC_PhaseChanged`
- Card pool initialized with `EnsureCardPoolInitialized()` + `WaitUntil`

## Runtime Data Pipeline

```
[DevZone Web Editor] -> [Express API on Lambda] -> [S3: game data JSON]
                                                         |
[Unity WebGL Game]  <---- fetches cards.json, synergies.json, config.json at startup
```

- Data fetched at startup by `RuntimeDataLoader` (execution order -100)
- Falls back to built-in `CardDatabase` if S3 is unreachable
- No rebuild needed — edit in DevZone, publish, game picks up changes on next load

## Error & Fallback Patterns

| Scenario | Fallback |
|----------|----------|
| S3 unreachable | Built-in CardDatabase (30 cards) |
| JSON parse failure | Built-in defaults |
| Player disconnect during discovery | `OnDestroy()` returns pending cards to pool |
| Combat clone leak | `CleanupCombatClones()` called before every return path |
| AbilityManager between games | Static dict cleared to prevent memory leak |

## Related Documentation

- [System Overview](system-overview.md) — architecture diagram and patterns
- [Runtime Data Loading](../developer/runtime-data.md) — JSON config details
- [Multiplayer](../developer/multiplayer.md) — full networking docs
- [DevZone Editor](../developer/devzone.md) — web editor for live data
