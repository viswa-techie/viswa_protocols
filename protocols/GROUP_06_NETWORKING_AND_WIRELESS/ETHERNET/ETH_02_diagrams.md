# AUTOMOTIVE ETHERNET — DIAGRAMS & VISUAL REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 01: Automotive Network Evolution

```mermaid
graph LR
    A["2000s<br/>CAN + MOST + LIN"] --> B["2010s<br/>CAN + LVDS + Ethernet"]
    B --> C["2020s<br/>Ethernet Backbone<br/>+ CAN/LIN legacy"]
    C --> D["2030s<br/>ALL Ethernet<br/>(incl. 10BASE-T1S)"]
    
    style A fill:#fdd
    style B fill:#ffd
    style C fill:#dfd
    style D fill:#ddf
```

---

## DIAGRAM 02: Physical Layer Comparison

```
STANDARD ETHERNET (100BASE-TX):          AUTOMOTIVE ETHERNET (100BASE-T1):
  4 wires (2 pairs):                       2 wires (1 pair):
  
  Pair 1 (TX): ──→──→──→──→──            Single Pair: ←→←→←→←→
  Pair 2 (RX): ←──←──←──←──            (Full-duplex on SAME pair!)
                                          
  100m reach                              15m reach
  RJ45 connector                         MATEnet/H-MTD connector
  Cat5 cable (4 pairs)                   1 UTP pair (automotive grade)
  -                                       Echo cancellation separates TX/RX
  Office/Data center                      Vehicle only
```

---

## DIAGRAM 03: PAM-3 Signal Encoding

```
        100BASE-T1: PAM-3 Coding (3 voltage levels)
        
Voltage
 +1V ─── ┌─┐     ┌─┐           ┌─┐
          │ │     │ │           │ │
  0V ─── ─┘ └─┐ ─┘ └───┐   ┌──┘ └──
               │         │   │
 -1V ───       └─────────┘   └──────

Symbol:   +1  0  +1  0   -1  -1  0  +1  0
         
3 levels → log₂(3) = 1.585 bits/symbol
66.67 MBaud × 1.585 = 105.7 Mbps (raw)
→ 100 Mbps net after encoding overhead

vs NRZ (2 levels): would need 100 MBaud for 100 Mbps
   PAM-3 uses LESS bandwidth → better EMC!
```

---

## DIAGRAM 04: Echo Cancellation (Full-Duplex on 1 Pair)

```
┌─────────────────────────┐              ┌─────────────────────────┐
│         PHY A           │    1 pair    │         PHY B           │
│                         │ ════════════ │                         │
│  TX Data ──┐            │              │            ┌── TX Data  │
│            ├─ Hybrid ───┤──── Wire ────┤─── Hybrid ─┤           │
│  RX Data ──┘     │      │              │      │     └── RX Data  │
│              Echo │      │              │      │ Echo             │
│              Cancel      │              │      Cancel             │
│              │           │              │           │             │
│         Subtract own TX  │              │  Subtract own TX       │
│         from received    │              │  from received         │
│         = remote TX      │              │  = remote TX           │
└─────────────────────────┘              └─────────────────────────┘

On wire: SUM of both TX signals
PHY A receives: (PHY_A_TX + PHY_B_TX)
PHY A knows its own TX → subtracts it
Result: PHY_B_TX = PHY A's RX data ✓
```

---

## DIAGRAM 05: Ethernet Frame Structure

```
┌─────────┬─────┬────────────┬────────────┬───────────┬──────────────────────┬──────┐
│Preamble │ SFD │  Dest MAC  │  Src MAC   │ EtherType │       Payload        │  FCS │
│ 7 bytes │  1  │  6 bytes   │  6 bytes   │  2 bytes  │    46-1500 bytes     │  4   │
│ AA..AA  │ AB  │FF:FF:FF:.. │00:1A:2B:.. │  0x0800   │   (IP packet etc)   │ CRC  │
└─────────┴─────┴────────────┴────────────┴───────────┴──────────────────────┴──────┘
│←── 8 ──→│     │←────────────── 14 ──────────────────→│←──── 46-1500 ──────→│← 4 →│

Minimum frame: 64 bytes (excl. preamble)
Maximum frame: 1518 bytes (standard) / 1522 with VLAN tag
```

---

## DIAGRAM 06: VLAN Tagged Frame (802.1Q)

```
Standard Frame:
┌──────────┬──────────┬───────────┬─────────┬──────┐
│ Dst MAC  │ Src MAC  │ EtherType │ Payload │  FCS │
│ 6 bytes  │ 6 bytes  │  2 bytes  │         │  4   │
└──────────┴──────────┴───────────┴─────────┴──────┘

VLAN Tagged Frame (4 bytes inserted):
┌──────────┬──────────┬──────────────────┬───────────┬─────────┬──────┐
│ Dst MAC  │ Src MAC  │   802.1Q Tag     │ EtherType │ Payload │  FCS │
│ 6 bytes  │ 6 bytes  │   (4 bytes)      │  2 bytes  │         │  4   │
└──────────┴──────────┴────────┬─────────┴───────────┴─────────┴──────┘
                               │
                    ┌──────────┴──────────┐
                    │ TPID    │   TCI     │
                    │ 0x8100  │           │
                    │ (2 B)   │  (2 B)    │
                    └─────────┼───────────┘
                              │
                    ┌─────────┴──────────┐
                    │PCP │DEI│   VID     │
                    │3bit│1b │  12 bit   │
                    │0-7 │   │ 0-4095   │
                    └────┴───┴──────────┘
                    Priority   VLAN ID
```

---

## DIAGRAM 07: Vehicle Network Architecture (Zonal)

```mermaid
graph TD
    HPC["Central HPC<br/>(ADAS + IVI + Vehicle Computer)"]
    SW["Central Ethernet Switch<br/>10G Backbone"]
    
    HPC ---|10G| SW
    
    ZF["Zone Front<br/>Gateway"]
    ZR["Zone Rear<br/>Gateway"]
    ZL["Zone Left<br/>Gateway"]
    ZRR["Zone Right<br/>Gateway"]
    
    SW ---|1G| ZF
    SW ---|1G| ZR
    SW ---|1G| ZL
    SW ---|1G| ZRR
    
    ZF --- C1["Camera<br/>100M"]
    ZF --- R1["Radar<br/>100M"]
    ZF --- CAN1["CAN Bus<br/>(legacy ECUs)"]
    
    ZR --- C2["Rear Camera<br/>100M"]
    ZR --- P1["Parking<br/>Sensors"]
    
    ZL --- D1["Door Module<br/>10BASE-T1S"]
    ZL --- LIN1["LIN Bus<br/>(mirrors, seats)"]
    
    ZRR --- D2["Door Module<br/>10BASE-T1S"]
    
    style HPC fill:#f96
    style SW fill:#ff9
```

---

## DIAGRAM 08: SOME/IP Communication Pattern

```mermaid
sequenceDiagram
    participant SD as Service Discovery<br/>(Multicast)
    participant Server as ECU_A<br/>(Service Provider)
    participant Client as ECU_B<br/>(Service Consumer)
    
    Server->>SD: OFFER Service 0x1234
    Client->>SD: FIND Service 0x1234
    SD->>Client: OFFER (unicast response)
    
    Client->>Server: SUBSCRIBE EventGroup 0x01
    Server->>Client: SUBSCRIBE_ACK
    
    Note over Server,Client: Event-based communication
    Server->>Client: NOTIFICATION (Speed Changed)
    Server->>Client: NOTIFICATION (Speed Changed)
    
    Note over Server,Client: Request/Response
    Client->>Server: REQUEST GetDiagnosticData()
    Server->>Client: RESPONSE {data...}
```

---

## DIAGRAM 09: DoIP Diagnostic Session

```mermaid
sequenceDiagram
    participant T as Diagnostic Tester
    participant G as DoIP Gateway<br/>(TCP:13400)
    participant E as Target ECU
    
    T->>G: Vehicle Identification Request (UDP broadcast)
    G->>T: Vehicle Identification Response (VIN, IP)
    
    T->>G: TCP Connect (port 13400)
    T->>G: Routing Activation Request
    G->>T: Routing Activation Response (OK)
    
    T->>G: Diagnostic Message (UDS: Read DTC)
    G->>E: Forward UDS request
    E->>G: UDS Response (DTC list)
    G->>T: Diagnostic Message (UDS Response)
    
    T->>G: Diagnostic Message (UDS: Flash Request)
    G->>E: Forward Flash data
    Note over E: Flashing... (fast over Ethernet!)
    E->>G: UDS Positive Response
    G->>T: Diagnostic Message (Flash OK)
```

---

## DIAGRAM 10: TSN Time-Aware Shaper (802.1Qbv)

```
Gate Control List (GCL) — repeating schedule:

Time →   0µs    100µs   600µs   800µs   1000µs  (cycle repeats)
         │       │       │       │       │
Queue 7: ████████│       │       │       │████████  ← Critical control
(highest)│OPEN   │CLOSED │CLOSED │CLOSED │OPEN
         │       │       │       │       │
Queue 5: │       │████████████████│       │         ← Camera video
         │CLOSED │OPEN            │CLOSED │CLOSED
         │       │       │       │       │
Queue 0: │       │       │       │████████│         ← Best effort
(lowest) │CLOSED │CLOSED │CLOSED │OPEN   │CLOSED

Only frames in OPEN queues can transmit during that time slot.
→ Critical traffic ALWAYS gets its guaranteed slot!
→ Deterministic latency: max wait = 1 cycle time
```

---

## DIAGRAM 11: Frame Preemption (802.3br)

```
WITHOUT Frame Preemption:
─────────────────────────────────────────────────────────
│    Low Priority Frame (1518 bytes)        │ Critical │
│    ~123µs @ 100Mbps                       │ WAITS!   │
─────────────────────────────────────────────────────────
Time: 0                                    123µs  125µs
                                Critical must wait up to 123µs!

WITH Frame Preemption:
─────────────────────────────────────────────────────────
│ Low-Pri Part 1 │ Critical │ Low-Pri Part 2 (resumed) │
│   Fragment     │ EXPRESS  │   Fragment               │
─────────────────────────────────────────────────────────
Time: 0     30µs  32µs  34µs                       ~125µs
             ↑
      Preemption point: Low-Pri interrupted!
      Critical gets through in ~2µs!
      Low-Pri frame reassembled at receiver.
```

---

## DIAGRAM 12: TCP/IP Protocol Stack (Automotive)

```mermaid
graph TD
    APP["Applications<br/>SOME/IP | DoIP | HTTP | MQTT"]
    TLS["Security<br/>TLS 1.3 / DTLS"]
    TCP["Transport<br/>TCP | UDP"]
    IP["Network<br/>IPv4 / IPv6"]
    ETH["Data Link<br/>Ethernet + VLAN + TSN"]
    PHY["Physical<br/>100BASE-T1 / 1000BASE-T1"]
    
    APP --> TLS
    TLS --> TCP
    TCP --> IP
    IP --> ETH
    ETH --> PHY
    
    style APP fill:#e8f4e8
    style TLS fill:#f4e8f4
    style TCP fill:#e8e8f4
    style IP fill:#f4f4e8
    style ETH fill:#f4e8e8
    style PHY fill:#e8f4f4
```

---

## DIAGRAM 13: Ethernet Switch Forwarding

```
Frame arrives Port 0:  Dst=AA:BB:CC:DD:EE:FF

┌─────────────────────────────────────────────────────┐
│                 ETHERNET SWITCH                      │
│                                                     │
│  Port 0 ──→ [Ingress] ──→ [MAC Lookup] ──→ ?      │
│                              │                      │
│                     ┌────────┴─────────┐           │
│                     │ MAC Address Table │           │
│                     │                  │           │
│                     │ AA:BB:CC:..→Port3│ ← Found!  │
│                     │ 11:22:33:..→Port1│           │
│                     │ DE:AD:BE:..→Port5│           │
│                     └──────────────────┘           │
│                              │                      │
│                              ↓                      │
│  Port 3 ←── [Egress + QoS] ←┘  Frame out Port 3  │
│                                                     │
│  If MAC NOT found → FLOOD to all ports (learning)  │
└─────────────────────────────────────────────────────┘
```

---

## DIAGRAM 14: AUTOSAR Ethernet Stack

```mermaid
graph TD
    SWC["Application SWC"]
    RTE["RTE"]
    COM["COM<br/>(Signal routing)"]
    SD["SD<br/>(SOME/IP-SD)"]
    SOAD["SoAd<br/>(Socket Adaptor)"]
    DOIP["DoIP<br/>(Diagnostics)"]
    TCPIP["TcpIp<br/>(TCP/UDP/IP)"]
    ETHIF["EthIf<br/>(Eth Interface)"]
    ETHSWT["EthSwt<br/>(Switch Driver)"]
    ETH["Eth<br/>(MAC Driver)"]
    ETHTRCV["EthTrcv<br/>(PHY Driver)"]
    HW["Hardware<br/>(MAC + PHY + Switch)"]
    
    SWC --> RTE
    RTE --> COM
    RTE --> SD
    COM --> SOAD
    SD --> SOAD
    DOIP --> SOAD
    SOAD --> TCPIP
    TCPIP --> ETHIF
    ETHIF --> ETHSWT
    ETHIF --> ETH
    ETH --> ETHTRCV
    ETHTRCV --> HW
    ETHSWT --> HW
    
    style TCPIP fill:#ff9,stroke:#333,stroke-width:2px
    style ETHIF fill:#9ff,stroke:#333,stroke-width:2px
```

---

## DIAGRAM 15: 10BASE-T1S Multidrop Topology

```
Traditional Ethernet (Star — needs switch):
         ┌────────┐
    [A]──┤        ├──[C]
    [B]──┤ Switch ├──[D]
         └────────┘
         Cost: Switch + N cables

10BASE-T1S (Bus — NO switch!):
    ─────┬────────┬────────┬────────┬─────
         │        │        │        │
      [Node A] [Node B] [Node C] [Node D]
      (coord.)
      
    • Up to 8 nodes per segment
    • 25m bus length
    • PLCA: deterministic access (no collisions)
    • Cost: Just cable + termination!
    • Target: Replace CAN sensor buses
```

---

## DIAGRAM 16: gPTP Time Synchronization (802.1AS)

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│Grand Master  │       │  Bridge      │       │  End Station │
│   (GM)       │       │  (Switch)    │       │  (ECU)       │
└──────┬───────┘       └──────┬───────┘       └──────┬───────┘
       │                      │                      │
       │──── Sync ──────────→│                      │
       │  (t1 = departure)   │                      │
       │                      │──── Sync ──────────→│
       │──── Follow_Up ─────→│  (t2=arrival,        │
       │  (t1 timestamp)     │   t3=departure)      │
       │                      │──── Follow_Up ─────→│
       │                      │  (t3 timestamp,     │
       │                      │   correction)        │
       │                      │                      │
       │                      │←── Pdelay_Req ──────│
       │                      │──── Pdelay_Resp ───→│
       │                      │                      │
       │                      │    (measures link    │
       │                      │     delay = ~100ns)  │
       
Result: All nodes synchronized to <1µs accuracy!
Used for: Camera frame sync, sensor fusion, TSN scheduling
```

---

## DIAGRAM 17: MACsec Encryption (802.1AE)

```
UNENCRYPTED (original frame):
┌──────────┬──────────┬───────────┬──────────────────┬──────┐
│ Dst MAC  │ Src MAC  │ EtherType │     Payload      │  FCS │
└──────────┴──────────┴───────────┴──────────────────┴──────┘
                        ↓ MACsec processing ↓

ENCRYPTED (MACsec frame):
┌──────────┬──────────┬─────────┬────────────────────────────┬───────┬──────┐
│ Dst MAC  │ Src MAC  │SecTAG   │  ENCRYPTED Payload         │  ICV  │      │
│          │          │(8-16B)  │  (AES-GCM-128/256)        │(8-16B)│      │
└──────────┴──────────┴─────────┴────────────────────────────┴───────┴──────┘
                        │                                      │
                  Security Tag:                         Integrity Check:
                  - SecY identifier                     - AES-GCM tag
                  - Packet Number                      - Authenticates header+data
                  - (prevents replay)                  - Detects tampering
                  
Wire-speed encryption: PHY/Switch does it in hardware!
```

---

## DIAGRAM 18: Camera over Ethernet (AVTP - IEEE 1722)

```
Camera Module                              ADAS ECU
┌─────────────────┐   100BASE-T1   ┌─────────────────────┐
│                 │                 │                     │
│  Image Sensor   │                │  Ethernet MAC       │
│       ↓        │                 │       ↓            │
│  ISP (compress)│                 │  AVTP Depacketize  │
│       ↓        │  Single Pair   │       ↓            │
│  AVTP Packetize│ ════════════►  │  Decompress        │
│       ↓        │    (15m)       │       ↓            │
│  Ethernet MAC  │                 │  Frame Buffer      │
│       ↓        │                 │       ↓            │
│  100BASE-T1 PHY│                 │  AI/CV Processing  │
└─────────────────┘                └─────────────────────┘

AVTP Header includes:
  • Stream ID (identifies which camera)
  • Sequence number (detect drops)
  • Presentation timestamp (from gPTP)
  • Media-specific header (H.264/MJPEG/RAW)
```

---

## DIAGRAM 19: Debug Decision Tree

```mermaid
flowchart TD
    A["Ethernet Not Working?"] --> B{"Link LED on?<br/>(ethtool shows link?)"}
    B -->|No link| C{"PHY registers readable?<br/>(mdio read)"}
    C -->|No| D["Check: PHY power, MDIO wiring,<br/>reset pin, clock input"]
    C -->|Yes| E{"Master/Slave config correct?"}
    E -->|Same role| F["Fix: One master, one slave"]
    E -->|Correct| G["Check: cable, connectors,<br/>termination, cable length"]
    B -->|Link UP| H{"Ping works?"}
    H -->|No| I{"ARP resolves?<br/>(arping, arp -a)"}
    I -->|No| J["Check: IP config, same subnet,<br/>VLAN match, switch port config"]
    I -->|Yes| K["Check: iptables/firewall,<br/>routing table, MTU"]
    H -->|Yes| L{"Application works?"}
    L -->|No| M["Check: port numbers, SOME/IP config,<br/>service discovery, socket binding"]
    L -->|Yes| N["✅ Working!"]
    
    style N fill:#9f9
    style A fill:#f99
```

---

## DIAGRAM 20: Automotive Ethernet Speed Tiers

```
Speed Comparison (logarithmic scale):

10G ─────── ████████████████████████████████████████████  10GBASE-T1 (backbone)
5G  ─────── ████████████████████████                      5GBASE-T1 (multi-cam)
2.5G ────── ████████████████                              2.5GBASE-T1 (aggregation)
1G  ─────── ████████████                                  1000BASE-T1 (ADAS, IVI)
100M ────── ████                                          100BASE-T1 (cameras, sensors)
10M ─────── █                                             10BASE-T1S (replace CAN)
            │
    CAN FD: ▌ (2-8 Mbps)
    MOST:   ███ (150 Mbps)
    LVDS:   ████████ (800 Mbps, P2P only)

ALL automotive Ethernet variants:
  ✓ Single unshielded/shielded twisted pair
  ✓ Full-duplex
  ✓ Automotive temp range (-40°C to +105°C)
  ✓ IP-capable
  ✓ Same protocol stack (just different PHY)
```

---

END OF DOCUMENT 02 — DIAGRAMS
