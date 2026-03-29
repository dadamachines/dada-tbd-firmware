# Doom

Doom (1993) running on the TBD-16 RP2350 with full audio through the P4 codec.

**Author:** [dadamachines](https://github.com/dadamachines)
**Repository:** [dadamachines/dada-tbd-doom](https://github.com/dadamachines/dada-tbd-doom)
**License:** GPL-2.0

## Features

- Full Doom gameplay on RP2350 at 150 MHz
- OPL music and sound effects via P4 audio codec
- 128×64 OLED display (SSD1309) with 15 dithering modes
- WAD loading from SD card
- Button input via I2C keyboard controller

## Data Files

This app requires `doom1.whx` on the SD card. The file is included in `data/` and must be copied to the SD card's `DATA/` directory:

```
SD card
└── DATA/
    └── DOOM1.WHX
```

The bootloader will copy data files automatically when the app is first installed.
