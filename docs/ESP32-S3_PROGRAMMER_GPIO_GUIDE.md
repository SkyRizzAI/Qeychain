# ESP32-S3 Programmer GPIO Guide

This guide is the practical firmware-facing summary of the board in `ESP32-S3-LCD.epro`.

If you are writing code for this board, these are the GPIOs and onboard devices that matter most.

## 1. What is actually onboard

The schematic shows these programmer-relevant onboard parts:

| Onboard part | What it is | How you talk to it |
| --- | --- | --- |
| `U6` | `X096-2864KSWPG01-H30` OLED, SSD1315-based | I2C on `GPIO15`/`GPIO16`, plus reset on `GPIO14` |
| `U2` | `SLS32AIA020X2USON10XTMA4`, identified as Infineon `OPTIGA Trust X` | I2C on `GPIO15`/`GPIO16`, control pins on `GPIO17` and `GPIO18` |
| `SW1` | User button 1 | `GPIO38`, active-low |
| `SW2` | User button 2 | `GPIO39`, active-low |
| `LED2` | Single indicator LED | `GPIO47`, output |
| `U8` | `WS2812B` RGB LED | `GPIO48`, single-wire data |
| `BUZZER1` | Buzzer | `GPIO21`, output through transistor |
| `USB1` | USB-C data | `GPIO19` and `GPIO20` |

Important clarification:
- I did **not** find a normal environmental sensor such as temperature, IMU, light, or humidity in this schematic.
- The extra onboard IC `U2` is a **secure element**, not a sensor.

## 2. GPIOs you should use in code

### 2.1 Main firmware quick reference

| Firmware function | GPIO | Direction / mode | Active level / bus info | Notes |
| --- | --- | --- | --- | --- |
| OLED reset | `GPIO14` | Output | Active-low (`RES#`) | Pulse low, then high before initializing the display. |
| Shared I2C SCL | `GPIO15` | I2C clock | Shared bus | Used by both the OLED and the secure element. |
| Shared I2C SDA | `GPIO16` | I2C data | Shared bus | Used by both the OLED and the secure element. |
| Secure element power control | `GPIO17` | Output | **Likely active-low** | From the schematic, `GPIO17` drives a PMOS high-side switch (`Q1`) for `U2` power. |
| Secure element reset/control | `GPIO18` | Output | Board symbol says `RST` | The symbol labels `U2` pin 9 as `RST`; use this as the module reset/control pin. |
| Buzzer drive | `GPIO21` | Output | Active-high | `GPIO21` drives transistor `Q2`, which switches the buzzer. |
| Button 1 (`SW1`) | `GPIO38` | Input | Pressed = `LOW` | External pull-up already exists on the board (`R12`). |
| Button 2 (`SW2`) | `GPIO39` | Input | Pressed = `LOW` | External pull-up already exists on the board (`R11`). This is also the default JTAG `MTCK` pad. |
| Indicator LED (`LED2`) | `GPIO47` | Output | Active-high | LED anode is driven from `GPIO47` through `R13`. |
| WS2812 RGB LED data | `GPIO48` | Output | NeoPixel/WS2812 data | One-wire timing-sensitive LED data line. |
| USB D- | `GPIO19` | USB | Reserved for USB | Avoid reusing if you want native USB. |
| USB D+ | `GPIO20` | USB | Reserved for USB | Avoid reusing if you want native USB. |
| BOOT button | `GPIO0` | Input | Pressed = `LOW` | Boot strap pin. Do not repurpose casually. |

### 2.2 Shared I2C bus devices

The board has one obvious onboard I2C bus:

| Device | GPIO SCL | GPIO SDA | Extra control pins | Expected 7-bit I2C address | Notes |
| --- | --- | --- | --- | --- | --- |
| OLED `U6` (SSD1315) | `GPIO15` | `GPIO16` | `GPIO14` reset | `0x3C` | Strap pins show the OLED is wired for I2C mode. |
| Secure element `U2` (`OPTIGA Trust X`) | `GPIO15` | `GPIO16` | `GPIO17` power switch, `GPIO18` reset/control | `0x30` | Same I2C bus as the OLED. |

Important bus note:
- `GPIO15` and `GPIO16` already have pull-ups on the board (`R16` and `R15`), so you usually do **not** need extra pull-ups in firmware or on small add-ons.
- In the ESP32 symbol these two pads appear as `XTAL_32K_P` and `XTAL_32K_N`, but on this board they are used as normal `GPIO15` and `GPIO16`.

## 3. What this means for programmers

### Display

Use the onboard OLED as an **I2C SSD1315 display**.

- `SCL` = `GPIO15`
- `SDA` = `GPIO16`
- `RESET` = `GPIO14`
- Expected I2C address = `0x3C`

Why this is safe to assume:
- The display strap pins are `BS0=0`, `BS1=1`, `BS2=0`, which is the SSD1315 I2C mode selection.
- `CS#`, `D/C#`, `R/W#`, and `E/RD#` are tied low in the schematic, matching the fixed serial/I2C wiring style.

### Buttons

The board has two normal user buttons:

- `SW1` -> `GPIO38`
- `SW2` -> `GPIO39`

Both are:
- active-low
- already pulled up to `3V3` by hardware
- best used as plain digital inputs with software debounce

So in code:
- `LOW` = button pressed
- `HIGH` = button released

### Onboard extra module (`U2`)

`U2` is best treated as the board's **security module**, not as a sensor.

Use these pins for it:

- `GPIO15` = I2C `SCL`
- `GPIO16` = I2C `SDA`
- `GPIO17` = module power switch control
- `GPIO18` = module reset/control from the schematic symbol

Practical meaning:
- If you want to talk to the secure element, first make sure its power rail is enabled by the `GPIO17` control line.
- Because `Q1` is drawn as a PMOS high-side switch with a pull-up on the gate, `GPIO17` is **very likely active-low** for power enable.
- The expected I2C address is `0x30`.

### LEDs and buzzer

- `GPIO47` -> simple indicator LED (`HIGH` should turn it on)
- `GPIO48` -> WS2812 RGB LED data
- `GPIO21` -> buzzer drive (`HIGH` should turn it on)

## 4. Recommended firmware aliases

Use names like these in your code:

```c
#define PIN_OLED_RST           14
#define PIN_I2C_SCL            15
#define PIN_I2C_SDA            16
#define PIN_SEC_PWR_EN_N       17
#define PIN_SEC_RST            18
#define PIN_BUZZER             21
#define PIN_BUTTON1            38
#define PIN_BUTTON2            39
#define PIN_STATUS_LED         47
#define PIN_RGB_LED            48

#define OLED_I2C_ADDR          0x3C
#define OPTIGA_I2C_ADDR        0x30
```

Notes:
- I used `_N` in `PIN_SEC_PWR_EN_N` because the power-switch topology indicates **active-low enable**.
- `PIN_SEC_RST` is named generically because the schematic says `RST`, not `RST#`.

## 5. Suggested startup order

For a typical firmware bring-up:

1. Configure `GPIO47`, `GPIO21`, `GPIO17`, `GPIO18`, and `GPIO14` as outputs.
2. Configure `GPIO38` and `GPIO39` as inputs.
3. Enable the secure element power rail through `GPIO17`.
4. Reset the OLED using `GPIO14` (`LOW` pulse, then `HIGH`).
5. Start I2C on `GPIO16`/`GPIO15`.
6. Probe the I2C bus for:
   - OLED at `0x3C`
   - Secure element at `0x30`

## 6. Minimal bring-up example

This is an Arduino-style example, but the pin choices apply to ESP-IDF too:

```cpp
pinMode(PIN_OLED_RST, OUTPUT);
pinMode(PIN_SEC_PWR_EN_N, OUTPUT);
pinMode(PIN_SEC_RST, OUTPUT);
pinMode(PIN_STATUS_LED, OUTPUT);
pinMode(PIN_BUZZER, OUTPUT);

pinMode(PIN_BUTTON1, INPUT);
pinMode(PIN_BUTTON2, INPUT);

digitalWrite(PIN_SEC_PWR_EN_N, LOW);   // likely enables U2 power
digitalWrite(PIN_OLED_RST, LOW);
delay(10);
digitalWrite(PIN_OLED_RST, HIGH);

Wire.begin(PIN_I2C_SDA, PIN_I2C_SCL);
```

## 7. Best pins to leave alone

Avoid repurposing these unless you intentionally want to change board behavior:

- `GPIO19` / `GPIO20`: native USB
- `GPIO0`: BOOT / strapping
- `GPIO39`: default JTAG clock pad and also wired to `SW2`
- `GPIO47`: onboard indicator LED
- `GPIO48`: WS2812 RGB LED
- `GPIO15` / `GPIO16`: shared onboard I2C bus

If you need extra GPIO for your own code, the cleanest breakout pins are still the header pins `GP1`-`GP13`.
