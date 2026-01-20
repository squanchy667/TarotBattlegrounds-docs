# Tavern System

## Overview

The Tavern System manages the shop interface where players buy, sell, and reroll cards during the Recruit Phase.

---

## Core Mechanics

### Shop Operations

#### Buy Card (3 Gold)
- Removes card from shop
- Adds to player board (max 7)
- Deducts 3 gold

#### Sell Card (1 Gold)
- Removes card from board
- Returns to pool
- Adds 1 gold

#### Reroll Shop (1 Gold)
- Refreshes all shop cards
- Pulls from current tier pool
- Deducts 1 gold

---

## Gold Economy

### Per-Turn Gold System

| Turn | Gold Given | Gold Cap | Notes |
|------|------------|----------|-------|
| 1    | 3          | 3        | Starting gold |
| 2    | 4          | 4        | +1 per turn |
| 3    | 5          | 5        | Tier 2 unlocks |
| ...  | ...        | ...      | ... |
| 10   | 10         | 10       | **Capped at 10** |
| 11+  | 10         | 10       | No further increase |

### Gold Reset Logic
```csharp
void StartRecruitPhase()
{
    int expectedGold = Mathf.Min(currentTurn + 2, 10);
    gold = expectedGold;
}
```

---

## Tier Unlocking

### Unlock Progression

| Tier | Unlock Turn | Base Cost | Cost After Turn |
|------|-------------|-----------|-----------------|
| 2    | 3           | 5         | Decreases by 1/turn |
| 3    | 5           | 5         | Decreases by 1/turn |
| 4    | 7           | 5         | Decreases by 1/turn |
| 5    | 9           | 5         | Decreases by 1/turn |
| 6    | 11          | 5         | Decreases by 1/turn |

### Cost Formula
```csharp
int GetTierUpgradeCost(int tier)
{
    int baseCost = 5;
    int unlockTurn = (tier - 1) * 2 + 1; // 3, 5, 7, 9, 11
    int discount = Mathf.Max(0, currentTurn - unlockTurn);
    return Mathf.Max(0, baseCost - discount);
}
```

---

## Shop Size Scaling

| Player Tier | Shop Size | Cards Shown |
|-------------|-----------|-------------|
| 1           | 3         | 3 cards     |
| 2           | 4         | 4 cards     |
| 3           | 4         | 4 cards     |
| 4           | 5         | 5 cards     |
| 5           | 5         | 5 cards     |
| 6           | 6         | 6 cards     |

---

## Card Pool System

### Copy Counts (Per Tier)
- Tier 1: 16 copies
- Tier 2: 15 copies
- Tier 3: 13 copies
- Tier 4: 11 copies
- Tier 5: 9 copies
- Tier 6: 6 copies

### Generation Logic
```csharp
List<Card> GenerateShop()
{
    List<Card> pool = GetCardPoolForTier(currentTier);
    List<Card> shop = new List<Card>();
    
    int shopSize = GetShopSize();
    for (int i = 0; i < shopSize && pool.Count > 0; i++)
    {
        int randomIndex = Random.Range(0, pool.Count);
        shop.Add(pool[randomIndex]);
        pool.RemoveAt(randomIndex);
    }
    
    return shop;
}
```

---

## Related Documentation

- [Card System](card-system.md)
- [Economy Design](../product/game-design/economy.md)
