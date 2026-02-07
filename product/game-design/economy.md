# Economy System

## Gold Sources

| Source | Amount | Notes |
|--------|--------|-------|
| Per Turn | +1 (capped at 10) | Starting gold: 3 |
| Selling Cards | +1 base | Plus `sellValueModifier` (clamped to min 0) |
| Pentacles Synergy (Tier 1) | +1 on sell | 2+ Pentacles on board |
| Pentacles Synergy (Tier 3) | +2 on sell | 6+ Pentacles on board |

## Gold Sinks

| Sink | Base Cost | Notes |
|------|-----------|-------|
| Buy Card | 3 gold | Plus `buyCostModifier`, minus synergy reduction (min 0) |
| Reroll Shop | 1 gold | Refreshes shop cards, unfreezes if frozen |
| Tier 2 Upgrade | 5 gold | Decreases by 1 each turn |
| Tier 3 Upgrade | 8 gold | Decreases by 1 each turn |
| Tier 4 Upgrade | 11 gold | Decreases by 1 each turn |
| Tier 5 Upgrade | 11 gold | Decreases by 1 each turn |
| Tier 6 Upgrade | 11 gold | Decreases by 1 each turn |

## Synergy Economy Effects

| Synergy | Threshold | Effect |
|---------|-----------|--------|
| Pentacles Tier 1 | 2 members | +1 gold when selling Pentacles cards |
| Pentacles Tier 2 | 4 members | +1 gold when buying Pentacles cards |
| Pentacles Tier 3 | 6 members | +2 gold when selling Pentacles cards |
| Pentacles + Cups combo | 2 each | Bonus gold effect |

## Cost Calculation

```
Effective Buy Cost = max(0, baseCost + buyCostModifier - synergyReduction)
Effective Sell Value = max(0, baseSellValue + sellValueModifier + synergyBonus)
```

- `baseCost` = 3 gold
- `baseSellValue` = 1 gold
- Synergy reduction calculated per-player using `SynergySnapshot`
- All costs clamped to minimum 0

## Coin Cap

- Maximum 10 gold at any time (`MAX_COINS = 10`)
- Enforced in the `coins` property setter with `Mathf.Clamp`
- Applies to all sources: turn income, selling, synergy bonuses

## Balance Philosophy

| Phase | Gold/Turn | Strategy |
|-------|-----------|----------|
| Early (turns 1-3) | 3-5 | Buy cheap cards, build early board |
| Mid (turns 4-7) | 6-8 | Upgrade tavern, target synergies |
| Late (turns 8+) | 10 (cap) | Reroll for upgrades, golden cards |

## Runtime Overrides

All economy values can be overridden via DevZone's `config.json`:
- `startingGold`, `goldPerTurn`, `maxGold`
- `baseBuyCost`, `goldenMultiplier`
- `tavernUpgradeCosts` per tier
- `tierCopies` and `shopSizes` per tier

## Related Documentation

- [Core Gameplay](core-gameplay.md) — full game loop
- [Tavern System](../../developer/tavern-system.md) — shop and pool mechanics
- [Synergy System](../../developer/synergy-system.md) — tribe bonuses
- [Runtime Data Loading](../../developer/runtime-data.md) — JSON config overrides
