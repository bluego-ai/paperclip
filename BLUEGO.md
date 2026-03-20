# bluego.ai Deployment Notes

This is the `bluego` branch of the `bluego-ai/paperclip` fork. It tracks upstream `paperclipai/paperclip` with minimal additions for on-droplet deployment.

## What's Different

This branch adds:
- `bluego.json` — deployment config documenting the openclaw-fork integration
- This file

**Everything else is upstream Paperclip unchanged.**

## OpenClaw Integration

Paperclip already has native OpenClaw support via `@paperclipai/adapter-openclaw-gateway`. No code changes needed.

On bluego.ai droplets, each OpenClaw agent runs from `bluego-ai/openclaw-fork` (bluego branch) on its own port (18789, 18790, ...). Paperclip connects to each agent via:

```
url: ws://localhost:{port}
token: {gatewayToken}
sessionKeyStrategy: issue
```

Agent ports and gateway tokens are injected by `paperclip-sidecar` at provisioning time via:
```
POST /sidecar/agents/{agentId}/config
```

The sidecar writes `openclaw.json` per agent and updates `/opt/openclaw/company.json` with the `agentPorts` map.

## Keeping in Sync with Upstream

```bash
git fetch upstream
git rebase upstream/master
git push origin bluego --force-with-lease
```

## Architecture

See `projects/per-company-provisioning-v4.md` in the bluego.ai workspace for the full architecture.

Repos involved:
- `bluego-ai/monorepo` — cloud: auth, billing, onboarding, provisioning
- `bluego-ai/openclaw-fork` (bluego branch) — AI agent gateway, runs on droplet
- `bluego-ai/paperclip-sidecar` — infra management API, runs on droplet
- `bluego-ai/paperclip` (this repo, bluego branch) — dashboard + work orchestration, runs on droplet
