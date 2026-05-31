# CHAPTER 11 — DOMAIN: AUTOMOTIVE & EMBEDDED SYSTEMS STANDARDS
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapters 2, 4, 5, 10

---

## CHAPTER SCOPE

**Domain Coverage — Automotive & Embedded:**
- Vehicle networking protocols: CAN, LIN, FlexRay, MOST, Automotive Ethernet, SOME/IP
- ECU software architecture: AUTOSAR Classic, Adaptive, Foundation
- Automotive safety: ISO 26262, IEC 61508, SOTIF (ISO 21448)
- Automotive cybersecurity: ISO/SAE 21434, UNECE WP.29 Reg 155/156
- Diagnostics: UDS (ISO 14229), OBD-II (SAE J1979), SOVD (ASAM)
- Autonomous driving: SAE J3016 (L0–L5), ISO PAS 22736
- V2X communications: IEEE 802.11p / 802.11bd (ITS-G5 / C-V2X), SAE J2735, J2945, ETSI ITS
- EV charging: IEC 61851, ISO 15118, CHAdeMO, SAE J1772, CCS, NACS
- OTA updates: ISO 24089, AUTOSAR Adaptive OTA, UNECE Reg 156
- ASAM standards: MDF, ARXML, FIBEX, ASC, MTS, ODS
- Embedded OS: OSEK/VDX, AUTOSAR OS, POSIX, RTEMS, FreeRTOS, Zephyr, QNX
- Programming languages for embedded: MISRA C, MISRA C++, CERT C, Ada, Rust (embedded)
- JTAG / boundary scan: IEEE 1149.1, IJTAG (IEEE 1687)

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class automotive software architect and embedded systems standards specialist.
Generate the complete **Chapter 11: Automotive & Embedded Systems Standards** encyclopedia.

### SECTION 11.1 — AUTOMOTIVE STANDARDS ECOSYSTEM OVERVIEW

Produce a structured overview explaining:
- The unique complexity of automotive standards: safety + security + networking + diagnostics + homologation all co-exist
- The three axes of automotive standardization:
  1. **Safety axis**: IEC 61508 → ISO 26262 → SOTIF (ISO 21448) → ISO/SAE 21434
  2. **Architecture axis**: OSEK/VDX → AUTOSAR Classic → AUTOSAR Adaptive → SDV
  3. **Communication axis**: CAN/LIN/FlexRay → Automotive Ethernet (TSN) → SOME/IP/DDS
- The automotive development lifecycle:
  V-model: System requirements → Architecture → SW/HW Design → Implementation → Unit Test → Integration → System Test → Validation
- The role of automotive Tier 1 suppliers (Bosch, Continental, ZF, Aptiv, Magna) as both standards creators and consumers
- The homologation chain: ISO standard → UNECE regulation → National type approval → Vehicle on road

### SECTION 11.2 — VEHICLE NETWORKING PROTOCOLS

**CAN (Controller Area Network):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Controller Area Network                          |
| Standard                     | ISO 11898-1 (data link layer), ISO 11898-2 (physical — high-speed CAN) |
| Entry Type                   | Communication protocol — vehicle bus               |
| Governance Tier              | L1 (ISO) — originally Bosch L6/L8 de-facto        |
| Developed by                 | Bosch GmbH (1983–1986), standardized ISO 1993     |
| Speed                        | Classical CAN: up to 1 Mbps; CAN FD: up to 8 Mbps; CAN XL: up to 10 Mbps+ |
| Key variants                 | Classical CAN (ISO 11898), CAN FD (ISO 11898-1:2015), CAN XL (ISO 11898-1:2024) |
| Frame format                 | 11-bit or 29-bit arbitration ID, data field 0–8 bytes (CAN), 0–64 bytes (CAN FD) |
| Bus topology                 | Linear bus with termination resistors            |
| Arbitration                  | Non-destructive CSMA/CD-based (highest priority wins) |
| Higher-layer protocols on CAN| CANopen (CiA 301), SAE J1939, OBD-II (ISO 15031), UDS (ISO 14229), AUTOSAR COM |

**LIN (Local Interconnect Network):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Local Interconnect Network                       |
| Standard            | ISO 17987                                        |
| Governance Tier     | L1 (ISO)                                         |
| Speed               | Up to 20 kbps (single-wire, low cost)            |
| Topology            | Master-slave, single wire                        |
| Use cases           | Window motors, mirrors, seat control, door modules|

**FlexRay:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | FlexRay                                          |
| Standard            | ISO 17458-1 to 17458-5                           |
| Governance Tier     | L1 (ISO) — consortium-developed (FlexRay Consortium 2000–2009) |
| Speed               | Up to 10 Mbps per channel (2 channels = 20 Mbps) |
| Key feature         | Time-triggered, deterministic — suited for safety-critical (chassis dynamics, steer-by-wire) |
| Status              | Declining adoption — SOME/IP over Ethernet replacing in new platforms |

**Automotive Ethernet (100BASE-T1, 1000BASE-T1):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Automotive Ethernet                              |
| Standards                    | IEEE 802.3bp (100BASE-T1), IEEE 802.3ch (Multi-gig automotive Ethernet) |
| OPEN Alliance SIG            | Industry group promoting BroadR-Reach / 100BASE-T1 |
| Speed variants               | 100 Mbps (100BASE-T1), 1 Gbps (1000BASE-T1), 2.5G, 5G, 10G |
| Key feature                  | Single unshielded twisted pair (lightweight, cost-effective vs. standard Ethernet) |
| TSN extensions               | IEEE 802.1AS (gPTP), 802.1Qbv (TAS), 802.1CB (FRER) — for safety-critical determinism |
| Application                  | Camera, radar, LIDAR, ADAS, infotainment, OTA backbone |

**SOME/IP (Scalable service-Oriented MiddlEware over IP):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Scalable service-Oriented MiddlEware over IP     |
| Governance          | AUTOSAR (embedded in Adaptive Platform) + COVESA |
| Protocol type       | Application-layer protocol for service-oriented communication |
| Transport           | UDP (events/notifications) or TCP (reliable services) |
| Key concepts        | Service Instance, Event Group, Method, Field    |
| SOME/IP-SD          | Service Discovery protocol for SOME/IP          |
| Alternative         | DDS (OMG) — also supported in AUTOSAR Adaptive as alternative transport |
| Serialization       | SOME/IP binary format (defined in AUTOSAR FIBEX/ARXML) |

### SECTION 11.3 — AUTOMOTIVE SAFETY STANDARDS STACK

Produce the complete safety standards chain:

```
IEC 61508:2010
(Functional Safety of Electrical/Electronic/Programmable Electronic Safety-Related Systems)
— Root standard — ALL industry-specific safety standards derive from this
        ↓
ISO 26262:2018
(Road Vehicles — Functional Safety)
— Derived from IEC 61508 — defines ASIL A/B/C/D hazard classification
        ↓
SOTIF ISO 21448:2022
(Safety of the Intended Functionality)
— Extends ISO 26262 — covers known and unknown hazards from nominal system behavior
— Critical for ADAS and automated driving: sensor limitations, edge cases
        ↓
ISO/SAE 21434:2021
(Road Vehicles — Cybersecurity Engineering)
— Complements ISO 26262 with cybersecurity — TARA (Threat Analysis and Risk Assessment)
```

**ASIL (Automotive Safety Integrity Level) Classification:**

| ASIL Level | Hazard Class  | Examples                                   | Required SW measures            |
|------------|---------------|--------------------------------------------|---------------------------------|
| ASIL A     | Low hazard    | Windshield wiper, interior lighting        | Basic error detection           |
| ASIL B     | Medium hazard | Side window control, door locks            | Redundant checks                |
| ASIL C     | High hazard   | Electronic Power Steering assist           | Formal verification elements    |
| ASIL D     | Highest hazard| Brakes, airbags, engine throttle control   | Highest redundancy, formal methods|
| QM         | Quality Managed| Infotainment, non-safety functions         | Normal quality management       |

**ISO 26262 Part Structure:**

| Part  | Title                                              |
|-------|----------------------------------------------------|
| Part 1| Vocabulary                                        |
| Part 2| Management of functional safety                   |
| Part 3| Concept phase                                     |
| Part 4| Product development: system level                 |
| Part 5| Product development: hardware level               |
| Part 6| Product development: software level               |
| Part 7| Production, operation, service and decommissioning|
| Part 8| Supporting processes                              |
| Part 9| Automotive safety integrity level (ASIL)-oriented and safety-oriented analyses |
| Part 10| Guidelines on ISO 26262                         |
| Part 11| Guidelines on application of ISO 26262 to semiconductors |
| Part 12| Adaptation for motorcycles                      |

### SECTION 11.4 — AUTOMOTIVE CYBERSECURITY STANDARDS

**ISO/SAE 21434:2021:**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Road Vehicles — Cybersecurity Engineering         |
| Governance                   | ISO/TC 22 + SAE (joint standard)                  |
| Governance Tier              | L1 (ISO) + L4 (SAE)                               |
| Published                    | August 2021                                       |
| Key concept                  | TARA (Threat Analysis and Risk Assessment)        |
| TARA output                  | Cybersecurity goals → Cybersecurity requirements → Validation |
| CAL levels                   | Cybersecurity Assurance Levels (CAL 1–4, analogous to ASIL) |
| Relationship to UNECE Reg 155| Reg 155 mandates an ISO 21434-compatible CSMS    |
| Key clauses                  | Clause 8 (Product development), Clause 9 (Cybersecurity validation), Clause 10 (Production), Clause 15 (TARA) |

**UNECE WP.29 Regulation 155 (Vehicle Cybersecurity):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | UN Regulation No. 155 — Cyber Security and Cyber Security Management System |
| Entry Type          | UN Regulation (legally binding in EU, Japan, Korea) |
| Effective           | January 2022 (new types); July 2024 (all vehicles)|
| Requirements        | OEM must implement a CSMS (Cybersecurity Management System) covering the full vehicle lifecycle |
| Audit               | Type approval authority verifies CSMS            |

**UNECE WP.29 Regulation 156 (OTA Software Updates):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | UN Regulation No. 156 — Software Update and Software Update Management System |
| Requirements        | OEM must implement a SUMS (Software Update Management System) |
| Aligned standard    | ISO 24089 (Road vehicles — Software update engineering) |

### SECTION 11.5 — VEHICLE DIAGNOSTICS STANDARDS

**UDS (Unified Diagnostic Services):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Unified Diagnostic Services                      |
| Standard                     | ISO 14229-1 (base), ISO 14229-2 to -7 (transport layers) |
| Entry Type                   | Application-layer diagnostic protocol            |
| Governance Tier              | L1 (ISO)                                         |
| Key services                 | 0x10 DiagnosticSessionControl, 0x11 ECUReset, 0x22 ReadDataByIdentifier, 0x2E WriteDataByIdentifier, 0x27 SecurityAccess, 0x31 RoutineControl, 0x34/35 Download/Upload, 0x36 TransferData, 0x3E TesterPresent |
| Transport bindings           | CAN (ISO 15765 / ISOTP), Ethernet DoIP (ISO 13400), FlexRay, K-Line |

**OBD-II (On-Board Diagnostics II):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Standard            | SAE J1979 (service definitions), ISO 15031, CARB OBD-II regulation |
| Connector           | SAE J1962 (OBD-II DLC — 16-pin trapezoidal connector) |
| DTCs                | SAE J2012 — Diagnostic Trouble Code format       |
| Protocols           | ISO 9141-2, ISO 14230 (KWP2000), SAE J1850, ISO 15765 (CAN-OBD) |

**DoIP (Diagnostics over IP):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Standard            | ISO 13400-1/-2/-3                                |
| Transport           | UDP/TCP over Ethernet                            |
| Key use             | UDS over Ethernet (required for AUTOSAR Adaptive ECUs and HPC platforms) |

### SECTION 11.6 — V2X COMMUNICATIONS STANDARDS

Produce a comparison table of the two competing V2X (Vehicle-to-Everything) technologies:

| Dimension            | DSRC / ITS-G5 (IEEE 802.11p)          | C-V2X (Cellular V2X — 3GPP)           |
|----------------------|---------------------------------------|----------------------------------------|
| Standard base        | IEEE 802.11p (OCB mode), ETSI ITS     | 3GPP LTE-V2X (Rel. 14), NR-V2X (Rel. 16) |
| Frequency            | 5.9 GHz ITS band                      | 5.9 GHz ITS band (PC5 interface)      |
| Range                | ~300–500 m                            | ~500–1000+ m (LTE); improved NR       |
| Latency              | Low (1–4 ms)                          | Low (LTE ~20ms; NR <1ms target)       |
| Network dependency   | None — direct V2V/V2I                 | PC5 mode: no network; Uu mode: cellular|
| Standards body       | IEEE + ETSI + SAE                     | 3GPP + ETSI + SAE                     |
| Region adoption      | EU (ITS-G5 / C-ITS)                  | China (C-V2X), USA (moving to C-V2X) |
| Key message standards| SAE J2735 (BSM), SAE J2945, ETSI ETSI EN 302 637 | SAE J2735, ETSI standards    |

### SECTION 11.7 — EV CHARGING STANDARDS

| Standard       | Full Name                                         | Governance  | Region       | Connector Type      |
|----------------|---------------------------------------------------|-------------|--------------|---------------------|
| IEC 61851      | Electric vehicle conductive charging system       | IEC TC 69   | Global       | Infrastructure standard|
| ISO 15118      | Road vehicles — Vehicle to grid communication     | ISO TC 22   | Global       | Protocol standard (PLC over CP) |
| SAE J1772      | Electric Vehicle Conductive Charge Coupler        | SAE         | North America| J1772 Type 1 AC     |
| CCS (Combo 1)  | Combined Charging System — DC fast charge         | CharIN e.V. | North America| J1772 + DC pins     |
| CCS (Combo 2)  | Combined Charging System — Type 2                 | CharIN e.V. | Europe       | IEC 62196 Type 2 + DC|
| CHAdeMO        | Japanese DC fast charge standard                  | CHAdeMO Assoc| Japan/global| CHAdeMO connector   |
| GB/T 20234     | Chinese charging standard                         | China SAC   | China        | GB/T connector      |
| NACS            | North American Charging Standard (Tesla origin)  | SAE J3400   | North America| NACS connector      |
| ISO 15118-20   | Wireless charging (WPT) + bidirectional (V2G)    | ISO TC 22   | Global       | Protocol extension  |

### SECTION 11.8 — EMBEDDED OS AND RTOS STANDARDS

| OS / Standard    | Governance Tier | Safety Cert | Key Standards           | Typical Domain              |
|------------------|-----------------|-------------|-------------------------|-----------------------------|
| OSEK/VDX OS      | L3 (OSEK consortium → AUTOSAR) | IEC 61508 basis | OSEK OS spec, OIL | Automotive body/powertrain ECUs |
| AUTOSAR OS       | L3 (AUTOSAR)    | ASIL-D capable | AUTOSAR OS spec (based on OSEK) | All AUTOSAR Classic ECUs |
| QNX (BlackBerry) | L7 (proprietary)| ISO 26262 ASIL-D | POSIX-compliant    | ADAS, HPC, infotainment     |
| VxWorks (Wind River) | L7 (proprietary) | DO-178C Level A, ISO 26262 | POSIX | Avionics, automotive, space |
| PikeOS (Sysgo)   | L7 (proprietary)| DO-178C, ARINC 653 | POSIX, ARINC 653 | Avionics, automotive        |
| FreeRTOS         | L5 (MIT License, AWS-supported) | IEC 61508 (SafeRTOS) | Basic POSIX subset | IoT, microcontrollers |
| Zephyr RTOS      | L5 (Linux Foundation) | IEC 61508 efforts | POSIX subset    | IoT, automotive, embedded   |
| RTEMS            | L5 (open source) | DO-178C, FACE | POSIX              | Space, mil, industrial      |
| Linux (with ELISA)| L5 (Linux Foundation) | ELISA project | POSIX, PREEMPT_RT | Automotive (non-safety), HPC |

### SECTION 11.9 — MISRA C / CERT C / CODING STANDARDS

**MISRA C:**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Motor Industry Software Reliability Association C Guidelines |
| Governance                   | MISRA Consortium (Horiba MIRA, automotive industry) |
| Governance Tier              | L3/L4 (industry consortium + professional standard)|
| Current versions             | MISRA C:2023 (C language), MISRA C++:2023 (C++ language) |
| Entry Type                   | Coding standard — set of mandatory and advisory rules |
| Key rule categories          | Language subset restrictions, pointer discipline, integer overflow prevention, unreachable code |
| Compliance                   | Static analysis tool verification (PC-lint, Polyspace, Coverity, LDRA) |
| Mandatory in                 | Automotive (ISO 26262 recommends), aerospace (implicit DO-178C) |

### SECTION 11.10 — ASAM STANDARDS (AUTOMOTIVE)

| Standard  | Full Name                                          | Domain                      |
|-----------|----------------------------------------------------|-----------------------------|
| ASAM MDF  | Measurement Data Format                            | ECU measurement data files  |
| ASAM ARXML| AUTOSAR XML schema — exchange format for AUTOSAR   | AUTOSAR toolchain           |
| ASAM FIBEX| Field Bus Exchange Format                          | Network topology description|
| ASAM MTS  | Model Test Script (HIL/SIL testing)               | Test automation             |
| ASAM ODS  | Open Data Services (test bench data)              | Test data management        |
| ASAM XIL  | Cross-vendor Interface for test environments      | HIL/SIL interface standard  |

### SECTION 11.11 — AUTOMOTIVE STANDARDS MASTER TABLE

| Standard / Protocol | Governance Body | Tier | Domain                   | Mandatory? | Current Version |
|---------------------|-----------------|------|--------------------------|------------|-----------------|
| ISO 11898 (CAN)     | ISO TC 22       | L1   | Vehicle bus              | De-facto   | 2024            |
| ISO 17458 (FlexRay) | ISO TC 22       | L1   | Safety-critical bus      | Optional   | Current         |
| AUTOSAR Classic     | AUTOSAR         | L3   | ECU SW architecture      | De-facto   | R23-11          |
| AUTOSAR Adaptive    | AUTOSAR         | L3   | HPC/ADAS platform        | De-facto   | R23-11          |
| ISO 26262           | ISO TC 22       | L1   | Functional safety        | Market-required | 2018      |
| ISO 21448 (SOTIF)   | ISO TC 22       | L1   | Autonomous driving safety| Emerging   | 2022            |
| ISO/SAE 21434       | ISO + SAE       | L1/L4| Cybersecurity            | UNECE mandated | 2021         |
| UNECE Reg 155       | UNECE WP.29     | L2   | Vehicle cybersecurity    | Mandatory (EU/JP/KR) | 2021  |
| UNECE Reg 156       | UNECE WP.29     | L2   | OTA software updates     | Mandatory (EU/JP/KR) | 2021  |
| ISO 14229 (UDS)     | ISO TC 22       | L1   | Diagnostics              | De-facto   | 2020            |
| SAE J3016           | SAE             | L4   | AD taxonomy              | Reference  | 2021            |
| SAE J1939           | SAE             | L4   | Heavy-duty CAN           | De-facto   | Current         |
| MISRA C:2023        | MISRA           | L3   | C coding rules           | Recommended| 2023            |
| IEC 62304           | IEC TC 62       | L1   | Medical SW lifecycle     | FDA/MDR req| 2015+AMD1:2015  |

---

## OUTPUT FORMAT REQUIREMENTS

- All ISO standard numbers must include the year of current edition
- AUTOSAR release designation format: R23-11 (year-month of release)
- CAN frame format bits must be correct (11-bit standard, 29-bit extended)
- ASIL levels must use correct format (ASIL A/B/C/D — not numeric)
- Minimum length: 6,000 words
