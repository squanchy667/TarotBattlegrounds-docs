# Synergy System

## Overview

The synergy system provides tribe-based bonuses when a player has enough cards of the same tribe on their board. It uses tiered thresholds (2/4/6) and cross-tribe combo bonuses.

## Architecture

```
Player Board
    |
    v
SynergyManager.CalculateSynergies(board) -> SynergySnapshot
    |
    v
SynergySnapshot contains:
  - tribeCounts: {Swords: 3, Cups: 2}
  - activeTiers: {Swords: Tier1(threshold=2)}
  - activeCombos: [(Swords, Wands)]
```

## SynergyManager

MonoBehaviour singleton that manages synergy definitions and calculations.

### Key Methods

| Method | Description |
|--------|-------------|
| `CalculateSynergies(board)` | Returns immutable `SynergySnapshot` for a board |
| `TriggerSynergies(trigger, board, owner, snapshot)` | Apply effects from snapshot |
| `GetSellBonus(card, snapshot)` | Calculate sell bonus from synergies |
| `GetCostReduction(card, snapshot)` | Calculate buy cost reduction |
| `GetEffectiveCost(card, board)` | Full cost calculation with synergy reduction |
| `GetTribeSynergy(tribe)` | Get definition for a tribe |

### Per-Player Snapshots (Multiplayer Safe)

All synergy methods use `SynergySnapshot` objects instead of mutating singleton state. This prevents multiplayer bugs where Player A's synergy calculation overwrites Player B's state.

```csharp
// Correct usage:
var snapshot = SynergyManager.Instance.CalculateSynergies(player.board);
SynergyManager.Instance.TriggerSynergies(trigger, board, player, snapshot);

// Deprecated (causes multiplayer bugs):
SynergyManager.Instance.UpdateTribeCounts(board);  // [Obsolete] error
```

## TribeSynergy ScriptableObject

Each tribe has a `TribeSynergy` definition:

```csharp
public class TribeSynergy : ScriptableObject
{
    public TribeType tribe;
    public string tribeName;
    public string description;
    public Color themeColor;

    public SynergyTier[] tiers;          // Tiered bonuses (2/4/6)

    // Cross-tribe combo
    public TribeType comboTribe;
    public int comboThreshold;
    public SynergyEffect comboEffect;
    public int comboValue;
    public string comboDescription;
}
```

## Synergy Tiers

Each tribe has up to 3 tiers of bonuses, activated by having enough tribe members on board:

```csharp
public class SynergyTier
{
    public int threshold;           // Number of tribe members needed (2, 4, 6)
    public SynergyTrigger trigger;  // When to apply
    public SynergyEffect effect;    // What to apply
    public SynergyTarget target;    // Who to apply to
    public int value;               // Effect magnitude
    public string description;
}
```

## Synergy Triggers

```csharp
public enum SynergyTrigger
{
    Passive,         // Always active
    StartOfCombat,   // Applied when combat begins
    EndOfTurn,       // Applied at end of recruit phase
    OnSell,          // When selling a card
    OnBuy            // When buying a card
}
```

## Synergy Effects

```csharp
public enum SynergyEffect
{
    BuffAttack,    // +attack
    BuffHealth,    // +health
    BuffStats,     // +attack and +health
    BonusGold,     // +gold
    Shield,        // Gain Aegis
    HealFlat,      // Heal for flat amount
    ReduceCost     // Reduce buy cost
}
```

## Synergy Targets

```csharp
public enum SynergyTarget
{
    AllTribeMembers,  // All cards of this tribe
    AllFriendly,      // All cards on board
    Adjacent,         // Cards adjacent to tribe members
    Random            // One random card
}
```

## Cross-Tribe Combos

When two specific tribes both meet their combo thresholds, a bonus effect is applied to all friendly cards:

```csharp
if (synergy.IsComboActive(thisTribeCount, partnerCount))
{
    // Apply combo effect to all friendly cards
}
```

Combos are deduplicated (A+B and B+A are the same combo).

## Current Tribe Synergies

| Tribe | Tier 1 (2) | Tier 2 (4) | Tier 3 (6) |
|-------|------------|------------|------------|
| Pentacles | +1 gold on sell | +1 gold on buy | +2 gold on sell |
| Cups | +1 health to Cups | Shield to Cups | +2 health to all |
| Swords | +1 attack to Swords | +1 attack to all | +2 attack to Swords |
| Wands | +1 stats to adjacent | +1 attack to all | +2 stats to Wands |

## Initialization

Synergies are initialized by `CardPoolInitializer`:

1. Check `RuntimeDataLoader` for runtime synergy data
2. If available, build `TribeSynergy[]` from JSON
3. Otherwise, fall back to `SynergyTestData.CreateAllTribeSynergies()`
4. Assign to `SynergyManager.tribeSynergies` and reinitialize cache

## Integration Points

- **Buying**: `SynergyManager.GetEffectiveCost()` for cost reduction
- **Selling**: `SynergyManager.GetSellBonus()` for bonus gold
- **End of Recruit**: `TriggerSynergies(EndOfTurn, ...)` for end-of-turn effects
- **Combat Start**: `TriggerSynergies(StartOfCombat, ...)` for pre-combat buffs

## Related Documentation

- [Card System](card-system.md) — tribes and multi-tribe cards
- [Ability System](ability-system.md) — per-card abilities
- [Combat System](combat-system.md) — synergies in combat
- [Tavern System](tavern-system.md) — economy synergies
