# SPI — REFERENCES & LEARNING RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 08 of 08 — A-Z Resource Guide
# ════════════════════════════════════════════════════════════════════

---

# 1. STANDARDS & SPECIFICATIONS

| Document | Publisher | Notes |
|----------|-----------|-------|
| Motorola 68HC11 Reference Manual | Motorola/NXP | Original SPI definition |
| JEDEC JESD251 (xSPI) | JEDEC | Standardized high-speed SPI for Flash |
| JEDEC JESD216 (SFDP) | JEDEC | Serial Flash Discoverable Parameters |
| TI 16550D Datasheet | TI | SPI timing reference patterns |
| W25Q128JV Datasheet | Winbond | Most popular SPI NOR Flash — excellent command reference |
| IS25LP128 Datasheet | ISSI | Alternative NOR Flash with clear timing diagrams |

**Note:** There is no single "SPI standard." Vendor datasheets ARE the specification.

---

# 2. BOOKS

## Essential

| Book | Author | Why |
|------|--------|-----|
| **Mastering STM32** | Carmine Noviello | Complete SPI chapters: polling, IRQ, DMA, Flash access |
| **The Definitive Guide to ARM Cortex-M3/M4** | Joseph Yiu | SPI peripheral architecture on ARM |
| **Embedded Systems: Real-Time Interfacing** | Jonathan Valvano | SPI labs with TM4C, hands-on exercises |
| **Linux Device Drivers, 3rd Ed** | Corbet, Rubini, Kroah-Hartman | Kernel SPI/char driver fundamentals |
| **The Art of Electronics, 3rd Ed** | Horowitz & Hill | Signal integrity, level shifting, practical circuits |

## Advanced

| Book | Author | Why |
|------|--------|-----|
| **Linux Kernel Development** | Robert Love | IRQ handling, DMA — for SPI driver understanding |
| **Designing Embedded Hardware** | John Catsoulis | SPI hardware design, PCB considerations |
| **High-Speed Digital Design** | Howard Johnson | Signal integrity for high-speed SPI (>25 MHz) |
| **Automotive Embedded Systems Handbook** | Nicolas Navet | SPI in automotive context |

---

# 3. WEBSITES & TUTORIALS

## Deep-Dive Tutorials

| Site | URL | Content |
|------|-----|---------|
| **DeepBlueMbedded** | https://deepbluembedded.com/spi-tutorial-with-stm32/ | Best STM32 SPI tutorial (all modes + DMA) |
| **Sparkfun SPI Tutorial** | https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi | Visual beginner guide with diagrams |
| **Analog Devices SPI Guide** | https://www.analog.com/en/resources/analog-dialogue/articles/introduction-to-spi-interface.html | Hardware-focused explanation |
| **ControllersTech** | https://controllerstech.com/spi-using-registers-in-stm32/ | Register-level STM32 SPI |
| **Bootlin Elixir** | https://elixir.bootlin.com/linux/latest/source/drivers/spi | Browse Linux SPI source |
| **Phil's Lab** | https://youtube.com/@PhilsLab | SPI PCB design, signal integrity |
| **Ben Eater** | https://youtube.com/@BenEater | Digital logic fundamentals (shift registers) |

## Quick References

| Resource | URL | Use |
|----------|-----|-----|
| **Wikipedia: SPI** | https://en.wikipedia.org/wiki/Serial_Peripheral_Interface | Overview, history |
| **man7.org spidev** | https://man7.org/linux/man-pages/man4/spidev.4.html | Linux spidev docs |
| **Kernel SPI docs** | https://docs.kernel.org/spi/ | Official kernel SPI documentation |

## Forums

| Platform | URL | Best For |
|----------|-----|----------|
| **EE Stack Exchange** | https://electronics.stackexchange.com/questions/tagged/spi | Hardware questions |
| **Stack Overflow** | https://stackoverflow.com/questions/tagged/spi | Software/driver questions |
| **STM32 Community** | https://community.st.com | STM32 SPI specifics |
| **Linux Kernel ML** | https://lore.kernel.org/linux-spi/ | SPI driver development |

---

# 4. GITHUB REPOSITORIES

## Educational

| Repo | URL | Description |
|------|-----|-------------|
| **libopencm3** | https://github.com/libopencm3/libopencm3 | Open-source ARM firmware lib — clean SPI examples |
| **STM32CubeF4** | https://github.com/STMicroelectronics/STM32CubeF4 | Official STM32 HAL SPI examples |
| **stm32-spi-flash** | Search GitHub | Numerous SPI NOR Flash driver implementations |
| **linux/drivers/spi/** | https://elixir.bootlin.com/linux/latest/source/drivers/spi | Linux SPI core + all controller drivers |
| **linux/drivers/mtd/spi-nor/** | https://elixir.bootlin.com/linux/latest/source/drivers/mtd/spi-nor | SPI NOR Flash framework |

## Tools

| Tool | URL | Description |
|------|-----|-------------|
| **flashrom** | https://github.com/flashrom/flashrom | Universal Flash programmer — supports SPI Flash |
| **spi-tools** | https://github.com/cpb-/spi-tools | Linux spi-config + spi-pipe CLI utilities |
| **sigrok/PulseView** | https://github.com/sigrokproject/libsigrokdecode | SPI protocol decoder |
| **Serial Studio** | https://github.com/Serial-Studio/Serial-Studio | Real-time data visualization |
| **pyserial + spidev** | https://pypi.org/project/spidev/ | Python SPI access for rapid prototyping |

## Embedded Frameworks with SPI

| Framework | URL | Notes |
|-----------|-----|-------|
| **Zephyr RTOS** | https://github.com/zephyrproject-rtos/zephyr | drivers/spi/ — async SPI API |
| **FreeRTOS** | https://github.com/FreeRTOS/FreeRTOS | SPI with task notification |
| **Mbed OS** | https://github.com/ARMmbed/mbed-os | SPI class with DMA support |
| **RIOT OS** | https://github.com/RIOT-OS/RIOT | Multi-platform SPI driver |
| **Apache NuttX** | https://github.com/apache/nuttx | POSIX-like SPI interface |

---

# 5. APPLICATION NOTES

| Vendor | Document | Topic |
|--------|----------|-------|
| ST | AN4286 | SPI communication with STM32 |
| ST | AN5543 | OCTOSPI on STM32H7 |
| TI | SPRA861 | SPI protocol overview and applications |
| NXP | AN11643 | SPI slave implementation on LPC |
| Microchip | AN937 | SPI communication for PIC |
| Winbond | W25Q128JV datasheet | De facto SPI Flash command reference |
| Qualcomm | 80-PK177-1 | QUP SPI programming guide (limited access) |

---

# 6. TOOLS

## Hardware

| Tool | Price | Use |
|------|-------|-----|
| Saleae Logic 8 | $400 | SPI protocol decode + timing |
| FX2 clone + PulseView | $15 | Budget SPI analyzer |
| Bus Pirate | $30 | SPI master from command line |
| FTDI FT2232H breakout | $20 | USB-SPI bridge |
| Total Phase Aardvark | $300 | Professional SPI analyzer |
| Oscilloscope (Rigol) | $400 | Signal integrity at high speed |
| SPI NOR Flash breakout | $5 | Practice target |

## Software

| Tool | Platform | Use |
|------|----------|-----|
| flashrom | Linux/Win | Read/write SPI Flash |
| spi-tools | Linux | CLI SPI access |
| PulseView | Cross-platform | SPI protocol analysis |
| spidev + Python | Linux | Rapid prototyping |
| STM32CubeMX | Cross-platform | SPI configuration GUI |
| Saleae Logic 2 | Cross-platform | Professional SPI decode |

---

# 7. STUDY PATH

## Week 1: Fundamentals
1. Read: Sparkfun SPI tutorial
2. Read: W25Q128JV datasheet (focus: commands, timing)
3. Study: CPOL/CPHA modes — draw timing diagrams from memory
4. Lab: Loopback test + JEDEC ID read

## Week 2: Embedded Programming
5. Read: "Mastering STM32" SPI chapters
6. Study: libopencm3 SPI examples
7. Lab: Flash read/write/erase cycle
8. Lab: Multi-slave bus management

## Week 3: Advanced & Linux
9. Read: Linux kernel SPI documentation
10. Study: spi.c, spidev.c, spi-nor core source
11. Lab: Linux spidev userspace programming
12. Lab: DMA-based SPI Flash access

## Week 4: Automotive & High-Speed
13. Study: QSPI/OSPI/xSPI concepts
14. Read: JEDEC JESD251 overview
15. Lab: QSPI Flash on automotive platform
16. Review: AUTOSAR Spi Handler/Driver architecture

---

# 8. QUICK REFERENCE CARD

```
╔══════════════════════════════════════════════════════════╗
║                   SPI QUICK CARD                         ║
╠══════════════════════════════════════════════════════════╣
║ Wires: SCLK + MOSI + MISO + CS̄ (active LOW)            ║
║ Speed: 1–200+ MHz | Overhead: 0%                        ║
║ Mode 0: CPOL=0,CPHA=0 (sample rising) ← 80% of devices ║
║ Mode 3: CPOL=1,CPHA=1 (sample rising) ← 15% of devices ║
║ Transfer = simultaneous exchange (send dummy to read)    ║
║ CS̄ LOW to select, HIGH to deselect                      ║
║ Flash ID: CS̄↓ [0x9F] → [MFR][TYPE][CAP] CS̄↑            ║
║ Clock: SPI_CLK = APB_CLK / 2^(BR+1) (STM32)            ║
║ Linux: /dev/spidev0.0 + ioctl(SPI_IOC_MESSAGE)         ║
║ Debug: send 0x55, check pattern on scope/LA             ║
╚══════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES
