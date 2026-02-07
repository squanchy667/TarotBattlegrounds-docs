# Phase M: Multiplayer

**Status**: COMPLETE
**Completed**: February 2026

## Overview

Online multiplayer using Photon PUN 2 with host-authoritative architecture. Deployed on WebGL via S3 + CloudFront with Photon Cloud handling all networking.

## Architecture

- **Networking**: Photon PUN 2 (WebSocketSecure for WebGL)
- **Model**: Host-authoritative — MasterClient runs game logic, clients receive state via RPCs
- **Hosting**: Photon Cloud (free 20 CCU tier, no server to manage)
- **Deployment**: S3 + CloudFront (~$0.15/month)

## Network Components

| Component | File | Role |
|-----------|------|------|
| PhotonConnector | Network/PhotonConnector.cs | Connection lifecycle singleton |
| NetworkGameBridge | Network/NetworkGameBridge.cs | RPC bridge between game and network |
| NetworkGameSetup | Network/NetworkGameSetup.cs | Player slot assignment on join |
| RoomManager | Network/RoomManager.cs | Room creation and joining |
| NetworkCardData | Network/NetworkCardData.cs | Card serialization for network |
| NetworkPlayerState | Network/NetworkPlayerState.cs | Full player state serialization |
| CardLookup | Network/CardLookup.cs | Card name to Card object resolution |

## What Gets Synced

| Data | Direction | Method |
|------|-----------|--------|
| Phase change | Host -> All | BroadcastPhaseChange |
| Player state | Host -> Owner | BroadcastPlayerState |
| Shop cards | Host -> Owner | BroadcastShopForPlayer |
| Combat result | Host -> All | BroadcastCombatResult |
| Timer | Host -> All | BroadcastTimerUpdate |
| Game over | Host -> All | BroadcastGameOver |
| Discovery cards | Host -> Owner | BroadcastDiscoveryForPlayer |

## Multiplayer Bug Fixes (Phase M)

All found and fixed during ParrelSync testing and 10-agent game audits.

| Fix | Issue | Root Cause |
|-----|-------|------------|
| M1 | Synergy singleton overwrite | Per-player `SynergySnapshot` pattern |
| M2 | Discovery not synced to clients | Added `BroadcastDiscoveryForPlayer` RPC |
| M3 | Shop cards duplicated across players | Reserve cards from pool when placed in shop |
| M4 | Player 2 cannot buy cards | Bounds check + shop sync after buy |
| M5 | Upgrade cost desync | `SyncedUpgradeCost` field on Player |
| M6 | Ability leak across games | `AbilityManager.ClearAll()` on game start/end |
| M7 | Combat log shows wrong battle | Filter to local player's battle |
| M8 | Coins property bypass | All coin changes use property setter for events |
| M9 | Card pool init race condition | `EnsureCardPoolInitialized()` + `WaitUntil` |
| M10 | Upgrade cost shows base on client | SyncedUpgradeCost in NetworkPlayerState |
| M11 | Phase panel wrong text/turn | `SetPhaseFromNetwork()` applies before UI refresh |

## Disconnect Handling

- Disconnected player's slot converts to AI control
- `GameManager.RegisterAIController()` adds AI for the slot
- Game continues without interruption

## Testing

- ParrelSync used for local multiplayer testing (3 test runs)
- 10-agent consensus audits for game logic bugs
- WebGL deployed and tested at CloudFront URL

## Related Documentation

- [Multiplayer Architecture](../../developer/multiplayer.md) — technical details
- [Deployment Guide](../../developer/deployment.md) — WebGL deployment
- [Known Issues](../../resources/known-issues.md) — remaining bugs
