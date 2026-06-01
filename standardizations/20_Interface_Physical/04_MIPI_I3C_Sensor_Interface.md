# MIPI I3C — Next-Generation Sensor Interface

**Topic:** MIPI I3C v1.1.1; I3C Basic v1.1.1; sensor bus architecture; I2C replacement; in-band interrupt; dynamic addressing; HDR modes; multi-master; hot-join  
**Standards:** MIPI I3C v1.1.1 (2021), MIPI I3C Basic v1.1.1 (2021), MIPI I3C HCI v1.1 (Host Controller Interface)  
**SDO:** MIPI Alliance  
**Audience:** Sensor interface engineers, embedded systems engineers, SoC designers, IoT device engineers, automotive platform engineers  
**Prerequisites:** I2C protocol basics (SDA/SCL, master/slave, addressing), embedded bus concepts, basic digital electronics

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1982 | I2C (Philips/NXP) | 2-wire bus: clock + data; 100 kHz; revolutionized sensor/control communication |
| 1998 | I2C Fast-Mode Plus (FM+) | 1 MHz; still limited for modern sensors |
| 2006 | I2C High-Speed (HS) | 3.4 MHz; complex; rarely used (protocol overhead) |
| 2014 | **MIPI I3C v1.0** | Next-gen 2-wire bus: backward-compatible with I2C; up to 12.5 MHz; in-band interrupt |
| 2016 | I3C v1.0 ratified | Industry adoption begins |
| 2019 | **I3C Basic v1.0** | Simplified subset for broad adoption (IoT, consumer) |
| 2021 | **I3C v1.1.1 / Basic v1.1.1** | HDR modes; multi-master (multi-controller); hot-join; improved error handling |
| 2022 | I3C HCI v1.1 | Standardized host controller interface (register-level specification) |
| 2023+ | I3C adoption accelerating | Major SoC vendors (Qualcomm, MediaTek, NXP, STM, TI) integrate I3C controllers |

### 1.2 Why Replace I2C?

| I2C Limitation | I3C Solution |
|:---:|---|
| **Speed: max 3.4 MHz (HS mode; rarely used)** | **12.5 MHz SDR; up to 100 Mbps (HDR modes)** |
| **No in-band interrupt**: sensors need separate GPIO for interrupt → extra wire per sensor | **In-Band Interrupt (IBI)**: sensor signals interrupt ON the data wire (SDA); no extra GPIO |
| **Static addressing**: 7-bit address set at design time; conflicts require hardware changes | **Dynamic Address Assignment (DAA)**: controller assigns addresses at runtime; no conflicts |
| **No device discovery**: master must know what's on bus; no hot-plug detection | **Hot-Join**: new device joins bus and requests address dynamically |
| **Pull-up resistors limit speed**: open-drain + pull-up → slow rise time at high speed | **Push-pull mode**: I3C uses push-pull driver in HS → fast transitions; no pull-up needed for I3C |
| **High power for pull-ups**: constant current through pull-ups when SDA/SCL low | **Lower power**: push-pull (no pull-up current); devices can be put in deep sleep |
| **No error recovery**: I2C has no standard recovery from stuck bus | **Error detection + recovery**: parity; CRC (HDR); bus reset; target reset |

---

## Chapter 2 — I3C Architecture

### 2.1 Bus Topology

```mermaid
graph TB
    subgraph "I3C Bus (2 wires: SCL + SDA)"
        CTRL[I3C Controller (Active)<br/>━━━━━━━━━━━<br/>• Drives SCL clock<br/>• Initiates transfers<br/>• Assigns dynamic addresses (DAA)<br/>• Handles IBI from targets<br/>• Push-pull + open-drain modes]
        
        T1[I3C Target: Accelerometer<br/>━━━━━━━━━━━<br/>• Dynamic address: 0x08<br/>• Speed: 12.5 MHz SDR<br/>• IBI: data-ready interrupt<br/>• 48-bit Provisional ID]
        
        T2[I3C Target: Gyroscope<br/>━━━━━━━━━━━<br/>• Dynamic address: 0x09<br/>• Speed: 12.5 MHz SDR<br/>• IBI: motion detect<br/>• Hot-join capable]
        
        T3[I3C Target: Temperature<br/>━━━━━━━━━━━<br/>• Dynamic address: 0x0A<br/>• Speed: 12.5 MHz SDR<br/>• IBI: threshold alarm]
        
        LEGACY[I2C Legacy Device (Humidity)<br/>━━━━━━━━━━━<br/>• Static address: 0x44<br/>• Speed: 400 kHz (I2C FM)<br/>• No IBI; uses open-drain only<br/>• Coexists on same bus]
    end
    
    CTRL --- T1
    CTRL --- T2
    CTRL --- T3
    CTRL --- LEGACY
```

### 2.2 I3C Protocol Modes

| Mode | Speed | Encoding | Driver | Use |
|:----:|:-----:|:--------:|:------:|-----|
| **SDR (Single Data Rate)** | Up to 12.5 MHz | Open-drain (I2C-like) + Push-pull | Push-pull (HS); Open-drain (start/stop) | Standard communication; register access |
| **HDR-DDR** | Up to 25 MHz (50 Mbps effective) | Double Data Rate on SDA | Push-pull | Higher-bandwidth sensor data (bulk transfers) |
| **HDR-TSP** | Up to 25 MHz (25 Mbps) | Ternary Symbol Pure | Push-pull; 3-level SDA | Maximum bandwidth per wire (ternary encoding) |
| **HDR-TSL** | Up to 25 MHz (25 Mbps) | Ternary Symbol Legacy | Push-pull; 3-level SDA | Ternary with backward-compatible framing |
| **HDR-BT** | Up to 50 MHz (100 Mbps) | Bulk Transfer mode | Push-pull | Highest speed (I3C v1.1) |
| **I2C Legacy** | 100K / 400K / 1 MHz | Open-drain | Open-drain only | Backward compatibility with I2C devices |

### 2.3 I3C vs. I2C Pin Compatibility

| Wire | I2C Function | I3C Function | Compatibility |
|:----:|:---:|:---:|---|
| **SCL** | Clock (master-driven; open-drain) | Clock (controller-driven; push-pull in HS) | Same wire; I3C pushes-pull faster; I2C devices still work (open-drain during I2C frames) |
| **SDA** | Data (bidirectional; open-drain) | Data (bidirectional; push-pull in HS) | Same wire; I3C targets switch to push-pull; I2C targets stay open-drain |
| **Pull-ups** | Required (external resistors) | Required (for I2C legacy devices); optional if all I3C | Pull-ups needed only if I2C devices present on bus |

**Key: Same 2 wires. Same PCB layout. I3C and I2C devices coexist on same bus.**

---

## Chapter 3 — Key I3C Features

### 3.1 In-Band Interrupt (IBI)

```mermaid
sequenceDiagram
    participant CTRL as I3C Controller
    participant SENSOR as I3C Target (Accel)
    
    Note over CTRL,SENSOR: Controller is idle; bus is free
    
    SENSOR->>CTRL: Drive SDA LOW during idle (IBI request)
    Note over CTRL: Detects SDA pulled low → IBI pending
    CTRL->>SENSOR: Send ACK (acknowledge IBI)
    SENSOR->>CTRL: Send Mandatory Data Byte (interrupt status)
    Note over CTRL: Read status: "data ready"
    CTRL->>SENSOR: Read sensor data (normal read transfer)
    
    Note over CTRL,SENSOR: No external GPIO interrupt line needed!
```

**IBI advantage:**
- Traditional I2C: each sensor needs a dedicated GPIO interrupt pin on SoC → pin count explodes with many sensors
- I3C IBI: ALL sensors share the same 2 wires (SCL + SDA) for both data AND interrupts
- SoC saves: 10 sensors × 1 GPIO each = 10 GPIOs saved → significant for small IoT SoCs

### 3.2 Dynamic Address Assignment (DAA)

| Step | Action | Result |
|:----:|--------|--------|
| 1 | Controller issues ENTDAA (Enter Dynamic Address Assignment) CCC | Bus enters DAA mode |
| 2 | All unaddressed targets arbitrate on bus (lowest Provisional ID wins) | One target wins arbitration |
| 3 | Winning target sends its 48-bit Provisional ID + BCR + DCR | Controller learns device identity |
| 4 | Controller assigns 7-bit dynamic address to this target | Target stores new address |
| 5 | Repeat for next unaddressed device | Until all targets assigned |

**Provisional ID (48-bit):**

| Field | Bits | Description |
|:-----:|:----:|-------------|
| MIPI Manufacturer ID | 15 | Identifies silicon vendor |
| Part ID | 16 | Device part number |
| Instance ID | 4 | Distinguishes multiple same-device on bus |
| Random ID | 8 | Random for uniqueness |
| Type ID | 5 | Device type (sensor, bridge, hub, etc.) |

### 3.3 Common Command Codes (CCC)

| CCC | Name | Direction | Description |
|:---:|:----:|:---------:|-------------|
| 0x06 | SETDASA | Broadcast | Assign Dynamic Address using Static Address |
| 0x07 | ENTDAA | Broadcast | Enter Dynamic Address Assignment mode |
| 0x08 | DEFSLVS | Broadcast | Define List of Targets (for secondary controllers) |
| 0x09 | ENEC | Broadcast | Enable Events (IBI, Hot-Join) |
| 0x0A | DISEC | Broadcast | Disable Events |
| 0x0B | RSTDAA | Broadcast | Reset Dynamic Addresses (all targets lose address) |
| 0x20 | SETMWL | Direct | Set Max Write Length |
| 0x21 | SETMRL | Direct | Set Max Read Length |
| 0x8E | GETPID | Direct | Get Provisional ID |
| 0x8F | GETBCR | Direct | Get Bus Characteristics Register |
| 0x90 | GETDCR | Direct | Get Device Characteristics Register |
| 0x91 | GETSTATUS | Direct | Get Target Status |

### 3.4 Hot-Join

| Event | Description |
|:-----:|-------------|
| New device connected | Target drives SDA low during bus idle (like IBI) with Hot-Join event type |
| Controller detects | Recognizes Hot-Join request (different from IBI by protocol identification) |
| DAA initiated | Controller runs ENTDAA specifically for the new device |
| Address assigned | New device gets dynamic address; now fully operational |
| **Use case** | Hot-pluggable sensor boards; modular IoT systems; docking stations with sensors |

---

## Chapter 4 — I3C Timing & Performance

### 4.1 Speed Comparison

| Mode | Clock Rate | Data Rate | Latency (1 byte) |
|:----:|:----------:|:---------:|:-----------------:|
| I2C Standard (SM) | 100 kHz | 100 kbps | ~90 µs |
| I2C Fast (FM) | 400 kHz | 400 kbps | ~22 µs |
| I2C FM+ | 1 MHz | 1 Mbps | ~9 µs |
| **I3C SDR** | **12.5 MHz** | **12.5 Mbps** | **~0.7 µs** |
| **I3C HDR-DDR** | **25 MHz** | **50 Mbps** | **~0.2 µs** |
| **I3C HDR-BT** | **50 MHz** | **100 Mbps** | **~0.1 µs** |
| SPI (for comparison) | 10-50 MHz | 10-50 Mbps | ~0.1-0.5 µs |

### 4.2 Bus Capacitance & Devices

| Parameter | I2C | I3C |
|:---------:|:---:|:---:|
| Max bus capacitance | 400 pF (FM/FM+) | **50 pF** (push-pull; lower for faster edges) |
| Max devices | Limited by address space (112 unique 7-bit) | Limited by capacitance (~10-20 devices practical) |
| Pull-up resistor | Required (1-10 kΩ typical) | Optional (only if I2C devices present) |
| Rise time (@ 400 pF) | 300 ns (I2C FM) | ~3-5 ns (push-pull at 12.5 MHz) |

**I3C capacitance limit:** Push-pull drivers can charge 50 pF fast enough for 12.5 MHz edges. More capacitance (more devices) → slower edges → must reduce clock speed.

---

## Chapter 5 — I3C Basic vs. Full I3C

| Feature | I3C Basic | I3C Full (v1.1.1) |
|:-------:|:---------:|:------------------:|
| **SDR mode** | Yes (12.5 MHz) | Yes |
| **HDR-DDR** | Optional | Yes |
| **HDR-TSP/TSL/BT** | No | Yes |
| **IBI (In-Band Interrupt)** | Yes | Yes |
| **Dynamic Address Assignment** | Yes | Yes |
| **Hot-Join** | Yes | Yes |
| **Multi-Controller** | No (single controller only) | Yes (multiple controllers; handoff) |
| **Group addressing** | No | Yes |
| **Target-initiated CCC** | No | Yes |
| **Bridge/Hub support** | No | Yes |
| **Target audience** | IoT; consumer; simple sensor nodes | Automotive; mobile SoC; complex systems |
| **Implementation complexity** | Low (IP core: ~5K gates) | Higher (~20-50K gates) |

---

## Chapter 6 — I3C in Modern SoCs

### 6.1 SoC Integration

```mermaid
graph TB
    subgraph "Mobile SoC (e.g., Qualcomm Snapdragon)"
        I3C_CTRL[I3C Controller(s)<br/>━━━━━━━━━━━<br/>• 2-4 I3C master ports<br/>• 12.5 MHz SDR; HDR-DDR capable<br/>• HCI-compliant register interface<br/>• DMA support for bulk transfers<br/>• IBI interrupt to CPU/DSP]
        
        subgraph "I3C Bus 0 (Motion Sensors)"
            ACCEL[Accelerometer<br/>(I3C; IBI: motion)]
            GYRO[Gyroscope<br/>(I3C; IBI: rotation)]
            MAG[Magnetometer<br/>(I2C legacy; 400 kHz)]
        end
        
        subgraph "I3C Bus 1 (Environmental)"
            BARO[Barometer<br/>(I3C; IBI: threshold)]
            TEMP[Temperature<br/>(I3C)]
            HUMID[Humidity<br/>(I2C legacy)]
            ALS[Ambient Light<br/>(I3C; IBI: change)]
        end
        
        subgraph "I3C Bus 2 (Camera/Touch Control)"
            TOUCH[Touch Controller<br/>(I3C; IBI: touch event)]
            CAM_CTRL[Camera AF Controller<br/>(I2C legacy; control only)]
        end
    end
    
    I3C_CTRL --- ACCEL
    I3C_CTRL --- GYRO
    I3C_CTRL --- MAG
    I3C_CTRL --- BARO
    I3C_CTRL --- TEMP
    I3C_CTRL --- HUMID
    I3C_CTRL --- ALS
    I3C_CTRL --- TOUCH
    I3C_CTRL --- CAM_CTRL
```

### 6.2 I3C vs. SPI for Sensor Interface

| Dimension | I3C | SPI |
|:---------:|:---:|:---:|
| **Wires per device** | 2 (shared bus; all devices on same SCL/SDA) | 4 + 1 CS per additional device |
| **10 sensors** | 2 wires total (shared) | 4 + 10 CS = **14 wires** |
| **Speed** | 12.5-100 Mbps | 10-50 Mbps (depends on implementation) |
| **Interrupt** | In-band (IBI on SDA; no extra wire) | External GPIO per device |
| **Discovery** | Dynamic (DAA; Hot-Join) | None (hardcoded CS lines) |
| **Duplex** | Half-duplex (shared SDA) | Full-duplex (MOSI + MISO) |
| **Bus topology** | Multi-drop (many devices on 2 wires) | Point-to-point (1 CS per device) |
| **Use case** | Many sensors with moderate speed | Single high-speed device (flash, ADC) |

**Rule of thumb:** If you have 3+ sensors → I3C saves wires and GPIOs. If you have 1 device needing max speed and full-duplex → SPI.

---

## Chapter 7 — Comparison with Competing Interfaces

| Interface | Speed | Wires | Interrupt | Discovery | Devices/Bus | Use Case |
|:---------:|:-----:|:-----:|:---------:|:---------:|:-----------:|----------|
| **I2C (FM+)** | 1 Mbps | 2 + pullups | External GPIO | None | ~20 | Legacy sensors; EEPROM; configuration |
| **I3C SDR** | 12.5 Mbps | 2 | In-Band (IBI) | DAA + Hot-Join | ~10-20 | Modern sensors; IoT; mobile |
| **I3C HDR-DDR** | 50 Mbps | 2 | IBI | DAA | ~10 | High-throughput sensor data |
| **SPI** | 10-50 Mbps | 4 + 1/device | External GPIO | None | 1 per CS | Flash memory; ADC; display; single device |
| **UART** | 0.115-12 Mbps | 2 (TX/RX) | N/A | None | Point-to-point | Debug; GPS; BLE module |
| **1-Wire** | ~16 kbps | 1 + GND | None | ROM search | Many | Temperature (DS18B20); low cost |
| **CAN FD** | 8 Mbps | 2 (CANH/CANL) | N/A | None | ~30 | Automotive control; industrial |
| **SDIO** | 208 Mbps (UHS-I) | 6 | In-band | Card detect | 1-2 | WiFi/BT cards; SD cards |

---

## Chapter 8 — Architecture Diagrams

### 8.1 I3C Frame Structure (SDR Mode)

```mermaid
sequenceDiagram
    participant CTRL as Controller
    participant TGT as Target (Addr 0x08)
    
    Note over CTRL,TGT: Write transfer: Controller writes 2 bytes to Target register 0x10
    
    CTRL->>TGT: START + Address(0x08) + W(0) + [push-pull header]
    TGT->>CTRL: ACK (T-bit = 0: no IBI; continue)
    CTRL->>TGT: Data byte 1: 0x10 (register address)
    TGT->>CTRL: T-bit (parity: odd parity of data)
    CTRL->>TGT: Data byte 2: 0x55 (register value)
    TGT->>CTRL: T-bit
    CTRL->>TGT: STOP
    
    Note over CTRL,TGT: Read transfer: Controller reads 4 bytes from Target
    
    CTRL->>TGT: START + Address(0x08) + R(1)
    TGT->>CTRL: ACK (T-bit)
    TGT->>CTRL: Data byte 1 (sensor data MSB)
    CTRL->>TGT: T-bit (ACK from controller)
    TGT->>CTRL: Data byte 2
    CTRL->>TGT: T-bit
    TGT->>CTRL: Data byte 3
    CTRL->>TGT: T-bit
    TGT->>CTRL: Data byte 4 (sensor data LSB)
    CTRL->>TGT: T-bit (with end-of-data indicator)
    CTRL->>TGT: STOP
```

### 8.2 IBI Arbitration

```mermaid
graph TB
    subgraph "IBI Arbitration (Multiple Targets)"
        BUS_IDLE[Bus Idle<br/>SCL=HIGH, SDA=HIGH]
        
        IBI_REQ[Targets with pending IBI<br/>pull SDA LOW simultaneously]
        
        ARB[Address Arbitration<br/>━━━━━━━━━━━<br/>• Each target drives its dynamic address<br/>• Bit-by-bit on SDA (LSB first)<br/>• Lower address wins (drives 0 when<br/>  other drives 1 → winner has lower value)<br/>• Losing target releases bus]
        
        WIN[Winning Target<br/>━━━━━━━━━━━<br/>• Sends Mandatory Data Byte<br/>  (interrupt status/type)<br/>• Controller reads and handles]
        
        NEXT[Next IBI<br/>━━━━━━━━━━━<br/>• Losing target retries on next IBI slot<br/>• Or controller polls with GETSTATUS]
    end
    
    BUS_IDLE --> IBI_REQ --> ARB --> WIN
    ARB --> NEXT
```

---

## Chapter 9 — Case Studies

### 9.1 Smartphone: I2C → I3C Migration

| Aspect | Detail |
|--------|--------|
| **System** | Flagship smartphone; 12 sensors (accelerometer, gyroscope, magnetometer, barometer, proximity, ambient light, temperature, humidity, UV, Hall effect, grip, step counter) |
| **Before (I2C)** | 12 sensors on 2 I2C buses. 8 sensors need interrupt → 8 GPIO pins consumed. Total wires: 2 (I2C bus 0) + 2 (I2C bus 1) + 8 (GPIO interrupts) = 12 signal lines. Speed: 400 kHz max (most sensors). Polling some sensors (no GPIO left). |
| **After (I3C)** | 12 sensors on 2 I3C buses (6 per bus). All use IBI (in-band interrupt) → **0 GPIOs needed**. Total wires: 2 (I3C bus 0) + 2 (I3C bus 1) = 4 signal lines. Speed: 12.5 MHz (12.5× faster than I2C FM). Dynamic addressing: no address conflicts (all auto-assigned). |
| **Benefits** | (1) Saved 8 GPIOs → freed for other functions (or smaller SoC package). (2) 12.5× faster communication → sensor data arrives sooner → better responsiveness. (3) Lower power: push-pull (no pull-up current drain); faster transfers (bus active for less time). (4) Hot-Join: if sensor module disconnected (repair), system reconfigures automatically. |
| **Migration effort** | Sensor vendors released I3C-compatible sensors (same die; I3C PHY added). SoC already had I3C controller (Snapdragon Gen 2+). Driver changes: use I3C kernel framework (Linux kernel i3c subsystem) instead of i2c. DT bindings updated. 3 months engineering effort. |

### 9.2 Automotive: I3C for Sensor Fusion

| Aspect | Detail |
|--------|--------|
| **System** | Automotive sensor fusion ECU: combines IMU, pressure, temperature, radar trigger |
| **Requirement** | Deterministic sensor data delivery; low latency; safety-relevant (ASIL-B) |
| **I3C design** | (1) IMU (accelerometer + gyroscope combo): I3C HDR-DDR mode at 50 Mbps. Delivers 6-axis data at 4 kHz. IBI when new sample ready. (2) Pressure sensors × 4: I3C SDR at 12.5 MHz. Each IBIs when threshold crossed. (3) Temperature × 2: I3C SDR. Periodic polling (no IBI needed). (4) Radar trigger: I3C timing-critical CCC command to synchronize radar pulse with IMU timestamp. |
| **Functional safety** | I3C parity bit per byte (SDR mode): detects single-bit errors. HDR-DDR CRC: detects multi-bit errors. Controller monitors bus health: if target doesn't respond within timeout → fault report. Hot-Join used for replaceable sensor modules (field service). |
| **Timing** | IBI latency: <5 µs (controller responds within 5 µs of IBI assertion). Read 12 bytes (6-axis × 16-bit): 12 bytes × 8 bits / 12.5 MHz = ~8 µs. Total sensor sample latency: <15 µs (IBI + read). vs. I2C: same read at 400 kHz = ~240 µs. **16× improvement.** |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **I3C v2.0** | 2024-2025 | Higher HDR speeds (200+ Mbps); improved multi-controller; group commands |
| **I3C in automotive** | 2024+ | AEC-Q100 qualified sensors with I3C; ASIL-B rated I3C controllers |
| **I3C replacing SPI for some uses** | 2025+ | Multi-drop advantage over SPI; I3C HDR-BT at 100 Mbps approaches SPI speed |
| **I3C over connector** | 2025+ | Standardized I3C connector for modular IoT; hot-pluggable sensor boards |
| **I3C + CSI-2 USL** | 2024+ | Unified Serial Link: I3C control + CSI-2 data on same wires (camera control) |
| **I3C for DIMM SPD** | 2023+ | JEDEC DDR5 uses I3C for SPD hub (replaces I2C SMBus for DIMM configuration) |
| **I3C in wearables** | 2024+ | Ultra-low power I3C sensors for watches, earbuds, health monitors |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is I3C and what are its main advantages over I2C?

**A:** I3C (Improved Inter-Integrated Circuit) is MIPI's next-generation 2-wire sensor bus, designed as a backward-compatible replacement for I2C. It uses the same SCL and SDA wires.

**Main advantages:**

| Feature | I2C | I3C |
|:-------:|:---:|:---:|
| Speed | 400 kHz (FM) / 1 MHz (FM+) | **12.5 MHz** (SDR) — 12-30× faster |
| Interrupt | Requires extra GPIO per sensor | **In-Band Interrupt (IBI)** — on same SDA wire |
| Addressing | Static (set at design; risk of conflicts) | **Dynamic** (auto-assigned at runtime; no conflicts) |
| Hot-plug | Not supported (must know devices at boot) | **Hot-Join** (new device announces itself; gets address) |
| Driver | Open-drain only (slow rise; needs pull-up) | **Push-pull** (fast edges; no pull-up for I3C devices) |
| Power | Pull-ups draw current continuously | Lower (push-pull; faster → bus active for less time) |
| Error handling | No standard error detection | **Parity** (SDR); **CRC** (HDR modes) |
| Backward compatible | — | **Yes** (I2C devices coexist on same bus) |

**Practical impact:** A smartphone with 10 sensors saves 10 GPIO pins (no separate interrupt wires). The bus is 12× faster, so the CPU spends less time communicating with sensors → better battery life and responsiveness.

### Tier 2: Mid-Level

**Q2:** Explain how In-Band Interrupt (IBI) works in I3C. How does the controller handle multiple simultaneous IBIs?

**A:**

**IBI mechanism:**

When a target (sensor) has data ready or an event to report:
1. Target waits for bus to be idle (SCL HIGH, SDA HIGH)
2. Target pulls SDA LOW (just like I2C start condition)
3. Controller detects SDA transition → IBI is pending
4. Controller generates clock on SCL
5. Target drives its **dynamic address** on SDA (bit by bit, during clock cycles)
6. Controller reads the address → identifies which target is interrupting
7. If controller ACKs: target sends **Mandatory Data Byte** (MDB) — contains interrupt cause
8. Controller can then read more data from target (or handle later)
9. If controller NACKs: target must defer (retry later)

**Multiple simultaneous IBIs (arbitration):**

When 2+ targets try IBI simultaneously:
- Both pull SDA low at the same time
- Both drive their dynamic address bit-by-bit
- Lower address wins: if target A drives '0' and target B drives '1' on same bit position → SDA stays LOW (target B sees SDA doesn't match what it drove) → target B loses arbitration and releases bus
- Winner completes its IBI; loser retries on next IBI opportunity

**This is exactly like I2C multi-master arbitration** — same electrical mechanism reused for IBI priority.

**Controller configuration:**
- Controller can disable IBI for specific targets (DISEC CCC)
- Controller can enable IBI only for specific targets (ENEC CCC)
- Maximum IBI count configurable (avoid IBI storms)
- MDB byte tells controller the interrupt type (no need to read registers immediately)

### Tier 3: Senior

**Q3:** Design the sensor subsystem for an IoT edge device with 20 sensors, considering I3C bus topology, performance, power, error handling, and system-level integration.

**A:**

**Sensor inventory:**

| Category | Sensors | Count | Data Rate | IBI Needed? |
|:--------:|:-------:|:-----:|:---------:|:-----------:|
| Motion | Accel, Gyro, Mag | 3 | 4 kHz × 6-axis × 16-bit = 384 kbps each | Yes (motion detect) |
| Environmental | Temp, Humidity, Pressure, Gas (VOC) | 4 | 1 Hz each (low) | Yes (threshold alarm) |
| Optical | Ambient light, Proximity, UV, IR | 4 | 10 Hz | Yes (event change) |
| Audio | MEMS microphone (control only; audio via I2S) | 1 | Control: rare | No |
| Power | Current monitor, Battery gauge | 2 | 1 Hz | Yes (critical alert) |
| Location | GPS module (control only; NMEA via UART) | 1 | Control: rare | No |
| User | Touch sensor, Haptic driver | 2 | Touch: 200 Hz | Yes (touch event) |
| Health | PPG (optical heart rate), SpO2 | 2 | 100 Hz × 3-channel × 24-bit | Yes (measurement done) |
| Radar | 60 GHz presence sensor (control) | 1 | Control: periodic | Yes (presence change) |
| **Total** | | **20** | | 12 need IBI |

**Bus topology design:**

| Bus | Sensors | Bus Load | Rationale |
|:---:|:-------:|:--------:|-----------|
| **I3C Bus 0** | Motion (3) + Touch (1) + Haptic (1) | Medium (motion at 4 kHz) | Latency-critical: motion + touch → fastest response needed |
| **I3C Bus 1** | Environmental (4) + Power (2) + Radar (1) | Low (mostly 1 Hz) | Low-priority; can share with slow devices |
| **I3C Bus 2** | Optical (4) + Health (2) | Medium (health at 100 Hz) | Health sensors need consistent timing |
| **I2C Bus (legacy)** | Audio ctrl + GPS ctrl + 2 legacy sensors | Very low | Devices without I3C; 400 kHz sufficient |

**Why 3 buses (not 1):**
- Bus capacitance: 20 devices × ~5 pF each = 100 pF > 50 pF I3C limit
- IBI storm risk: 12 devices with IBI on same bus → high arbitration contention
- Isolation: mission-critical sensors (health) isolated from chatty sensors (motion)
- Fault containment: bus fault on Bus 0 doesn't affect Bus 1 health monitoring

**Power optimization:**

| Strategy | Implementation | Savings |
|:--------:|----------------|:-------:|
| IBI instead of polling | Only communicate when sensor has data (vs. polling every 10ms) | ~60% bus power reduction |
| HDR-DDR for bulk | Health sensor: 100 Hz × 6 bytes = transfer at 50 Mbps → very short active time | ~80% active time reduction |
| ULPS between transfers | Bus goes ultra-low power between IBIs | ~µW standby |
| Sensor sleep via CCC | ENTAS (Enter Activity State) CCC puts targets in deep sleep | ~nA per sensor when sleeping |
| Batch read | After IBI: read all pending data in single burst (fewer START/STOP overhead) | 15% overhead reduction |

**Error handling:**

| Error Type | Detection | Recovery |
|:----------:|:---------:|----------|
| Parity error (SDR) | Odd parity per byte (T-bit) | Controller retries transfer |
| CRC error (HDR) | Per-frame CRC-8 | Controller retries; if persistent → mark device faulty |
| Bus hang (stuck SDA) | Controller timeout | Issue Bus Reset (HDR Exit Pattern → STOP) |
| Target not responding | No ACK to address | Retry 3×; then issue Target Reset (RSTACT CCC) |
| IBI storm (malfunction) | IBI rate monitor (>100 IBIs/sec unexpected) | DISEC to that target; log fault; alert system |
| Address conflict | Shouldn't happen (DAA prevents) but detected if 2 targets respond to same address | Re-run RSTDAA + ENTDAA (full re-enumeration) |

**System integration (Linux):**

```c
// Device Tree example
i3c_master0: i3c@40000 {
    compatible = "vendor,i3c-controller";
    #address-cells = <3>;
    #size-cells = <0>;
    clock-frequency = <12500000>;  // 12.5 MHz SDR

    accel@0,0x6a,0x04400001 {
        // Provisional ID based addressing
        compatible = "vendor,i3c-accel";
        reg = <0 0x6a 0x04400001>;
        // IBI handled by i3c subsystem automatically
    };

    // Legacy I2C device on same bus
    legacy_mag@44 {
        compatible = "vendor,mag-sensor";
        reg = <0x44 0 0>;  // Static I2C address
    };
};
```

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
MIPI I3C — QUICK REFERENCE
═══════════════════════════════════════════

BASICS:
  2 wires: SCL + SDA (same as I2C; backward compatible)
  Speed: SDR 12.5 Mbps; HDR-DDR 50 Mbps; HDR-BT 100 Mbps
  Interrupt: In-Band (IBI) — no extra GPIO wire needed
  Addressing: Dynamic (auto-assigned by controller)
  Hot-plug: Hot-Join (device announces itself; gets address)
  Error: Parity (SDR); CRC (HDR)

═══════════════════════════════════════════
I3C vs I2C:
  Speed:      I3C 12.5 MHz vs I2C 1 MHz (12.5× faster)
  Interrupt:  I3C in-band vs I2C external GPIO
  Addressing: I3C dynamic vs I2C static (conflict-prone)
  Hot-plug:   I3C yes vs I2C no
  Driver:     I3C push-pull vs I2C open-drain only
  Compatible: I2C devices work on I3C bus (legacy mode)

═══════════════════════════════════════════
I3C MODES:
  SDR:       12.5 Mbps; open-drain start + push-pull data
  HDR-DDR:   50 Mbps; double-data-rate on SDA
  HDR-TSP:   25 Mbps; ternary symbol (3-level SDA)
  HDR-BT:    100 Mbps; bulk transfer (highest speed)
  I2C Legacy: 100K/400K/1M; open-drain (for old devices)

═══════════════════════════════════════════
KEY FEATURES:
  IBI: Target asserts SDA low → controller reads address → handles interrupt
  DAA: ENTDAA CCC → targets arbitrate → controller assigns 7-bit addresses
  Hot-Join: New device → pulls SDA → controller runs DAA for it
  CCC: Common Command Codes (bus-level commands: ENTDAA, ENEC, DISEC, RSTDAA)
  Provisional ID: 48-bit unique per device (manufacturer + part + instance)

═══════════════════════════════════════════
I3C Basic vs Full:
  Basic: SDR + IBI + DAA + Hot-Join (simple; IoT)
  Full: + HDR modes + multi-controller + group address (automotive; mobile SoC)

═══════════════════════════════════════════
BUS DESIGN RULES:
  Max capacitance: 50 pF (push-pull; for 12.5 MHz)
  Practical max devices: 10-20 per bus
  Multiple buses for: >20 devices, latency isolation, fault containment
  Pull-ups: needed ONLY if I2C legacy devices on bus
  Trace length: keep short (<20 cm for 12.5 MHz)

═══════════════════════════════════════════
USE CASES:
  Smartphone: 10-15 sensors on 2-3 I3C buses (saves 10+ GPIOs)
  Automotive: ASIL-B sensors with IBI; deterministic timing
  IoT/Wearable: Ultra-low power; few sensors; I3C Basic
  DDR5 DIMM: SPD hub uses I3C (replaces I2C SMBus)
  Laptop: Sensor hub + EC communication

═══════════════════════════════════════════
LINUX SUPPORT:
  Kernel subsystem: drivers/i3c/ (since Linux 5.0)
  Device Tree: i3c-master node; provisional-id based binding
  Userspace: /dev/i3c-* (for debug/test)
  Frameworks: IIO (Industrial I/O) for sensor drivers
```

---

*End of Document — 04_MIPI_I3C_Sensor_Interface.md*
