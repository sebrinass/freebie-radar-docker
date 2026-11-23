# freebie-radar-docker

Unraid-ready Docker image builds for [freebie-radar](https://github.com/ZiChuanLan/freebie-radar) (白嫖雷达 / 资源雷达) — a self-hosted freebie aggregation tool written in TypeScript.

Upstream ships its own Dockerfile (frontend pnpm build + backend), so this repo simply checks out the upstream `main` branch every **3 days**, builds it with GitHub Actions, and pushes to GHCR. No fork, no patches.

## Image

```
ghcr.io/sebrinass/freebie-radar-docker:latest
```

Tags: `latest` plus a dated commit tag (`YYYYMMDD-<shortsha>`).

## How it updates

- A scheduled workflow runs every 3 days at 04:37 UTC (12:37 Beijing time).
- Manual trigger: go to [Actions → update → Run workflow](https://github.com/sebrinass/freebie-radar-docker/actions/workflows/update.yml), optionally specifying an upstream branch/tag to build.
- Each build also runs a smoke test: the container must start and answer `/health` before the image is published.

## Usage (docker / Unraid)

| Item | Value |
|---|---|
| Port | `8788` (container listens on `0.0.0.0:8788`) |
| Volume | `/data` — SQLite DB lives at `/data/radar.db` |
| TZ | `Asia/Shanghai` (recommended) |

Key environment variables: `PORT=8788`, `HOST=0.0.0.0`, `DATABASE_PATH=/data/radar.db`, `SITE_MODE=selfhost`, `ADMIN_TOKEN` (leave empty for open access), `FLARESOLVERR_URL` (optional, for Cloudflare-protected sources).

Example:

```bash
docker run -d \
  --name freebie-radar \
  --restart unless-stopped \
  -p 8788:8788 \
  -v /mnt/user/appdata/freebie-radar:/data \
  -e TZ=Asia/Shanghai \
  ghcr.io/sebrinass/freebie-radar-docker:latest
```

For Unraid, an `unraid/` template XML draft is kept in this repo.

## Credits

All credit to the upstream project: [ZiChuanLan/freebie-radar](https://github.com/ZiChuanLan/freebie-radar) (MIT).
