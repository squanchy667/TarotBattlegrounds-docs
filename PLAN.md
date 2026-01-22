# 🎮 Tarot Battlegrounds - Master Plan

> **Purpose**: This document is the single source of truth for the project. Any agent or developer can pick up a task with minimal context by reading this file.

**Last Updated**: January 22, 2026  
**Current Phase**: Core Engine Completion  
**Goal**: Build a fully functional, theme-agnostic auto-battler engine that can be reskinned infinitely

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
| Tribe Synergies | 🔴 NOT STARTED | Critical for core engine |
| AI Opponents | 🟡 Placeholder | Basic AI exists, needs strategy |
| 4-Player Lobby | 🔴 NOT STARTED | Single-player simulation only |
| Health UI Bug | ✅ Fixed | Combat damage now reflects in UI |
| Sell from Hand | ✅ Fixed | Can sell from board or hand |

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
**Goal**: Generic tribe/tag system for any theme

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| C1: Create `TribeType` enum (generic: TypeA, TypeB, TypeC, TypeD) | HIGH | 30m | 🔴 TODO |
| C2: Add tribe[] field to Card SO | HIGH | 30m | 🔴 TODO |
| C3: Create `SynergyManager.cs` | HIGH | 2h | 🔴 TODO |
| C4: Implement tribe count detection | HIGH | 1h | 🔴 TODO |
| C5: Implement basic synergy bonuses | HIGH | 3h | 🔴 TODO |
| C6: Test with 4 tribes, 3+ cards each | HIGH | 2h | 🔴 TODO |

**Tarot Theme Tribes** (for reference):
- Pentacles (Earth) → +1 gold on sell
- Cups (Water) → Heal adjacent minions
- Swords (Air) → Piercing damage (TBD)
- Wands (Fire) → Buff attack (TBD)

**Agent Quick Start**: Read `product/game-design/tarot-mechanics.md` for tribe details.

---

### Phase D: AI System (Sprint 5)
**Goal**: Competent AI opponents for single-player testing

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| D1: Create `AIController.cs` | HIGH | 2h | 🔴 TODO |
| D2: Implement basic buy logic (prioritize stats) | HIGH | 2h | 🔴 TODO |
| D3: Implement tier upgrade logic | MEDIUM | 1h | 🔴 TODO |
| D4: Implement board positioning | MEDIUM | 2h | 🔴 TODO |
| D5: Add 3 AI difficulty levels | LOW | 3h | 🔴 TODO |
| D6: Balance test: AI vs AI 100 runs | HIGH | 2h | 🔴 TODO |

**Agent Quick Start**: Look at existing `Player.cs` for state management, `TavernManager.cs` for shop actions.

---

### Phase E: 4-Player Lobby (Sprint 6)
**Goal**: Simulate full game with 4 players (local, not networked)

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| E1: Create `LobbyManager.cs` | HIGH | 2h | 🔴 TODO |
| E2: Initialize 4 players (1 human, 3 AI) | HIGH | 1h | 🔴 TODO |
| E3: Implement round-robin matchmaking | HIGH | 2h | 🔴 TODO |
| E4: Track eliminations and placements | HIGH | 2h | 🔴 TODO |
| E5: End game when 1 player remains | HIGH | 1h | 🔴 TODO |
| E6: Full game test (4 players, ~15 turns) | HIGH | 2h | 🔴 TODO |

**Agent Quick Start**: Read `product/game-design/core-gameplay.md` for loop definition.

---

### Phase F: Card Pool Expansion (Sprint 7)
**Goal**: Enough cards for meaningful gameplay

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| F1: Design 5 cards per tier (30 total) | HIGH | 3h | 🔴 TODO |
| F2: Create ScriptableObjects for all cards | HIGH | 2h | 🔴 TODO |
| F3: Distribute abilities across tiers | HIGH | 1h | 🔴 TODO |
| F4: Distribute tribes evenly | HIGH | 1h | 🔴 TODO |
| F5: Balance pass (100+ AI games) | HIGH | 4h | 🔴 TODO |

**Agent Quick Start**: Look at existing cards in `Assets/ScriptableObjects/Cards/`.

---

### Phase G: Core Engine Polish (Sprint 8)
**Goal**: Ready to branch as template

| Task | Priority | Effort | Status |
|------|----------|--------|--------|
| G1: Remove all tarot-specific strings from code | HIGH | 2h | 🔴 TODO |
| G2: Create `ThemeConfig.cs` for theme settings | HIGH | 2h | 🔴 TODO |
| G3: Document "How to create a new skin" | HIGH | 3h | 🔴 TODO |
| G4: Create template card database (generic) | MEDIUM | 2h | 🔴 TODO |
| G5: Full playtest (10+ games) | HIGH | 4h | 🔴 TODO |
| G6: Create `template` branch | HIGH | 30m | 🔴 TODO |

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
| C: Tribes | 6 | 0 | ⬜⬜⬜⬜⬜⬜ 0% |
| D: AI | 6 | 0 | ⬜⬜⬜⬜⬜⬜ 0% |
| E: Lobby | 6 | 0 | ⬜⬜⬜⬜⬜⬜ 0% |
| F: Cards | 5 | 0 | ⬜⬜⬜⬜⬜ 0% |
| G: Polish | 6 | 0 | ⬜⬜⬜⬜⬜⬜ 0% |
| **TOTAL** | **40** | **0** | **0%** |

---

## 🔄 Update History

| Date | Update |
|------|--------|
| Jan 22, 2026 | Initial PLAN.md created - Core engine roadmap defined |

---

## ❓ Open Questions

1. Should abilities be ScriptableObjects or just enums with switch logic?
2. How many cards minimum per tier for good gameplay?
3. Should AI difficulty affect shop RNG or just decision making?

---

*This document is maintained by the development team and AI agents. Always pull latest before starting work.*
