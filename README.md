# dada-tbd-firmware

Firmware CDN and App Catalog for [dadamachines TBD-16](https://dadamachines.com) — served via GitHub Pages.

**URL:** `https://dadamachines.github.io/dada-tbd-firmware/`

## Structure

```
stable/                    ← latest stable release (dispatch-only)
  releases.json            ← channel catalog (versions array, shared file paths)
  p4/                      ← ESP32-P4 firmware binaries
    dada-tbd-16-app.bin             ← latest app firmware
    dada-tbd-16-bootloader.bin      ← latest bootloader
    dada-tbd-16-partitions.bin      ← latest partition table
    dada-tbd-16-otadata.bin         ← latest OTA data
    dada-tbd-16-sd.zip              ← latest SD card image
    dada-tbd-16-sd-hash.txt         ← latest SD card hash
    dada-tbd-16-tusb-msc.bin        ← USB mass storage firmware
    dada-tbd-16-v0.5.0-unified.bin  ← versioned unified image (accumulates)
    v0.5.0/                         ← version archive
      dada-tbd-16-sd.zip
      dada-tbd-16-sd-hash.txt
    v0.4.2/                         ← older version archive
      dada-tbd-16-sd.zip
      dada-tbd-16-sd-hash.txt
  pico/                    ← RP2350 firmware
    dada-tbd-pico.uf2               ← latest (source for versioned copies)
    dada-tbd-16-v0.5.0-pico.uf2    ← versioned (accumulates)
staging/                   ← staging pre-releases (same layout)
feature-test-*/            ← ephemeral feature channels
apps/                      ← app registry (PR-reviewed)
  groovebox/
    manifest.json          ← app metadata, release info, SHA-256
    README.md
    groovebox-0.4.0.uf2    ← binary (committed by CI)
  mcl/
    manifest.json
    ...
schema/                    ← JSON schemas
  manifest.schema.json
app-catalog.json           ← auto-generated from apps/*/manifest.json
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
