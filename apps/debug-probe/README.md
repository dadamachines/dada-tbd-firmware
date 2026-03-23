# Debug Probe

Turns the RP2350 into a CMSIS-DAP debug probe for flashing STM32 UI firmware via SWD.

**Author:** [dadamachines](https://github.com/dadamachines)
**License:** MIT

## Usage

1. Boot the RP2350 into this app via the bootloader menu
2. Connect RP2350 to computer via USB 2 (outermost rear, rightmost)
3. Flash STM32 using OpenOCD:

```bash
openocd -f interface/cmsis-dap.cfg \
  -c "adapter speed 1000" \
  -c "transport select swd" \
  -f target/stm32f0x.cfg \
  -c "program dada-tbd-ui.elf verify reset exit"
```
