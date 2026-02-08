# Runtime Data Loading

## Overview

The runtime data system allows the game to fetch card definitions, synergy configs, and balance parameters from remote JSON files (hosted on S3) instead of using the built-in `CardDatabase`. This enables live balance updates without rebuilding the game.

## Architecture

```
[DevZone Web Editor] -> [Express API on Lambda] -> [S3: game-data JSON]
                                                          |
[Unity WebGL Game]  <---- fetches cards.json, synergies.json, config.json at startup
```

## Components

### DataConfig (ScriptableObject)

Configuration for runtime data loading:

```csharp
[CreateAssetMenu(menuName = "Game/Data Config")]
public class DataConfig : ScriptableObject
{
    public string dataBaseUrl;              // e.g., "https://data-cdn.cloudfront.net/live/"
    public bool enableRuntimeLoading;       // Toggle on/off
    public float requestTimeout = 10f;      // HTTP timeout
    public bool fallbackToBuiltIn = true;   // Use built-in data on failure
}
```

Create in Unity: `Assets > Create > Game > Data Config`
Assign to `RuntimeDataLoader.dataConfig` in the scene.

**Pre-built asset:** `Assets/Data/DataConfig.asset` ships with:
- `dataBaseUrl`: `https://tarot-battlegrounds-data-prod.s3.amazonaws.com/live/`
- `enableRuntimeLoading`: true
- `requestTimeout`: 10s
- `fallbackToBuiltIn`: true

### RuntimeDataLoader (MonoBehaviour Singleton)

Fetches and parses JSON on startup:

1. Checks if `DataConfig` exists and `enableRuntimeLoading` is true
2. Fetches `cards.json`, `synergies.json`, `config.json` **in parallel** (3 coroutines)
3. Parses JSON using Newtonsoft.Json with `StringEnumConverter`
4. Stores parsed data in properties: `Cards`, `Synergies`, `Config`
5. Fires `OnLoadComplete` event when done (success or failure)

```csharp
// Properties
RuntimeDataLoader.Instance.IsLoaded    // true if all data loaded
RuntimeDataLoader.Instance.IsComplete  // true when loading finished
RuntimeDataLoader.Instance.Cards       // List<RuntimeCardData>
RuntimeDataLoader.Instance.Synergies   // List<RuntimeSynergyData>
RuntimeDataLoader.Instance.Config      // RuntimeGameConfig

// Conversion methods
RuntimeDataLoader.Instance.BuildCards()      // -> List<Card>
RuntimeDataLoader.Instance.BuildSynergies()  // -> TribeSynergy[]
```

### CardPoolInitializer Integration

`CardPoolInitializer` coordinates the loading sequence:

```
1. Start() -> Check if RuntimeDataLoader exists
2. If loading, subscribe to OnLoadComplete
3. When complete, call Initialize()
4. Initialize() calls CardDatabase.GenerateAllCards()
   -> CardDatabase checks RuntimeDataLoader first
   -> Falls back to built-in 35 cards if not loaded
5. Apply runtime config to TavernManager (tierCopies, shopSizes)
6. Initialize synergies from runtime data or SynergyTestData fallback
```

## JSON Data Format

### cards.json

```json
[
  {
    "id": "flame-dancer",
    "cardName": "Flame Dancer",
    "tier": 2,
    "attack": 3,
    "health": 2,
    "tribes": ["Wands"],
    "ability": "OnAttack: Deal 1 extra damage",
    "abilityTrigger": "OnAttack",
    "abilityEffect": "OnAttackBonusDamage",
    "abilityValue": 1,
    "effectType": "",
    "buyCostModifier": 0,
    "sellValueModifier": 0,
    "imageUrl": ""
  }
]
```

### synergies.json

```json
[
  {
    "tribeType": "Swords",
    "tiers": [
      {
        "threshold": 2,
        "trigger": "StartOfCombat",
        "effect": "BuffAttack",
        "target": "AllTribeMembers",
        "value": 1,
        "description": "+1 Attack to all Swords"
      }
    ],
    "combo": {
      "tribe": "Wands",
      "threshold": 2,
      "effect": "BuffAttack",
      "value": 1,
      "description": "Swords + Wands: +1 Attack to all"
    }
  }
]
```

### config.json

```json
{
  "tierCopies": {"1": 16, "2": 15, "3": 13, "4": 11, "5": 9, "6": 7},
  "shopSizes": {"1": 3, "2": 4, "3": 4, "4": 5, "5": 5, "6": 6},
  "baseBuyCost": 3,
  "goldenMultiplier": 2,
  "recruitTimerSeconds": 35,
  "startingGold": 3,
  "goldPerTurn": 1,
  "maxGold": 10,
  "tavernUpgradeCosts": {"2": 5, "3": 8, "4": 11, "5": 11, "6": 11},
  "startingHealth": 40
}
```

## Runtime Config Application

When runtime data is loaded, various systems apply overrides:

| System | What's Overridden | Method |
|--------|-------------------|--------|
| `TavernManager` | tierCopies, shopSizes | `ApplyRuntimeConfig(config)` |
| `GameManager` | recruitTimer, startingHealth | `ApplyRuntimeConfig()` |
| `Player` | baseUpgradeCosts | `ApplyRuntimeConfig(config)` |

## Fallback Behavior

If runtime loading fails (network error, parse error, disabled):

1. `RuntimeDataLoader.IsLoaded` is `false`
2. `CardDatabase.GenerateAllCards()` returns built-in 35 cards
3. `SynergyTestData` provides fallback synergy definitions
4. All managers use their hardcoded default values
5. Game works completely offline

## RuntimeCardImageLoader

Optional component for loading card art from URLs:

- `GetCardSprite(url, callback)` — returns placeholder immediately, invokes callback with loaded sprite
- `Preload(urls)` — batch prefetch card images
- Sprite caching to avoid re-downloads
- Concurrent download queue (max 4 simultaneous)

## Scene Setup

To wire RuntimeDataLoader into the Game scene:

1. Open the Game scene in Unity Editor
2. Run `Tools > Game > Setup Runtime Data Loader`
3. Save the scene

The editor script (`Assets/Editor/RuntimeDataSetup.cs`) will:
- Create a `RuntimeDataLoader` GameObject in the scene
- Assign `Assets/Data/DataConfig.asset` to it
- Set Script Execution Order: RuntimeDataLoader (-100) before CardPoolInitializer (0)

## S3 Public Access

The `live/` prefix on `tarot-battlegrounds-data-prod` has a public-read bucket policy so the Unity WebGL client can fetch JSON directly. CORS allows requests from the CloudFront game origin (`https://dui22oafwco41.cloudfront.net`).

## Related Documentation

- [Card System](card-system.md) — card definitions
- [DevZone Editor](devzone.md) — web-based data editor
- [Deployment Guide](deployment.md) — S3 hosting for game data
