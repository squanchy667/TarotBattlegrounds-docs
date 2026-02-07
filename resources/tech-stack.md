# Tech Stack

## Game Engine

- **Unity 2022.3.48f1** (LTS)
- **C#** primary language
- **WebGL** build target (deployed via S3 + CloudFront)

## Networking

- **Photon PUN 2** — multiplayer networking
- **WebSocketSecure** protocol for WebGL builds
- **Photon Cloud** — managed server (free 20 CCU tier)

## AWS Infrastructure

| Service | Purpose |
|---------|---------|
| S3 | WebGL build hosting, game data JSON, card images |
| CloudFront | CDN with COOP/COEP headers for SharedArrayBuffer |
| Lambda | DevZone API backend |
| API Gateway | HTTP API routing to Lambda |
| DynamoDB | DevZone user accounts, version metadata |

## DevZone (Game Editor)

- **Frontend**: React + Vite + TypeScript + Tailwind CSS
- **Backend**: Express + TypeScript + serverless-http
- **Validation**: Zod schemas (shared between client/server)
- **Auth**: JWT tokens, bcrypt passwords

## Libraries & Packages

| Package | Purpose |
|---------|---------|
| Photon PUN 2 | Multiplayer RPCs and state sync |
| Newtonsoft.Json | JSON parsing for runtime data loading |
| TextMeshPro | UI text rendering |
| @aws-sdk/* | S3, DynamoDB, CloudFront access (DevZone) |
| @tanstack/react-query | Data fetching (DevZone frontend) |

## Tools

- Visual Studio / VS Code / Rider
- Git / GitHub
- Unity Hub
- AWS CLI
- ParrelSync (multiplayer testing in Editor)

## Build & Deploy

- `BuildScript.cs` — auto-increment WebGL builds, keeps last 10
- `redeploy.sh` — auto-detects latest build, uploads to S3, invalidates CloudFront
- No CI/CD pipeline — manual build and deploy workflow

## Related Documentation

- [Deployment Guide](../developer/deployment.md)
- [DevZone Editor](../developer/devzone.md)
- [Multiplayer Architecture](../developer/multiplayer.md)
