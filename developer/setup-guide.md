# Setup Guide

## Prerequisites

- **Unity 2022.3.48f1** (LTS) — exact version required
- **Git** for version control
- **Node.js 18+** (for DevZone editor, optional)

## Clone & Open

```bash
git clone <repo-url> TarotBattlegrounds-POC
```

Open Unity Hub, click "Open" and navigate to:
```
TarotBattlegrounds-POC/TarotBattlegrounds-POC/
```

> The Unity project is **nested** — the inner `TarotBattlegrounds-POC/` folder contains `Assets/`, `ProjectSettings/`, etc.

## Project Structure

```
TarotBattlegrounds-POC/           # Git root
├── TarotBattlegrounds-POC/       # Unity project root
│   ├── Assets/
│   │   ├── Editor/               # Editor-only scripts
│   │   ├── Photon/               # Photon PUN 2 SDK
│   │   ├── Scripts/              # Game logic
│   │   ├── Scenes/               # Game.unity, MainMenu.unity
│   │   ├── WebGLTemplates/       # Custom WebGL template
│   │   └── link.xml              # IL2CPP stripping protection
│   ├── Packages/
│   └── ProjectSettings/
├── AWS/                          # Deployment scripts
│   ├── scripts/
│   │   ├── redeploy.sh           # Quick redeployment
│   │   └── deploy-webgl.sh       # First-time deployment
│   └── PLAN.md
└── .claude/                      # Claude Code config
```

## Scene Setup

### Main Menu Scene (`MainMenu.unity`)

Two-panel navigation:
- **MainPanel**: Solo / Multiplayer / Quit buttons
- **SoloPanel**: Player count (4/6/8) + AI difficulty (Easy/Medium/Hard)

To regenerate the Main Menu UI programmatically:
```
Unity Editor > Tools > Game > Setup Main Menu UI
```

### Game Scene (`Game.unity`)

Required scene objects:
- `TavernManager` — card pool management
- `CardPoolInitializer` — initializes cards from CardDatabase
- `SynergyManager` — tribe synergy calculations
- `GameManager` — game loop (assign Player references + playerPrefab)
- `GameUIManager` — UI coordinator
- `Canvas` — all UI elements (ShopUI, HandUI, BoardUI, etc.)

To create the Player prefab programmatically:
```
Unity Editor > Tools > Game > Setup Player Prefab
```

## Photon PUN 2 Configuration

1. Open `Window > Photon Unity Networking > PUN Wizard`
2. Enter your Photon App ID (from the Photon Dashboard)
3. The App ID is stored in `PhotonServerSettings.asset`

> For WebGL builds, `PhotonConnector.cs` automatically switches to `WebSocketSecure` protocol.

## Key Configuration

### GameConfig (Static)

`GameConfig.cs` holds static game settings configured from the Main Menu:

| Setting | Default | Description |
|---------|---------|-------------|
| `PlayerCount` | 2 | Number of players (2-8) |
| `HumanPlayerIndex` | 0 | Which slot is human |
| `DefaultAIDifficulty` | Medium | AI difficulty for bots |
| `CurrentGameMode` | Solo | Solo or Multiplayer |

### DataConfig (ScriptableObject)

`Assets/Scripts/Data/DataConfig.cs` — optional runtime data loading config:

| Field | Default | Description |
|-------|---------|-------------|
| `dataBaseUrl` | (empty) | S3 URL for game data JSON |
| `enableRuntimeLoading` | false | Whether to fetch data at startup |
| `requestTimeout` | 10 | HTTP timeout in seconds |
| `fallbackToBuiltIn` | true | Use built-in data on fetch failure |

Create via: `Assets > Create > Game > Data Config`

## Running the Game

### In Editor (Solo Mode)

1. Open `Scenes/MainMenu.unity`
2. Press Play
3. Select "Solo", choose player count and difficulty, then Start

### In Editor (Quick Test)

1. Open `Scenes/Game.unity` directly
2. Press Play (defaults to 2-player solo with GameConfig defaults)

### WebGL Build

```bash
# Close Unity Editor first (lockfile conflict)
/Applications/Unity/Hub/Editor/2022.3.48f1/Unity.app/Contents/MacOS/Unity \
  -batchmode -nographics -quit \
  -projectPath TarotBattlegrounds-POC/TarotBattlegrounds-POC \
  -executeMethod BuildScript.BuildWebGLCLI
```

Build output: `TarotBattlegrounds-POC/WebGLBuild/WebGLBuildNN/` (auto-incremented)

### Deploy to AWS

```bash
cd AWS/scripts
./redeploy.sh  # Auto-detects latest build directory
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "TavernManager not found" | Ensure `TavernManager` object exists in Game scene |
| "playerPrefab is null" | Assign Player prefab in GameManager inspector |
| Photon won't connect | Check App ID in PhotonServerSettings |
| WebGL build fails | Close Unity Editor before CLI build |
| Cards don't appear | Check CardPoolInitializer is in scene with `useGeneratedDatabase = true` |
