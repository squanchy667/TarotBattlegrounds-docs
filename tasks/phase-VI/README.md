# Phase VI: Ranked System

> Implement MMR-based ranked play with Elo calculation, rank tiers (Bronze-Legend), seasons, leaderboard, and match history.

## Overview

Phase VI adds a competitive ranked system backed by DynamoDB and Lambda. Players earn MMR via Elo calculation with placement matches, progress through rank tiers from Bronze to Legend, and compete on a leaderboard. Seasons reset monthly with rewards. Matchmaking uses MMR range for fair pairing. Full match history and post-game stats round out the experience.

**Prerequisites:** Phase I complete (Cognito auth and DynamoDB infrastructure required).

**Key Deliverables:**
- DynamoDB ranked tables + Lambda endpoints
- MMR calculation (Elo + placement matches)
- Rank tier system (Bronze through Legend)
- Season management with monthly reset and rewards
- MMR-based matchmaking
- Leaderboard, match history, and post-game stats UI
- PlayerProfileManager Unity integration

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| VI-B1 | T501-T503 (Backend) | ranked-backend-engineer |
| VI-B2 | T504-T509 (Features) | ranked-backend-engineer, ui-engineer |
| VI-B3 | T510-T515 (UI + testing) | ranked-backend-engineer, tarot-test-agent |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T501 | DynamoDB ranked tables | HIGH | PENDING | — | T502 | ranked-backend-engineer |
| T502 | Lambda ranked endpoints | HIGH | PENDING | T501 | T503, T511, T513, T514 | ranked-backend-engineer |
| T503 | MMR calculation (Elo + placement) | HIGH | PENDING | T502 | T504, T506, T510 | ranked-backend-engineer |
| T504 | Rank tier system (Bronze-Legend) | HIGH | PENDING | T503 | T505, T507 | ranked-backend-engineer |
| T505 | Season management (monthly reset) | MEDIUM | PENDING | T504 | T508 | ranked-backend-engineer |
| T506 | Matchmaking with MMR range | HIGH | PENDING | T503 | T509 | ranked-backend-engineer |
| T507 | Rank display UI | HIGH | PENDING | T504 | — | ui-engineer |
| T508 | Season rewards UI | MEDIUM | PENDING | T505 | — | ui-engineer |
| T509 | Queue UI with wait time | MEDIUM | PENDING | T506 | — | ui-engineer |
| T510 | Post-game stats screen | HIGH | PENDING | T503 | T515 | ui-engineer |
| T511 | Match history backend | MEDIUM | PENDING | T502 | T512 | ranked-backend-engineer |
| T512 | Match history UI | MEDIUM | PENDING | T511 | — | ui-engineer |
| T513 | Leaderboard UI | HIGH | PENDING | T502 | T515 | ui-engineer |
| T514 | PlayerProfileManager Unity | HIGH | PENDING | T502 | T515 | ranked-backend-engineer |
| T515 | Ranked integration testing | HIGH | PENDING | T510, T513, T514 | — | tarot-test-agent |

## Acceptance Criteria

- [ ] MMR adjusts correctly after matches
- [ ] Rank tiers display correctly (Bronze-Legend)
- [ ] Seasons reset monthly with rewards
- [ ] Matchmaking uses MMR range
- [ ] Leaderboard shows top 100
- [ ] Match history shows last 50 games

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase VI details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
