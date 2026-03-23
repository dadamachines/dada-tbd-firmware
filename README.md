# dada-tbd-firmware

Firmware CDN and App Catalog for [dadamachines TBD-16](https://dadamachines.com) — served via GitHub Pages.

**URL:** `https://dadamachines.github.io/dada-tbd-firmware/`

## Structure

```
apps/                      ← shared utility apps + app catalog
  tusb-msc/
    dada-tbd-16-tusb-msc.bin        ← USB mass storage firmware (shared, not per-channel)
  groovebox/
    manifest.json          ← app metadata, release info, SHA-256
    README.md
stable/                    ← latest stable release (dispatch-only)
  releases.json            ← channel manifest (versions array)
  p4/                      ← ESP32-P4 firmware (versioned filenames accumulate)
    dada-tbd-16-v0.4.2-unified.bin
    dada-tbd-16-v0.4.2-sd.zip
    dada-tbd-16-v0.4.2-sd-hash.txt
    dada-tbd-16-v0.4.1-unified.bin
    dada-tbd-16-v0.4.1-sd.zip
    dada-tbd-16-v0.4.1-sd-hash.txt
  pico/                    ← RP2350 firmware (versioned only, no unversioned copies)
    dada-tbd-16-v0.4.1-pico.uf2
staging/                   ← staging pre-releases (same layout as stable/)
  releases.json
  p4/
    dada-tbd-16-staging-v0.4.2-3-unified.bin
    ...
  pico/
    dada-tbd-16-staging-v0.4.2-3-pico.uf2
feature-test-*/            ← ephemeral per-feature channels (same layout)
webui-updates/             ← WebUI update packages (mirror of docs site)
  latest.json              ← version manifest for on-device updater
  webui-update-v0.3.5.zip
  webui-update-v0.3.4.zip
  ...
schema/                    ← JSON schemas
  manifest.schema.json
app-catalog.json           ← auto-generated from apps/*/manifest.json
index.html                 ← landing page with channel links
```

## How It Works

### P4 Firmware (dispatch)
1. `dadamachines/ctag-tbd` CI builds firmware → triggers `repository_dispatch`
2. `receive-firmware.yml` downloads artifact → places in channel directory → deploys Pages

### Pico Apps — Profile A (dispatch)
1. Trusted contributor tags release → CI dispatches to this repo
2. `receive-pico-app.yml` downloads `.uf2` → verifies SHA-256 → commits → deploys Pages

### Pico Apps — Profile B (manifest PR)
1. External contributor creates GitHub Release with `.uf2` attached
2. Opens PR to add/update `apps/{app-id}/manifest.json` with `sourceUrl` + `sha256`
3. `validate-pr.yml` checks schema, downloads binary server-side, verifies SHA-256
4. Maintainer reviews and merges
5. `build-catalog.yml` downloads binary → commits to `apps/{id}/` → regenerates `app-catalog.json` → deploys Pages

### Why Binaries Live Here (CORS)

GitHub Release download URLs (`github.com/.../releases/download/...`) do **not**
set `Access-Control-Allow-Origin` headers. The browser-based flash page and App
Manager on `dadamachines.github.io` can only `fetch()` from the same origin.

All binaries are served from `dadamachines.github.io/dada-tbd-firmware/` — same
origin, no CORS issues. CI workflows download from external sources server-side
(GitHub Actions runner) and commit the binaries here.

## Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `receive-firmware.yml` | `repository_dispatch: firmware-update` | P4 firmware from main repo CI |
| `receive-pico-app.yml` | `repository_dispatch: pico-app-update` | Pico `.uf2` from trusted contributors |
| `validate-pr.yml` | PR touching `apps/` or `system-tools/` | Validate manifest PRs |
| `build-catalog.yml` | Push to `main` touching `apps/` | Download binaries + regenerate catalog |
| `deploy-pages.yml` | Push to `main` | Deploy everything to GitHub Pages |

## Publishing an App

See [CONTRIBUTING.md](https://github.com/dadamachines/ctag-tbd/blob/dada-tbd-master/CONTRIBUTING.md#building-rp2350-apps-pico-side) in the main repo for the full guide.
