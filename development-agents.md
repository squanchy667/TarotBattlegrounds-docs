# Development Agents

> Agent strategy and configuration for Tarot Battlegrounds. Agents are defined in `TarotBattlegrounds-POC/.claude/agents/`.

## Agent Inventory

| Agent | Model | Role |
|-------|-------|------|
| **tarot-orchestrator** | sonnet | Master orchestrator — coordinates all other agents, reads PLAN.md as source of truth |
| **unity-game-developer** | sonnet | Core implementation — C# scripting, physics, UI systems, optimization |
| **game-designer** | sonnet | Game design — mechanics, balancing, progression systems, difficulty curves |
| **tarot-game-auditor** | sonnet | QA auditor — rules integrity, combat balance, economy, synergies, regression checks |
| **game-auditor** | sonnet | QA auditor — audits economy, cards, combat, abilities, synergies, AI, UI against rules |
| **tarot-test-agent** | sonnet | Test & QA — generates NUnit tests, analyzes test suites, validates multiplayer scenarios |
| **tester** | sonnet | Verification — verifies bug fixes, checks for regressions, writes unit tests |
| **debugger** | sonnet | Diagnostics — root cause analysis for combat bugs, synergy issues, UI events, AI behavior |
| **fixer** | sonnet | Surgical fixes — minimal bug fixes from debugger diagnoses, preserves architecture |
| **log-analyzer** | — | Log parsing — analyzes Unity console logs from ParrelSync multiplayer testing |
| **deploy-orchestrator** | — | Deployment coordinator — orchestrates S3, CloudFront, and Photon Cloud setup |
| **aws-webgl-deployer** | — | AWS infrastructure — S3 + CloudFront with COOP/COEP headers for WebGL |
| **deploy-validator** | — | Deployment QA — validates CloudFront, MIME types, headers, Unity loader |
| **unity-webgl-builder** | — | Build prep — configures Photon WSS, WebGL template, prevents IL2CPP stripping |
| **ssl-dns-agent** | — | Post-deploy — custom domain and SSL certificate for CloudFront |

**15 agents total** — 8 with sonnet model routing, 7 with default model.

## Orchestration Pipeline

The tarot-orchestrator coordinates agents in a structured pipeline:

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

## Agent Categories

### Core Development
- **unity-game-developer** — primary implementation agent
- **game-designer** — mechanics and balance decisions

### Quality Assurance
- **tarot-game-auditor** — rules-based audit (10-agent consensus rounds)
- **game-auditor** — system-level audit
- **tarot-test-agent** — test generation and analysis
- **tester** — fix verification and regression testing

### Diagnostics
- **debugger** — root cause analysis
- **fixer** — surgical fix implementation
- **log-analyzer** — multiplayer log parsing

### Deployment
- **deploy-orchestrator** — master deployment coordinator
- **aws-webgl-deployer** — AWS infrastructure setup
- **deploy-validator** — post-deploy verification
- **unity-webgl-builder** — WebGL build configuration
- **ssl-dns-agent** — domain and SSL setup

## Batch Strategy

For Phase I (AWS Online Multiplayer), agents will execute in batches:

| Batch | Agents | Tasks |
|-------|--------|-------|
| 1 | unity-game-developer, aws-webgl-deployer | I1-I3: AWS setup |
| 2 | unity-game-developer | I4-I6: Auth + WebSocket |
| 3 | unity-game-developer, game-designer | I7-I9: Networking + matchmaking |
| 4 | unity-game-developer, tarot-test-agent | I10-I11: Validation + reconnection |
| 5 | tarot-game-auditor, tester | I12-I13: Leaderboard + playtest |

## Audit History

The project has completed **10 audit rounds** with consensus-based bug finding:
- Rounds 1-10 found and fixed 42+ bugs across combat, economy, synergies, AI, and UI
- 10-agent consensus rounds (Rounds 7+) for high-confidence bug detection
- All fixes validated through automated test suites (50+ NUnit tests)
