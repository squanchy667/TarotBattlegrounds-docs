# Known Issues

## Active Issues

### Medium Priority
- 🟡 **UI polish needed** - Layout and visual improvements required
- 🟡 **Card art placeholder** - Many cards showing gray placeholder instead of artwork

### Low Priority
- 🟢 **Combat log styling** - Could use better visual formatting
- 🟢 **Selection highlight** - Could be more visible

---

## Phase C (Next Up)

### High Priority
- 🔴 Tribe synergies not implemented
- 🔴 Need more cards per tier for balanced gameplay

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

### Sprint 3 (January 23, 2026)
- ✅ Combat turn order logging - Turn 1 now displays correctly in logs
- ✅ Combat debug tracing added - Verbose logging for future debugging

### Sprint 2 (January 22, 2026)
- ✅ Health not updating in UI - Synced playerHealths[] to Player.Health property
- ✅ Cannot sell from hand - Added SellCardFromHand() method
- ✅ Duplicate event subscriptions - Removed direct UI subscriptions, GameUIManager is single source

### Sprint 1 (January 21, 2026)
- ✅ UI not refreshing on player switch
- ✅ Card selection not persisting
- ✅ Buy button not enabling after selection
- ✅ Event system not firing on state changes

### Phase 2 (July 2025)
- ✅ Shop generation fixed
- ✅ Coin progression fixed
- ✅ Sell gold bug fixed
