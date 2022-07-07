# Monoprice Select Mini Control Board Replacement: SKR Mini E3 V3

I replaced the control board in my MPSM V1 with a SKR Mini E3 V3.0.
This control board is set up as a direct replacement for Ender printers, so
some adjustment is needed to make it work with the MPSM.

## Hardware Setup

The main thing to note here is to plug the Hotend Cooling Fan into Fan Header 1,
not Fan Header 0.  F0 is the part cooling fan and usually only turns on when
instructed by G-Code.  The F1 header is by default bound to control the
`E0_AUTO_FAN_PIN`.  This is set to turn on automatically any time the extruder
temp is over `EXTRUDER_AUTO_FAN_TEMPERATURE`.

## Steps to Build & Update the Firmware

1. Download Visual Studio Code
2. Install the [PlatformIO IDE](https://marketplace.visualstudio.com/items?itemName=platformio.platformio-ide) plugin
3. Install the [GNU Arm Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain).  For me, that was [gcc-arm-11.2-2022.02-mingw-w64-i686-arm-none-eabi.exe](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads)
4. Clone the [bigtreetech/Marlin](https://github.com/bigtreetech/Marlin) repo.
5. Checkout the `SKR-mini-E3-V3.0-G0B1` branch.
6. Make any needed changes to `Configuration.h` and `Configuration_adv.h`
7. Use PlatformIO to build the firmware (Command Pallette -> "PlatformIO: Build")
8. Copy the `.pio\build\STM32G0B1RE_btt\firmware.bin` file onto an empty SD Card
9. While turned off, plug the SD Card into your SKR control board and then boot
    the printer.  It'll take a few seconds and then be ready.

## Firmware Modifications for Marlin for SKR Mini E3 V3.0

In the following blocks, changes that might be made are commented out, changes
I actually made are not.

### Useful Info

If you want to check how pins are set up, look at `Marlin/src/pins/stm32g0/pins_BTT_SKR_MINI_E3_V3_0.h`

By default, the expected fan pin setup is

* `FAN_PIN = PC6 // Fan 0` -- Part Cooling Fan
* `FAN1_PIN = PC7 // Fan 1` -- Hotend Fan
* `FAN2_PIN = PB15 // Fan 2` -- Controller Fan Pin



### Configuration.h

```C++
#define CUSTOM_MACHINE_NAME "MPSM Franky"

#define EXTRUDE_MINTEMP 185

// On my MPSM, the endstops seemed to have inverted logic from what the
// SKR board was expecting, this fixed that problem.
#define X_MIN_ENDSTOP_INVERTING true // Set to true to invert the logic of the endstop.
#define Y_MIN_ENDSTOP_INVERTING true // Set to true to invert the logic of the endstop.
#define Z_MIN_ENDSTOP_INVERTING true // Set to true to invert the logic of the endstop.

// Handy to pre-program the E-Steps so you have a good starting point to work from
// later.
#define DEFAULT_AXIS_STEPS_PER_UNIT   { 93, 93, 1097.50, 97 }
// I had an issue with the Z-axis sounding weird (gridning?) when I set the
// E-Steps correctly.  This fixed the problem along with the HOMING_FEEDRATE_MM_M below.
#define DEFAULT_MAX_FEEDRATE          { 150, 150, 1.5, 50 }
#define DEFAULT_MAX_ACCELERATION      { 800, 800, 20, 10000 }

// If the motors are moving the wrong way, you can either flip the stepper plug
// direction or you can toggle these flags between `true` and `false`
#define INVERT_E0_DIR false
// #define INVERT_X_DIR true
// #define INVERT_Y_DIR true
// #define INVERT_Z_DIR false

// Once homed, these will prevent the printer from going out of bounds.
// It might be useful to set the x-axis a little larger if you want to be able to do
// stuff like purge wipe outisde of the printing area that you set in your slicer.
#define X_BED_SIZE 120
#define Y_BED_SIZE 120
#define Z_MAX_POS 120

// This is a companion setting to the DEFAULT_MAX_FEEDRATE and DEFAULT_MAX_ACCELERATION
// set above
#define HOMING_FEEDRATE_MM_M { (50*60), (50*60), (4*60) }
```

### Configuration_adv.h

```c++
// I was trying to fix the touchscreen not being able to see the SD card on the SKR
// itself.  This didn't work but I thought I'd leave this here as a jumping off point
// for continuing to research the issue.
#define SD_SPI_SPEED SPI_QUARTER_SPEED
#define SD_DETECT_STATE HIGH

// These are probably already set.  Marlin will complain if you try to
// change E0 Auto Pin to PC6 which is set up as FAN 0 or the part
// cooling fan.
#define E0_AUTO_FAN_PIN PC7 // PC7 is set up in the pins file as FAN 1
#define EXTRUDER_AUTO_FAN_TEMPERATURE 50 // If you want the hotend fan to kick in at a different temp, change this
```

### `Configuration.h` adjustments for Micro Swiss Bowden Dual Gear Extruder

```c++
#define INVERT_E0_DIR true
#define DEFAULT_AXIS_STEPS_PER_UNIT   { 93, 93, 1097.50, 130 }
```
