# 🎮 Tarot Battlegrounds - Master Plan

> **Purpose**: This document is the single source of truth for the project. Any agent or developer can pick up a task with minimal context by reading this file.

**Last Updated**: January 30, 2026
**Current Phase**: Phase M Complete → Phase I (AWS Online Multiplayer)
**Goal**: Build a fully functional, theme-agnostic auto-battler engine that can be reskinned and played online

---

## 📋 Project Vision

### The Big Picture
```
┌─────────────────────────────────────────────────┐
│           CORE ENGINE (Template Branch)          │
│  ─────────────────────────────────────────────  │
│  • Game Loop (Recruit → Combat → Repeat)        │
│  • Tavern System (Buy/Sell/Reroll/Tiers)        │
│  • Combat System (Turn-based auto-battle)       │
│  • AI Opponents                                  │
│  • Ability/Synergy Framework                    │
│  • Minimal Debug UI                             │
└─────────────────────────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
   ┌─────────┐   ┌─────────┐   ┌─────────┐
   │ TAROT   │   │ ANIME   │   │ SPORTS  │
   │  SKIN   │   │  SKIN   │   │  SKIN   │
   └─────────┘   └─────────┘   └─────────┘
```

### Design Principle
The core engine must be **100% theme-agnostic**:
- Cards = `ID + Stats + AbilityType + TribeTag[]`
- Abilities = Enum-based (Battlecry, Deathrattle, OnAttack, etc.)
- All text/names/art loaded from **ScriptableObject data packs**
- A "skin" = New card database + Art assets + UI theme = **Zero code changes**

---

## 🎯 Current Status

| System | Status | Notes |
|--------|--------|-------|
| Game Loop | ✅ Complete | Recruit ↔ Combat phases working |
| Tavern/Shop | ✅ Complete | Buy/Sell/Reroll/Tiers functional |
| Combat Core | ✅ Complete | Turn-based simulation working |
| Event-Driven UI | ✅ Complete | All player events firing |
| Ability Framework | ✅ Complete | Battlecry, Deathrattle, OnAttack, Taunt |
| Tribe Synergies | ✅ Complete | Multi-tribe, combos, tiered effects |
| AI Opponents | ✅ Complete | 3 difficulty levels, integrated into GameManager |
| 4-Player Lobby | ✅ Complete | Round-robin matchmaking, eliminations |
| Card Tooltips | ✅ Complete | Hover shows card details/abilities |
| Selection Manager | ✅ Complete | Click-to-deselect, cross-panel selection |
| Health UI Bug | ✅ Fixed | Combat damage now reflects in UI |
| Sell from Hand | ✅ Fixed | Can sell from board or hand |
| AI Integration | ✅ Fixed | AI controllers properly initialized |
| Theme System | ✅ Complete | ThemeConfig + ThemeManager for reskinning |
| GameConfig | ✅ Complete | Automatic player/AI selection |
| Template Branch | ✅ Created | Clean engine ready for new themes |
| UI Theming | ✅ Complete | All UI implements IThemeable, hot-swap ready |
| Tarot Skin | ✅ Complete | tarot-skin branch with full Tarot theme |
| End Turn Button | ✅ Complete | Skip recruit phase timer early |
| Freeze Shop | ✅ Complete | Toggle freeze, auto-unfreeze next turn |
| Board Reordering | ✅ Complete | Click-to-swap cards on board |
| Game Over Screen | ✅ Complete | Standings, placement, Play Again/Quit |
| Triple/Fusion | ✅ Complete | 3 copies → golden card + discovery popup |
| Background Visuals | ✅ Complete | Themeable game background |

### 🧪 Testing Status
- ~~Tribe synergy activation at 2/4/6 thresholds~~ ✅ 35+ automated tests (SynergyTests.cs)
- ~~Card abilities triggering correctly~~ ✅ Covered in synergy + combat tests
- ~~Full 4-player game completion~~ ✅ 100-game batch tests (AIBattleTests.cs)
- ~~AI balance across difficulty levels~~ ✅ Hard > Easy verified, win rate < 45%
- ~~Cross-tribe combos~~ ✅ All 4 combo pairs tested
- Full playtest (10+ games) for Phase G - **Manual testing required**

#### Test Suites (Jan 29, 2026)
- `SynergyTests.cs` — 35+ NUnit tests: tribe counting, thresholds (2/4/6), combos, effect application
- `AIBattleTests.cs` — 15+ NUnit tests: batch simulations (100 games), balance, difficulty scaling, tribe competitiveness
- `CardSystemTests.cs` — Card buy/sell/play mechanics
- `CombatTests.cs` — Combat simulation, damage caps, special abilities
- `EconomyTests.cs` — Gold economy, tier upgrades
- `EdgeCaseTests.cs` — Boundary conditions across all systems

---

## 🚀 Roadmap to Core Engine Completion

### Phase A: Fix Critical Bugs (Sprint 2)
**Goal**: Stabilize existing systems before adding features

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| A1: Fix health UI not updating after combat | HIGH | 2h | ✅ DONE |
| A2: Enable sell from hand (not just board) | HIGH | 1h | ✅ DONE |
| A3: Validate all event subscriptions working | MEDIUM | 1h | ✅ DONE |

**Agent Quick Start**: Read `developer/architecture.md` for event system, check `GameUIManager.cs` for UI subscriptions.

---

### Phase B: Ability Framework (Sprint 3)
**Goal**: Generic ability system that any theme can use

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| B1: Create `AbilityType` enum | HIGH | 30m | ✅ DONE |
| B2: Create `IAbility` interface | HIGH | 1h | ✅ DONE |
| B3: Implement Battlecry (on play) | HIGH | 2h | ✅ DONE |
| B4: Implement Deathrattle (on death) | HIGH | 2h | ✅ DONE |
| B5: Implement OnAttack trigger | MEDIUM | 2h | ✅ DONE |
| B6: Implement Taunt (must be attacked first) | HIGH | 2h | ✅ DONE |
| B7: Add ability field to Card SO | HIGH | 30m | ✅ DONE |
| B8: Test with 5+ ability cards | HIGH | 2h | ✅ DONE |

**Ability Types for Core Engine**:
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

**Agent Quick Start**: Read `developer/card-system.md`, look at existing `Card.cs` ScriptableObject.

---

### Phase C: Tribe Synergy System (Sprint 4)
**Goal**: Flexible, data-driven tribe system with multi-tribe cards and cross-tribe combos

#### Design Principles
```
┌─────────────────────────────────────────────────────────────┐
│                    SYNERGY DESIGN GOALS                      │
├─────────────────────────────────────────────────────────────┤
│ 1. DATA-DRIVEN     → Change synergies without touching code │
│ 2. MULTI-TRIBE     → Cards can have 1, 2, or even 3 tribes  │
│ 3. COMBO-FRIENDLY  → Tribes interact with each other        │
│ 4. TIERED SCALING  → 2/4/6 thresholds with escalating power │
│ 5. DIVERSE EFFECTS → Economy, Combat, Healing, Buffs        │
└─────────────────────────────────────────────────────────────┘
```

#### Core Enums
```csharp
public enum TribeType { None, Pentacles, Cups, Swords, Wands }

public enum SynergyTrigger {
    Passive, StartOfCombat, EndOfCombat, OnSell, OnBuy, OnDeath, EndOfTurn
}

public enum SynergyEffect {
    BuffAttack, BuffHealth, BuffStats, BonusGold, ReduceCost,
    BonusDamage, Piercing, Cleave, HealFlat, HealPercent, Shield,
    ExtraCardDraw, Discover
}

public enum SynergyTarget {
    AllTribeMembers, AllFriendly, Adjacent, Random, Self
}
```

#### TribeSynergy ScriptableObject Structure
```csharp
[CreateAssetMenu(menuName = "TarotBG/Tribe Synergy")]
public class TribeSynergy : ScriptableObject
{
    public TribeType tribe;
    public string tribeName;
    public string description;
    public Color themeColor;
    public SynergyTier[] tiers;

    // Cross-tribe combo
    public TribeType comboTribe;
    public SynergyEffect comboEffect;
    public int comboValue;
}
```

#### Tarot Tribe Designs
| Tribe | Theme | Tier 2 | Tier 4 | Tier 6 | Combo With |
|-------|-------|--------|--------|--------|------------|
| **Pentacles** | Economy | +1 gold on sell | +2 gold on sell | -1 cost on buy | Cups: +1 gold/turn |
| **Cups** | Healing | Heal adjacent 1 | Heal tribe 2 | Shield all 2 | Wands: Heals buff attack |
| **Swords** | Aggro | +1 attack | +2 bonus damage | Cleave | Pentacles: Kills give gold |
| **Wands** | Buffs | +1/+1 random | +1/+1 tribe | +2 attack all | Swords: Double attack buffs |

| Task | Description | Effort | Status |
|------|-------------|--------|--------|
| C1: Create `TribeType` enum | Define tribe types | 15m | ✅ DONE |
| C2: Create `SynergyTrigger` enum | When synergies activate | 15m | ✅ DONE |
| C3: Create `SynergyEffect` enum | What synergies do | 15m | ✅ DONE |
| C4: Create `SynergyTarget` enum | Who is affected | 15m | ✅ DONE |
| C5: Create `TribeSynergy` ScriptableObject | Data container with tiers + combos | 1h | ✅ DONE |
| C6: Add `TribeType[] tribes` to Card SO | Multi-tribe support on cards | 30m | ✅ DONE |
| C7: Create `SynergyManager.cs` | Count tribes on board | 1.5h | ✅ DONE |
| C8: Implement tier threshold checking | Detect 2/4/6 thresholds | 1h | ✅ DONE |
| C9: Hook synergies into triggers | OnSell, StartOfCombat, EndOfTurn, etc. | 2h | ✅ DONE |
| C10: Implement cross-tribe combo detection | Detect and apply combo bonuses | 1h | ✅ DONE |
| C11: Create 4 TribeSynergy SOs | Pentacles, Cups, Swords, Wands | 1h | ✅ DONE |
| C12: Create 4 mixed-tribe test cards | Cards with 2 tribes each | 30m | ✅ DONE |
| C13: Test tribe tiers | Full board, verify all tiers trigger | 1h | ✅ DONE |
| C14: Test cross-tribe combos | Verify combo bonuses activate | 1h | ✅ DONE |

**Agent Quick Start**:
1. Read `developer/architecture.md` for system patterns
2. Look at `Assets/Scripts/Abilities/` for similar ScriptableObject patterns
3. Reference `product/game-design/tarot-mechanics.md` for tribe lore

---

### Phase D: AI System (Sprint 5)
**Goal**: Competent AI opponents for single-player testing

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| D1: Create `AIController.cs` | HIGH | 2h | ✅ DONE |
| D2: Implement basic buy logic (prioritize stats) | HIGH | 2h | ✅ DONE |
| D3: Implement tier upgrade logic | MEDIUM | 1h | ✅ DONE |
| D4: Implement board positioning | MEDIUM | 2h | ✅ DONE |
| D5: Add 3 AI difficulty levels | LOW | 3h | ✅ DONE |
| D6: Balance test: AI vs AI 100 runs | HIGH | 2h | ✅ DONE |

**Agent Quick Start**: Look at existing `Player.cs` for state management, `TavernManager.cs` for shop actions.

---

### Phase E: 4-Player Lobby (Sprint 6)
**Goal**: Simulate full game with 4 players (local, not networked)

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| E1: Create `LobbyManager.cs` | HIGH | 2h | ✅ DONE |
| E2: Initialize 4 players (1 human, 3 AI) | HIGH | 1h | ✅ DONE |
| E3: Implement round-robin matchmaking | HIGH | 2h | ✅ DONE |
| E4: Track eliminations and placements | HIGH | 2h | ✅ DONE |
| E5: End game when 1 player remains | HIGH | 1h | ✅ DONE |
| E6: Full game test (4 players, ~15 turns) | HIGH | 2h | ✅ DONE |

**Agent Quick Start**: Read `product/game-design/core-gameplay.md` for loop definition.

---

### Phase F: Card Pool Expansion (Sprint 7)
**Goal**: Enough cards for meaningful gameplay

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| F1: Design 5 cards per tier (30 total) | HIGH | 3h | ✅ DONE |
| F2: Create ScriptableObjects for all cards | HIGH | 2h | ✅ DONE |
| F3: Distribute abilities across tiers | HIGH | 1h | ✅ DONE |
| F4: Distribute tribes evenly | HIGH | 1h | ✅ DONE |
| F5: Balance pass (100+ AI games) | HIGH | 4h | ✅ DONE |

**Agent Quick Start**: Look at existing cards in `Assets/ScriptableObjects/Cards/`.

---

### Phase G: Core Engine Polish (Sprint 8)
**Goal**: Ready to branch as template

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| G1: Remove all tarot-specific strings from code | HIGH | 2h | ✅ DONE |
| G2: Create `ThemeConfig.cs` for theme settings | HIGH | 2h | ✅ DONE |
| G3: Document "How to create a new skin" | HIGH | 3h | ✅ DONE |
| G4: Create template card database (generic) | MEDIUM | 2h | ✅ DONE |
| G5: Full playtest (10+ games) | HIGH | 4h | 🟡 TESTING |
| G6: Create `template` branch | HIGH | 30m | ✅ DONE |
| G7: Make AI/player selection more automatic | MEDIUM | 2h | ✅ DONE |

**Phase G Deliverables:**
- `ThemeConfig.cs` - ScriptableObject for theme data (tribes, colors, UI text)
- `ThemeManager.cs` - Singleton for accessing active theme
- `GameConfig.cs` - Static class for game settings (player count, AI difficulty)
- `SKINNING-GUIDE.md` - Complete documentation for creating new themes
- `template` branch - Clean engine ready for reskinning

---

### Phase H: Skinnable Theme System (Sprint 9)
**Goal**: Allow complete visual reskinning without code changes

```
┌─────────────────────────────────────────────────────────────┐
│                    THEME SYSTEM ARCHITECTURE                  │
├─────────────────────────────────────────────────────────────┤
│  ThemeConfig (ScriptableObject)                              │
│  ├── Color Palette (primary, secondary, accent, etc.)       │
│  ├── Font Assets (title, body, stats)                       │
│  ├── Card Frame Sprites (common, rare, epic, legendary)     │
│  ├── UI Panel Backgrounds                                    │
│  ├── Button Styles                                           │
│  └── Sound Effects Pack (optional)                          │
│                                                              │
│  ThemeManager (Singleton)                                    │
│  ├── LoadTheme(ThemeConfig)                                 │
│  ├── ApplyToAllUI()                                         │
│  └── OnThemeChanged event                                   │
│                                                              │
│  IThemeable (Interface for UI components)                   │
│  └── ApplyTheme(ThemeConfig)                                │
└─────────────────────────────────────────────────────────────┘
```

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| H1: Create `ThemeConfig` ScriptableObject | HIGH | 2h | ✅ DONE |
| H2: Create `ThemeManager.cs` singleton | HIGH | 2h | ✅ DONE |
| H3: Create `IThemeable` interface | HIGH | 1h | ✅ DONE |
| H4: Make all UI panels implement IThemeable | HIGH | 4h | ✅ DONE |
| H5: Create card frame sprite slots | MEDIUM | 2h | ✅ DONE |
| H6: Create default "Tarot" theme | HIGH | 2h | ✅ DONE |
| H7: Create alternate "Debug/Generic" theme | MEDIUM | 2h | ✅ DONE |
| H8: Theme hot-swap testing | HIGH | 2h | ✅ DONE |

**Phase H Deliverables (COMPLETE):**
- `IThemeable.cs` - Interface + ThemeableUI base class
- `ThemeConfig.cs` - Extended with colors, card frames, fonts, tribe data
- `ThemeManager.cs` - Singleton with runtime Tarot theme fallback
- All UI components (CardDisplayUI, GameUIManager, ShopUI, HandUI, BoardUI, CombatLogUI) now implement IThemeable
- Editor menu items: `Game/Create Default Tarot Theme` and `Game/Create Debug Theme`
- All components subscribe to OnThemeChanged for hot-swap

**Agent Quick Start**: Look at `CardDisplayUI.cs` for current UI patterns, create theme assets in `Assets/Resources/`.

---

### Phase T: Automated Testing (Sprint 10)
**Goal**: Verify all systems work end-to-end via automated AI gameplay

```
┌─────────────────────────────────────────────────────────────┐
│                    TESTING STRATEGY                           │
├─────────────────────────────────────────────────────────────┤
│  1. AI vs AI batch runs (100+ games)                        │
│     → Verify games complete without errors                  │
│     → Collect win rates, game lengths, tribe usage          │
│                                                              │
│  2. Synergy verification                                    │
│     → Force specific board states                           │
│     → Assert 2/4/6 thresholds trigger correctly             │
│     → Assert cross-tribe combos activate                    │
│                                                              │
│  3. Balance analysis                                        │
│     → No tribe dominates (>40% win rate)                    │
│     → Games last 8-20 turns on average                      │
│     → All tiers see play                                    │
│                                                              │
│  4. If automated tests inconclusive → manual playtest       │
└─────────────────────────────────────────────────────────────┘
```

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| T1: Create automated test runner (AI vs AI batch) | HIGH | 2h | ✅ DONE |
| T2: Add synergy threshold verification tests | HIGH | 2h | ✅ DONE |
| T3: Add cross-tribe combo verification tests | HIGH | 1h | ✅ DONE |
| T4: Run 100+ AI games, collect statistics | HIGH | 2h | ✅ DONE |
| T5: Analyze results - balance report | HIGH | 1h | ✅ DONE |
| T6: Fix any issues found during testing | MEDIUM | TBD | ✅ DONE (no issues found) |
| T7: Manual playtest if automated results inconclusive | LOW | 2h | ⏭️ SKIPPED (automated tests sufficient) |

**Phase T Deliverables (COMPLETE):**
- `SynergyTests.cs` — 35+ NUnit tests: tribe counting, threshold tiers (2/4/6), cross-tribe combos, effect application, prebuilt board scenarios
- `AIBattleTests.cs` — 15+ NUnit tests: game completion, 100-game batch runs (2p and 4p), balance (win rates <45%, game length 5-25 turns), difficulty scaling, tribe competitiveness, damage cap verification

**Agent Quick Start**: Look at existing `AITestRunner.cs` and `AIController.cs`. Test files in `Assets/Scripts/Tests/Editor/`.

---

### Phase P: Polish Pass (Sprint 11)
**Goal**: Add missing gameplay features and improve game feel

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| P1: Fix Tier 6 "999 gold" display → show "MAX" | HIGH | 15m | ✅ DONE |
| P2: Win/Loss resolution screen (GameOverUI) | HIGH | 2h | ✅ DONE |
| P3: Triple/Fusion system with Discovery (golden cards) | HIGH | 3h | ✅ DONE |
| P4: Background and arena visuals (theme support) | MEDIUM | 30m | ✅ DONE |
| P5: Board positioning + card reordering (click-to-swap) | HIGH | 1.5h | ✅ DONE |
| P6: End Turn button (skip recruit timer) | HIGH | 1h | ✅ DONE |
| P7: Freeze Shop button (toggle with auto-unfreeze) | HIGH | 1h | ✅ DONE |

**Phase P Deliverables (COMPLETE):**
- `GameOverUI.cs` — Game over overlay with standings, placement, Play Again / Quit buttons
- `DiscoveryUI.cs` — Triple discovery popup offering 3 higher-tier cards
- `Card.cs` — Added `isGolden`, `CreateGoldenVersion()` for triple/fusion
- `Player.cs` — Added `ShopFrozen`, `ToggleShopFreeze()`, `SwapBoardCards()`, `CheckAndResolveTriples()`, `AddDiscoveryCard()`
- `GameManager.cs` — Added `GameOverData`, `OnGameOver` event, elimination tracking, `EndRecruitPhaseEarly()`
- `GameUIManager.cs` — End Turn + Freeze buttons, background image, board swap/slot handlers, MAX tier display
- `BoardUI.cs` — Empty slot click events, board card swap mode
- `ThemeConfig.cs` — Added `maxTierText`, `freezeButtonText`, `unfreezeButtonText`, `gameOverTitle`, `playAgainText`, `quitToMenuText`, `gameBackgroundColor`, `gameBackground`, `goldenCardColor`

**Agent Quick Start**: All code is on `tarot-skin` branch. Scene work still needed in Unity Inspector to wire new buttons and panels.

---

### Phase M: Multiplayer Bug Fixes (Sprint 12)
**Goal**: Fix all bugs found during 2-player ParrelSync testing before online launch

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| M1: Fix SynergyManager global state — make synergy calculation per-player | HIGH | 2h | ✅ DONE |
| M2: Fix DiscoveryUI race condition — per-player pending discoveries | HIGH | 1h | ✅ DONE |
| M3: Fix shop pool integrity — reserve cards when placed in shop | HIGH | 1.5h | ✅ DONE |
| M4: Fix Player 2 buy flow — ensure RPC shop sync and card lookup work | HIGH | 2h | ✅ DONE |
| M5: Fix tavern upgrade state sync — include upgrade cost in NetworkPlayerState | HIGH | 1.5h | ✅ DONE |
| M6: Fix AbilityManager memory leak — clear static dict between games | MEDIUM | 15m | ✅ DONE |
| M7: Fix combat log overwrite — filter to local player's battle | MEDIUM | 1h | ✅ DONE |
| M8: Fix RefreshShop coin property bypass — use setter consistently | LOW | 15m | ✅ DONE |

**Agent Quick Start**: Read `developer/architecture.md` for manager pattern. All bugs found via ParrelSync 2-player testing. Key files: `SynergyManager.cs`, `DiscoveryUI.cs`, `TavernManager.cs`, `NetworkGameBridge.cs`, `Player.cs`, `GameManager.cs`.

---

### Phase I: AWS Online Multiplayer (Sprint 13-15)
**Goal**: Real-time online multiplayer with matchmaking

```
┌─────────────────────────────────────────────────────────────┐
│                    AWS ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────┤
│  CLIENT (Unity)                                              │
│  ├── NetworkManager.cs (connection handling)                │
│  ├── GameStateSync.cs (state synchronization)               │
│  └── MatchmakingUI.cs (lobby/queue interface)               │
│                                                              │
│  AWS SERVICES                                                │
│  ├── Cognito         → User authentication                  │
│  ├── API Gateway     → REST endpoints                       │
│  ├── Lambda          → Game logic validation                │
│  ├── DynamoDB        → Player profiles, match history       │
│  ├── GameLift/AppSync→ Real-time matchmaking               │
│  └── WebSocket API   → Live game state updates              │
│                                                              │
│  GAME FLOW                                                   │
│  1. Player authenticates (Cognito)                          │
│  2. Joins matchmaking queue (GameLift/AppSync)              │
│  3. Match found → WebSocket connection established          │
│  4. Game state synced every action (recruit phase)          │
│  5. Combat results calculated server-side (Lambda)          │
│  6. Match ends → Results stored (DynamoDB)                  │
└─────────────────────────────────────────────────────────────┘
```

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| I1: Set up AWS account and IAM roles | HIGH | 2h | 🔴 TODO |
| I2: Create Cognito user pool | HIGH | 2h | 🔴 TODO |
| I3: Create DynamoDB tables (players, matches) | HIGH | 2h | 🔴 TODO |
| I4: Create API Gateway + Lambda for auth | HIGH | 4h | 🔴 TODO |
| I5: Implement Unity authentication flow | HIGH | 3h | 🔴 TODO |
| I6: Set up WebSocket API Gateway | HIGH | 4h | 🔴 TODO |
| I7: Create `NetworkManager.cs` | HIGH | 4h | 🔴 TODO |
| I8: Create `GameStateSync.cs` | HIGH | 6h | 🔴 TODO |
| I9: Implement matchmaking queue | HIGH | 4h | 🔴 TODO |
| I10: Server-side combat validation | MEDIUM | 4h | 🔴 TODO |
| I11: Reconnection handling | MEDIUM | 3h | 🔴 TODO |
| I12: Leaderboard system | LOW | 3h | 🔴 TODO |
| I13: Full online playtest | HIGH | 4h | 🔴 TODO |

**Agent Quick Start**: Read AWS documentation, consider using Mirror or Netcode for GameObjects as Unity networking layer on top of WebSocket transport.

---

## 📂 Repository Links

| Repo | Purpose |
|------|---------|
| [TarotBattlegrounds-docs](https://github.com/squanchy667/TarotBattlegrounds-docs) | Documentation (this repo) |
| [TarotBattlegrounds-POC](https://github.com/squanchy667/TarotBattlegrounds-POC) | Unity project code |

---

## 🤖 Agent Instructions

### How to Pick Up a Task
1. Read this `PLAN.md` to understand current status
2. Find an uncompleted task (🔴 TODO)
3. Read linked documentation for context
4. Check `resources/known-issues.md` for related bugs
5. Implement the task
6. Update this PLAN.md with new status (🟡 IN PROGRESS or ✅ DONE)
7. Update `resources/changelog.md` with what you did
8. Commit and push changes

### Task Naming Convention
```
[PHASE][NUMBER]: Description
Example: B3: Implement Battlecry ability
```

### Commit Message Format
```
[Sprint X] Task ID: Brief description

- Detail 1
- Detail 2
```

### When You're Done
1. Update task status in PLAN.md
2. Add entry to changelog.md
3. If you found new issues, add to known-issues.md
4. Push all changes

---

## 📊 Progress Tracker

| Phase | Tasks | Done | Progress |
|-------|-------|------|----------|
| A: Bug Fixes | 3 | 3 | 🟩🟩🟩 100% |
| B: Abilities | 8 | 8 | 🟩🟩🟩🟩🟩🟩🟩🟩 100% |
| C: Tribes | 14 | 14 | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩 100% |
| D: AI | 6 | 6 | 🟩🟩🟩🟩🟩🟩 100% |
| E: Lobby | 6 | 6 | 🟩🟩🟩🟩🟩🟩 100% |
| F: Cards | 5 | 5 | 🟩🟩🟩🟩🟩 100% |
| G: Polish | 7 | 6 | 🟩🟩🟩🟩🟩🟩🟨 86% |
| H: Skinning | 8 | 8 | 🟩🟩🟩🟩🟩🟩🟩🟩 100% |
| T: Testing | 7 | 7 | 🟩🟩🟩🟩🟩🟩🟩 100% |
| P: Polish | 7 | 7 | 🟩🟩🟩🟩🟩🟩🟩 100% |
| M: MP Bugs | 8 | 8 | 🟩🟩🟩🟩🟩🟩🟩🟩 100% |
| I: Online | 13 | 0 | ⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜ 0% |
| **TOTAL** | **92** | **78** | **85%** |

### 🎯 Milestone Targets
- **Core Engine Complete**: Phases A-G (ready for skinning)
- **Skinnable Release**: Phase H complete (can create new themes)
- **Battle-Tested**: Phase T complete (automated verification passed)
- **Polished**: Phase P complete (all known bugs fixed)
- **Multiplayer-Ready**: Phase M complete (all MP bugs fixed)
- **Online Beta**: Phase I complete (multiplayer ready)

---

## 🔄 Update History

| Date | Update |
|------|--------|
| Jan 30, 2026 | Added Phase M (Multiplayer Bug Fixes) — 8 bugs from ParrelSync 2-player testing |
| Jan 29, 2026 | Phase T COMPLETE - SynergyTests.cs (35+ tests) and AIBattleTests.cs (15+ tests) for tribe thresholds, combos, AI balance, batch simulations |
| Jan 29, 2026 | Phase P COMPLETE - 7 polish features: End Turn, Freeze Shop, Board Reordering, Game Over Screen, Triple/Fusion with Discovery, Background Visuals, MAX tier fix |
| Jan 29, 2026 | Testing tasks COMPLETE (C13, C14, E6, F5) - Added SynergyTests.cs and AIBattleTests.cs with 40+ automated tests |
| Jan 29, 2026 | Added Phase T (Automated Testing) and Phase P (Polish). Updated roadmap: Docs → Testing → Polish → Online |
| Jan 29, 2026 | Documentation refresh - updated known-issues, changelog, SUMMARY, PLAN with current state |
| Jan 28, 2026 | Phase H COMPLETE - Skinnable theme system implemented on tarot-skin branch |
| Jan 28, 2026 | Phase G (6/7) - Theme system, GameConfig, SKINNING-GUIDE.md, template branch created |
| Jan 27, 2026 | AI integrated into GameManager, card tooltips added, selection manager added |
| Jan 27, 2026 | Added Phase H (Skinnable Theme System) and Phase I (AWS Online Multiplayer) |
| Jan 26, 2026 | Phase D, E, F implemented - AI system, Lobby, 30-card database complete |
| Jan 26, 2026 | Phase C implemented - Tribe synergies with multi-tribe and combos |
| Jan 23, 2026 | Phase C redesigned - Expanded to 14 tasks with multi-tribe and combo support |
| Jan 22, 2026 | Initial PLAN.md created - Core engine roadmap defined |

---

## ❓ Open Questions

1. ~~Should abilities be ScriptableObjects or just enums with switch logic?~~ → **Decided: Enums with switch**
2. ~~How many cards minimum per tier for good gameplay?~~ → **Decided: 5 per tier (30 total)**
3. ~~Should AI difficulty affect shop RNG or just decision making?~~ → **Decided: Decision making only**
4. AWS: GameLift vs custom WebSocket matchmaking?
5. AWS: Should combat be validated server-side or trust client?
6. ~~Theme system: Support runtime theme switching or only at startup?~~ → **Decided: Runtime switching via OnThemeChanged event**

---

*This document is maintained by the development team and AI agents. Always pull latest before starting work.*

---

## Major Upgrade: 7-Phase Expansion

> **Started**: February 2026
> **Goal**: Content expansion (100+ cards, hero powers, new abilities), production polish (combat animations, VFX, sound, UI overhaul), and online infrastructure (matchmaking, ranked ladder, 2-8 players)

### What's Already Done vs What's Needed

| System | Current State | Target State |
|--------|---------------|--------------|
| Card Pool | ~30 cards, 4 tribes | 100+ cards, 6 tribes (+ Stars, Coins) |
| Abilities | 4 triggers (Battlecry, Deathrattle, OnAttack, OnDamaged) | 8 triggers (+ OnAllyDeath, OnAllySummoned, OnSell, Aura) |
| Effects | Basic (damage, buff, taunt) | 12+ (+ Reborn, Windfury, Venomous, SummonToken, StealBuff, GainArmor, BuffAllTribes, RandomTransform) |
| Hero Powers | None | 12 hero powers with cooldowns, recruit-phase integration |
| Combat Display | Instant simulation, text log | Animated replay with VFX, SFX, skip/speed controls |
| VFX/SFX | None | VFXManager (particle pooling), SFXManager (AudioSource pooling), MusicManager |
| UI | Functional placeholder | Card frames, hover zoom, drag-drop, collection viewer, settings, main menu |
| Online | Photon LAN (ParrelSync) | Cognito auth, matchmaking queue, 4-player Photon lobbies |
| Ranked | None | MMR (Elo), Bronze-Legend tiers, seasons, leaderboards |
| Player Count | 4 (local) | 2-8 (online), with ghost opponents and spectator mode |
| Network Optimization | Basic Photon RPCs | DeltaStateCompressor, message batching, bandwidth profiling |
| Content Pipeline | Manual ScriptableObjects | DevZone CRUD → S3 → RuntimeDataLoader |
| Testing | 50+ NUnit tests | Extended suites for new abilities, hero powers, 6-tribe balance, 8-player scenarios |

### Phase Structure

| Phase | Theme | Tasks | Dependencies | Key Deliverables |
|-------|-------|-------|-------------|------------------|
| **I** | Online Infrastructure | T001-T010 (10) | Phases A-M done | Cognito auth, matchmaking, Photon lobbies, reconnection |
| **II** | New Abilities & Hero Powers | T101-T118 (18) | — | 4 new triggers, 8 new effects, 12 hero powers |
| **III** | Card Pool to 100+ | T201-T216 (16) | Phase II | Stars + Coins tribes, 65 new cards, 6-tribe rebalance |
| **IV** | Combat Animation & VFX | T301-T320 (20) | — | CombatReplay, CombatAnimator, VFX/SFX managers |
| **V** | UI Overhaul | T401-T418 (18) | Phase IV | Card frames, drag-drop, hover zoom, collection viewer |
| **VI** | Ranked System | T501-T515 (15) | Phase I | MMR ladder, seasons, leaderboards, match history |
| **VII** | 8-Player Scale & Polish | T601-T620 (20) | Phases I, III, IV | 8-player rooms, ghost opponents, delta compression, final balance |

**Total new tasks: 117** (bringing project total to 195)

**Parallel tracks:**
- Track A: I → II → III → VI (online → content → ranked)
- Track B: I → IV → V → VII (online → animation → UI → scale)
- Phase VII is the convergence point requiring I, III, and IV complete

### Key Technical Decisions

1. **Combat Replay** (not real-time animation) — keep `SimulateBattle()` instant, record `CombatReplay` data structure, play back via `CombatAnimator` coroutines. This preserves deterministic combat while adding visual polish.

2. **Hero Powers as Distinct System** — player-level (not card-level), cooldown-gated, recruit-phase-only, separate from the ability system. `HeroPower` base class + `HeroPowerManager` singleton + `HeroPowerDatabase` ScriptableObject.

3. **DevZone as Primary Content Pipeline** — new cards authored via DevZone CRUD → published to S3 → loaded at runtime via `RuntimeDataLoader`. The `devzone-sync-engineer` keeps Unity and DevZone enums in sync.

4. **New Tribes** — Stars (Celestial/Control theme, synergy: shield + foresight) and Coins (Currency/Scaling theme, synergy: gold generation + stat scaling). Both integrate into existing 2/4/6 threshold system with cross-tribe combos.

5. **Network Delta Compression** — `DeltaStateCompressor` sends only changed fields per frame, critical for 8-player scaling where full state broadcast would exceed bandwidth budget.

6. **Dual-Repo Sync** — `devzone-sync-engineer` keeps Unity C# enums (`TribeType`, `AbilityTrigger`, `HeroPowerType`) in sync with DevZone TypeScript enums. Runs as a quality gate after content phases.

### Agent Roster (26 Total)

| Category | Existing (15) | New (11) |
|----------|--------------|----------|
| **Orchestration** | tarot-orchestrator | upgrade-orchestrator |
| **Implementation** | unity-game-developer | — |
| **Content Design** | game-designer | card-content-designer (opus) |
| **Systems Engineering** | — | ability-engineer, hero-power-engineer, combat-vfx-engineer, sfx-engineer (haiku), ui-engineer |
| **Infrastructure** | — | network-engineer, ranked-backend-engineer |
| **Integration** | — | devzone-sync-engineer |
| **Quality Assurance** | tarot-game-auditor, game-auditor, tarot-test-agent, tester, debugger, fixer | balance-auditor (opus) |
| **Diagnostics** | log-analyzer | — |
| **Deployment** | deploy-orchestrator, aws-webgl-deployer, deploy-validator, unity-webgl-builder, ssl-dns-agent | — |

See [development-agents.md](development-agents.md) for full agent details, batch assignments, and model routing.

### Quality Gates

Between each phase, all gates must pass before advancing:

1. **tarot-game-auditor** — Rules compliance audit, score >= 7.0/10
2. **tarot-test-agent** — NUnit test suite passes, 0 regressions
3. **balance-auditor** — No tribe win rate > 45%, meta health check (active in Phases II, III, VI, VII)
4. **DevZone publish verification** — All new cards/abilities/hero powers published and loadable via RuntimeDataLoader

### Deliverables Summary

| Metric | Current | After Upgrade |
|--------|---------|---------------|
| Cards | ~30 | 100+ |
| Tribes | 4 (Pentacles, Cups, Swords, Wands) | 6 (+ Stars, Coins) |
| Ability Triggers | 4 | 8 |
| Ability Effects | ~5 | 12+ |
| Hero Powers | 0 | 12 |
| Combat Display | Instant + text log | Animated replay + VFX + SFX |
| Player Count | 4 (local) | 2-8 (online) |
| Auth | None | Cognito (register, login, guest) |
| Matchmaking | None | Queue-based with MMR range |
| Ranked | None | Elo MMR, Bronze-Legend, seasons |
| Agents | 15 | 26 |
| Commands | 4 | 10 |
| Total Tasks | 92 | 195 |
| NUnit Tests | 50+ | 150+ (estimated) |
