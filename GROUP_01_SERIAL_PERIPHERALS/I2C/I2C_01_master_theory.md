# I2C — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C (Inter-Integrated Circuit) | Document: 01 of 08
# Depth: Encyclopedia-grade | Domain: Embedded + Automotive + Android
# ════════════════════════════════════════════════════════════════════

---

# SECTION 1: METADATA & IDENTITY

| Field | Detail |
|-------|--------|
| Full Name | Inter-Integrated Circuit (I²C, IIC) |
| Pronunciation | "I-squared-C" or "I-two-C" |
| Creator | Philips Semiconductor (now NXP Semiconductors) |
| Year | 1982 (original), standardized 1992 |
| Current Spec | UM10204 Rev 7.0 (October 2021) — FREE from NXP |
| OSI Layer | Layer 1 (Physical) + Layer 2 (Data Link) |
| Topology | Multi-master, multi-slave, shared 2-wire bus |
| Wires | 2: SDA (Serial Data) + SCL (Serial Clock) |
| Speed Modes | Standard (100 kHz), Fast (400 kHz), Fast+ (1 MHz), High-Speed (3.4 MHz), Ultra-Fast (5 MHz) |
| Duplex | Half-duplex (single bidirectional data line) |
| Addressing | 7-bit (standard) or 10-bit (extended) |
| Error Detection | ACK/NACK after every byte |
| Max Devices | 112 usable (7-bit) or 1008 (10-bit) |
| Bus Capacitance | Max 400 pF (standard/fast mode) |
| Power | Open-drain with external pull-ups; 1.8V, 2.5V, 3.3V, 5V |

---

# SECTION 2: HISTORY & EVOLUTION

## Timeline

| Year | Event |
|------|-------|
| 1982 | Philips develops I2C for inter-chip communication in TVs |
| 1992 | Version 1.0 published — 100 kHz standard mode, 7-bit addressing |
| 1998 | Version 2.0 — Fast mode (400 kHz), 10-bit addressing added |
| 2000 | Version 2.1 — High-Speed mode (3.4 MHz) |
| 2007 | NXP (formerly Philips Semi) releases I2C patent-free |
| 2012 | Version 4.0 — Fast-mode Plus (Fm+, 1 MHz) |
| 2012 | Version 5.0 — Ultra-Fast mode (UFm, 5 MHz, push-pull, unidirectional) |
| 2014 | Version 6.0 — Device ID, clarifications |
| 2021 | UM10204 Rev 7.0 — current specification |

## Why I2C Was Created
Philips needed a simple, low-pin-count bus to connect CPUs, EEPROMs, ADCs, and I/O expanders inside TV sets. Requirements: (1) only 2 wires, (2) support many devices, (3) software addressing, (4) multi-master capable. I2C met all these with elegant simplicity.

## SMBus — The Intel Cousin
In 1995, Intel defined **SMBus** (System Management Bus) based on I2C:
- Stricter timing (min clock 10 kHz, max 100 kHz)
- Timeout: 35ms — prevents stuck bus
- PEC (Packet Error Checking) — CRC-8 error detection
- Alert line (SMBALERT#) for slave-initiated interrupts
- Standardized device protocols (battery, temperature, voltage)
- Used in PCs: DIMM SPD, fan control, voltage monitoring

| Feature | I2C | SMBus |
|---------|-----|-------|
| Min clock | No minimum (can be static) | 10 kHz |
| Max clock | 3.4 MHz (HS) | 100 kHz (1.x), 1 MHz (3.x) |
| Timeout | None (can hang forever!) | 35 ms |
| Error check | ACK only | ACK + PEC (CRC-8) |
| Alert | No | SMBALERT# |
| Voltage | 1.8V–5V | 1.8V–5V (tighter specs) |

## PMBus — Power Management
PMBus extends SMBus for power supply control:
- Standardized commands for voltage/current/temp monitoring
- Used in server power supplies, VRMs
- I2C-compatible at physical layer

---

# SECTION 3: PHILOSOPHY & DESIGN PRINCIPLES

## Core Design Philosophy
I2C maximizes device count while minimizing wire count:

1. **Two wires for everything** — SDA (data) + SCL (clock) connect all devices
2. **Software addressing** — 7/10-bit addresses eliminate per-device CS̄ pins
3. **Open-drain bus** — wired-AND allows multi-master without bus contention damage
4. **ACK per byte** — immediate error feedback (unlike SPI)
5. **Multi-master** — any device can initiate communication with arbitration
6. **Clock stretching** — slow slave can hold SCL LOW to pause master

## The Open-Drain Genius

This is I2C's most important electrical concept:

```
VDD ─────┬───────────┬───────────┐
         │           │           │
        [Rp]        [Rp]        │ Rp = Pull-up resistor
         │           │           │
SDA ─────┼───────────┼───────────┼──── (all devices share)
         │           │           │
      ┌──┴──┐     ┌──┴──┐    ┌──┴──┐
      │Dev A│     │Dev B│    │Dev C│
      │ OD  │     │ OD  │   │ OD  │   OD = Open-Drain output
      └──┬──┘     └──┬──┘   └──┬──┘
         │           │          │
GND ─────┴───────────┴──────────┴────
```

**How it works:**
- Device can only pull line LOW (connect to GND through MOSFET)
- Device RELEASES line to go HIGH (pull-up resistor pulls it up)
- If ANY device pulls LOW, line goes LOW (wired-AND)
- No device can force HIGH against another pulling LOW → **no bus contention damage**
- This is why I2C is multi-master safe, unlike SPI

## When to Use I2C

| Use I2C When | Don't Use I2C When |
|-------------|-------------------|
| Many devices, few pins | Need high speed (>5 MHz) |
| Low/medium speed ok (<1 MHz typical) | Need full-duplex |
| Need multi-master | Need deterministic latency |
| Need ACK/error detection | Need streaming data (audio, video) |
| Board-level sensor network | Long distance (>1–2 m) |
| Configuration/status registers | Need >400 pF bus capacitance |
| EEPROM, RTC, temp sensor, PMIC | Need guaranteed real-time response |

---

# SECTION 4: PROTOCOL FAMILY TREE

```
Serial Protocols
├── Synchronous (clock present)
│   ├── SPI (4-wire, master-slave, fastest)
│   ├── I2C ← THIS DOCUMENT
│   │   ├── Standard I2C (Philips/NXP UM10204)
│   │   ├── SMBus (Intel, stricter I2C subset)
│   │   │   └── PMBus (power management extension)
│   │   ├── TWI (Two-Wire Interface — Atmel's I2C clone)
│   │   ├── SCCB (Serial Camera Control Bus — OmniVision, I2C-like)
│   │   └── DDC (Display Data Channel — HDMI/VGA, I2C-based)
│   ├── I2S (audio, SPI-variant)
│   └── JTAG/SWD (debug)
└── Asynchronous (no clock)
    ├── UART
    └── 1-Wire (Dallas/Maxim)
```

## I2C Variants and Cousins

| Variant | Relationship | Key Difference |
|---------|-------------|----------------|
| **TWI** | Identical to I2C | Atmel's name to avoid Philips trademark (pre-patent expiry) |
| **SMBus** | Subset with additions | Timeout, PEC, alert; stricter timing |
| **PMBus** | Extension of SMBus | Power supply control commands |
| **SCCB** | I2C-like | Camera sensors, no clock stretching, 2-phase write |
| **DDC** | I2C at 100 kHz | Monitor EDID data over HDMI/DVI/VGA |
| **MIPI I3C** | I2C successor | Backward compatible, up to 12.5 MHz, push-pull, in-band interrupt |

---

# SECTION 5: PHYSICAL LAYER DEEP DIVE

## Electrical Characteristics

### Open-Drain / Open-Collector
- Both SDA and SCL are **open-drain** (MOSFET) or **open-collector** (BJT)
- Each line needs an **external pull-up resistor** to VDD
- Device drives LOW = active (MOSFET on)
- Device releases = passive HIGH (pull-up resistor)

### Voltage Levels

| VDD | V_IL (max) | V_IH (min) | Note |
|-----|-----------|-----------|------|
| 5V | 1.5V | 3.0V | Classic |
| 3.3V | 0.99V | 2.31V | Modern standard |
| 1.8V | 0.54V | 1.26V | Low-power |

- V_OL (output LOW): max 0.4V at specified sink current
- No V_OH spec — pull-up resistor determines HIGH level

### Pull-Up Resistor Selection — THE Critical Design Decision

```
              VDD
               │
              [Rp]  ← Pull-up resistor
               │
    SDA/SCL ───┼─── Total bus capacitance (Cb)
               │
            [Device]
               │
              GND
```

**Rp_min (fastest rise time, limited by sink current):**
```
Rp_min = (VDD - V_OL) / I_OL
       = (3.3 - 0.4) / 3mA = 967Ω → use 1KΩ minimum
```

**Rp_max (slowest acceptable rise time, limited by capacitance):**
```
Rp_max = t_rise / (0.8473 × Cb)

Standard mode (1µs rise): Rp_max = 1µs / (0.8473 × 200pF) = 5.9 KΩ
Fast mode (300ns rise):    Rp_max = 300ns / (0.8473 × 200pF) = 1.77 KΩ
Fast+ mode (120ns rise):  Rp_max = 120ns / (0.8473 × 200pF) = 708Ω
```

### Common Pull-Up Values

| Mode | Bus Cap ~50pF | Bus Cap ~200pF | Bus Cap ~400pF |
|------|-------------|---------------|---------------|
| Standard (100k) | 10KΩ | 4.7KΩ | 2.2KΩ |
| Fast (400k) | 4.7KΩ | 2.2KΩ | 1KΩ |
| Fast+ (1M) | 2.2KΩ | 1KΩ | 470Ω |

> **Rule of Thumb**: 4.7KΩ works for most standard/fast mode applications with <200pF bus capacitance.

### Bus Capacitance Budget
```
Max total: 400 pF (standard/fast mode)
Per device: 5–10 pF (I/O pin)
Per cm trace: ~1 pF (FR4 PCB)
Per connector: 2–5 pF
Practical limit: ~20-30 devices on short bus
```

### Level Shifting
I2C open-drain makes level shifting elegant:

```
   3.3V                    5V
    │                       │
   [Rp]                   [Rp]
    │     MOSFET (BSS138)   │
    ├──── G ─── S ──────────┤
    │     │     │            │
  SDA_3V3 │   SDA_5V         │
    │     D ←── D            │
    │     (bidirectional)    │
```
The MOSFET's body diode + gate bias enables automatic bidirectional level shifting. This is why I2C is so easy to level-shift compared to SPI.

---

# SECTION 6: PROTOCOL — START, STOP, DATA, ACK

## Bus States

| Condition | SDA | SCL | Meaning |
|-----------|-----|-----|---------|
| **Idle** | HIGH | HIGH | Bus free |
| **START (S)** | HIGH→LOW | HIGH | Master initiates |
| **STOP (P)** | LOW→HIGH | HIGH | Master terminates |
| **Data bit** | Stable | LOW→HIGH→LOW | Bit transferred |
| **ACK** | LOW (by receiver) | HIGH pulse | Byte acknowledged |
| **NACK** | HIGH (by receiver) | HIGH pulse | Not acknowledged |

### Critical Rule: SDA changes only when SCL is LOW
Exception: START and STOP conditions — SDA changes while SCL is HIGH.

## START Condition (S)
```
SDA  ‾‾‾‾\______    ← SDA goes LOW while SCL is HIGH
SCL  ‾‾‾‾‾‾‾‾\__    ← then SCL goes LOW
```
This unique pattern signals all devices: "New transaction beginning!"

## STOP Condition (P)
```
SCL  _____/‾‾‾‾‾    ← SCL goes HIGH first
SDA  _________/‾‾   ← then SDA goes HIGH while SCL is HIGH
```

## Repeated START (Sr)
```
SDA  ___/‾‾\_____    ← SDA goes HIGH, then LOW while SCL HIGH
SCL  ___/‾‾‾‾\__    ← SCL goes HIGH, stays HIGH during SDA transition
```
Used for: combined write-then-read without releasing bus (critical for register reads).

## Data Transfer
```
     S  A6 A5 A4 A3 A2 A1 A0 RW ACK D7 D6 D5 D4 D3 D2 D1 D0 ACK  P
SDA  ‾\_|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__/‾
SCL  ‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾‾
      ↑                          ↑  ↑                           ↑  ↑
    START   address byte + R/W  ACK    data byte               ACK STOP
```

## Byte Format
- **8 bits** transmitted MSB first
- **9th clock** = ACK/NACK from receiver
  - ACK: receiver pulls SDA LOW → "got it"
  - NACK: receiver leaves SDA HIGH → "problem" or "last byte"
- Master generates ALL clock pulses (including ACK clock)

---

# SECTION 7: ADDRESS FRAME

## 7-Bit Addressing
```
Byte 1 (after START):
  [A6][A5][A4][A3][A2][A1][A0][R/W̄]
   ↑────── 7-bit address ──────↑  ↑
                                  0 = Write
                                  1 = Read
```

### Reserved Addresses

| Address | Binary | Purpose |
|---------|--------|---------|
| 0x00 | 0000 000 | General Call |
| 0x01 | 0000 001 | CBUS address |
| 0x02 | 0000 010 | Reserved for different bus |
| 0x03 | 0000 011 | Reserved for future |
| 0x04–0x07 | 0000 1xx | HS mode master code |
| 0x78–0x7B | 1111 0xx | 10-bit addressing prefix |
| 0x7C–0x7F | 1111 1xx | Reserved (Device ID, etc.) |

**Usable addresses: 0x08–0x77 = 112 addresses**

### Common Device Addresses (Memorize These!)

| Device Type | Address | Configurable Bits |
|------------|---------|-------------------|
| EEPROM (24Cxx) | 0x50–0x57 | A2, A1, A0 pins → 8 devices |
| Temp sensor (LM75) | 0x48–0x4F | A2, A1, A0 pins |
| RTC (DS3231) | 0x68 | Fixed |
| IMU (MPU6050) | 0x68 or 0x69 | AD0 pin |
| Accelerometer (ADXL345) | 0x1D or 0x53 | SDO/ALT pin |
| OLED display (SSD1306) | 0x3C or 0x3D | SA0 pin |
| GPIO expander (PCF8574) | 0x20–0x27 | A2, A1, A0 |
| ADC (ADS1115) | 0x48–0x4B | ADDR pin |
| DAC (MCP4725) | 0x60–0x67 | A2, A1, A0 |
| PMIC (various) | Vendor-specific | Usually fixed |
| Audio codec (WM8731) | 0x1A or 0x1B | CSB pin |
| Touch controller | 0x38–0x3F | Vendor-specific |

### Address Collision Problem
If two different devices have the same address: **I2C address conflict**. Solutions:
1. Use device's address pin(s) to change one address
2. Use I2C multiplexer (PCA9548A: 1:8 mux)
3. Use I2C switch (PCA9546A)
4. Use different I2C bus

## 10-Bit Addressing
```
Byte 1: [1][1][1][1][0][A9][A8][R/W̄]   (5-bit prefix + 2 MSB of address)
Byte 2: [A7][A6][A5][A4][A3][A2][A1][A0] (8 LSB of address)
```
- Extends address space to 1024 devices
- Backward compatible with 7-bit (different prefix)
- Rarely used in practice

---

# SECTION 8: READ & WRITE TRANSACTIONS

## Write Transaction (Master → Slave)
```
S [Addr+W] [ACK] [Reg]  [ACK] [Data] [ACK] P
  ←master→ ←slv→ ←mst→ ←slv→ ←mst→  ←slv→
```

Example: Write 0x42 to register 0x0A of device 0x68:
```
S [0xD0]  [ACK] [0x0A] [ACK] [0x42] [ACK] P
   0x68<<1|0=0xD0
```

## Read Transaction (Slave → Master)
```
S [Addr+W] [ACK] [Reg]  [ACK] Sr [Addr+R] [ACK] [Data] [NACK] P
  ←master→ ←slv→ ←mst→ ←slv→    ←master→ ←slv→ ←slv→  ←mst→
```

**Why Repeated START (Sr)?** The slave needs to know WHICH register to read. So master first WRITES the register address, then does a Repeated START (without STOP) to switch to READ mode. This prevents another master from grabbing the bus between write and read.

Example: Read register 0x0A from device 0x68:
```
S [0xD0] [ACK] [0x0A] [ACK] Sr [0xD1] [ACK] [Data] [NACK] P
  Write phase (set register pointer)    Read phase
```

## Multi-Byte Read
```
S [Addr+W] [ACK] [Reg] [ACK] Sr [Addr+R] [ACK] [D0] [ACK] [D1] [ACK] [D2] [NACK] P
                                                  ↑ master ACKs     ↑ master NACKs
                                             (more bytes please)   (last byte, stop)
```

**Key Rule:** Master sends ACK for all bytes except the LAST one, where it sends NACK to tell slave "stop sending."

## General Call (Broadcast)
```
S [0x00] [ACK] [Command] [ACK] P
```
Address 0x00 with W bit = general call. All slaves that support it respond. Used for: software reset (0x06), address programming.

---

# SECTION 9: ARBITRATION — MULTI-MASTER MAGIC

## How Arbitration Works

When two masters start simultaneously:
1. Both drive SDA — open-drain means wired-AND
2. If master A sends HIGH but reads LOW → another master is sending LOW → master A LOST
3. Loser backs off, winner continues unaware
4. **No data corruption** — winner's data is intact
5. **No bus damage** — open-drain prevents electrical conflict

```
Master A sends: 1 1 0 1 0 0 ...
Master B sends: 1 1 0 1 1 ...  ← Master B loses here!
Bus (wired-AND):1 1 0 1 0 ...  ← Master A wins
                          ↑
            Master B reads 0, sent 1 → LOST → backs off
```

## Arbitration Rules
1. Arbitration happens **bit by bit** during address phase
2. Master sending LOW always wins over master sending HIGH (wired-AND)
3. Lower address has inherent priority (more zeros early = wins)
4. Loser must wait for STOP, then retry
5. Arbitration can also happen during data phase (if same address) — rare
6. Arbitration is NON-destructive — winning transaction is not affected

## Clock Synchronization (Multi-Master)
When multiple masters drive SCL:
- SCL goes LOW when ANY master pulls it LOW (wired-AND)
- SCL goes HIGH only when ALL masters release it
- This naturally synchronizes clocks to the slowest master

---

# SECTION 10: CLOCK STRETCHING

## Concept
A **slave** can hold SCL LOW to pause the master when it needs more time to process data.

```
Normal:     SCL  _/‾\_/‾\_/‾\_/‾\_
                  clk1 clk2 clk3 clk4

Stretched:  SCL  _/‾\_/‾\___________/‾\_/‾\_
                  clk1 clk2 ←stretch→ clk3 clk4
                           (slave holds SCL LOW)
```

## Rules
- Only the **slave** stretches (holds SCL LOW after data bit)
- Master MUST check SCL before assuming clock HIGH
- Master releases SCL → checks if it actually went HIGH → if not, wait
- No timeout in I2C spec (dangerous!) — SMBus adds 35ms timeout

## Why Clock Stretching Matters
1. Slow slaves (EEPROM write cycle, ADC conversion) buy processing time
2. Bit-banged I2C slaves need software response time
3. **Problem**: If slave hangs, it can hold SCL LOW forever → bus stuck!
4. **Problem**: Many I2C master implementations ignore clock stretching → bugs at high speed

## Clock Stretching Pitfalls
- Linux i2c-gpio (bit-bang) supports it; hardware controllers may not
- Some devices stretch excessively (>10ms) → causes timeout in strict implementations
- Qualcomm QUP I2C has configurable stretch timeout
- Automotive: clock stretching must be bounded for deterministic timing

---

# SECTION 11: SPEED MODES

| Mode | Max Speed | Pull-up | Year | Use Case |
|------|----------|---------|------|----------|
| Standard (Sm) | 100 kHz | 4.7KΩ typical | 1982 | EEPROMs, RTC, basic sensors |
| Fast (Fm) | 400 kHz | 2.2KΩ typical | 1992 | Sensors, audio codecs |
| Fast-mode Plus (Fm+) | 1 MHz | 1KΩ or active | 2012 | High-perf sensors, large data |
| High-Speed (HS) | 3.4 MHz | Active pull-ups | 2000 | High-speed peripherals |
| Ultra-Fast (UFm) | 5 MHz | Push-pull (no OD!) | 2012 | LED drivers (unidirectional!) |

### High-Speed Mode Protocol
1. Master sends START in Fast/Std mode
2. Sends HS master code (0000 1xxx) at <400 kHz
3. Sends Repeated START
4. Switches to HS mode (3.4 MHz) for data
5. STOP returns to Fast/Std mode

### Ultra-Fast Mode (UFm)
- **NOT** open-drain — uses push-pull drivers
- **Unidirectional only** — master to slave (no read back!)
- No arbitration, no clock stretching, no ACK
- Used for LED matrix drivers where only writes needed

---

# SECTION 12: TIMING PARAMETERS

## Key Timing (Fast Mode, 400 kHz)

| Parameter | Symbol | Min | Max | Unit |
|-----------|--------|-----|-----|------|
| SCL clock freq | f_SCL | 0 | 400 | kHz |
| Hold time START | t_HD;STA | 0.6 | — | µs |
| Setup time START | t_SU;STA | 0.6 | — | µs |
| SCL LOW period | t_LOW | 1.3 | — | µs |
| SCL HIGH period | t_HIGH | 0.6 | — | µs |
| Setup time STOP | t_SU;STO | 0.6 | — | µs |
| Data hold time | t_HD;DAT | 0 | — | µs |
| Data setup time | t_SU;DAT | 100 | — | ns |
| SDA/SCL rise time | t_r | — | 300 | ns |
| SDA/SCL fall time | t_f | — | 300 | ns |
| Bus free time (between STOP-START) | t_BUF | 1.3 | — | µs |

## Timing Formula
```
Minimum clock period (Fast mode):
  T = t_LOW + t_HIGH = 1.3µs + 0.6µs = 1.9µs
  → f_max = 1/1.9µs = 526 kHz (theoretical)
  → 400 kHz with margin

Rise time constraint:
  t_rise ≤ 300ns (Fast mode)
  t_rise = 0.8473 × Rp × Cb
  → Rp × Cb ≤ 354ns
  → At 200pF: Rp ≤ 1.77KΩ
```

---

# SECTION 13: ERROR CONDITIONS

## NACK Scenarios

| When | Who NACKs | Meaning |
|------|-----------|---------|
| Address phase | All slaves | No device at that address |
| Write data | Slave | Slave can't accept data (buffer full, invalid register) |
| Read last byte | Master | Master tells slave "stop sending" (expected, not error) |
| During transfer | Slave | Internal error, unsupported operation |

## Bus Stuck (Hung Bus)

The most dreaded I2C problem — SDA stuck LOW.

**Causes:**
1. Slave holding SDA LOW (mid-byte, waiting for clock)
2. Slave crashed during ACK (holding SDA LOW)
3. Glitch on SCL miscounted as extra clocks
4. Power glitch reset master but not slave

**Recovery — The Clock Pulse Method:**
```
1. Master sends up to 9 clock pulses on SCL
2. After each pulse, check if SDA goes HIGH
3. If SDA goes HIGH → send STOP condition
4. If still LOW after 9 clocks → hardware reset needed

// Implementation:
void i2c_bus_recovery(void) {
    set_scl_gpio_output();
    set_sda_gpio_input();
    for (int i = 0; i < 9; i++) {
        scl_low(); delay_us(5);
        scl_high(); delay_us(5);
        if (read_sda() == HIGH) break;
    }
    // Generate STOP
    sda_low(); delay_us(5);
    scl_high(); delay_us(5);
    sda_high(); delay_us(5);
}
```

**Linux kernel bus recovery:**
```c
struct i2c_bus_recovery_info recovery = {
    .recover_bus = i2c_generic_scl_recovery,
    .get_scl = get_scl_gpio_value,
    .set_scl = set_scl_gpio_value,
    .get_sda = get_sda_gpio_value,
};
adap->bus_recovery_info = &recovery;
```

---

# SECTION 14: HARDWARE ARCHITECTURE

## Typical MCU I2C Peripheral

```
                    ┌──────────────────────────────────────┐
                    │          I2C Peripheral               │
APB Bus ──────────→ │                                      │
                    │  ┌──────────────┐  ┌──────────┐     │──→ SCL (OD)
  CPU/DMA ←→        │  │ Shift Reg    │←→│ SDA Ctrl │─────│──→ SDA (OD)
                    │  │ (8-bit + ACK)│  └──────────┘     │
                    │  └──────────────┘                    │
                    │  ┌──────────────┐                    │
                    │  │ Address      │                    │
                    │  │ Comparator   │                    │
                    │  │ (7/10-bit)   │                    │
                    │  └──────────────┘                    │
                    │  ┌──────────────┐                    │
                    │  │ Clock Gen    │                    │
                    │  │ (SCL timing) │                    │
                    │  └──────────────┘                    │
                    │  ┌──────────────┐                    │
                    │  │ Control/Stat │──→ IRQ             │
                    │  │ CR1,CR2,SR,DR│──→ DMA Req         │
                    │  └──────────────┘                    │
                    └──────────────────────────────────────┘
```

## STM32 I2C Registers (Representative)

| Register | Purpose | Key Bits |
|----------|---------|----------|
| I2C_CR1 | Control 1 | PE, START, STOP, ACK, SWRST |
| I2C_CR2 | Control 2 | FREQ, ITEVTEN, ITBUFEN, DMAEN |
| I2C_OAR1 | Own Address 1 | ADD[7:1] (7-bit), ADD[9:0] (10-bit) |
| I2C_OAR2 | Own Address 2 | ENDUAL, ADD2 (dual address mode) |
| I2C_DR | Data Register | 8-bit data read/write |
| I2C_SR1 | Status 1 | SB, ADDR, BTF, STOPF, AF, BERR, OVR, TIMEOUT |
| I2C_SR2 | Status 2 | MSL, BUSY, TRA |
| I2C_CCR | Clock Control | F/S mode select, CCR value |
| I2C_TRISE | Rise Time | TRISE value based on bus speed |

### Clock Configuration (STM32)
```
Standard mode (100 kHz):
  CCR = APB_CLK / (2 × 100000)
  TRISE = (APB_CLK_MHz × 1000ns / 1000) + 1

Fast mode (400 kHz):
  CCR = APB_CLK / (3 × 400000)  (duty cycle 2:1)
  or CCR = APB_CLK / (25 × 400000) (duty 16:9)
  TRISE = (APB_CLK_MHz × 300ns / 1000) + 1

Example: APB = 42 MHz, Fast mode
  CCR = 42000000 / (3 × 400000) = 35
  TRISE = (42 × 300/1000) + 1 = 13
```

---

# SECTION 15: SOFTWARE STACK

## Bare Metal — Minimal I2C Driver

```c
void i2c_init(void) {
    RCC->APB1ENR |= RCC_APB1ENR_I2C1EN;
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOBEN;
    
    // PB6=SCL, PB7=SDA: AF4, open-drain, pull-up
    GPIOB->MODER |= (2<<12)|(2<<14);  // AF mode
    GPIOB->OTYPER |= (1<<6)|(1<<7);   // Open-drain!
    GPIOB->PUPDR |= (1<<12)|(1<<14);  // Pull-up
    GPIOB->AFR[0] |= (4<<24)|(4<<28); // AF4
    
    I2C1->CR2 = 42;      // APB1 = 42 MHz
    I2C1->CCR = 210;      // 100 kHz: 42M/(2×100k)
    I2C1->TRISE = 43;     // 42+1
    I2C1->CR1 = I2C_CR1_PE; // Enable
}

void i2c_write_reg(uint8_t addr, uint8_t reg, uint8_t val) {
    I2C1->CR1 |= I2C_CR1_START;
    while (!(I2C1->SR1 & I2C_SR1_SB));
    
    I2C1->DR = (addr << 1);  // Address + Write
    while (!(I2C1->SR1 & I2C_SR1_ADDR));
    (void)I2C1->SR2;  // Clear ADDR
    
    I2C1->DR = reg;    // Register address
    while (!(I2C1->SR1 & I2C_SR1_BTF));
    
    I2C1->DR = val;    // Data
    while (!(I2C1->SR1 & I2C_SR1_BTF));
    
    I2C1->CR1 |= I2C_CR1_STOP;
}

uint8_t i2c_read_reg(uint8_t addr, uint8_t reg) {
    // Phase 1: Write register address
    I2C1->CR1 |= I2C_CR1_START;
    while (!(I2C1->SR1 & I2C_SR1_SB));
    I2C1->DR = (addr << 1);
    while (!(I2C1->SR1 & I2C_SR1_ADDR));
    (void)I2C1->SR2;
    I2C1->DR = reg;
    while (!(I2C1->SR1 & I2C_SR1_BTF));
    
    // Phase 2: Repeated START + Read
    I2C1->CR1 |= I2C_CR1_START;
    while (!(I2C1->SR1 & I2C_SR1_SB));
    I2C1->DR = (addr << 1) | 1;  // Address + Read
    I2C1->CR1 &= ~I2C_CR1_ACK;   // NACK after 1 byte
    while (!(I2C1->SR1 & I2C_SR1_ADDR));
    (void)I2C1->SR2;
    I2C1->CR1 |= I2C_CR1_STOP;
    
    while (!(I2C1->SR1 & I2C_SR1_RXNE));
    return I2C1->DR;
}
```

## RTOS Integration
```c
SemaphoreHandle_t i2c_mutex;

int i2c_safe_read(uint8_t addr, uint8_t reg, uint8_t *val) {
    if (xSemaphoreTake(i2c_mutex, pdMS_TO_TICKS(100)) != pdTRUE)
        return -EBUSY;
    *val = i2c_read_reg(addr, reg);
    xSemaphoreGive(i2c_mutex);
    return 0;
}
```

---

# SECTION 16: LINUX I2C SUBSYSTEM

## Architecture

```
┌────────────────────────────────────────────────────┐
│                   Userspace                         │
│  /dev/i2c-X          i2c-tools (i2cdetect, etc.)  │
├────────────────────────────────────────────────────┤
│               Linux I2C Framework                   │
│  ┌──────────────┐   ┌──────────────┐              │
│  │ I2C Adapter  │   │ I2C Client   │              │
│  │ (Controller) │   │ (Device drv) │              │
│  │ i2c-bcm2835  │   │ lm75, ds3231 │              │
│  │ i2c-designware│  │ at24, adxl345│              │
│  └──────┬───────┘   └──────┬───────┘              │
│         │    i2c_msg        │                      │
│         └────────┬──────────┘                      │
│        ┌─────────┴───────────┐                     │
│        │  I2C Core (i2c-core)│                     │
│        │  i2c_transfer()     │                     │
│        └─────────────────────┘                     │
├────────────────────────────────────────────────────┤
│              Hardware: SCL + SDA                    │
└────────────────────────────────────────────────────┘
```

## Key Kernel Files

| File | Purpose |
|------|---------|
| drivers/i2c/i2c-core-base.c | I2C core framework |
| drivers/i2c/i2c-dev.c | Userspace /dev/i2c-X |
| drivers/i2c/busses/i2c-designware-*.c | DesignWare I2C controller |
| drivers/i2c/busses/i2c-bcm2835.c | Raspberry Pi |
| drivers/i2c/busses/i2c-qup.c | Qualcomm QUP |
| drivers/i2c/busses/i2c-imx.c | NXP i.MX |
| drivers/i2c/muxes/i2c-mux-pca954x.c | I2C multiplexer |
| include/linux/i2c.h | I2C API header |

## Device Tree
```dts
&i2c0 {
    status = "okay";
    clock-frequency = <400000>;  /* Fast mode */
    
    /* Temperature sensor */
    temp_sensor: lm75@48 {
        compatible = "national,lm75";
        reg = <0x48>;
    };
    
    /* EEPROM */
    eeprom: 24c256@50 {
        compatible = "atmel,24c256";
        reg = <0x50>;
        pagesize = <64>;
    };
    
    /* I2C Multiplexer */
    i2c-mux@70 {
        compatible = "nxp,pca9548";
        reg = <0x70>;
        #address-cells = <1>;
        #size-cells = <0>;
        
        i2c@0 {
            reg = <0>;
            #address-cells = <1>;
            #size-cells = <0>;
            sensor@68 {
                compatible = "invensense,mpu6050";
                reg = <0x68>;
            };
        };
    };
};
```

## i2c-tools Commands (Essential!)
```bash
# Detect all devices on bus 0
i2cdetect -y 0

# Read register 0x0A from device 0x68 on bus 0
i2cget -y 0 0x68 0x0A

# Write 0x42 to register 0x0A of device 0x68
i2cset -y 0 0x68 0x0A 0x42

# Dump all registers of device 0x68
i2cdump -y 0 0x68

# Transfer (raw): write 0x0A then read 2 bytes
i2ctransfer -y 0 w1@0x68 0x0A r2
```

## Userspace Access via /dev/i2c-X
```c
#include <linux/i2c-dev.h>
#include <sys/ioctl.h>

int fd = open("/dev/i2c-0", O_RDWR);
ioctl(fd, I2C_SLAVE, 0x68);  // Set slave address

// Write register
uint8_t buf[2] = {0x0A, 0x42};  // reg, value
write(fd, buf, 2);

// Read register
uint8_t reg = 0x0A;
write(fd, &reg, 1);   // Write register address
uint8_t val;
read(fd, &val, 1);    // Read value
```

---

# SECTION 17: ANDROID HAL & FRAMEWORK

## Android I2C Access
```
Application (Java/Kotlin)
    ↓ (JNI/NDK)
Native Library (C/C++)
    ↓ (open/ioctl on /dev/i2c-X)
Linux I2C Framework
    ↓
I2C Controller Driver
    ↓
Hardware (SCL + SDA)
```

## AAOS — Sensor Access via I2C
In Android Automotive:
```
Physical Sensor (I2C) → Sensor HAL → SensorManager → Application

Relevant AIDL:
  android.hardware.sensors (AIDL)
  
Sensor HAL reads I2C devices and publishes to Android SensorManager.
Touch, PMIC, audio codec also commonly on I2C.
```

---

# SECTION 18: AUTOSAR I2C

## AUTOSAR I2C Stack
```
┌───────────────────┐
│ Application (SWC) │
├───────────────────┤
│ RTE               │
├───────────────────┤
│ I2c Module (BSW)  │ ← Not standardized like SPI Handler!
├───────────────────┤
│ I2c_Hw (MCAL)     │ ← Vendor-specific MCAL driver
├───────────────────┤
│ I2C Hardware      │
└───────────────────┘
```

Note: AUTOSAR does not have a standardized I2C Handler/Driver like SPI. I2C access is typically vendor-specific or wrapped in a custom Complex Driver (CDD).

---

# SECTION 19: I2C MULTIPLEXERS & SWITCHES

## Why Mux?
- Address conflict: two devices with same address
- Bus capacitance: exceed 400pF limit
- Fault isolation: one segment's failure doesn't affect others

## PCA9548A (8-Channel I2C Mux)
```
           ┌──────────┐
  SCL ────→│          ├──→ SCL_CH0 → [Devices]
  SDA ←───→│ PCA9548A ├──→ SCL_CH1 → [Devices]
           │ (0x70)   ├──→ SCL_CH2 → [Devices]
           │          │    ...
           │          ├──→ SCL_CH7 → [Devices]
           └──────────┘
           
Control: Write channel mask to 0x70
  0x01 = CH0, 0x02 = CH1, 0x04 = CH2, ...
  0x03 = CH0+CH1 simultaneously
```

---

# SECTION 20: I3C — THE SUCCESSOR

## MIPI I3C Overview

| Feature | I2C | I3C |
|---------|-----|-----|
| Speed | 400k–3.4M | Up to 12.5 MHz (HDR) |
| Wires | 2 (OD) | 2 (push-pull + OD) |
| Addressing | 7/10-bit static | 7-bit dynamic assigned |
| In-band IRQ | No (separate pin) | Yes (IBI on SDA) |
| Hot-join | No | Yes |
| Multi-master | Yes | Yes (secondary masters) |
| Backward compat | — | With I2C devices on same bus |
| Error detection | ACK only | CRC + Parity |

I3C is backward compatible: I2C devices work on I3C bus alongside I3C devices.

---

# SECTION 21: REAL-WORLD AUTOMOTIVE APPLICATIONS

| Component | I2C Device | Address | Purpose |
|-----------|-----------|---------|---------|
| PMIC | PCA9450 | 0x25 | Power management for SoC |
| Temp sensor | TMP112 | 0x48 | Board temperature |
| EEPROM | AT24C256 | 0x50 | Config, calibration data |
| RTC | DS3231 | 0x68 | Time keeping (backup) |
| Touch controller | GT911 | 0x5D | Capacitive touch |
| Audio codec | TAS5805M | 0x2C | Audio amplifier |
| IO expander | PCA9555 | 0x20 | GPIO expansion |
| LED driver | LP5009 | 0x14 | Ambient lighting |
| I2C mux | PCA9548A | 0x70 | Bus segmentation |
| Fuel gauge | BQ27426 | 0x55 | Battery monitoring |

## Qualcomm SA8155P/SA8295P I2C
```
I2C controllers: QUP (same engine as SPI/UART)
  - Each QUP configurable as I2C, SPI, or UART
  - Up to 12 QUP instances
  - Fast mode (400 kHz) and Fast+ (1 MHz) supported
  - DMA via GPI engine
  - Linux driver: i2c-qup.c / i2c-geni-qcom.c
  - Typical use: PMIC, touch, sensor hub, audio codec
```

---

# SECTION 22: COMMON PITFALLS & GOTCHAS

1. **Missing pull-up resistors** — #1 mistake. I2C REQUIRES external pull-ups (internal weak pull-ups are NOT sufficient for reliable operation)
2. **Wrong pull-up value** — too high = slow rise → fails at 400kHz; too low = excessive current, can't pull LOW
3. **Address confusion: 7-bit vs 8-bit** — Datasheets list address differently! 0x68 (7-bit) = 0xD0 (8-bit write) / 0xD1 (8-bit read)
4. **Bus capacitance exceeded** — >400pF → slow rise → fails. Use I2C mux or buffer
5. **Clock stretching ignored** — master must check SCL went HIGH. Bit-bang implementations often forget this
6. **No bus recovery** — if SDA stuck LOW, bus hangs forever without recovery mechanism
7. **Multiple pull-ups** — each device with its own pull-up → parallel resistance too low. Use ONE pair of pull-ups
8. **Voltage mismatch** — 3.3V device on 5V bus without level shifter → damage
9. **Long wires** — capacitance increases, pick up noise, reflections. Max ~1-2m typically
10. **Shared bus without mutex** — RTOS with multiple tasks accessing same I2C bus without protection → corruption
11. **NACK not handled** — slave may NACK and software continues → reads garbage
12. **Repeated START vs STOP+START** — register read MUST use Repeated START to keep bus locked. STOP releases bus → another master may intervene

---

# SECTION 23: SECURITY CONSIDERATIONS

| Threat | Description | Mitigation |
|--------|-------------|------------|
| Bus sniffing | Probe SDA/SCL with analyzer | Encrypt payload, conformal coating |
| Address spoofing | Malicious device answers wrong address | Authenticated communication |
| Denial of Service | Pull SDA LOW → entire bus stuck | Bus recovery, mux isolation |
| EEPROM tampering | Modify calibration/config data | Write protection, secure boot |
| Replay attack | Capture and replay I2C transactions | Sequence numbers, timestamps |

---

# SECTION 24: INTERVIEW GOTCHAS

| Question | Trap | Correct Answer |
|----------|------|----------------|
| "How many devices on I2C?" | Saying 128 | 112 usable (7-bit minus reserved). 1008 for 10-bit. Also limited by capacitance. |
| "Is I2C faster than SPI?" | Oversimplifying | No. I2C max 5 MHz, SPI 200+ MHz. I2C wins on pin count, not speed. |
| "What if two masters transmit?" | Saying "collision" | Arbitration via wired-AND. Non-destructive — winner continues cleanly. |
| "Can I2C detect errors?" | Saying "CRC" | Standard I2C: ACK/NACK only. SMBus adds PEC (CRC-8). I3C adds CRC+parity. |
| "Pull-up value?" | Random guess | Depends on bus capacitance and speed mode. 4.7KΩ for standard, calculate for fast. |
| "What is TWI?" | Saying different protocol | Same as I2C. Atmel's name to avoid Philips trademark. |

---

END OF DOCUMENT 01 — MASTER THEORY
