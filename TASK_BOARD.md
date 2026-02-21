# Task Board

> Active task tracking for Tarot Battlegrounds. See [PLAN.md](PLAN.md) for full phase details and [development-agents.md](development-agents.md) for agent assignments.

## Task Lifecycle

```
PENDING → IN_PROGRESS → IN_REVIEW → DONE
                                      ↑
BLOCKED ──────────────────────────────┘
```

## Progress Summary

| Phase | Theme | Tasks | Done | Progress |
|-------|-------|-------|------|----------|
| A | Bug Fixes | 3 | 3 | 100% |
| B | Ability Framework | 8 | 8 | 100% |
| C | Tribe Synergies | 14 | 14 | 100% |
| D | AI System | 6 | 6 | 100% |
| E | 4-Player Lobby | 6 | 6 | 100% |
| F | Card Pool Expansion | 5 | 5 | 100% |
| G | Core Engine Polish | 7 | 6 | 86% |
| H | Skinnable Theme System | 8 | 8 | 100% |
| T | Automated Testing | 7 | 7 | 100% |
| P | Polish Pass | 7 | 7 | 100% |
| M | Multiplayer Bug Fixes | 8 | 8 | 100% |
| I | Online Infrastructure | 7 | 0 | 0% |
| II | New Abilities & Hero Powers | 18 | 0 | 0% |
| III | Card Pool to 100+ | 16 | 0 | 0% |
| IV | Combat Animation & VFX | 20 | 0 | 0% |
| V | UI Overhaul | 18 | 0 | 0% |
| VI | Ranked System | 15 | 0 | 0% |
| VII | 8-Player Scale & Polish | 20 | 0 | 0% |
| **Total** | | **192** | **78** | **41%** |

## Milestones

- **Core Engine Complete** (Phases A-G) — ready for skinning
- **Skinnable Release** (Phase H) — can create new themes
- **Battle-Tested** (Phase T) — automated verification passed
- **Polished** (Phase P) — all known bugs fixed
- **Multiplayer-Ready** (Phase M) — all MP bugs fixed
- **Online Beta** (Phase I) — AWS auth + matchmaking + Photon lobbies
- **Content Expansion** (Phase II + III) — 100+ cards, 6 tribes, 12 hero powers, new abilities
- **Production Polish** (Phase IV + V) — animated combat, VFX, SFX, full UI overhaul
- **Ranked Launch** (Phase VI) — MMR ladder, seasons, leaderboards
- **Full Scale** (Phase VII) — 8-player games, network optimization, final balance

## Phase Dependency Diagram

```
Phases A-M (78/78 DONE)
        │
        ▼
   ┌─────────┐
   │ Phase I  │  Online Infrastructure (T001-T007)
   └────┬─────┘
        │
        ├──────────────────────────────────────┐
        │                                      │
        ▼                                      ▼
   ┌──────────┐                          ┌──────────┐
   │ Phase II │  Abilities & Hero Powers │ Phase IV │  Combat Animation & VFX
   └────┬─────┘                          └────┬─────┘
        │                                      │
        ▼                                      ▼
   ┌───────────┐                         ┌──────────┐
   │ Phase III │  Card Pool to 100+      │ Phase V  │  UI Overhaul
   └────┬──────┘                         └────┬─────┘
        │                                      │
        ├──────────────────┐                   │
        │                  │                   │
        ▼                  ▼                   │
   ┌──────────┐      ┌───────────┐             │
   │ Phase VI │      │ Phase VII │ ◄───────────┘
   │  Ranked  │      │  8-Player │
   └──────────┘      └───────────┘
```

**Parallel tracks:**
- Track A: I → II → III → VI (online → content → ranked)
- Track B: I → IV → V → VII (online → animation → UI → scale)
- Phase VII depends on both tracks completing (I, III, IV)

---

## Phase I: Online Infrastructure (T001-T007)

> Extends existing DevZone AWS stack (SAM template, DynamoDB, Lambda, JWT auth). No Cognito needed.

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T001 | Add PlayersTable to SAM template + deploy | HIGH | PENDING | — | T002 |
| T002 | Game auth endpoints (register, login, guest, profile) | HIGH | PENDING | T001 | T003, T004 |
| T003 | Unity GameAuthManager + login/register/guest UI | HIGH | PENDING | T002 | T005, T006 |
| T004 | Matchmaking queue endpoint (DynamoDB + Lambda) | HIGH | PENDING | T002 | T005 |
| T005 | Unity MatchmakingManager + queue UI | HIGH | PENDING | T003, T004 | T006, T007 |
| T006 | Wire Photon lobbies with player identity + reconnection | HIGH | PENDING | T003 | T007 |
| T007 | Integration testing + live playtest via CloudFront | HIGH | PENDING | T005, T006 | — |

---

## Phase II: New Abilities & Hero Powers (T101-T118)

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T101 | Add OnAllyDeath trigger + IAbility subclass | HIGH | PENDING | — | T105, T108, T117 |
| T102 | Add OnAllySummoned trigger + IAbility subclass | HIGH | PENDING | — | T108, T117 |
| T103 | Add OnSell trigger + IAbility subclass | MEDIUM | PENDING | — | T117 |
| T104 | Add Aura trigger (continuous buff) + IAbility subclass | HIGH | PENDING | — | T111, T117 |
| T105 | Implement Reborn effect (revive with 1 HP) | HIGH | PENDING | T101 | T117 |
| T106 | Implement Windfury effect (attack twice) | HIGH | PENDING | — | T117 |
| T107 | Implement Venomous effect (instant kill) | HIGH | PENDING | — | T117 |
| T108 | Implement SummonToken effect (create token on trigger) | HIGH | PENDING | T101, T102 | T117 |
| T109 | Implement StealBuff effect (steal enemy buff) | MEDIUM | PENDING | — | T117 |
| T110 | Implement GainArmor effect (damage shield) | MEDIUM | PENDING | — | T117 |
| T111 | Implement BuffAllTribes effect (buff all of a tribe) | MEDIUM | PENDING | T104 | T117 |
| T112 | Implement RandomTransform effect (transform into random card) | LOW | PENDING | — | T117 |
| T113 | HeroPower base class + HeroPowerManager singleton | HIGH | PENDING | — | T114, T115 |
| T114 | HeroPowerDatabase (12 hero powers) | HIGH | PENDING | T113 | T115 |
| T115 | Hero power recruit phase integration | HIGH | PENDING | T113, T114 | T116 |
| T116 | Hero power UI (button, cooldown, selection) | HIGH | PENDING | T115 | T118 |
| T117 | CombatManager integration (new triggers + effects) | HIGH | PENDING | T101-T112 | T118 |
| T118 | DevZone enum sync (new abilities + hero powers) | HIGH | PENDING | T117 | — |

---

## Phase III: Card Pool to 100+ (T201-T216)

> Blocked by: Phase II (new abilities required for card design)

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T201 | Add Stars tribe to TribeType enum (Unity + DevZone) | HIGH | PENDING | Phase II | T203, T209 |
| T202 | Add Coins tribe to TribeType enum (Unity + DevZone) | HIGH | PENDING | Phase II | T204, T210 |
| T203 | Design 10 Stars cards (tier 1-3) | HIGH | PENDING | T201 | T209, T214 |
| T204 | Design 10 Coins cards (tier 1-3) | HIGH | PENDING | T202 | T210, T214 |
| T205 | Design 15 Swords expansion cards (tier 1-5) | HIGH | PENDING | — | T214 |
| T206 | Design 15 Cups expansion cards (tier 1-5) | HIGH | PENDING | — | T214 |
| T207 | Design 10 Pentacles expansion cards (tier 1-4) | MEDIUM | PENDING | — | T214 |
| T208 | Design 5 Wands expansion cards (tier 2-5) | MEDIUM | PENDING | — | T214 |
| T209 | Stars synergy tiers (2/4/6) | HIGH | PENDING | T203 | T211, T213 |
| T210 | Coins synergy tiers (2/4/6) | HIGH | PENDING | T204 | T212, T213 |
| T211 | Cross-tribe combo: Stars + Swords | MEDIUM | PENDING | T209 | T213 |
| T212 | Cross-tribe combo: Coins + Pentacles | MEDIUM | PENDING | T210 | T213 |
| T213 | Rebalance existing tribe synergies for 6-tribe meta | HIGH | PENDING | T209, T210 | T215 |
| T214 | Update card tier distribution (ensure smooth curve) | MEDIUM | PENDING | T203-T208 | T215 |
| T215 | Update AI card evaluation for new cards/abilities | HIGH | PENDING | T213 | T216 |
| T216 | 100-game AI balance pass | HIGH | PENDING | T215 | — |

---

## Phase IV: Combat Animation & VFX (T301-T320)

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T301 | CombatReplay data structure (actions, events, results) | HIGH | PENDING | — | T302, T303 |
| T302 | CombatManager replay recording (alongside instant sim) | HIGH | PENDING | T301 | T304 |
| T303 | Replay serialization for network broadcast | MEDIUM | PENDING | T301 | T318 |
| T304 | CombatAnimator coroutine-based playback | HIGH | PENDING | T302 | T305, T316 |
| T305 | CombatCardVisual (per-card visual state + animations) | HIGH | PENDING | T304 | T306, T307 |
| T306 | Attack animation (lunge + impact) | HIGH | PENDING | T305 | T316 |
| T307 | Death animation (fade + dissolve) | HIGH | PENDING | T305 | T316 |
| T308 | VFXManager with particle pooling | HIGH | PENDING | — | T309, T310, T311, T316 |
| T309 | Attack VFX (swoosh trail, impact burst) | MEDIUM | PENDING | T308 | T319 |
| T310 | Ability VFX (per-ability-type particles) | MEDIUM | PENDING | T308 | T319 |
| T311 | Death VFX (soul release, card shatter) | MEDIUM | PENDING | T308 | T319 |
| T312 | SFXManager with AudioSource pooling | HIGH | PENDING | — | T313 |
| T313 | SFXConfig ScriptableObject (event-to-clip mapping) | HIGH | PENDING | T312 | T314 |
| T314 | Combat SFX integration (attack, damage, death clips) | MEDIUM | PENDING | T313 | T319 |
| T315 | MusicManager (menu, recruit, combat tracks, crossfade) | MEDIUM | PENDING | — | — |
| T316 | UI integration (combat phase shows animated replay) | HIGH | PENDING | T304, T308 | T317, T320 |
| T317 | Skip button + speed controls for replay | MEDIUM | PENDING | T316 | — |
| T318 | Network replay broadcast (host → clients) | HIGH | PENDING | T303 | — |
| T319 | Ability trigger VFX/SFX in replay | MEDIUM | PENDING | T310, T314 | — |
| T320 | Performance profiling (particle count, frame budget) | HIGH | PENDING | T316 | — |

---

## Phase V: UI Overhaul (T401-T418)

> Blocked by: Phase IV (combat animation integration required)

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T401 | Card art pipeline setup (S3 + DevZone upload) | HIGH | PENDING | Phase IV | — |
| T402 | Card frame system (rarity borders) | HIGH | PENDING | — | T403, T404 |
| T403 | Tribe-colored card backgrounds | MEDIUM | PENDING | T402 | — |
| T404 | Golden card overlay for triples | MEDIUM | PENDING | T402 | — |
| T405 | Hover zoom (enlarged preview with full stats) | HIGH | PENDING | — | T406 |
| T406 | Card tooltip redesign (abilities, synergies, lore) | MEDIUM | PENDING | T405 | — |
| T407 | DragDropManager (core drag system) | HIGH | PENDING | — | T408, T409, T410 |
| T408 | Drag from shop to hand | HIGH | PENDING | T407 | — |
| T409 | Drag from hand to board | HIGH | PENDING | T407 | — |
| T410 | Board reorder via drag | HIGH | PENDING | T407 | — |
| T411 | Recruit timer animated countdown | MEDIUM | PENDING | — | — |
| T412 | Shop refresh animation | LOW | PENDING | — | — |
| T413 | Tier-up celebration effect | LOW | PENDING | — | — |
| T414 | Opponent board viewer (read-only peek) | HIGH | PENDING | Phase IV | — |
| T415 | Game over screen redesign (stats, animations) | HIGH | PENDING | — | — |
| T416 | Main menu redesign (Play, Collection, Settings, Profile) | HIGH | PENDING | — | T417, T418 |
| T417 | Collection viewer (all cards, filter by tribe/tier) | MEDIUM | PENDING | T416 | — |
| T418 | Settings menu (audio, graphics, controls) | LOW | PENDING | T416 | — |

---

## Phase VI: Ranked System (T501-T515)

> Blocked by: Phase I (online infrastructure required)

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T501 | DynamoDB tables for ranked (players, matches, seasons) | HIGH | PENDING | Phase I | T502 |
| T502 | Lambda endpoints (match-result, profile, leaderboard, season) | HIGH | PENDING | T501 | T503, T511, T513, T514 |
| T503 | MMR calculation (Elo with placement matches) | HIGH | PENDING | T502 | T504, T506, T510 |
| T504 | Rank tier system (Bronze-Legend, 4 divisions) | HIGH | PENDING | T503 | T505, T507 |
| T505 | Season management (monthly reset, rewards) | MEDIUM | PENDING | T504 | T508 |
| T506 | Matchmaking queue with MMR range | HIGH | PENDING | T503 | T509 |
| T507 | Rank display UI (tier badge, MMR, division) | HIGH | PENDING | T504 | T515 |
| T508 | Season rewards UI | MEDIUM | PENDING | T505 | T515 |
| T509 | Queue UI with estimated wait time | MEDIUM | PENDING | T506 | T515 |
| T510 | Post-game stats screen | HIGH | PENDING | T503 | T515 |
| T511 | Match history (last 50 matches) | MEDIUM | PENDING | T502 | T512 |
| T512 | Match history UI | MEDIUM | PENDING | T511 | T515 |
| T513 | Leaderboard UI (top 100 global) | HIGH | PENDING | T502 | T515 |
| T514 | PlayerProfileManager Unity integration | HIGH | PENDING | T502 | T515 |
| T515 | Ranked integration testing | HIGH | PENDING | T510, T513, T514 | — |

---

## Phase VII: 8-Player Scale & Polish (T601-T620)

> Blocked by: Phases I, III, and IV

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| T601 | 8-player Photon room configuration | HIGH | PENDING | Phase I | T602, T603, T605, T606, T607, T608 |
| T602 | Ghost opponent system (odd player pairing) | HIGH | PENDING | T601 | T604 |
| T603 | Round-robin pairing for 8 players | HIGH | PENDING | T601 | T604 |
| T604 | Dynamic elimination (8→4→2→winner) | HIGH | PENDING | T603 | T614 |
| T605 | Player health scaling for 8-player | MEDIUM | PENDING | T601 | — |
| T606 | Lobby UI for 8 players | HIGH | PENDING | T601 | — |
| T607 | Opponent board viewer (cycle through 7 opponents) | HIGH | PENDING | T601 | T609, T610 |
| T608 | Mini-map showing all player health/status | HIGH | PENDING | T601 | — |
| T609 | Opponent board transition animations | MEDIUM | PENDING | T607 | — |
| T610 | Spectator mode (watch after elimination) | MEDIUM | PENDING | T607 | — |
| T611 | DeltaStateCompressor (only send changes) | HIGH | PENDING | Phase I, Phase III | T612 |
| T612 | Message batching (aggregate RPCs per frame) | MEDIUM | PENDING | T611 | T613 |
| T613 | Bandwidth profiling + optimization | HIGH | PENDING | T612 | T617 |
| T614 | 8-player AI balance pass | HIGH | PENDING | Phase III, T604 | T615 |
| T615 | Shop pool scaling for 8 players | HIGH | PENDING | T614 | T616 |
| T616 | Economy rebalance for 8-player games | MEDIUM | PENDING | T615 | T620 |
| T617 | Performance profiling (8 players, 100+ cards, VFX) | HIGH | PENDING | Phase IV, T613 | T618 |
| T618 | Memory optimization (card pooling, texture atlas) | MEDIUM | PENDING | T617 | T619 |
| T619 | WebGL build size optimization | MEDIUM | PENDING | T618 | T620 |
| T620 | Final integration test + 8-player playtest | HIGH | PENDING | T614-T619 | — |

---

## Completed Phases (Summary)

Phases A through M are complete (78/78 tasks). See individual phase details in [PLAN.md](PLAN.md).

### Test Suites
- `SynergyTests.cs` — 35+ NUnit tests
- `AIBattleTests.cs` — 15+ NUnit tests
- `CardSystemTests.cs`, `CombatTests.cs`, `EconomyTests.cs`, `EdgeCaseTests.cs`
