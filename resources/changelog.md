# Changelog

## Phase P: Polish Pass - January 29, 2026

### P1: Fix Tier 6 "999 gold" Display
- Added `maxTierText` field to ThemeConfig (default: "MAX")
- GameUIManager now shows "Upgrade: MAX" when at tier 6 instead of "Upgrade: 999g"

### P6: End Turn Button
- Added `recruitPhaseSkipped` bool and `EndRecruitPhaseEarly()` to GameManager
- Modified recruit phase timer loop: exits immediately when skipped
- Added `endTurnButton` + `endTurnButtonText` fields to GameUIManager
- Button enabled only during recruit phase

### P7: Freeze Shop Button
- Added `ShopFrozen` property with `OnShopFreezeChanged` event to Player
- Modified `RefreshShop()`: if frozen, keeps current shop and auto-unfreezes
- Added `ToggleShopFreeze()` method to Player
- Added `freezeButtonText` / `unfreezeButtonText` to ThemeConfig
- GameUIManager toggles label between Freeze/Unfreeze based on state

### P4: Background and Arena Visuals
- Added `gameBackground` (Sprite) and `gameBackgroundColor` fields to ThemeConfig
- Added `gameBackgroundImage` reference to GameUIManager
- In `ApplyTheme()`: sets background sprite or fallback to color

### P5: Board Positioning + Card Reordering
- BoardUI: Added click handlers to empty slots → `OnEmptySlotSelected` event
- BoardUI: `OnCardClicked` now supports swap mode (select card A, click card B → swap via `OnBoardSwapRequested`)
- Player: Added `SwapBoardCards(indexA, indexB)` method
- GameUIManager: Subscribes to BoardUI events for hand-to-slot placement and board swaps

### P2: Win/Loss Resolution Screen
- Added `GameOverData` struct and `OnGameOver` static event to GameManager
- Track elimination order in `eliminationOrder` list
- Replaced `Debug.Log("Game Over")` with `TriggerGameOver()` that builds standings and fires event
- Added `playAgainText`, `quitToMenuText`, `gameOverTitle` to ThemeConfig

**New file `GameOverUI.cs`:**
- IThemeable panel subscribing to `GameManager.OnGameOver`
- Shows title, placement text, standings list, Play Again / Quit buttons
- Play Again reloads Game scene, Quit loads MainMenu scene

### P3: Triple/Fusion System with Discovery (Golden Minions)

**Triple mechanic:**
- Card.cs: Added `isGolden` field, `CreateGoldenVersion()` method (doubles attack, health, abilityValue)
- Player.cs: Added `CheckAndResolveTriples()` — groups hand+board by cardName, if 3+ non-golden copies found → removes 3, creates golden, adds to hand. Called after `BuyCard()`.
- CardDisplayUI.cs: Golden visual — star prefix in name (`* CardName *`), golden background tint
- ThemeConfig.cs: Added `goldenCardColor` field

**Discovery reward (on triple):**
- After merging, fires `OnTripleDiscovery` event with 3 cards from current tier+1 (capped at 6)
- TavernManager: Added `GetDiscoveryCards(tier, count)` — returns deduplicated random cards from pool at specified tier

**New file `DiscoveryUI.cs`:**
- Modal overlay with 3 card choices from CardDisplayUI
- Player picks one, it goes to hand via `AddDiscoveryCard()`
- AI players auto-pick the first card
- IThemeable, subscribes to all players' `OnTripleDiscovery` events

**Files Added:**
- `Assets/Scripts/UI/GameOverUI.cs`
- `Assets/Scripts/UI/DiscoveryUI.cs`

**Files Modified:**
- `Assets/Cards/Card.cs` — isGolden, CreateGoldenVersion(), Clone() update
- `Assets/Scripts/GameManager.cs` — GameOverData, OnGameOver, elimination tracking, EndRecruitPhaseEarly, phase UI refresh
- `Assets/Scripts/Player.cs` — ShopFrozen, ToggleShopFreeze, SwapBoardCards, CheckAndResolveTriples, AddDiscoveryCard, OnTripleDiscovery event
- `Assets/Scripts/TavernManager.cs` — GetDiscoveryCards()
- `Assets/Scripts/Theme/ThemeConfig.cs` — 9 new fields, updated both factory methods
- `Assets/Scripts/UI/BoardUI.cs` — OnEmptySlotSelected, OnBoardSwapRequested events, swap mode
- `Assets/Scripts/UI/CardDisplayUI.cs` — Golden card visuals
- `Assets/Scripts/UI/GameUIManager.cs` — End Turn, Freeze, background, board events, MAX tier, OnShopFreezeChanged

---

## Phase H: Skinnable Theme System - January 28, 2026

### H1-H8: Complete Theme System
Implemented full skinnable theme system allowing visual reskinning without code changes.

**ThemeConfig.cs (Extended ScriptableObject):**
- Color palette (primary, secondary, accent, backgrounds)
- Font asset slots (title, body, stats)
- Card frame sprites (common, rare, epic, legendary)
- UI panel backgrounds and button styles
- Tribe configuration with names, colors, icons
- UI text customization (shop/hand/board titles, button labels)

**ThemeManager.cs (Singleton):**
- LoadTheme / SetTheme for runtime switching
- ApplyToAllUI() broadcasts to all IThemeable components
- OnThemeChanged event for hot-swap support
- Runtime Tarot theme fallback

**IThemeable Interface + ThemeableUI Base Class:**
- All UI components implement IThemeable
- CardDisplayUI, GameUIManager, ShopUI, HandUI, BoardUI, CombatLogUI

**Editor Tools:**
- Menu: Game/Create Default Tarot Theme
- Menu: Game/Create Debug Theme

**Tarot Skin Branch:**
- Full Tarot theme applied on `tarot-skin` branch
- All cards, tribes, colors themed to Tarot aesthetic

---

## Phase G: Core Engine Polish & UI/UX Overhaul - January 28, 2026

### G1-G7: Engine Polish
- G1: Removed all tarot-specific strings from core code
- G2: Created ThemeConfig.cs for theme settings
- G3: Documented "How to create a new skin" (SKINNING-GUIDE.md)
- G4: Created template card database (generic)
- G6: Created `template` branch - clean engine ready for reskinning
- G7: GameConfig.cs for automatic player/AI selection

### UI/UX Overhaul
- Responsive design overhaul for all UI panels
- Removed static board slots - BoardUI creates them dynamically
- Fixed UI positioning issues across resolutions

---

## Sprint 8: Phase G Fixes - January 27-28, 2026

### Integration Fixes
- Fixed synergy system and ability registration bugs
- Integrated AI controllers into GameManager properly
- Added SelectionManager for click-to-deselect and cross-panel selection
- Added card tooltip hover system
- Fixed game end detection
- Added Card Generator editor tool
- Fixed compilation errors in AITestRunner and AIController
- Fixed CardDatabase.cs to use correct enum types

---

## Sprint 7: Phase F - Card Pool Expansion - January 26, 2026

### F1-F4: Complete Card Database
Created 30 balanced cards (5 per tier) with distributed abilities and tribes.

**CardDatabase.cs:**
- Static class generating all 30 cards programmatically
- Tier 1-2: Simple stats, basic effects
- Tier 3-4: Complex abilities, synergy enablers
- Tier 5-6: Powerful finishers, multi-tribe cards

**Tribe Distribution:**
- Pentacles (Economy): 8 cards
- Cups (Healing): 8 cards
- Swords (Aggro): 7 cards
- Wands (Buffs): 8 cards
- Multi-tribe cards: 6 (including 1 triple-tribe)

**Ability Distribution:**
- Battlecry: 12 cards
- Deathrattle: 4 cards
- OnAttack: 5 cards
- Guardian: 4 cards

**CardPoolInitializer.cs:**
- MonoBehaviour to auto-initialize TavernManager with CardDatabase
- Optional synergy initialization
- Debug summary printing

**Files Added:**
- `Assets/Scripts/Cards/CardDatabase.cs`
- `Assets/Scripts/Cards/CardPoolInitializer.cs`

---

## Sprint 6: Phase E - 4-Player Lobby - January 26, 2026

### E1-E5: Complete Lobby System
Created LobbyManager for 1 human + 3 AI player games.

**LobbyManager.cs:**
- Singleton manager for 4-player lobbies
- 1 human player + 3 AI opponents with configurable difficulty
- Round-robin matchmaking avoiding repeat opponents
- Player elimination tracking with placements (1st-4th)
- Game end detection when 1 player remains

**Features:**
- Events: OnTurnStarted, OnPhaseChanged, OnPlayerEliminated, OnGameEnded, OnMatchResult
- GamePhase enum: Lobby, Recruit, Combat, Results
- Bye handling for odd player counts
- Recent opponent tracking to vary matchups

**Files Added:**
- `Assets/Scripts/Lobby/LobbyManager.cs`

---

## Sprint 5: Phase D - AI System - January 26, 2026

### D1-D6: Complete AI Framework
Created intelligent AI opponents with 3 difficulty levels.

**AIController.cs:**
- Component-based AI that controls a Player instance
- Full recruit phase decision making:
  - Tavern tier upgrade decisions
  - Card evaluation and purchasing
  - Hand-to-board card playing
  - Weak card selling
  - Strategic rerolling
  - Board positioning optimization

**AIDifficulty Levels:**
- **Easy**: Random choices, slower upgrades, no repositioning
- **Medium**: Balanced play, standard decisions
- **Hard**: Optimized choices, aggressive synergy building, strategic positioning

**Card Evaluation System:**
- Base stats value (attack + health)
- Tier bonus
- Synergy potential with existing board
- Multi-tribe card bonus
- Ability bonus

**AITestRunner.cs:**
- Automated AI vs AI balance testing
- Configurable game count (default 100)
- Win rate tracking and statistics
- Average game length calculation

**Files Added:**
- `Assets/Scripts/AI/AIController.cs`
- `Assets/Scripts/AI/AITestRunner.cs`

---

## Sprint 4: Phase C - Tribe Synergy System - January 26, 2026

### C1-C12: Complete Synergy Framework
Implemented data-driven tribe synergy system with multi-tribe cards and combos.

**Core Enums Created:**
- `TribeType.cs` - None, Pentacles, Cups, Swords, Wands
- `SynergyTrigger.cs` - Passive, StartOfCombat, EndOfCombat, OnSell, OnBuy, OnDeath, EndOfTurn
- `SynergyEffect.cs` - BuffAttack, BuffHealth, BonusGold, ReduceCost, Cleave, etc.
- `SynergyTarget.cs` - AllTribeMembers, AllFriendly, Adjacent, Random, Self

**TribeSynergy.cs (ScriptableObject):**
- Tribe definition with name, description, theme color
- Tiered effects (2/4/6 thresholds)
- Cross-tribe combo configuration

**SynergyManager.cs:**
- Singleton manager for tribe counting and synergy application
- `UpdateTribeCounts()` - Recalculates tribes on board changes
- `TriggerSynergies()` - Applies effects by trigger type
- `GetSellBonus()` / `GetCostReduction()` - Query synergy bonuses

**Card.cs Updates:**
- Added `TribeType[] tribes` field for multi-tribe support
- Added `HasTribe()` and `GetTribes()` helper methods

**Integration Points:**
- Player.cs: SellCard synergy bonus, PlayCard tribe update, EndRecruitPhase triggers
- CombatManager.cs: StartOfCombat synergy triggers

**Test Data:**
- `SynergyTestData.cs` - Creates 4 tribe synergies programmatically
- `SynergyTestCards.cs` - Creates 17 test cards (12 single-tribe, 4 dual-tribe, 1 triple-tribe)

**Files Added:**
- `Assets/Scripts/Synergies/TribeType.cs`
- `Assets/Scripts/Synergies/SynergyTrigger.cs`
- `Assets/Scripts/Synergies/SynergyEffect.cs`
- `Assets/Scripts/Synergies/SynergyTarget.cs`
- `Assets/Scripts/Synergies/TribeSynergy.cs`
- `Assets/Scripts/Synergies/SynergyManager.cs`
- `Assets/Scripts/Synergies/SynergyTestData.cs`
- `Assets/Scripts/Synergies/SynergyTestCards.cs`

**Files Modified:**
- `Assets/Cards/Card.cs` - Added tribes field and helpers
- `Assets/Scripts/Player.cs` - Synergy hooks
- `Assets/Scripts/CombatManager.cs` - Combat synergy triggers

---

## Phase C Redesign - January 23, 2026

### Tribe Synergy System Expanded
Phase C has been redesigned with a more flexible, data-driven approach:

**New Features:**
- Multi-tribe cards (cards can belong to 2+ tribes)
- Cross-tribe combos (Pentacles + Cups = bonus effect)
- Data-driven synergies via ScriptableObjects
- Tiered thresholds (2/4/6 counts)

**New Enums:**
- `TribeType` - None, Pentacles, Cups, Swords, Wands
- `SynergyTrigger` - Passive, StartOfCombat, EndOfCombat, OnSell, OnBuy, OnDeath, EndOfTurn
- `SynergyEffect` - BuffAttack, BuffHealth, BonusGold, ReduceCost, Piercing, Cleave, etc.
- `SynergyTarget` - AllTribeMembers, AllFriendly, Adjacent, Random, Self

**Tribe Designs:**
| Tribe | Theme | Combo Partner |
|-------|-------|---------------|
| Pentacles | Economy | Cups (+1 gold/turn) |
| Cups | Healing | Wands (heals buff attack) |
| Swords | Aggro | Pentacles (kills give gold) |
| Wands | Buffs | Swords (double attack buffs) |

**Task Count:** 6 → 14 tasks

---

## Sprint 3 (continued): Combat Debug - January 23, 2026

### Combat Turn Order Fix
- Added verbose debug logging to CombatManager.cs for turn tracing
- Added logging to CombatLogUI.cs for event receipt tracing
- Verified combat turn order is correct (alternating sides, proper first attacker)

**Debug Logs Added:**
- `=== COMBAT START ===` with pFirst, firstPlayer values
- `--- TURN X START/END ---` boundaries
- `[BEFORE_LOGENTRY]` / `[AFTER_LOGENTRY]` around LogEntry calls
- `[LogEntry]` in LogEntry function
- `[CombatLogUI.AddLogEntry]`, `[CombatLogUI.ClearLog]`, `[CombatLogUI.OnCombatStart]`

**Files Modified:**
- `Assets/Scripts/CombatManager.cs` - Added debug logging throughout combat loop
- `Assets/Scripts/UI/CombatLogUI.cs` - Added debug logging to event handlers

---

## Sprint 3: Ability Framework - January 22, 2026

### B4-B8: Complete Ability Framework

**B4: Deathrattle Ability**
- Created `DeathrattleAbility.cs` with effects: BuffRandomFriendly, DamageRandomEnemy, DamageAllEnemies
- Hooked into CombatManager death handling

**B5: OnAttack Ability**
- Created `OnAttackAbility.cs` with effects: BuffSelf, BonusDamage, Cleave, DamageAdjacent
- Hooked into CombatManager attack phase

**B6: Taunt Ability**
- Created `TauntAbility.cs` as passive marker
- Integrates with existing Guardian combat logic

**B7: Card SO Ability Fields**
- Added `abilityTrigger`, `abilityEffect`, `abilityValue` to Card.cs
- Added `AbilityEffectType` enum for inspector configuration
- Added `RegisterAbility()` method for runtime ability setup
- Updated Clone() to copy ability fields and auto-register

**B8: Test Cards**
- Created `AbilityTestCards.cs` with 10 test cards:
  - Battlecry: Battle Commander, War Drummer, Shield Bearer, Gold Finder
  - Deathrattle: Dying Mentor, Explosive Imp, Bomb Lobber
  - OnAttack: Raging Berserker, Whirlwind Warrior
  - Taunt: Stone Guardian

**Files Added:**
- `Assets/Scripts/Abilities/Abilities/DeathrattleAbility.cs`
- `Assets/Scripts/Abilities/Abilities/OnAttackAbility.cs`
- `Assets/Scripts/Abilities/Abilities/TauntAbility.cs`
- `Assets/Scripts/Abilities/AbilityTestCards.cs`

**Files Modified:**
- `Assets/Cards/Card.cs` - Added ability system fields
- `Assets/Scripts/CombatManager.cs` - Added ability triggers
- `Assets/Scripts/Player.cs` - Added RegisterAbility() call

---

### B3: Implement Battlecry
Created Battlecry ability system that triggers when cards are played from hand.

**BattlecryAbility.cs:**
- Supports multiple effects: BuffAdjacent, BuffAllFriendly, GainAegis, GainCoins, etc.
- Uses AbilityBase for logging and validation

**AbilityManager.cs:**
- Static manager for registering/unregistering card abilities
- `TriggerAbilities()` method to fire abilities by trigger type
- `CreateBattlecryContext()` helper for play-time context

**Player.cs Integration:**
- `PlayCard()` now triggers Battlecry abilities after placing card
- `SellCard()` / `SellCardFromHand()` now unregister abilities

**Files Added:**
- `Assets/Scripts/Abilities/Abilities/BattlecryAbility.cs`
- `Assets/Scripts/Abilities/AbilityManager.cs`

**Files Modified:**
- `Assets/Scripts/Player.cs` - Battlecry trigger + ability cleanup

---

### B2: Create IAbility Interface
Created the ability framework foundation:

**IAbility.cs:**
- `IAbility` interface with `Trigger`, `Description`, `Execute()`, `CanExecute()`
- `AbilityContext` class containing source card, owner, target, board states

**AbilityBase.cs:**
- Abstract base class with logging and validation
- `AbilityEffects` static helper for common effects:
  - `BuffAttack()`, `BuffHealth()`, `BuffStats()`
  - `DealDamage()`, `GrantAegis()`

**Files Added:**
- `Assets/Scripts/Abilities/IAbility.cs`
- `Assets/Scripts/Abilities/AbilityBase.cs`

---

### B1: Create AbilityTrigger Enum
Created the `AbilityTrigger` enum defining when abilities trigger:
- `None` - No ability
- `Battlecry` - When played from hand
- `Deathrattle` - When card dies
- `OnAttack` - When card attacks
- `OnDamaged` - When card takes damage
- `StartOfCombat` - Before combat begins
- `EndOfTurn` - At end of recruit phase

**Files Added:**
- `Assets/Scripts/Abilities/AbilityTrigger.cs`

---

## Sprint 2: Bug Fixes - January 22, 2026

### A3: Validate Event Subscriptions
**Audit:** Verified all 10 events (7 Player, 3 CombatManager) have proper subscribers.

**Issue Found:** Duplicate subscriptions in UI components:
- ShopUI, HandUI, BoardUI subscribed directly to player events
- GameUIManager also subscribes and calls their refresh methods
- Result: Every event triggered 2 UI refreshes

**Fix:** Removed direct subscriptions from UI components (-92 lines):
- GameUIManager is now single source of truth for event handling
- Cleaner architecture, no duplicate refreshes

**Files Changed:**
- `Assets/Scripts/UI/ShopUI.cs` - Removed duplicate subscriptions
- `Assets/Scripts/UI/HandUI.cs` - Removed duplicate subscriptions
- `Assets/Scripts/UI/BoardUI.cs` - Removed duplicate subscriptions

---

### A2: Enable Sell from Hand
**Problem:** Players could only sell cards from the board, not from their hand.

**Fix:**
- Added `SellCardFromHand(int index)` method to `Player.cs`
- Updated sell button to enable when board OR hand card selected
- Updated sell action to check both board and hand selection

**Files Changed:**
- `Assets/Scripts/Player.cs` - Added new method (+26 lines)
- `Assets/Scripts/UI/GameUIManager.cs` - Updated button state and action

---

### A1: Fix Health UI Not Updating After Combat
**Problem:** After combat, player health was reduced internally but the UI didn't reflect the change.

**Root Cause:** Data source mismatch in `GameManager.cs`:
- Combat damage modified `playerHealths[]` list directly
- Never updated `Player.Health` property
- So `OnHealthChanged` event never fired
- UI never received notification

**Fix:** Added sync lines in `GameManager.cs` after each health modification:
```csharp
players[pX].Health = playerHealths[pX]; // Sync to fire OnHealthChanged
```

**Files Changed:**
- `Assets/Scripts/GameManager.cs` - 4 lines added (lines 85, 89, 101, 111)

---

## Sprint 1: UI System Overhaul - January 21, 2026

### Summary
Complete rewrite of UI system from polling-based to event-driven architecture. Full game loop now functional.

### Added
- ✅ **Event System in Player.cs**
  - OnHandChanged, OnBoardChanged, OnCoinsChanged events
  - OnTierChanged, OnHealthChanged, OnShopRefreshed events
  - Static OnAnyPlayerStateChanged for global listeners
  - NotifyAllStateChanged() for player switching

- ✅ **CombatLogUI** (New Component)
  - Real-time combat visualization
  - Color-coded log entries (attacks, deaths, effects)
  - Auto-scroll with entry limiting

- ✅ **Combat Events in CombatManager.cs**
  - OnCombatStart, OnCombatLogEntry, OnCombatEnd events
  - Detailed combat action logging

- ✅ **Test Suite** (35 tests)
  - CardSystemTests.cs - Card operations
  - CombatTests.cs - Battle simulation
  - EconomyTests.cs - Gold/tier mechanics
  - EdgeCaseTests.cs - Boundary conditions

### Changed
- 🔄 **GameUIManager.cs** - Complete rewrite
  - Event subscription model
  - Player switching with re-subscription
  - UpdateButtons() method for selection state
  - Removed Update() polling

- 🔄 **ShopUI.cs** - Event-driven refresh
  - Subscribes to OnShopRefreshed, OnTierChanged
  - Selection triggers button state update

- 🔄 **HandUI.cs** - Event-driven refresh
  - Subscribes to OnHandChanged
  - Selection triggers button state update

- 🔄 **BoardUI.cs** - Event-driven refresh
  - Subscribes to OnBoardChanged
  - Selection triggers button state update

### Fixed
- ✅ Player switching now properly updates all UI panels
- ✅ Card selection persists until action taken
- ✅ Button states update on selection change
- ✅ Shop refreshes correctly on tier upgrade

### Working Features
- ✅ Buy cards from shop
- ✅ Play cards to board
- ✅ Sell cards from board
- ✅ Refresh shop (reroll)
- ✅ Upgrade tavern tier
- ✅ Switch between players
- ✅ Combat execution
- ✅ Turn progression

### Known Issues (To Fix)
- ✅ ~~Health not updating in UI after combat damage~~ (Fixed in Sprint 2 - A1)
- ✅ ~~Cannot sell cards from hand (only board)~~ (Fixed in Sprint 2 - A2)
- ⚠️ UI needs visual polish

---

## Phase 3 (In Progress) - July 2025

### July 31, 2025
- ✅ Fixed combat outcome discrepancies (final board state logic)
- ✅ Validated with 5-run logs (consistent naming and turn tracking)
- ✅ Made names generic (pName/eName parameters passed from CombatTester)
- ✅ Achieved working balance: 0% "ofek" wins, 52% "jaya" wins, 48% ties

### July 29, 2025
- ✅ Started Phase 3: Combat System
- ✅ Implemented CombatManager.SimulateBattle
- ✅ Added health tracking (starting 40, damage cap 5)
- ✅ Integrated board from TavernManager

---

## Phase 2 (Complete) - July 28, 2025

### Achievements
- ✅ Card ScriptableObject system
- ✅ TavernManager with shop mechanics
- ✅ Buy/sell/reroll logic
- ✅ Tavern tiers (1-6) with unlocking
- ✅ Shop size scaling (3-6 cards)
- ✅ Coin progression (Turn 1:3/3, Turn 2:4/4, cap 10)
- ✅ Board limit enforcement (7 cards)
- ✅ Reroll logic (1 gold, refreshes shop)

### Bug Fixes
- ✅ Fixed "Available cards = 0" bug
- ✅ Fixed coin increment reset
- ✅ Fixed sell gold not adding
- ✅ Fixed shop size not scaling
- ✅ Fixed tier unlocking visibility

---

## Phase 1 (Complete) - July 22, 2025
- ✅ Unity project setup
- ✅ GameManager with Recruit/Combat phases
- ✅ Coroutine-based game loop
- ✅ AI placeholder
- ✅ Basic logging

---

## Pre-Phase (July 22, 2025)
- ✅ Project initialization
- ✅ Git repository created
- ✅ README.md and PRD.md written
