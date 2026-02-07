# Combat System

## Overview

Combat is fully automated. After the recruit phase, players are paired and their boards fight. `CombatManager` is a static class that simulates battles without mutating the original boards.

## Battle Flow

```
SimulateBattle(board1, board2, tier1, tier2, name1, name2)
|
+- 1. Clone both boards (originals preserved)
+- 2. Handle empty boards (immediate win/tie)
+- 3. Trigger StartOfCombat synergies (per-player snapshots)
+- 4. Random coin flip: who attacks first
|
+- 5. Combat Loop (alternating turns):
|   +- Pick attacker: first alive card on attacking side
|   +- Pick target:
|   |   +- Guardian/Taunt card if one exists
|   |   +- Otherwise random alive card
|   +- Check attacker's OnAttack abilities
|   +- Apply attack damage (or Aegis blocks)
|   +- Apply counterattack damage (or Aegis blocks)
|   +- Process death queue (cascade up to 10 iterations)
|   +- Switch sides
|
+- 6. Calculate damage: surviving cards count + winner's tavern tier
+- 7. Cleanup combat clones (unregister abilities)
```

## Damage Calculation

Per Hearthstone Battlegrounds rules:
```
damage = number_of_surviving_minions + winner_tavern_tier
```

If both sides are eliminated simultaneously, it's a **Tie** with 0 damage.

**Examples:**

| Scenario | Survivors | Winner Tier | Damage |
|----------|-----------|-------------|--------|
| Early game | 2 | 1 | 3 |
| Mid game | 3 | 3 | 6 |
| Late game | 5 | 5 | 10 |

## Special Mechanics

### Guardian (Taunt)

Cards with `EffectType.Guardian` or `AbilityEffectType.Taunt` must be attacked first. If multiple Guardians exist, the first one in board order is targeted.

### Aegis (Divine Shield)

Cards with `hasAegis = true` block the first instance of damage:
- Attack damage blocked: Aegis consumed, no damage dealt
- When Aegis blocks, OnAttack abilities do **not** trigger
- Counterattack Aegis works the same way

### OnAttack Abilities

Triggered when a card attacks and the attack is **not** blocked by Aegis:
- `OnAttackBonusDamage`: Temporarily adds extra attack for this hit
- `OnAttackCleave`: Deals damage to cards adjacent to the target
- `OnAttackBuffSelf`: Permanently increases own attack

### Deathrattle

Triggered when a card dies in combat. Processed via the death queue:
- `DeathrattleBuffRandomFriendly`: Gives +X/+X to a random surviving friendly card
- `DeathrattleDamageAllEnemies`: Deals X damage to all enemy cards

### Death Queue System

Deaths are processed in a deterministic order to handle cascading effects:

```
Death Queue Processing:
1. Collect all dead cards (health <= 0)
   - Attacker board first (left to right)
   - Defender board second (left to right)
2. Process each death:
   - Log death
   - Trigger Echo (legacy)
   - Trigger Deathrattle abilities
   - Remove from board
3. Check for new deaths caused by deathrattles
4. Repeat (up to 10 cascade iterations)
```

This ensures deterministic deathrattle order, handles cleave victims in the same batch, and processes cascade deaths correctly.

## Synergy Integration

Before combat begins, StartOfCombat synergies are applied to cloned boards using per-player snapshots:

```csharp
var pSnapshot = SynergyManager.Instance.CalculateSynergies(pBoardCopy);
SynergyManager.Instance.TriggerSynergies(SynergyTrigger.StartOfCombat, pBoardCopy, null, pSnapshot);
```

## Combat Events

`CombatManager` fires events for UI visualization:

| Event | Parameters | When |
|-------|------------|------|
| `OnCombatStart` | player1Name, player2Name | Battle begins |
| `OnCombatLogEntry` | CombatLogEntry struct | Each combat action |
| `OnCombatEnd` | winnerName, damage | Battle ends |

### CombatLogEntry Types

`TurnStart`, `Attack`, `Counterattack`, `CardDeath`, `AegisBlock`, `EchoTrigger`, `GuardianTaunt`, `DamageDealt`, `BattleResult`

## Matchmaking

`GameManager.GeneratePairwiseBattles()` generates pairings:
- Shuffles alive players randomly
- Gives bye to player with fewest recent fights (odd player count)
- Prefers opponents not recently fought
- Tracks recent opponents per player (cleared after 2 rounds)

## Safety Limits

- Maximum 100 turns per battle (prevents infinite loops)
- Maximum 10 cascade iterations per death queue
- All combat uses cloned cards (originals never mutated)
- Combat clones are cleaned up after battle (ability unregistration)

## Related Documentation

- [Card System](card-system.md) — card stats and abilities
- [Ability System](ability-system.md) — ability triggers and effects
- [Synergy System](synergy-system.md) — tribe synergies in combat
- [Game Manager](game-manager.md) — combat phase integration
