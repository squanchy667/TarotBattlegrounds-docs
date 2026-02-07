# Core Gameplay

## Game Concept

2-8 player auto-battler with a Tarot theme. Players recruit cards during a timed phase, then watch their boards fight automatically. Last player standing wins.

## Gameplay Loop

```
1. Recruit Phase (35s, configurable)
   ├── Buy cards from shop (3 gold base, synergy discounts possible)
   ├── Sell cards (1 gold + synergy bonuses)
   ├── Reroll shop (1 gold)
   ├── Play cards from hand to board (max 7)
   ├── Reorder board (click-to-swap)
   ├── Upgrade tavern tier (unlocks higher-tier cards)
   ├── Freeze shop (keep current cards for next turn)
   └── End turn early (optional)

2. Combat Phase (Auto)
   ├── Pairwise matchmaking (avoids recent opponents)
   ├── Board vs board simulation
   ├── Pre-combat synergy buffs (StartOfCombat triggers)
   ├── Alternating attacks (left-to-right)
   ├── Abilities trigger: OnAttack, Deathrattle, Guardian
   ├── Damage = surviving cards + winner's tavern tier
   └── Eliminated at 0 health

3. Repeat until one player remains
```

## Win Condition

Last player standing. Players are eliminated when health reaches 0 and ranked by elimination order.

## Core Mechanics

### Cards
- 30 cards across 6 tiers (5 per tier)
- 4 tribes: Pentacles, Cups, Swords, Wands
- Multi-tribe cards exist (dual and triple)
- Abilities: Battlecry, Deathrattle, OnAttack, Guardian

### Golden Cards (Triples)
- Collect 3 copies of the same card to auto-merge into a golden version
- Golden cards have doubled attack and health
- Merging grants a Discovery reward: pick 1 of 3 cards from the next tier

### Synergies
- Tribe bonuses activate at 2/4/6 tribe members on board
- Pentacles: economy bonuses (gold on sell/buy)
- Cups: healing and shields
- Swords: attack buffs
- Wands: stat buffs and adjacent bonuses
- Cross-tribe combos when two specific tribes are both active

### Tavern Tiers
| Tier | Upgrade Cost | Shop Size | Cards in Tier |
|------|-------------|-----------|---------------|
| 1 | - | 3 | 5 cards |
| 2 | 5 gold | 4 | 5 cards |
| 3 | 8 gold | 4 | 5 cards |
| 4 | 11 gold | 5 | 5 cards |
| 5 | 11 gold | 5 | 5 cards |
| 6 | 11 gold | 6 | 5 cards |

Upgrade cost decreases by 1 gold each turn automatically.

### Economy
- Starting gold: 3, increases by 1 per turn, capped at 10
- Buy: 3 gold (base) + card modifier - synergy reduction
- Sell: 1 gold + card modifier + synergy bonus
- Reroll: 1 gold

## Player Counts

- **Solo**: 4, 6, or 8 players (1 human + AI fill)
- **Multiplayer**: 2-8 players (humans + AI fill empty slots)
- AI difficulty: Easy, Medium, Hard

## Related Documentation

- [Economy](economy.md) — detailed gold flow
- [Card System](../../developer/card-system.md) — card definitions
- [Combat System](../../developer/combat-system.md) — battle mechanics
- [Synergy System](../../developer/synergy-system.md) — tribe bonuses
