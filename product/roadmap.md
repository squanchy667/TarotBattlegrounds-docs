# Roadmap

> Phase timeline and milestones for Tarot Battlegrounds. See [PLAN.md](../PLAN.md) for full task breakdowns.

## Timeline

| Phase | Sprint | Theme | Status |
|-------|--------|-------|--------|
| A | Sprint 2 | Bug Fixes | DONE |
| B | Sprint 3 | Ability Framework | DONE |
| C | Sprint 4 | Tribe Synergies | DONE |
| D | Sprint 5 | AI System | DONE |
| E | Sprint 6 | 4-Player Lobby | DONE |
| F | Sprint 7 | Card Pool Expansion | DONE |
| G | Sprint 8 | Core Engine Polish | 86% |
| H | Sprint 9 | Skinnable Theme System | DONE |
| T | Sprint 10 | Automated Testing | DONE |
| P | Sprint 11 | Polish Pass | DONE |
| M | Sprint 12 | Multiplayer Bug Fixes | DONE |
| **I** | **Sprint 13-15** | **AWS Online Multiplayer** | **TODO** |

## Milestones

### Core Engine Complete (Phases A-G)
Engine ready for skinning — game loop, abilities, synergies, AI, lobby, card pool, polish.

### Skinnable Release (Phase H)
ThemeConfig + ThemeManager + IThemeable. Runtime theme hot-swap. Tarot skin on `tarot-skin` branch.

### Battle-Tested (Phase T)
50+ NUnit tests: SynergyTests (35+), AIBattleTests (15+), plus CardSystem, Combat, Economy, EdgeCase tests.

### Polished (Phase P)
End Turn button, Freeze Shop, Board Reordering, Game Over screen, Triple/Fusion with Discovery, Background visuals.

### Multiplayer-Ready (Phase M)
8 bugs fixed from ParrelSync 2-player testing: per-player synergy snapshots, discovery race conditions, shop pool integrity, state sync.

### Online Beta (Phase I) — Next
AWS online multiplayer with Cognito auth, WebSocket real-time state, DynamoDB profiles, matchmaking, server-side combat validation.

## Phase I Architecture

```
CLIENT (Unity)
├── NetworkManager.cs (connection handling)
├── GameStateSync.cs (state synchronization)
└── MatchmakingUI.cs (lobby/queue interface)

AWS SERVICES
├── Cognito         → User authentication
├── API Gateway     → REST endpoints
├── Lambda          → Game logic validation
├── DynamoDB        → Player profiles, match history
├── GameLift/AppSync→ Real-time matchmaking
└── WebSocket API   → Live game state updates
```

## Open Questions

1. AWS: GameLift vs custom WebSocket matchmaking?
2. AWS: Should combat be validated server-side or trust client?

## Update History

| Date | Update |
|------|--------|
| Jan 30, 2026 | Phase M complete — 8 multiplayer bugs fixed |
| Jan 29, 2026 | Phase T + P complete — testing and polish |
| Jan 28, 2026 | Phase H complete — skinnable theme system |
| Jan 27, 2026 | Phase G (6/7) — theme system, GameConfig |
| Jan 26, 2026 | Phases D, E, F complete — AI, lobby, cards |
| Jan 22, 2026 | Initial roadmap created |
