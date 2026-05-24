# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 13: AEROSPACE & MILITARY PROTOCOLS                                   ║
# ║  MIL-STD-1553, ARINC 429/664, SpaceWire, CCSDS, DO-254/178               ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Aerospace and military protocols prioritize determinism, fault tolerance, and certification over raw speed. These systems must operate for decades, in extreme environments, under strict certification (DO-178C for software, DO-254 for hardware). A single protocol error can cause loss of aircraft or spacecraft.

## PURPOSE
Enable ultra-reliable, deterministic communication between avionics/space systems with guaranteed worst-case behavior and traceability for certification.

## KEY REQUIREMENTS
- **Deterministic**: Bounded worst-case latency (provable)
- **Fault-tolerant**: Redundant buses, bit-level error detection
- **Certified**: DO-178C (DAL A-E), ECSS for space
- **Long lifecycle**: 20-40 years (no obsolescence)
- **EMI/radiation hardened**: High-altitude radiation, EMP
- **Temperature**: -55°C to +125°C (military grade)

---

## PROTOCOL FAMILY TABLE

| Protocol | Domain | Year | Speed | Topology | Deterministic | Redundancy |
|----------|--------|------|-------|----------|---------------|------------|
| MIL-STD-1553 | Military | 1973 | 1 Mbps | Dual-bus | Yes (C/C) | Dual |
| ARINC 429 | Commercial aviation | 1977 | 100 kbps | Unidirectional P2P | Yes | Multiple Tx |
| ARINC 664 (AFDX) | Commercial aviation | 2005 | 100 Mbps | Switched Ethernet | Yes (VL) | Dual |
| SpaceWire | Space | 2003 | 2-400 Mbps | Network (routers) | Partial | Redundant |
| SpaceFibre | Space | 2019 | 6.25 Gbps | Network (P2P) | Yes (QoS) | Redundant |
| CCSDS | Space telecommand | 1982 | Variable | P2P (space-ground) | Yes | FEC |
| TTP (Time-Triggered) | Safety-critical | 1998 | 25 Mbps | Bus/Star | Yes (TDMA) | Dual |
| TTEthernet | Safety-critical | 2011 | 1 Gbps | Switched Ethernet | Yes (TT) | Triple |
| FC-AE (Fibre Channel) | Military | 2004 | 1-4 Gbps | Switched/loop | Deterministic | Dual |
| CAN Aerospace | Avionics | 2006 | 1 Mbps | Bus | Priority | Dual |

---

## MIL-STD-1553

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Military Standard 1553 Digital Time Division C/C Data Bus |
| Standard | MIL-STD-1553B (1978, still active) |
| Year | 1973 (1553A), 1978 (1553B) |
| Creator | US Department of Defense |
| Speed | 1 Mbps |
| Encoding | Manchester II biphase |
| Topology | Dual redundant bus (Stub-coupled) |
| Nodes | 1 Bus Controller (BC) + up to 31 Remote Terminals (RT) |
| Word size | 20 bits (16 data + 3 sync + 1 parity) |
| Deterministic | Yes (command/response, BC-controlled) |
| Use | Fighter jets (F-16/F-35), tanks, ships, satellites |

### MIL-STD-1553 Architecture
```
          BUS A (Primary)
═══════════╤══════════╤══════════╤════════════
           │          │          │
        ┌──┴──┐   ┌──┴──┐   ┌──┴──┐
        │ BC  │   │RT 1 │   │RT 2 │  ... RT 31
        │     │   │     │   │     │
        └──┬──┘   └──┬──┘   └──┬──┘
           │          │          │
═══════════╧══════════╧══════════╧════════════
          BUS B (Backup - identical)
```

### 1553 Transaction Types
| Type | Flow | Description |
|------|------|-------------|
| BC→RT | Command + Data → RT | Bus Controller sends to Remote Terminal |
| RT→BC | Command → RT → Data | BC requests data from RT |
| RT→RT | 2 Commands → Rx RT → Data → Tx RT | Transfer between two RTs |
| Broadcast | Command → All RTs | One-to-all (no response) |
| Mode Code | Special commands | Reset, sync, self-test |

### Why 1553 Still Exists (50+ years!)
- **Proven**: Billions of flight hours, known failure modes
- **Certified**: Established certification basis (DO-254)
- **Radiation tolerant**: Manchester encoding is robust
- **Simple**: Deterministic, easy to analyze timing
- **Installed base**: Retrofitting entire fleet is prohibitively expensive
- **Adequate**: Many military systems only need 1 Mbps

---

## ARINC 429

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | ARINC Specification 429 (Mark 33 DITS) |
| Standard | ARINC 429 (Airlines Electronic Engineering Committee) |
| Year | 1977 |
| Speed | Low: 12.5 kbps, High: 100 kbps |
| Topology | Unidirectional point-to-point (1 Tx → up to 20 Rx) |
| Wires | Twisted pair (differential) |
| Word | 32 bits (fixed format) |
| Encoding | Bipolar RZ (Return to Zero) |
| Use | Commercial aircraft (B737, A320, etc.) |

### ARINC 429 Word Format
```
┌────┬────────┬───┬──────────────────────┬──┬───────┐
│Bit │ Label  │SDI│      Data            │SSM│Parity │
│    │(8 bits)│(2)│   (19 bits)          │(2)│(1 bit)│
└────┴────────┴───┴──────────────────────┴──┴───────┘
 32                                                  1

Label: Identifies parameter (e.g., 0310 = altitude)
SDI: Source/Destination Identifier
Data: BNR (binary) or BCD (decimal) or Discrete
SSM: Sign/Status Matrix (validity)
P: Odd parity
```

### ARINC 429 Key Properties
- **Unidirectional**: Each wire pair has ONE transmitter → many receivers
- **Self-clocking**: Bipolar RZ encoding (always returns to zero)
- **No bus contention**: One transmitter per line (never collides)
- **Simple**: No protocol stack, no handshaking, no arbitration
- **Label-based**: Parameter identified by 8-bit label code
- **Thousands of labels**: Standardized parameter assignments

---

## ARINC 664 (AFDX — Avionics Full-Duplex Switched Ethernet)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | ARINC 664 Part 7 (AFDX) |
| Standard | ARINC 664 / AFDX |
| Year | 2005 (first aircraft: A380) |
| Base | Ethernet 100BASE-TX (standard hardware) |
| Speed | 100 Mbps |
| Topology | Dual-redundant switched network |
| Determinism | Virtual Links (VL) with guaranteed bandwidth |
| Use | A380, A350, B787, A400M, military UAVs |

### AFDX Key Concepts
- **Virtual Link (VL)**: Fixed path from one source to N destinations with guaranteed bandwidth (BAG: Bandwidth Allocation Gap)
- **Dual redundancy**: Two independent networks (A + B), end-system selects valid frame
- **Traffic policing**: Switch enforces VL bandwidth contracts
- **Standard Ethernet frame**: With extra sequence number for integrity

### AFDX vs Standard Ethernet
| Feature | Standard Ethernet | AFDX |
|---------|-------------------|------|
| Deterministic | No | Yes (VL policing) |
| Redundancy | Optional | Mandatory dual |
| Traffic contract | None | VL with BAG + Jitter |
| Sequence checking | No | Yes (in-order, no loss) |
| Max frame delay | Unbounded | Bounded (calculable) |
| Certification | N/A | DO-254/DO-178C capable |

---

## SpaceWire

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | SpaceWire |
| Standard | ECSS-E-ST-50-12C (ESA) |
| Year | 2003 |
| Creator | ESA (European Space Agency) |
| Speed | 2-400 Mbps (link rate programmable) |
| Encoding | Data-Strobe (DS encoding) |
| Topology | Network with routers (packet switching) |
| Wires | 4 pairs (2 data + 2 strobe, each direction) |
| Use | ESA missions, some NASA, JAXA (satellite payloads, instruments) |

### SpaceWire Network
```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Sensor 1 │───►│          │───►│ Processor│
│          │◄───│  Router  │◄───│          │
└──────────┘    │  (SpW)   │    └──────────┘
                │          │
┌──────────┐    │          │    ┌──────────┐
│ Sensor 2 │───►│          │───►│ Mass     │
│          │◄───│          │◄───│ Memory   │
└──────────┘    └──────────┘    └──────────┘
```

### SpaceFibre (Next-gen SpaceWire)
- Speed: 6.25 Gbps per lane (multi-lane)
- QoS: Virtual Channels with guaranteed bandwidth
- FDIR: Fault Detection, Isolation, Recovery built-in
- Designed for: next-gen Earth observation (high data rate instruments)

---

## CCSDS (Consultative Committee for Space Data Systems)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | CCSDS Space Data Link / Telecommand / Telemetry |
| Standard | CCSDS Blue Books (100+ standards) |
| Year | 1982 |
| Creator | CCSDS (NASA, ESA, JAXA, ROSCOSMOS, etc.) |
| Purpose | Space-to-ground data transfer standards |
| Key protocols | TM (Telemetry), TC (Telecommand), AOS, Proximity-1, DTN |

### CCSDS Stack
```
Space Link Layer:
  ├── TM (Telemetry): spacecraft → ground
  ├── TC (Telecommand): ground → spacecraft
  ├── AOS (Advanced Orbiting Systems): high-rate telemetry
  └── Proximity-1: short-range (rover ↔ orbiter)

Coding/Sync Layer:
  ├── Turbo codes, LDPC, Reed-Solomon (FEC)
  └── Frame synchronization (ASM markers)

Physical Layer:
  ├── RF modulation (QPSK, 8PSK, OQPSK)
  └── Frequency bands (S, X, Ka, optical)
```

---

## TTEthernet (Time-Triggered Ethernet)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Time-Triggered Ethernet |
| Standard | SAE AS6802 / ARINC 664 Part 7 complement |
| Year | 2011 |
| Creator | TTTech |
| Speed | 100 Mbps - 1 Gbps |
| Determinism | Time-triggered (TDMA) + Rate-constrained + Best-effort |
| Use | NASA Orion, F-35 (portions), Airbus research |

### TTEthernet Traffic Classes
| Class | Name | Guarantee | Use |
|-------|------|-----------|-----|
| TT | Time-Triggered | Fixed schedule (µs precision) | Safety-critical control |
| RC | Rate-Constrained | Bounded latency (like AFDX VL) | Avionics data |
| BE | Best-Effort | No guarantee | Non-critical (download/debug) |

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | MIL-STD-1553 speed? | 1 Mbps, Manchester II, 31 RTs max |
| 2 | 1553 topology? | Dual redundant bus (BC + 31 RTs) |
| 3 | ARINC 429 directionality? | Unidirectional (1 Tx → up to 20 Rx per wire pair) |
| 4 | ARINC 429 word size? | 32 bits (label + SDI + data + SSM + parity) |
| 5 | AFDX base? | Standard Ethernet 100BASE-TX + Virtual Links |
| 6 | AFDX determinism? | Virtual Links with BAG (Bandwidth Allocation Gap) |
| 7 | SpaceWire speed? | 2-400 Mbps (programmable link rate) |
| 8 | SpaceWire encoding? | Data-Strobe (DS) encoding |
| 9 | CCSDS purpose? | Space-ground data transfer (telemetry, telecommand) |
| 10 | TTEthernet classes? | TT (time-triggered), RC (rate-constrained), BE (best-effort) |
| 11 | Why 1553 still used? | Proven, certified, radiation tolerant, 50+ years of flight heritage |
| 12 | AFDX aircraft? | A380, A350, B787 (commercial), military UAVs |

---

## INTERVIEW QUESTIONS (3)

**Q1: Why has MIL-STD-1553 survived 50+ years while other technologies obsolesced?**
A: (1) Certification heritage: established safety basis, re-certification costs billions. (2) Adequate performance: many military systems need <1 Mbps. (3) Radiation/EMI tolerance: Manchester encoding + transformer coupling = inherently robust. (4) Deterministic by design: command/response with single BC guarantees timing. (5) Installed base: millions of deployed platforms. (6) Risk aversion: military prioritizes proven reliability over performance. New systems increasingly use 1553 replacement (Fibre Channel, TTEthernet) but legacy platforms keep 1553 for decades more.

**Q2: How does AFDX provide determinism over standard Ethernet?**
A: AFDX uses Virtual Links (VL): each VL has a guaranteed maximum bandwidth defined by BAG (minimum frame spacing) and maximum frame size. Switches enforce these contracts — if a VL exceeds its allocated rate, excess frames are dropped. Combined with dual-redundancy and sequence numbering, worst-case end-to-end latency is mathematically bounded and provable for DO-178C certification. Unlike best-effort Ethernet, no VL can starve another.

**Q3: Compare SpaceWire and MIL-STD-1553 for a new satellite design.**
A: 1553: 1 Mbps, simple, flight-proven, deterministic (C/C), but low bandwidth. SpaceWire: 2-400 Mbps, packet-switched network with routers, supports high-data-rate instruments (cameras, radar). Choose 1553: low-rate telemetry/command, high reliability subsystem (attitude control). Choose SpaceWire: payload data handling (instruments generating Gbps), mass memory interface. Modern satellites often use BOTH: 1553 for critical command/control, SpaceWire for payload data.

---

END OF PART 13 — AEROSPACE & MILITARY PROTOCOLS
