# 🎮 Tarot Battlegrounds - Master Plan

> **Purpose**: This document is the single source of truth for the project. Any agent or developer can pick up a task with minimal context by reading this file.

**Last Updated**: January 28, 2026
**Current Phase**: Phase H Complete → Testing → Phase I Online
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

### 🧪 Currently Testing
- Tribe synergy activation at 2/4/6 thresholds
- Card abilities triggering correctly
- Full 4-player game completion
- AI balance across difficulty levels
- Full playtest (10+ games) for Phase G

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
| C13: Test tribe tiers | Full board, verify all tiers trigger | 1h | 🟡 TESTING |
| C14: Test cross-tribe combos | Verify combo bonuses activate | 1h | 🟡 TESTING |

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
| E6: Full game test (4 players, ~15 turns) | HIGH | 2h | 🟡 TESTING |

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
| F5: Balance pass (100+ AI games) | HIGH | 4h | 🟡 TESTING |

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

### Phase I: AWS Online Multiplayer (Sprint 10-12)
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
| C: Tribes | 14 | 12 | 🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟨🟨 86% |
| D: AI | 6 | 6 | 🟩🟩🟩🟩🟩🟩 100% |
| E: Lobby | 6 | 5 | 🟩🟩🟩🟩🟩🟨 83% |
| F: Cards | 5 | 4 | 🟩🟩🟩🟩🟨 80% |
| G: Polish | 7 | 6 | 🟩🟩🟩🟩🟩🟩🟨 86% |
| H: Skinning | 8 | 8 | 🟩🟩🟩🟩🟩🟩🟩🟩 100% |
| I: Online | 13 | 0 | ⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜ 0% |
| **TOTAL** | **70** | **52** | **74%** |

### 🎯 Milestone Targets
- **Core Engine Complete**: Phases A-G (ready for skinning)
- **Skinnable Release**: Phase H complete (can create new themes)
- **Online Beta**: Phase I complete (multiplayer ready)

---

## 🔄 Update History

| Date | Update |
|------|--------|
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
