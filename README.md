# Gab44 Jasmine Final

Dated snapshot: **2026-09-07**

This repository preserves the exact JavaScript bundle deployed as the Cloudflare Worker `gab44-pages`.

- Live site: https://gab44-pages.nchobah.workers.dev/gab44
- Cloudflare worker: `gab44-pages`
- Worker modified: `2026-09-01T10:50:54.105128Z`
- Snapshot captured: `2026-09-07`
- Source provenance: Gab44 modules restored from the Drive archive documented for this deployment, then published to Cloudflare Workers.

## Contents

- `index.js`: exact deployed ES-module bundle downloaded from Cloudflare
- `wrangler.toml`: deployment-compatible Worker configuration
- `SNAPSHOT.json`: source URL, deployment metadata, byte count, and SHA-256

## Verify locally

```bash
npm run check
npm run dev
```

Then open `http://localhost:8787/gab44`.

## Deploy

This snapshot deliberately contains no credentials or account identifiers. Authenticate Wrangler, then run:

```bash
npm run deploy
```

Deploying under another Worker name is recommended so this historical snapshot remains untouched.
