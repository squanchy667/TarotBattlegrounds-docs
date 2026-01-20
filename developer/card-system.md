# Card System

## Overview

The Card System in Tarot Battlegrounds is built on Unity's **ScriptableObject** architecture, providing a data-driven approach to card definitions and mechanics.

---

## ScriptableObject Implementation

### Card.cs Structure

```csharp
[CreateAssetMenu(fileName = "New Card", menuName = "Tarot/Card")]
public class Card : ScriptableObject
{
    [Header("Basic Info")]
    public string cardName;
    public int tier;              // 1-6
    
    [Header("Tarot Theme")]
    public string tribe;          // Pentacles, Cups, Swords, Wands
    
    [Header("Stats")]
    public int attack;
    public int health;
    
    [Header("Abilities")]
    public string ability;        // Ability description
    
    [Header("Visuals")]
    public Sprite cardArt;
}
```

---

## Tier System

### Tier Definitions

| Tier | Unlock Turn | Base Upgrade Cost | Shop Size | Card Examples |
|------|-------------|-------------------|-----------|---------------|
| 1    | Start       | N/A              | 3         | Ace of Pentacles |
| 2    | Turn 3      | 5 gold           | 4         | Three of Swords, Four of Wands |
| 3    | Turn 5      | 5 gold           | 4         | Five of Cups |
| 4    | Turn 7      | 5 gold           | 5         | Knight of Swords |
| 5    | Turn 9      | 5 gold           | 5         | (To be added) |
| 6    | Turn 11     | 5 gold           | 6         | (To be added) |

### Tier Unlocking Logic

```csharp
int GetTierUpgradeCost(int currentTier)
{
    int baseCost = 5;
    int discount = currentTurn - GetTierUnlockTurn(currentTier + 1);
    return Mathf.Max(0, baseCost - discount);
}

int GetTierUnlockTurn(int tier)
{
    // Tier 1: Start, Tier 2: Turn 3, Tier 3: Turn 5, etc.
    return tier == 1 ? 0 : (tier - 1) * 2 + 1;
}
```

**Cost Reduction**: After the unlock turn, the upgrade cost decreases by 1 gold per turn.

---

## Tribe System (Suits)

### Four Tribes (Tarot Suits)

#### 1. **Pentacles** (Earth Element)
- **Theme**: Wealth, resources, material gain
- **Synergy**: +1 gold on sell (planned)
- **Example Cards**: Ace of Pentacles

#### 2. **Cups** (Water Element)
- **Theme**: Healing, support, friendly effects
- **Synergy**: Heal friendly minions for 2 (planned)
- **Example Cards**: Five of Cups

#### 3. **Swords** (Air Element)
- **Theme**: Aggression, direct damage
- **Synergy**: (To be designed)
- **Example Cards**: Three of Swords, Knight of Swords

#### 4. **Wands** (Fire Element)
- **Theme**: Action, energy, buff effects
- **Synergy**: (To be designed)
- **Example Cards**: Four of Wands

---

## Card Definitions

### Implemented Cards (Phase 2 Complete)

#### Ace of Pentacles
- **Tier**: 1
- **Tribe**: Pentacles
- **Attack**: 1
- **Health**: 1
- **Ability**: None (basic minion)

#### Three of Swords
- **Tier**: 2
- **Tribe**: Swords
- **Attack**: 3
- **Health**: 2
- **Ability**: (Placeholder)

#### Four of Wands
- **Tier**: 2
- **Tribe**: Wands
- **Attack**: 2
- **Health**: 4
- **Ability**: (Placeholder)

#### Knight of Swords
- **Tier**: 4
- **Tribe**: Swords
- **Attack**: 4
- **Health**: 4
- **Ability**: (Placeholder)

#### Five of Cups
- **Tier**: 3
- **Tribe**: Cups
- **Attack**: 5
- **Health**: 5
- **Ability**: "Heal friendly minion for 2" (placeholder)

---

## Creating New Cards

### Step-by-Step Guide

1. **Right-Click in Project** → `Create` → `Tarot` → `Card`
2. **Name the Asset**: e.g., `SixOfPentacles`
3. **Configure Properties**:
   ```
   Card Name: Six of Pentacles
   Tier: 3
   Tribe: Pentacles
   Attack: 6
   Health: 3
   Ability: "Give adjacent minions +1/+1"
   ```
4. **Add Card Art**: Drag sprite into `Card Art` field
5. **Save Asset**: Store in `Assets/Data/Cards/`

### Importing from CSV (CardImporter.cs)

```csharp
// CSV Format:
// Name,Tier,Tribe,Attack,Health,Ability
// "Seven of Cups",4,"Cups",3,7,"Draw a card"

public void ImportCardsFromCSV(string csvPath)
{
    string[] lines = File.ReadAllLines(csvPath);
    foreach (string line in lines.Skip(1)) // Skip header
    {
        string[] fields = line.Split(',');
        Card newCard = CreateInstance<Card>();
        newCard.cardName = fields[0];
        newCard.tier = int.Parse(fields[1]);
        newCard.tribe = fields[2];
        newCard.attack = int.Parse(fields[3]);
        newCard.health = int.Parse(fields[4]);
        newCard.ability = fields[5];
        
        AssetDatabase.CreateAsset(newCard, $"Assets/Data/Cards/{newCard.cardName}.asset");
    }
}
```

---

## Card Pool Management

### Generation Logic (TavernManager)

```csharp
public List<Card> GenerateCardPool()
{
    List<Card> pool = new List<Card>();
    
    // Load all cards from Resources
    Card[] allCards = Resources.LoadAll<Card>("Cards");
    
    // Filter by current tier (max tier unlocked)
    foreach (Card card in allCards)
    {
        if (card.tier <= currentTier)
        {
            // Add multiple copies based on tier
            int copies = GetCopiesForTier(card.tier);
            for (int i = 0; i < copies; i++)
            {
                pool.Add(card);
            }
        }
    }
    
    return pool;
}

int GetCopiesForTier(int tier)
{
    // Hearthstone-style copy counts
    switch (tier)
    {
        case 1: return 16;
        case 2: return 15;
        case 3: return 13;
        case 4: return 11;
        case 5: return 9;
        case 6: return 6;
        default: return 10;
    }
}
```

### Shop Refresh Logic

```csharp
public void RefreshTavernShop()
{
    currentShop.Clear();
    
    List<Card> pool = GenerateCardPool();
    int shopSize = GetShopSize(); // 3-6 based on tier
    
    // Randomly select cards from pool
    for (int i = 0; i < shopSize && pool.Count > 0; i++)
    {
        int randomIndex = Random.Range(0, pool.Count);
        currentShop.Add(pool[randomIndex]);
        pool.RemoveAt(randomIndex); // Prevent duplicates in same shop
    }
}
```

---

## Card Interactions

### Buy Mechanic

```csharp
public bool BuyCard(Card card)
{
    if (gold < 3) return false;           // Not enough gold
    if (board.Count >= 7) return false;   // Board full
    
    gold -= 3;
    board.Add(card);
    currentShop.Remove(card);
    
    Debug.Log($"Bought {card.cardName} for 3 gold");
    return true;
}
```

### Sell Mechanic

```csharp
public bool SellCard(Card card)
{
    if (!board.Contains(card)) return false;
    
    gold += 1;
    board.Remove(card);
    
    Debug.Log($"Sold {card.cardName} for 1 gold");
    return true;
}
```

### Reroll Mechanic

```csharp
public bool RerollShop()
{
    if (gold < 1) return false;
    
    gold -= 1;
    RefreshTavernShop();
    
    Debug.Log("Rerolled shop for 1 gold");
    return true;
}
```

---

## Card Abilities (Planned)

### Ability System Design

```csharp
public enum AbilityTrigger
{
    OnPlay,           // When placed on board
    OnSell,           // When sold
    OnAttack,         // When attacking
    OnDamaged,        // When taking damage
    OnDeath,          // When health reaches 0
    StartOfTurn,      // Each recruit phase
    EndOfTurn         // Before combat
}

public abstract class CardAbility : ScriptableObject
{
    public AbilityTrigger trigger;
    public abstract void Execute(Card thisCard, BoardManager board);
}
```

### Example Abilities

#### Pentacles Synergy: "Profit"
```csharp
// On Sell: Gain +1 gold (total 2 instead of 1)
public class ProfitAbility : CardAbility
{
    public override void Execute(Card thisCard, BoardManager board)
    {
        if (trigger == AbilityTrigger.OnSell)
        {
            board.player.gold += 1; // Bonus gold
        }
    }
}
```

#### Cups Synergy: "Healing Aura"
```csharp
// Start of Turn: Heal adjacent minions for 2
public class HealingAuraAbility : CardAbility
{
    public override void Execute(Card thisCard, BoardManager board)
    {
        if (trigger == AbilityTrigger.StartOfTurn)
        {
            List<Card> adjacent = board.GetAdjacentCards(thisCard);
            foreach (Card card in adjacent)
            {
                card.health = Mathf.Min(card.health + 2, card.maxHealth);
            }
        }
    }
}
```

---

## Card Visuals

### Sprite Requirements
- **Resolution**: 512x512 or 1024x1024
- **Format**: PNG with transparency
- **Style**: Tarot card aesthetic (ornate borders, symbolic imagery)

### UI Layout (Planned Phase 4)
```
┌─────────────┐
│   Card Art  │  ← Sprite
├─────────────┤
│ Card Name   │  ← Text
├─────────────┤
│ [Tribe Icon]│  ← Pentacles/Cups/Swords/Wands
├─────────────┤
│ ⚔️ Atk  ❤️ HP│  ← Stats
└─────────────┘
```

---

## Testing Cards

### CardTest.cs Example

```csharp
public class CardTest : MonoBehaviour
{
    public Card testCard;
    
    void Start()
    {
        // Log card properties
        Debug.Log($"Testing: {testCard.cardName}");
        Debug.Log($"Tier: {testCard.tier}, Tribe: {testCard.tribe}");
        Debug.Log($"Stats: {testCard.attack}/{testCard.health}");
        Debug.Log($"Ability: {testCard.ability}");
    }
}
```

---

## Best Practices

### ✅ DO
- Use descriptive card names matching tarot cards
- Balance attack/health around tier (Tier 1: ~2 total stats, Tier 6: ~12+ total stats)
- Test new cards in isolation before adding to pool
- Document ability mechanics in the `ability` string field

### ❌ DON'T
- Hardcode card stats in C# scripts
- Create cards without assigning a tribe
- Exceed tier 6 (balancing concerns)
- Forget to add card art (visual clarity is key)

---

## Future Enhancements

### Phase 4 (UI/Polish)
- Visual card effects and animations
- Card rarity system (common/rare/legendary)
- Golden card variants

### Phase 5 (Multiplayer)
- Network-synchronized card state
- Opponent board visibility
- Card discover mechanics (3 random options)

---

## Related Documentation

- [Tavern System](tavern-system.md) - Shop and economy mechanics
- [Combat System](combat-system.md) - How cards interact in battle
- [Tarot Mechanics](../product/game-design/tarot-mechanics.md) - Lore and theme
