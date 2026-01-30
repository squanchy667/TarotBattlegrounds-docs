# Known Issues

## Active Issues

### High Priority (Multiplayer)
- **SynergyManager global state** — `_tribeCounts`/`_activeTiers` are singleton dicts overwritten by whichever player last called `UpdateTribeCounts()`. Sell bonuses and combat synergy triggers use wrong player's data. (`SynergyManager.cs`)
- **DiscoveryUI race condition** — `PendingDiscoveryCards` and `pendingPlayer` are singular. Two simultaneous triples overwrite each other; wrong player gets the card. (`DiscoveryUI.cs`)
- **Shop pool integrity** — Cards placed in shops are not reserved from the master pool. Two players can see and buy the same card, corrupting the pool. (`TavernManager.cs`)
- **Player 2 cannot buy cards** — In Photon multiplayer, Player 2's buy actions fail or don't reflect. Shop sync and card lookup issues between host and client. (`NetworkGameBridge.cs`, `Player.cs`)
- **Tavern upgrade cost not reflected** — After upgrading via RPC, the client doesn't see coins deducted. `NetworkPlayerState` doesn't include upgrade cost, and `tierTurnCounter` is never synced. (`Player.cs`, `NetworkPlayerState.cs`)
- **AbilityManager memory leak** — Static `_cardAbilities` dict never cleared between games. Stale card references accumulate across sessions. (`AbilityManager.cs`)
- **Combat log shows wrong battle** — Multiple battles fire `OnCombatStart` sequentially, each clearing the log. Only the last battle is visible. (`CombatLogUI.cs`)
- **RefreshShop bypasses coin setter** — Writes directly to `_coins` backing field, bypassing property setter event. Fragile pattern. (`Player.cs`)

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
