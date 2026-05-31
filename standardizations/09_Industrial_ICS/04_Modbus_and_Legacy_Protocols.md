# Modbus & Legacy Industrial Protocols

**Topic:** Modbus RTU/TCP, PROFIBUS, DeviceNet, HART, Foundation Fieldbus — Legacy Protocol Analysis  
**Standards:** Modbus Application Protocol (Modbus.org), IEC 61158 (PROFIBUS), ODVA DeviceNet, IEC 62591 (WirelessHART)  
**SDO:** Modbus Organization (now OPC Foundation), PI International, ODVA, FieldComm Group  
**Audience:** Automation engineers, OT security professionals, brownfield system integrators, protocol converters  
**Prerequisites:** Serial communication (RS-232/RS-485), networking basics, PLC programming fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event |
|------|-------|
| 1968 | First PLC (Modicon 084, Dick Morley) — proprietary communication |
| 1975 | Modbus RTU/ASCII published — first open industrial protocol |
| 1979 | PROFIBUS development begins (Germany, Siemens + universities) |
| 1983 | HART protocol developed (Rosemount/Emerson) |
| 1985 | Allen-Bradley Data Highway Plus |
| 1989 | PROFIBUS DP specification finalized |
| 1994 | DeviceNet specification (Allen-Bradley → ODVA) |
| 1994 | Foundation Fieldbus H1 specification |
| 1996 | Modbus TCP published (Modicon/Schneider) |
| 1999 | "Fieldbus Wars" era: PROFIBUS vs Foundation Fieldbus vs DeviceNet |
| 2000 | IEC 61158 unified standard (multi-protocol inclusion) |
| 2007 | WirelessHART (IEC 62591) — first wireless fieldbus standard |
| 2010 | Stuxnet exploits Modbus/S7 vulnerabilities |
| 2020 | Legacy protocols still ~40% of installed industrial devices |
| 2024 | Modbus still #1 by installed base (billions of devices) |

### 1.2 Why Legacy Protocols Still Matter

| Factor | Detail |
|--------|--------|
| Installed base | Billions of Modbus/HART/PROFIBUS devices globally |
| Lifecycle | 15-30 year plant lifecycles; replacement uneconomical |
| Simplicity | Easy to implement, debug, troubleshoot |
| Reliability | Proven over decades of operation |
| Brownfield | New projects must integrate with existing legacy systems |
| Security risk | Zero authentication/encryption = major OT vulnerability |
| Migration path | Understanding legacy is prerequisite for modernization |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Protocol Comparison Overview

| Protocol | Physical Layer | Data Rate | Distance | Topology | Security |
|----------|---------------|-----------|----------|----------|----------|
| Modbus RTU | RS-485 (2-wire) | 9600-115200 bps | 1200m | Line (multidrop) | None |
| Modbus TCP | Ethernet | 10/100 Mbps | Standard Ethernet | Star/tree | None (TLS optional) |
| PROFIBUS DP | RS-485 | 12 Mbps max | 100m@12M, 1200m@9.6k | Line | None |
| PROFIBUS PA | MBP (31.25 kbps) | 31.25 kbps | 1900m | Line/tree | None |
| DeviceNet | CAN (2-wire) | 125/250/500 kbps | 500m@125k | Line (trunk/drop) | None |
| HART | 4-20mA + FSK | 1200 bps | 3000m | Point-to-point/multidrop | None |
| Foundation Fieldbus H1 | IEC 61158-2 | 31.25 kbps | 1900m | Line/tree | None |
| Foundation Fieldbus HSE | Ethernet | 100 Mbps | Standard | Star/tree | None |

### 2.2 OSI Layer Mapping

```mermaid
graph TB
    subgraph "Protocol Stack Comparison"
        subgraph "Modbus RTU"
            M_APP[Application<br/>(Function Codes)]
            M_DL[Data Link<br/>(RTU framing + CRC16)]
            M_PH[Physical<br/>(RS-485)]
        end
        
        subgraph "Modbus TCP"
            MT_APP[Application<br/>(Function Codes)]
            MT_MBAP[MBAP Header<br/>(TCP mapping)]
            MT_TCP[TCP/IP<br/>(standard stack)]
            MT_ETH[Ethernet<br/>(standard)]
        end
        
        subgraph "PROFIBUS DP"
            P_APP[Application<br/>(DP services)]
            P_FDL[FDL<br/>(token passing + polling)]
            P_PH[Physical<br/>(RS-485)]
        end
        
        subgraph "HART"
            H_APP[Application<br/>(commands)]
            H_DL[Data Link<br/>(polling, burst)]
            H_PH[Physical<br/>(4-20mA + 1200Hz FSK)]
        end
    end
```

---

## Chapter 3 — Technical Deep Dive

### 3.1 Modbus Protocol

**Modbus RTU Frame:**

| Field | Size | Description |
|-------|------|-------------|
| Slave Address | 1 byte | 1-247 (0 = broadcast) |
| Function Code | 1 byte | Operation type |
| Data | N bytes | Request/response data |
| CRC | 2 bytes | CRC-16 (error detection) |

**Common Function Codes:**

| Code | Name | Description |
|------|------|-------------|
| 0x01 | Read Coils | Read discrete outputs (bits) |
| 0x02 | Read Discrete Inputs | Read discrete inputs (bits) |
| 0x03 | Read Holding Registers | Read output registers (16-bit) |
| 0x04 | Read Input Registers | Read input registers (16-bit) |
| 0x05 | Write Single Coil | Write one discrete output |
| 0x06 | Write Single Register | Write one register |
| 0x0F | Write Multiple Coils | Write multiple discrete outputs |
| 0x10 | Write Multiple Registers | Write multiple registers |
| 0x17 | Read/Write Multiple Registers | Combined read+write |

**Modbus TCP (MBAP Header):**

| Field | Size | Description |
|-------|------|-------------|
| Transaction ID | 2 bytes | Request/response matching |
| Protocol ID | 2 bytes | 0x0000 = Modbus |
| Length | 2 bytes | Remaining bytes in frame |
| Unit ID | 1 byte | Slave address (for gateways) |
| Function Code | 1 byte | Same as RTU |
| Data | N bytes | Same as RTU |

### 3.2 Modbus Security Vulnerabilities

| Vulnerability | Impact | IEC 62443 Mitigation |
|--------------|--------|---------------------|
| No authentication | Anyone on network can read/write registers | Network segmentation (zones), IDS |
| No encryption | All data visible (sniffing) | VPN or data diode for remote access |
| No integrity check | Man-in-the-middle modification | Application whitelisting, anomaly detection |
| Broadcast function (0) | DoS potential (all devices respond) | Firewall blocking broadcast to critical zones |
| Write to any register | Unauthorized process changes | OT-aware firewall (function code filtering) |
| No device authentication | Rogue device injection | Port security, MAC filtering |

**Modbus/TCP Security extension (2018, RFC draft):** TLS wrapper over port 802 (mbaps://) — limited adoption.

### 3.3 PROFIBUS DP

**Architecture:**
- Master Class 1: cyclic data exchange (PLC)
- Master Class 2: acyclic parameterization (engineering tool)
- Slaves: field devices (I/O, drives, transmitters)
- Token: masters share bus via token-passing; slaves polled by masters

**Frame Structure:**

| Field | Content |
|-------|---------|
| SD (Start Delimiter) | 0x10 (fixed), 0x68 (variable), 0xA2 (token) |
| DA (Destination Address) | 0-125 (slave) or master |
| SA (Source Address) | Sender address |
| FC (Function Code) | Service type |
| Data (DSAP/SSAP) | Service access points + user data |
| FCS | Frame Check Sequence |
| ED | End Delimiter (0x16) |

**PROFIBUS DP cycle:**
1. Master sends OUTPUT data to slave
2. Slave responds with INPUT data
3. Master polls next slave
4. Cycle time = (number of slaves) × (telegram time + slave response time)

### 3.4 HART Protocol

| Feature | Detail |
|---------|--------|
| Physical | Superimposed on 4-20mA analog signal (FSK modulation) |
| Frequencies | 1200 Hz (mark/1), 2200 Hz (space/0) — Bell 202 FSK |
| Coexistence | Analog + digital on same 2 wires simultaneously |
| Data rate | 1200 bps (slow but adequate for process variables) |
| Commands | Universal (all devices), Common Practice, Device-Specific |
| Addressing | Polling (0-15 devices) or point-to-point (address 0) |
| Multi-variable | Can transmit 4+ variables per device (digital) |
| WirelessHART | IEEE 802.15.4 radio + mesh networking (IEC 62591) |

### 3.5 DeviceNet

| Feature | Detail |
|---------|--------|
| Physical | CAN (Controller Area Network) bus |
| Data rates | 125 kbps (500m), 250 kbps (250m), 500 kbps (100m) |
| Max nodes | 64 per network |
| Protocol | CIP (Common Industrial Protocol) over CAN |
| Communication | Polling, cyclic, change-of-state, explicit messaging |
| Power | 24V power on same cable (thick/thin cable variants) |
| Object model | CIP objects (same as EtherNet/IP at application layer) |
| Configuration | EDS files (Electronic Data Sheet) |

---

## Chapter 4 — Implementation Guide

### 4.1 Modbus Implementation

| Component | Options |
|-----------|---------|
| Master (polling) | PLC (built-in), SCADA (Kepware, Ignition), Python (pymodbus) |
| Slave (device) | Microcontroller (STM32 + libmodbus), PLC, smart sensor |
| Serial (RTU) | RS-485 transceiver (MAX485, ADM485), 2/4 wire |
| TCP | Standard Ethernet + TCP/IP stack |
| Gateway | Serial-to-TCP converter (Moxa, Digi, Anybus) |
| Testing | Modbus Poll/Slave (Windows), mbpoll (Linux), QModMaster |

### 4.2 Modbus Register Map Design (Best Practice)

| Address Range | Convention | Data |
|---------------|-----------|------|
| 0-99 | System/Status | Device state, firmware version, serial number |
| 100-199 | Configuration | Setpoints, parameters, modes |
| 200-299 | Process Variables | Temperature, pressure, flow (read-only) |
| 300-399 | Alarms/Diagnostics | Alarm status, error codes |
| 400-499 | Counters/Totals | Production counts, runtime hours |
| 500+ | Application-specific | Custom data |

### 4.3 Migration from Legacy to Modern

| Legacy Protocol | Migration Path | Approach |
|----------------|---------------|---------|
| Modbus RTU → IP | Modbus TCP gateway (Moxa, Anybus) | Transparent protocol conversion |
| Modbus → OPC UA | OPC UA gateway (Kepware, Softing) | Add semantics + security |
| PROFIBUS → PROFINET | PN/PB coupler (Siemens IE/PB Link) | PROFIBUS device behind PROFINET proxy |
| HART → OPC UA | HART multiplexer + OPC UA server | Digital data extraction |
| DeviceNet → EtherNet/IP | EtherNet/IP adapter with DeviceNet scanner | CIP protocol continuity |
| Foundation Fieldbus → Ethernet-APL | FF H1 → Ethernet-APL bridge | 2-wire Ethernet replacement |

---

## Chapter 5 — Certification & Compliance

### 5.1 Modbus

| Aspect | Detail |
|--------|--------|
| Certification body | None (open specification, no certification required) |
| Conformance testing | Modbus Conformance Test Specification (optional) |
| Logo/trademark | Modbus trademark (permission from OPC Foundation) |
| Interoperability | Varies (register maps are vendor-specific) |

### 5.2 PROFIBUS

| Aspect | Detail |
|--------|--------|
| Certification body | PI (PROFIBUS International) |
| Mandatory | Yes (for PROFIBUS/PROFINET logo use) |
| Test houses | PROFI Interface Center, accredited test labs |
| GSD file | Required (device description for engineering tools) |

### 5.3 HART

| Aspect | Detail |
|--------|--------|
| Certification body | FieldComm Group |
| Test specification | HART Communication Foundation test procedures |
| DD (Device Description) | Required for interoperability |
| Registration | Device must be registered with FieldComm Group |

---

## Chapter 6 — Regional & Domain Variants

| Region | Legacy Installed Base |
|--------|---------------------|
| North America | Modbus (dominant in HVAC, building), DeviceNet, HART |
| Europe | PROFIBUS (dominant in manufacturing), HART (process) |
| Middle East (O&G) | HART + Foundation Fieldbus (process industry) |
| Asia (manufacturing) | Modbus TCP + CC-Link (Japan), PROFIBUS |
| Process industry (global) | HART (#1 field device protocol, 40M+ devices) |
| Building automation | Modbus RTU (BACnet growing, Modbus legacy) |
| Power/Utility | Modbus RTU (substations), DNP3 |
| Water/Wastewater | Modbus RTU/TCP (dominant) |
| Discrete manufacturing | PROFIBUS DP (Europe), DeviceNet (Americas) |

---

## Chapter 7 — Comparison: Legacy vs Modern

| Dimension | Modbus | PROFIBUS DP | HART | EtherCAT | OPC UA |
|-----------|--------|-------------|------|----------|--------|
| Year | 1975 | 1989 | 1983 | 2003 | 2006 |
| Data rate | 9.6-115.2 kbps (RTU) / 100Mbps (TCP) | 12 Mbps | 1.2 kbps | 100 Mbps | Varies |
| Security | None | None | None | Limited | Full (TLS, X.509) |
| Information model | Register-based (no semantics) | GSD (limited) | DD (good for device) | ESI (device) | Full semantic model |
| Device count | 247 (RTU) / unlimited (TCP) | 126 per segment | 15 (multidrop) / 1 (P2P) | 65,535 | Unlimited |
| Determinism | No | Yes (token + polling) | No | Yes (sub-μs) | Yes (with TSN) |
| Installed base | Billions | ~50 million nodes | 40+ million devices | Growing | Growing |
| Future | Maintained, not evolving | Stable (legacy support only) | WirelessHART growing | Active development | Active development |
| Migration | Gateways to OPC UA/MQTT | PROFINET proxy | Ethernet-APL | — (is target) | — (is target) |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Modbus RTU Network

```mermaid
graph TB
    subgraph "Modbus RTU Network (RS-485 Bus)"
        MASTER[Modbus Master<br/>(PLC/SCADA)<br/>Address: N/A<br/>Polls all slaves]
        
        S1[Slave 1<br/>VFD (Drive)<br/>Address: 1<br/>Registers: Speed, Torque]
        S2[Slave 2<br/>Power Meter<br/>Address: 2<br/>Registers: V, I, P, E]
        S3[Slave 3<br/>Temperature TX<br/>Address: 3<br/>Registers: Temp, Status]
        S4[Slave 4<br/>Valve Actuator<br/>Address: 4<br/>Coils: Open, Close]
    end
    
    MASTER --- S1
    MASTER --- S2
    MASTER --- S3
    MASTER --- S4
    
    style MASTER fill:#f9f,stroke:#333
```

### 8.2 HART Superimposed Communication

```mermaid
graph LR
    subgraph "HART Communication"
        SENSOR[HART Transmitter<br/>4-20mA = Process Variable<br/>FSK = Digital Data]
        WIRE[2-Wire Cable<br/>4-20mA analog +<br/>1200/2200 Hz FSK digital<br/>(superimposed)]
        PLC_H[PLC/DCS<br/>Reads 4-20mA (fast)<br/>+ HART digital (slow)]
        HHELD[HART Handheld<br/>Configuration<br/>Calibration<br/>Diagnostics]
    end
    
    SENSOR --> WIRE
    WIRE --> PLC_H
    HHELD -.->|"Connects in parallel"| WIRE
```

### 8.3 Brownfield Integration Architecture

```mermaid
graph TB
    subgraph "Modern Layer"
        CLOUD[Cloud / MES<br/>OPC UA / MQTT]
        SCADA_M[Modern SCADA<br/>OPC UA Client]
    end
    
    subgraph "Gateway Layer"
        GW1[Protocol Gateway<br/>Modbus → OPC UA<br/>(Kepware / Softing)]
        GW2[PROFIBUS → PROFINET<br/>Proxy (IE/PB Link)]
        GW3[HART Multiplexer<br/>→ OPC UA]
    end
    
    subgraph "Legacy Field Layer"
        MB1[Modbus RTU<br/>VFDs, meters]
        PB1[PROFIBUS DP<br/>Drives, I/O]
        HT1[HART<br/>Transmitters]
    end
    
    CLOUD --> SCADA_M
    SCADA_M --> GW1
    SCADA_M --> GW2
    SCADA_M --> GW3
    GW1 --> MB1
    GW2 --> PB1
    GW3 --> HT1
```

---

## Chapter 9 — Case Studies & Failure Analysis

### 9.1 Stuxnet — Modbus/S7 Protocol Exploitation

| Aspect | Detail |
|--------|--------|
| Protocol exploited | S7comm (Siemens proprietary, similar concepts to Modbus) |
| Vulnerability | No authentication on PLC communication |
| Attack method | Man-in-the-middle: replayed "normal" data to SCADA while modifying centrifuge commands |
| Modbus relevance | Same vulnerability class — any unauthenticated protocol can be subverted |
| Industry impact | Accelerated IEC 62443 development; OT security market created |

### 9.2 Oldsmar Water Treatment (2021)

| Aspect | Detail |
|--------|--------|
| System | Water treatment SCADA (TeamViewer + Modbus/proprietary HMI) |
| Attack | Remote access via TeamViewer → changed sodium hydroxide setpoint from 100 ppm to 11,100 ppm |
| Protocol role | SCADA command sent to PLC (likely Modbus TCP or proprietary) — no authentication |
| Detection | Operator noticed mouse moving and reversed changes immediately |
| Lessons | Remote access without MFA + no write protection on critical setpoints = disaster |
| IEC 62443 gap | Zero segmentation, shared passwords, no monitoring |

### 9.3 Natural Gas Pipeline — Modbus Vulnerability (Generic)

| Aspect | Detail |
|--------|--------|
| Scenario | RTUs connected via Modbus TCP over cellular/VSAT |
| Vulnerability | Modbus TCP open on public IP (SHODAN-discoverable) |
| Risk | Attacker could write to valve control registers (open/close) |
| Mitigation | VPN tunnel, OT firewall (function code filtering), IDS monitoring |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Impact on Legacy |
|-------|-----------------|
| Ethernet-APL | Replaces 4-20mA/HART/PROFIBUS PA with 2-wire Ethernet (same cable, 10 Mbps) |
| Modbus Security (TLS) | Modbus/TCP with TLS (mbaps://) — very slow adoption |
| HART-IP | HART over Ethernet (digital-only, faster) |
| Gateway proliferation | More protocol converters (legacy→OPC UA/MQTT) rather than rip-and-replace |
| Digital twin integration | Even legacy data connected to digital twin via gateways |
| OT security mandates | NIS2, CRA forcing security around legacy (compensating controls) |
| PROFIBUS support mode | PI maintaining PROFIBUS but no new features (frozen) |
| IO-Link replacing discrete I/O | Smart sensors replacing simple Modbus discrete |
| End-of-life planning | 1975-era Modbus devices now 50 years old — lifecycle management |
| NAMUR NOA | Second communication channel (OPC UA) alongside legacy 4-20mA/HART |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** Explain Modbus RTU communication — how does a master read a temperature from a slave?  
**A:** **Modbus RTU is master-slave, half-duplex, serial protocol (RS-485):** The master initiates ALL communication; slaves only respond when addressed. **Example: Read temperature from slave address 3, holding register 200:** (1) Master builds request frame: `[03][03][00][C8][00][01][CRC][CRC]` — Slave=3, Function=03 (Read Holding Registers), Start=0x00C8 (register 200), Quantity=1, CRC16. (2) Master sends frame on RS-485 bus (all slaves hear it). (3) Only slave 3 responds (others ignore because address≠theirs). (4) Slave 3 builds response: `[03][03][02][01][90][CRC][CRC]` — Slave=3, Function=03, Byte count=2, Data=0x0190 (400 decimal, meaning 40.0°C if scale factor=10). (5) Master receives response within timeout (T3.5 character silence marks frame boundary). **Key limitations:** Only master can initiate (no slave-initiated alerts), no timestamps in protocol, no authentication (any device on RS-485 bus can send frames), register meaning is vendor-specific (register 200 = temperature only because vendor documented it — no standardized information model).

### Tier 2: Mid-Level

**Q2:** You're integrating a brownfield plant with 200 Modbus RTU devices, 50 PROFIBUS DP devices, and 100 HART transmitters into a modern OPC UA-based system. Design the gateway architecture.  
**A:** **Architecture: Hierarchical gateway with zone segregation.** (1) **Modbus RTU segment (200 devices):** Group by RS-485 segments (max ~32 devices per segment due to electrical loading). Deploy 7 Modbus-to-OPC UA gateways (e.g., Softing dataFEED, Moxa MGate). Each gateway: polls its segment, publishes as OPC UA nodes. Register map: defined per device type (standardize naming convention). Polling rate: prioritize (critical = 1s, non-critical = 5s) to stay within RS-485 bandwidth. (2) **PROFIBUS DP (50 devices):** Use Siemens IE/PB Link PN IO or Softing pnGate PB as PROFINET proxy. From PROFINET proxy → OPC UA server (built-in to Siemens PLCs, or gateway). Alternative: direct PROFIBUS-to-OPC UA gateway (fewer hops). (3) **HART (100 transmitters):** HART multiplexer (Emerson HC900, MTL HART-to-Ethernet). Each multiplexer handles 16-64 HART devices. Multiplexer output: Modbus TCP or OPC DA → gateway to OPC UA. OR: Modern DCS with native HART I/O cards (extracts digital data directly). (4) **OPC UA aggregation layer:** Central OPC UA aggregation server (Prosys, Kepware, Unified Automation). Information model: standardized ObjectTypes per device class. Namespace: `ns=2; uri=http://plant.com/BrownfieldIntegration`. Security: X.509 certificates, RBAC, TLS (applied at this layer — legacy below has none). (5) **Security (IEC 62443):** Legacy zone (SL-T: 2 with compensating controls): network segmentation, IDS monitoring. Gateways in DMZ-like segment: protocol break point. OPC UA zone (SL-T: 3): full authentication, encryption, monitoring. Key compensating control: OT IDS (Nozomi/Claroty) monitoring Modbus/PROFIBUS traffic for anomalies. (6) **Data flow:** Field devices → gateways (legacy protocols, same wiring) → OPC UA aggregation → SCADA/MES/Cloud. No modification to field devices required (non-invasive).

### Tier 3: Senior

**Q3:** How do you secure a Modbus-based SCADA system that cannot be replaced for 10+ years, against increasingly sophisticated OT threats?  
**A:** **Constraint:** Modbus protocol has ZERO built-in security. Devices cannot be upgraded. Must protect without changing field devices or protocol. **Defense-in-depth approach:** (1) **Network segmentation (FR5 — IEC 62443):** Isolate Modbus segments in dedicated VLANs. One-to-one mapping: each Modbus serial segment on its own subnet/VLAN (if TCP). Firewall between Modbus zone and any other zone. Rule: only authorized master IP can communicate with slaves. (2) **Application-layer firewall (Modbus-aware):** Deploy OT-specific firewall (Tofino, Fortinet OT, Palo Alto OT). Configure function code filtering: allow only READ (0x01-0x04) from SCADA → devices. WRITE (0x05, 0x06, 0x0F, 0x10) only from engineering workstation (specific IP + time window). Block ALL function codes from unauthorized sources. Block Modbus broadcast (function code 0x08 diagnostic) from external sources. (3) **Monitoring and anomaly detection (FR6):** Deploy OT IDS (Dragos Platform, Nozomi Guardian, Claroty). Baseline: learn normal Modbus communication patterns (which master→which slave, which registers, how often). Alert on: new source addressing Modbus slaves, unusual function codes, register write to safety-critical setpoints, abnormal polling rate changes. SIEM integration: correlate OT alerts with IT events (lateral movement detection). (4) **Access control (FR1, FR2):** Engineering workstation: MFA login, application whitelisting, no internet access. Remote access: jump host in DMZ → VPN → MFA → session recording. No direct remote access to Modbus network (ever). Physical: locked panels for serial connections, USB port blocking. (5) **Data diode for monitoring:** Unidirectional gateway from Modbus zone → monitoring zone. Allows reading Modbus data for historian/analytics without any return path. Eliminates remote attack vector entirely for monitoring path. (6) **Compensating controls documentation:** IEC 62443 risk assessment: document that inherent SL-C = 0 (Modbus has no security). Compensating controls raise effective protection to SL-2 or SL-3. Re-assess annually (threat landscape evolves). (7) **Incident response preparation:** Maintain offline backup of PLC programs + Modbus device configurations. Know how to isolate segments physically (documented emergency procedures). Pre-positioned spare devices (in case of compromise requiring replacement). Forensic capability: packet capture at zone boundaries (ring buffer, 30-day retention).

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Modbus Function Codes

```
Read:
  0x01  Read Coils (bits, outputs)
  0x02  Read Discrete Inputs (bits, inputs)
  0x03  Read Holding Registers (16-bit, outputs)
  0x04  Read Input Registers (16-bit, inputs)

Write:
  0x05  Write Single Coil
  0x06  Write Single Register
  0x0F  Write Multiple Coils
  0x10  Write Multiple Registers

Combined:
  0x17  Read/Write Multiple Registers

Diagnostic:
  0x08  Diagnostics (loopback, clear counters)
```

### Modbus Data Model

```
Coils (bits):           0x0001–0xFFFF  (read/write, digital outputs)
Discrete Inputs (bits): 0x0001–0xFFFF  (read-only, digital inputs)
Holding Registers:      0x0001–0xFFFF  (read/write, analog outputs)
Input Registers:        0x0001–0xFFFF  (read-only, analog inputs)
```

### RS-485 Wiring Quick Reference

```
Max devices per segment: 32 (standard drivers) / 256 (low-power drivers)
Cable: Shielded twisted pair (STP), 120Ω characteristic impedance
Termination: 120Ω at BOTH ends of line
Distance vs speed: 9600bps=1200m, 115200bps=100m (approximate)
Biasing: 390Ω pull-up on A, 390Ω pull-down on B (idle state)
Grounding: Shield grounded at ONE end only (avoid ground loops)
```

### Legacy Protocol Security Posture

```
Protocol     Auth   Encrypt   Integrity   Security Status
Modbus RTU:  None   None      CRC16 only  INSECURE (by design)
Modbus TCP:  None   None*     None        INSECURE (*TLS extension exists)
PROFIBUS:    None   None      FCS         INSECURE (by design)
HART:        None   None      Checksum    INSECURE (by design)
DeviceNet:   None   None      CRC         INSECURE (by design)
Foundation:  None   None      CRC         INSECURE (by design)
→ ALL legacy protocols require compensating controls (IEC 62443)
```

### Migration Path Summary

```
Modbus RTU    → Gateway → Modbus TCP → OPC UA
PROFIBUS DP   → PN/PB Proxy → PROFINET → OPC UA
HART          → Multiplexer → HART-IP → OPC UA
DeviceNet     → Gateway → EtherNet/IP → OPC UA
4-20mA        → Ethernet-APL (2-wire Ethernet, same cable)
All legacy    → NAMUR NOA concept (second channel, non-invasive)
```

---

*End of Document — 04_Modbus_and_Legacy_Protocols.md*
