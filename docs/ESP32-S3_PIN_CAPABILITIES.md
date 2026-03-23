# ESP32-S3 GPIO / Pin Capabilities Cross-Reference

This file cross-references the board nets from `ESP32-S3-LCD.epro` against ESP32-S3 pin capabilities.

Capability rules used here:
- ESP32-S3 exposes 45 physical GPIOs: `GPIO0`-`GPIO21` and `GPIO26`-`GPIO48`.
- `GPIO1`-`GPIO10` are `ADC1_CH0`-`ADC1_CH9`.
- `GPIO11`-`GPIO20` are `ADC2_CH0`-`ADC2_CH9`.
- `GPIO1`-`GPIO14` are capacitive touch pins `T1`-`T14`.
- `GPIO0`-`GPIO21` are also RTC GPIOs (`RTC_GPIO0`-`RTC_GPIO21`).
- `GPIO19` and `GPIO20` are USB pins by default.
- Strapping pins are `GPIO0`, `GPIO3`, `GPIO45`, and `GPIO46`.
- Default JTAG pads are `GPIO39`=`MTCK`, `GPIO40`=`MTDO`, `GPIO41`=`MTDI`, `GPIO42`=`MTMS`.
- Default UART0 pads are `GPIO43`=`U0TXD` and `GPIO44`=`U0RXD`.
- Most digital peripheral functions (LEDC/PWM, I2C, SPI, UART, I2S, RMT, etc.) can be remapped through the ESP32-S3 GPIO matrix to most free digital GPIOs.

## 1. Per-net capability table

| Board net | ESP32 GPIO / pad | Current board use | Capability highlights | Important caveats |
| --- | --- | --- | --- | --- |
| `BOOT` | `GPIO0` | Boot button | RTC GPIO, digital I/O | Strapping pin. Keep it high during reset for normal boot. |
| `GP1` | `GPIO1` | `H2` pin 3 breakout | `ADC1_CH0`, `T1`, `RTC_GPIO1`, digital I/O | Free on header. |
| `GP2` | `GPIO2` | `H2` pin 4 breakout | `ADC1_CH1`, `T2`, `RTC_GPIO2`, digital I/O | Free on header. |
| `GP3` | `GPIO3` | `H2` pin 5 breakout | `ADC1_CH2`, `T3`, `RTC_GPIO3`, digital I/O | Strapping pin; avoid forcing its level during reset. |
| `GP4` | `GPIO4` | `H2` pin 6 breakout | `ADC1_CH3`, `T4`, `RTC_GPIO4`, digital I/O | Free on header. |
| `GP5` | `GPIO5` | `H2` pin 7 breakout | `ADC1_CH4`, `T5`, `RTC_GPIO5`, digital I/O | Free on header. |
| `GP6` | `GPIO6` | `H2` pin 8 breakout | `ADC1_CH5`, `T6`, `RTC_GPIO6`, digital I/O | Free on header. |
| `GP7` | `GPIO7` | `H2` pin 9 breakout | `ADC1_CH6`, `T7`, `RTC_GPIO7`, digital I/O | Free on header. |
| `GP8` | `GPIO8` | `H1` pin 9 breakout | `ADC1_CH7`, `T8`, `RTC_GPIO8`, digital I/O | Free on header. |
| `GP9` | `GPIO9` | `H1` pin 8 breakout | `ADC1_CH8`, `T9`, `RTC_GPIO9`, digital I/O | Free on header. |
| `GP10` | `GPIO10` | `H1` pin 7 breakout | `ADC1_CH9`, `T10`, `RTC_GPIO10`, digital I/O | Free on header. |
| `GP11` | `GPIO11` | `H1` pin 6 breakout | `ADC2_CH0`, `T11`, `RTC_GPIO11`, digital I/O | Free on header. |
| `GP12` | `GPIO12` | `H1` pin 5 breakout | `ADC2_CH1`, `T12`, `RTC_GPIO12`, digital I/O | Free on header. |
| `GP13` | `GPIO13` | `H1` pin 4 breakout | `ADC2_CH2`, `T13`, `RTC_GPIO13`, digital I/O | Free on header. |
| `GP14` | `GPIO14` | Display reset (`U6 RES#`) | `ADC2_CH3`, `T14`, `RTC_GPIO14`, digital I/O | Already committed to display reset. |
| `GP15` | `GPIO15` on pad `XTAL_32K_P` | Shared `U6 D0` + `U2 SCL` + pull-up | `ADC2_CH4`, `RTC_GPIO15`, digital I/O | Shared bus line. This package pad can also be used for an optional 32 kHz crystal on other designs. |
| `GP16` | `GPIO16` on pad `XTAL_32K_N` | Shared `U6 D1`/`D2` + `U2 SDA` + pull-up | `ADC2_CH5`, `RTC_GPIO16`, digital I/O | Shared bus line. This package pad can also be used for an optional 32 kHz crystal on other designs. |
| `GP17` | `GPIO17` | Controls `Q1` high-side switch for `U2` power | `ADC2_CH6`, `RTC_GPIO17`, digital I/O | Already assigned to a power-control function, not a direct peripheral data line. |
| `GP18` | `GPIO18` | `U2 RST` | `ADC2_CH7`, `RTC_GPIO18`, digital I/O | Already assigned as peripheral reset. |
| `DN` | `GPIO19` | USB D- | `ADC2_CH8`, `RTC_GPIO19`, USB PHY / USB-JTAG default | Reconfiguring it as plain GPIO disables default USB-JTAG / USB function. |
| `DP` | `GPIO20` | USB D+ | `ADC2_CH9`, `RTC_GPIO20`, USB PHY / USB-JTAG default | Reconfiguring it as plain GPIO disables default USB-JTAG / USB function. |
| `GP21` | `GPIO21` | Buzzer driver control | `RTC_GPIO21`, digital I/O | Used through `R19` and transistor `Q2`, not directly at the buzzer. |
| `GP33` | `GPIO33` | Net label only | Digital I/O | No downstream component found. Verify package-specific availability before reusing. |
| `GP34` | `GPIO34` | Net label only | Digital I/O | No downstream component found. Verify package-specific availability before reusing. |
| `GP35` | `GPIO35` | Net label only | Digital I/O | No downstream component found. Verify package-specific availability before reusing. |
| `GP36` | `GPIO36` | Net label only | Digital I/O | No downstream component found. Verify package-specific availability before reusing. |
| `GP37` | `GPIO37` | Net label only | Digital I/O | No downstream component found. Verify package-specific availability before reusing. |
| `GP38` | `GPIO38` | User button `SW1` | Digital I/O | Button input with pull-up `R12`; active-low. |
| `GP39` | `GPIO39` on pad `MTCK` | User button `SW2` | Digital I/O, default JTAG `MTCK` | Button input with pull-up `R11`; active-low; default JTAG clock pad. |
| `GP40` | `GPIO40` on pad `MTDO` | Net label only | Digital I/O, default JTAG `MTDO` | No downstream component found. |
| `GP41` | `GPIO41` on pad `MTDI` | Net label only | Digital I/O, default JTAG `MTDI` | No downstream component found. |
| `GP42` | `GPIO42` on pad `MTMS` | Net label only | Digital I/O, default JTAG `MTMS` | No downstream component found. |
| `U0TX` | `GPIO43` / `U0TXD` | Header `H2` pin 1 | Default UART0 TX, digital I/O | Shared with serial console / flashing workflows if you use UART0 externally. |
| `U0RX` | `GPIO44` / `U0RXD` | Header `H2` pin 2 | Default UART0 RX, digital I/O | Shared with serial console / flashing workflows if you use UART0 externally. |
| `GP45` | `GPIO45` | Net label only | Digital I/O | Strapping pin. Keep external circuitry from forcing the wrong level during reset. |
| `GP46` | `GPIO46` | Net label only | Digital I/O | Strapping pin. Keep external circuitry from forcing the wrong level during reset. |
| `GP47` | `GPIO47` on pad `SPICLK_P` | Indicator LED through `R13` | Digital I/O | Already used as an LED output. |
| `GP48` | `GPIO48` on pad `SPICLK_N` | WS2812 `DI` + pull-up `R8` | Digital I/O | Already used as a timing-sensitive RGB LED data line. |

## 2. Special non-GPIO pads used in this board

| ESP32 pad | Current board use | Why it matters |
| --- | --- | --- |
| `CHIP_PU` | Reset / enable line (`RST` net) | This is not a general GPIO; it must remain valid for reset and power-up behavior. |
| `LNA_IN` | RF matching network to antenna | RF path, not application GPIO. |
| `XTAL_P`, `XTAL_N` | Main crystal `X1` and capacitors `C5`, `C6` | Required for the main clock. |
| `VDD3P3`, `VDD3P3_RTC`, `VDD3P3_CPU`, `VDDA` | 3.3 V rails | Power pins, not GPIO. |
| `EP` | Ground | Exposed pad tied to ground. |

## 3. Board-specific takeaways

- If you need a clean I/O for new hardware, the easiest breakout nets are `GP1`-`GP13` on headers `H1` and `H2`.
- `GP15` and `GP16` are already shared between `U6` and `U2`, and both are pulled up to `3V3`.
- `GP17` and `GP21` are better thought of as control lines for power / actuator stages, not simple spare GPIOs.
- `DN` / `DP` (`GPIO19` / `GPIO20`) are committed to USB.
- `GP38` / `GP39` are already tied to buttons.
- `GP47` / `GP48` are already tied to LEDs.
- `GP45` / `GP46` are not wired elsewhere in the schematic, but because they are strapping pins they are poor choices for anything that might disturb reset behavior.

## 4. Important clarification about `GPIO45` / `GPIO46`

Common pinout charts often treat `GPIO45` and `GPIO46` specially. For this document I followed the official ESP32-S3 GPIO summary from Espressif, which lists all ESP32-S3 pads as usable general-purpose I/O and separately calls out `GPIO45` and `GPIO46` as strapping pins.

So the practical rule for this board is:
- Treat `GP45` and `GP46` as technically usable GPIO nets.
- Still avoid hard-driving them during reset, because they participate in boot strapping.
