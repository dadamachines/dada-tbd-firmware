# Flash Nuke

Erases the RP2350 flash memory. Use this to factory-reset the Pico before reinstalling the bootloader and apps.

**Author:** [Raspberry Pi](https://github.com/raspberrypi)
**License:** BSD-3-Clause

## Usage

1. Boot the RP2350 into BOOTSEL mode (hold right front button while powering up)
2. Drag and drop `universal_flash_nuke.uf2` onto the RP2350 USB drive
3. The Pico will erase its flash and reboot into BOOTSEL mode
4. Reinstall the bootloader and apps from a fresh SD card image
