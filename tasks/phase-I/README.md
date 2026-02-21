# Phase I: Online Infrastructure

> Establish AWS backend (Cognito, DynamoDB, Lambda) and integrate authenticated multiplayer with Photon lobbies, matchmaking, and reconnection.

## Overview

Phase I transforms Tarot Battlegrounds from a local-only game into a fully online multiplayer experience. Players will register/login via AWS Cognito, get matched by skill level, and play in authenticated 4-player Photon lobbies with reconnection support. The phase culminates in a live playtest via CloudFront.

**Key Deliverables:**
- Cognito authentication (register, login, guest mode)
- DynamoDB player profiles
- Lambda matchmaking queue
- 4-player Photon lobbies with auth tokens
- Reconnection handling (rejoin within 60s)

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| I-B1 | T001-T003 (Cognito auth + DynamoDB) | network-engineer, aws-webgl-deployer |
| I-B2 | T004-T007 (Auth UI + matchmaking + lobbies) | unity-game-developer, network-engineer |
| I-B3 | T008-T010 (Reconnection + testing) | unity-game-developer, tarot-test-agent |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T001 | Set up Cognito user pool + identity provider | HIGH | PENDING | — | — | network-engineer |
| T002 | Create DynamoDB player profiles table | HIGH | PENDING | — | T003 | aws-webgl-deployer |
| T003 | Lambda auth endpoints (register, login, verify) | HIGH | PENDING | T001, T002 | — | network-engineer |
| T004 | Unity CognitoAuthManager integration | HIGH | PENDING | T003 | T005 | unity-game-developer |
| T005 | Auth UI flow (login, register, guest mode) | HIGH | PENDING | T004 | — | unity-game-developer |
| T006 | Matchmaking queue (Lambda + DynamoDB) | HIGH | PENDING | T003 | T007 | network-engineer |
| T007 | Unity MatchmakingManager + queue UI | HIGH | PENDING | T006 | — | unity-game-developer |
| T008 | 4-player Photon lobby with auth tokens | HIGH | PENDING | T004 | — | network-engineer |
| T009 | Reconnection handling (rejoin within 60s) | MEDIUM | PENDING | T008 | — | unity-game-developer |
| T010 | Integration testing + live playtest | HIGH | PENDING | T005, T007, T008, T009 | — | tarot-test-agent |

## Acceptance Criteria

- [ ] Players can register and login via Cognito
- [ ] Matchmaking pairs players by skill level
- [ ] 4-player lobbies work with authenticated sessions
- [ ] Reconnection recovers player state within 60s
- [ ] Live playtest via CloudFront URL passes

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase I details and AWS architecture
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
