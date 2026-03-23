# ESP32-S3 Pin Mapping for `ESP32-S3-LCD.epro`

This document was derived from the schematic inside `ESP32-S3-LCD.epro`.

Notes:
- The ESP32 device is `U1` (`ESP32-S3(FN8)`).
- Several board nets are labeled as `GPx`; those correspond to ESP32 GPIOs.
- Some ESP32 package pin names in the symbol are functional pad names (`MTCK`, `U0TXD`, `SPICLK_P`, `XTAL_32K_P`, etc.), but the board uses them as GPIO nets.
- For firmware-oriented aliases, bus assignments, active levels, and startup notes, see `ESP32-S3_PROGRAMMER_GPIO_GUIDE.md`.

## 1. Module-centric summary

| Module / component | ESP32 connection(s) | Notes |
| --- | --- | --- |
| `U6` `X096-2864KSWPG01-H30` display | `GP14`, `GP15`, `GP16`, `3V3`, `GND` | `RES#` on `GP14`; `D0` on `GP15`; `D1` and `D2` on `GP16`; interface strap pins tie the module into a serial mode. |
| `U2` `SLS32AIA020X2USON10XTMA4` (`OPTIGA Trust X` secure element) | `GP15`, `GP16`, `GP18`, switched `3V3`, `GND` | `SCL`=`GP15`, `SDA`=`GP16`, `RST`=`GP18`; `VCC` is switched by `Q1` under ESP32 control from `GP17`. |
| `USB1` USB-C | `DN`=`GPIO19`, `DP`=`GPIO20`, `VBUS`, `GND` | Direct USB D-/D+ connection to the ESP32. |
| `U8` WS2812B RGB LED | `GP48`, `3V3`, `GND` | Data input `DI` is on `GP48`; `R8` pulls that line up to `3V3`. |
| `LED2` indicator LED | `GP47` | `GP47` drives `LED2` through series resistor `R13`; LED cathode is at `GND`. |
| `SW1` button | `GP38` | Active-low button to `GND` with `R12` pull-up to `3V3`. |
| `SW2` button | `GP39` | Active-low button to `GND` with `R11` pull-up to `3V3`. |
| `BOOT` button | `GPIO0` | Active-low boot strap button; `R4` pulls it up to `3V3`. |
| `RST` button | `CHIP_PU` | Active-low reset path; `R1` pull-up and `C3` to `GND`. |
| `BUZZER1` | `GP21` via `Q2` | `GP21` drives `R19` -> `Q2` base; `Q2` sinks buzzer negative terminal; `R18` is a pulldown. |
| Header `H1` | `VBUS`, `GND`, `3V3`, `GP8`-`GP13` | GPIO breakout header. |
| Header `H2` | `U0TX`, `U0RX`, `GP1`-`GP7` | UART + GPIO breakout header. |
| RF network (`L2`, `C8`, antenna path) | `LNA_IN` | ESP32 RF pin to antenna matching network. |
| Crystal network (`X1`, `C5`, `C6`) | `XTAL_P`, `XTAL_N` | Main crystal connection. |

## 2. Detailed ESP32-to-board map

### 2.1 GPIO and control nets actually used on the board

| ESP32 package pin | ESP32 symbol pin | Board net | Connected module(s) / component(s) | Practical function in this design |
| --- | --- | --- | --- | --- |
| 4 | `CHIP_PU` | `RST` | `RST` button, `R1`, `C3` | Reset / enable line for the ESP32. |
| 5 | `GPIO0` | `BOOT` | `BOOT` button, `R4` | Boot strap / download button. |
| 6 | `GPIO1` | `GP1` | `H2` pin 3 | Breakout GPIO on header `H2`. |
| 7 | `GPIO2` | `GP2` | `H2` pin 4 | Breakout GPIO on header `H2`. |
| 8 | `GPIO3` | `GP3` | `H2` pin 5 | Breakout GPIO on header `H2`. |
| 9 | `GPIO4` | `GP4` | `H2` pin 6 | Breakout GPIO on header `H2`. |
| 10 | `GPIO5` | `GP5` | `H2` pin 7 | Breakout GPIO on header `H2`. |
| 11 | `GPIO6` | `GP6` | `H2` pin 8 | Breakout GPIO on header `H2`. |
| 12 | `GPIO7` | `GP7` | `H2` pin 9 | Breakout GPIO on header `H2`. |
| 13 | `GPIO8` | `GP8` | `H1` pin 9 | Breakout GPIO on header `H1`. |
| 14 | `GPIO9` | `GP9` | `H1` pin 8 | Breakout GPIO on header `H1`. |
| 15 | `GPIO10` | `GP10` | `H1` pin 7 | Breakout GPIO on header `H1`. |
| 16 | `GPIO11` | `GP11` | `H1` pin 6 | Breakout GPIO on header `H1`. |
| 17 | `GPIO12` | `GP12` | `H1` pin 5 | Breakout GPIO on header `H1`. |
| 18 | `GPIO13` | `GP13` | `H1` pin 4 | Breakout GPIO on header `H1`. |
| 19 | `GPIO14` | `GP14` | `U6` pin 14 `RES#` | Display reset line. |
| 21 | `XTAL_32K_P` | `GP15` | `U6` pin 18 `D0`, `U2` pin 8 `SCL`, `R16` pull-up to `3V3` | Shared display/peripheral clock line. |
| 22 | `XTAL_32K_N` | `GP16` | `U6` pins 19 `D1` and 20 `D2`, `U2` pin 3 `SDA`, `R15` pull-up to `3V3` | Shared display/peripheral data line. |
| 23 | `GPIO17` | `GP17` | `R10` -> `Q1` gate, `R9`, `C12`, switched `U2` `VCC` rail | ESP32-controlled power switch for `U2`. |
| 24 | `GPIO18` | `GP18` | `U2` pin 9 `RST` | Reset line for `U2`. |
| 25 | `GPIO19` | `DN` | `USB1` pins `A7`/`B7` | USB D- line. |
| 26 | `GPIO20` | `DP` | `USB1` pins `A6`/`B6` | USB D+ line. |
| 27 | `GPIO21` | `GP21` | `R19` -> `Q2` base, `R18`, `BUZZER1` drive stage | Buzzer control through transistor driver. |
| 36 | `SPICLK_N` | `GP48` | `U8` pin 3 `DI`, `R8` pull-up to `3V3` | WS2812B data line. |
| 37 | `SPICLK_P` | `GP47` | `R13` -> `LED2` anode | Indicator LED drive. |
| 38 | `GPIO33` | `GP33` | No downstream component found | Labeled net only at `U1`. |
| 39 | `GPIO34` | `GP34` | No downstream component found | Labeled net only at `U1`. |
| 40 | `GPIO35` | `GP35` | No downstream component found | Labeled net only at `U1`. |
| 41 | `GPIO36` | `GP36` | No downstream component found | Labeled net only at `U1`. |
| 42 | `GPIO37` | `GP37` | No downstream component found | Labeled net only at `U1`. |
| 43 | `GPIO38` | `GP38` | `SW1`, `R12` | Active-low user button input. |
| 44 | `MTCK` | `GP39` | `SW2`, `R11` | Active-low user button input on the default JTAG clock pad. |
| 45 | `MTDO` | `GP40` | No downstream component found | Labeled net only at `U1`. |
| 47 | `MTDI` | `GP41` | No downstream component found | Labeled net only at `U1`. |
| 48 | `MTMS` | `GP42` | No downstream component found | Labeled net only at `U1`. |
| 49 | `U0TXD` | `U0TX` | `H2` pin 1 | UART0 TX breakout. |
| 50 | `U0RXD` | `U0RX` | `H2` pin 2 | UART0 RX breakout. |
| 51 | `GPIO45` | `GP45` | No downstream component found | Labeled net only at `U1`. |
| 52 | `GPIO46` | `GP46` | No downstream component found | Labeled net only at `U1`. |

### 2.2 Special ESP32 pins that are not general application GPIOs in this schematic

| ESP32 package pin | ESP32 symbol pin | Board net | Connected component(s) | Notes |
| --- | --- | --- | --- | --- |
| 1 | `LNA_IN` | `LNA` | `L2`, `C8`, RF network | RF pin into the antenna matching path. |
| 2 | `VDD3P3` | `VDD3V3` | `L1`, `C11` | Main 3.3 V supply decoupling path. |
| 3 | `VDD3P3` | `VDD3V3` | `L1`, `C11` | Same supply domain as pin 2. |
| 20 | `VDD3P3_RTC` | `3V3` | `U4` output and board 3.3 V rail | RTC-domain 3.3 V supply. |
| 46 | `VDD3P3_CPU` | `3V3` | `U4` output and board 3.3 V rail | CPU-domain 3.3 V supply. |
| 53 | `XTAL_N` | `XN` | `X1`, `C6` | Main crystal pin. |
| 54 | `XTAL_P` | `XP` | `X1`, `C5` | Main crystal pin. |
| 55 | `VDDA` | `3V3` | Board 3.3 V rail | Analog 3.3 V supply. |
| 56 | `VDDA` | `3V3` | Board 3.3 V rail | Analog 3.3 V supply. |
| 57 | `EP` | `GND` | Ground plane / ground symbols | Exposed pad tied to ground. |

### 2.3 ESP32 symbol pins that have no external board net in this schematic

These pins appear in the `U1` symbol but do not connect to a named board net here:

- Pin 28 `SPICS1`
- Pin 29 `VDD_SPI`
- Pin 30 `SPIHD`
- Pin 31 `SPIWP`
- Pin 32 `SPICS0`
- Pin 33 `SPICLK`
- Pin 34 `SPIQ`
- Pin 35 `SPID`

## 3. Breakout header map

### Header `H1`

| `H1` pin | Net | Meaning |
| --- | --- | --- |
| 1 | `VBUS` | USB / board 5 V rail |
| 2 | `GND` | Ground |
| 3 | `3V3` | Regulated 3.3 V rail |
| 4 | `GP13` | ESP32 `GPIO13` |
| 5 | `GP12` | ESP32 `GPIO12` |
| 6 | `GP11` | ESP32 `GPIO11` |
| 7 | `GP10` | ESP32 `GPIO10` |
| 8 | `GP9` | ESP32 `GPIO9` |
| 9 | `GP8` | ESP32 `GPIO8` |

### Header `H2`

| `H2` pin | Net | Meaning |
| --- | --- | --- |
| 1 | `U0TX` | ESP32 UART0 TX (`U0TXD`, GPIO43) |
| 2 | `U0RX` | ESP32 UART0 RX (`U0RXD`, GPIO44) |
| 3 | `GP1` | ESP32 `GPIO1` |
| 4 | `GP2` | ESP32 `GPIO2` |
| 5 | `GP3` | ESP32 `GPIO3` |
| 6 | `GP4` | ESP32 `GPIO4` |
| 7 | `GP5` | ESP32 `GPIO5` |
| 8 | `GP6` | ESP32 `GPIO6` |
| 9 | `GP7` | ESP32 `GPIO7` |

## 4. Board-level observations

- `GP15` and `GP16` are shared between `U6` and `U2`, and both lines have pull-ups (`R16`, `R15`) to `3V3`.
- `GP17` does not feed a module directly; it controls `Q1`, which switches the `U2` supply rail.
- `GP21` also does not feed the buzzer directly; it drives the `Q2` transistor stage through `R19`.
- `GP38` and `GP39` are both button inputs with pull-ups to `3V3` and switches to `GND`.
- `GP33`, `GP34`, `GP35`, `GP36`, `GP37`, `GP40`, `GP41`, `GP42`, `GP45`, and `GP46` have U1-side net labels, but I did not find any downstream component or header connection for them in this schematic.
