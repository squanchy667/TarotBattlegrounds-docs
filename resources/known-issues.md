# Known Issues

## Active Issues

### Medium Priority
- **Card art placeholder** - Cards showing placeholder colors, no artwork yet
- **Combat has no visualization** - Combat works in background but no in-game animation
- **Scene wiring needed for Phase P** - End Turn, Freeze, Game Over, Discovery panels need Inspector setup in Unity

### Low Priority
- **Combat log styling** - Could use better visual formatting
- **Selection highlight** - Could be more visible

---

## Pending Verification

### Tribe Synergies (Phase C)
- Tribe synergy activation at 2/4/6 thresholds - needs automated test confirmation
- Cross-tribe combo bonuses - needs automated test confirmation

### Full Game Loop (Phase E/G)
- Full 4-player game to completion (~15 turns) - needs automated verification
- Card balance across 100+ AI games - needs automated verification

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
