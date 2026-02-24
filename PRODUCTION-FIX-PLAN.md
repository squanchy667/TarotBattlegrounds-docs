# Production Fix Plan

> Audit date: February 23-24, 2026 | Branch: `tarot-skin` | Health Score: 6.5→~7.5/10
> Generated from 5-agent comprehensive audit + triple-agent verification (game-auditor, tarot-game-auditor, balance-auditor)

## Overview

31 issues identified across 3 priority tiers. 38 bugs fixed across 3 audit rounds + quality gate. 240 unit tests added. Triple-agent audit performed for final verification.

## Fix Lifecycle

```
IDENTIFIED → IN_PROGRESS → FIXED → VERIFIED → DONE
```

---

## CRITICAL BLOCKERS (Must Fix Before Any Build)

| ID | Issue | File(s) | Status |
|----|-------|---------|--------|
| C1 | Lobby scene GUID broken in Build Settings (zero GUID) | `ProjectSettings/EditorBuildSettings.asset` | IDENTIFIED |
| C2 | COEP header `require-corp` → must be `credentialless` | `AWS/scripts/deploy-webgl.sh:146`, `validate-deployment.sh:53` | **FIXED** |
| C3 | API Gateway CORS not configured in SAM template | `tarot-devzone/aws/template.yaml` | IDENTIFIED |
| C4 | `DataConfig.apiBaseUrl` is blank — auth/matchmaking silently disabled | `Assets/Data/DataConfig.asset` (Unity Inspector) | IDENTIFIED |
| C5 | Attack order always picks leftmost (no cycling) | `Assets/Scripts/CombatManager.cs:195` | **FIXED** |
| C6 | Synergy tiers don't stack (only highest fires) | `Assets/Scripts/Synergies/SynergyManager.cs:345-348` | **FIXED** |
| C7 | Double cleave bug (ability + synergy both fire) | `Assets/Scripts/CombatManager.cs:272,307` | **FIXED** |
| C8 | Aura target tribe not set (buffs all instead of tribe) | `Assets/Cards/Card.cs:229` | **FIXED** |

### Fix Details

**C1 — Lobby Scene GUID**
The Lobby.unity scene has GUID `00000000...` in EditorBuildSettings.asset. Must open Unity Editor → File → Build Settings → remove and re-add Lobby.unity. Cannot be fixed via code.

**C2 — COEP Header**
Change `"Value": "require-corp"` to `"Value": "credentialless"` in `deploy-webgl.sh` line 146. Update validation in `validate-deployment.sh` lines 53-56. The `require-corp` value blocks cross-origin S3 card images from loading.

**C3 — API Gateway CORS**
Add CORS configuration to the HttpApi in `template.yaml`. Without this, WebGL preflight OPTIONS requests fail, breaking all online features.

**C4 — DataConfig apiBaseUrl**
Set the API Gateway URL in the DataConfig ScriptableObject via Unity Inspector. Requires manual Editor action after API Gateway is deployed.

**C5 — Attack Order Cycling**
Replace `attackers.FirstOrDefault(c => c.health > 0)` at line 195 with an attack pointer index that advances after each attack, wrapping around. Each side needs its own pointer tracked across turns.

**C6 — Synergy Tier Stacking**
`SynergyManager.CalculateSynergies()` stores only one `activeTier` per tribe (the highest). Change to store ALL active tiers per tribe. `TriggerSynergies()` must iterate all tiers, not just one. `TribeSynergy.GetAllActiveTiers()` already exists (line 95) but is not used.

**C7 — Double Cleave**
Cards with both `OnAttackCleave` ability AND `hasCleave` synergy flag fire cleave twice. Fix: skip the `hasCleave` block (lines 307-324) if the card also has an `OnAttackCleave` ability, OR set `hasCleave = true` from the ability and remove the separate ability-system cleave path.

**C8 — Aura Target Tribe**
`Card.CreateAbility()` line 229 creates `new AuraAbility(AuraAbility.AuraEffect.BuffTribematesAttack, abilityValue)` without passing the card's tribe. Fix: pass `this.tribes[0]` as the third argument so the aura only buffs same-tribe minions.

---

## HIGH PRIORITY (Fix Before Multiplayer Launch)

| ID | Issue | File(s) | Status |
|----|-------|---------|--------|
| H1 | Token summon doesn't add to combat clone tracking | `CombatManager.cs:68-70` | **FIXED** |
| H2 | Tavern upgrade costs wrong (T4/5/6 all cost 11) | `Player.cs:128-132` | **FIXED** |
| H3 | Guardian targeting deterministic (leftmost, not random) | `CombatManager.cs:209-211` | **FIXED** |
| H4 | No host migration — host DC kills game for all | `NetworkGameBridge.cs:702` | **FIXED** |
| H5 | Hero powers not broadcast to clients | `NetworkPlayerState.cs` | **FIXED** |
| H6 | `hasCleave` not in `NetworkCardData` | `NetworkCardData.cs:14-18` | **FIXED** |
| H7 | Stars/Coins missing from ThemeConfig tribes (hardcoded 4) | `RuntimeDataLoader.cs:334` | **FIXED** |
| H8 | `RankedManager.SubmitMatchResult()` never called | `GameManager.cs` (end-of-game) | **FIXED** |
| H9 | Combo gold effects fire per-card instead of once | `SynergyManager.cs:557-560,780-783` | **FIXED** |
| H10 | Board card lookup by name not reference | `BoardManager.cs:23` | **FIXED** |
| H11 | Shop pool not scaled for 8 players | `EightPlayerManager.cs` + `TavernManager.cs` | **FIXED** |
| H12 | Human hero power selection UI missing | `GameManager.cs:487` | **FIXED** |

---

## MEDIUM PRIORITY

| ID | Issue | File(s) | Status |
|----|-------|---------|--------|
| M1 | `goldPerTurnBase` field name mismatch (reads 0 from config) | `RuntimeDataLoader.cs` | **FIXED** |
| M2 | Remote config fields silently ignored | `RuntimeDataLoader.cs` | **FIXED** |
| M3 | Matchmaking → Photon room handoff unvalidated | `MatchmakingManager.cs`, `LobbyUI.cs` | VERIFIED OK |
| M4 | AIBattleTests damage cap contradicts production code | `AIBattleTests.cs:241,649` | **FIXED** |
| M5 | Discovery hand-size guard missing | `Player.cs:AddDiscoveryCard()` | IDENTIFIED |
| M6 | DiscoveryUI dispatch bug (host vs client state) | `NetworkGameBridge.cs:308` | VERIFIED OK |
| M7 | Photon region hardcoded to `eu` | `PhotonServerSettings.asset` | **FIXED** |
| M8 | Mirror package compiled into WebGL (unused, bloats binary) | `ProjectSettings/` | IDENTIFIED |
| M9 | No global error handler / crash reporting | — | IDENTIFIED |
| M10 | Stale matchId not cleaned up in PlayerPrefs | `MatchmakingManager.cs` | **FIXED** |
| M11 | DevZone frontend runs over plain HTTP | `tarot-devzone/aws/template.yaml` | IDENTIFIED |

---

## TEST COVERAGE (240 Tests)

| Area | Tests | Status |
|------|-------|--------|
| Ability system (16 abilities) | 90 | **DONE** — AbilityTests.cs |
| Hero powers (12 powers) | 118 | **DONE** — HeroPowerTests.cs |
| Tribe synergies (6 tribes) | 65 | **DONE** — SynergyTests.cs |
| Combat system | 17 | **DONE** — CombatTests.cs |
| AI battles | 15+ | **DONE** — AIBattleTests.cs |
| Card system / Economy / Edge cases | ~35 | **DONE** — CardSystemTests, EconomyTests, EdgeCaseTests |
| Multiplayer RPCs | 10% | Future — NetworkTests.cs |
| Combat replay | 0% | Future — ReplayTests.cs |

---

## EXECUTION PLAN

### Sprint 1: Critical Code Fixes (C5-C8)
**Agent:** `debugger` + `fixer`
- C5: Add attack pointer cycling to CombatManager
- C6: Change SynergySnapshot to store List<SynergyTier> per tribe
- C7: Deduplicate cleave paths
- C8: Pass card tribe to AuraAbility constructor

### Sprint 2: Build & Deploy Fixes (C1-C2)
**Agent:** `unity-game-developer`
- C2: Fix COEP header in deploy scripts
- C1: Document Lobby scene GUID fix (requires Unity Editor)

### Sprint 3: High Priority Gameplay (H1-H3, H9-H10)
**Agent:** `fixer` + `combat-vfx-engineer`
- H1: Track summoned tokens in allClones
- H2: Fix tavern upgrade costs {4:9, 5:10, 6:11}
- H3: Random selection among guardians
- H9: Combo gold fires once per player
- H10: Use object reference for board card lookup

### Sprint 4: Network Fixes (H4-H6)
**Agent:** `network-engineer`
- H4: Basic host migration or graceful disconnect
- H5: Add heroPowerId to NetworkPlayerState
- H6: Add hasCleave/hasReborn to NetworkCardData

### Sprint 5: Data Pipeline (H7-H8, M1-M2)
**Agent:** `devzone-sync-engineer`
- H7: Extend ThemeConfig tribes to 6
- H8: Wire RankedManager.SubmitMatchResult in game-over flow
- M1: Fix goldPerTurnBase field name
- M2: Map all config fields to RuntimeGameConfig

### Sprint 6: Test Coverage
**Agent:** `tarot-test-agent`
- Fix asmdef file
- Write ability tests (P0)
- Write hero power tests (P0)
- Fix contradictory damage cap test (M4)
- Add Stars/Coins synergy tests

### Sprint 7: UI & Polish (H11-H12, M3-M11)
**Agent:** `ui-engineer` + `game-designer`
- H11: Wire shop pool multiplier to TavernManager
- H12: Hero power selection UI for human players
- Remaining medium priority items

---

## AgentTailor Integration

Reference: [github.com/squanchy667/agenttailor](https://github.com/squanchy667/agenttailor)

AgentTailor can be used to:
1. **Context Assembly** — Feed this fix plan + relevant code files into Pipeline A to generate optimal context for each sprint's agents
2. **Agent Factory** — Use Pipeline B to generate specialized agent definitions for each sprint, ensuring agents have the right domain knowledge baked in
3. **Quality Scoring** — Use the quality assessment engine to verify that fix implementations meet coverage, relevance, and correctness criteria

### Setup
```bash
# Clone AgentTailor
git clone https://github.com/squanchy667/agenttailor.git

# Feed project docs into Pipeline A
# Upload: PRODUCTION-FIX-PLAN.md, architecture/system-overview.md, developer/*.md
# AgentTailor will build a vector index for semantic search

# Generate sprint-specific agents via Pipeline B
# Input: sprint description + relevant file paths
# Output: production-ready agent definitions (Claude format)
```

---

## Progress Tracking

Updated as fixes are applied. Check git log on `tarot-skin` branch for fix commits.

| Sprint | Scope | Status |
|--------|-------|--------|
| 1 | Critical code fixes (C5-C8) | **DONE** |
| 2 | Build & deploy (C1-C2) | **C2 DONE** (C1 requires Unity Editor) |
| 3 | High gameplay (H1-H3, H9-H10) | **DONE** |
| 4 | Network (H4-H6) | **DONE** |
| 5 | Data pipeline (H7-H8, M1-M2) | **DONE** |
| 6 | Test coverage (240 tests) | **DONE** — 90 ability + 118 hero + 65 synergy + combat tests |
| 7 | Bug fixes round 2+3 (13 fixes) | **DONE** — 7 fixes (round 2) + 6 fixes (round 3) |
| 8 | Triple-agent audit | **DONE** — 8 high-confidence + 14 single-agent findings logged |
