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

### Game Audit — Fixed in Audit Round 10 (February 2, 2026)
- ~~**Shop UI costs stale after synergy change** — Costs calculated once on shop refresh, never recalculated when board changed. Added `OnBoardChanged` subscription to `ShopUI`. Fixed.~~
- ~~**Combat damage counts dead minions** — Used `.Count` instead of `.Count(c => c.health > 0)` for damage calculation. Made defensive filter consistent with loop exit condition. Fixed.~~

### Game Audit — Fixed in Consensus Round (February 2, 2026)
- ~~**SimulateBattle single tier for both players** — Damage used same tier for both sides. Split into per-player tier params. Fixed.~~
- ~~**Golden cards returned to pool on sell** — Not properly consumed in all sell paths. Ensured golden guard in both sell methods. Fixed.~~
- ~~**Discovery cards not reserved from pool** — Unchosen cards never returned after selection. Added `ReturnDiscoveryCards()`. Fixed.~~
- ~~**Shop freeze persists through manual reroll** — `_shopFrozen` not cleared before reroll. Added explicit clear. Fixed.~~
- ~~**Card.Clone() stored buffed stats as base** — `StoreBaseStats()` captured current values. Now propagates original base stats from source card. Fixed.~~
- ~~**Player disconnect leaks discovery cards** — Pending discovery cards lost from pool on destroy. Added `OnDestroy()` cleanup. Fixed.~~

### Game Audit — Fixed in Audit Round 9 (February 2, 2026)
- ~~**Golden flag on sell** — Not properly reset during sell flow. Fixed.~~
- ~~**DRY cost reduction** — Cost calculation duplicated in GameUIManager instead of using centralized `GetEffectiveCost()`. Fixed.~~
- ~~**Tier counter adjustment** — Upgrade cost reduction tracking was off. Fixed.~~
- ~~**Excessive debug logs** — Cleaned up verbose console output. Fixed.~~

### Game Audit — Fixed in Audit Round 7 (February 2, 2026)
- ~~**No coin cap** — Selling/abilities could push gold above 10. Added `Mathf.Clamp` in property setter. Fixed.~~
- ~~**BuffAllFriendly excluded self** — "All friendly" should include played card (Hearthstone standard). Added `includeSelf` param; split into All/Other variants. Fixed.~~

### Game Audit — Fixed in Audit Round 6 (February 2, 2026)
- ~~**AI buy loop hardcoded cost** — Loop checked `coins >= 3` instead of minimum possible cost (1). Fixed.~~
- ~~**Buy button ignores synergy cost** — Button disabled for synergy-discounted cards player could afford. Added `GetCostReduction()`. Fixed.~~
- ~~**AI Medium guardian missing events** — Swap didn't check new ability system or fire `NotifyBoardChanged()`. Fixed.~~

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

### Production Audit — Fixed (February 23, 2026)
- ~~**C5: Attack order always picks leftmost** — CombatManager.FirstOrDefault selected leftmost alive card every turn. Added attack pointer cycling per side. Fixed.~~
- ~~**C6: Synergy tiers don't stack** — Only highest tier fired. Changed SynergySnapshot to store List<SynergyTier> per tribe so all met thresholds fire. Fixed.~~
- ~~**C7: Double cleave bug** — Cards with OnAttackCleave ability AND hasCleave synergy flag fired cleave twice. Added guard to skip synergy cleave if ability cleave exists. Fixed.~~
- ~~**C8: Aura target tribe not set** — AuraBuffTribematesAttack created without TargetTribe, buffing all friendlies. Now passes card's primary tribe. Fixed.~~
- ~~**C2: COEP header wrong** — deploy-webgl.sh had `require-corp`, changed to `credentialless`. Fixed.~~
- ~~**H3: Guardian targeting deterministic** — FirstOrDefault picked leftmost guardian. Changed to random selection among all guardians. Fixed.~~
- ~~**H9: Combo gold fires per-card** — BonusGold combo effect iterated all board cards. Now fires once for gold effects. Fixed.~~
- ~~**H2: Tavern upgrade costs wrong** — Tiers 4/5/6 all cost 11. Changed to 9/10/11. Fixed.~~
- ~~**H1: Token summon clone tracking** — Tokens summoned mid-combat not tracked in allClones. Now passed through ProcessDeaths. Fixed.~~
- ~~**H10: Board card lookup by name** — Used cardName+tier instead of reference. Changed to IndexOf. Fixed.~~
- ~~**H7: Stars/Coins missing from ThemeConfig** — Hardcoded 4 tribes, now 6. Fixed.~~
- ~~**H4: Host disconnect kills game** — OnMasterClientSwitched now handles graceful migration. New master assumes game loop, eliminates DC'd players, broadcasts snapshot. Fixed.~~
- ~~**H5: Hero powers not broadcast** — heroPowerId + usedThisTurn added to NetworkPlayerState. Fixed.~~
- ~~**H6: hasCleave not synced** — hasCleave + hasReborn added to NetworkCardData. Fixed.~~
- ~~**H8: RankedManager.SubmitMatchResult never called** — Wired into TriggerGameOver with placement data. Fixed.~~
- ~~**H11: Shop pool not scaled for 8 players** — TavernManager now calls EightPlayerManager.GetShopPoolMultiplier. Fixed.~~
- ~~**H12: No hero power selection for humans** — OnHeroPowerSelectionNeeded event fires 3 choices with callback. Falls back to auto-assign. Fixed.~~
- ~~**M1: goldPerTurnBase field mismatch** — goldPerTurn mapped to _startingGold in Player. Fixed.~~
- ~~**M2: Config fields silently ignored** — startingGold, maxGold wired into Player gold calculation. Fixed.~~
- ~~**M4: Damage cap test contradicts code** — Test updated to check damage >= 1 instead of <= 5. Fixed.~~
- ~~**M7: Photon region hardcoded** — PhotonConnector clears FixedRegion at connect. Fixed.~~
- ~~**M10: Stale matchId in PlayerPrefs** — Cleaned up on idle/error state transitions. Fixed.~~
- **C1: Lobby scene GUID broken** — Zero GUID in EditorBuildSettings.asset. Must fix in Unity Editor. OPEN.
- **C3: API Gateway CORS not configured** — SAM template needs Cors section on HttpApi. OPEN.
- **C4: DataConfig apiBaseUrl blank** — Must set API URL in Unity Inspector. OPEN.

See [PRODUCTION-FIX-PLAN.md](/PRODUCTION-FIX-PLAN.md) for full fix plan.

### Production Audit Round 2 — Fixed (February 24, 2026)
7 bugs fixed + 240 unit tests added. Commit: `9a8f686`

- ~~**Reborn+OnAllyDeath double-trigger** — Reborn cards stayed in board and received their own death notification. Skip OnAllyDeath for reborn cards. Fixed.~~ *(Note: per triple-agent audit, this may need revisiting — BG rules say Reborn deaths should trigger OnAllyDeath on other allies)*
- ~~**ArcaneBolt armed flag persists** — `armed` private field never cleared by `ResetForNewTurn()`. Made ResetForNewTurn virtual, override in ArcaneBolt. Fixed.~~
- ~~**RecruiterPower pool corruption** — `pool.Remove(picked)` operated on live `allCards` list. Changed to `TavernManager.RemoveCardFromPool()`. Fixed.~~
- ~~**Hero power combat buffs corrupt live boards** — WarChief/Tactician/ArcaneBolt modified live boards before SimulateBattle cloned them. Added board snapshot/restore around hero power triggers. Fixed.~~
- ~~**SellCardFromHand missing aura cleanup** — No `AuraManager.RemoveAurasForCard()` call in hand sell path. Added. Fixed.~~
- ~~**Golden card doubles buffed stats** — `CreateGoldenVersion` used current (possibly buffed) stats. Changed to use `_baseAttack`/`_baseHealth`. Fixed.~~
- ~~**Combo effect asymmetry** — `ApplyComboEffects` only checked synergy1's comboTribe. Now checks both synergy sides. Fixed.~~

### Production Audit Round 3 — Fixed (February 24, 2026)
6 bugs fixed. Commit: `2094bd2`

- ~~**CalculateCombatDamage dead code** — `EightPlayerManager.CalculateCombatDamage()` never called. Wired into GameManager for 5+ player games. Fixed.~~
- ~~**SynergyTarget.Random not tribe-filtered** — Cups T6 Aegis could hit non-Cups cards. Now filters by tribe before random pick. Fixed.~~
- ~~**NetworkCardData missing hasWindfury/hasVenomous** — Added both fields to serialization and reconstruction. Fixed.~~
- ~~**Pairing history stale entries** — Eliminated players left stale entries in pairingHistory dict. Now pruned each round. Fixed.~~
- ~~**Cost reduction floor prevents free cards** — `Mathf.Max(1, ...)` blocked cost going to 0. Changed to `Mathf.Max(0, ...)`. Fixed.~~
- ~~**MidasTouch description** — Description lacked once-per-turn note. Updated to "Gain 1 coin (once per turn)". Fixed.~~

### Triple-Agent Audit Findings (February 24, 2026)
3 independent audit agents ran in parallel. Score: 5.5-6.01/10. The following are NEW issues not yet fixed, categorized by confidence:

**HIGH CONFIDENCE (2+ agents agree):**

| ID | Finding | Severity | Status |
|----|---------|----------|--------|
| TA-1 | ArcaneBolt kills bypass death pipeline (no Deathrattle/Reborn) + card resurrects after combat via board restore | CRITICAL | OPEN |
| TA-2 | Cleave damage (synergy + ability paths) bypasses GainArmorAbility.ApplyArmor | HIGH | OPEN |
| TA-3 | RandomTransformAbility takes card from pool without removing (pool duplication) | HIGH | OPEN |
| TA-4 | Reborn+OnAllyDeath: per BG rules, Reborn deaths SHOULD trigger OnAllyDeath on surviving allies (our fix may be wrong) | HIGH | NEEDS DESIGN DECISION |
| TA-5 | Reroller hero power costs 1 gold = same as normal refresh (zero value) | HIGH | OPEN |
| TA-6 | Windfury: dead target counterattacks during second strike | MEDIUM | OPEN |
| TA-7 | Dewdrop Fairy assigned to Wands, should be Cups | LOW | OPEN |
| TA-8 | Upgrade costs T3-T5 are 1 gold above BG standard | MEDIUM | OPEN |

**SINGLE-AGENT (need verification):**

| ID | Finding | Severity | Status |
|----|---------|----------|--------|
| TA-9 | OnBuy/Passive synergy triggers never called (Coins T2, Wands T2/T4 are dead code) | CRITICAL | NEEDS VERIFICATION |
| TA-10 | Intuitive Novice: `hasAegis` never set despite `EffectType.Aegis` in CardDatabase | CRITICAL | NEEDS VERIFICATION |
| TA-11 | Turn 1 shop may be empty (RefreshShop gated by `turnNumber > 1`, but InitializeAI may cover it) | CRITICAL | NEEDS VERIFICATION |
| TA-12 | Pentacles T6 cost reduction only applies to Pentacles cards, not all cards per PLAN.md | HIGH | OPEN |
| TA-13 | Cups T6 gives Aegis to 1 random Cup; PLAN.md says "Shield all 2" | HIGH | DESIGN MISMATCH |
| TA-14 | Swords T6 gives +2/+2; PLAN.md says "Cleave" | HIGH | DESIGN MISMATCH |
| TA-15 | Hero power selection callback overwritten for multi-human players | HIGH | OPEN |
| TA-16 | RecruiterPower doesn't call CheckAndResolveTriples (triple from hero power missed) | HIGH | OPEN |
| TA-17 | Deathrattle damage bypasses Aegis (DealDamage skips hasAegis check) | MEDIUM | OPEN |
| TA-18 | OnSell bonus double-counted (synergy + ability both fire for Pentacles OnSell cards) | HIGH | OPEN |
| TA-19 | LifeTap modifies Player.Health without syncing GameManager.playerHealths | MEDIUM | OPEN |
| TA-20 | Aura removal can kill wounded card (health drops to 0 when aura-granter sold) | MEDIUM | OPEN |
| TA-21 | AI never uses hero powers | MEDIUM | OPEN |
| TA-22 | HeroPowerManager not cleared between games | LOW | OPEN |

### Balance Findings (from Triple-Agent Audit — February 24, 2026)
Balance auditor score: 5.5/10.

**Critical Balance:**
- Synergy tier stacking makes Stars (+6/+5 at 6 units) and Swords (+5/+2) dramatically OP vs other tribes
- Coins tribe only 11 cards (T6 synergy nearly impossible to achieve)
- Only 5 T6 cards for 6 tribes — late game feels random not strategic

**Hero Power Tier List:**
- **S Tier**: Midas Touch (free +1 gold), Economist (+1 net gold)
- **A Tier**: Tactician (free +2/+2), Healer (+3 HP), Fortify (Aegis), Recruiter (card advantage)
- **B Tier**: Empower, Blade Master, War Chief, Arcane Bolt
- **C Tier**: Reroller (zero value), Life Tap (strictly worse than Midas Touch)

**Card Pool:**
- Battlecry overrepresented (28.5% of all cards)
- Swords has 22 cards (above 15-20 target), Coins has 11 (below target)
- Overstatted: Leviathan (T6, 5/12+Guardian), Abyssal Leviathan (T5, 4/10+Guardian), Warmonger (T4, 6/5+OnAllyDeath)
- Pentacles gets zero combat stats from synergies (pure economy)

**Economy:**
- Gold curve matches BG exactly (3 base, +1/turn, cap 10)
- Upgrade costs T3/T4/T5 are each 1 gold above BG standard

### Medium Priority
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
