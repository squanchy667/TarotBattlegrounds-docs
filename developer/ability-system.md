# Ability System

## Overview

The ability system provides per-card effects triggered at specific game moments (play, attack, death). It uses an interface-based design with a static `AbilityManager` for registration and triggering.

## Architecture

```
Card.RegisterAbility()
    |
    v
AbilityManager (static dictionary: Card -> List<IAbility>)
    |
    v
TriggerAbilities(trigger, context)
    |
    v
IAbility.Execute(context) for matching triggers
```

## IAbility Interface

Each ability implements `IAbility`:

```csharp
public interface IAbility
{
    AbilityTrigger Trigger { get; }
    bool CanExecute(AbilityContext context);
    void Execute(AbilityContext context);
}
```

## AbilityContext

Context passed to abilities containing all relevant state:

```csharp
public class AbilityContext
{
    public Card SourceCard;     // Card that owns this ability
    public Card TargetCard;     // Target of attack (combat only)
    public Player Owner;        // Player who owns the card
    public List<Card> OwnerBoard;   // Friendly board
    public List<Card> EnemyBoard;   // Enemy board (combat only)
}
```

## AbilityManager

Static class managing ability lifecycle:

| Method | Description |
|--------|-------------|
| `RegisterAbility(card, ability)` | Register an ability for a card (deduplicates) |
| `UnregisterCard(card)` | Remove all abilities for a card (on sell/death) |
| `GetAbilities(card)` | Get all abilities for a card |
| `TriggerAbilities(trigger, context)` | Trigger matching abilities for a card |
| `TriggerAbilitiesForBoard(trigger, board, owner)` | Trigger for all cards on a board |
| `CreateBattlecryContext(card, owner)` | Create context for when a card is played |
| `ClearAll()` | Remove all registrations (new game cleanup) |

## Ability Triggers

```csharp
public enum AbilityTrigger
{
    None,
    Battlecry,     // When played from hand to board
    Deathrattle,   // When card dies in combat
    OnAttack       // When card attacks (not blocked by Aegis)
}
```

## Ability Effects

```csharp
public enum AbilityEffectType
{
    None,
    GainCoins,                      // Player gains gold
    GainAegis,                      // Card gains Aegis shield
    BuffAdjacentAttack,             // +attack to adjacent cards
    BuffAdjacentStats,              // +attack/+health to adjacent cards
    BuffAllFriendlyAttack,          // +attack to all friendly cards
    OnAttackBonusDamage,            // Extra damage during this attack
    OnAttackCleave,                 // Damage adjacent enemies
    OnAttackBuffSelf,               // Permanent +attack on self
    DeathrattleBuffRandomFriendly,  // +stats to random friendly on death
    DeathrattleDamageAllEnemies,    // Damage all enemies on death
    Taunt                           // Must be attacked first (Guardian)
}
```

## How Abilities Are Registered

When a card is played from hand to board, `Player.PlayCard()` calls:

```csharp
card.RegisterAbility();  // Card creates IAbility based on abilityTrigger/abilityEffect
AbilityManager.TriggerAbilities(AbilityTrigger.Battlecry, battlecryContext);
```

## How Abilities Are Triggered

### During Recruit Phase (Battlecry)

```csharp
// In Player.PlayCard():
var context = AbilityManager.CreateBattlecryContext(card, this);
AbilityManager.TriggerAbilities(AbilityTrigger.Battlecry, context);
```

### During Combat (OnAttack, Deathrattle)

```csharp
// In CombatManager.TriggerCombatAbility():
var context = new AbilityContext
{
    SourceCard = source,
    TargetCard = target,
    OwnerBoard = ownerBoard,
    EnemyBoard = enemyBoard,
    Owner = null  // Combat uses cloned cards
};
AbilityManager.TriggerAbilities(trigger, context);
```

### Cleanup

Abilities are cleaned up:
- On card sell: `AbilityManager.UnregisterCard(card)`
- On game end: `AbilityManager.ClearAll()`
- After combat: `CleanupCombatClones()` unregisters all cloned cards

## Golden Card Abilities

Golden cards have doubled `abilityValue`. When a golden card is created via triple fusion, its ability value is `baseCard.abilityValue * 2`.

## Legacy Effect System

Some effects still use the legacy `Card.EffectType` enum:
- `Guardian` — taunt (must be attacked first)
- `Echo` — on death, buff a random friendly
- `Summoning` — on play, buff adjacent cards

These are being gradually migrated to the `IAbility` system.

## Related Documentation

- [Card System](card-system.md) — card definitions and effects
- [Combat System](combat-system.md) — combat ability triggering
- [Synergy System](synergy-system.md) — tribe-based effects
