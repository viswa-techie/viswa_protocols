# Chapter 11 — Domain: Automotive & Embedded Systems Standards

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 11.1 Automotive Standards Ecosystem Overview

### The Unique Complexity of Automotive Standardization

The automotive industry represents one of the most densely standardized technology domains in existence. Unlike IT/web ecosystems where a single standard often dominates, automotive requires simultaneous compliance across multiple orthogonal axes:

```
┌─────────────────────────────────────────────────────────┐
│              AUTOMOTIVE STANDARDS LANDSCAPE              │
├──────────────┬──────────────┬───────────────────────────┤
│  SAFETY      │  SECURITY    │  COMMUNICATION            │
│  IEC 61508   │  ISO 21434   │  CAN / LIN / FlexRay      │
│  ISO 26262   │  UNECE R155  │  Automotive Ethernet      │
│  ISO 21448   │  UNECE R156  │  SOME/IP / DDS            │
├──────────────┼──────────────┼───────────────────────────┤
│  DIAGNOSTICS │  ARCHITECTURE│  REGULATORY               │
│  UDS (14229) │  AUTOSAR CP  │  UNECE WP.29              │
│  OBD-II      │  AUTOSAR AP  │  EU Type Approval         │
│  DoIP        │  OSEK/VDX    │  FMVSS (USA)              │
└──────────────┴──────────────┴───────────────────────────┘
```

### The Three Axes of Automotive Standardization

**Axis 1 — Safety:**
```
IEC 61508:2010 (root functional safety standard — all industries)
    ↓ (automotive derivation)
ISO 26262:2018 (Road Vehicles — Functional Safety — ASIL A/B/C/D)
    ↓ (extends to intended function hazards)
ISO 21448:2022 (SOTIF — Safety Of The Intended Functionality)
    ↓ (adds cybersecurity dimension)
ISO/SAE 21434:2021 (Cybersecurity Engineering)
```

**Axis 2 — Architecture:**
```
OSEK/VDX (1993–2005) — static task scheduling, minimal OS
    ↓
AUTOSAR Classic Platform (2003–present) — component-based, signal-oriented
    ↓
AUTOSAR Adaptive Platform (2017–present) — service-oriented, POSIX, C++14/17
    ↓
Software-Defined Vehicle (SDV) — cloud-connected, continuous deployment
```

**Axis 3 — Communication:**
```
CAN (1986) / LIN (2003) / FlexRay (2005) — low-bandwidth, deterministic buses
    ↓
Automotive Ethernet + TSN (2015+) — high-bandwidth, deterministic IP networking
    ↓
SOME/IP + DDS (service-oriented) — dynamic service discovery, publish-subscribe
```

### The Automotive V-Model Development Lifecycle

```
System Requirements ──→ System Architecture ──→ SW/HW Design ──→ Implementation
         ↑                       ↑                     ↑                ↓
         │                       │                     │                ↓
System Validation ←── System Integration ←── Unit/Integration Test ←── Coding
(ISO 26262 Part 4)    (ISO 26262 Part 4)    (ISO 26262 Part 6)
```

### The Homologation Chain

```
International Standard (ISO/IEC)
    ↓ referenced by
UN Regulation (UNECE WP.29 — Reg 155, Reg 156)
    ↓ adopted by
Contracting Parties (EU, Japan, South Korea, Australia)
    ↓ implemented via
National Type Approval Authority (KBA Germany, VCA UK, RDW Netherlands)
    ↓ issues
Whole Vehicle Type Approval (WVTA)
    ↓ enables
Vehicle Registration and Road Use
```

---

## 11.2 Vehicle Networking Protocols

### CAN (Controller Area Network)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Controller Area Network                                        |
| Standard                     | ISO 11898-1:2015 (data link layer), ISO 11898-2:2016 (physical — high-speed) |
| Entry Type                   | Serial communication protocol — vehicle bus                   |
| Governance Tier              | L1 (ISO) — originally Bosch GmbH proprietary (1986), standardized ISO 1993 |
| Developed By                 | Robert Bosch GmbH (1983–1986)                                 |
| Speed                        | Classical CAN: up to 1 Mbps; CAN FD: up to 8 Mbps; CAN XL: up to 10+ Mbps |
| Variants                     | Classical CAN, CAN FD (ISO 11898-1:2015), CAN XL (ISO 11898-1:2024) |
| Frame Format                 | Standard (11-bit ID) or Extended (29-bit ID); data: 0–8 bytes (CAN), 0–64 bytes (CAN FD) |
| Bus Topology                 | Linear bus with 120Ω termination resistors at each end        |
| Arbitration                  | Non-destructive bitwise CSMA/CR (Carrier Sense Multiple Access / Collision Resolution) |
| Error Detection              | CRC, bit stuffing, frame check, ACK, error counters (TEC/REC) |
| Higher-Layer Protocols       | CANopen (CiA 301), SAE J1939, OBD-II (ISO 15031), UDS/ISO-TP (ISO 15765), AUTOSAR COM |
| Industry Status              | Most widely deployed vehicle bus — billions of nodes worldwide |

### CAN FD vs. Classical CAN vs. CAN XL

| Feature           | Classical CAN     | CAN FD              | CAN XL                |
|-------------------|-------------------|---------------------|------------------------|
| Max data length   | 8 bytes           | 64 bytes            | 2048 bytes             |
| Max bit rate      | 1 Mbps            | 8 Mbps (data phase) | 10+ Mbps               |
| Bit rate switching| No                | Yes (arbitration at 1M, data up to 8M) | Yes                    |
| Standard          | ISO 11898-1:2003  | ISO 11898-1:2015    | ISO 11898-1:2024       |
| Backward compat.  | —                 | Not with Classical  | Not with CAN FD        |
| Frame overhead     | Large per byte    | Better ratio        | Best for large payloads |

### LIN (Local Interconnect Network)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Local Interconnect Network                                     |
| Standard                     | ISO 17987:2016                                                |
| Governance Tier              | L1 (ISO)                                                      |
| Speed                        | Up to 20 kbps (single-wire, low cost)                         |
| Topology                     | Master-slave, single-wire bus (up to 16 slaves)              |
| Use Cases                    | Window motors, mirrors, seat adjustment, rain sensors, door modules |
| Key Feature                  | Very low cost (no crystal oscillator needed on slave nodes)   |
| Relationship to CAN          | LIN sub-networks connect to CAN via gateway ECU              |

### FlexRay

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | FlexRay                                                        |
| Standard                     | ISO 17458-1 to 17458-5                                        |
| Governance Tier              | L1 (ISO) — originally FlexRay Consortium (BMW, DaimlerChrysler, Motorola, Philips) |
| Speed                        | 10 Mbps per channel (dual-channel = 20 Mbps aggregate)       |
| Key Feature                  | Time-triggered + event-triggered communication; deterministic scheduling |
| Topology                     | Bus, star, or hybrid                                          |
| Safety Domain                | Chassis dynamics (steer-by-wire, brake-by-wire), suspension control |
| Status                       | Declining — replaced by Automotive Ethernet + TSN in new platforms |
| Notable Adopters             | BMW (F-Series, G-Series), Audi, Mercedes (older platforms)    |

### Automotive Ethernet (100BASE-T1 / 1000BASE-T1)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Automotive Ethernet (BroadR-Reach derivative)                 |
| Standards                    | IEEE 802.3bw (100BASE-T1), IEEE 802.3bp (1000BASE-T1), IEEE 802.3ch (multi-gig) |
| Governance Tier              | L1 (IEEE) + L3 (OPEN Alliance SIG)                           |
| Speed Variants               | 100 Mbps, 1 Gbps, 2.5 Gbps, 5 Gbps, 10 Gbps               |
| Physical Layer Key           | Single unshielded twisted pair (lighter, cheaper vs. 4-pair Cat5/6) |
| TSN Extensions               | IEEE 802.1AS (gPTP time sync), 802.1Qbv (Time-Aware Shaper), 802.1CB (FRER redundancy) |
| Applications                 | Camera, radar, LiDAR, ADAS compute, infotainment, OTA backbone, zone controllers |
| Industry Direction           | Replacing CAN/FlexRay for high-bandwidth domains; backbone for zonal architecture |

### SOME/IP (Scalable service-Oriented MiddlEware over IP)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Scalable service-Oriented MiddlEware over IP                  |
| Governance                   | AUTOSAR (defined in Adaptive Platform) + COVESA               |
| Governance Tier              | L3 (AUTOSAR consortium)                                       |
| Protocol Type                | Application-layer middleware for service-oriented communication |
| Transport                    | UDP (events/notifications) or TCP (reliable method calls)     |
| Key Concepts                 | Service Instance, Event Group, Method, Field, Service Discovery (SOME/IP-SD) |
| Serialization                | SOME/IP binary format (network byte order, TLV for optional fields) |
| Alternative Middleware       | DDS (OMG) — also supported in AUTOSAR Adaptive               |
| Use Cases                    | Inter-ECU service communication in ADAS, HPC, zonal platforms |

### Vehicle Network Protocol Comparison

| Protocol     | Speed        | Topology    | Deterministic? | Typical Use                    | Status       |
|--------------|--------------|-------------|----------------|--------------------------------|--------------|
| CAN          | 1 Mbps       | Bus         | Priority-based | Powertrain, body, chassis      | Dominant     |
| CAN FD       | 8 Mbps       | Bus         | Priority-based | Same + larger payloads         | Growing      |
| CAN XL       | 10+ Mbps     | Bus         | Priority-based | Bridge CAN→Ethernet            | New (2024)   |
| LIN          | 20 kbps      | Master-slave| Yes (schedule) | Low-cost actuators/sensors     | Stable       |
| FlexRay      | 10/20 Mbps   | Bus/Star    | Yes (TDMA)     | Chassis, steer-by-wire         | Declining    |
| Auto Ethernet| 100M–10G     | Point-to-point/Switch | With TSN | Cameras, ADAS, backbone     | Dominant (new)|
| MOST         | 25/150 Mbps  | Ring        | Yes (sync)     | Infotainment (legacy)          | Declining    |

---

## 11.3 Automotive Safety Standards Stack

### IEC 61508 (Root Standard)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Functional Safety of Electrical/Electronic/Programmable Electronic Safety-Related Systems |
| Standard Number              | IEC 61508:2010 (2nd edition)                                  |
| Governance Tier              | L1 (IEC)                                                      |
| Scope                        | ALL industries using E/E/PE systems for safety functions      |
| Safety Levels                | SIL 1 through SIL 4 (Safety Integrity Level)                 |
| Key Concept                  | Safety lifecycle from concept through decommissioning         |
| Derived Standards            | ISO 26262 (auto), EN 50128 (rail), IEC 62304 (medical), IEC 61511 (process) |

### ISO 26262:2018 (Automotive Functional Safety)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Road Vehicles — Functional Safety                              |
| Standard Number              | ISO 26262:2018 (2nd edition, 12 parts)                        |
| Governance Tier              | L1 (ISO TC 22/SC 32)                                         |
| Derived From                 | IEC 61508 (adapted for automotive lifecycle and hazard classification) |
| Applicability                | Passenger vehicles up to 3500 kg (motorcycles added in Part 12) |
| Key Innovation               | ASIL (Automotive Safety Integrity Level) classification       |
| Certification                | Not "certified" — assessed by independent assessors; evidence in safety case |

### ASIL Classification

| ASIL | Severity | Probability | Controllability | Required Measures                     |
|------|----------|-------------|-----------------|---------------------------------------|
| QM   | Any combination below threshold  ||| Normal quality management              |
| A    | S1       | E2–E4       | C2–C3           | Basic safety mechanisms               |
| B    | S2       | E2–E3       | C2–C3           | Redundant error detection             |
| C    | S2–S3    | E3–E4       | C3              | Hardware metrics, design verification |
| D    | S3       | E4          | C3              | Highest redundancy, formal methods, independent review |

### ISO 26262 Part Structure

| Part  | Title                                              | Key Outputs                        |
|-------|----------------------------------------------------|------------------------------------|
| Part 1| Vocabulary                                         | Definitions                       |
| Part 2| Management of functional safety                    | Safety plan, confirmation measures|
| Part 3| Concept phase                                      | HARA, Safety goals, FSC           |
| Part 4| Product development: system level                  | Technical safety concept, TSR      |
| Part 5| Product development: hardware level                | HW safety requirements, FMEDA     |
| Part 6| Product development: software level                | SW safety requirements, coding guidelines |
| Part 7| Production, operation, service, decommissioning   | Production guidelines              |
| Part 8| Supporting processes                               | Configuration mgmt, change mgmt   |
| Part 9| ASIL-oriented and safety-oriented analyses         | FMEA, FTA, dependent failure analysis |
| Part 10| Guidelines on ISO 26262                          | Application guidance               |
| Part 11| Semiconductors                                   | HW metrics for silicon             |
| Part 12| Motorcycles                                      | Adaptation for 2/3-wheelers       |

### SOTIF — ISO 21448:2022

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Safety Of The Intended Functionality                           |
| Standard Number              | ISO 21448:2022                                                |
| Governance Tier              | L1 (ISO TC 22)                                                |
| Scope                        | Hazards arising from functional insufficiencies (not faults)  |
| Key Difference from 26262    | ISO 26262 covers E/E system FAULTS; SOTIF covers hazardous behavior from INTENDED function working correctly but inadequately |
| Critical For                 | ADAS and autonomous driving: sensor limitations, algorithm edge cases, unknown scenarios |
| Key Concepts                 | Known safe scenarios, known unsafe scenarios, unknown unsafe scenarios (target: reduce unknown unsafe area) |

### Safety Standards Comparison

| Standard          | Covers                        | Hazard Source                  | For Which Systems            |
|-------------------|-------------------------------|--------------------------------|------------------------------|
| ISO 26262         | E/E system faults             | Random HW failures, systematic SW defects | All E/E safety-related      |
| ISO 21448 (SOTIF) | Functional insufficiencies    | Sensor limits, algorithm gaps, misuse | ADAS, AD (L2+)             |
| ISO/SAE 21434     | Cybersecurity threats         | Malicious actors               | All connected vehicle systems|

---

## 11.4 Automotive Cybersecurity

### ISO/SAE 21434:2021

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Road Vehicles — Cybersecurity Engineering                      |
| Standard Number              | ISO/SAE 21434:2021                                            |
| Governance Tier              | L1 (ISO TC 22) + L4 (SAE)                                    |
| Joint Development            | ISO and SAE co-developed (rare joint standard)                |
| Published                    | August 2021                                                   |
| Key Concept                  | TARA — Threat Analysis and Risk Assessment                    |
| Output                       | Cybersecurity goals → Requirements → Validation → Monitoring  |
| CAL Levels                   | Cybersecurity Assurance Level 1–4 (rigor of evidence required)|
| Relationship to UNECE R155   | R155 mandates ISO 21434-compatible CSMS for type approval     |
| Lifecycle Coverage           | Concept → Development → Production → Operations → Decommissioning |

### TARA Process (ISO/SAE 21434 Clause 15)

```
1. Asset Identification → What needs protection (ECUs, data, functions)
2. Threat Scenarios → What could go wrong (STRIDE, attack trees)
3. Impact Rating → Safety, financial, operational, privacy (1–4)
4. Attack Feasibility → Elapsed time, expertise, knowledge, equipment, window (1–5)
5. Risk Value → Impact × Feasibility
6. Risk Treatment → Avoid, reduce, share, retain
7. Cybersecurity Goals → High-level security objectives
8. Cybersecurity Requirements → Detailed implementation requirements
```

### UNECE WP.29 Regulation 155

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | UN Regulation No. 155 — Cyber Security and Cyber Security Management System |
| Entry Type                   | UN Regulation (legally binding in contracting parties)        |
| Effective                    | July 2022 (new vehicle types); July 2024 (all new vehicles)  |
| Contracting Parties          | EU, Japan, South Korea, Australia, others (NOT USA/China)     |
| Key Requirements             | OEM must implement and maintain a CSMS covering entire vehicle lifecycle |
| Audit                        | National type approval authority audits the CSMS              |
| 7 Threat Categories          | Back-end servers, communication channels, update procedures, human actions, external connectivity, vehicle data/code, vulnerabilities |
| Certificate Validity         | 3 years (must be renewed)                                     |

### UNECE WP.29 Regulation 156

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | UN Regulation No. 156 — Software Update and Software Update Management System |
| Key Requirements             | OEM must implement a SUMS; assess update impact on type approval; ensure safe OTA delivery |
| Aligned Standard             | ISO 24089:2024 (Road vehicles — Software update engineering)  |

---

## 11.5 Vehicle Diagnostics Standards

### UDS (Unified Diagnostic Services)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Unified Diagnostic Services                                    |
| Standard                     | ISO 14229-1:2020 (base), ISO 14229-2 to -7 (transport bindings) |
| Governance Tier              | L1 (ISO TC 22)                                                |
| Entry Type                   | Application-layer diagnostic protocol                         |
| Transport Bindings           | CAN (ISO 15765 / ISO-TP), Ethernet/DoIP (ISO 13400), FlexRay, K-Line |
| Session Types                | Default (0x01), Programming (0x02), Extended (0x03), OEM-specific |

### Key UDS Services

| SID (Hex) | Service Name                  | Purpose                              |
|-----------|-------------------------------|--------------------------------------|
| 0x10      | DiagnosticSessionControl      | Switch diagnostic session            |
| 0x11      | ECUReset                      | Reset ECU (hard/soft/keyoff)         |
| 0x14      | ClearDiagnosticInformation    | Clear DTCs                           |
| 0x19      | ReadDTCInformation            | Read stored trouble codes            |
| 0x22      | ReadDataByIdentifier          | Read calibration/config data         |
| 0x27      | SecurityAccess                | Seed-key authentication              |
| 0x28      | CommunicationControl          | Enable/disable comm                  |
| 0x2E      | WriteDataByIdentifier         | Write calibration data               |
| 0x31      | RoutineControl                | Start/stop/request results of routines |
| 0x34      | RequestDownload               | Initiate ECU reprogramming           |
| 0x35      | RequestUpload                 | Read ECU memory                      |
| 0x36      | TransferData                  | Data block transfer                  |
| 0x37      | RequestTransferExit           | End transfer                         |
| 0x3E      | TesterPresent                 | Keep session alive                   |
| 0x85      | ControlDTCSetting             | Enable/disable DTC detection         |

### OBD-II (On-Board Diagnostics)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | On-Board Diagnostics II                                        |
| Standard                     | SAE J1979 (service definitions), ISO 15031 (EU equivalent)    |
| Connector                    | SAE J1962 (16-pin trapezoidal DLC)                            |
| DTC Format                   | SAE J2012 (e.g., P0300 = Random/Multiple Cylinder Misfire)    |
| Mandated By                  | CARB (California, 1996), EPA (USA), EU (EOBD from 2001)       |
| Primary Purpose              | Emissions monitoring and MIL (Malfunction Indicator Lamp) control |

### DoIP (Diagnostics over IP)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Diagnostics over Internet Protocol                             |
| Standard                     | ISO 13400-1/-2/-3:2019                                        |
| Transport                    | TCP/UDP over Ethernet                                         |
| Key Use                      | UDS over Ethernet — required for AUTOSAR Adaptive ECUs and HPC |
| Port                         | UDP 13400 (discovery), TCP 13400 (diagnostic communication)  |
| Why Needed                   | CAN-based diagnostics limited to 1 Mbps; modern ECUs need Ethernet throughput for flash programming |

---

## 11.6 V2X Communications

### Competing V2X Technologies

| Dimension            | DSRC / ITS-G5 (IEEE 802.11p)        | C-V2X (Cellular V2X — 3GPP)         |
|----------------------|--------------------------------------|--------------------------------------|
| Standard Base        | IEEE 802.11p (OCB mode), ETSI ITS-G5| 3GPP LTE-V2X (Rel. 14), NR-V2X (Rel. 16+) |
| Frequency            | 5.9 GHz ITS band                    | 5.9 GHz (PC5 sidelink)              |
| Range                | ~300–500 m                           | ~500–1000+ m (LTE); enhanced NR     |
| Latency              | 1–4 ms                              | LTE: ~20ms; NR-V2X: <5ms            |
| Network Dependency   | None — direct V2V/V2I               | PC5: no network; Uu: cellular needed |
| Region Adoption      | EU (initially), Japan                | China (dominant), USA (FCC favoring) |
| Message Standards    | SAE J2735 (BSM), ETSI CAM/DENM      | SAE J2735, ETSI standards           |
| Maturity             | Mature (deployed since 2019)         | LTE-V2X deployed; NR-V2X emerging   |
| Evolution Path       | 802.11bd (next gen)                  | NR-V2X (5G sidelink)                |

---

## 11.7 EV Charging Standards

| Standard/System | Full Name                                    | Governance  | Region        | Type           |
|-----------------|----------------------------------------------|-------------|---------------|----------------|
| IEC 61851       | EV conductive charging system                | IEC TC 69   | Global        | Infrastructure |
| ISO 15118       | Vehicle-to-grid communication interface      | ISO TC 22   | Global        | Protocol (PLC/TLS) |
| SAE J1772       | EV Conductive Charge Coupler                 | SAE         | North America | AC connector   |
| CCS Combo 1     | Combined Charging System (Type 1 + DC)      | CharIN e.V. | North America | DC fast charge |
| CCS Combo 2     | Combined Charging System (Type 2 + DC)      | CharIN e.V. | Europe        | DC fast charge |
| CHAdeMO         | Japanese DC fast charging                    | CHAdeMO Assoc| Japan/Asia   | DC fast charge |
| GB/T 20234      | Chinese national charging standard           | China SAC   | China         | AC + DC        |
| NACS (SAE J3400)| North American Charging Standard (Tesla-origin)| SAE       | North America | AC + DC combo  |
| ISO 15118-20    | Bidirectional (V2G) + wireless power transfer| ISO TC 22   | Global        | Protocol ext.  |

### ISO 15118 — Plug & Charge

| Feature              | Description                                          |
|----------------------|------------------------------------------------------|
| Plug & Charge        | Automatic authentication + billing on cable plug-in  |
| TLS 1.3              | Encrypted communication between EV and EVSE          |
| Contract certificates| PKI-based identity (no RFID card needed)            |
| V2G (Vehicle-to-Grid)| Bidirectional power flow (ISO 15118-20)             |
| PLC (HomePlug GreenPHY) | Communication over pilot wire in charging cable  |

---

## 11.8 Embedded OS and RTOS Standards

| OS / Standard       | Governance Tier | Safety Certification | Key Standards     | Domain                       |
|---------------------|-----------------|---------------------|-------------------|------------------------------|
| OSEK/VDX OS         | L3 → AUTOSAR   | IEC 61508 basis     | OSEK OS, OIL      | Classic automotive ECUs      |
| AUTOSAR OS          | L3 (AUTOSAR)   | ASIL-D capable      | AUTOSAR OS SWS    | All AUTOSAR Classic ECUs     |
| QNX (BlackBerry)    | L7 (proprietary)| ISO 26262 ASIL-D   | POSIX-compliant   | ADAS, digital cockpit, HPC   |
| VxWorks (Wind River)| L7 (proprietary)| DO-178C DAL-A, ISO 26262 | POSIX    | Avionics, automotive, space  |
| PikeOS (Sysgo)      | L7 (proprietary)| DO-178C, ARINC 653 | POSIX, ARINC 653  | Avionics, mixed-criticality  |
| FreeRTOS            | L5 (MIT, AWS)   | SafeRTOS (IEC 61508)| Minimal POSIX    | IoT, microcontrollers        |
| Zephyr RTOS         | L5 (Linux Foundation)| IEC 61508 in progress | POSIX subset | IoT, automotive, wearables  |
| RTEMS               | L5 (open source)| DO-178C, FACE      | POSIX             | Space (NASA/ESA), defense    |
| Linux (PREEMPT_RT)  | L5 (LF)        | ELISA project       | POSIX, LSB        | Non-safety HPC, infotainment |

---

## 11.9 Coding Standards for Safety-Critical Embedded

### MISRA C:2023

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Motor Industry Software Reliability Association C Guidelines   |
| Current Version              | MISRA C:2023 (4th edition)                                    |
| Governance                   | MISRA Consortium (part of Horiba MIRA group)                  |
| Governance Tier              | L3/L4 (industry consortium)                                   |
| Entry Type                   | Coding standard — subset of C language for safety-critical use |
| Rule Categories              | Mandatory (must comply), Required (shall comply, deviation documented), Advisory (should comply) |
| Total Rules                  | ~175 rules (MISRA C:2023)                                     |
| Verification                 | Static analysis tools: PC-lint, Polyspace, Coverity, LDRA, Parasoft |
| Mandated By                  | ISO 26262 (recommended for ASIL B–D), DO-178C (common practice) |

### MISRA C:2023 — Key Rule Examples

| Rule ID        | Category   | Description                                              |
|----------------|------------|----------------------------------------------------------|
| Rule 11.3      | Required   | A cast shall not convert pointer to different object type|
| Rule 12.2      | Required   | The right-hand operand of a shift shall be non-negative and < width |
| Rule 17.7      | Required   | Return value of non-void function shall be used         |
| Rule 18.1      | Mandatory  | Pointer arithmetic shall not exceed array bounds         |
| Rule 21.3      | Required   | Memory allocation (malloc/free) shall not be used        |
| Dir 4.1        | Required   | Run-time failures shall be minimized                     |

### Coding Standards Comparison

| Standard       | Language | Governance | Domain                  | Rules Count | Key Focus                |
|----------------|----------|------------|-------------------------|-------------|--------------------------|
| MISRA C:2023   | C        | MISRA (L3) | Automotive, safety      | ~175        | Undefined/unspecified behavior avoidance |
| MISRA C++:2023 | C++      | MISRA (L3) | Automotive, safety      | ~200+       | Safe C++ subset          |
| CERT C         | C        | SEI/CMU (L4)| Cybersecurity          | ~100 rules + recs | Security vulnerabilities |
| AUTOSAR C++14  | C++14    | AUTOSAR (L3)| AUTOSAR Adaptive       | ~350 rules  | Combines MISRA C++ + HIC++ |
| BARR-C         | C        | Barr Group | Embedded firmware       | ~100 rules  | Bug prevention, readability |

---

## 11.10 ASAM Standards (Automotive Measurement & Calibration)

| Standard   | Full Name                          | Domain                          | Format/Protocol |
|------------|------------------------------------|---------------------------------|-----------------|
| ASAM MDF   | Measurement Data Format            | ECU measurement data logging    | Binary file (.mdf, .mf4) |
| ASAM ARXML | AUTOSAR XML                        | AUTOSAR system description      | XML schema      |
| ASAM FIBEX | Field Bus Exchange Format          | Network topology/signals        | XML             |
| ASAM MCD-2 MC | Measurement, Calibration, Diagnostics — Calibration | ECU calibration | A2L files + CCP/XCP protocol |
| ASAM XCP   | Universal Measurement and Calibration Protocol | ECU measurement/cal | CAN, Ethernet, SPI transport |
| ASAM ODS   | Open Data Services                 | Test bench data management      | SQL/CORBA API   |
| ASAM XIL   | Cross-vendor Interface Layer       | HIL/SIL test environments       | API standard    |
| ASAM OpenSCENARIO | Scenario description for AD testing | Automated driving test scenarios | XML/OpenDRIVE |
| ASAM OpenDRIVE | Road network description        | HD map format for simulation    | XML             |

---

## 11.11 Automotive Standards Master Reference Table

| Standard / Protocol | Governance Body | Tier  | Domain                    | Status       | Current Version   |
|---------------------|-----------------|-------|---------------------------|--------------|-------------------|
| ISO 11898 (CAN)     | ISO TC 22       | L1    | Vehicle bus               | De-facto dominant | 2015 (FD), 2024 (XL) |
| ISO 17987 (LIN)     | ISO TC 22       | L1    | Low-cost bus              | Stable       | 2016              |
| ISO 17458 (FlexRay) | ISO TC 22       | L1    | Safety-critical bus       | Declining    | 2013              |
| IEEE 802.3bw/bp/ch  | IEEE 802.3      | L1    | Automotive Ethernet PHY   | Growing      | 2015/2016/2020    |
| SOME/IP              | AUTOSAR         | L3    | Service middleware         | Growing      | R23-11            |
| AUTOSAR Classic      | AUTOSAR         | L3    | ECU SW architecture        | Dominant     | R23-11            |
| AUTOSAR Adaptive     | AUTOSAR         | L3    | HPC/ADAS platform          | Growing      | R23-11            |
| ISO 26262            | ISO TC 22       | L1    | Functional safety          | Market-required | 2018 (2nd ed.) |
| ISO 21448 (SOTIF)    | ISO TC 22       | L1    | AD safety                  | Emerging     | 2022              |
| ISO/SAE 21434        | ISO + SAE       | L1/L4 | Cybersecurity              | UNECE-mandated | 2021            |
| UNECE Reg 155        | UNECE WP.29     | L2    | Vehicle cybersecurity      | Mandatory (EU/JP/KR) | 2021       |
| UNECE Reg 156        | UNECE WP.29     | L2    | OTA updates                | Mandatory    | 2021              |
| ISO 14229 (UDS)      | ISO TC 22       | L1    | Diagnostics                | De-facto     | 2020              |
| ISO 13400 (DoIP)     | ISO TC 22       | L1    | Ethernet diagnostics       | Growing      | 2019              |
| ISO 15118            | ISO TC 22       | L1    | EV charging protocol       | Growing      | -2/-20 (2022/2023)|
| SAE J3016            | SAE             | L4    | AD levels taxonomy         | Reference    | 2021              |
| SAE J1939            | SAE             | L4    | Heavy-duty CAN protocol    | Dominant     | Current           |
| SAE J3400 (NACS)     | SAE             | L4    | Charging connector         | Emerging     | 2023              |
| MISRA C:2023         | MISRA           | L3    | C coding rules             | Dominant     | 2023 (4th ed.)    |
| IEC 61508            | IEC SC 65A      | L1    | Functional safety (root)   | Foundation   | 2010 (2nd ed.)    |

---

## 11.12 Key Disambiguation Notes

### 1. ISO 26262 vs. SOTIF vs. ISO/SAE 21434

These are COMPLEMENTARY, not competing standards:

| Standard    | Answers                                                | Hazard Source        |
|-------------|--------------------------------------------------------|----------------------|
| ISO 26262   | "What if the system has a FAULT?"                     | HW random failure, SW bugs |
| ISO 21448   | "What if the system works correctly but inadequately?"| Sensor limits, algorithm gaps |
| ISO/SAE 21434 | "What if someone ATTACKS the system?"              | Malicious actors     |

### 2. AUTOSAR Classic vs. Adaptive

| Aspect              | AUTOSAR Classic Platform (CP)     | AUTOSAR Adaptive Platform (AP)    |
|---------------------|------------------------------------|------------------------------------|
| Target Hardware     | Microcontrollers (< 512KB RAM)    | High-performance computers (multi-GB RAM) |
| OS                  | AUTOSAR OS (static, OSEK-based)   | POSIX OS (Linux, QNX)            |
| Language            | C                                 | C++14/17                          |
| Communication       | Signal-based (PDU routing)        | Service-oriented (SOME/IP, DDS)   |
| Scheduling          | Static (configured at build time) | Dynamic (runtime service discovery)|
| Update              | Full reflash                      | OTA partial update (per application)|
| Typical ECUs        | Body, powertrain, chassis         | ADAS/AD compute, cockpit HPC     |

### 3. CAN FD vs. CAN XL vs. Automotive Ethernet

| Decision Factor    | CAN FD                          | CAN XL                           | Automotive Ethernet              |
|--------------------|---------------------------------|----------------------------------|----------------------------------|
| Bandwidth needed   | < 8 Mbps                       | < 10 Mbps                       | > 10 Mbps                       |
| Legacy compatibility| CAN FD controllers available   | New silicon required             | Switch infrastructure needed     |
| Determinism        | Priority-based arbitration     | Priority-based                   | Requires TSN (802.1Qbv)        |
| Cost               | Low (existing harness)         | Medium                           | Higher (switches, shielding)     |
| Best for           | Body/powertrain (moderate data)| CAN→Ethernet bridge period       | Camera, ADAS, infotainment       |

---

*End of Chapter 11 — Automotive & Embedded Systems Standards*
*Next: Chapter 12 — Networking & Telecommunications Standards*
