# Development Agents

> Agent strategy and configuration for Tarot Battlegrounds. Agents are defined in `TarotBattlegrounds-POC/.claude/agents/`. The project uses **26 agents** across 10 categories to execute a 7-phase upgrade plan.

## Agent Inventory

| # | Agent | Model | Role |
|---|-------|-------|------|
| 1 | **tarot-orchestrator** | sonnet | Master orchestrator — coordinates all agents, reads PLAN.md as source of truth |
| 2 | **upgrade-orchestrator** | sonnet | Phase pipeline orchestrator — manages 7-phase upgrade execution, batch scheduling, quality gates |
| 3 | **unity-game-developer** | sonnet | Core implementation — C# scripting, physics, UI systems, optimization |
| 4 | **game-designer** | sonnet | Game design — mechanics, balancing, progression systems, difficulty curves |
| 5 | **card-content-designer** | opus | Card content authoring — designs cards with stats, abilities, tribe assignments, lore text via DevZone |
| 6 | **ability-engineer** | sonnet | Ability system — new triggers (OnAllyDeath, OnAllySummoned, OnSell, Aura), new effects (Reborn, Windfury, Venomous, etc.) |
| 7 | **hero-power-engineer** | sonnet | Hero power system — HeroPower base class, HeroPowerManager, 12 hero powers, recruit phase integration |
| 8 | **combat-vfx-engineer** | sonnet | Combat animation & VFX — CombatReplay, CombatAnimator, VFXManager, particle pooling |
| 9 | **sfx-engineer** | haiku | Sound effects — SFXManager, SFXConfig ScriptableObject, MusicManager, AudioSource pooling |
| 10 | **ui-engineer** | sonnet | UI overhaul — card frames, drag-drop, hover zoom, tooltips, collection viewer, settings menu |
| 11 | **network-engineer** | sonnet | Network infrastructure — Cognito auth, matchmaking, Photon lobbies, reconnection, delta compression |
| 12 | **ranked-backend-engineer** | sonnet | Ranked system backend — DynamoDB tables, Lambda endpoints, MMR calculation, season management |
| 13 | **devzone-sync-engineer** | sonnet | Dual-repo sync — keeps Unity and DevZone enums, tribes, abilities, and hero powers in sync |
| 14 | **tarot-game-auditor** | sonnet | QA auditor — rules integrity, combat balance, economy, synergies, regression checks |
| 15 | **game-auditor** | sonnet | QA auditor — audits economy, cards, combat, abilities, synergies, AI, UI against rules |
| 16 | **tarot-test-agent** | sonnet | Test & QA — generates NUnit tests, analyzes test suites, validates multiplayer scenarios |
| 17 | **tester** | sonnet | Verification — verifies bug fixes, checks for regressions, writes unit tests |
| 18 | **debugger** | sonnet | Diagnostics — root cause analysis for combat bugs, synergy issues, UI events, AI behavior |
| 19 | **fixer** | sonnet | Surgical fixes — minimal bug fixes from debugger diagnoses, preserves architecture |
| 20 | **balance-auditor** | opus | Balance analysis — no tribe >45% win rate, synergy power curves, meta health scoring |
| 21 | **log-analyzer** | default | Log parsing — analyzes Unity console logs from ParrelSync multiplayer testing |
| 22 | **deploy-orchestrator** | default | Deployment coordinator — orchestrates S3, CloudFront, and Photon Cloud setup |
| 23 | **aws-webgl-deployer** | default | AWS infrastructure — S3 + CloudFront with COOP/COEP headers for WebGL |
| 24 | **deploy-validator** | default | Deployment QA — validates CloudFront, MIME types, headers, Unity loader |
| 25 | **unity-webgl-builder** | default | Build prep — configures Photon WSS, WebGL template, prevents IL2CPP stripping |
| 26 | **ssl-dns-agent** | default | Post-deploy — custom domain and SSL certificate for CloudFront |

**26 agents total** — 2 opus, 1 haiku, 16 sonnet, 7 default.

## Agent Categories

### Orchestration (2)
- **tarot-orchestrator** — master coordinator for audit/test/fix pipeline, reads PLAN.md as source of truth
- **upgrade-orchestrator** — manages 7-phase upgrade execution, schedules batches, enforces quality gates between phases

### Implementation (1)
- **unity-game-developer** — primary C# implementation agent for all Unity systems

### Content Design (2)
- **game-designer** — mechanics, balancing, progression, difficulty curves
- **card-content-designer** (opus) — designs card stats, abilities, tribe assignments, and lore text; authors content via DevZone CRUD

### Systems Engineering (5)
- **ability-engineer** — new ability triggers (OnAllyDeath, OnAllySummoned, OnSell, Aura) and effects (Reborn, Windfury, Venomous, SummonToken, StealBuff, GainArmor, BuffAllTribes, RandomTransform)
- **hero-power-engineer** — HeroPower base class, HeroPowerManager singleton, 12 hero powers, recruit phase integration, hero power UI
- **combat-vfx-engineer** — CombatReplay data structure, CombatAnimator coroutine playback, VFXManager with particle pooling, attack/death/ability VFX
- **sfx-engineer** (haiku) — SFXManager with AudioSource pooling, SFXConfig ScriptableObject, MusicManager with crossfade, combat SFX integration
- **ui-engineer** — card art pipeline, card frames, hover zoom, drag-drop system, collection viewer, settings menu, game over redesign

### Infrastructure (2)
- **network-engineer** — Cognito auth, DynamoDB profiles, Lambda endpoints, matchmaking queue, Photon lobbies, reconnection handling, DeltaStateCompressor
- **ranked-backend-engineer** — ranked DynamoDB tables, Lambda endpoints for MMR/leaderboard/season, Elo calculation, tier system (Bronze-Legend)

### Integration (1)
- **devzone-sync-engineer** — keeps Unity and DevZone enums in sync (TribeType, AbilityTrigger, HeroPowerType), publishes new cards to S3 via RuntimeDataLoader pipeline

### Quality Assurance (6)
- **tarot-game-auditor** — rules-based audit with 10-agent consensus rounds
- **game-auditor** — system-level audit across economy, cards, combat, abilities, synergies, AI, UI
- **tarot-test-agent** — NUnit test generation, test suite analysis, multiplayer scenario validation
- **tester** — fix verification, regression testing
- **debugger** — root cause analysis for combat, synergy, UI, and AI bugs
- **fixer** — surgical fix implementation from debugger diagnoses

### Balance (1)
- **balance-auditor** (opus) — validates no tribe >45% win rate, analyzes synergy power curves, scores meta health, recommends stat adjustments

### Diagnostics (1)
- **log-analyzer** — parses Unity console logs from ParrelSync and Photon multiplayer testing

### Deployment (5)
- **deploy-orchestrator** — master deployment coordinator
- **aws-webgl-deployer** — S3 + CloudFront infrastructure with COOP/COEP headers
- **deploy-validator** — post-deploy verification (CloudFront, MIME types, headers, Unity loader)
- **unity-webgl-builder** — WebGL build configuration (Photon WSS, template, IL2CPP)
- **ssl-dns-agent** — custom domain and SSL certificate setup

## Orchestration Pipeline

The **upgrade-orchestrator** manages the 7-phase upgrade using a structured pipeline:

```
For each Phase (I → VII):
  1. PLAN       → Read TASK_BOARD.md, identify batch groups
  2. BATCH      → Assign agents to task batches (3-5 agents per batch)
  3. EXECUTE    → Run batch (agents implement tasks in parallel)
  4. AUDIT      → tarot-game-auditor + game-auditor verify rules compliance
  5. TEST       → tarot-test-agent generates/runs NUnit tests
  6. BALANCE    → balance-auditor validates meta health (Phases II, III, VII)
  7. SYNC       → devzone-sync-engineer syncs enums/data to DevZone
  8. GATE       → Quality gate: >=7.0/10 audit, 0 regressions, no tribe >45%
  9. NEXT       → Advance to next phase or fix failures
```

The **tarot-orchestrator** continues to handle the audit/test/fix cycle within each batch:

```
1. AUDIT     → tarot-game-auditor / game-auditor
                 Compares implementation against game rules
                 Produces bug list with severity

2. TEST      → tarot-test-agent / tester
                 Generates NUnit tests for found issues
                 Validates existing test coverage

3. REPORT    → debugger / log-analyzer
                 Root cause analysis on failures
                 Parses Unity logs for desyncs

4. IMPLEMENT → unity-game-developer / fixer
                 Implements fixes from diagnoses
                 Minimal, surgical changes only
```

## Batch Execution Strategy

### Phase I: Online Infrastructure (T001-T010)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | network-engineer | T001-T002: Cognito + DynamoDB setup |
| 2 | network-engineer | T003-T004: Lambda auth + Unity integration |
| 3 | network-engineer, ui-engineer | T005-T007: Auth UI, matchmaking queue + UI |
| 4 | network-engineer | T008-T009: Photon lobby + reconnection |
| 5 | tarot-test-agent, tester | T010: Integration testing + playtest |

### Phase II: New Abilities & Hero Powers (T101-T118)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | ability-engineer | T101-T104: New triggers (OnAllyDeath, OnAllySummoned, OnSell, Aura) |
| 2 | ability-engineer | T105-T108: Effects batch 1 (Reborn, Windfury, Venomous, SummonToken) |
| 3 | ability-engineer | T109-T112: Effects batch 2 (StealBuff, GainArmor, BuffAllTribes, RandomTransform) |
| 4 | hero-power-engineer | T113-T116: Hero power system + UI |
| 5 | unity-game-developer, devzone-sync-engineer | T117-T118: CombatManager integration + DevZone sync |

### Phase III: Card Pool to 100+ (T201-T216)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | unity-game-developer, devzone-sync-engineer | T201-T202: New tribe enums (Stars, Coins) |
| 2 | card-content-designer | T203-T204: Stars + Coins cards (20 cards) |
| 3 | card-content-designer | T205-T208: Expansion cards (45 cards) |
| 4 | game-designer | T209-T213: Synergy tiers, cross-tribe combos, rebalance |
| 5 | game-designer, balance-auditor | T214-T216: Tier distribution, AI evaluation, 100-game balance pass |

### Phase IV: Combat Animation & VFX (T301-T320)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | combat-vfx-engineer | T301-T303: CombatReplay data + recording + serialization |
| 2 | combat-vfx-engineer | T304-T307: CombatAnimator + card visual animations |
| 3 | combat-vfx-engineer | T308-T311: VFXManager + attack/ability/death VFX |
| 4 | sfx-engineer | T312-T315: SFXManager + SFXConfig + combat SFX + MusicManager |
| 5 | ui-engineer, network-engineer | T316-T318: UI integration, skip/speed controls, network replay |
| 6 | combat-vfx-engineer, tester | T319-T320: Ability VFX in replay + performance profiling |

### Phase V: UI Overhaul (T401-T418)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | ui-engineer | T401-T404: Card art pipeline, frames, tribe backgrounds, golden overlay |
| 2 | ui-engineer | T405-T406: Hover zoom + tooltip redesign |
| 3 | ui-engineer | T407-T410: DragDropManager + drag from shop/hand/board reorder |
| 4 | ui-engineer | T411-T413: Timer countdown, shop refresh animation, tier-up celebration |
| 5 | ui-engineer | T414-T418: Opponent viewer, game over, main menu, collection, settings |

### Phase VI: Ranked System (T501-T515)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | ranked-backend-engineer | T501-T502: DynamoDB tables + Lambda endpoints |
| 2 | ranked-backend-engineer | T503-T505: MMR calculation, rank tiers, season management |
| 3 | ranked-backend-engineer, network-engineer | T506: Matchmaking with MMR range |
| 4 | ui-engineer | T507-T509: Rank display, season rewards, queue UI |
| 5 | ui-engineer, ranked-backend-engineer | T510-T514: Post-game stats, match history, leaderboard, profile manager |
| 6 | tarot-test-agent, tester | T515: Ranked integration testing |

### Phase VII: 8-Player Scale & Polish (T601-T620)

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | network-engineer | T601-T605: 8-player Photon room, ghost opponent, round-robin, elimination, health scaling |
| 2 | ui-engineer | T606-T610: Lobby UI, opponent viewer, mini-map, transitions, spectator mode |
| 3 | network-engineer | T611-T613: DeltaStateCompressor, message batching, bandwidth profiling |
| 4 | game-designer, balance-auditor | T614-T616: 8-player AI balance, shop pool scaling, economy rebalance |
| 5 | combat-vfx-engineer, unity-game-developer | T617-T619: Performance profiling, memory optimization, WebGL build size |
| 6 | tarot-test-agent, tester | T620: Final integration test + 8-player playtest |

## Model Routing

| Tier | Agents | Count | Rationale |
|------|--------|-------|-----------|
| **opus** | card-content-designer, balance-auditor | 2 | Creative card design and nuanced balance analysis require highest reasoning |
| **haiku** | sfx-engineer | 1 | Audio clip mapping and config is structured, low-reasoning work |
| **sonnet** | All others with explicit model | 16 | Standard implementation, testing, and coordination |
| **default** | log-analyzer, deploy-orchestrator, aws-webgl-deployer, deploy-validator, unity-webgl-builder, ssl-dns-agent | 6 | Infrastructure and deployment tasks |

Override model routing by editing the `model:` field in agent YAML frontmatter.

## Quality Gates

Quality gates run between every phase. All must pass before advancing:

1. **tarot-game-auditor** — Rules compliance audit, score >= 7.0/10
2. **tarot-test-agent** — NUnit test suite, 0 regressions allowed
3. **balance-auditor** — No tribe win rate > 45%, meta health check (active in Phases II, III, VI, VII)
4. **DevZone publish verification** — All new cards/abilities/hero powers published and loadable via RuntimeDataLoader

If any gate fails, the upgrade-orchestrator routes failures to debugger + fixer for resolution before re-running the gate.

## Audit History

The project has completed **10 audit rounds** with consensus-based bug finding:
- Rounds 1-10 found and fixed 42+ bugs across combat, economy, synergies, AI, and UI
- 10-agent consensus rounds (Rounds 7+) for high-confidence bug detection
- All fixes validated through automated test suites (50+ NUnit tests)
