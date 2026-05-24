# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 02: SERIAL & PARALLEL COMMUNICATION PROTOCOLS                        ║
# ║  The Foundation of All Digital Communication                                ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Serial and parallel communication are the most fundamental building blocks of all digital systems. Every processor, microcontroller, SoC, and FPGA uses some form of serial/parallel interface for basic I/O. These protocols predate networking, operate at the physical/data-link layers, and remain ubiquitous in embedded systems, automotive, industrial, and consumer electronics.

## PURPOSE
Connect processors to peripherals, sensors, memory, and other processors using minimal wiring with well-defined timing and electrical characteristics.

## ENGINEERING PROBLEMS SOLVED
- Simple, low-cost data exchange between ICs
- Sensor reading and actuator control
- Configuration and debug access
- Boot-time communication (before complex stacks are available)
- Low pin-count connectivity (especially serial)

## MODERN RELEVANCE
Despite the rise of high-speed protocols, basic serial (SPI, I2C, UART) remains in EVERY embedded system, vehicle ECU, smartphone, and server BMC. They are the "last mile" connecting processors to the physical world.

## FUTURE DIRECTION
- Higher-speed variants (Quad-SPI → Octal-SPI → HyperBus)
- Integration into multi-protocol PHYs
- Security extensions (encrypted I2C, authenticated SPI)
- Automotive adaptations (PSI5, SENT for sensors)

---

## COMPLETE PROTOCOL FAMILY LIST

| Protocol | Type | Speed | Wires | Topology | Domain | Status |
|----------|------|-------|-------|----------|--------|--------|
| UART/RS-232 | Async Serial | 115.2k – 4 Mbps | 2-3 | P2P | Universal | Active |
| RS-422 | Differential Serial | Up to 10 Mbps | 4 | P2P/Multi-drop | Industrial | Active |
| RS-485 | Differential Serial | Up to 50 Mbps | 2 | Multi-drop bus | Industrial | Active |
| SPI | Sync Serial | 1 – 200 MHz | 4+ | Master-Slave | Embedded | Active |
| Quad-SPI (QSPI) | Sync Serial (4-bit) | Up to 400 MHz | 6 | P2P | Flash/Storage | Active |
| Octal-SPI | Sync Serial (8-bit) | Up to 400 MHz | 11 | P2P | High-speed Flash | Active |
| HyperBus | Sync Parallel-ish | Up to 400 MHz DDR | 13 | P2P | Flash/RAM | Active |
| I2C | Sync Serial | 100k – 3.4 MHz | 2 | Multi-master bus | Sensor/Config | Active |
| I3C | Sync Serial | Up to 12.5 MHz (HDR) | 2 | Multi-master | Next-gen sensor | Growing |
| SMBus | I2C variant | 100k – 1 MHz | 2 | Bus | System mgmt | Active |
| PMBus | SMBus extension | 100-400 kHz | 2 | Bus | Power mgmt | Active |
| 1-Wire | Async Serial | 16 kbps (std) | 1+GND | Bus | iButton/temp | Active |
| SWI (Atmel) | Single-Wire | 230 kbps | 1 | P2P | Crypto auth | Active |
| SDIO | Sync Parallel | Up to 208 MHz | 6 | P2P | SD cards/WiFi | Active |
| Parallel Bus | Sync Parallel | Various | 8-64+ | Bus | Legacy CPU | Declining |
| ISA | Parallel Bus | 8.33 MHz | 62-pin | Bus | Legacy PC | Obsolete |
| Centronics/IEEE 1284 | Parallel | 2 MBps | 25-pin | P2P | Printer | Obsolete |
| PSI5 | Current-loop Serial | 189 kbps | 2 | P2P | Auto sensors | Active |
| SENT (SAE J2716) | Single-wire Serial | Up to 1.5 MHz | 1 | P2P | Auto sensors | Active |
| DSI3 | Differential Serial | 16.6 Mbps | 2 | Bus | Auto sensors | Growing |

---

## UART / RS-232

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Universal Asynchronous Receiver-Transmitter |
| Standard | EIA/TIA-232 (electrical), no fixed framing standard |
| Year | 1960 (RS-232), UART concept from 1960s |
| Creator | EIA (Electronic Industries Association) |
| Layer | Physical + Data Link |
| Topology | Point-to-Point |
| Wires | Tx, Rx, GND (+ optional RTS/CTS/DTR/DSR) |
| Speed | 300 bps to 4+ Mbps |
| Encoding | NRZ, start/stop bit framing |
| Deterministic | No (asynchronous) |
| Real-Time | No (best-effort) |

### Key Characteristics
- **Asynchronous**: No shared clock — both sides agree on baud rate
- **Frame**: [START(1)] [DATA(5-9)] [PARITY(0-1)] [STOP(1-2)]
- **Flow control**: None, XON/XOFF (software), RTS/CTS (hardware)
- **Voltage levels**: RS-232 (±3-15V), TTL (0/3.3V or 0/5V), RS-422 (differential)

### Strengths
- Simplest possible serial: 2 wires, no clock
- Universal: every MCU/SoC has UART
- Debug console standard (Linux kernel, bootloaders)
- No master/slave — peer-to-peer

### Weaknesses
- Low speed (practical limit ~4 Mbps)
- No addressing (point-to-point only)
- Clock drift issues at high baud
- No error correction (only parity)

### Linux Support
- `/dev/ttyS*` (8250/16550 UART), `/dev/ttyUSB*` (USB-serial), `/dev/ttyAMA*` (ARM)
- `drivers/tty/serial/` — all UART drivers
- Console: `console=ttyS0,115200n8` in kernel command line
- SerDev framework for kernel-space UART clients

### Android Support
- Serial console for debug (ADB over serial)
- AAOS: CAN→UART bridges, GPS modules via UART
- `android.hardware.serial` HAL (limited)

### Automotive
- ECU debug console
- GPS/GNSS modules (NMEA over UART)
- Bluetooth modules (HCI-UART, H4/BCSP)
- Legacy telematics

### Semiconductor
- Every SoC has 2-8+ UART peripherals
- First communication during boot (before any complex protocol)
- FPGA soft-UART: trivial to implement

---

## SPI (Serial Peripheral Interface)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Serial Peripheral Interface |
| Standard | De facto (Motorola, 1980s) — no formal standard |
| Year | ~1985 (Motorola 68HC11) |
| Creator | Motorola |
| Layer | Physical + Data Link |
| Topology | Master-Slave (1 master, N slaves with CS) |
| Wires | SCLK, MOSI, MISO, CS (per slave) |
| Speed | 1 MHz – 200+ MHz |
| Encoding | Synchronous, clock-edge triggered |
| Full Duplex | Yes |
| Deterministic | Yes (master-controlled clock) |

### Key Characteristics
- **Synchronous**: Master provides clock
- **Full duplex**: Simultaneous Tx and Rx
- **Modes**: CPOL/CPHA (4 combinations: Mode 0/1/2/3)
- **No addressing**: Hardware CS selects slave
- **No ACK**: Master can't tell if slave exists or is broken

### Variants
| Variant | Data Width | Speed | Use |
|---------|-----------|-------|-----|
| Standard SPI | 1-bit | Up to ~50 MHz | Sensors, configs |
| Dual SPI | 2-bit | Up to ~100 MHz | Flash |
| Quad SPI (QSPI) | 4-bit | Up to ~200 MHz | NOR Flash, boot |
| Octal SPI | 8-bit | Up to ~400 MHz | High-perf flash |
| HyperBus | 8-bit DDR | Up to ~800 MB/s | HyperRAM/Flash |

### Strengths
- Fast (up to 200 MHz practical for standard)
- Full duplex
- Simple hardware (shift register)
- No pull-ups needed (push-pull)
- Deterministic timing

### Weaknesses
- Pin count scales with slaves (1 CS per slave)
- No flow control or error detection
- No multi-master (standard)
- Short distance (PCB-level, <30cm)
- No standard (vendor variations)

### Linux Support
- `drivers/spi/` — SPI master controller drivers
- `struct spi_device`, `struct spi_transfer`
- Userspace: `/dev/spidevX.Y` (spidev)
- DT binding: `spi-max-frequency`, `spi-cpol`, `spi-cpha`

### Semiconductor Usage
- **Flash boot**: Most SoCs boot from SPI NOR flash
- **Sensor reading**: ADC, IMU, DAC
- **FPGA config**: Bitstream loaded via SPI
- **Inter-chip**: Simple data exchange between MCUs

---

## I2C (Inter-Integrated Circuit)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Inter-Integrated Circuit |
| Standard | NXP (Philips) specification, v6.0 (2014) |
| Year | 1982 |
| Creator | Philips Semiconductor (now NXP) |
| Layer | Physical + Data Link |
| Topology | Multi-master bus |
| Wires | SDA (data), SCL (clock) |
| Speed | 100 kHz (Std), 400 kHz (Fast), 1 MHz (Fast+), 3.4 MHz (HS) |
| Addressing | 7-bit (128) or 10-bit (1024) |
| Deterministic | Mostly (clock stretching can delay) |

### Key Characteristics
- **Open-drain**: Requires pull-up resistors
- **Addressing**: Every device has unique 7/10-bit address
- **Multi-master**: Bus arbitration via SDA monitoring
- **ACK/NACK**: Every byte acknowledged by receiver
- **Clock stretching**: Slave can hold SCL low to stall master
- **Frame**: [START] [ADDR(7)+R/W(1)] [ACK] [DATA(8)] [ACK] ... [STOP]

### Strengths
- Only 2 wires for many devices (up to 127 on one bus)
- Multi-master support
- ACK mechanism confirms device presence
- Standard addressing = interoperable
- Low power (open-drain, pull-ups only)

### Weaknesses
- Slow (max 3.4 MHz in HS mode)
- Open-drain: limited by pull-up + capacitance
- Bus capacitance limits (400pF standard)
- Address conflicts possible (limited 7-bit space)
- No error correction

### Linux Support
- `drivers/i2c/` — adapter drivers, algorithm, mux
- `i2c-tools`: `i2cdetect`, `i2cget`, `i2cset`, `i2cdump`
- Userspace: `/dev/i2c-N`
- DT: `i2c@address { device@addr { ... }; };`
- regmap-i2c for register-based devices

### Key Addresses (Common)
| Address | Device |
|---------|--------|
| 0x50-0x57 | EEPROM (AT24Cxx) |
| 0x68/0x69 | IMU (MPU6050, ICM-42688) |
| 0x76/0x77 | Barometer (BME280) |
| 0x3C/0x3D | OLED display (SSD1306) |
| 0x48-0x4F | ADC (ADS1115) |
| 0x20-0x27 | I/O expander (PCF8574) |

---

## I3C (Improved Inter-Integrated Circuit)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Improved Inter-Integrated Circuit |
| Standard | MIPI Alliance I3C (v1.0: 2017, v1.1: 2021) |
| Year | 2017 |
| Creator | MIPI Alliance |
| Layer | Physical + Data Link |
| Topology | Single master + multi-slave (can promote secondary master) |
| Wires | SDA, SCL (same as I2C — backward compatible) |
| Speed | 12.5 MHz (SDR), up to 100 MHz (HDR modes) |
| Addressing | 7-bit dynamic + static |

### Key Improvements Over I2C
- **Much faster**: 12.5 MHz base, HDR up to 100 MHz
- **Push-pull clocking**: SCL driven push-pull (faster edges)
- **Dynamic addressing**: Controller assigns addresses at runtime
- **In-band interrupt**: Slaves can signal controller without extra pin
- **Hot-join**: Devices can join bus after initialization
- **HDR modes**: DDR (double data rate), Ternary (3-level signaling)
- **I2C backward compatible**: I3C bus can include legacy I2C devices

### Linux Support
- `drivers/i3c/` — I3C subsystem (since Linux 4.20)
- I3C controller drivers (Synopsys DW, Cadence, etc.)

---

## RS-485

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | TIA-485 (formerly EIA-485) |
| Standard | TIA/EIA-485-A |
| Year | 1983 |
| Creator | EIA/TIA |
| Layer | Physical only (needs protocol above: Modbus, PROFIBUS, DMX) |
| Topology | Multi-drop bus (up to 32/256 nodes) |
| Wires | A, B (differential pair) + GND |
| Speed | Up to 50 Mbps (at short distance) |
| Distance | Up to 1200m (at low speed) |
| Signaling | Differential (balanced) |

### Key Characteristics
- **Differential**: Immune to common-mode noise
- **Half-duplex** (2-wire) or **full-duplex** (4-wire)
- **Multi-drop**: Up to 32 standard nodes (256 with low-power receivers)
- **Long distance**: Up to 1200m
- **Needs protocol**: RS-485 is ONLY electrical spec — needs Modbus, DMX, etc. on top

### Industrial Importance
- Foundation of: Modbus RTU, PROFIBUS PA, DMX512 (lighting), BACnet MS/TP
- Used in: factories, buildings, energy systems, HVAC
- Rugged: works in noisy industrial environments

### Linux Support
- Standard UART + GPIO for DE (driver enable)
- `drivers/tty/serial/` with `rs485-rts-on-send` DT property
- `struct serial_rs485` ioctl for RS-485 mode configuration

---

## SENT (SAE J2716)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Single Edge Nibble Transmission |
| Standard | SAE J2716 |
| Year | 2008 |
| Creator | SAE International |
| Domain | Automotive sensor communication |
| Topology | Point-to-point (sensor → ECU) |
| Wires | 1 signal + power + GND |
| Speed | Tick-based (up to 1.5 MHz clock tick) |

### Key Characteristics
- **Unidirectional**: Sensor sends to ECU (no commands back in basic)
- **Single-wire**: Data encoded in falling-edge timing (nibbles)
- **Low cost**: Replaces analog signals with digital
- **Automotive grade**: Designed for harsh vehicle environment
- **Nibble encoding**: 4 bits per timing period

### Usage
- Throttle position sensors
- Pressure sensors
- Wheel speed sensors
- Temperature sensors
- Replacing analog 0-5V sensor outputs

---

## PSI5 (Peripheral Sensor Interface 5)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Peripheral Sensor Interface (version 5) |
| Standard | PSI5 Standard (PSI5 Alliance) |
| Year | 2004 |
| Domain | Automotive airbag/crash sensors |
| Topology | Point-to-point or bus |
| Wires | 2 (power + data on same pair, current modulation) |
| Speed | 189 kbps |
| Signaling | Current-mode on power supply line |

### Key Characteristics
- **Current-loop**: Data modulated on power supply current
- **Safety-critical**: Used for crash sensors (airbag deployment)
- **2-wire**: Power + bidirectional data on same pair
- **Manchester encoding**: Self-clocking
- **CRC**: Error detection built in

---

## PARALLEL COMMUNICATION (LEGACY)

### Historical Context
Before serial links became fast enough, parallel buses dominated:

| Bus | Width | Speed | Era | Replacement |
|-----|-------|-------|-----|-------------|
| ISA | 8/16-bit | 8.33 MHz | 1981-1995 | PCI |
| PCI | 32/64-bit | 33/66 MHz | 1992-2004 | PCIe |
| ATA/IDE | 16-bit | 133 MB/s | 1986-2010 | SATA |
| SCSI (parallel) | 8/16-bit | 320 MB/s | 1986-2005 | SAS |
| AGP | 32-bit | 2.1 GB/s | 1997-2004 | PCIe |
| VMEbus | 16/32/64-bit | 320 MB/s | 1981-present | VPX |
| CompactPCI | 32/64-bit | 533 MB/s | 1999-present | CompactPCIe |
| Centronics | 8-bit | 2 MB/s | 1970-2005 | USB |
| IEEE 488 (GPIB) | 8-bit | 8 MB/s | 1975-present | USB-TMC |

### Why Serial Won
1. **Pin count**: Parallel needs N× pins → expensive packaging
2. **Skew**: At high speed, parallel traces must be matched — very difficult
3. **SI**: Cross-talk between parallel traces limits speed
4. **Cost**: Fewer pins = cheaper connectors, PCB, packages
5. **Speed**: SerDes technology allowed single lane > parallel bus speed
6. **Example**: PCIe x1 (1 lane) > PCI (32 parallel bits) in bandwidth

---

## COMPARISON TABLE: BASIC SERIAL PROTOCOLS

| Feature | UART | SPI | I2C | I3C | 1-Wire |
|---------|------|-----|-----|-----|--------|
| Wires | 2 | 4+N | 2 | 2 | 1 |
| Clock | Async | Master | Master | Master | Self |
| Max Speed | ~4M | ~200M | 3.4M | 100M | 16k |
| Multi-device | No | CS select | Address | Address | ROM ID |
| Full duplex | Yes | Yes | No | No | No |
| Pull-ups | No | No | Yes | Push/OD | Yes |
| ACK | No | No | Yes | Yes | Yes |
| Distance | Med | Short | Short | Short | Long |
| Typical use | Debug/GPS | Flash/ADC | Sensors | Sensors | Temp/Auth |
| Linux driver | tty/serial | spi/ | i2c/ | i3c/ | w1/ |

---

## FLASH CARD SET (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | UART wires? | Tx, Rx, GND (min 2 signal) |
| 2 | SPI wires? | SCLK, MOSI, MISO, CS (4 minimum) |
| 3 | I2C wires? | SDA, SCL (2 only) |
| 4 | I2C address bits? | 7 (or 10 extended) |
| 5 | SPI full duplex? | Yes |
| 6 | I2C full duplex? | No (half-duplex) |
| 7 | UART clock? | No shared clock (async, baud rate) |
| 8 | I2C pull-ups? | Yes (open-drain bus) |
| 9 | SPI multi-slave? | Via separate CS lines |
| 10 | I2C multi-master? | Yes (arbitration) |
| 11 | RS-485 advantage? | Differential → long distance, noise immune |
| 12 | RS-485 max distance? | 1200m (at low baud) |
| 13 | QSPI vs SPI? | 4 data lines vs 1 (4× BW) |
| 14 | I3C vs I2C? | Faster (12.5MHz+), push-pull, IBI, hot-join |
| 15 | SENT purpose? | Automotive sensor → ECU (single-wire) |
| 16 | PSI5 purpose? | Airbag crash sensor (current-loop) |
| 17 | SPI clock modes? | 4 (CPOL × CPHA: Mode 0/1/2/3) |
| 18 | I2C START condition? | SDA high→low while SCL high |
| 19 | Linux I2C userspace? | /dev/i2c-N, i2ctools |
| 20 | Why serial beat parallel? | Fewer pins, no skew, cheaper, SerDes faster |

---

## INTERVIEW QUESTIONS (5 KEY)

**Q1: When would you choose SPI over I2C?**
A: Choose SPI when: (1) Speed matters (>1 MHz), (2) Full-duplex needed, (3) Few slaves (CS lines manageable), (4) No need for hot-plug/discovery. Choose I2C when: many devices, only 2 wires available, addressing needed, lower speed OK.

**Q2: Explain I2C bus arbitration.**
A: In multi-master scenario: both masters start transmitting. They monitor SDA — if a master drives SDA high but sees it low (another master pulling it low), it loses arbitration and backs off. The master sending a '0' wins because open-drain AND-wires the bus.

**Q3: Why does RS-485 use differential signaling?**
A: Differential rejects common-mode noise. Both A and B lines pick up the same interference, but the receiver only looks at the DIFFERENCE (A-B). This allows communication over 1200m in electrically noisy industrial environments where single-ended (RS-232) would fail.

**Q4: What is SPI Mode 0 vs Mode 3?**
A: Mode 0: CPOL=0 (idle low), CPHA=0 (sample on rising edge). Mode 3: CPOL=1 (idle high), CPHA=1 (sample on rising edge). Both sample data on the same edge — the difference is idle state and which edge triggers.

**Q5: How does UART achieve synchronization without a clock?**
A: Both sides pre-agree on baud rate. The START bit (high→low transition) signals frame beginning. Receiver uses its internal clock to sample each bit at the center. Accuracy must be within ~2-3% or errors occur. STOP bit(s) return line to idle for next frame detection.

---

END OF PART 02 — SERIAL & PARALLEL COMMUNICATION
