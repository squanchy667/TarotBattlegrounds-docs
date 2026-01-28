# Tarot Battlegrounds - Test Plan

This document outlines all tests that should be validated before making the game live. Tests can be automated (AI vs AI battles with log validation) or manual.

---

## 1. Core Mechanics

### 1.1 Card Operations
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Buy card | Manual | ⬜ | Card moves from shop to hand, costs 3 coins |
| Sell card from board | Manual | ⬜ | Card returns to pool, player gets 1 coin |
| Sell card from hand | Manual | ⬜ | Card returns to pool, player gets 1 coin |
| Play card to board | Manual | ⬜ | Card moves from hand to board at correct position |
| Card stat reset on sell | Manual | ⬜ | Buffed card returns to original stats when sold |
| Board limit (7 cards) | Manual | ⬜ | Cannot play more than 7 cards to board |
| Hand limit (10 cards) | Manual | ⬜ | Cannot buy when hand is full |

### 1.2 Economy
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Starting coins | Manual | ⬜ | Turn 1 = 3 coins, increases each turn (max 10) |
| Reroll cost | Manual | ⬜ | Reroll costs 1 coin |
| Upgrade cost reduction | Manual | ⬜ | Upgrade cost decreases by 1 each turn at current tier |
| Tavern upgrade | Manual | ⬜ | Upgrading increases tier and unlocks higher tier cards |

### 1.3 Shop System
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Shop refresh | Manual | ⬜ | New cards appear each turn |
| Tier-appropriate cards | Manual | ⬜ | Only cards ≤ tavern tier appear in shop |
| Shop size scaling | Manual | ⬜ | Shop size increases with tavern tier (3→4→4→5→5→6) |
| Shared card pool | Manual | ⬜ | Buying removes from pool, selling returns to pool |

---

## 2. Tribe Synergies

### 2.1 Cups (Healing/Protection)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Cups (2) | Manual | ⬜ | EndOfTurn: Heal adjacent cards for 1 |
| Cups (4) | Manual | ⬜ | EndOfTurn: Heal all Cups for 2 |
| Cups (6) | Manual | ⬜ | StartOfCombat: All friendly gain Aegis |

### 2.2 Wands (Buffs)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Wands (2) | Manual | ⬜ | EndOfTurn: Random friendly gets +1/+1 |
| Wands (4) | Manual | ⬜ | EndOfTurn: All Wands get +1/+1 |
| Wands (6) | Manual | ⬜ | StartOfCombat: All friendly get +2 attack |

### 2.3 Swords (Damage)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Swords (2) | Manual | ⬜ | StartOfCombat: Swords get +1 attack |
| Swords (4) | Manual | ⬜ | StartOfCombat: Swords deal +2 bonus damage |
| Swords (6) | Manual | ⬜ | Passive: Swords hit adjacent enemies (cleave) |

### 2.4 Pentacles (Economy)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Pentacles (2) | Manual | ⬜ | OnSell: +1 gold when selling Pentacles |
| Pentacles (4) | Manual | ⬜ | OnSell: +2 gold when selling Pentacles |
| Pentacles (6) | Manual | ⬜ | Passive: Pentacles cost 1 less to buy |

### 2.5 Cross-Tribe Combos
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Pentacles + Cups (2 each) | Manual | ⬜ | +1 gold at end of turn |
| Cups + Wands (2 each) | Manual | ⬜ | Healing also grants +1 attack |
| Swords + Pentacles (2 each) | Manual | ⬜ | Killing enemies grants +1 gold |
| Wands + Swords (2 each) | Manual | ⬜ | Attack buffs are doubled |

---

## 3. Card Abilities

### 3.1 Battlecry (On Play)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| BuffAdjacentAttack | Manual | ⬜ | Buff adjacent cards' attack |
| BuffAdjacentHealth | Manual | ⬜ | Buff adjacent cards' health |
| BuffAdjacentStats | Manual | ⬜ | Buff adjacent cards' attack and health |
| BuffAllFriendlyAttack | Manual | ⬜ | Buff all friendly cards' attack |
| GainAegis | Manual | ⬜ | Card gains Aegis shield |
| GainCoins | Manual | ⬜ | Player gains coins |
| No duplicate trigger | Manual | ⬜ | Battlecry triggers exactly once when played |

### 3.2 Deathrattle (On Death)
| Test | Type | Status | Description |
|------|------|--------|-------------|
| BuffRandomFriendly | Manual | ⬜ | Buff a random friendly card on death |
| DamageRandomEnemy | Manual | ⬜ | Deal damage to random enemy on death |
| DamageAllEnemies | Manual | ⬜ | Deal damage to all enemies on death |

### 3.3 OnAttack
| Test | Type | Status | Description |
|------|------|--------|-------------|
| BuffSelfAttack | Manual | ⬜ | Gain attack when attacking |
| BonusDamage | Manual | ⬜ | Deal bonus damage when attacking |
| Cleave | Manual | ⬜ | Damage adjacent enemies when attacking |

### 3.4 Passive Abilities
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Taunt | Manual | ⬜ | Must be attacked first |
| Aegis | Manual | ⬜ | Blocks first damage instance |

---

## 4. Combat System

### 4.1 Basic Combat
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Attacker selection | Auto | ⬜ | Attackers alternate, leftmost first |
| Defender selection | Auto | ⬜ | Random defender (respects Taunt) |
| Damage calculation | Auto | ⬜ | Both cards take damage equal to opponent's attack |
| Death processing | Auto | ⬜ | Cards at 0 or less health die |
| Combat resolution | Auto | ⬜ | Combat ends when one side is empty |

### 4.2 Combat Special Cases
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Taunt targeting | Auto | ⬜ | Taunt cards must be attacked first |
| Aegis blocks damage | Auto | ⬜ | First hit removes Aegis instead of dealing damage |
| Deathrattle triggers | Auto | ⬜ | Deathrattles trigger on death |
| Simultaneous death | Auto | ⬜ | Both cards can die in same attack |

### 4.3 Combat Outcome
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Winner determination | Auto | ⬜ | Player with remaining cards wins |
| Tie handling | Auto | ⬜ | No damage dealt on tie |
| Damage to player | Auto | ⬜ | Loser takes damage = remaining enemy board strength |

---

## 5. Game Flow

### 5.1 Turn Structure
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Recruit phase timing | Manual | ⬜ | Players have time to buy/play cards |
| Combat phase | Auto | ⬜ | Combat resolves correctly |
| Turn counter | Manual | ⬜ | Turn number increases each round |
| Coin refresh | Manual | ⬜ | Coins reset appropriately each turn |

### 5.2 Game End Conditions
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Player elimination | Auto | ⬜ | Player at 0 health is eliminated |
| Winner declaration | Auto | ⬜ | Last player standing wins |
| 2-player game end | Auto | ⬜ | Game ends when one player eliminated |
| 4-player game end | Auto | ⬜ | Game ends when one player remains |

---

## 6. AI System

### 6.1 AI Behavior
| Test | Type | Status | Description |
|------|------|--------|-------------|
| AI buys cards | Auto | ⬜ | AI purchases cards during recruit phase |
| AI plays cards | Auto | ⬜ | AI plays cards to board |
| AI upgrades tavern | Auto | ⬜ | AI upgrades at appropriate times |
| AI uses reroll | Auto | ⬜ | AI rerolls when beneficial |

### 6.2 AI Difficulty Levels
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Easy AI | Auto | ⬜ | Makes suboptimal decisions, beatable |
| Medium AI | Auto | ⬜ | Makes decent decisions |
| Hard AI | Auto | ⬜ | Makes strong decisions, challenging |

### 6.3 AI Balance
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Easy vs Easy (100 games) | Auto | ⬜ | ~50% win rate each |
| Easy vs Medium (100 games) | Auto | ⬜ | Medium wins majority |
| Easy vs Hard (100 games) | Auto | ⬜ | Hard wins vast majority |
| Medium vs Hard (100 games) | Auto | ⬜ | Hard wins majority |

---

## 7. UI/UX

### 7.1 Card Display
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Card stats visible | Manual | ⬜ | Attack/health clearly shown |
| Card tribe visible | Manual | ⬜ | Tribe indicator visible |
| Card tier visible | Manual | ⬜ | Tier stars/indicator visible |
| Card ability tooltip | Manual | ⬜ | Hover shows ability description |
| Buff indicators | Manual | ⬜ | Buffed stats shown differently |

### 7.2 Board State
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Shop cards display | Manual | ⬜ | All shop cards visible |
| Hand cards display | Manual | ⬜ | All hand cards visible |
| Board cards display | Manual | ⬜ | All board cards visible in position |
| Synergy indicators | Manual | ⬜ | Active synergies shown |

### 7.3 Player Info
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Coins display | Manual | ⬜ | Current coins shown |
| Health display | Manual | ⬜ | Current health shown |
| Tavern tier display | Manual | ⬜ | Current tier shown |
| Upgrade cost display | Manual | ⬜ | Upgrade cost shown |

### 7.4 Interactions
| Test | Type | Status | Description |
|------|------|--------|-------------|
| Card selection | Manual | ⬜ | Click to select works |
| Card deselection | Manual | ⬜ | Click elsewhere deselects |
| Button responsiveness | Manual | ⬜ | Buy/Sell/Play/Reroll buttons work |
| Drag and drop (if impl) | Manual | ⬜ | Drag cards to reposition |

---

## 8. Automated Test Suite

### 8.1 AI vs AI Battle Test
```csharp
// Proposed test structure
public class AIBattleTest
{
    public void RunBattleSimulation(int numGames, AIDifficulty ai1, AIDifficulty ai2)
    {
        // 1. Create game with 2 AI players
        // 2. Run game to completion
        // 3. Log winner, turns taken, final health
        // 4. Aggregate statistics
    }

    public void ValidateCombatLogs()
    {
        // 1. Parse combat logs
        // 2. Verify damage calculations
        // 3. Verify ability triggers
        // 4. Verify death processing
    }
}
```

### 8.2 Expected Log Patterns
```
// Synergy activation
[SynergyManager] Player X: Tribe tier active: N members, threshold M
[Synergy] Player X: CardName gains +N stat (old -> new)

// Ability trigger
[Ability] CardName triggers AbilityType: Description
[AbilityType] Effect applied

// Combat
[Combat] Attacker: CardA (X/Y) vs Defender: CardB (X/Y)
[Combat] CardA takes N damage (health: X -> Y)
[Combat] CardB dies
```

---

## 9. Performance Tests

| Test | Type | Status | Description |
|------|------|--------|-------------|
| 100 turn game | Auto | ⬜ | Game remains responsive |
| Large card pool | Auto | ⬜ | No lag with 400+ cards in pool |
| Rapid actions | Manual | ⬜ | Quick clicks don't break UI |
| Memory usage | Auto | ⬜ | No memory leaks over time |

---

## 10. Edge Cases

| Test | Type | Status | Description |
|------|------|--------|-------------|
| Empty board combat | Auto | ⬜ | Handled gracefully |
| 0 coins actions | Manual | ⬜ | Buttons disabled appropriately |
| Max tier reached | Manual | ⬜ | Upgrade button disabled at tier 6 |
| Empty card pool | Auto | ⬜ | Shop shows available cards only |
| All same tribe | Manual | ⬜ | Highest tier synergy only |
| Multi-tribe cards | Manual | ⬜ | Count for all tribes they belong to |

---

## Test Execution Checklist

### Pre-Release
- [ ] All manual tests passed
- [ ] AI vs AI simulation (1000 games) completed
- [ ] No critical bugs in logs
- [ ] Performance acceptable on target hardware
- [ ] UI/UX review completed

### Sign-off
- [ ] Developer testing complete
- [ ] QA testing complete (if applicable)
- [ ] Ready for release

---

## Notes

- **Auto** tests can be implemented as Unity Test Framework tests or standalone simulation scripts
- **Manual** tests require human verification
- Status: ⬜ Not tested | 🟡 In progress | ✅ Passed | ❌ Failed

Last Updated: 2026-01-28
