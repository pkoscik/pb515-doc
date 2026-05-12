# PocketBook 515 - Complete GPIO Pin Map

All assignments extracted from script.fex. Only pins actually used are listed.

## Port B
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PB00 | I2C0 SCL | twi0_para | AXP209 PMIC |
| PB01 | I2C0 SDA | twi0_para | AXP209 PMIC |
| PB10 | GPIO OUT | lcd0_para | eink VPOS enable |
| PB15 | I2C1 SCL | twi1_para | PCF8563 RTC + encrypt chip |
| PB16 | I2C1 SDA | twi1_para | PCF8563 RTC + encrypt chip |
| PB17 | I2C2 SCL | twi2_para | (no devices on PB515) |
| PB18 | I2C2 SDA | twi2_para | (no devices on PB515) |

## Port C
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PC06 | MMC2 CMD | mmc2_para | Internal SD card |
| PC07 | MMC2 CLK | mmc2_para | Internal SD card |
| PC08 | MMC2 D0 | mmc2_para | Internal SD card |
| PC09 | MMC2 D1 | mmc2_para | Internal SD card |
| PC10 | MMC2 D2 | mmc2_para | Internal SD card |
| PC11 | MMC2 D3 | mmc2_para | Internal SD card |

## Port D - LCD / E-Ink Display
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PD00 | LCD D0 | lcd0_para | 24-bit parallel data |
| PD01 | LCD D1 | lcd0_para | |
| PD02 | LCD D2 / eink RL | lcd0_para | **DUAL USE** - scan direction |
| PD03 | LCD D3 | lcd0_para | |
| PD04 | LCD D4 | lcd0_para | |
| PD05 | LCD D5 | lcd0_para | |
| PD06 | LCD D6 | lcd0_para | |
| PD07 | LCD D7 | lcd0_para | |
| PD08 | LCD D8 | lcd0_para | |
| PD09 | LCD D9 | lcd0_para | |
| PD10 | LCD D10 | lcd0_para | |
| PD11 | LCD D11 | lcd0_para | |
| PD12 | LCD D12 | lcd0_para | |
| PD13 | LCD D13 | lcd0_para | |
| PD14 | LCD D14 / eink UD | lcd0_para | **DUAL USE** - scan direction |
| PD15 | LCD D15 | lcd0_para | |
| PD16 | LCD D16 | lcd0_para | |
| PD17 | LCD D17 | lcd0_para | |
| PD18 | LCD D18 / eink VGG | lcd0_para | **DUAL USE** - gate voltage |
| PD19 | LCD D19 / eink VCOM | lcd0_para | **DUAL USE** - VCOM voltage |
| PD20 | LCD D20 | lcd0_para | |
| PD21 | LCD D21 | lcd0_para | |
| PD22 | LCD D22 | lcd0_para | |
| PD23 | LCD D23 | lcd0_para | |
| PD24 | LCD CLK | lcd0_para | 16 MHz pixel clock |
| PD25 | LCD DE | lcd0_para | Data enable |
| PD26 | LCD HSYNC | lcd0_para | Horizontal sync |
| PD27 | LCD VSYNC | lcd0_para | Vertical sync |

## Port E
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PE00 | GPIO OUT | LED_para | Power LED |
| PE01 | GPIO OUT | lcd0_para | eink main power enable |
| PE03 | GPIO OUT | lcd0_para | eink VNEG enable |
| PE09 | GPIO OUT | lcd0_para | eink VEE enable |
| PE10 | GPIO OUT | encrypt_para_new | Encrypt data (bitbang) |
| PE11 | GPIO OUT | encrypt_para_new | Encrypt CS (bitbang) |

## Port F - Boot0 SD (not used at runtime)
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PF00 | MMC0 D1 | card_boot0_para | Boot ROM SD access only |
| PF01 | MMC0 D0 | card_boot0_para | |
| PF02 | MMC0 CLK | card_boot0_para | |
| PF03 | MMC0 CMD | card_boot0_para | |
| PF04 | MMC0 D3 | card_boot0_para | |
| PF05 | MMC0 D2 | card_boot0_para | |

## Port G
| Pin | Function | Section | Notes |
|-----|----------|---------|-------|
| PG01 | GPIO IN | usbc0 | USB VBUS detect |
| PG02 | GPIO IN | usbc0 | USB ID detect (OTG) |
| PG03 | UART1 TX | uart_para1 | Debug serial console |
| PG04 | UART1 RX | uart_para1 | Debug serial console |
| PG09 | EINT | GPIOKEY_para | Button (KEY_THREE) |
| PG10 | EINT | GPIOKEY_para | Button (KEY_FOUR) |
| PG11 | GPIO OUT | usbc1 | USB1 VBUS drive |
| PG12 | GPIO OUT | usbc0 | USB0 VBUS drive |

## Dual-Use Pin Notes

The PD02/PD14/PD18/PD19 pins serve double duty:
- During frame transmission: LCD data function (func 2)
- During power sequencing: GPIO output function (func 1)

The EPDC driver dynamically switches these pins between functions
using `configure_pins` (visible in epdc.ko strings). This is handled
by writing directly to the A13's PIO configuration registers:
- Port D config register: 0x01C20800 + 0x6C (base + offset for port D)
- Each pin's function is set by a 3-bit field in the config registers

This dual-use is necessary because the A13 doesn't have enough
GPIO pins to dedicate separate pins for power control. The display
data bus is only active during frame transmission, so the pins can
be safely repurposed for GPIO control between frames.
