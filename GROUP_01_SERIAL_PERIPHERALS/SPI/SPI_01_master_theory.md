# SPI — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 01 of 08 — Comprehensive Theory
# Depth: Encyclopedia-grade | Domain: Embedded + Automotive + Android
# ════════════════════════════════════════════════════════════════════

---

# SECTION 1: METADATA & IDENTITY

| Field | Detail |
|-------|--------|
| Full Name | Serial Peripheral Interface |
| Acronym | SPI |
| Creator | Motorola (now NXP), mid-1980s |
| First Appeared In | Motorola 68HC11 microcontroller (1984) |
| Formal Standard | **None** — de facto industry standard, no governing body |
| Closest Specs | Motorola 68HC11 Reference Manual; vendor datasheets |
| OSI Layer | Layer 1 (Physical) + Layer 2 (Data Link — partial) |
| Topology | Master-Slave, Star (shared bus with individual chip-select) |
| Wires | 4 minimum: SCLK, MOSI, MISO, CS̄ (active low) |
| Speed | 1 MHz – 200+ MHz (device dependent) |
| Duplex | Full-duplex (simultaneous bidirectional) |
| Addressing | Hardware: dedicated CS̄ line per slave (no software address) |
| Error Detection | None built-in (no parity, no CRC, no ACK) |
| Max Devices | Limited by CS̄ pins (typically 4–16; extendable via decoder) |
| Typical Distance | PCB trace: 10–30 cm; cable: up to ~1 m with care |
| Power | Varies: 1.8V, 2.5V, 3.3V, 5V logic levels |

---

# SECTION 2: HISTORY & EVOLUTION

## Timeline

| Year | Event |
|------|-------|
| 1979 | Motorola begins development of 68HC11 with serial subsystem |
| 1984 | 68HC11 released — SPI first appears as named peripheral |
| 1987 | Multiple MCU vendors adopt SPI (Intel, Philips, TI) |
| 1990s | SPI becomes de facto standard for Flash, ADC, DAC, sensors |
| 2000s | Dual SPI (2-bit) and Quad SPI (4-bit) emerge for NOR Flash |
| 2010s | Octal SPI (8-bit), OSPI/xSPI standardized by JEDEC (JESD251) |
| 2016 | JEDEC publishes xSPI standard (eXpanded SPI) for high-speed Flash |
| 2020s | SPI remains dominant for sensor/peripheral interfaces; speeds >200 MHz |

## Why No Formal Standard?
- Motorola never published a formal specification document
- Each vendor implemented slight variations (naming, timing, features)
- Despite this, the core 4-wire protocol is remarkably consistent across vendors
- JEDEC xSPI (JESD251) is the closest thing to a formal standard, but only for Flash

## Naming Chaos Across Vendors

| Motorola/NXP | TI | Microchip | STMicro | Signal |
|-------------|-----|-----------|---------|--------|
| MOSI | SIMO | SDO | MOSI | Master Out, Slave In |
| MISO | SOMI | SDI | MISO | Master In, Slave Out |
| SCLK | CLK | SCK | SCK | Serial Clock |
| SS̄ | STE | CS̄ | NSS | Chip Select (active low) |

> **2022 Update**: OSIS (Open Source Hardware Association) recommends:
> MOSI → **COPI** (Controller Out, Peripheral In)
> MISO → **CIPO** (Controller In, Peripheral Out)
> Master → **Controller**, Slave → **Peripheral**
> SS → **CS** (Chip Select)

---

# SECTION 3: PHILOSOPHY & DESIGN PRINCIPLES

## Core Design Philosophy
SPI was designed for **maximum simplicity and speed** for on-board communication:

1. **Shift-register based** — The entire protocol is two shift registers connected in a ring
2. **No overhead** — Zero protocol bits (no start, stop, address, ACK, CRC)
3. **Synchronous** — Clock provided by master → perfect bit alignment guaranteed
4. **Full-duplex** — Every clock cycle moves one bit in each direction simultaneously
5. **Hardware addressing** — CS̄ pin eliminates software addressing overhead
6. **No arbitration** — Master has absolute control; no bus contention possible

## When to Use SPI

| Use SPI When | Don't Use SPI When |
|-------------|-------------------|
| Speed matters (>1 MHz) | Many slaves needed (>8–10) |
| Full-duplex required | Pin count is critical |
| Short distance (on-board) | Long distance (>1 m) |
| Connecting Flash, ADC, DAC, sensor | Need hot-plug capability |
| Deterministic timing needed | Need multi-master |
| Streaming data (audio, display) | Need built-in error detection |

## SPI vs Alternatives — Decision Matrix

| Feature | SPI | I2C | UART | CAN |
|---------|-----|-----|------|-----|
| Speed | ★★★★★ | ★★☆☆☆ | ★★☆☆☆ | ★★★☆☆ |
| Pin count | ★★☆☆☆ | ★★★★★ | ★★★★☆ | ★★★★☆ |
| Distance | ★☆☆☆☆ | ★★☆☆☆ | ★★★☆☆ | ★★★★★ |
| Multi-device | ★★★☆☆ | ★★★★★ | ★☆☆☆☆ | ★★★★★ |
| Full-duplex | ★★★★★ | ☆☆☆☆☆ | ★★★★★ | ☆☆☆☆☆ |
| Error detection | ☆☆☆☆☆ | ★★☆☆☆ | ★☆☆☆☆ | ★★★★★ |
| Complexity | ★★★★☆ | ★★★☆☆ | ★★★★★ | ★★☆☆☆ |

---

# SECTION 4: PROTOCOL FAMILY TREE

```
Serial Protocols
├── Synchronous (clock line present)
│   ├── SPI (Serial Peripheral Interface) ← THIS DOCUMENT
│   │   ├── Standard SPI (4-wire, single data)
│   │   ├── 3-Wire SPI (bidirectional single data)
│   │   ├── Dual SPI (2 data lines)
│   │   ├── Quad SPI / QSPI (4 data lines)
│   │   ├── Octal SPI / OSPI (8 data lines)
│   │   └── xSPI (JEDEC JESD251, standardized high-speed)
│   ├── I2C (2-wire, addressed, multi-master)
│   ├── I2S (audio-specific SPI variant)
│   ├── JTAG/SWD (debug)
│   └── MIPI (CSI-2, DSI — high-speed serial)
└── Asynchronous (no clock line)
    ├── UART/USART
    └── 1-Wire
```

## SPI Variants Deep Dive

| Variant | Data Lines | Clock | Use Case | Throughput |
|---------|-----------|-------|----------|------------|
| Standard SPI | 1 MOSI + 1 MISO | 1 SCLK | General purpose | 1× |
| 3-Wire SPI | 1 SDIO (bidirectional) | 1 SCLK | Pin-limited designs | 1× half-duplex |
| Dual SPI | 2 (IO0 + IO1) | 1 SCLK | NOR Flash read | 2× |
| Quad SPI (QSPI) | 4 (IO0–IO3) | 1 SCLK | NOR Flash fast read/write | 4× |
| Octal SPI (OSPI) | 8 (IO0–IO7) | 1 SCLK (+DQS) | High-performance Flash | 8× |
| xSPI (JEDEC) | 8 + DQS | 1 SCLK (DDR) | Next-gen NOR Flash | 16× (DDR Octal) |

---

# SECTION 5: OSI MODEL MAPPING

```
┌─────────────────────────────────────────────────┐
│ Layer 7: Application                            │
│   → User data: sensor readings, Flash commands  │
├─────────────────────────────────────────────────┤
│ Layer 6: Presentation                           │
│   → Byte ordering (MSB/LSB first varies!)       │
├─────────────────────────────────────────────────┤
│ Layer 5: Session                                │
│   → CS̄ assert/deassert = session boundary       │
├─────────────────────────────────────────────────┤
│ Layer 4: Transport                              │
│   → Not applicable (no guaranteed delivery)     │
├─────────────────────────────────────────────────┤
│ Layer 3: Network                                │
│   → CS̄ line = hardware "address"                │
├─────────────────────────────────────────────────┤
│ Layer 2: Data Link                              │
│   → Shift-register exchange, no framing         │
│   → No error detection/correction               │
├─────────────────────────────────────────────────┤
│ Layer 1: Physical                               │
│   → SCLK, MOSI, MISO, CS̄                       │
│   → Push-pull CMOS drivers (mostly)             │
│   → 1.8V / 3.3V / 5V levels                    │
└─────────────────────────────────────────────────┘
```

---

# SECTION 6: PHYSICAL LAYER DEEP DIVE

## Signal Descriptions

| Signal | Direction | Function | Electrical |
|--------|-----------|----------|------------|
| SCLK | Master → Slave | Serial clock; master generates continuously during transfer | Push-pull |
| MOSI | Master → Slave | Master Out Slave In; data from master to slave | Push-pull |
| MISO | Slave → Master | Master In Slave Out; data from slave to master | Tri-state (high-Z when CS̄=1) |
| CS̄ / SS̄ | Master → Slave | Chip Select; active LOW enables specific slave | Push-pull |

## Electrical Characteristics

### Drive Types
- **MOSI, SCLK, CS̄**: Push-pull output from master — actively drives HIGH and LOW
- **MISO**: **Tri-state** from slave — this is CRITICAL:
  - When CS̄ = HIGH (deselected): MISO = high impedance (floating)
  - When CS̄ = LOW (selected): MISO = actively driven by that slave
  - Without tri-state: bus contention if multiple slaves share MISO!

### Voltage Levels (CMOS)

| Standard | V_IL (max) | V_IH (min) | V_OL (max) | V_OH (min) |
|----------|-----------|-----------|-----------|-----------|
| 5V CMOS | 1.5V | 3.5V | 0.4V | 4.4V |
| 3.3V CMOS | 0.8V | 2.0V | 0.4V | 2.4V |
| 1.8V CMOS | 0.6V | 1.2V | 0.4V | 1.4V |

### Level Shifting Scenarios
- **3.3V master ↔ 5V slave**: Need bidirectional level shifter (MISO: 5V→3.3V)
- **3.3V master ↔ 1.8V slave**: Need level translator
- **Common mistake**: Direct connection of different voltage domains → device damage or unreliable comms

### Signal Integrity at High Speed
- At >10 MHz: treat PCB traces as transmission lines
- Rule of thumb: if trace_length > λ/10, impedance matching needed
- For 50 MHz SPI: λ ≈ 4m → traces >40cm need care (rare on PCB)
- Keep SCLK trace shorter than data traces to avoid clock-data skew
- Add series resistors (22–33Ω) for impedance matching at >25 MHz
- Maximum stub length for CS̄: keep short to avoid reflections
- Ground plane under SPI traces is essential at high speed

### Typical Capacitance Budget
```
Source                  Typical Value
Pin capacitance         5–10 pF per device
Trace capacitance       ~1 pF/cm (FR4)
Via capacitance         ~0.5 pF per via
Connector               2–5 pF
─────────────────────────────────
Total budget at 50 MHz: keep < 50 pF
```

---

# SECTION 7: THE FOUR SPI MODES — CPOL & CPHA

This is the MOST important concept in SPI. Every interview will ask about it.

## Definitions

| Parameter | Meaning |
|-----------|---------|
| **CPOL** (Clock Polarity) | Idle state of SCLK: 0=LOW idle, 1=HIGH idle |
| **CPHA** (Clock Phase) | When data is sampled: 0=first edge, 1=second edge |

## The Four Modes

| Mode | CPOL | CPHA | SCLK Idle | Data Captured On | Data Shifted On | Motorola Name |
|------|------|------|-----------|-----------------|-----------------|---------------|
| **0** | 0 | 0 | LOW | Rising edge (1st) | Falling edge | — |
| **1** | 0 | 1 | LOW | Falling edge (2nd) | Rising edge | — |
| **2** | 1 | 0 | HIGH | Falling edge (1st) | Rising edge | — |
| **3** | 1 | 1 | HIGH | Rising edge (2nd) | Falling edge | — |

## Mode 0 Timing (Most Common)
```
CS̄   ‾‾‾‾\_________________________________/‾‾‾‾
SCLK _____/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\______
           ↑   ↑   ↑   ↑   ↑   ↑   ↑   ↑  = SAMPLE (rising)
MOSI ─────<D7><D6><D5><D4><D3><D2><D1><D0>─────
MISO ─────<D7><D6><D5><D4><D3><D2><D1><D0>─────
```

## Mode 3 Timing (Second Most Common)
```
CS̄   ‾‾‾‾\_________________________________/‾‾‾‾
SCLK ‾‾‾‾‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\_/‾\‾‾‾‾‾‾
            ↑   ↑   ↑   ↑   ↑   ↑   ↑   ↑  = SAMPLE (rising)
MOSI ─────<D7><D6><D5><D4><D3><D2><D1><D0>─────
MISO ─────<D7><D6><D5><D4><D3><D2><D1><D0>─────
```

## Which Devices Use Which Modes?

| Device Type | Typical Mode | Examples |
|------------|-------------|----------|
| Flash memory (NOR/NAND) | Mode 0 or Mode 3 | W25Q, IS25LP, MT25Q |
| ADC | Mode 0 | ADS1256, MCP3008, AD7193 |
| DAC | Mode 0 | MCP4921, DAC8564 |
| Accelerometer / IMU | Mode 0 or 3 | MPU6500 (Mode 0/3), ADXL345 (Mode 3) |
| Pressure sensor | Mode 0 | BMP280, MS5611 |
| Display (LCD/OLED) | Mode 0 | ILI9341, SSD1306 |
| SD Card (SPI mode) | Mode 0 | All SD/microSD cards |
| Ethernet controller | Mode 0 | W5500, ENC28J60 |
| CAN controller | Mode 0 | MCP2515 |
| RF transceiver | Mode 0 | nRF24L01, CC1101, SX1276 |
| Touch controller | Mode 0 | XPT2046, STMPE610 |

> **Rule of Thumb**: When in doubt, try Mode 0 first — it works with ~80% of devices.

## CRITICAL: What Happens With Wrong Mode?
- **Wrong CPOL**: Data sampled at transition instead of stable → every bit wrong
- **Wrong CPHA**: Data shifted by half a clock → bit-shifted garbage
- **Both wrong (Mode 0 vs Mode 3)**: May accidentally work for slow speeds! (Both sample on rising edge, just different idle)
- **Mode 0 vs Mode 1**: Total garbage — sampling on opposite edges

---

# SECTION 8: DATA TRANSFER MECHANISM

## The Fundamental Concept: Circular Shift Register

```
MASTER                          SLAVE
┌─────────────────┐            ┌─────────────────┐
│ [7][6][5][4][3] │            │ [7][6][5][4][3] │
│ [2][1][0]  ──MOSI──────────→ [2][1][0]        │
│         ←──MISO──────────── [MSB out]          │
│ 8-bit Shift Reg │    SCLK→  │ 8-bit Shift Reg │
└─────────────────┘            └─────────────────┘
```

### How It Works (Step by Step)
1. Master loads TX byte into shift register
2. Master asserts CS̄ LOW to select slave
3. Master generates SCLK pulses
4. On each clock edge:
   - Master shifts MSB out on MOSI
   - Slave shifts MSB out on MISO
   - Both simultaneously clock in the incoming bit at LSB
5. After 8 clocks: master and slave have EXCHANGED bytes
6. This is always a **simultaneous bidirectional exchange** — you cannot send without receiving, and vice versa

### Key Implications
- **To read from slave**: Master must send dummy bytes (0x00 or 0xFF) to generate clocks
- **To write to slave**: Master must ignore received bytes
- **Transfer size**: Always equal in both directions (N bytes out = N bytes in)
- **No "just read" or "just write"**: Every transfer is an exchange

## Bit Order
- **MSB first**: Most common (default for most SPI peripherals)
- **LSB first**: Some devices (check datasheet!)
- **Configurable**: Most MCU SPI peripherals let you choose

## Word Size
- **8-bit**: Most common
- **16-bit**: Some ADCs, DACs
- **Arbitrary**: Some controllers support 4–32 bit words
- Multiple bytes: just keep clocking — CS̄ stays low for entire transaction

---

# SECTION 9: CHIP SELECT MANAGEMENT

## CS̄ Behavior Rules
1. CS̄ is **active LOW** (assert = drive LOW)
2. Master controls all CS̄ lines
3. **Only ONE slave selected at a time** (else bus contention on MISO!)
4. CS̄ must be deasserted between separate transactions (most devices)
5. Some devices require CS̄ to stay low for multi-byte sequences

## CS̄ Timing Parameters

| Parameter | Symbol | Typical | Description |
|-----------|--------|---------|-------------|
| CS̄ setup time | t_CSS | 5–100 ns | CS̄ LOW before first SCLK |
| CS̄ hold time | t_CSH | 5–100 ns | CS̄ LOW after last SCLK |
| CS̄ deselect time | t_CSD | 50–500 ns | CS̄ HIGH between transactions |
| CS̄ to MISO active | t_CSR | 10–50 ns | Slave starts driving MISO |

## Scaling Beyond Available CS̄ Pins

### Method 1: GPIO as CS̄
```c
// Most common approach — use any GPIO pin
void spi_select(int slave) {
    GPIO_Write(cs_pins[slave], LOW);
}
void spi_deselect(int slave) {
    GPIO_Write(cs_pins[slave], HIGH);
}
```

### Method 2: Decoder (74HC138)
```
Master CS0 ──→ ┌──────────┐──→ CS̄_0
Master CS1 ──→ │ 74HC138  │──→ CS̄_1
Master CS2 ──→ │ 3-to-8   │──→ CS̄_2
               │ Decoder   │──→ CS̄_3
               │           │──→ ...
               └──────────┘──→ CS̄_7

3 GPIO pins → 8 chip selects
```

### Method 3: Daisy-Chain
```
Master ──MOSI──→ Slave1 ──MOSI──→ Slave2 ──MOSI──→ Slave3
       ←─MISO──────────────────────────────MISO──┘
       ──SCLK──→──────────→──────────→
       ──CS̄────→──────────→──────────→ (shared)

Data shifts through all slaves like a pipeline.
To write to slave3: send 3 bytes; slave3 gets first byte.
Used by: LED drivers (WS2801), DAC chains.
```

---

# SECTION 10: MULTI-BYTE TRANSACTIONS & COMMAND PROTOCOLS

## Typical SPI Device Command Structure

Most SPI devices follow this pattern:

```
CS̄ LOW → [Command Byte] → [Address Bytes] → [Data Bytes] → CS̄ HIGH
         ↓                ↓                 ↓
    Op code (R/W/Erase)  Register/Memory   Read or Write data
```

### Example: SPI NOR Flash (W25Q series)

| Command | Opcode | Sequence |
|---------|--------|----------|
| Read Status | 0x05 | CS̄↓ [0x05] [read byte] CS̄↑ |
| Write Enable | 0x06 | CS̄↓ [0x06] CS̄↑ |
| Page Program | 0x02 | CS̄↓ [0x02] [A23..A0] [data...256 bytes max] CS̄↑ |
| Sector Erase | 0x20 | CS̄↓ [0x20] [A23..A0] CS̄↑ |
| Read Data | 0x03 | CS̄↓ [0x03] [A23..A0] [read data...unlimited] CS̄↑ |
| Fast Read | 0x0B | CS̄↓ [0x0B] [A23..A0] [dummy] [read data...] CS̄↑ |
| Read JEDEC ID | 0x9F | CS̄↓ [0x9F] [3 bytes returned: MFR, Type, Cap] CS̄↑ |

### Example: SPI ADC (MCP3008, 10-bit, 8-channel)

```
CS̄↓
  Master sends:  [0x01] [0x80 | (ch<<4)] [0x00]
  Slave returns: [xxxx] [0 0 0 0 0 B9 B8 B7] [B6 B5 B4 B3 B2 B1 B0 x]
CS̄↑

10-bit result = ((byte2 & 0x03) << 8) | byte3
```

### Example: SPI IMU Register Access (MPU6500)

```
Read register:  CS̄↓ [0x80 | reg_addr] [dummy] → [reg_value returned] CS̄↑
Write register: CS̄↓ [0x00 | reg_addr] [value] CS̄↑

Bit 7 of first byte: 1=Read, 0=Write
```

---

# SECTION 11: TIMING & PERFORMANCE

## Speed Calculation

```
Throughput = SCLK_frequency × data_bits / total_bits_per_frame
```
Since SPI has zero overhead bits:
```
Throughput = SCLK_frequency (for single SPI, 1 bit per clock)
```

| Clock | Standard SPI | Dual SPI | Quad SPI | Octal SPI |
|-------|-------------|----------|----------|-----------|
| 1 MHz | 1 Mbps | 2 Mbps | 4 Mbps | 8 Mbps |
| 10 MHz | 10 Mbps | 20 Mbps | 40 Mbps | 80 Mbps |
| 50 MHz | 50 Mbps | 100 Mbps | 200 Mbps | 400 Mbps |
| 100 MHz | 100 Mbps | 200 Mbps | 400 Mbps | 800 Mbps |
| 200 MHz (DDR) | 400 Mbps | 800 Mbps | 1.6 Gbps | 3.2 Gbps |

## Timing Parameters

| Parameter | Symbol | Description | Typical |
|-----------|--------|-------------|---------|
| SCLK frequency | f_SCK | Clock speed | 1–200 MHz |
| Setup time | t_SU | Data stable before sample edge | 2–10 ns |
| Hold time | t_HD | Data stable after sample edge | 2–10 ns |
| Clock-to-output | t_CO | Clock edge to valid output data | 5–20 ns |
| CS̄ setup | t_CSS | CS̄ active before first clock | 5–100 ns |
| CS̄ hold | t_CSH | CS̄ active after last clock | 5–100 ns |
| CS̄ deselect | t_CSD | Minimum time between transactions | 50–500 ns |
| MISO turn-off | t_DIS | MISO goes high-Z after CS̄ deassert | 10–50 ns |

## Maximum Clock Rate Determination
```
f_max = 1 / (t_CO + t_SU + t_prop)

Where:
  t_CO   = slave clock-to-output delay
  t_SU   = master setup time requirement
  t_prop = propagation delay (trace + connector)

Example: t_CO=15ns, t_SU=5ns, t_prop=2ns
  f_max = 1/(15+5+2)ns = 45.5 MHz
```

---

# SECTION 12: ERROR HANDLING & RELIABILITY

## SPI Has NO Built-in Error Detection

This is both a strength (zero overhead) and weakness (silent corruption). You must add your own.

## Error Sources

| Error Type | Cause | Symptom |
|-----------|-------|---------|
| Clock skew | Trace length mismatch | Bit errors at high speed |
| Voltage glitch | Power supply noise | Random bit flips |
| EMI coupling | Nearby switching circuits | Burst errors |
| CS̄ glitch | Noise on chip-select line | Transaction boundary corruption |
| Bus contention | Multiple CS̄ active | Corrupted MISO, possible damage |
| Floating MISO | No pull-up, no slave selected | Random data read |

## Software Error Detection Strategies

### 1. Read-Back Verification
```c
// Write register, then read back and compare
spi_write_reg(addr, value);
uint8_t readback = spi_read_reg(addr);
if (readback != value) { /* ERROR */ }
```

### 2. CRC in Application Layer
```c
// Add CRC to data payload
uint8_t tx_buf[] = {CMD, DATA1, DATA2, CRC8(DATA1, DATA2)};
spi_transfer(tx_buf, rx_buf, 4);
```

### 3. Device ID Verification
```c
// Periodically verify device is responsive
uint32_t id = spi_read_jedec_id();
if (id != EXPECTED_FLASH_ID) { /* bus error or wrong device */ }
```

### 4. Status Register Polling
```c
// Check device status for internal errors
uint8_t status = spi_read_status();
if (status & ERROR_FLAGS) { /* device reports error */ }
```

## Hardware Error Prevention
- **Series termination resistors**: 22–33Ω on SCLK, MOSI for impedance matching
- **Pull-up on CS̄**: Ensure devices deselected during master reset
- **Pull-up/down on MISO**: Prevent floating when no slave selected
- **Decoupling caps**: 100nF + 10µF close to each slave device VDD
- **Separate ground return**: Minimize ground bounce at high speeds
- **Keep traces short and matched**: Especially SCLK vs data

---

# SECTION 13: HARDWARE ARCHITECTURE

## Typical MCU SPI Peripheral Block Diagram

```
                    ┌─────────────────────────────────────┐
                    │         SPI Peripheral               │
APB Bus ──────────→ │                                     │
                    │  ┌──────────┐  ┌──────────────┐     │──→ SCLK
                    │  │ TX FIFO  │→ │ TX Shift Reg  │─────│──→ MOSI
  CPU/DMA ←→        │  │ (4-16B)  │  │ (8/16/32-bit) │     │
                    │  └──────────┘  └──────────────┘     │
                    │  ┌──────────┐  ┌──────────────┐     │
                    │  │ RX FIFO  │← │ RX Shift Reg  │←────│←── MISO
                    │  │ (4-16B)  │  │ (8/16/32-bit) │     │
                    │  └──────────┘  └──────────────┘     │
                    │                                     │──→ CS̄ (HW)
                    │  ┌───────────────────────┐          │
                    │  │ Baud Rate Generator   │          │
                    │  │ (Prescaler + Divider) │          │
                    │  └───────────────────────┘          │
                    │  ┌───────────────────────┐          │
                    │  │ Control/Status Regs   │──→ IRQ   │
                    │  │ CR1, CR2, SR, DR      │──→ DMA Req│
                    │  └───────────────────────┘          │
                    └─────────────────────────────────────┘
```

## STM32 SPI Registers (Representative)

| Register | Name | Key Fields |
|----------|------|-----------|
| SPI_CR1 | Control 1 | BIDIMODE, BIDIOE, CRCEN, DFF, RXONLY, SSM, SSI, LSBFIRST, SPE, BR[2:0], MSTR, CPOL, CPHA |
| SPI_CR2 | Control 2 | TXEIE, RXNEIE, ERRIE, SSOE, TXDMAEN, RXDMAEN |
| SPI_SR | Status | BSY, OVR, MODF, CRCERR, TXE, RXNE |
| SPI_DR | Data | 8 or 16-bit data register |
| SPI_CRCPR | CRC Poly | CRC polynomial for hardware CRC |

### CR1.BR[2:0] — Baud Rate Prescaler

| BR | Divisor | At 84 MHz APB | At 42 MHz APB |
|----|---------|--------------|--------------|
| 000 | /2 | 42 MHz | 21 MHz |
| 001 | /4 | 21 MHz | 10.5 MHz |
| 010 | /8 | 10.5 MHz | 5.25 MHz |
| 011 | /16 | 5.25 MHz | 2.625 MHz |
| 100 | /32 | 2.625 MHz | 1.3125 MHz |
| 101 | /64 | 1.3125 MHz | 656 kHz |
| 110 | /128 | 656 kHz | 328 kHz |
| 111 | /256 | 328 kHz | 164 kHz |

## FIFO Sizes by MCU Family

| MCU Family | TX FIFO | RX FIFO | Max Speed |
|-----------|---------|---------|-----------|
| STM32F1 | 1 byte | 1 byte | 18 MHz |
| STM32F4 | 1 byte | 1 byte | 42 MHz |
| STM32H7 | 8 bytes | 8 bytes | 150 MHz |
| NXP i.MX RT | 16 words | 16 words | 60 MHz |
| NXP S32K | 4 bytes | 4 bytes | 30 MHz |
| TI AM335x/AM572x | 32 bytes | 32 bytes | 48 MHz |
| Qualcomm QUP v3 | 16 bytes | 16 bytes | 50 MHz |

---

# SECTION 14: SOFTWARE STACK

## Bare Metal — Minimal SPI Driver

```c
void spi_init(void) {
    // 1. Enable clocks
    RCC->APB2ENR |= RCC_APB2ENR_SPI1EN;
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
    
    // 2. GPIO: PA5=SCLK, PA6=MISO, PA7=MOSI as AF5
    GPIOA->MODER |= (2<<10)|(2<<12)|(2<<14); // AF mode
    GPIOA->AFR[0] |= (5<<20)|(5<<24)|(5<<28); // AF5
    
    // 3. SPI config: Master, CPOL=0, CPHA=0, /8 prescaler, 8-bit, SW CS
    SPI1->CR1 = SPI_CR1_MSTR | SPI_CR1_SSM | SPI_CR1_SSI
              | (0b010 << 3);  // BR=/8
    
    // 4. Enable
    SPI1->CR1 |= SPI_CR1_SPE;
}

uint8_t spi_transfer(uint8_t tx) {
    while (!(SPI1->SR & SPI_SR_TXE));    // Wait TX empty
    SPI1->DR = tx;                        // Send byte
    while (!(SPI1->SR & SPI_SR_RXNE));   // Wait RX ready
    return SPI1->DR;                      // Return received byte
}

void spi_write_reg(uint8_t cs_pin, uint8_t reg, uint8_t val) {
    CS_LOW(cs_pin);
    spi_transfer(reg & 0x7F);  // Write: bit7=0
    spi_transfer(val);
    CS_HIGH(cs_pin);
}

uint8_t spi_read_reg(uint8_t cs_pin, uint8_t reg) {
    CS_LOW(cs_pin);
    spi_transfer(reg | 0x80);  // Read: bit7=1
    uint8_t val = spi_transfer(0xFF);  // Dummy byte to clock data in
    CS_HIGH(cs_pin);
    return val;
}
```

## RTOS Integration

```c
// FreeRTOS mutex-protected SPI access
SemaphoreHandle_t spi_mutex;

int spi_transaction(uint8_t cs, uint8_t *tx, uint8_t *rx, size_t len) {
    if (xSemaphoreTake(spi_mutex, pdMS_TO_TICKS(100)) != pdTRUE)
        return -EBUSY;
    
    CS_LOW(cs);
    for (size_t i = 0; i < len; i++)
        rx[i] = spi_transfer(tx[i]);
    CS_HIGH(cs);
    
    xSemaphoreGive(spi_mutex);
    return 0;
}
```

## DMA-Based Transfer

```c
void spi_dma_transfer(uint8_t *tx, uint8_t *rx, uint16_t len) {
    // Configure TX DMA
    DMA_TX->PAR  = (uint32_t)&SPI1->DR;
    DMA_TX->M0AR = (uint32_t)tx;
    DMA_TX->NDTR = len;
    DMA_TX->CR   = DMA_MEM2PER | DMA_MINC | DMA_EN;
    
    // Configure RX DMA
    DMA_RX->PAR  = (uint32_t)&SPI1->DR;
    DMA_RX->M0AR = (uint32_t)rx;
    DMA_RX->NDTR = len;
    DMA_RX->CR   = DMA_PER2MEM | DMA_MINC | DMA_TCIE | DMA_EN;
    
    // Enable SPI DMA
    SPI1->CR2 |= SPI_CR2_TXDMAEN | SPI_CR2_RXDMAEN;
    
    // Wait for completion (or use ISR)
    while (!(DMA_RX->ISR & DMA_FLAG_TC));
}
```

---

# SECTION 15: LINUX SPI SUBSYSTEM

## Architecture

```
┌──────────────────────────────────────────────────┐
│                  Userspace                        │
│   /dev/spidevX.Y    spidev ioctl()               │
├──────────────────────────────────────────────────┤
│              Linux SPI Framework                  │
│   ┌────────────┐  ┌─────────────┐               │
│   │ SPI Master │  │ SPI Device  │               │
│   │ Controller │  │   Driver    │               │
│   │ (spi-xxx)  │  │ (spi-nor,  │               │
│   │            │  │  mcp251x,  │               │
│   │            │  │  ads1256)  │               │
│   └────┬───────┘  └──────┬─────┘               │
│        │     spi_message  │                      │
│        └────────┬─────────┘                      │
│                 │                                │
│   ┌─────────────┴──────────────┐                │
│   │   SPI Core (spi.c)        │                │
│   │   spi_sync/spi_async      │                │
│   └───────────────────────────┘                │
├──────────────────────────────────────────────────┤
│              Hardware                            │
│   SPI Controller → SCLK, MOSI, MISO, CS̄         │
└──────────────────────────────────────────────────┘
```

## Key Files in Linux Kernel

| File | Purpose |
|------|---------|
| drivers/spi/spi.c | SPI core framework |
| drivers/spi/spidev.c | Userspace access (/dev/spidevX.Y) |
| drivers/spi/spi-bcm2835.c | Raspberry Pi SPI controller |
| drivers/spi/spi-stm32.c | STM32 SPI controller |
| drivers/spi/spi-qup.c | Qualcomm QUP SPI controller |
| drivers/spi/spi-imx.c | NXP i.MX SPI controller |
| drivers/mtd/spi-nor/ | SPI NOR Flash driver framework |
| include/linux/spi/spi.h | SPI API header |

## Device Tree Configuration

```dts
&spi0 {
    status = "okay";
    #address-cells = <1>;
    #size-cells = <0>;
    
    /* SPI NOR Flash on CS0 */
    flash@0 {
        compatible = "jedec,spi-nor";
        reg = <0>;                    /* CS0 */
        spi-max-frequency = <50000000>;  /* 50 MHz */
        spi-cpol;                     /* CPOL=1 (omit for CPOL=0) */
        spi-cpha;                     /* CPHA=1 (omit for CPHA=0) */
        m25p,fast-read;
    };
    
    /* ADC on CS1 */
    adc@1 {
        compatible = "ti,ads7950";
        reg = <1>;                    /* CS1 */
        spi-max-frequency = <10000000>;
    };
};
```

## Userspace Access via spidev

```c
#include <linux/spi/spidev.h>
#include <sys/ioctl.h>

int fd = open("/dev/spidev0.0", O_RDWR);

// Configure
uint8_t mode = SPI_MODE_0;
uint32_t speed = 1000000;  // 1 MHz
uint8_t bits = 8;
ioctl(fd, SPI_IOC_WR_MODE, &mode);
ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed);
ioctl(fd, SPI_IOC_WR_BITS_PER_WORD, &bits);

// Full-duplex transfer
struct spi_ioc_transfer xfer = {
    .tx_buf = (unsigned long)tx_buf,
    .rx_buf = (unsigned long)rx_buf,
    .len = sizeof(tx_buf),
    .speed_hz = speed,
    .bits_per_word = bits,
};
ioctl(fd, SPI_IOC_MESSAGE(1), &xfer);
```

## Linux SPI Commands

```bash
# List SPI devices
ls /dev/spidev*

# Check SPI controller
ls /sys/class/spi_master/

# Device info
cat /sys/bus/spi/devices/spi0.0/modalias

# Quick test with spi-tools
spi-config -d /dev/spidev0.0 -m 0 -s 1000000
echo -ne '\x9F' | spi-pipe -d /dev/spidev0.0 -s 1000000 -b 1 | xxd
```

---

# SECTION 16: ANDROID HAL & FRAMEWORK

## Android SPI Access Path

```
Android Application (Java/Kotlin)
    ↓ (NDK JNI)
Native Library (C/C++)
    ↓ (open/ioctl)
/dev/spidevX.Y
    ↓
Linux SPI Framework
    ↓
SPI Controller Driver
    ↓
Hardware
```

## Android Peripheral I/O (Things API - deprecated but educational)

```java
// Android Things API (deprecated but shows concept)
PeripheralManager manager = PeripheralManager.getInstance();
SpiDevice device = manager.openSpiDevice("SPI0.0");
device.setMode(SpiDevice.MODE0);
device.setFrequency(1_000_000);
device.setBitsPerWord(8);

byte[] tx = {(byte)0x9F};
byte[] rx = new byte[4];
device.transfer(tx, rx, 4);
device.close();
```

## AAOS (Android Automotive) — Vehicle HAL SPI Sensors

In automotive Android, SPI sensors (pressure, temperature, IMU) connect through:
```
Sensor → SPI → MCU/SoC → Sensor HAL → SensorManager → App

Relevant HIDL/AIDL:
  android.hardware.sensors@2.1 (HIDL)
  android.hardware.sensors (AIDL)
```

---

# SECTION 17: AUTOSAR SPI HANDLER/DRIVER

## AUTOSAR SPI Stack

```
┌───────────────────────────┐
│ Application (SWC)         │
├───────────────────────────┤
│ RTE                       │
├───────────────────────────┤
│ Spi Handler/Driver (SpiH) │ ← AUTOSAR BSW Module
│  - Job management         │
│  - Sequence scheduling    │
│  - Priority handling      │
├───────────────────────────┤
│ Spi_Hw (MCAL driver)      │ ← Low-level HW access
├───────────────────────────┤
│ SPI Hardware              │
└───────────────────────────┘
```

## Key AUTOSAR Concepts

| Concept | Description |
|---------|-------------|
| **Channel** | A single data buffer (TX/RX pair) |
| **Job** | Collection of channels transferred in one CS̄ assertion |
| **Sequence** | Ordered list of jobs (may span multiple CS̄ cycles) |
| **Priority** | Jobs have priority; handler schedules accordingly |

## AUTOSAR API

```c
Std_ReturnType Spi_WriteIB(Spi_ChannelType ch, const Spi_DataBufferType *buf);
Std_ReturnType Spi_ReadIB(Spi_ChannelType ch, Spi_DataBufferType *buf);
Std_ReturnType Spi_AsyncTransmit(Spi_SequenceType seq);
Spi_SeqResultType Spi_GetSequenceResult(Spi_SequenceType seq);
Spi_StatusType Spi_GetStatus(void);
```

---

# SECTION 18: QSPI / OSPI — EXTENDED SPI FOR FLASH

## Quad SPI (QSPI)

```
Standard SPI:  SCLK + MOSI + MISO + CS̄  → 1 bit/clock
Dual SPI:      SCLK + IO0 + IO1 + CS̄    → 2 bits/clock
Quad SPI:      SCLK + IO0-IO3 + CS̄      → 4 bits/clock

IO0 = MOSI (becomes bidirectional)
IO1 = MISO (becomes bidirectional)
IO2 = WP̄  (repurposed from Write Protect)
IO3 = HOLD̄ (repurposed from Hold)
```

## QSPI Transaction Phases

```
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│ Command  │ Address  │  Alt/Pad │  Dummy   │   Data   │
│ (1-line) │ (1/2/4)  │  (opt)   │ (cycles) │ (1/2/4)  │
└──────────┴──────────┴──────────┴──────────┴──────────┘
    8 clk    24/32 clk   0-8 clk   0-20 clk   N clks
```

## XIP (Execute-In-Place)

QSPI can be memory-mapped:
```
CPU address 0x90000000 → QSPI controller → Flash read command → Data returned
```
- Flash appears as regular memory in CPU address space
- CPU can execute code directly from SPI Flash (no copy to RAM needed)
- Latency: higher than internal Flash, but saves RAM
- Used extensively in automotive for storing calibration data

## Octal SPI / xSPI (JEDEC JESD251)

| Feature | QSPI | OSPI/xSPI |
|---------|------|-----------|
| Data lines | 4 | 8 |
| DQS (Data Strobe) | No | Yes |
| DDR support | Some | Standard |
| Max throughput | 200 Mbps @50MHz | 3.2 Gbps @200MHz DDR |
| Standard | De facto | JEDEC JESD251 |
| Use case | Cost-effective Flash | High-performance automotive |

---

# SECTION 19: SECURITY CONSIDERATIONS

## SPI Security Threats

| Threat | Description | Mitigation |
|--------|-------------|------------|
| Bus sniffing | Attacker probes SPI signals with logic analyzer | Encrypt data in application layer |
| MITM attack | Insert device between master and slave | Physical enclosure, tamper detection |
| Flash extraction | Read SPI Flash contents via JTAG/probe | Encrypted Flash, secure boot |
| Glitch attack | Voltage/clock glitch to skip security checks | Glitch detectors, redundant checks |
| CS̄ manipulation | Force CS̄ to access device out of sequence | Monitor CS̄ state, add authentication |
| Firmware injection | Write malicious firmware to SPI Flash | Secure boot, signed images |

## Automotive Security for SPI

```
┌─────────────────────────────────────────┐
│ Secure Boot Chain                       │
│ ROM → SBL (signed) → ABL (signed)      │
│ All loaded from SPI NOR Flash           │
│                                         │
│ Security measures:                      │
│ 1. Flash write-protect (WP̄ pin)        │
│ 2. Block-level write protection         │
│ 3. One-Time-Programmable (OTP) region   │
│ 4. Signed firmware images               │
│ 5. Hardware root of trust               │
└─────────────────────────────────────────┘
```

---

# SECTION 20: REAL-WORLD APPLICATIONS IN AUTOMOTIVE

## Where SPI is Used in a Modern Vehicle

| Component | SPI Device | Speed | Purpose |
|-----------|-----------|-------|---------|
| Boot Flash | NOR Flash (W25Q/IS25LP) | 50–100 MHz QSPI | Store bootloader, firmware |
| Instrument Cluster | LCD controller (ILI9341) | 10–40 MHz | Display rendering |
| ADAS SoC | External NOR Flash | 100+ MHz OSPI | Code storage, calibration |
| Radar module | ADC (AD9680) | 80 MHz | Signal digitization |
| Airbag ECU | Pressure sensor (BMP280) | 10 MHz | Crash detection supplement |
| Infotainment | Audio codec (WM8731) | 20 MHz | Audio I/O |
| Telematics | RF module (CC1101) | 6.5 MHz | Sub-GHz communication |
| Body controller | CAN transceiver (MCP2515) | 10 MHz | CAN interface via SPI |

## Qualcomm SA8155P / SA8295P SPI

```
SPI controllers: QUP (Qualcomm Unified Protocol) engine
  - Each QUP can be SPI, I2C, or UART (runtime configurable)
  - Up to 12 QUP blocks available
  - Max speed: 50 MHz (standard), higher in special modes
  - DMA support: GPI (Generic Peripheral Interface) DMA
  - Used for: external Flash, touch controller, sensor hub
```

---

# SECTION 21: I2S — THE AUDIO COUSIN OF SPI

## I2S vs SPI

I2S (Inter-IC Sound) is a variant of SPI specifically for audio:

| Aspect | SPI | I2S |
|--------|-----|-----|
| Purpose | General data | Audio samples |
| Clock | SCLK | SCK (bit clock) |
| Data out | MOSI | SD (Serial Data) |
| Data in | MISO | SD (half-duplex) or separate |
| Frame sync | CS̄ | WS (Word Select: L/R channel) |
| Framing | CS̄ per transaction | WS toggles per sample |
| Bit order | MSB (usually) | MSB always |
| Creator | Motorola | Philips (now NXP) |

---

# SECTION 22: COMMON PITFALLS & GOTCHAS

1. **Forgetting dummy bytes for read**: To read N bytes from slave, master must send N bytes (even if garbage)
2. **CS̄ polarity**: Almost always active LOW — but some devices are active HIGH
3. **Mode mismatch**: Even Mode 0 vs Mode 3 can work at low speeds (both sample on rising) — masking bugs
4. **MISO contention**: Two slaves with CS̄ both LOW → bus fight → possible damage
5. **First byte garbage**: After CS̄ assert, slave needs setup time before first clock
6. **CS̄ between bytes**: Some devices require CS̄ to stay LOW for entire multi-byte command
7. **MSB vs LSB first**: Most SPI is MSB-first, but some devices (and SPI peripherals) can do LSB-first
8. **Hardware CS̄ timing**: Many MCU hardware CS̄ deasserts between bytes — use GPIO instead
9. **Shared bus clock speed**: Must run at SLOWEST device's max speed when sharing bus
10. **Pull-ups on unused CS̄**: Always pull CS̄ HIGH when not in use; floating = random selection
11. **DMA alignment**: Many DMA controllers need word-aligned buffers
12. **Clock phase vs device startup**: Some devices need CS̄ toggle to reset internal state machine

---

# SECTION 23: INTERVIEW GOTCHAS — EXPERT LEVEL

| Question | Trap | Correct Answer |
|----------|------|----------------|
| "Is SPI a standard?" | Assuming yes | No formal standard. De facto convention from Motorola. JEDEC xSPI for Flash only. |
| "How many slaves can SPI support?" | Saying "unlimited" | Limited by: CS̄ pins, bus capacitance, signal integrity. Practical limit: ~8-16 |
| "Is SPI faster than I2C?" | Oversimplifying | SPI: 1–200 MHz. I2C: 100 kHz–3.4 MHz (HS). SPI wins by 10-100×. But I2C uses fewer pins. |
| "Can SPI detect errors?" | Saying "no" | No built-in detection, but some MCUs have hardware CRC (e.g., STM32 SPI has CRC generator) |
| "Is SPI full-duplex?" | Missing nuance | Standard 4-wire: yes. 3-wire: half-duplex. Dual/Quad SPI: half-duplex (shared data lines) |
| "Can you have multi-master SPI?" | Saying "no" | Possible but rare/complex. Need MODF detection + arbitration logic. Not practical. |

---

# SECTION 24: MIGRATION GUIDE

## From SPI to...

| Target | When | Key Changes |
|--------|------|-------------|
| QSPI | Need faster Flash access | Add IO2, IO3; use quad commands; may need XIP |
| I2C | Need fewer pins, many devices | Lose speed; gain addressing; change protocol entirely |
| UART | Need longer distance | Lose synchronous advantage; add baud rate management |
| Parallel bus | Need maximum bandwidth | More pins, different timing, complex layout |
| PCIe | Need multi-Gbps | Completely different protocol, serializer needed |

---

END OF DOCUMENT 01 — MASTER THEORY
