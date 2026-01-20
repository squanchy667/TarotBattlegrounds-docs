# Combat System

## Overview

The Combat System simulates turn-based auto-battles between two player boards, determining winners based on remaining minion strength and applying appropriate damage to player health.

---

## Combat Flow

```
┌─────────────────────────────────────────────────────────┐
│                   Combat Phase Start                     │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │  Clone Both Boards    │ (Preserve originals)
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │ Determine First       │ (Random: "ofek" or "jaya")
         │ Attacker              │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │   Turn-Based Loop     │
         │   ┌───────────────┐   │
         │   │ Attack Phase  │   │
         │   │ - Attacker    │   │
         │   │ - Defender    │   │
         │   └───────┬───────┘   │
         │           │           │
         │           ▼           │
         │   ┌───────────────┐   │
         │   │ Remove Dead   │   │
         │   │ Minions (HP≤0)│   │
         │   └───────┬───────┘   │
         │           │           │
         │           ▼           │
         │   ┌───────────────┐   │
         │   │ Check Boards  │   │
         │   │ Empty?        │   │
         │   └───────┬───────┘   │
         │           │           │
         │      Yes  │  No       │
         │      ↓    └──↑        │
         └──────┼───────────────┘
                │
                ▼
         ┌───────────────────────┐
         │ Calculate Remaining   │
         │ Board Strength        │
         │ (Σ attack + health)   │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │ Apply Damage to Loser │
         │ (Capped by Turn/Tier) │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │ Update Player Health  │
         └───────────┬───────────┘
                     │
                     ▼
         ┌───────────────────────┐
         │ Determine Outcome     │
         │ (Win/Loss/Tie)        │
         └───────────┬───────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────────┐
│                  Return to GameManager                  │
└────────────────────────────────────────────────────────┘
```

---

## Key Mechanics

### 1. Board Cloning

**Why?** Preserve original board state for debugging and prevent accidental modifications.

```csharp
public List<Card> CloneBoard(List<Card> original)
{
    List<Card> cloned = new List<Card>();
    foreach (Card card in original)
    {
        // Create instance copies (not references)
        Card copy = Instantiate(card);
        cloned.Add(copy);
    }
    return cloned;
}
```

---

### 2. First Attacker Selection

**Logic**: Random selection between "ofek" and "jaya" (player names - will be generalized).

```csharp
string DetermineFirstAttacker()
{
    return Random.value > 0.5f ? "ofek" : "jaya";
}
```

**Balance Impact**: Slight advantage to first attacker (attacks first each turn).

---

### 3. Turn-Based Attack Loop

```csharp
public void SimulateBattle(List<Card> playerBoard, List<Card> enemyBoard)
{
    List<Card> pBoard = CloneBoard(playerBoard);
    List<Card> eBoard = CloneBoard(enemyBoard);
    
    string attacker = DetermineFirstAttacker();
    
    while (pBoard.Count > 0 && eBoard.Count > 0)
    {
        if (attacker == "ofek")
        {
            // Player attacks enemy
            AttackMinion(pBoard[0], eBoard[0]);
            if (eBoard[0].health <= 0) eBoard.RemoveAt(0);
            
            // Enemy counterattacks
            if (eBoard.Count > 0 && pBoard.Count > 0)
            {
                AttackMinion(eBoard[0], pBoard[0]);
                if (pBoard[0].health <= 0) pBoard.RemoveAt(0);
            }
        }
        else
        {
            // Enemy attacks player
            AttackMinion(eBoard[0], pBoard[0]);
            if (pBoard[0].health <= 0) pBoard.RemoveAt(0);
            
            // Player counterattacks
            if (pBoard.Count > 0 && eBoard.Count > 0)
            {
                AttackMinion(pBoard[0], eBoard[0]);
                if (eBoard[0].health <= 0) eBoard.RemoveAt(0);
            }
        }
        
        // Check for board wipe
        if (pBoard.Count == 0 || eBoard.Count == 0) break;
    }
    
    // Calculate damage based on remaining board
    int playerStrength = CalculateBoardStrength(pBoard);
    int enemyStrength = CalculateBoardStrength(eBoard);
    
    ApplyDamage(playerStrength, enemyStrength);
}
```

---

### 4. Damage Calculation

```csharp
int CalculateBoardStrength(List<Card> board)
{
    int strength = 0;
    foreach (Card card in board)
    {
        strength += card.attack + card.health;
    }
    return strength;
}
```

**Example**:
- Remaining Board: [3/2, 5/4, 1/1]
- Strength = (3+2) + (5+4) + (1+1) = **19 damage**

---

### 5. Damage Application

```csharp
void ApplyDamage(int playerStrength, int enemyStrength)
{
    if (playerStrength > enemyStrength)
    {
        int damage = CalculateCappedDamage(playerStrength - enemyStrength);
        enemyHealth -= damage;
        Debug.Log($"Enemy takes {damage} damage");
    }
    else if (enemyStrength > playerStrength)
    {
        int damage = CalculateCappedDamage(enemyStrength - playerStrength);
        playerHealth -= damage;
        Debug.Log($"Player takes {damage} damage");
    }
    else
    {
        Debug.Log("Tie - no damage");
    }
}

int CalculateCappedDamage(int rawDamage)
{
    int cap = GetDamageCap();
    return Mathf.Min(rawDamage, cap);
}

int GetDamageCap()
{
    // First 3 turns: Cap at 5 damage
    if (currentTurn <= 3) return 5;
    
    // After turn 3: Adjust based on highest tier in combat
    // (Future implementation - currently returns 5)
    return 5 + (GetHighestTierInCombat() - 1);
}
```

---

## Health System

### Starting Health
- **Each Player**: 40 HP

### Damage Cap
| Turn Range | Damage Cap | Reason |
|------------|------------|--------|
| Turns 1-3  | 5 damage   | Early game protection |
| Turn 4+    | 5 + (tier - 1) | Scales with board power |

**Example**:
- Turn 2 combat: Max 5 damage
- Turn 7 combat (Tier 4 boards): Max 8 damage (5 + 3)

---

## Win Conditions

### Player Wins
- Opponent health ≤ 0

### Tie
- Both players reduced to ≤ 0 in same turn
- Both boards wiped with equal strength

### Loss
- Player health ≤ 0

---

## Combat Outcomes

```csharp
public string DetermineWinner()
{
    if (playerHealth <= 0 && enemyHealth <= 0)
        return "tie";
    else if (playerHealth <= 0)
        return "enemy";
    else if (enemyHealth <= 0)
        return "player";
    else
        return "ongoing";
}
```

---

## Current Balance (Phase 3 Testing)

### Test Results (50+ Runs)
- **"Ofek" Wins**: 0% (balance issue identified)
- **"Jaya" Wins**: 52%
- **Ties**: 48%

### Known Issues
- First attacker advantage too strong
- Board population imbalance (needs more cards)
- Tribe synergies not yet implemented

---

## Planned Enhancements

### Phase 3 Completion
- ✅ Multi-minion attack support (currently 1v1 style)
- ✅ Taunt mechanics (priority targeting)
- ✅ Divine Shield (absorbs first damage)
- ✅ Tribe synergies (Pentacles +gold, Cups +heal)

### Phase 4 (UI)
- Combat animations
- Health bar updates
- Damage number pop-ups
- Attack indicators

### Phase 5 (Multiplayer)
- Opponent board preview
- Combat replay system
- Simultaneous combat resolution

---

## Testing Combat

### Manual Test

```csharp
void TestCombat()
{
    // Create test boards
    List<Card> playerBoard = new List<Card>
    {
        CreateCard("Ace of Pentacles", 1, 1),
        CreateCard("Three of Swords", 3, 2)
    };
    
    List<Card> enemyBoard = new List<Card>
    {
        CreateCard("Knight of Swords", 4, 4),
        CreateCard("Five of Cups", 5, 5)
    };
    
    // Simulate battle
    CombatManager.SimulateBattle(playerBoard, enemyBoard);
    
    // Check results
    Debug.Log($"Player HP: {playerHealth}");
    Debug.Log($"Enemy HP: {enemyHealth}");
}
```

### Automated Testing (100+ Runs)

```csharp
void RunCombatSimulations(int runs)
{
    int playerWins = 0;
    int enemyWins = 0;
    int ties = 0;
    
    for (int i = 0; i < runs; i++)
    {
        ResetHealth();
        SimulateBattle(playerBoard, enemyBoard);
        
        string outcome = DetermineWinner();
        if (outcome == "player") playerWins++;
        else if (outcome == "enemy") enemyWins++;
        else ties++;
    }
    
    Debug.Log($"Results over {runs} runs:");
    Debug.Log($"Player Wins: {playerWins} ({playerWins * 100 / runs}%)");
    Debug.Log($"Enemy Wins: {enemyWins} ({enemyWins * 100 / runs}%)");
    Debug.Log($"Ties: {ties} ({ties * 100 / runs}%)");
}
```

---

## Debugging Tips

### Enable Combat Logging

```csharp
public bool debugMode = true;

void AttackMinion(Card attacker, Card defender)
{
    if (debugMode)
    {
        Debug.Log($"{attacker.cardName} ({attacker.attack}/{attacker.health}) " +
                  $"attacks {defender.cardName} ({defender.attack}/{defender.health})");
    }
    
    defender.health -= attacker.attack;
    
    if (debugMode)
    {
        Debug.Log($"{defender.cardName} now at {defender.health} HP");
    }
}
```

### Visualize Board State

```csharp
void LogBoardState(List<Card> board, string owner)
{
    Debug.Log($"{owner}'s Board:");
    foreach (Card card in board)
    {
        Debug.Log($"  - {card.cardName}: {card.attack}/{card.health}");
    }
}
```

---

## Related Documentation

- [Card System](card-system.md) - Card properties and stats
- [Game Manager](game-manager.md) - Combat phase integration
- [Combat Rules](../product/game-design/combat-rules.md) - Design reasoning
