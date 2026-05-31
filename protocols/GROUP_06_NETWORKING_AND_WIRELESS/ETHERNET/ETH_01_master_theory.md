# AUTOMOTIVE ETHERNET — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 01 of 08
# Depth: Encyclopedia-grade | Domain: Embedded + Automotive + Android
# ════════════════════════════════════════════════════════════════════

---

# SECTION 1: METADATA & IDENTITY

| Field | Detail |
|-------|--------|
| Full Name | Automotive Ethernet (IEEE 802.3 variants for vehicles) |
| Origin | BroadR-Reach (Broadcom, 2011) → standardized by IEEE |
| Key Standards | 802.3bw (100BASE-T1), 802.3bp (1000BASE-T1), 802.3ch (Multi-Gig) |
| Physical Medium | Single Unshielded Twisted Pair (1 UTP) |
| Speeds | 10M, 100M, 1G, 2.5G, 5G, 10G |
| Duplex | Full-duplex (simultaneous TX + RX on same pair) |
| Topology | Point-to-point links connected via Ethernet switches |
| Max Reach | 15m (100BASE-T1) to 40m (1000BASE-T1 long reach) |
| Temperature | -40°C to +105°C (automotive grade) |
| EMC | Meets automotive EMC (CISPR 25 Class 5) |
| Frame Size | 64–1518 bytes (standard) / up to 9000+ (jumbo) |
| Addressing | 48-bit MAC address |
| Upper Protocols | TCP/IP, UDP, SOME/IP, DoIP, AVB/TSN |
| AUTOSAR | Full Ethernet stack (EthIf, TcpIp, SoAd, SD) |
| Key Players | Broadcom, Marvell, NXP, Realtek, Microchip, TI |

---

# SECTION 2: HISTORY & EVOLUTION

## Timeline

| Year | Event |
|------|-------|
| 1973 | Ethernet invented at Xerox PARC (Bob Metcalfe) |
| 1983 | IEEE 802.3 standard published (10BASE5) |
| 1995 | Fast Ethernet 100BASE-TX (100 Mbps, 2 pairs Cat5) |
| 1999 | Gigabit Ethernet 1000BASE-T (1 Gbps, 4 pairs Cat5e) |
| 2008 | BMW/Broadcom start automotive Ethernet research |
| 2011 | Broadcom BroadR-Reach: 100 Mbps over single pair |
| 2012 | OPEN Alliance SIG formed (automotive Ethernet consortium) |
| 2014 | First production vehicle with Ethernet (BMW, diagnostics) |
| 2015 | IEEE 802.3bw: 100BASE-T1 standardized |
| 2016 | IEEE 802.3bp: 1000BASE-T1 standardized |
| 2019 | IEEE 802.3cg: 10BASE-T1S (multidrop, CAN replacement) |
| 2020 | IEEE 802.3ch: 2.5/5/10GBASE-T1 (Multi-Gigabit) |
| 2022+ | Mass deployment in ADAS, cameras, zonal architectures |

## Why Automotive Ethernet Was Created

Traditional vehicle networks hit walls:
- **CAN**: Max 2 Mbps → can't carry camera video (needs ~100+ Mbps per camera)
- **MOST**: 150 Mbps but expensive, ring topology (single point of failure), proprietary
- **LVDS**: High bandwidth but point-to-point only (no switching/routing)
- **FlexRay**: Only 10 Mbps, expensive, limited vendor support

Automotive Ethernet solves all:
- Scalable speed (100M → 10G) — same protocol, different PHYs
- Switched topology — flexible, fault-tolerant
- Single pair — 30% cable weight reduction
- IP-based — reuse massive IT/telecom ecosystem
- Standard — IEEE open, multi-vendor

## The BroadR-Reach Innovation

Broadcom's key insight: use **PAM-3 coding** (3 voltage levels: -1, 0, +1) to achieve 100 Mbps full-duplex on ONE unshielded twisted pair — compared to standard 100BASE-TX which needs 2 pairs. This was revolutionary for automotive where every gram of cable weight matters.

---

# SECTION 3: PHYSICAL LAYER — THE SINGLE PAIR MAGIC

## 100BASE-T1 (IEEE 802.3bw)

| Parameter | Value |
|-----------|-------|
| Speed | 100 Mbps full-duplex |
| Medium | 1 unshielded twisted pair (UTP) |
| Coding | PAM-3 (3 levels: -1, 0, +1) |
| Symbol rate | 66.67 MBaud |
| Max reach | 15 meters |
| Duplex | Full (TX+RX simultaneously on SAME pair!) |
| How? | Echo cancellation separates TX from RX |
| Temperature | -40°C to +105°C |
| EMC | CISPR 25 Class 5 |

### How Full-Duplex on One Pair Works
```
Standard Ethernet (100BASE-TX): SEPARATE pairs
  Pair 1: TX →→→→→→→→→→
  Pair 2: RX ←←←←←←←←←←

Automotive Ethernet (100BASE-T1): SAME pair
  Single Pair: TX ←→ RX (simultaneous!)
  
Magic: HYBRID + ECHO CANCELLATION
  ┌──────────┐           ┌──────────┐
  │  PHY A   │           │  PHY B   │
  │          │  1 pair   │          │
  │ TX──┐    ├───────────┤    ┌──TX │
  │     ├─Hybrid         Hybrid─┤   │
  │ RX──┘    │           │    └──RX │
  │  Echo    │           │  Echo    │
  │  Cancel  │           │  Cancel  │
  └──────────┘           └──────────┘

PHY subtracts its own TX signal from the received signal
→ what remains is the REMOTE TX signal = our RX data
```

### PAM-3 Coding
```
Voltage Levels:
  +1V ────────  Level +1 (symbol value = +1)
   0V ────────  Level  0 (symbol value = 0)
  -1V ────────  Level -1 (symbol value = -1)

3 levels × 66.67 MBaud = log₂(3) × 66.67 = 1.585 × 66.67 = 105.7 Mbps raw
→ After encoding overhead: 100 Mbps net data rate

Compared to standard NRZ (2 levels):
  100M NRZ needs 100 MBaud → 100 MHz bandwidth
  100M PAM-3 needs 66.67 MBaud → 66.67 MHz bandwidth (LESS!)
  → Lower bandwidth = better EMC, longer reach
```

## 1000BASE-T1 (IEEE 802.3bp)

| Parameter | Value |
|-----------|-------|
| Speed | 1000 Mbps (1 Gbps) full-duplex |
| Medium | 1 UTP (shielded optional for long reach) |
| Coding | PAM-3, 3 symbols per clock (750 MBaud effective) |
| Max reach | 15m (standard) / 40m (long reach, shielded) |
| Duplex | Full (echo cancellation) |
| Key addition | DFE (Decision Feedback Equalizer) for ISI |

## Multi-Gigabit: 802.3ch (2.5G / 5G / 10G BASE-T1)

| Speed | Coding | Pairs | Reach | Use Case |
|-------|--------|-------|-------|----------|
| 2.5 Gbps | PAM-4 | 1 STP | 15m | Camera aggregation |
| 5 Gbps | PAM-4 | 1 STP | 15m | Multi-camera fusion |
| 10 Gbps | PAM-4 | 1 STP | 15m | Backbone, data recorder |

PAM-4 = 4 voltage levels → 2 bits per symbol → higher data rate same bandwidth.

## 10BASE-T1S — The CAN Killer

| Parameter | Value |
|-----------|-------|
| Speed | 10 Mbps |
| Topology | **Multidrop bus** (up to 8 nodes without switch!) |
| Medium | 1 UTP |
| Reach | 25m (bus length) |
| Access | PLCA (Physical Layer Collision Avoidance) |
| Target | Replace CAN/LIN for sensors, actuators |

This is revolutionary: Ethernet with BUS topology (like CAN!) — no switch needed for simple sensor networks. Lower cost than switch-based, but still IP-capable.

---

# SECTION 4: PHY LAYER DETAILS

## PHY Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Ethernet PHY (e.g., Marvell 88Q2112)     │
│                                                              │
│  ┌──────────────┐   ┌─────────────┐   ┌────────────────┐  │
│  │    PCS       │   │    PMA      │   │    PMD         │  │
│  │ (Coding/     │───│ (Scrambler/ │───│ (Line Driver/  │──── Twisted
│  │  Framing)    │   │  Equalizer) │   │  Receiver/     │    Pair
│  │              │   │             │   │  Echo Cancel)  │  │
│  └──────┬───────┘   └─────────────┘   └────────────────┘  │
│         │                                                    │
│  ┌──────┴───────┐                                           │
│  │    MII/RGMII │ ← Interface to MAC                       │
│  │    /SGMII    │                                           │
│  └──────────────┘                                           │
└─────────────────────────────────────────────────────────────┘
```

## MAC-to-PHY Interfaces

| Interface | Signals | Speed | Use |
|-----------|---------|-------|-----|
| MII | 16 pins | 10/100M | Legacy |
| RMII | 9 pins | 10/100M | Pin-reduced |
| RGMII | 12 pins | 10/100/1000M | Common automotive |
| SGMII | 4 (serial) | 10/100/1000M | High-speed serial |
| USXGMII | 2 (serial) | Multi-Gig | 2.5G+ |
| XFI/SFI | 2 (serial) | 10G | 10GBASE-T1 |

## PHY Master/Slave

In automotive Ethernet, one end is **Master** and one is **Slave**:
- Master provides the timing reference (clock recovery)
- Slave synchronizes to master's clock
- Configured via register or auto-negotiation
- NOT related to data priority — just clock source

## Link Training / Auto-Negotiation

100BASE-T1 / 1000BASE-T1:
1. PHYs exchange capabilities
2. Agree on speed, duplex, master/slave role
3. Train equalizers (adapt to specific cable)
4. Link established → data flows

---

# SECTION 5: ETHERNET FRAME STRUCTURE

## Standard Ethernet Frame (IEEE 802.3)

```
┌─────────┬──────┬──────────┬──────────┬──────────┬─────────┬─────┐
│Preamble │ SFD  │  Dest    │  Source  │EtherType │ Payload │ FCS │
│ 7 bytes │1 byte│  MAC     │  MAC     │ /Length  │46-1500  │4 by │
│ 0xAA... │ 0xAB │ 6 bytes  │ 6 bytes  │ 2 bytes  │ bytes   │     │
└─────────┴──────┴──────────┴──────────┴──────────┴─────────┴─────┘
│←─── 8 ──→│     │←──────── 14 ────────→│         │←─ up to 1500 ─→│

Total frame: 64 to 1518 bytes (excluding preamble/SFD)
Minimum payload: 46 bytes (padded if shorter)
Maximum payload: 1500 bytes (standard MTU)
```

## Key EtherType Values (Automotive)

| EtherType | Protocol | Use |
|-----------|----------|-----|
| 0x0800 | IPv4 | Standard IP communication |
| 0x0806 | ARP | Address resolution |
| 0x86DD | IPv6 | Next-gen IP |
| 0x8100 | 802.1Q VLAN tag | VLAN tagging |
| 0x88F7 | PTP (IEEE 1588) | Time sync |
| 0x22F0 | AVB Audio (802.1Qav) | Audio/Video streaming |
| 0x88B5 | Experimental | Testing |
| 0x88CC | LLDP | Link Layer Discovery |
| 0x8902 | 802.1ag CFM | Connectivity Fault Mgmt |

## VLAN Tagged Frame (802.1Q)

```
┌──────┬──────┬────────────┬──────────┬──────────┬─────────┬─────┐
│ Dest │ Src  │ 802.1Q Tag │EtherType │ Payload  │   FCS   │     │
│ MAC  │ MAC  │ (4 bytes)  │          │          │         │     │
└──────┴──────┴────────────┴──────────┴──────────┴─────────┴─────┘
                     │
              ┌──────┴──────┐
              │TPID │  TCI  │
              │0x8100│PCP|DEI|VID│
              │      │3b |1b|12b│
              └─────────────┘
              
PCP = Priority Code Point (0-7, for QoS)
DEI = Drop Eligible Indicator
VID = VLAN Identifier (0-4095)
```

---

# SECTION 6: AUTOMOTIVE ETHERNET NETWORK ARCHITECTURE

## Zonal Architecture (Modern Vehicles 2022+)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VEHICLE NETWORK ARCHITECTURE                      │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐ │
│  │              CENTRAL COMPUTE (HPC)                             │ │
│  │    ┌──────────┐  ┌──────────┐  ┌──────────┐                 │ │
│  │    │ ADAS SoC │  │  IVI SoC │  │ Vehicle  │                 │ │
│  │    │(SA8295P) │  │(SA8155P) │  │ Computer │                 │ │
│  │    └────┬─────┘  └────┬─────┘  └────┬─────┘                 │ │
│  │         │1G            │1G           │1G                      │ │
│  └─────────┼──────────────┼─────────────┼───────────────────────┘ │
│            │              │             │                           │
│  ┌─────────┴──────────────┴─────────────┴───────────────────────┐ │
│  │              CENTRAL ETHERNET SWITCH (10G backbone)           │ │
│  │         (e.g., Marvell 88Q6113, NXP SJA1110)                │ │
│  └──┬──────────┬──────────┬──────────┬──────────┬──────────┬───┘ │
│     │1G        │1G        │100M      │1G        │100M      │     │
│  ┌──┴───┐   ┌──┴───┐   ┌──┴───┐  ┌──┴───┐   ┌──┴───┐          │
│  │Zone  │   │Zone  │   │Zone  │  │Zone  │   │Zone  │          │
│  │Front │   │Rear  │   │Left  │  │Right │   │Roof  │          │
│  │Gateway│  │Gateway│  │Gateway│ │Gateway│  │Gateway│          │
│  └──┬───┘   └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘          │
│     │          │          │         │          │               │
│  Cameras   Cameras    Sensors   Sensors    Lidar/Radar        │
│  Radar     Parking    Doors     Doors      Cameras            │
│  Lights    Trunk      Windows   Windows                       │
└─────────────────────────────────────────────────────────────────────┘
```

## Gateway ECU Role
The Ethernet switch / gateway bridges:
- Ethernet ←→ CAN (legacy ECUs)
- Ethernet ←→ LIN (body control)
- Ethernet ←→ FlexRay (if present)
- Ethernet ←→ Ethernet (routing between zones)

## Typical Bandwidth Allocation

| Domain | Speed | Traffic Type |
|--------|-------|-------------|
| Camera (each) | 100M–1G | Raw/compressed video |
| Radar (each) | 100M | Point cloud, detections |
| LiDAR | 1G–10G | Dense point clouds |
| IVI / Head Unit | 1G | Streaming, mirroring, updates |
| ADAS Compute | 1G–10G | Fused sensor data |
| Gateway/Body | 100M | CAN-over-Ethernet, control |
| Diagnostics | 100M | DoIP, logging |
| V2X | 100M | External communication |

---

# SECTION 7: TCP/IP STACK IN AUTOMOTIVE

## Protocol Stack Layers

```
┌─────────────────────────────────────────────────────────────┐
│  Application Layer                                          │
│  SOME/IP │ DoIP │ HTTP │ MQTT │ DDS │ Custom Apps          │
├─────────────────────────────────────────────────────────────┤
│  Transport Layer                                            │
│  TCP (reliable) │ UDP (fast, unreliable)                   │
├─────────────────────────────────────────────────────────────┤
│  Network Layer                                              │
│  IPv4 / IPv6 │ ICMP │ ARP/NDP │ IGMP                      │
├─────────────────────────────────────────────────────────────┤
│  Data Link Layer                                            │
│  Ethernet MAC │ 802.1Q VLAN │ TSN (802.1) │ MACsec        │
├─────────────────────────────────────────────────────────────┤
│  Physical Layer                                             │
│  100BASE-T1 │ 1000BASE-T1 │ xGBASE-T1 │ 10BASE-T1S      │
└─────────────────────────────────────────────────────────────┘
```

## IP Addressing in Vehicles

| Approach | Description | Use |
|----------|-------------|-----|
| Static IP | Fixed per ECU at manufacturing | Common in production |
| DHCP | Dynamic assignment | Development/testing |
| Link-local | 169.254.x.x (auto-config) | Fallback |
| IPv6 SLAAC | Stateless auto-config | Future vehicles |

Typical scheme: `192.168.1.0/24`
- Gateway: 192.168.1.1
- ADAS: 192.168.1.10
- IVI: 192.168.1.20
- Camera 1: 192.168.1.101
- Camera 2: 192.168.1.102
- etc.

## Socket Programming in Automotive
```c
// UDP SOME/IP sender (typical automotive pattern)
int sock = socket(AF_INET, SOCK_DGRAM, 0);
struct sockaddr_in dest = {
    .sin_family = AF_INET,
    .sin_port = htons(30490),  // SOME/IP port
    .sin_addr.s_addr = inet_addr("192.168.1.10")
};
sendto(sock, someip_msg, msg_len, 0, 
       (struct sockaddr*)&dest, sizeof(dest));
```

---

# SECTION 8: SOME/IP — SERVICE-ORIENTED MIDDLEWARE

## What is SOME/IP?

**SOME/IP** = Scalable service-Oriented MiddlewarE over IP

The application-layer protocol that makes automotive Ethernet service-oriented (vs. CAN's signal-oriented approach).

## SOME/IP vs CAN: Paradigm Shift

```
CAN (Signal-Oriented):
  ECU_A sends signal "EngineRPM" at 10ms periodic
  ECU_B, ECU_C listen → all get it (broadcast)
  → Fixed, static, no flexibility

SOME/IP (Service-Oriented):
  ECU_A offers SERVICE "EngineData"
  ECU_B subscribes to EVENT "RPM_Changed" 
  ECU_C calls METHOD "GetCurrentRPM()"
  → Dynamic, on-demand, efficient
```

## SOME/IP Message Format

```
┌────────────────────────────────────────────────────────┐
│                    SOME/IP Header                       │
├──────────────┬─────────────────────────────────────────┤
│ Service ID   │ Method/Event ID                         │
│ (16 bits)    │ (16 bits)                               │
├──────────────┼─────────────────────────────────────────┤
│ Length (32 bits) — payload + 8 bytes                   │
├──────────────┬──────────┬──────────┬──────────────────┤
│ Client ID    │ Session ID│ Protocol │ Interface       │
│ (16 bits)    │ (16 bits)│ Version  │ Version         │
├──────────────┴──────────┼──────────┼──────────────────┤
│ Message Type            │Return Code│                  │
│ (REQUEST/RESPONSE/      │(E_OK,    │                  │
│  NOTIFICATION/ERROR)    │ E_NOT_OK)│                  │
├─────────────────────────┴──────────┴──────────────────┤
│                    Payload                              │
│            (serialized data)                           │
└────────────────────────────────────────────────────────┘
```

## SOME/IP Communication Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| **Request/Response** | Client calls method, server returns result | GetVehicleSpeed() → 120 km/h |
| **Fire & Forget** | Client sends, no response expected | SetDisplayBrightness(80) |
| **Notification (Event)** | Server pushes on change/periodic | SpeedChanged → 121 km/h |
| **Field** | Getter/Setter/Notifier combo | Get/Set/Notify Temperature |

## SOME/IP-SD (Service Discovery)

How do ECUs find each other?

```
1. ECU_A offers Service 0x1234:
   → Multicast OFFER on 224.244.224.245:30490

2. ECU_B needs Service 0x1234:
   → Multicast FIND on 224.244.224.245:30490
   
3. ECU_A receives FIND, responds with OFFER (unicast)

4. ECU_B subscribes to EventGroup:
   → SUBSCRIBE message to ECU_A

5. ECU_A acknowledges:
   → SUBSCRIBE_ACK to ECU_B

6. ECU_A sends events to ECU_B (unicast/multicast)
```

---

# SECTION 9: DoIP — DIAGNOSTICS OVER IP (ISO 13400)

## Why DoIP?
- CAN diagnostics (UDS over CAN): ~500 kbps → takes hours for firmware update
- DoIP: 100 Mbps+ → firmware update in minutes
- Same UDS protocol (ISO 14229), just faster transport

## DoIP Architecture

```
┌──────────────┐         ┌──────────────────────────────┐
│  Diagnostic  │  TCP/IP │     Vehicle                   │
│  Tester      ├─────────┤  ┌─────────────┐            │
│  (PC + SW)   │  DoIP   │  │ DoIP Gateway│            │
│              │         │  │ (Edge Node) │            │
└──────────────┘         │  └──────┬──────┘            │
                         │         │ Routes UDS to ECUs │
                         │  ┌──────┴──────┐            │
                         │  │ ECU_A (UDS) │            │
                         │  │ ECU_B (UDS) │            │
                         │  │ ECU_C (UDS) │            │
                         └──┴─────────────┴────────────┘
```

## DoIP Protocol
- Transport: **TCP** (port 13400) for diagnostics
- Activation: Vehicle identification request → tester connects
- Routing: DoIP gateway maps logical addresses to ECU IP addresses
- Security: TLS optional for secure diagnostics

## Key DoIP Messages

| Type | Purpose |
|------|---------|
| Vehicle Identification Request | Discover vehicles on network |
| Vehicle Identification Response | Vehicle announces itself |
| Routing Activation Request | Tester requests diagnostic session |
| Routing Activation Response | Gateway confirms |
| Diagnostic Message | UDS payload (read DTC, flash, etc.) |
| Alive Check | Keep session alive |

---

# SECTION 10: TSN — TIME-SENSITIVE NETWORKING

## Why TSN for Automotive?

Standard Ethernet = "best effort" → no timing guarantees
Automotive needs: camera frames delivered within 1ms, control messages within 100µs

TSN = set of IEEE 802.1 standards that add **determinism** to Ethernet.

## Key TSN Standards

| Standard | Name | Purpose |
|----------|------|---------|
| **802.1AS** | gPTP (generalized PTP) | Time synchronization (<1µs) |
| **802.1Qbv** | Time-Aware Shaper (TAS) | Time-gated queues (TDMA-like) |
| **802.1Qav** | Credit-Based Shaper (CBS) | Bandwidth reservation (AVB) |
| **802.1Qbu/802.3br** | Frame Preemption | Interrupt low-priority frames |
| **802.1Qci** | Per-Stream Filtering & Policing | Protect against babbling |
| **802.1CB** | Seamless Redundancy (FRER) | Zero-loss failover |
| **802.1Qcc** | Stream Reservation Protocol | Reserve bandwidth |

## 802.1AS — Time Synchronization (gPTP)

```
Grand Master Clock (GM)
    │
    │ Sync + Follow-Up messages
    ↓
┌────────┐     ┌────────┐     ┌────────┐
│Switch A│────→│Switch B│────→│ End    │
│(Bridge)│     │(Bridge)│     │Station │
└────────┘     └────────┘     └────────┘
    │               │              │
    t1             t2,t3          t4
    
Accuracy: <1 µs across entire network
Used for: Camera frame sync, sensor fusion timestamp alignment
```

## 802.1Qbv — Time-Aware Shaper (TAS)

```
Time Slots (Gate Control List):
┌────────┬────────┬────────┬────────┬────────┐
│ Slot 0 │ Slot 1 │ Slot 2 │ Slot 3 │ Slot 0 │ ...
│CRITICAL│ CAMERA │  BEST  │ CAMERA │CRITICAL│
│(Control)│(Video)│ EFFORT │(Video) │(Control)│
└────────┴────────┴────────┴────────┴────────┘
  100µs    500µs    200µs    500µs    100µs

Each time slot: only specific priority queues can transmit
→ GUARANTEED latency for critical traffic
→ Like TDMA on Ethernet!
```

## 802.1Qbu/802.3br — Frame Preemption

```
Without preemption:
  [────── Low Priority Frame (1518B) ──────][Critical]
  Critical message waits up to 123µs (worst case at 100Mbps)!

With preemption:
  [── Low Pri ──][INTERRUPT][Critical][RESUME Low Pri ──]
  Critical message gets through in <10µs!
  
Low priority frame is "preempted" (split), critical inserts,
then low priority resumes. Receiver reassembles transparently.
```

---

# SECTION 11: AVB — AUDIO VIDEO BRIDGING

## AVB for Automotive Multimedia

AVB (IEEE 802.1 Audio/Video Bridging) = precursor to TSN, focused on streaming.

| Standard | Purpose |
|----------|---------|
| 802.1AS | Time synchronization (same as TSN) |
| 802.1Qav | Credit-Based Shaper (bandwidth reservation) |
| 802.1BA | AVB system profile |
| IEEE 1722 | AVTP (Audio/Video Transport Protocol) |
| IEEE 1722.1 | Discovery and configuration |

## Use Cases in Vehicle
- Surround-view camera streams (4-8 cameras → head unit)
- Rear-seat entertainment audio/video
- Digital microphone arrays
- Driver monitoring camera
- Replacing MOST for multimedia

## AVTP (IEEE 1722) Frame
```
┌────────┬────────┬────────────────────────────────────┐
│  Eth   │  AVTP  │         Payload                    │
│ Header │ Header │  (H.264, MJPEG, PCM audio, etc.)  │
└────────┴────────┴────────────────────────────────────┘

AVTP Header includes:
- Stream ID (64-bit: identifies camera/audio stream)
- Sequence number (detect packet loss)
- Timestamp (presentation time from gPTP)
- Media-specific fields
```

---

# SECTION 12: ETHERNET SWITCH ARCHITECTURE

## Automotive Ethernet Switch

```
┌──────────────────────────────────────────────────────────────┐
│              AUTOMOTIVE ETHERNET SWITCH                       │
│         (e.g., Marvell 88Q5050, NXP SJA1105)               │
│                                                              │
│  Port 0 ──┐                                                 │
│  Port 1 ──┤   ┌──────────┐  ┌───────────┐  ┌──────────┐  │
│  Port 2 ──┼───│  MAC     │──│ Forwarding│──│  Queues  │  │
│  Port 3 ──┤   │  Layer   │  │  Engine   │  │  (QoS)   │  │
│  Port 4 ──┤   │          │  │ (L2/L3)   │  │  TSN     │  │
│  Port 5 ──┘   └──────────┘  └───────────┘  └──────────┘  │
│                                                              │
│  Features: VLAN, QoS, TSN, MACsec, Port Mirroring         │
│  Management: SPI (from MCU) or in-band (Ethernet)          │
└──────────────────────────────────────────────────────────────┘
```

## Key Automotive Switch ICs

| IC | Vendor | Ports | Speed | Features |
|----|--------|-------|-------|----------|
| SJA1105 | NXP | 5 | 100M/1G | TSN, AVB, low cost |
| SJA1110 | NXP | 10 | 100M/1G + 2.5G | TSN, integrated MCU |
| 88Q5050 | Marvell | 8 | 100M/1G | Full TSN, MACsec |
| 88Q6113 | Marvell | 13 | 100M/1G/2.5G | Zonal gateway |
| KSZ9897 | Microchip | 7 | 100M/1G | Cost-effective |

## Switch Forwarding
1. Frame arrives on port X
2. Switch reads destination MAC address
3. Looks up MAC table → finds output port Y
4. Applies VLAN rules, QoS priority, TSN gates
5. Forwards frame to port Y
6. If unknown MAC → flood to all ports (learning)

---

# SECTION 13: VLAN & QOS IN AUTOMOTIVE

## VLAN Use Cases

| VLAN ID | Name | Traffic |
|---------|------|---------|
| 10 | ADAS | Camera streams, radar, sensor fusion |
| 20 | IVI | Multimedia, navigation, app data |
| 30 | Body | Door control, lights, windows |
| 40 | Diagnostics | DoIP, logging, calibration |
| 50 | OTA | Firmware updates |
| 100 | Management | Switch config, network management |

## Priority (PCP) Mapping

| PCP | Priority | Traffic Class |
|-----|----------|---------------|
| 7 | Highest | Network control (TSN sync) |
| 6 | — | ADAS critical (control commands) |
| 5 | — | Camera video streams (AVB) |
| 4 | — | Interactive (IVI, voice) |
| 3 | — | Streaming (rear-seat video) |
| 2 | — | Best effort (OTA background) |
| 1 | — | Background |
| 0 | Default | Untagged traffic |

---

# SECTION 14: SECURITY — MACsec, SecOC, TLS

## Security Layers

```
┌─────────────────────────────────────────────────────────────┐
│ Application:  TLS 1.3 / DTLS (end-to-end encryption)       │
├─────────────────────────────────────────────────────────────┤
│ Network:      IPsec (IP-level encryption)                   │
├─────────────────────────────────────────────────────────────┤
│ Data Link:    MACsec 802.1AE (wire-speed L2 encryption)    │
├─────────────────────────────────────────────────────────────┤
│ AUTOSAR:      SecOC (Secure On-board Communication)        │
└─────────────────────────────────────────────────────────────┘
```

## MACsec (IEEE 802.1AE)

- Encrypts at Layer 2 (Ethernet frame level)
- AES-GCM-128 or AES-GCM-256
- Provides: Confidentiality + Integrity + Replay protection
- Wire-speed (hardware acceleration in switch/PHY)
- Point-to-point (each link encrypted separately)
- Automotive: protects against bus sniffing on physical wire

## Threats to Automotive Ethernet

| Threat | Description | Mitigation |
|--------|-------------|------------|
| Eavesdropping | Tap into Ethernet wire | MACsec encryption |
| Spoofing | Inject fake frames | MACsec authentication |
| DoS | Flood network | 802.1Qci policing, VLAN isolation |
| Man-in-middle | Insert device between ECUs | MACsec + certificate auth |
| Replay | Capture and retransmit | MACsec sequence numbers |
| Unauthorized access | Rogue device plugs in | 802.1X port authentication |

---

# SECTION 15: AUTOSAR ETHERNET STACK

## Classic AUTOSAR Ethernet Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        APPLICATION (SWC)                         │
├─────────────────────────────────────────────────────────────────┤
│                           RTE                                    │
├─────────┬──────────┬──────────┬──────────┬──────────────────────┤
│ COM     │ DCM      │ SD       │ SoAd     │  (Service Layer)     │
│(Signals)│(Diagnost)│(Service  │(Socket   │                      │
│         │          │ Discov.) │ Adaptor) │                      │
├─────────┴──────────┴──────────┼──────────┤                      │
│                               │ TcpIp    │  (TCP/IP Stack)      │
│                               │ Module   │                      │
├───────────────────────────────┼──────────┤                      │
│                               │ EthIf    │  (Ethernet Interface) │
├───────────────────────────────┼──────────┤                      │
│                               │ EthTrcv  │  (Transceiver Driver) │
├───────────────────────────────┼──────────┤                      │
│                               │ Eth      │  (Controller Driver)  │
└───────────────────────────────┴──────────┴──────────────────────┘
```

## Key AUTOSAR Ethernet Modules

| Module | Purpose |
|--------|---------|
| **Eth** | Ethernet controller driver (MCAL) — frame TX/RX |
| **EthTrcv** | PHY transceiver driver — link up/down, speed |
| **EthIf** | Interface — abstracts multiple Eth controllers |
| **EthSwt** | Switch driver — VLAN, forwarding, port config |
| **TcpIp** | TCP/IP stack — socket API, ARP, DHCP, ICMP |
| **SoAd** | Socket Adaptor — maps PDUs to sockets |
| **SD** | Service Discovery — SOME/IP-SD |
| **DoIP** | Diagnostics over IP |
| **EthTSyn** | Time synchronization (802.1AS/gPTP) |
| **SecOC** | Secure On-board Communication |

## Adaptive AUTOSAR (ara::com)

Modern automotive software uses Adaptive AUTOSAR:
```cpp
// Service consumer (client)
auto proxy = ara::com::FindService<RadarService>();
auto future = proxy->GetDetections();
auto detections = future.get();

// Service provider (server)  
class RadarServiceImpl : public RadarServiceSkeleton {
    ara::core::Future<DetectionList> GetDetections() override {
        return ara::core::MakeFuture(current_detections_);
    }
};
```
Binding: ara::com uses SOME/IP over Ethernet as transport.

---

# SECTION 16: LINUX NETWORKING FOR AUTOMOTIVE

## Linux Network Stack

```
┌────────────────────────────────────────────────────────────┐
│  Userspace: Applications (SOME/IP daemon, DoIP, etc.)      │
│    socket() → bind() → send()/recv()                       │
├────────────────────────────────────────────────────────────┤
│  Socket Layer: AF_INET (TCP/UDP), AF_PACKET (raw)         │
├────────────────────────────────────────────────────────────┤
│  Transport: TCP / UDP                                      │
├────────────────────────────────────────────────────────────┤
│  Network: IPv4/IPv6, routing, netfilter                   │
├────────────────────────────────────────────────────────────┤
│  Data Link: Ethernet driver (MAC), bridge, VLAN, TSN      │
├────────────────────────────────────────────────────────────┤
│  PHY: MDIO, phylib (drivers/net/phy/)                     │
├────────────────────────────────────────────────────────────┤
│  Hardware: MAC Controller + PHY chip + cable              │
└────────────────────────────────────────────────────────────┘
```

## Key Linux Networking Commands (Automotive)

```bash
# Interface status
ip link show eth0
ethtool eth0          # PHY info, speed, link status
ethtool -S eth0       # Statistics (TX/RX counters, errors)

# IP configuration
ip addr add 192.168.1.10/24 dev eth0
ip route add default via 192.168.1.1

# VLAN
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 10.0.10.1/24 dev eth0.10

# Bridge (for switch)
ip link add br0 type bridge
ip link set eth0 master br0
ip link set eth1 master br0

# Traffic capture
tcpdump -i eth0 -w capture.pcap
tcpdump -i eth0 port 30490    # SOME/IP traffic

# Network namespace (for testing)
ip netns add test_ns
ip link set eth1 netns test_ns

# TSN (tc - traffic control)
tc qdisc add dev eth0 parent root taprio \
    num_tc 3 \
    map 2 2 1 0 2 2 2 2 \
    queues 1@0 1@1 1@2 \
    base-time 0 \
    sched-entry S 01 300000 \
    sched-entry S 02 500000 \
    sched-entry S 04 200000
```

## Linux Ethernet Driver Architecture

```
drivers/net/ethernet/
├── stmicro/stmmac/     ← Synopsys DesignWare MAC (very common)
├── freescale/          ← NXP i.MX, Layerscape
├── qualcomm/emac/      ← Qualcomm EMAC
├── marvell/            ← Marvell controllers
└── ti/                 ← TI AM335x, Jacinto

drivers/net/phy/
├── marvell.c           ← Marvell 88Q2112 automotive PHY
├── broadcom.c          ← Broadcom BroadR-Reach PHY
├── nxp-tja11xx.c       ← NXP TJA1100/TJA1101 100BASE-T1
└── micrel.c            ← Microchip/Micrel PHYs
```

## Device Tree for Automotive Ethernet
```dts
&ethernet0 {
    status = "okay";
    phy-mode = "rgmii-id";
    phy-handle = <&phy0>;
    
    fixed-link {
        speed = <1000>;
        full-duplex;
    };
    
    mdio {
        #address-cells = <1>;
        #size-cells = <0>;
        
        phy0: ethernet-phy@0 {
            compatible = "ethernet-phy-id0141.0DD1";  /* Marvell 88Q2112 */
            reg = <0>;
            marvell,master-slave = "master";
        };
    };
};
```

---

# SECTION 17: ANDROID & AUTOMOTIVE ETHERNET

## Android Networking Stack

```
┌──────────────────────────────────────────────────────────┐
│  Java Apps: ConnectivityManager, NetworkRequest           │
├──────────────────────────────────────────────────────────┤
│  Framework: ConnectivityService, EthernetManager         │
├──────────────────────────────────────────────────────────┤
│  Native: netd (network daemon), dns resolver             │
├──────────────────────────────────────────────────────────┤
│  HAL: android.hardware.net.ethernet (AIDL)               │
├──────────────────────────────────────────────────────────┤
│  Kernel: Linux network stack + Ethernet MAC driver       │
├──────────────────────────────────────────────────────────┤
│  Hardware: MAC + PHY + single twisted pair               │
└──────────────────────────────────────────────────────────┘
```

## AAOS Ethernet Use Cases
- **IVI connectivity**: SoC connects to vehicle backbone
- **Vehicle data**: SOME/IP services for speed, gear, fuel
- **Rear cameras**: AVTP video streams over Ethernet
- **OTA updates**: Fast download via DoIP/HTTP
- **Tethering**: Share vehicle's cellular via Ethernet to other ECUs

## Android Ethernet Configuration
```
# SystemProperties
persist.ethernet.interface=eth0
persist.ethernet.ipaddress=192.168.1.20
persist.ethernet.netmask=255.255.255.0
persist.ethernet.gateway=192.168.1.1

# Or via ConnectivityManager API
EthernetManager ethManager = context.getSystemService(EthernetManager.class);
```

---

# SECTION 18: AUTOMOTIVE ETHERNET PHY ICs

## Key PHY Chips

| PHY IC | Vendor | Speed | Features |
|--------|--------|-------|----------|
| **TJA1100** | NXP | 100BASE-T1 | First automotive 100M PHY |
| **TJA1101** | NXP | 100BASE-T1 | Lower power, smaller |
| **88Q2112** | Marvell | 1000BASE-T1 | Most common 1G automotive PHY |
| **88Q2220** | Marvell | 2.5GBASE-T1 | Multi-gig |
| **BCM89810** | Broadcom | 1000BASE-T1 | Original BroadR-Reach |
| **DP83TD510E** | TI | 10BASE-T1S | Multidrop (CAN replacement) |
| **LAN8770** | Microchip | 100BASE-T1 | Cost-effective |

## PHY Registers (MDIO/Clause 45)

```
Standard registers (IEEE 802.3):
  Reg 0: Control (speed, duplex, reset, loopback)
  Reg 1: Status (link up/down, auto-neg complete)
  Reg 2-3: PHY Identifier
  Reg 4-5: Auto-negotiation
  Reg 15+: Vendor-specific (extended features)
  
Access via MDIO bus (2-wire serial, like I2C for PHYs):
  MDC = clock, MDIO = data
  Linux: ethtool, mdio-tools, /sys/bus/mdio_bus/
```

---

# SECTION 19: CONNECTOR & CABLING

## Automotive Ethernet Connectors

| Connector | Type | Pins | Speed | Vendor |
|-----------|------|------|-------|--------|
| **MATEnet** | Sealed, compact | 2 | 100M-1G | TE Connectivity |
| **H-MTD** | High-speed, miniature | 2 | Up to 10G | Rosenberger |
| **Mini50** | Miniaturized | 2 | 100M-1G | Various |
| **HSD** | FAKRA variant | 4 | Up to 10G | Rosenberger |
| **SPE** | Single Pair Ethernet | 2 | 10M-1G | Harting/Wurth |

## Cable Specifications

| Standard | Cable | Impedance | Max Length |
|----------|-------|-----------|------------|
| 100BASE-T1 | UTP, 1 pair | 100Ω | 15m |
| 1000BASE-T1 | STP or UTP | 100Ω | 15m (UTP) / 40m (STP) |
| Multi-Gig | STP, 1 pair | 100Ω | 15m |

Key difference from IT Ethernet:
- **Automotive**: 1 pair, 15m max, automotive-grade connectors, -40 to +105°C
- **IT/Office**: 4 pairs (Cat5e/6), 100m, RJ45, 0-60°C

---

# SECTION 20: 10BASE-T1S — MULTIDROP ETHERNET

## The CAN/LIN Replacement

10BASE-T1S enables **Ethernet on a shared bus** (like CAN):

```
Traditional Ethernet: Star (switch-based)
  [Device]──[Switch]──[Device]
  [Device]──┘      └──[Device]
  
10BASE-T1S: Multidrop Bus (NO switch needed!)
  ─────┬──────────┬──────────┬──────────┬─────
       │          │          │          │
    [Node A]  [Node B]  [Node C]  [Node D]
    
Up to 8 nodes on one bus segment (25m)
PLCA (Physical Layer Collision Avoidance) manages access
```

## PLCA (Physical Layer Collision Avoidance)
- Deterministic access (like token ring but simpler)
- Each node has a transmit opportunity (slot)
- Node 0 = coordinator (sends beacon)
- Guaranteed maximum latency
- No collisions (unlike CSMA/CD)

## Why 10BASE-T1S for Automotive?
- **Replace CAN for non-critical sensors**: Same wiring topology (bus)
- **IP-capable**: Sensors can use TCP/IP directly
- **Lower cost**: No switch needed for small clusters
- **Higher bandwidth than CAN**: 10 Mbps vs 2 Mbps
- **Coexistence**: Mix of switched Ethernet + 10BASE-T1S buses

---

# SECTION 21: REAL-WORLD AUTOMOTIVE DEPLOYMENTS

## BMW (Pioneer)
- 2013: First production car with Ethernet (diagnostics only)
- 2015: Camera connectivity over 100BASE-T1
- 2020+: Full zonal architecture with Ethernet backbone

## Volkswagen / Audi
- MEB platform (ID.3, ID.4): Ethernet backbone
- 1000BASE-T1 for ADAS domain controller

## Hyundai / Kia
- E-GMP platform: Zonal architecture
- Multiple Ethernet domains (ADAS, body, IVI)

## GM
- Ultifi platform: Vehicle-wide Ethernet network
- 10GBASE-T1 backbone for autonomous driving

## Qualcomm (SoC Side)
- **SA8155P**: 1× RGMII (1G Ethernet MAC)
- **SA8295P**: 2× Ethernet MAC (1G + 2.5G capable)
- **SA9000P (Ride)**: Multiple 10G Ethernet for autonomous

---

# SECTION 22: NETWORK DESIGN PATTERNS

## Pattern 1: Camera Connection
```
Camera Module                    ADAS ECU
┌───────────┐   100BASE-T1    ┌──────────┐
│ ISP + MAC │──── 1 pair ─────│ MAC + SW │
│ + PHY     │   (15m max)     │          │
└───────────┘                  └──────────┘

Protocol: AVTP (IEEE 1722) or raw UDP
Bandwidth: 20-50 Mbps per camera (compressed H.264/HEVC)
           100-500 Mbps (raw/lightly compressed)
```

## Pattern 2: Zonal Gateway
```
         Central Switch (1G-10G backbone)
              │
              │ 1000BASE-T1
              │
         ┌────┴─────┐
         │Zone ECU  │
         │(Gateway) │
         └─┬──┬──┬──┘
           │  │  │
         CAN LIN 10BASE-T1S
          │  │     │
        [ECUs] [Actuators] [Sensors]
        
Zone ECU bridges: Ethernet ←→ CAN/LIN for legacy devices
```

## Pattern 3: ADAS Sensor Fusion
```
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Camera 1 │ │ Camera 2 │ │  Radar   │ │  LiDAR   │
│ 100M     │ │ 100M     │ │  100M    │ │   1G     │
└────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
     │             │            │             │
┌────┴─────────────┴────────────┴─────────────┴─────┐
│            ADAS ETHERNET SWITCH                    │
│            (TSN-capable, <10µs latency)           │
└────────────────────────┬───────────────────────────┘
                         │ 1G/10G
                    ┌────┴─────┐
                    │ ADAS SoC │
                    │(SA8295P /│
                    │ Orin)    │
                    └──────────┘
```

---

# SECTION 23: COMMON PITFALLS & GOTCHAS

1. **Not automotive-grade PHY** — Standard IT Ethernet PHYs (RTL8211) won't work: different coding (NRZ vs PAM-3), different pairs (4 vs 1), not qualified for automotive temp/EMC
2. **Cable quality** — Automotive Ethernet requires specific impedance (100Ω ±15%) twisted pair; random wire won't work
3. **Master/Slave misconfiguration** — Both PHYs set to same role → no link. One must be master, one slave
4. **Missing termination** — 100Ω between T+ and T- at each end
5. **EMC failures** — Automotive EMC (CISPR 25) is MUCH stricter than IT; need proper common-mode chokes
6. **VLAN misconfiguration** — Traffic in wrong VLAN → dropped silently; use port mirroring to debug
7. **TSN time sync failure** — gPTP requires hardware timestamping; software timestamping has too much jitter
8. **MTU mismatch** — One side sends jumbo frames (9000B), other expects standard (1500B) → drops
9. **Switch learning** — New device MAC not yet learned → traffic flooded; can saturate bandwidth momentarily
10. **IP address conflict** — Two ECUs with same IP → intermittent connectivity; use careful IP planning
11. **Firewall/iptables** — Linux firewall drops legitimate traffic; check netfilter rules
12. **Half-duplex confusion** — Automotive Ethernet is ALWAYS full-duplex; no CSMA/CD (except 10BASE-T1S with PLCA)
13. **Cable length exceeded** — >15m on 100BASE-T1 → signal attenuation → intermittent link drops
14. **Power supply noise** — Couples into PHY → increases bit error rate → frame drops

---

# SECTION 24: FUTURE TRENDS

## Near-Term (2024-2027)
- **10GBASE-T1 deployment** for autonomous vehicle backbones
- **10BASE-T1S mass adoption** replacing CAN for sensor networks
- **TSN standardization completion** across all automotive switch ICs
- **MACsec everywhere** (hardware-accelerated security on every link)
- **Zonal architecture** becoming standard (centralized compute + zone gateways)

## Medium-Term (2027-2030)
- **25G/50G automotive Ethernet** for L4/L5 autonomous data pipelines
- **Software-Defined Vehicle**: OTA-updateable network configuration
- **Ethernet as the ONLY vehicle bus** (CAN/LIN gone for new designs)
- **Vehicle-to-Cloud** native Ethernet (5G bridge transparent)

## Long-Term (2030+)
- **Terabit vehicle networks** for full autonomous + AR/VR passenger experience
- **Optical Ethernet** in vehicles (weight reduction, EMI immunity)
- **Quantum-safe encryption** on automotive Ethernet

---

END OF DOCUMENT 01 — MASTER THEORY
