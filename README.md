# dada-tbd-firmware

Firmware CDN for TBD-16 — served via GitHub Pages.

**URL:** `https://dadamachines.github.io/dada-tbd-firmware/`

## Structure

```
stable/               ← latest stable release
  latest.json         ← channel manifest (tag, timestamp, file paths)
  p4/
    ctag-tbd.bin      ← main P4 firmware (from CI)
    tbd-sd-card.zip   ← SD card image (from CI)
    tbd-sd-card-hash.txt
    tusb_msc.bin      ← USB Mass Storage firmware (manual)
  pico/
    ctag-tbd-pico.uf2 ← RP2350 firmware (manual)
staging/              ← staging pre-releases (same layout)
feature/<name>/       ← feature-test builds (same layout)
```

## How it works

1. The `ctag-tbd` repo CI builds firmware and triggers `repository_dispatch`
2. `receive-firmware.yml` downloads the artifact and places files in the channel directory
3. `deploy-pages.yml` publishes the repo to GitHub Pages
4. Flash pages on `dadamachines.github.io/ctag-tbd/` fetch firmware from
   `dadamachines.github.io/dada-tbd-firmware/` — **same origin, no CORS issues**

## Static firmware

`tusb_msc.bin` and `ctag-tbd-pico.uf2` are built separately and committed directly.
They change rarely and are not part of the ESP-IDF CI pipeline.
A place for binaries
