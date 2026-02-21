# Phase VII: 8-Player Scale & Polish

> Scale from 4-player to 8-player lobbies with ghost opponents, dynamic elimination, delta compression, and final balance/performance pass.

## Overview

Phase VII is the final scaling and polish phase. The game upgrades from 4-player to 8-player lobbies via Photon, introducing ghost opponent pairing for odd numbers, round-robin matchmaking, dynamic elimination (8 to 4 to 2 to 1), and health scaling. Network optimization via delta compression and message batching targets >50% bandwidth reduction. A comprehensive balance pass ensures all 6 tribes remain viable in the 8-player meta, and performance profiling ensures 60fps with 8 players + full VFX in a WebGL build under 50MB.

**Prerequisites:** Phase I (online infrastructure), Phase III (100+ card pool), Phase IV (combat VFX) complete.

**Key Deliverables:**
- 8-player Photon room configuration
- Ghost opponent system for odd-number pairing
- Round-robin pairing with dynamic elimination
- Opponent board viewer and mini-map for 8 players
- DeltaStateCompressor + message batching
- 8-player AI balance pass (shop pool scaling, economy rebalance)
- Performance optimization (60fps, <50MB WebGL build)

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| VII-B1 | T601-T606 (8-player lobby) | network-engineer, unity-game-developer |
| VII-B2 | T607-T610 (Opponent viewer) | ui-engineer |
| VII-B3 | T611-T613 (Delta compression) | network-engineer |
| VII-B4 | T614-T620 (Balance + performance) | balance-auditor, tester |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T601 | 8-player Photon room config | HIGH | PENDING | — | T602, T603, T605, T606, T607, T608 | network-engineer |
| T602 | Ghost opponent system | HIGH | PENDING | T601 | — | network-engineer |
| T603 | Round-robin pairing (8 players) | HIGH | PENDING | T601 | T604 | unity-game-developer |
| T604 | Dynamic elimination (8 to 4 to 2 to 1) | HIGH | PENDING | T603 | T614 | unity-game-developer |
| T605 | Player health scaling for 8 | MEDIUM | PENDING | T601 | — | unity-game-developer |
| T606 | Lobby UI for 8 players | HIGH | PENDING | T601 | — | ui-engineer |
| T607 | Opponent board viewer (7 opponents) | HIGH | PENDING | T601 | T609, T610 | ui-engineer |
| T608 | Mini-map (all player health) | HIGH | PENDING | T601 | — | ui-engineer |
| T609 | Opponent board transitions | MEDIUM | PENDING | T607 | — | ui-engineer |
| T610 | Spectator mode | MEDIUM | PENDING | T607 | — | ui-engineer |
| T611 | DeltaStateCompressor | HIGH | PENDING | — | T612 | network-engineer |
| T612 | Message batching | MEDIUM | PENDING | T611 | T613 | network-engineer |
| T613 | Bandwidth profiling | HIGH | PENDING | T612 | T617 | network-engineer |
| T614 | 8-player AI balance pass | HIGH | PENDING | T604 | T615 | balance-auditor |
| T615 | Shop pool scaling for 8 | HIGH | PENDING | T614 | T616 | balance-auditor |
| T616 | Economy rebalance for 8 players | MEDIUM | PENDING | T615 | — | balance-auditor |
| T617 | Performance profiling (8p + VFX) | HIGH | PENDING | T613 | T618 | tester |
| T618 | Memory optimization | MEDIUM | PENDING | T617 | T619 | tester |
| T619 | WebGL build size optimization | MEDIUM | PENDING | T618 | T620 | tester |
| T620 | Final 8-player playtest | HIGH | PENDING | T614-T619 | — | tester |

## Acceptance Criteria

- [ ] 8-player lobbies work via Photon
- [ ] Ghost opponent pairs correctly with odd numbers
- [ ] Dynamic elimination tracks standings
- [ ] Delta compression reduces bandwidth by >50%
- [ ] All 6 tribes viable in 8-player meta
- [ ] Maintains 60fps with 8 players + VFX
- [ ] WebGL build size under 50MB

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase VII details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
