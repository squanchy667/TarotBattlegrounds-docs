# Changelog

## Sprint 3: Ability Framework - January 22, 2026

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
