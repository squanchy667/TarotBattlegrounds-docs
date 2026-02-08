# Deployment Guide

## Architecture

```
[Unity WebGL Build] -> [S3 Bucket] -> [CloudFront CDN] -> Players
                                              |
[Photon Cloud] <-------- WebSocketSecure -----+
```

- **Hosting**: AWS S3 + CloudFront for static WebGL files (~$0.15/mo)
- **Multiplayer**: Photon Cloud (free 20 CCU tier, no server to manage)
- **No EC2 needed** — Photon is fully managed

## Prerequisites

- AWS CLI configured with appropriate credentials
- Unity 2022.3.48f1 installed
- Photon App ID configured in `PhotonServerSettings.asset`

## Build

### From CLI (Recommended)

```bash
# Close Unity Editor first (lockfile conflict)
/Applications/Unity/Hub/Editor/2022.3.48f1/Unity.app/Contents/MacOS/Unity \
  -batchmode -nographics -quit \
  -projectPath /path/to/TarotBattlegrounds-POC/TarotBattlegrounds-POC \
  -executeMethod BuildScript.BuildWebGLCLI
```

`BuildScript.cs` features:
- Auto-increments build numbers (`WebGLBuild/WebGLBuild01`, `WebGLBuild02`, etc.)
- Keeps last 10 builds, deletes older ones
- Output: `TarotBattlegrounds-POC/WebGLBuild/WebGLBuildNN/`

### From Editor

`File > Build Settings > WebGL > Build`

## Deploy

### Quick Redeployment

```bash
cd AWS/scripts
./redeploy.sh
```

`redeploy.sh` auto-detects the highest-numbered build directory. No arguments needed.

What it does:
1. Finds latest `WebGLBuild/WebGLBuildNN/` directory
2. Uploads to S3 with correct MIME types
3. Sets Brotli content-encoding headers for `.br` files
4. Invalidates CloudFront cache

### First-Time Deployment

```bash
cd AWS/scripts
./deploy-webgl.sh
```

Creates S3 bucket, CloudFront distribution, and uploads the build.

### Validate

```bash
cd AWS/scripts
./validate-deployment.sh
```

Checks S3 uploads, CloudFront status, and response headers.

## Critical Configuration

### CloudFront Response Headers

Required for WebGL SharedArrayBuffer support:

```
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: credentialless
```

**Important:** `credentialless` (not `require-corp`) is required because the game fetches JSON from a cross-origin S3 bucket at runtime. `require-corp` blocks cross-origin fetches unless the response includes `Cross-Origin-Resource-Policy`, which S3 doesn't provide.

### S3 MIME Types

Brotli-compressed Unity files need explicit content types:

| File | Content-Type | Content-Encoding |
|------|-------------|------------------|
| `.data.br` | `application/octet-stream` | `br` |
| `.wasm.br` | `application/wasm` | `br` |
| `.js.br` | `application/javascript` | `br` |
| `.framework.js.br` | `application/javascript` | `br` |

### Photon WebGL Protocol

`PhotonConnector.cs` auto-configures:
```csharp
#if UNITY_WEBGL && !UNITY_EDITOR
PhotonNetwork.PhotonServerSettings.AppSettings.Protocol =
    ExitGames.Client.Photon.ConnectionProtocol.WebSocketSecure;
#endif
```

## Runtime Data (S3)

The game fetches card definitions, synergies, and balance config from S3 at startup:

```
https://tarot-battlegrounds-data-prod.s3.amazonaws.com/live/cards.json
https://tarot-battlegrounds-data-prod.s3.amazonaws.com/live/synergies.json
https://tarot-battlegrounds-data-prod.s3.amazonaws.com/live/config.json
```

The `live/` prefix has a public-read bucket policy. CORS allows the game's CloudFront origin. If S3 is unreachable, the game falls back to its built-in 35-card pool.

Edits made in the DevZone web editor and published to live are picked up by the game on next page load.

## Live URL

The game is deployed at the CloudFront distribution URL. No custom domain is configured.

## CI/CD

Currently manual (no CI/CD pipeline). The workflow is:
1. Make changes in Unity
2. Build WebGL from CLI
3. Run `redeploy.sh`
4. Run `validate-deployment.sh`

## Cost

- **S3 + CloudFront**: ~$0.15/month for static hosting
- **Photon Cloud**: Free tier (20 concurrent users)
- **Total**: ~$0.15/month

## Related Documentation

- [Setup Guide](setup-guide.md) — local development setup
- [Multiplayer Architecture](multiplayer.md) — Photon networking
- [DevZone Editor](devzone.md) — game data deployment
