# Phase III: Card Pool to 100+

> Expand the card pool to 100+ cards with 2 new tribes (Stars, Coins), cross-tribe synergies, and a full AI balance pass.

## Overview

Phase III scales the card pool from the current set to over 100 cards by introducing 2 new tribes (Stars and Coins), expanding existing tribes, adding synergy tiers and cross-tribe combos, and running a comprehensive AI balance pass. All new content syncs to DevZone.

**Prerequisites:** Phase II complete (new abilities and effects needed for card designs).

**Key Deliverables:**
- Stars and Coins tribes with 10 cards each
- 45 expansion cards across existing tribes
- Synergy tiers (2/4/6) for all 6 tribes
- Cross-tribe combos (Stars+Swords, Coins+Pentacles)
- Rebalanced meta for 6-tribe ecosystem
- AI evaluation update + 100-game balance validation

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| III-B1 | T201-T202 (New tribes) | card-content-designer, devzone-sync-engineer |
| III-B2 | T203-T208 (65 new cards) | card-content-designer, unity-game-developer |
| III-B3 | T209-T214 (Synergy rebalance) | balance-auditor, card-content-designer |
| III-B4 | T215-T216 (AI update + balance pass) | balance-auditor, game-designer |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T201 | Add Stars tribe (Unity + DevZone) | HIGH | PENDING | — | T203, T209 | card-content-designer |
| T202 | Add Coins tribe (Unity + DevZone) | HIGH | PENDING | — | T204, T210 | card-content-designer |
| T203 | Design 10 Stars cards (tier 1-3) | HIGH | PENDING | T201 | T209, T214 | card-content-designer |
| T204 | Design 10 Coins cards (tier 1-3) | HIGH | PENDING | T202 | T210, T214 | card-content-designer |
| T205 | Design 15 Swords expansion | HIGH | PENDING | — | T214 | card-content-designer |
| T206 | Design 15 Cups expansion | HIGH | PENDING | — | T214 | card-content-designer |
| T207 | Design 10 Pentacles expansion | MEDIUM | PENDING | — | T214 | card-content-designer |
| T208 | Design 5 Wands expansion | MEDIUM | PENDING | — | T214 | card-content-designer |
| T209 | Stars synergy tiers (2/4/6) | HIGH | PENDING | T203 | T211, T213 | balance-auditor |
| T210 | Coins synergy tiers (2/4/6) | HIGH | PENDING | T204 | T212, T213 | balance-auditor |
| T211 | Cross-tribe combo: Stars + Swords | MEDIUM | PENDING | T209 | — | card-content-designer |
| T212 | Cross-tribe combo: Coins + Pentacles | MEDIUM | PENDING | T210 | — | card-content-designer |
| T213 | Rebalance existing synergies for 6-tribe meta | HIGH | PENDING | T209, T210 | T215 | balance-auditor |
| T214 | Update tier distribution curve | MEDIUM | PENDING | T203-T208 | — | balance-auditor |
| T215 | Update AI card evaluation | HIGH | PENDING | T213 | T216 | balance-auditor |
| T216 | 100-game AI balance pass | HIGH | PENDING | T215 | — | game-designer |

## Acceptance Criteria

- [ ] 100+ cards total in card pool
- [ ] 6 tribes all viable (none >45% win rate)
- [ ] All synergy tiers (2/4/6) achievable for all tribes
- [ ] Cross-tribe combos (Stars+Swords, Coins+Pentacles) working
- [ ] DevZone shows new tribes and card data
- [ ] AI plays competently with new cards

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase III details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
