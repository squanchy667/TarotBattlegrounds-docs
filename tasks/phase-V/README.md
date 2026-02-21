# Phase V: UI Overhaul

> Replace click-based interactions with drag-and-drop, add card art pipeline, rarity frames, and redesign all major screens.

## Overview

Phase V overhauls the entire user interface. Cards get art loaded from S3 via DevZone, rarity-colored frames with tribe backgrounds, and golden overlays for triples. The core interaction model shifts from click-based to drag-and-drop (shop to hand, hand to board, board reorder). All major screens are redesigned: main menu, game over, opponent viewer, collection, and settings.

**Prerequisites:** Phase IV complete (animations and VFX systems needed for UI polish).

**Key Deliverables:**
- Card art pipeline (S3 + DevZone upload)
- Card frames with rarity borders and tribe colors
- Hover zoom preview and tooltip redesign
- DragDropManager with shop/hand/board interactions
- Recruit phase polish (timer, shop refresh, tier-up effects)
- Redesigned main menu, game over, opponent viewer, collection, and settings screens

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| V-B1 | T401-T406 (Card art + frames) | ui-engineer |
| V-B2 | T407-T410 (Drag-and-drop) | ui-engineer |
| V-B3 | T411-T413 (Recruit polish) | ui-engineer |
| V-B4 | T414-T418 (Screens) | ui-engineer |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T401 | Card art pipeline (S3 + DevZone upload) | HIGH | PENDING | — | — | ui-engineer |
| T402 | Card frame system (rarity borders) | HIGH | PENDING | — | T403, T404 | ui-engineer |
| T403 | Tribe-colored backgrounds | MEDIUM | PENDING | T402 | — | ui-engineer |
| T404 | Golden card overlay for triples | MEDIUM | PENDING | T402 | — | ui-engineer |
| T405 | Hover zoom preview | HIGH | PENDING | — | T406 | ui-engineer |
| T406 | Card tooltip redesign | MEDIUM | PENDING | T405 | — | ui-engineer |
| T407 | DragDropManager (core system) | HIGH | PENDING | — | T408, T409, T410 | ui-engineer |
| T408 | Drag shop to hand | HIGH | PENDING | T407 | — | ui-engineer |
| T409 | Drag hand to board | HIGH | PENDING | T407 | — | ui-engineer |
| T410 | Board reorder via drag | HIGH | PENDING | T407 | — | ui-engineer |
| T411 | Recruit timer animated countdown | MEDIUM | PENDING | — | — | ui-engineer |
| T412 | Shop refresh animation | LOW | PENDING | — | — | ui-engineer |
| T413 | Tier-up celebration effect | LOW | PENDING | — | — | ui-engineer |
| T414 | Opponent board viewer | HIGH | PENDING | — | — | ui-engineer |
| T415 | Game over screen redesign | HIGH | PENDING | — | — | ui-engineer |
| T416 | Main menu redesign | HIGH | PENDING | — | T417, T418 | ui-engineer |
| T417 | Collection viewer | MEDIUM | PENDING | T416 | — | ui-engineer |
| T418 | Settings menu | LOW | PENDING | T416 | — | ui-engineer |

## Acceptance Criteria

- [ ] Drag-and-drop replaces click-based interaction
- [ ] Card frames show rarity with tribe colors
- [ ] Card art loads from S3 via DevZone
- [ ] Hover shows enlarged card with full details
- [ ] Main menu has Play, Collection, Settings, Profile
- [ ] Opponent boards viewable (read-only)

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase V details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
