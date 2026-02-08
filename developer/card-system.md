# Card System

## Overview

Cards are the core gameplay element. Each card is a `ScriptableObject` with stats, tribes, and abilities. The game has 35 unique cards across 6 tiers (30 generated + 5 original user-created cards).

## Card ScriptableObject

```csharp
public class Card : ScriptableObject
{
    public string cardName;
    public int tier;           // 1-6
    public int attack;
    public int health;
    public TribeType[] tribes; // Multi-tribe support
    public string ability;     // Display text

    // Ability system
    public AbilityTrigger abilityTrigger;    // Battlecry, Deathrattle, OnAttack, None
    public AbilityEffectType abilityEffect;  // GainCoins, BuffAdjacentAttack, etc.
    public int abilityValue;                 // Effect magnitude

    // Legacy effect system
    public EffectType effectType;    // Guardian, Echo, Summoning, etc.
    public string effectParameter;

    // Economy
    public int buyCostModifier;      // Added to base cost of 3
    public int sellValueModifier;    // Added to base sell value of 1

    // Golden card
    public bool isGolden;
}
```

## Tribes

Four tribes (tarot suits), plus None:

| Tribe | Theme | Gameplay Focus |
|-------|-------|----------------|
| Pentacles | Economy | Gold generation, cost reduction |
| Cups | Healing/Defense | Health buffs, Aegis, Guardian |
| Swords | Aggro | Attack buffs, bonus damage, cleave |
| Wands | Magic/Buffs | AoE buffs, deathrattle damage |

Cards can have **multiple tribes** (dual-tribe and triple-tribe cards exist). Multi-tribe cards count for synergies of all their tribes.

## Tiers (1-6)

| Tier | Cards | Power Level | Pool Copies |
|------|-------|-------------|-------------|
| 1 | 8 cards | Basic stats, simple abilities | 16 each |
| 2 | 6 cards | Simple abilities (Battlecry, OnAttack) | 15 each |
| 3 | 6 cards | Mid-game power, synergy enablers | 13 each |
| 4 | 5 cards | Late-game synergies, stronger effects | 11 each |
| 5 | 5 cards | Power spikes, dual-tribe | 9 each |
| 6 | 5 cards | Finishers, triple-tribe | 7 each |

## Ability System

Cards use the ability system via `AbilityTrigger` + `AbilityEffectType`:

### Triggers
- **Battlecry** — when played from hand to board
- **Deathrattle** — when the card dies in combat
- **OnAttack** — when the card attacks (not blocked by Aegis)
- **None** — passive or no ability

### Effects

| Effect | Description |
|--------|-------------|
| `GainCoins` | Player gains gold |
| `GainAegis` | Card gains Aegis shield |
| `BuffAdjacentAttack` | +attack to adjacent cards |
| `BuffAdjacentStats` | +attack/+health to adjacent cards |
| `BuffAllFriendlyAttack` | +attack to all friendly cards |
| `OnAttackBonusDamage` | Extra damage on attack |
| `OnAttackCleave` | Damage to adjacent enemies |
| `OnAttackBuffSelf` | Permanent +attack on self |
| `DeathrattleBuffRandomFriendly` | +stats to random friendly on death |
| `DeathrattleDamageAllEnemies` | Damage all enemies on death |
| `Taunt` / Guardian | Must be attacked first |

See [Ability System](ability-system.md) for full details.

## Golden Cards (Triples)

When a player has 3 copies of the same non-golden card (across hand and board), they automatically fuse into a **golden** version:

1. All 3 copies are removed (consumed, not returned to pool)
2. A golden card is created with **doubled stats** (2x attack, 2x health, 2x ability value)
3. Golden card is added to hand
4. **Discovery reward**: Player is offered 3 random cards from one tier above their current tavern tier
5. Player picks one discovery card; unchosen cards return to pool

Golden cards are never returned to the pool when sold.

## Card Pool

The shared pool is managed by `TavernManager`. Each unique card has a number of copies based on tier (see tier table above). When a card is bought, it's removed from the pool. When sold, it returns (unless golden).

### Pool Flow

```
TavernManager.masterCards (35 unique cards)
    | GenerateFullPool() - create copies per tier
    v
allCards (shared pool ~355 cards)
    | RefreshPlayerShop() - draw random cards <= player tier
    v
availableCards[playerId] (per-player shop, 3-6 cards)
    | BuyCard() - clone to hand, remove from shop
    | SellCard() - return to pool (unless golden)
```

## CardDatabase

`CardDatabase.cs` is a static class that defines all 35 built-in cards (30 generated + 5 original). It also supports runtime data:

```csharp
List<Card> cards = CardDatabase.GenerateAllCards();
// Checks RuntimeDataLoader first, falls back to built-in
bool isRuntime = CardDatabase.IsUsingRuntimeData;
```

## CardPoolInitializer

`CardPoolInitializer.cs` runs on scene start:

1. Waits for `RuntimeDataLoader` if present
2. Calls `CardDatabase.GenerateAllCards()`
3. Assigns to `TavernManager.masterCards`
4. Applies runtime config (tierCopies, shopSizes)
5. Initializes `SynergyManager` with synergy data
6. Prints card pool summary (debug)

## Related Documentation

- [Ability System](ability-system.md) — ability triggers and effects
- [Synergy System](synergy-system.md) — tribe synergies
- [Tavern System](tavern-system.md) — shop and economy
- [Combat System](combat-system.md) — how cards fight
- [Runtime Data Loading](runtime-data.md) — loading cards from JSON
