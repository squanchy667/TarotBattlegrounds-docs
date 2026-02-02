# Known Issues

## Active Issues

### High Priority (Multiplayer) — Fixed in Sprint 12
- ~~**SynergyManager global state** — Per-player snapshots via `CalculateSynergies()`. Fixed.~~
- ~~**DiscoveryUI race condition** — Per-player pending discoveries dict. Fixed.~~
- ~~**Shop pool integrity** — Cards reserved from pool when placed in shop. Fixed.~~
- ~~**Player 2 cannot buy cards** — Bounds check + shop sync after buy. Fixed.~~
- ~~**Tavern upgrade cost not reflected** — `upgradeCost` synced in `NetworkPlayerState`. Fixed.~~
- ~~**AbilityManager memory leak** — `ClearAll()` in `InitializePlayers` and `OnDestroy`. Fixed.~~
- ~~**Combat log shows wrong battle** — Filter to local player's battle. Fixed.~~
- ~~**RefreshShop bypasses coin setter** — Use property setter consistently. Fixed.~~

### High Priority (Multiplayer) — Found in ParrelSync Test Run 2
- ~~**Card pool initialization race condition** — `EnsureCardPoolInitialized()` + `WaitUntil` in `NetworkGameSetup`. Fixed.~~
- ~~**Upgrade cost shows base value on client** — `SyncedUpgradeCost` in `NetworkPlayerState` + `GetUpgradeCost()` checks synced value first. Fixed.~~

### High Priority (Multiplayer) — Found in ParrelSync Test Run 3
- ~~**Phase panel shows wrong text/turn** — `RPC_PhaseChanged` now calls `GameManager.SetPhaseFromNetwork()` to apply phase and turn before UI refresh. Fixed.~~
- **Intermittent buy failure on non-host (1 occurrence)** — Non-host player paid coins but card didn't appear in hand. Likely card reconstruction timing issue. Added `LogError`-level diagnostics in `NetworkCardData.ToCard()` and `ApplyNetworkPlayerState()` to capture next occurrence. (`NetworkCardData.cs`, `GameManager.cs`)

### Game Audit — Fixed in Audit Round 1 (February 2, 2026)
- ~~**No matchmaking history** — GameManager used random pairing with frequent rematches. Added `recentOpponents` tracking. Fixed.~~
- ~~**Golden card ability doubling** — `CreateGoldenVersion()` doubled abilityValue (should only double stats). Removed. Fixed.~~
- ~~**OnAttack bonus damage bypassed Aegis** — DealBonusDamage dealt separate damage. Changed to temporary attack boost. Fixed.~~
- ~~**No death queue for deathrattles** — Deaths handled immediately. Added `ProcessDeaths()` with cascade support. Fixed.~~

### Game Audit — Fixed in Audit Round 5 (February 2, 2026)
- ~~**Triple returns clones to pool** — Copies returned via `ReturnCardToPool()` instead of being consumed. Removed pool return. Fixed.~~
- ~~**Triples don't resolve on PlayCard** — `PlayCard()` never called `CheckAndResolveTriples()`. Added call. Fixed.~~
- ~~**Guardian detection uses legacy only** — CombatManager only checked `effectType`. Now checks both `effectType` and `abilityEffect`. Fixed.~~
- ~~**AI Sort() mutates board silently** — Hard AI sorted board without firing events. Added `NotifyBoardChanged()`. Fixed.~~
- ~~**Tier counter off-by-one** — Counter increments at turn start; `GetUpgradeCost()` over-discounted by 1. Subtracted 1 from elapsed. Fixed.~~
- ~~**AbilityManager blocks multi-ability** — Registration skipped if card had any ability. Changed to per-type dedup. Fixed.~~
- ~~**Shop UI missing synergy cost** — Displayed raw cost without synergy reduction. Added `GetCostReduction()` call. Fixed.~~
- ~~**Discovery silent on empty pool** — Added warning log when no cards available at target tier. Fixed.~~

### Game Audit — Fixed in Audit Round 4 (February 2, 2026)
- ~~**Cleave ignores configured damage** — Hardcoded 0 instead of abilityValue. Now uses configured value. Fixed.~~
- ~~**Combat clone memory leak** — Clone abilities never unregistered. Added `CleanupCombatClones()`. Fixed.~~
- ~~**AI ignores synergy cost reduction** — Added `GetCostReduction()` to AI buy logic. Fixed.~~
- ~~**Cleave/adjacent ignores Aegis** — Added `hasAegis` check to all adjacent damage. Fixed.~~
- ~~**Discovery blocked at hand limit** — Removed hand cap check; allows overflow like triple. Fixed.~~
- ~~**Negative buyCostModifier** — Clamped base cost to min 0. Fixed.~~
- ~~**Negative sellValueModifier** — Clamped sell value to min 0 in both sell methods. Fixed.~~

### Game Audit — Fixed in Audit Round 3 (February 2, 2026)
- ~~**Synergy cost reduction dead code** — `GetCostReduction()` never called from `BuyCard()`. Wired up with per-player snapshot. Fixed.~~
- ~~**Timer never shows "0s"** — `CeilToInt` rounded up. Added explicit zero check. Fixed.~~

### Game Audit — Fixed in Audit Round 2 (February 2, 2026)
- ~~**OnAttack triggers before Aegis check** — Abilities fired even when attack blocked by Aegis. Moved trigger inside else branch. Fixed.~~
- ~~**Damage cap hardcoded to 5** — `Mathf.Min(5, ...)` made late-game wins meaningless. Removed cap. Fixed.~~
- ~~**Triple creation ignores hand limit** — Golden card added without checking hand size. Now allows temporary overflow (matching Hearthstone). Fixed.~~
- ~~**Discovery doesn't consume pool copies** — Discovered cards not removed from pool. Added `RemoveCardFromPool()`. Fixed.~~

### Medium Priority
- **Card art placeholder** - Cards showing placeholder colors, no artwork yet
- **Combat has no visualization** - Combat works in background but no in-game animation
- **Scene wiring needed for Phase P** - The following must be added manually in Unity Inspector:
  - **End Turn button**: Add UI Button to Canvas → assign to `GameUIManager.endTurnButton`
  - **Freeze Shop button**: Add UI Button to Canvas → assign to `GameUIManager.freezeShopButton`
  - **End Turn label**: Add TMP_Text child → assign to `GameUIManager.endTurnButtonText`
  - **Freeze label**: Add TMP_Text child → assign to `GameUIManager.freezeShopButtonText`
  - **Game Over panel**: Add overlay panel with GameOverUI component → wire `titleText`, `placementText`, `standingsText`, `playAgainButton`, `quitButton`
  - **Discovery panel**: Add overlay panel with DiscoveryUI component → wire `titleText`, `cardSlots[]` (3 CardDisplayUI), `cardContainer`
  - **Background image**: Add fullscreen Image as first Canvas child (Raycast Target = false) → assign to `GameUIManager.gameBackgroundImage`

### Low Priority
- **Combat log styling** - Could use better visual formatting
- **Selection highlight** - Could be more visible

---

## Pending Verification

*All items verified — see Phase T Resolved below.*

---

## Developer Notes

### Verbose Combat Logging (January 23, 2026)
CombatManager.cs and CombatLogUI.cs contain verbose debug logging for combat tracing:
- `=== COMBAT START ===` - Combat initialization
- `--- TURN X START/END ---` - Turn boundaries
- `[BEFORE_LOGENTRY]` / `[AFTER_LOGENTRY]` - LogEntry call tracing
- `[LogEntry]` - Event invocation tracing
- `[CombatLogUI.AddLogEntry]` - UI receipt tracing
- `[CombatLogUI.ClearLog]` - Log clear tracing

These logs help debug turn order and event subscription issues. Keep for future testing.

---

## Resolved

### Phase T - Automated Testing (January 29, 2026)
- Tribe synergy activation at 2/4/6 thresholds — verified with 35+ NUnit tests (SynergyTests.cs)
- Cross-tribe combo bonuses — all 4 pairs tested and verified
- Full 4-player game to completion — 100-game batch passes
- Card balance across 100+ AI games — win rates <45%, game length 5-25 turns, all tribes viable

### Phase P - Polish Pass (January 29, 2026)
- Tier 6 upgrade showing "999g" instead of "MAX" — fixed with themeable maxTierText
- No way to end turn early — added End Turn button
- No shop freeze — added Freeze/Unfreeze toggle with auto-unfreeze
- No board card reordering — added click-to-swap
- No game over screen — added GameOverUI with standings and navigation
- No triple/fusion mechanic — added golden cards with discovery reward
- No game background — added themeable background image/color

### Phase H - Skinnable Theme System (January 28, 2026)
- Fixed compilation errors in theme system
- All UI components now implement IThemeable with hot-swap support
- ThemeManager singleton with runtime Tarot theme fallback

### Phase G - UI/UX Overhaul (January 28, 2026)
- Removed static board slots - BoardUI creates them dynamically
- Fixed UI positioning issues for responsive design

### Sprint 3 (January 23, 2026)
- Combat turn order logging - Turn 1 now displays correctly in logs
- Combat debug tracing added - Verbose logging for future debugging

### Sprint 2 (January 22, 2026)
- Health not updating in UI - Synced playerHealths[] to Player.Health property
- Cannot sell from hand - Added SellCardFromHand() method
- Duplicate event subscriptions - Removed direct UI subscriptions, GameUIManager is single source

### Sprint 1 (January 21, 2026)
- UI not refreshing on player switch
- Card selection not persisting
- Buy button not enabling after selection
- Event system not firing on state changes

### Phase 2 (July 2025)
- Shop generation fixed
- Coin progression fixed
- Sell gold bug fixed
