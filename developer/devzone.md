# DevZone Editor

## Overview

DevZone is a web-based game data editor that allows non-developers to edit cards, synergies, balance parameters, and upload card art — all from a browser. Changes are pushed to S3 and loaded by the game at startup.

## Architecture

```
[DevZone React App]  ->  [Express API on Lambda]  ->  [S3: game-data JSON + images]
                                                             |
[Unity WebGL Game]   <---- fetches cards.json, synergies.json, config.json at startup
```

## Tech Stack

- **Frontend**: React + Vite + TypeScript + Tailwind CSS
- **Backend**: Express + TypeScript + serverless-http (Lambda)
- **Storage**: S3 (game data JSON + card images)
- **Database**: DynamoDB (user accounts + version metadata)
- **Auth**: JWT tokens, bcrypt passwords

## Repo Structure

```
tarot-devzone/
├── client/               # React frontend
│   ├── src/
│   │   ├── components/   # Card editor, synergy panels, balance tuner
│   │   ├── hooks/        # useCards, useSynergies, useAuth
│   │   ├── services/     # API client
│   │   └── App.tsx
│   └── package.json
├── server/               # Express backend
│   ├── src/
│   │   ├── routes/       # cards, synergies, config, images, auth, versions, deploy
│   │   ├── services/     # S3, DynamoDB, CloudFront
│   │   ├── middleware/   # JWT auth, validation
│   │   └── index.ts
│   └── package.json
├── shared/               # Shared TypeScript types
│   ├── types.ts          # CardData, SynergyData, GameConfigData
│   ├── enums.ts          # TribeType, AbilityTrigger, etc.
│   └── schemas.ts        # Zod validation
├── scripts/
│   ├── export-game-data.ts   # Export C# data to initial JSON
│   └── seed-db.ts            # Create initial admin user
└── package.json          # Workspace root
```

## API Endpoints

### Authentication
| Method | Route | Description |
|--------|-------|-------------|
| POST | `/api/auth/login` | Login, returns JWT |
| POST | `/api/auth/register` | Register user (admin-only) |
| GET | `/api/auth/me` | Get current user |

### Cards
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/cards` | List all cards |
| GET | `/api/cards/:id` | Get single card |
| POST | `/api/cards` | Create card |
| PUT | `/api/cards/:id` | Update card |
| DELETE | `/api/cards/:id` | Delete card |

### Synergies
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/synergies` | List all synergies |
| PUT | `/api/synergies/:tribe` | Update tribe synergy |

### Config & Theme
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/config` | Get balance config |
| PUT | `/api/config` | Update config |
| GET | `/api/theme` | Get theme config |
| PUT | `/api/theme` | Update theme |

### Images
| Method | Route | Description |
|--------|-------|-------------|
| POST | `/api/images/upload` | Upload card image |
| DELETE | `/api/images/:key` | Delete image |

### Versions & Deploy
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/versions` | List versions |
| POST | `/api/versions` | Create version snapshot |
| POST | `/api/versions/:id/publish` | Set as live |
| POST | `/api/deploy/publish` | Push draft to live + invalidate CDN |

## S3 Data Layout

```
s3://tarot-battlegrounds-data/
├── live/                    # Currently active data (fetched by game)
│   ├── cards.json
│   ├── synergies.json
│   ├── config.json
│   ├── theme.json
│   └── images/
├── versions/                # Snapshots for rollback
│   ├── v001/
│   └── v002/
└── drafts/                  # Work-in-progress
```

## DynamoDB Tables

- **devzone-users**: userId (PK), email, passwordHash, role, createdAt
- **devzone-versions**: versionId (PK), timestamp, author, description, isLive

## Workflow

1. Editor opens DevZone in browser
2. Logs in with JWT auth
3. Edits cards/synergies/balance (auto-saves to drafts)
4. Creates a version snapshot
5. Publishes version to live (copies to `live/` prefix, invalidates CloudFront)
6. Game fetches updated JSON on next startup

## Related Documentation

- [Runtime Data Loading](runtime-data.md) — how the game loads JSON
- [Deployment Guide](deployment.md) — AWS infrastructure
