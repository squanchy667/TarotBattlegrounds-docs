# Tavern System

## Overview

The Tavern system manages the shared card pool, per-player shops, and tavern tier progression. It's the economy backbone of the game.

## TavernManager

MonoBehaviour singleton that persists across scenes (in offline mode).

### Shared Card Pool

All cards exist in a single shared pool. When a card is offered in a shop, it's reserved (removed from pool). When sold back, it returns.

```
Pool sizes per tier:
Tier 1: 16 copies each (5 unique x 16 = 80)
Tier 2: 15 copies each (5 unique x 15 = 75)
Tier 3: 13 copies each (5 unique x 13 = 65)
Tier 4: 11 copies each (5 unique x 11 = 55)
Tier 5:  9 copies each (5 unique x  9 = 45)
Tier 6:  7 copies each (5 unique x  7 = 35)
Total: ~355 cards in pool
```

### Shop Generation

1. Return previous un-bought shop cards to pool
2. Filter pool for cards at or below player's tavern tier
3. Draw random cards up to shop size limit
4. Reserve drawn cards (remove from pool)

Shop sizes by tavern tier:

| Tavern Tier | Shop Size |
|-------------|-----------|
| 1 | 3 |
| 2 | 4 |
| 3 | 4 |
| 4 | 5 |
| 5 | 5 |
| 6 | 6 |

### Shop Freeze

Players can freeze their shop to keep current offers for the next turn:
- `Player.ToggleShopFreeze()` toggles freeze state
- When frozen, `RefreshShop()` skips `RefreshPlayerShop()` and auto-unfreezes
- Manual reroll clears freeze

### Discovery Cards

When a triple is formed, the player discovers cards from one tier above:

1. Filter pool for cards at the specified tier
2. Deduplicate by card name (offer variety)
3. Shuffle and take up to 3 cards
4. Reserve from pool (prevent other players from getting them)
5. After player picks one, unchosen cards return to pool

## Player Economy

### Gold

```
Gold per turn: min(3 + (turn - 1), 10)
Turn 1: 3 gold
Turn 2: 4 gold
...
Turn 8+: 10 gold (cap)
Max gold: 10 (capped by Player.MAX_COINS)
```

### Buying

- Base cost: `3 + card.buyCostModifier`
- Synergy reduction: `SynergyManager.GetEffectiveCost(card, board)` checks for cost reduction synergies
- Minimum cost: 1 (after reductions)
- Card is cloned from shop to hand
- Automatically checks for triples after buying

### Selling

- Base sell value: `1 + card.sellValueModifier`
- Synergy bonus: Pentacles sell bonus added via `SynergyManager.GetSellBonus(card, snapshot)`
- Golden cards are consumed (not returned to pool)
- Normal cards reset stats and return to pool
- Abilities are unregistered on sell

### Rerolling

- Costs 1 gold
- Clears freeze if frozen
- Generates new shop cards via `TavernManager.RefreshPlayerShop()`

## Tavern Tier Upgrades

Base upgrade costs:

| Target Tier | Base Cost |
|-------------|-----------|
| 2 | 5 |
| 3 | 8 |
| 4 | 11 |
| 5 | 11 |
| 6 | 11 |

**Lifecycle mechanic**: Upgrade costs reduce by 1 each turn (applied in `GameManager.RecruitPhase()`). After upgrading, cost resets to the base cost for the next tier.

Higher tavern tiers unlock higher-tier cards in the shop and increase combat damage.

## Network Integration

In online multiplayer:
- Host manages the shared pool and generates shops
- Client shops are set via `SetShopFromNetwork(playerIndex, shopCards)`
- Client actions are sent to host via RPCs
- Host broadcasts updated state back to all clients

## Runtime Config

Both `tierCopies` and `shopSizes` can be overridden by `RuntimeDataLoader` via `TavernManager.ApplyRuntimeConfig()`. Called by `CardPoolInitializer` after runtime data loads.

## Related Documentation

- [Card System](card-system.md) — card definitions
- [Economy Design](../product/game-design/economy.md) — design philosophy
- [Runtime Data Loading](runtime-data.md) — JSON config overrides
