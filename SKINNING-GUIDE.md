# How to Create a New Skin

This guide explains how to reskin the game with a new theme without modifying any code.

---

## Overview

The game engine is **theme-agnostic**. All theme-specific content is loaded from data files:

```
┌─────────────────────────────────────────────────────┐
│                   CORE ENGINE                        │
│  (Game logic, UI systems, AI - never changes)        │
└─────────────────────────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
    ┌───────────┐   ┌───────────┐   ┌───────────┐
    │   TAROT   │   │   ANIME   │   │  SPORTS   │
    │   THEME   │   │   THEME   │   │   THEME   │
    │           │   │           │   │           │
    │ • Config  │   │ • Config  │   │ • Config  │
    │ • Cards   │   │ • Cards   │   │ • Cards   │
    │ • Art     │   │ • Art     │   │ • Art     │
    └───────────┘   └───────────┘   └───────────┘
```

---

## Step 1: Create a Theme Configuration

Create a new **ThemeConfig** ScriptableObject:

1. In Unity, go to **Assets > Create > Game > Theme Config**
2. Name it `YourTheme.asset` (e.g., `AnimeTheme.asset`)
3. Configure the settings:

### Game Identity
| Field | Description | Example |
|-------|-------------|---------|
| `gameName` | Display name of your themed game | "Anime Battlers" |
| `themeId` | Short ID for menus/paths | "Anime" |

### Tribe Configuration
Configure all 4 tribes. Each tribe has a gameplay role:

| Index | Gameplay Role | Tarot Example | Anime Example | Sports Example |
|-------|---------------|---------------|---------------|----------------|
| 1 | **Economy** (gold/buy bonuses) | Pentacles | Merchants Guild | Sponsors |
| 2 | **Healing** (health/protection) | Cups | Healers | Medics |
| 3 | **Aggro** (damage/attack) | Swords | Warriors | Strikers |
| 4 | **Buffs** (stat increases) | Wands | Mages | Coaches |

For each tribe, set:
| Field | Description |
|-------|-------------|
| `tribeName` | Display name (e.g., "Warriors") |
| `description` | Flavor text |
| `themeColor` | UI accent color |
| `aliases` | Alternative names for CSV import |
| `tribeIcon` | Optional sprite |

### UI Text
Customize all UI strings:
- Shop/Hand/Board titles
- Button labels (Buy, Sell, Play, Reroll, etc.)
- Combat phase text
- Victory/Defeat messages

---

## Step 2: Create Card Assets

### Option A: Use the Card Database Generator

1. Create a CSV file with your cards at `Assets/Data/YourCards.csv`
2. Use the format:
```csv
Tribe Desscription,Name,Faction,Tier,Attack,Health,Ability,EffectType,EffectParameter
"Warrior description",Sword Knight,Warriors,1,2,1,Attacks first,NoEffect,
"Healer description",Field Medic,Healers,1,1,3,Heals allies,NoEffect,
```

3. Run **Tools > Game > Import Cards**

### Option B: Create Cards Manually

1. Go to **Assets > Create > Game > Card**
2. Configure each card:

| Field | Description |
|-------|-------------|
| `cardName` | Display name |
| `tier` | 1-6 (determines when it appears in shop) |
| `tribes` | Assign TribeType(s) from the enum |
| `attack` | Attack stat |
| `health` | Health stat |
| `cardImage` | Card artwork sprite |
| `abilityTrigger` | When ability activates |
| `abilityEffect` | What the ability does |
| `abilityValue` | Ability strength |

### Card Distribution Guidelines

For balanced gameplay, aim for:
- **30 total cards** (5 per tier)
- **7-8 cards per tribe** across all tiers
- **2-3 dual-tribe cards** for combo potential
- **1 triple-tribe card** at high tier

Example distribution:
```
Tier 1 (5 cards): 1 per tribe + 1 dual-tribe
Tier 2 (5 cards): 1 per tribe + 1 dual-tribe
Tier 3 (5 cards): 1 per tribe + 1 dual-tribe
Tier 4 (5 cards): 1 per tribe + 1 dual-tribe
Tier 5 (5 cards): 1 per tribe + 1 dual-tribe
Tier 6 (5 cards): 1 per tribe + 1 triple-tribe
```

---

## Step 3: Create Card Art

### Specifications
| Asset | Size | Format |
|-------|------|--------|
| Card artwork | 256x256 or 512x512 | PNG with transparency |
| Tribe icons | 64x64 | PNG with transparency |

### Import Settings
1. Import sprites to `Assets/Art/YourTheme/`
2. Set Texture Type to **Sprite (2D and UI)**
3. Enable **Alpha Is Transparency**

### Assigning Art to Cards
1. Select a Card ScriptableObject
2. Drag the sprite to the `cardImage` field

---

## Step 4: Apply Your Theme

### Method 1: Scene Setup (Recommended)
1. Add a **ThemeManager** component to a GameObject in your scene
2. Drag your ThemeConfig asset to the `Active Theme` field
3. The theme loads automatically on scene start

### Method 2: Runtime Loading
```csharp
// Load from Resources folder
ThemeConfig myTheme = Resources.Load<ThemeConfig>("Themes/AnimeTheme");
ThemeManager.Instance.SetTheme(myTheme);
```

---

## Step 5: Configure TavernManager

The TavernManager needs to know which cards to use:

1. Select the **TavernManager** in your scene
2. In the Inspector, assign all your Card ScriptableObjects to the `masterCards` list
3. The game will automatically:
   - Create the card pool based on tier copies
   - Make cards available in the shop at appropriate tiers

---

## Synergy System

Synergies are **automatically configured** based on tribe roles:

| Tribe Role | Tier 2 | Tier 4 | Tier 6 |
|------------|--------|--------|--------|
| Economy (1) | +1 gold on sell | +2 gold on sell | -1 cost to buy |
| Healing (2) | Heal adjacent +1 | Heal tribe +2 | All gain Aegis |
| Aggro (3) | +1 attack | +2 bonus damage | Cleave |
| Buffs (4) | Random +1/+1 | Tribe +1/+1 | All +2 attack |

### Cross-Tribe Combos
When you have 2+ of two different tribes:
- Economy + Healing = +1 gold/turn
- Healing + Buffs = Heals also buff attack
- Aggro + Economy = Kills grant gold
- Buffs + Aggro = Double attack buffs

---

## File Structure

Recommended organization:
```
Assets/
├── Data/
│   └── YourTheme.asset          # ThemeConfig
├── Cards/
│   └── YourTheme/               # Card ScriptableObjects
│       ├── Tier1_Warrior.asset
│       ├── Tier1_Healer.asset
│       └── ...
├── Art/
│   └── YourTheme/
│       ├── Cards/               # Card artwork
│       └── Icons/               # Tribe icons
└── Resources/
    └── DefaultTheme.asset       # Default theme (optional)
```

---

## Testing Your Theme

1. **Visual Check**: Play the game and verify:
   - Card names display correctly
   - Tribe names appear in tooltips
   - Theme colors are applied

2. **Gameplay Check**: Verify synergies work:
   - Get 2+ of a tribe → tier 2 synergy activates
   - Get 4+ of a tribe → tier 4 synergy activates
   - Get 2+ of two tribes → combo activates

3. **Balance Check**: Run AI vs AI games:
   - No tribe should dominate
   - Games should last 10-20 turns
   - All tiers should see play

---

## Checklist

Before releasing your theme:

- [ ] ThemeConfig created with all 4 tribes configured
- [ ] 30+ cards created across 6 tiers
- [ ] Each tribe has 7-8 cards
- [ ] All cards have artwork
- [ ] UI text customized
- [ ] Synergies tested at all thresholds
- [ ] 10+ full games played without errors
- [ ] AI performs reasonably

---

## Troubleshooting

### "Tribe not recognized"
- Check `aliases` in ThemeConfig includes the name used in CSV

### Synergies not activating
- Verify cards have `tribes` array set (not just legacy `tribe` string)
- Check SynergyManager is in the scene

### Wrong theme loading
- Ensure ThemeManager has your ThemeConfig assigned
- Check load order (ThemeManager.Awake runs first)

---

## Example: Creating an "Anime Battlers" Theme

1. **ThemeConfig**:
   - gameName: "Anime Battlers"
   - Tribe 1: "Merchants" (economy)
   - Tribe 2: "Shrine Maidens" (healing)
   - Tribe 3: "Samurai" (aggro)
   - Tribe 4: "Onmyoji" (buffs)

2. **Cards** (30 total):
   - Tier 1: Apprentice Merchant, Young Miko, Ronin, Apprentice Mage, Wanderer (dual)
   - ... (continue for all tiers)

3. **Art**: Anime-style character portraits

4. **Apply**: Set ThemeManager.ActiveTheme = AnimeTheme.asset

---

*For technical details, see the source code in `Assets/Scripts/Theme/`*
