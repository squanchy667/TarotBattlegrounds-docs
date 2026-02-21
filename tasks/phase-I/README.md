# Phase I: Online Infrastructure

> Extend the existing DevZone AWS backend with game player auth, matchmaking, and Photon lobby integration. Builds on the proven JWT + DynamoDB + Lambda stack — no Cognito needed.

## Overview

Phase I transforms Tarot Battlegrounds from a local-only game into a fully online multiplayer experience. **The DevZone already provides** a working SAM template, DynamoDB tables, Express-on-Lambda API, JWT auth middleware, S3 + CloudFront hosting, and deploy scripts. Phase I extends this stack with game-specific player accounts, matchmaking, and Photon lobby integration.

**Already exists (DO NOT rebuild):**
- SAM template (`tarot-devzone/aws/template.yaml`) with S3, DynamoDB, Lambda, CloudFront
- JWT auth pattern (`server/src/routes/auth.ts`) with bcrypt + jsonwebtoken
- Auth middleware (`server/src/middleware/auth.ts`) with Bearer token validation
- Photon PUN 2 rooms, RPCs, state sync, WebSocketSecure for WebGL
- WebGL deployment at `https://dui22oafwco41.cloudfront.net`

**Key Deliverables:**
- Game player accounts (open registration, unlike DevZone's admin-gated register)
- DynamoDB `PlayersTable` for player profiles and stats
- Matchmaking queue with basic skill-based pairing
- Photon lobbies with player identity (display name, player ID)
- Reconnection handling (rejoin within 60s)

## Batch Execution Plan

| Batch | Tasks | Agents |
|-------|-------|--------|
| I-B1 | T001-T002 (PlayersTable + auth endpoints) | network-engineer |
| I-B2 | T003-T005 (Unity auth + UI + matchmaking backend) | unity-game-developer, network-engineer |
| I-B3 | T006-T007 (Photon integration + testing) | unity-game-developer, tarot-test-agent |

## Tasks

| ID | Task | Priority | Status | Depends On | Blocks | Agent(s) |
|----|------|----------|--------|------------|--------|----------|
| T001 | Add PlayersTable to SAM template + deploy | HIGH | PENDING | — | T002 | network-engineer |
| T002 | Game auth endpoints (register, login, guest, profile) | HIGH | PENDING | T001 | T003, T004 | network-engineer |
| T003 | Unity GameAuthManager + login/register/guest UI | HIGH | PENDING | T002 | T005, T006 | unity-game-developer |
| T004 | Matchmaking queue endpoint (DynamoDB + Lambda) | HIGH | PENDING | T002 | T005 | network-engineer |
| T005 | Unity MatchmakingManager + queue UI | HIGH | PENDING | T003, T004 | T006, T007 | unity-game-developer |
| T006 | Wire Photon lobbies with player identity + reconnection | HIGH | PENDING | T003 | T007 | network-engineer |
| T007 | Integration testing + live playtest via CloudFront | HIGH | PENDING | T005, T006 | — | tarot-test-agent |

## Task Details

### T001: Add PlayersTable to SAM template + deploy
Extend `tarot-devzone/aws/template.yaml` with a new DynamoDB table:
- **PlayersTable**: PK `playerId` (String), GSI on `email` for lookup
- Attributes: displayName, email, passwordHash, rating (default 1000), gamesPlayed, wins, losses, createdAt
- Add `DynamoDBCrudPolicy` for PlayersTable to the Lambda function
- Add env var `DYNAMODB_PLAYERS_TABLE` to Lambda globals
- Deploy via `sam build && sam deploy`

### T002: Game auth endpoints
Add new route file `server/src/routes/game-auth.ts` following the existing `auth.ts` pattern:
- **POST /api/game-auth/register** — Open registration (no admin required, unlike DevZone). Email + password + displayName. Returns JWT.
- **POST /api/game-auth/login** — Email + password. Returns JWT with playerId, displayName, rating.
- **POST /api/game-auth/guest** — Creates anonymous player with generated name ("Guest_XXXX"). Returns JWT. No password needed.
- **GET /api/game-auth/profile** — Returns full player profile (requires JWT).
- **PUT /api/game-auth/profile** — Update displayName (requires JWT).
- Reuse existing `authMiddleware` for protected endpoints. JWT payload: `{ playerId, displayName, rating }`.

### T003: Unity GameAuthManager + login/register/guest UI
- Create `Scripts/Auth/GameAuthManager.cs` singleton with `RegisterAsync()`, `LoginAsync()`, `LoginAsGuestAsync()`, `GetProfile()`.
- Store JWT in `PlayerPrefs` (WebGL-safe). Auto-login on startup if token exists and not expired.
- Create login/register/guest UI panel in MainMenu scene. Show display name after login.
- On auth success: store playerId and displayName for Photon custom properties.

### T004: Matchmaking queue endpoint
Add `server/src/routes/matchmaking.ts`:
- **POST /api/matchmaking/join** — Add player to queue (playerId, rating, mode: casual/ranked). Store in DynamoDB or in-memory.
- **GET /api/matchmaking/status** — Check if match found. Returns Photon room name when matched.
- **DELETE /api/matchmaking/cancel** — Leave queue.
- Matching logic: pair players within ±200 rating. Expand range by 100 every 15s. Min 2 players, max 4.
- When match found: create Photon room name, return to all matched players.

### T005: Unity MatchmakingManager + queue UI
- Create `Scripts/Network/MatchmakingManager.cs` — calls matchmaking API, polls status every 2s, auto-joins Photon room when match found.
- Queue UI: "Finding match..." with cancel button, estimated wait time, player count in queue.
- On match found: connect to Photon with room name from API.

### T006: Wire Photon lobbies with player identity + reconnection
- Set Photon custom properties on join: `playerId`, `displayName`, `rating` from GameAuthManager.
- Display opponent names in lobby and in-game UI (replace "Player 1/2" with display names).
- Reconnection: on disconnect, attempt `PhotonNetwork.ReconnectAndRejoin()` for 60s. Show "Reconnecting..." overlay. If timeout, return to main menu.

### T007: Integration testing + live playtest
- Test full flow: register → login → queue → match → play → game over
- Test guest mode: guest → queue → play (no persistent account)
- Test reconnection: disconnect mid-game → rejoin within 60s
- Deploy updated WebGL build + Lambda via existing deploy scripts
- Verify on CloudFront URL with 2+ browser tabs

## Acceptance Criteria

- [ ] Players can register with email/password and login
- [ ] Guest mode creates anonymous accounts instantly
- [ ] JWT persists across sessions (auto-login)
- [ ] Matchmaking pairs players within rating range
- [ ] Photon lobbies show player display names
- [ ] Reconnection works within 60s timeout
- [ ] Full flow works on CloudFront WebGL deployment
- [ ] Existing DevZone auth is unaffected (separate route namespace)

## Reference

- [PLAN.md](../../PLAN.md) — Full project plan
- [TASK_BOARD.md](../../TASK_BOARD.md) — Dependency tracking
- `tarot-devzone/aws/template.yaml` — SAM template to extend
- `tarot-devzone/server/src/routes/auth.ts` — Existing JWT auth pattern to follow
- `tarot-devzone/server/src/middleware/auth.ts` — Existing auth middleware to reuse
