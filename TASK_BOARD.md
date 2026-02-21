# Task Board

> Active task tracking for Tarot Battlegrounds. See [PLAN.md](PLAN.md) for full phase details.

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
| I | AWS Online Multiplayer | 13 | 0 | 0% |
| **Total** | | **92** | **78** | **85%** |

## Milestones

- **Core Engine Complete** (Phases A-G) — ready for skinning
- **Skinnable Release** (Phase H) — can create new themes
- **Battle-Tested** (Phase T) — automated verification passed
- **Polished** (Phase P) — all known bugs fixed
- **Multiplayer-Ready** (Phase M) — all MP bugs fixed
- **Online Beta** (Phase I) — AWS online multiplayer

## Active Phase: I — AWS Online Multiplayer

| ID | Task | Priority | Status | Depends On | Blocks |
|----|------|----------|--------|------------|--------|
| I1 | Set up AWS account and IAM roles | HIGH | PENDING | — | I2, I3 |
| I2 | Create Cognito user pool | HIGH | PENDING | I1 | I4, I5 |
| I3 | Create DynamoDB tables (players, matches) | HIGH | PENDING | I1 | I4 |
| I4 | Create API Gateway + Lambda for auth | HIGH | PENDING | I2, I3 | I5 |
| I5 | Implement Unity authentication flow | HIGH | PENDING | I4 | I7, I9 |
| I6 | Set up WebSocket API Gateway | HIGH | PENDING | I1 | I7, I8 |
| I7 | Create NetworkManager.cs | HIGH | PENDING | I5, I6 | I8, I9 |
| I8 | Create GameStateSync.cs | HIGH | PENDING | I7 | I10, I13 |
| I9 | Implement matchmaking queue | HIGH | PENDING | I7 | I13 |
| I10 | Server-side combat validation | MEDIUM | PENDING | I8 | I13 |
| I11 | Reconnection handling | MEDIUM | PENDING | I7 | I13 |
| I12 | Leaderboard system | LOW | PENDING | I3 | — |
| I13 | Full online playtest | HIGH | PENDING | I8, I9, I10, I11 | — |

## Completed Phases (Summary)

Phases A through M are complete (78/78 tasks). See individual phase details in [PLAN.md](PLAN.md).

### Test Suites
- `SynergyTests.cs` — 35+ NUnit tests
- `AIBattleTests.cs` — 15+ NUnit tests
- `CardSystemTests.cs`, `CombatTests.cs`, `EconomyTests.cs`, `EdgeCaseTests.cs`
