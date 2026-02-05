# TAROT BATTLEGROUNDS — Official Rules Sheet v1.0
## Source: Extracted from PLAN.md (Jan 30, 2026) + known-issues.md fixes (Feb 2, 2026)

---

## 1. GAME OVERVIEW

Tarot Battlegrounds is a 4-player auto-battler where players recruit tarot cards from a tavern, build synergistic boards using tribal mechanics, and battle in automated combat. Last player standing wins.

**Design Principle:** The core engine is 100% theme-agnostic. Cards = ID + Stats + AbilityType + TribeTag[]. All text/names/art loaded from ScriptableObject data packs. A "skin" = new card database + art + UI theme = zero code changes.

---

## 2. GAME LOOP

```
┌──────────────────┐
│  RECRUIT PHASE    │  ← Player makes decisions
│  • Buy cards (3g) │
│  • Sell cards (1g)│
│  • Reroll shop (1g)│
│  • Upgrade tier   │
│  • Position board │
│  • Freeze shop    │
│  • End turn early │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  COMBAT PHASE     │  ← Automated
│  • Auto-battle    │
│  • Damage to loser│
│  • Eliminate 0 HP │
└────────┬─────────┘
         │
         ▼
    (Repeat until 1 player remains)
```

---

## 3. RECRUIT PHASE

### 3.1 Economy
| Action | Cost |
|--------|------|
| Buy a card | 3 gold (reduced by Pentacles synergy) |
| Sell a card | 1 gold (increased by Pentacles synergy) |
| Reroll shop | 1 gold |
| Upgrade tavern tier | 5 gold (decreases by 1 per turn) |

- **Gold per turn:** 3 base + bonuses from synergies
- **Gold cap:** 10 (enforced via property setter with Mathf.Clamp)
- **Sell value minimum:** 0 (clamped, cannot go negative)
- **Buy cost minimum:** 0 (clamped)

### 3.2 Shop System
- Shop offers cards from current tier and below
- Reroll refreshes all offerings
- **Freeze:** Toggle to keep current shop next turn (auto-unfreezes after next recruit phase starts; clears on manual reroll)
- Shop pool is shared — cards bought are removed from pool, sold cards return

### 3.3 Board Rules
- Maximum 7 cards on board per player
- Cards can be repositioned (click-to-swap)
- Board position matters for adjacency effects (Cups healing, Cleave)

### 3.4 Triple / Fusion System
- Collecting 3 copies of the same card → fuses into 1 **Golden** card
- Golden card: doubled attack and health (NOT doubled abilityValue)
- Golden creation triggers Discovery: choose 1 of 3 cards from next higher tier
- Golden cards are consumed when sold (NOT returned to pool)
- Discovery cards are reserved from pool; unchosen ones returned via ReturnDiscoveryCards()

---

## 4. CARD SYSTEM

### 4.1 Card Structure (ScriptableObject)
```
Card:
  - ID (int)
  - Name (string)
  - Attack (int)
  - Health (int)
  - Tier (1-6)
  - TribeType[] tribes (multi-tribe support)
  - AbilityTrigger abilityTrigger
  - AbilityEffect abilityEffect
  - int abilityValue
  - bool isGolden
```

### 4.2 Card Tiers
| Tier | Unlock | Cards Per Tier |
|------|--------|---------------|
| 1 | Start | 5 |
| 2 | Turn 2+ | 5 |
| 3 | Turn 4+ | 5 |
| 4 | Turn 6+ | 5 |
| 5 | Turn 8+ | 5 |
| 6 | Turn 10+ | 5 |

- 30 total cards in pool
- Tier 6 upgrade shows "MAX" (not cost)

### 4.3 Ability Triggers
```csharp
public enum AbilityTrigger {
    None,
    Battlecry,      // When played from hand
    Deathrattle,    // When this card dies
    OnAttack,       // When this card attacks
    OnDamaged,      // When this card takes damage
    StartOfCombat,  // Before combat begins
    EndOfTurn       // At end of recruit phase
}
```

### 4.4 Ability Effects
- **Guardian (Taunt):** Must be attacked first
- **Aegis (Shield):** Blocks first instance of damage
- **Echo (Deathrattle):** Effect triggers on death
- **BuffAttack / BuffHealth / BuffStats:** Stat modifications
- **BonusDamage:** Extra damage on attack (via temp attack boost, NOT separate damage)
- **Cleave:** Damage adjacent cards (uses abilityValue, not hardcoded)
- **Discover:** Offer 3 cards from a tier to choose from

---

## 5. TRIBES & SYNERGIES

### 5.1 Tribe Types
```csharp
public enum TribeType { None, Pentacles, Cups, Swords, Wands }
```

Cards can have 1, 2, or 3 tribes (multi-tribe). Each tribe counts toward ALL its types.

### 5.2 Synergy Thresholds

| Tribe | Theme | 2 Units | 4 Units | 6 Units | Combo Tribe | Combo Effect |
|-------|-------|---------|---------|---------|-------------|--------------|
| **Pentacles** | Economy | +1 gold on sell | +2 gold on sell | -1 cost on buy | Cups | +1 gold/turn |
| **Cups** | Healing | Heal adjacent 1 | Heal tribe 2 | Shield all 2 | Wands | Heals buff attack |
| **Swords** | Aggro | +1 attack | +2 bonus damage | Cleave | Pentacles | Kills give gold |
| **Wands** | Buffs | +1/+1 random | +1/+1 tribe | +2 attack all | Swords | Double attack buffs |

### 5.3 Synergy System Rules
- Synergies calculated **per-player** (not global — M1 fix)
- Recalculated on board change (OnBoardChanged event)
- SynergyManager.CalculateSynergies(Player) returns snapshot
- Cross-tribe combo activates when BOTH tribes meet their minimum threshold
- "BuffAllFriendly" includes the card itself (Hearthstone standard)

---

## 6. COMBAT SYSTEM

### 6.1 Combat Flow
1. Round-robin matchmaking (with recentOpponents tracking to avoid rematches)
2. Each player's board is cloned for combat (originals preserved)
3. Random first attacker
4. Turn-based: each card attacks in board order (left → right)
5. Counterattack mechanics
6. Abilities trigger at appropriate times
7. Combat continues until one side is eliminated
8. Losing player takes damage = tier of winning player + surviving units count
9. Player at 0 HP is eliminated

### 6.2 Combat Rules
- **No damage cap** (was hardcoded 5, removed in audit round 2)
- **Death queue:** Deaths processed via ProcessDeaths() with cascade support (deathrattles can cause more deaths)
- **Guardian (Taunt):** Must be attacked first; checked via both effectType AND abilityEffect
- **Aegis (Shield):** Blocks first damage instance; OnAttack triggers only inside else branch (not when blocked)
- **Cleave:** Damages adjacent cards using abilityValue; respects Aegis on adjacent targets
- **Clone cleanup:** CleanupCombatClones() called after combat to prevent memory leak
- **Combat log:** Filtered to local player's battle (M7)

### 6.3 Damage Calculation
```
damageToLoser = winnerPlayerTier + aliveUnitsOnWinnerBoard
// Uses Count(c => c.health > 0), not raw .Count
// Per-player tier (not same tier for both sides)
```

---

## 7. AI SYSTEM

### 7.1 Difficulty Levels
| Level | Buy Logic | Tier Logic | Positioning |
|-------|-----------|-----------|-------------|
| Easy | Random buys | Slow upgrades | Random |
| Medium | Stat priority | Standard curve | Guardian front |
| Hard | Synergy-aware | Aggressive tier | Optimized |

### 7.2 AI Rules
- AI buy loop checks `coins >= minimumPossibleCost` (not hardcoded 3)
- AI uses GetCostReduction() for synergy-discounted cards
- Hard AI Sort() fires NotifyBoardChanged() (not silent mutation)
- Medium AI guardian swap checks ability system AND fires board events

---

## 8. MULTIPLAYER (Phases M + I)

### 8.1 Current: Local with ParrelSync (Phase M)
- NetworkPlayerState syncs: coins, tier, health, upgradeCost, board state
- Card pool initialized with EnsureCardPoolInitialized() + WaitUntil
- Phase/turn changes via RPC_PhaseChanged → GameManager.SetPhaseFromNetwork()

### 8.2 Target: AWS Online (Phase I)
- Cognito for auth
- DynamoDB for player profiles / match history
- WebSocket API Gateway for real-time state
- Lambda for server-side combat validation
- Mirror or custom transport layer

---

## 9. THEME SYSTEM

```
ThemeConfig (ScriptableObject)
├── Color Palette
├── Font Assets
├── Card Frame Sprites
├── UI Panel Backgrounds
├── Tribe-specific data
└── Localized strings (maxTierText, freezeButtonText, etc.)

ThemeManager (Singleton)
├── LoadTheme(ThemeConfig)
├── ApplyToAllUI()
└── OnThemeChanged event

IThemeable (Interface)
└── ApplyTheme(ThemeConfig) — implemented by all UI components
```

All UI implements IThemeable. Runtime hot-swap supported.

---

## 10. AUDIT CHECKLIST

When auditing, verify each:

- [ ] All 4 tribes have equal card count per tier
- [ ] Synergy thresholds trigger at exactly 2/4/6
- [ ] Cross-tribe combos activate correctly (all 4 pairs)
- [ ] Golden cards double stats but NOT abilityValue
- [ ] Death queue cascades properly (deathrattle → more deaths → more deathrattles)
- [ ] No damage cap in combat
- [ ] Coin cap at 10 enforced
- [ ] Buy/sell cost clamped to minimum 0
- [ ] Shop pool integrity (reserve on display, return on reroll, consume on buy)
- [ ] Discovery consumes chosen card, returns unchosen
- [ ] AI respects synergy cost reductions
- [ ] Multiplayer state is per-player (not global)
- [ ] Clone base stats preserved (not buffed values)
- [ ] Combat damage uses per-player tier
- [ ] AbilityManager cleaned up between games

