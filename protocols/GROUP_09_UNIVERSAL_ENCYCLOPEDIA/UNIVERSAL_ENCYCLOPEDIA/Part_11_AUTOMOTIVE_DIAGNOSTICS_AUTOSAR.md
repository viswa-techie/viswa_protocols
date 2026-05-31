# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 11: AUTOMOTIVE DIAGNOSTICS & AUTOSAR                                 ║
# ║  UDS, OBD-II, XCP, DoIP, AUTOSAR COM, SecOC                               ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Automotive diagnostic and middleware protocols enable vehicle testing, fault diagnosis, calibration, software updates, and standardized inter-ECU communication. These protocols are mandated by regulation (OBD-II) or industry standard (AUTOSAR) and are essential for vehicle development, production, and aftermarket service.

## PURPOSE
- Diagnose faults in production vehicles (workshop/dealer)
- Calibrate ECU parameters during development
- Flash/reprogram ECU firmware (production/OTA)
- Standardize ECU software architecture (AUTOSAR)
- Secure in-vehicle communication

---

## PROTOCOL FAMILY TABLE

| Protocol | Layer | Transport | Purpose | Domain |
|----------|-------|-----------|---------|--------|
| UDS | Application | CAN/Ethernet/K-line | Unified diagnostics | All automotive |
| OBD-II | Application | CAN (SAE J1979) | Emissions diagnostics | Mandated (regulation) |
| KWP2000 | Application | K-line/CAN | Diagnostics (legacy) | Legacy |
| DoIP | Transport | TCP/IP (Ethernet) | UDS over IP | Modern vehicles |
| XCP | Application | CAN/Ethernet/USB | Measurement & calibration | Development |
| AUTOSAR COM | Middleware | CAN/Ethernet/LIN | Signal-based communication | AUTOSAR ECUs |
| AUTOSAR SOME/IP | Middleware | Ethernet | Service-oriented | AUTOSAR Adaptive |
| SecOC | Security | Any | Authentication of PDUs | AUTOSAR security |
| ISO-TP | Transport | CAN | Multi-frame segmentation | CAN diagnostics |

---

## UDS (Unified Diagnostic Services)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Unified Diagnostic Services |
| Standard | ISO 14229-1 (application), ISO 14229-2+ (transport bindings) |
| Year | 2006 (unified from KWP2000/OBD) |
| Creator | ISO TC 22 |
| Model | Client-Server (Tester = Client, ECU = Server) |
| Transport | ISO-TP over CAN, DoIP over Ethernet, K-line |
| Sessions | Default, Programming, Extended Diagnostic, Safety System |
| Security | Security Access (seed-key), Certificate-based (ISO 14229-8) |

### UDS Service Table (Key Services)
| SID | Service Name | Purpose |
|-----|-------------|---------|
| 0x10 | DiagnosticSessionControl | Switch session (default/extended/programming) |
| 0x11 | ECUReset | Reset ECU (hard/soft/key-off-on) |
| 0x14 | ClearDiagnosticInformation | Clear DTCs (fault codes) |
| 0x19 | ReadDTCInformation | Read stored fault codes |
| 0x22 | ReadDataByIdentifier | Read ECU data (DID) |
| 0x23 | ReadMemoryByAddress | Read raw memory |
| 0x27 | SecurityAccess | Unlock secured functions (seed-key) |
| 0x2E | WriteDataByIdentifier | Write configuration data |
| 0x2F | InputOutputControlByIdentifier | Actuator test |
| 0x31 | RoutineControl | Start/stop/get results of routine |
| 0x34 | RequestDownload | Initiate flash programming |
| 0x36 | TransferData | Send firmware blocks |
| 0x37 | RequestTransferExit | Complete flash transfer |
| 0x3E | TesterPresent | Keep session alive |
| 0x85 | ControlDTCSetting | Enable/disable DTC logging |

### UDS Session Flow (Typical Reflash)
```
Tester                                    ECU
  │── 0x10 02 (Extended Session) ────────►│
  │◄── 0x50 02 (Positive Response) ───────│
  │── 0x27 01 (Request Seed) ────────────►│
  │◄── 0x67 01 [seed] ────────────────────│
  │── 0x27 02 [key] ─────────────────────►│
  │◄── 0x67 02 (Security Unlocked) ───────│
  │── 0x10 02 (Programming Session) ─────►│
  │◄── 0x50 02 ───────────────────────────│
  │── 0x34 [addr, size] (RequestDownload)─►│
  │◄── 0x74 [blocksize] ──────────────────│
  │── 0x36 01 [data block 1] ────────────►│
  │◄── 0x76 01 ───────────────────────────│
  │── 0x36 02 [data block 2] ────────────►│
  │   ... (repeat for all blocks)          │
  │── 0x37 (RequestTransferExit) ─────────►│
  │◄── 0x77 ──────────────────────────────│
  │── 0x11 01 (ECUReset - Hard) ──────────►│
  │◄── 0x51 01 ───────────────────────────│
```

### UDS Negative Response Codes (NRC)
| NRC | Name | Meaning |
|-----|------|---------|
| 0x12 | SubFunctionNotSupported | Service sub-function invalid |
| 0x13 | IncorrectMessageLength | Wrong payload size |
| 0x22 | ConditionsNotCorrect | Prerequisites not met |
| 0x31 | RequestOutOfRange | Parameter invalid |
| 0x33 | SecurityAccessDenied | Security not unlocked |
| 0x35 | InvalidKey | Wrong security key |
| 0x72 | GeneralProgrammingFailure | Flash write failed |
| 0x78 | ResponsePending | ECU needs more time |

---

## OBD-II (On-Board Diagnostics)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | On-Board Diagnostics, 2nd generation |
| Standard | SAE J1979 (US), ISO 15031 (international) |
| Mandated | US: 1996+, EU: 2001+ (all road vehicles) |
| Purpose | Emissions-related fault monitoring |
| Transport | CAN (primary), K-line (legacy) |
| Connector | OBD-II 16-pin (SAE J1962) — standardized location |
| PIDs | Parameter IDs (standardized data requests) |

### OBD-II Standard PIDs
| PID | Description | Mode |
|-----|-------------|------|
| 0x00 | PIDs supported [01-20] | 01 |
| 0x04 | Calculated engine load | 01 |
| 0x05 | Engine coolant temperature | 01 |
| 0x0C | Engine RPM | 01 |
| 0x0D | Vehicle speed | 01 |
| 0x0F | Intake air temperature | 01 |
| 0x11 | Throttle position | 01 |
| 0x1F | Run time since start | 01 |

### OBD-II Modes
| Mode | Purpose |
|------|---------|
| 01 | Show current data (live PIDs) |
| 02 | Show freeze frame data |
| 03 | Show stored DTCs |
| 04 | Clear DTCs and freeze frame |
| 05 | Test results (O2 sensors) |
| 06 | Test results (non-continuous) |
| 07 | Show pending DTCs |
| 09 | Vehicle information (VIN, calibration ID) |

---

## DoIP (Diagnostics over Internet Protocol)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Diagnostics over Internet Protocol |
| Standard | ISO 13400-1/2/3/4 |
| Year | 2012 |
| Transport | TCP (diagnostic messages), UDP (discovery) |
| Port | TCP 13400, UDP 13400 |
| Purpose | UDS over Ethernet (replacing CAN diagnostics) |
| Advantage | High bandwidth (fast reflash), remote diagnostics |

### DoIP Architecture
```
┌─────────────┐         ┌─────────────────────────────────────┐
│  Diagnostic │  TCP    │  DoIP Gateway                        │
│  Tester     │◄──────►│  (Edge Node)                         │
│  (PC/Cloud) │  13400  │                                      │
└─────────────┘         │  ┌──────┐  ┌──────┐  ┌──────┐      │
                        │  │ECU 1 │  │ECU 2 │  │ECU 3 │      │
                        │  │(CAN) │  │(Eth) │  │(LIN) │      │
                        │  └──────┘  └──────┘  └──────┘      │
                        └─────────────────────────────────────┘
```

### DoIP vs CAN Diagnostics
| Feature | CAN (ISO-TP) | DoIP |
|---------|-------------|------|
| Bandwidth | 500 kbps (shared bus) | 100 Mbps+ (dedicated) |
| Flash time (1GB) | Hours | Minutes |
| Remote possible | No (physical connector) | Yes (over IP/cloud) |
| Discovery | Fixed CAN IDs | UDP broadcast/multicast |
| Security | Seed-key | TLS + certificate + seed-key |

---

## XCP (Universal Measurement and Calibration Protocol)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Universal Measurement and Calibration Protocol |
| Standard | ASAM XCP (v1.0: 2003, v1.5+) |
| Creator | ASAM (Association for Standardisation of Automation and Measuring) |
| Transport | CAN, CAN-FD, Ethernet (UDP/TCP), USB, SxI |
| Purpose | Real-time ECU measurement and calibration during development |
| Model | Master (tool) - Slave (ECU) |

### XCP Key Features
- **DAQ (Data Acquisition)**: Real-time measurement of ECU variables
- **STIM (Stimulation)**: Inject values into ECU (bypassing sensors)
- **Calibration**: Modify parameters in real-time (tuning)
- **Flash programming**: Upload calibration data
- **A2L file**: Describes ECU memory layout (what's where, scaling)

### XCP Use Case
```
Developer PC (CANape/INCA)  ←──XCP over Ethernet──→  ECU (SA8295P)
        │                                                 │
        │  Read engine_torque @ 1 kHz                    │  DAQ list
        │  Read fuel_injection_time @ 100 Hz             │  
        │  Modify PID_Kp = 0.85 (calibration)           │  STIM
        │  Flash new cal data to ECU                     │  PGM
```

---

## AUTOSAR Communication Stack

### Classic AUTOSAR COM Stack
```
┌────────────────────────────────────────────────────────┐
│  Application SWC (Software Component)                   │
├────────────────────────────────────────────────────────┤
│  RTE (Runtime Environment)                              │
├────────────────────────────────────────────────────────┤
│  COM (Communication — signal packing/routing)           │
├────────────────────────────────────────────────────────┤
│  PDU Router (Protocol Data Unit routing)                │
├────────────────────────────────────────────────────────┤
│  Transport Protocol                                     │
│  ├── CanTp (CAN Transport — ISO-TP segmentation)       │
│  ├── FrTp (FlexRay Transport)                          │
│  ├── DoIP (Ethernet)                                   │
│  └── LinTp (LIN Transport)                             │
├────────────────────────────────────────────────────────┤
│  Interface Layer                                        │
│  ├── CanIf (CAN Interface)                             │
│  ├── FrIf (FlexRay Interface)                          │
│  ├── EthIf (Ethernet Interface)                        │
│  └── LinIf (LIN Interface)                             │
├────────────────────────────────────────────────────────┤
│  Driver Layer                                           │
│  ├── Can Driver                                        │
│  ├── Fr Driver                                         │
│  ├── Eth Driver + EthTrcv                              │
│  └── Lin Driver                                        │
└────────────────────────────────────────────────────────┘
```

### SecOC (Secure Onboard Communication)
| Field | Value |
|-------|-------|
| Purpose | Authenticate AUTOSAR PDUs (prevent spoofing) |
| Standard | AUTOSAR SecOC specification |
| Method | CMAC (truncated MAC appended to PDU) |
| Counter | Freshness value (prevents replay) |
| Scope | Any bus (CAN, Ethernet, etc.) |
| Threat model | Attacker injects forged CAN/Ethernet messages |

### SecOC Frame
```
Original PDU:     │ Data Payload │
                           ↓ SecOC processing
Secured PDU:      │ Data Payload │ Freshness Value │ Truncated MAC │
                                    (counter/part)     (auth tag)
```

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | UDS SID 0x22? | ReadDataByIdentifier (read ECU data) |
| 2 | UDS SID 0x27? | SecurityAccess (seed-key unlock) |
| 3 | UDS SID 0x34? | RequestDownload (begin flash programming) |
| 4 | UDS NRC 0x78? | ResponsePending (ECU busy, wait) |
| 5 | OBD-II mandated since? | US: 1996, EU: 2001 |
| 6 | OBD-II PID 0x0C? | Engine RPM |
| 7 | DoIP port? | TCP/UDP 13400 |
| 8 | DoIP advantage? | High-bandwidth diagnostics over Ethernet (fast flash) |
| 9 | XCP DAQ? | Data Acquisition (real-time variable measurement) |
| 10 | XCP A2L file? | Describes ECU memory layout (variables, addresses, scaling) |
| 11 | SecOC purpose? | Authenticate PDUs (MAC + freshness counter) |
| 12 | ISO-TP purpose? | Segment CAN messages > 8 bytes (multi-frame) |
| 13 | AUTOSAR COM? | Signal packing and routing between SWCs |
| 14 | UDS sessions? | Default, Extended, Programming, Safety System |
| 15 | OBD-II connector? | 16-pin SAE J1962 (mandated location: driver-side) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Walk through a UDS ECU reflash sequence and explain each step's purpose.**
A: (1) Extended session (0x10 02): enable diagnostic services. (2) SecurityAccess (0x27): prove authorization (seed-key challenge). (3) Programming session (0x10 02): enable flash write services. (4) Pre-conditions: disable DTCs (0x85), check conditions (0x31 routine). (5) RequestDownload (0x34): specify address/size, get max block size. (6) TransferData (0x36): send firmware in blocks. (7) RequestTransferExit (0x37): finalize. (8) Verify (0x31 routine: CRC check). (9) ECUReset (0x11): boot new firmware.

**Q2: Why is DoIP replacing CAN-based diagnostics, and what security implications does it have?**
A: CAN diagnostics: limited to ~500kbps shared bus → flashing 1GB firmware takes hours. DoIP over Ethernet: dedicated 100Mbps+ → minutes. Also enables remote diagnostics (OTA) and cloud-based tools. Security implications: CAN was "secure by obscurity" (physical access needed). Ethernet/IP exposes diagnostic gateway to network — requires TLS encryption, mutual authentication (X.509 certificates), firewall rules, and careful network segmentation to prevent remote exploitation.

**Q3: Explain SecOC and the freshness counter mechanism.**
A: SecOC adds a truncated CMAC (e.g., 24-64 bits) to each PDU for authentication. The freshness value (counter/timestamp) prevents replay attacks — each message has a unique counter. Receiver maintains expected counter and rejects stale messages. Challenge: CAN bandwidth is limited, so MAC is truncated (trade-off: security vs overhead). Counter synchronization requires a freshness manager. Trip-counter + message-counter scheme avoids full counter in every frame.

**Q4: What is the difference between OBD-II and UDS?**
A: OBD-II (SAE J1979): legally mandated, emissions-only, standardized PIDs (all cars respond same way), fixed Mode 01-09 services, public/open (any scan tool works). UDS (ISO 14229): manufacturer-specific diagnostic protocol, covers ALL vehicle functions (not just emissions), proprietary DIDs, secured access, used for development/production/dealer diagnostics. In practice: OBD-II is the "public API" and UDS is the "private API" of vehicle diagnostics.

**Q5: How does XCP differ from UDS for ECU development?**
A: XCP: designed for real-time measurement and calibration DURING development. Read variables at high frequency (1-10 kHz DAQ), modify calibration parameters live, directly access memory by address (A2L file maps names to addresses). UDS: designed for diagnostics and service, lower frequency, standardized service interface, used in production. In development: XCP with CANape/INCA for tuning. In production: UDS with dealer diagnostic tools for fault finding.

---

END OF PART 11 — AUTOMOTIVE DIAGNOSTICS & AUTOSAR
