## TBD Bootloader 

1) connect tbd with usb 2 (outermost rear, rightmost) to computer
2) boot rp2350 into bootloader mode by pressing right front button while powering up device (left from usb-c), rp2350 icon should appear as usb drive on computer
3) flash (drag and drop onto rp2350 icon) rp2350 with sd-card/tbd-apps/tusb_msc.uf2 
4) delete all files on rp2350 sd-card
5) copy all files from sd-card onto root of rp2350 sd-card
6) eject rp2350 sd-card properly
7) bring rp2350 again into bootloader mode (see step 2)
8) flash rp2350 with bootloader-1.1.0.uf2
9) reboot rp2350

Alternatively, you can flash ui_test.uf2 in bootloader mode (step 1+2) to interact with legacy tbd firmware directly without custom bootloader.

## Custom Firmware Flashing via Bootloader
- to access boot menu, press cursor up (button between leftmost two pots) on ui when powering up device
- you can then chose from different firmwares to flash to rp2350
- chose firmware with leftmost pot
- flash firmware with long press on leftmost pot
- use ui_test for interaction / demo with legacy tbd fw
- use tusb_msc for fast sdio access via usb to rp2350 sd-card
- you can make the rp2350 sd-card available directly thru the custom bootloader, scroll to bottom of menu

## Flash stm32 ui firmware via rp2350 debugprobe
- install openocd with cmsis-dap support on your computer
- connect rp2350 to computer via usb 2 (outermost rear, rightmost)
- boot dbg_prg.uf2 on rp2350 to enable debugprobe functionality with bootloader (see above)
- execute ```openocd -f interface/cmsis-dap.cfg -c "adapter speed 1000" -c "transport select swd" -f target/stm32f0x.cfg -c "program dada-tbd-ui.elf verify reset exit"```