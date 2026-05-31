# I2C — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C | Document: 08 of 08 — Curated Resources + Study Plan
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Document | Source | Access |
|----------|--------|--------|
| **UM10204** — I2C-bus specification and user manual (Rev 7, 2021) | NXP | FREE — https://www.nxp.com/docs/en/user-guide/UM10204.pdf |
| SMBus Specification v3.2 | SMIF / SBS Forum | FREE — http://smbus.org/specs/ |
| PMBus Specification v1.4 | PMBus.org | FREE — https://pmbus.org/specifications |
| MIPI I3C Specification v1.1 | MIPI Alliance | Member access ($$) |

> **Priority**: Read UM10204 first. It's the definitive I2C reference — 64 pages, well-written, free.

---

## 2. BOOKS

### Essential
| Title | Author | Why Read |
|-------|--------|----------|
| **Mastering STM32** (2nd ed.) | Carmine Noviello | Chapters on I2C HAL, DMA, practical examples |
| **Linux Device Drivers** (4th ed.) | Corbet, Rubini, Kroah-Hartman | I2C subsystem chapter, writing client drivers |
| **I2C Bus: From Theory to Practice** | Dominique Paret | THE dedicated I2C book — covers everything |

### Supplementary
| Title | Author | Why Read |
|-------|--------|----------|
| Embedded Systems: Real-Time Interfacing to ARM Cortex-M | Jonathan Valvano | I2C peripheral programming, bare metal |
| Making Embedded Systems | Elecia White | Bus selection, debugging serial protocols |
| The Art of Electronics (3rd ed.) | Horowitz & Hill | Open-drain, pull-up design, signal integrity |
| Linux Kernel Development (3rd ed.) | Robert Love | Kernel driver fundamentals |

---

## 3. DATASHEETS TO STUDY (Learn by Example)

| Device | Why Study This Datasheet |
|--------|-------------------------|
| **AT24C256** (EEPROM) | Page write, ACK polling, 2-byte address |
| **MPU6050** (IMU) | Register map, burst read, WHO_AM_I pattern |
| **LM75 / TMP112** (Temp) | Simple: 2 registers, 9/12-bit conversion |
| **SSD1306** (OLED) | Command vs data modes, large writes |
| **PCA9548A** (Mux) | I2C mux operation, channel selection |
| **PCF8574** (GPIO) | Simplest I2C device — quasi-bidirectional |
| **DS3231** (RTC) | BCD encoding, alarm registers |
| **ADS1115** (ADC) | Config register, alert, conversion timing |
| **BSS138** (MOSFET) | Level shifting circuit reference |

---

## 4. WEBSITES & ONLINE RESOURCES

### Tutorials
| Resource | URL | Content |
|----------|-----|---------|
| NXP I2C Application Notes | nxp.com/products/interfaces/ic-spi-i3c-interface-devices | AN10216 (bus design), AN10441 (Fm+) |
| SparkFun I2C Tutorial | learn.sparkfun.com/tutorials/i2c | Beginner-friendly with waveforms |
| Adafruit I2C Guide | learn.adafruit.com/working-with-i2c-devices | Practical Arduino/Python examples |
| Total Phase Knowledge Base | totalphase.com/support/articles | Protocol analysis, debugging guides |
| kernel.org I2C docs | kernel.org/doc/html/latest/i2c/ | Linux I2C subsystem documentation |

### Reference
| Resource | URL | Content |
|----------|-----|---------|
| I2C Address Database | i2cdevices.org | Community-maintained address list |
| Linux I2C wiki | wiki.kernel.org | Driver writing guides |
| EEVBLOG Forums | eevblog.com/forum | Real-world I2C debugging discussions |

---

## 5. LINUX KERNEL SOURCE FILES

| Path | Purpose |
|------|---------|
| `drivers/i2c/i2c-core-base.c` | I2C framework core |
| `drivers/i2c/i2c-dev.c` | Userspace /dev/i2c-N |
| `drivers/i2c/busses/i2c-designware-core.c` | Synopsys DesignWare controller |
| `drivers/i2c/busses/i2c-bcm2835.c` | Raspberry Pi I2C |
| `drivers/i2c/busses/i2c-qup.c` | Qualcomm QUP legacy |
| `drivers/i2c/busses/i2c-qcom-geni.c` | Qualcomm GENI (newer) |
| `drivers/i2c/busses/i2c-imx.c` | NXP i.MX |
| `drivers/i2c/algos/i2c-algo-bit.c` | Bit-bang algorithm |
| `drivers/i2c/muxes/i2c-mux-pca954x.c` | PCA9548A mux driver |
| `drivers/misc/eeprom/at24.c` | AT24 EEPROM driver (great example) |
| `drivers/hwmon/lm75.c` | LM75 temp sensor (simple example) |
| `include/linux/i2c.h` | I2C API definitions |
| `include/uapi/linux/i2c-dev.h` | Userspace I2C ioctl definitions |

---

## 6. GITHUB REPOSITORIES

| Repository | Purpose |
|-----------|---------|
| `torvalds/linux` (drivers/i2c/) | Official Linux I2C subsystem |
| `raspberrypi/linux` | RPi-specific I2C drivers |
| `STMicroelectronics/STM32CubeF4` | STM32 HAL I2C examples |
| `adafruit/Adafruit_CircuitPython_BusDevice` | Python I2C abstraction |
| `Sensirion/embedded-i2c-sht4x` | Clean embedded I2C driver example |
| `sparkfun/SparkFun_MPU-9250_DMP` | I2C IMU driver (Arduino) |

---

## 7. APPLICATION NOTES

| Document | Source | Topic |
|----------|--------|-------|
| AN10216 | NXP | I2C bus design guidelines & PCB layout |
| AN10441 | NXP | Level shifting techniques for I2C |
| AN11075 | NXP | I2C fast-mode plus (Fm+) considerations |
| AN10658 | NXP | I2C addressing, conflict resolution |
| AN4235 | ST | I2C timing configuration and alternatives |
| AN2824 | ST | STM32 I2C optimized examples |
| SLVA704 | TI | Understanding I2C (excellent overview) |
| SLLA292 | TI | Pull-up resistor calculation guide |

---

## 8. TOOLS & EQUIPMENT

### Logic Analyzers / Protocol Decoders
| Tool | Price | Notes |
|------|-------|-------|
| **Saleae Logic Pro 8** | $399 | Best software, I2C decode built-in |
| **PulseView (sigrok)** | FREE | Open-source, works with cheap analyzers |
| **DSView + DSLogic** | $100 | Decent quality, sigrok-compatible |

### I2C Host Adapters
| Tool | Price | Notes |
|------|-------|-------|
| **Total Phase Aardvark** | $300 | Industry standard: master, slave, monitor |
| **Bus Pirate** | $30 | Open-source, multi-protocol |
| **FT232H breakout** | $15 | USB-to-I2C via libftdi/libmpsse |
| **Raspberry Pi** | $35 | Built-in I2C + i2c-tools |

### Software
| Tool | Purpose |
|------|---------|
| `i2c-tools` | Linux CLI: detect, get, set, dump, transfer |
| `sigrok/PulseView` | Protocol decoding from logic analyzer captures |
| `minicom/picocom` | Serial terminal (for debug UART alongside I2C) |
| `strace` | Trace ioctl calls to /dev/i2c-N |
| `ftrace` | Kernel I2C tracepoints |
| `devmem2` | Direct register access (debug I2C controller) |

---

## 9. VIDEO COURSES & LECTURES

| Source | Title | Notes |
|--------|-------|-------|
| Ben Eater (YouTube) | "How I2C Communication Works" | Excellent visual explanation |
| Phil's Lab (YouTube) | "STM32 I2C Tutorial" | Practical bare-metal setup |
| Neso Academy (YouTube) | I2C Protocol series | Theory-focused, clear |
| Fastbit Embedded | STM32 I2C Masterclass (Udemy) | Comprehensive STM32 HAL I2C |
| Linux Foundation | Linux Kernel I2C subsystem | Official training material |

---

## 10. FOUR-WEEK STUDY PATH

### Week 1: Fundamentals
| Day | Activity | Resource |
|-----|----------|----------|
| 1 | Read UM10204 Sections 1-3 (basics, electrical) | NXP spec (free PDF) |
| 2 | Read UM10204 Sections 4-6 (protocol, timing) | NXP spec |
| 3 | Watch Ben Eater I2C video | YouTube |
| 4 | Flashcard Decks 1-3 (Identity, Addressing, Protocol) | This encyclopedia |
| 5 | Lab 1: i2cdetect scan | Raspberry Pi + sensor |
| 6 | Lab 2: EEPROM read/write | AT24C256 |
| 7 | Review: Cheatsheet + redo failed flashcards | Doc 06 |

### Week 2: Hardware & Implementation
| Day | Activity | Resource |
|-----|----------|----------|
| 8 | Study open-drain, pull-up calculation | Doc 01 Sections 5-6 |
| 9 | Flashcard Decks 4-5 (Electrical, Arbitration) | This encyclopedia |
| 10 | Lab 3: MPU6050 sensor register access | MPU6050 datasheet |
| 11 | Study STM32 I2C registers, clock config | Doc 01 Section 14 |
| 12 | Flashcard Deck 6 (Registers) | This encyclopedia |
| 13 | Lab 4: Multi-device bus | Multiple I2C devices |
| 14 | Practice: Interview Q1-Q25 (Junior) | Doc 03 |

### Week 3: Linux & Advanced
| Day | Activity | Resource |
|-----|----------|----------|
| 15 | Linux I2C subsystem architecture | Doc 01 Section 16 |
| 16 | Read at24.c or lm75.c kernel driver | Linux source |
| 17 | Flashcard Deck 7 (Linux/DT) | This encyclopedia |
| 18 | Lab 6: Write kernel I2C driver | Module template |
| 19 | Lab 7: I2C mux (PCA9548A) | PCA9548A datasheet |
| 20 | Study debugging methodology | Doc 07 debug scenarios |
| 21 | Practice: Interview Q26-Q55 (Mid) | Doc 03 |

### Week 4: Mastery & Integration
| Day | Activity | Resource |
|-----|----------|----------|
| 22 | Lab 5: DMA-based I2C (STM32) | STM32 reference manual |
| 23 | Lab 8: Logic analyzer capture | Saleae/PulseView |
| 24 | Study automotive I2C, Qualcomm QUP | Doc 01 Sections 21-22 |
| 25 | Flashcard Decks 8-10 (Errors, Automotive, Comparisons) | This encyclopedia |
| 26 | Practice: Interview Q56-Q80 (Senior) | Doc 03 |
| 27 | Debug scenarios 1-7 (paper exercise) | Doc 07 |
| 28 | Final review: All flashcard decks + Staff questions | Complete review |

---

## 11. CERTIFICATION & CAREER RELEVANCE

### Where I2C Knowledge is Required
- **Embedded Systems Engineer**: Daily use — sensors, EEPROMs, PMICs
- **Linux Kernel Developer**: I2C driver development, Device Tree
- **Automotive Software Engineer**: AAOS HAL, ECU bring-up
- **Hardware Engineer**: Pull-up design, signal integrity, EMC
- **ASIC/IP Designer**: I2C controller IP verification
- **Test Engineer**: Production test, protocol validation

### Interview Weight
| Level | I2C Depth Expected |
|-------|-------------------|
| Junior | Know basics: wires, address, ACK, pull-ups |
| Mid | Configure, debug NACK, Device Tree, i2c-tools |
| Senior | Design bus architecture, recovery, timing calc, Linux driver |
| Staff | Full system design, automotive qual, I3C migration, security |

---

## 12. QUICK COMPARISON MATRIX (Protocol Selection)

| Need | Choose | Why |
|------|--------|-----|
| Many sensors, few pins | **I2C** | 2 wires, software addressing |
| High-speed data (>1MHz) | **SPI** | Full-duplex, 200MHz+ possible |
| Long distance (>5m) | **CAN/RS-485** | Differential, noise immune |
| Full-duplex debug | **UART** | Simple, no clock needed |
| Low pin + high speed | **I3C** | 2 wires, 12.5 MHz |
| One wire, few devices | **1-Wire** | Single wire + power |
| Streaming audio | **I2S** | Designed for audio clocking |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH

═══════════════════════════════════════════════════════
 I2C ENCYCLOPEDIA COMPLETE ✅ (8/8 documents)
═══════════════════════════════════════════════════════
