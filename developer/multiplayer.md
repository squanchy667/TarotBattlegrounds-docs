# Multiplayer Architecture

## Overview

Tarot Battlegrounds uses **Photon PUN 2** for online multiplayer. The architecture is host-authoritative: the MasterClient runs all game logic, and clients receive state via Photon RPCs.

## Network Stack

| Component | File | Role |
|-----------|------|------|
| `PhotonConnector` | Network/PhotonConnector.cs | Connection lifecycle singleton |
| `NetworkGameBridge` | Network/NetworkGameBridge.cs | RPC bridge between game and network |
| `NetworkGameSetup` | Network/NetworkGameSetup.cs | Player slot assignment on join |
| `RoomManager` | Network/RoomManager.cs | Room creation and joining |
| `NetworkCardData` | Network/NetworkCardData.cs | Card serialization for network |
| `NetworkPlayerState` | Network/NetworkPlayerState.cs | Full player state serialization |
| `CardLookup` | Network/CardLookup.cs | Card name to Card object resolution |

## Connection Flow

```
1. MainMenu -> "Multiplayer" button
2. PhotonConnector.Connect()
3. OnConnectedToMaster -> JoinLobby
4. LobbyUI shows available rooms
5. Player creates or joins room
6. OnJoinedRoom -> Load Game scene
7. NetworkGameSetup assigns player slots
8. GameManager.OnNetworkSetupComplete()
9. Host starts GameLoop; clients wait for RPCs
```

## Host-Authoritative Model

### Host (MasterClient)
- Runs the full `GameManager.GameLoop()` coroutine
- Manages the shared card pool (`TavernManager`)
- Controls AI players
- Generates shops, resolves combat, applies damage
- Broadcasts all state changes to clients

### Clients
- Do NOT run the game loop
- Send player actions (buy, sell, play, reroll, upgrade, freeze, end turn) to host via RPCs
- Receive full state broadcasts and apply them locally
- Render the received state

## State Synchronization

### What Gets Synced

| Data | Direction | Method |
|------|-----------|--------|
| Phase change | Host -> All | `BroadcastPhaseChange` |
| Player state (coins, tier, health, hand, board, shop) | Host -> Owner | `BroadcastPlayerState` |
| Shop cards | Host -> Owner | `BroadcastShopForPlayer` |
| Combat result | Host -> All | `BroadcastCombatResult` |
| Timer | Host -> All | `BroadcastTimerUpdate` |
| Game over | Host -> All | `BroadcastGameOver` |
| Discovery cards | Host -> Owner | `BroadcastDiscoveryForPlayer` |

### NetworkPlayerState

Full player state serialized for network:

```csharp
public class NetworkPlayerState
{
    public int playerIndex;
    public int coins;
    public int tavernTier;
    public int health;
    public bool shopFrozen;
    public int upgradeCost;
    public NetworkCardData[] hand;
    public NetworkCardData[] board;
    public NetworkCardData[] shopCards;
}
```

### NetworkCardData

Card serialization for network transfer:

```csharp
public class NetworkCardData
{
    public string cardName;
    public int attack;
    public int health;
    public bool isGolden;
    public bool hasAegis;
    // ... other combat-relevant fields
}
```

Cards are reconstructed on the client via `CardLookup` (maps card names to base Card objects from `CardDatabase`).

## WebGL Considerations

- Photon PUN 2 uses `WebSocketSecure` protocol for WebGL builds
- Set automatically in `PhotonConnector.Awake()`:
  ```csharp
  #if UNITY_WEBGL && !UNITY_EDITOR
  PhotonNetwork.PhotonServerSettings.AppSettings.Protocol =
      ExitGames.Client.Photon.ConnectionProtocol.WebSocketSecure;
  #endif
  ```
- Uses Photon Cloud (free tier: 20 CCU)

## Player Slot Assignment

`NetworkGameSetup` assigns player slots when players join:
- First player in room = slot 0 (host)
- Subsequent players fill remaining slots
- AI fills unoccupied slots

## Disconnect Handling

When a network player disconnects:
- Their slot is converted to AI control
- `GameManager.RegisterAIController()` adds an AI for that slot
- Game continues without interruption

## Key Multiplayer Fixes

| Fix ID | Issue | Solution |
|--------|-------|---------|
| M1 | Synergy singleton overwrite | Per-player `SynergySnapshot` pattern |
| M2 | Discovery not synced to clients | `BroadcastDiscoveryForPlayer` RPC |
| M3 | Shop cards duplicated | Reserve cards from pool when placed in shop |
| M5 | Upgrade cost desync | `SyncedUpgradeCost` field on Player |
| M6 | Ability leak across games | `AbilityManager.ClearAll()` on game start/end |
| M8 | Coins property bypass | All coin changes use property setter for events |

## Related Documentation

- [Architecture](architecture.md) — system overview
- [Game Manager](game-manager.md) — game loop and phases
- [Deployment Guide](deployment.md) — WebGL deployment with Photon
