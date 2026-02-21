# Phase IV: Combat Animation & VFX

> Replace instant combat results with animated replays featuring VFX, SFX, music, and network broadcast.

## Overview

Phase IV transforms the combat experience from instant resolution to a cinematic animated replay system. This includes a replay data structure recorded by CombatManager, coroutine-based playback with per-card animations, a pooled VFX system with attack/death/ability particles, an audio system with SFX and crossfading music, skip/speed controls, and network broadcast so all clients see the same replay.

**Key Deliverables:**
- CombatReplay data structure + recording
- CombatAnimator coroutine playback system
- Attack and death animations (lunge, impact, fade, dissolve)
- VFXManager with particle pooling and per-type effects
- SFXManager with AudioSource pooling
- MusicManager with crossfade tracks
- Skip button and speed controls
- Network replay broadcast
- 60fps performance target with full VFX

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| IV-B1 | T301-T303 (Replay data) | combat-vfx-engineer |
| IV-B2 | T304-T307 (Animator + visuals) | combat-vfx-engineer |
| IV-B3 | T308-T311 (VFX system) | combat-vfx-engineer |
| IV-B4 | T312-T315 (Audio system) | sfx-engineer |
| IV-B5 | T316-T320 (Integration) | combat-vfx-engineer, network-engineer |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T301 | CombatReplay data structure | HIGH | PENDING | — | T302, T303 | combat-vfx-engineer |
| T302 | CombatManager replay recording | HIGH | PENDING | T301 | T304 | combat-vfx-engineer |
| T303 | Replay serialization for network | MEDIUM | PENDING | T301 | T318 | combat-vfx-engineer |
| T304 | CombatAnimator coroutine playback | HIGH | PENDING | T302 | T305, T316 | combat-vfx-engineer |
| T305 | CombatCardVisual animations | HIGH | PENDING | T304 | T306, T307 | combat-vfx-engineer |
| T306 | Attack animation (lunge + impact) | HIGH | PENDING | T305 | — | combat-vfx-engineer |
| T307 | Death animation (fade + dissolve) | HIGH | PENDING | T305 | — | combat-vfx-engineer |
| T308 | VFXManager with particle pooling | HIGH | PENDING | — | T309, T310, T311 | combat-vfx-engineer |
| T309 | Attack VFX (swoosh, impact burst) | MEDIUM | PENDING | T308 | — | combat-vfx-engineer |
| T310 | Ability VFX (per-type particles) | MEDIUM | PENDING | T308 | T319 | combat-vfx-engineer |
| T311 | Death VFX (soul release, shatter) | MEDIUM | PENDING | T308 | — | combat-vfx-engineer |
| T312 | SFXManager with AudioSource pooling | HIGH | PENDING | — | T313 | sfx-engineer |
| T313 | SFXConfig ScriptableObject | HIGH | PENDING | T312 | T314 | sfx-engineer |
| T314 | Combat SFX integration | MEDIUM | PENDING | T313 | T319 | sfx-engineer |
| T315 | MusicManager (crossfade tracks) | MEDIUM | PENDING | — | — | sfx-engineer |
| T316 | UI integration (show animated replay) | HIGH | PENDING | T304, T308 | T317, T320 | combat-vfx-engineer |
| T317 | Skip button + speed controls | MEDIUM | PENDING | T316 | — | combat-vfx-engineer |
| T318 | Network replay broadcast | HIGH | PENDING | T303 | — | network-engineer |
| T319 | Ability trigger VFX/SFX in replay | MEDIUM | PENDING | T310, T314 | — | combat-vfx-engineer |
| T320 | Performance profiling | HIGH | PENDING | T316 | — | combat-vfx-engineer |

## Acceptance Criteria

- [ ] Combat shows animated replay instead of instant results
- [ ] VFX for attack, death, and ability triggers
- [ ] SFX for all combat events
- [ ] Background music with phase-appropriate tracks
- [ ] Skip/speed controls work
- [ ] Replay broadcasts to all network clients
- [ ] Maintains 60fps with full VFX

## Reference

- [PLAN.md](../../PLAN.md) — Full Phase IV details
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
