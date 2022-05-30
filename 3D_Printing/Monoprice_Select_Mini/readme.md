# Monoprice Select Mini

- [Monoprice Select Mini](#monoprice-select-mini)
  - [Start and End G-Code](#start-and-end-g-code)
    - [Start](#start)
    - [End](#end)
  - [Configure E-Stops](#configure-e-stops)
    - [Default Values](#default-values)
    - [Process](#process)
  - [Useful 3D Printed Modifications](#useful-3d-printed-modifications)
  - [Replacement Control Board: SKR Mini E3 V3.0](#replacement-control-board-skr-mini-e3-v30)

## Start and End G-Code

### Start

```
G21                 ;(metric values)
G90                 ;(absolute positioning)
M82                 ;(set extruder to absolute mode)
M107                ;(start with the fan off)
G28                 ;(Home the printer)
G92 E0              ;(Reset the extruder to 0)

G0 Z5 E5 F500       ;(Move up and prime the nozzle)
G0 X-1 Z0           ;(Move outside the printable area)
G1 Y60 E8 F500      ;(Draw a priming/wiping line to the rear)
G1 X-1              ;(Move a little closer to the print area)
G1 Y10 E16 F500     ;(draw more priming/wiping)
G1 E15 F250         ;(Small retract)
G92 E0              ;(Zero the extruder)
```

### End

```
G0 Z120 Y120 X110   ;(Move to the top back)
G92 E0              ;(reset the extruder position)
G1 E-30             ;(retract a bunch)
M104 S0             ;(turn off hotend/extruder heater)
M140 S0             ;(turn off bed heater)
G92 E10             ;(Set extruder to 10)
G1 E7 F200          ;(retract 3mm)
G4 S15              ;(Delay 15 seconds)
M107                ;(Turn off part fan)
M84                 ;(Turn off stepper motors.)
```

## Configure E-Stops

### Default Values

|                   |     X |      Y |       Z |     E |
|:---               |   ---:|    ---:|     ---:|   ---:|
| V1                | 93.00 | 93.00  | 1097.50 | 97.00 |
| V2                | 46.50 | 46.50  | 548.75  | 48.50 |
| V2 (post 06-2017) | 93.00 | 93.00  | 1097.50 | 97.00 |

### Process

1. Run the command `M503` on the printer
2. Look for the line that looks like
    ```
    M92 X<xx.xx> Y<yy.yy> Z<zz.zz> E<ee.ee>
    ```
    with whatever your current values are.
3. For each of the axes
    1. Zero a caliper on the nozzle carriage's current
    position.
    2. Command the printer to move by a certain amount (make note of
    how much).  Note: the further it moves, the more accurate you can be.
    3. Measure the ammount the printer actually moved.
    4. Determine the new eStep value with the formula
    `(expected_distance / actual_distance) * current_estep_setting = new_estep_setting`
    5. Send the command for
       1. x-axis: `M92 X<calcualted new value>`
       2. y-axis: `M92 Y<calcualted new value>`
       3. z-axis: `M92 Z<calcualted new value>`
       4. Extruder: `M92 E<calcualted new value>`
    6. Save the setting with the command `M500`
4. Repeat this process to confirm that the settings were well-dialed-in

## Useful 3D Printed Modifications

* [Filament Guide, M6 Bowden Adapter](https://www.printables.com/model/183102)
* [40mm Cooling Shroud Replacement](https://www.thingiverse.com/thing:2429054) - compatible w/ stock clips
* Bed
    * [Bed Leveling Thumbscrews](https://www.thingiverse.com/thing:4626595)
    * [Bed cable brace](https://www.thingiverse.com/thing:2764036)
* Side Panels
    * [Side Panel w/ Fan & Cable re-route](https://www.printables.com/model/171177)
    * [Side Panel w/ rear cable re-route](https://www.printables.com/model/12710)
* Z-Axis Endstop Spacers
    * [Endstop Re-Locate & Screw Adjuster](https://www.thingiverse.com/thing:2217149)

## Replacement Control Board: SKR Mini E3 V3.0

[Marlin Modifications](./skr_mini_e3_v3.md)
