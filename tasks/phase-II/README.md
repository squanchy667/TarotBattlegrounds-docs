# Phase II: New Abilities & Hero Powers

> Expand the ability system with 4 new triggers, 8 new effects, and introduce 12 hero powers with a full HeroPower subsystem.

## Overview

Phase II deepens gameplay by adding new combat triggers (OnAllyDeath, OnAllySummoned, OnSell, Aura), implementing 8 powerful effects (Reborn, Windfury, Venomous, SummonToken, StealBuff, GainArmor, BuffAllTribes, RandomTransform), and introducing the hero power system with 12 unique hero powers. All new abilities integrate into CombatManager and sync to DevZone.

**Key Deliverables:**
- 4 new ability triggers with IAbility subclasses
- 8 new ability effects integrated into combat
- HeroPower base class, manager, and database (12 powers)
- Hero power UI with cooldown and selection
- CombatManager integration for all new triggers/effects
- DevZone enum sync

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| II-B1 | T101-T104 (New triggers) | ability-engineer |
| II-B2 | T105-T112 (New effects) | ability-engineer, unity-game-developer |
| II-B3 | T113-T116 (Hero power system) | hero-power-engineer, ui-engineer |
| II-B4 | T117-T118 (Integration + sync) | ability-engineer, devzone-sync-engineer |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T101 | Add OnAllyDeath trigger + IAbility subclass | HIGH | PENDING | — | T105, T108, T117 | ability-engineer |
| T102 | Add OnAllySummoned trigger + IAbility subclass | HIGH | PENDING | — | T108, T117 | ability-engineer |
| T103 | Add OnSell trigger + IAbility subclass | MEDIUM | PENDING | — | T117 | ability-engineer |
| T104 | Add Aura trigger (continuous buff) | HIGH | PENDING | — | T111, T117 | ability-engineer |
| T105 | Implement Reborn effect (revive with 1 HP) | HIGH | PENDING | T101 | T117 | ability-engineer |
| T106 | Implement Windfury effect (attack twice) | HIGH | PENDING | — | T117 | ability-engineer |
| T107 | Implement Venomous effect (instant kill) | HIGH | PENDING | — | T117 | ability-engineer |
| T108 | Implement SummonToken effect | HIGH | PENDING | T101, T102 | T117 | ability-engineer |
| T109 | Implement StealBuff effect | MEDIUM | PENDING | — | T117 | ability-engineer |
| T110 | Implement GainArmor effect | MEDIUM | PENDING | — | T117 | ability-engineer |
| T111 | Implement BuffAllTribes effect | MEDIUM | PENDING | T104 | T117 | ability-engineer |
| T112 | Implement RandomTransform effect | LOW | PENDING | — | T117 | ability-engineer |
| T113 | HeroPower base class + HeroPowerManager | HIGH | PENDING | — | T114, T115 | hero-power-engineer |
| T114 | HeroPowerDatabase (12 hero powers) | HIGH | PENDING | T113 | T115 | hero-power-engineer |
| T115 | Hero power recruit phase integration | HIGH | PENDING | T113, T114 | T116 | hero-power-engineer |
| T116 | Hero power UI (button, cooldown, selection) | HIGH | PENDING | T115 | — | ui-engineer |
| T117 | CombatManager integration (new triggers + effects) | HIGH | PENDING | T101-T112 | T118 | ability-engineer |
| T118 | DevZone enum sync (new abilities + hero powers) | HIGH | PENDING | T117 | — | devzone-sync-engineer |

## Acceptance Criteria

- [ ] All 8 new ability effects work in combat simulation
- [ ] 4 new triggers fire correctly in CombatManager
- [ ] 12 hero powers selectable and functional
- [ ] Hero powers sync across network
- [ ] DevZone card editor shows all new ability types

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase II details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
