# Chapter 12 — Domain: Networking & Telecommunications Standards

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 12.1 Reference Models — OSI vs. TCP/IP

### OSI 7-Layer Model vs. TCP/IP 4-Layer Model

| Layer # | OSI Model (ISO/IEC 7498) | PDU          | TCP/IP Model     | Key Protocols                          |
|---------|--------------------------|--------------|------------------|----------------------------------------|
| 7       | Application              | Data         | Application      | HTTP/2/3, DNS, FTP, SMTP, SNMP, gRPC  |
| 6       | Presentation             | Data         | Application      | TLS/SSL, MIME, ASN.1, JPEG, gzip       |
| 5       | Session                  | Data         | Application      | RPC, NetBIOS, SOCKS, PPTP             |
| 4       | Transport                | Segment/Dgram| Transport       | TCP, UDP, QUIC, SCTP, DCCP            |
| 3       | Network                  | Packet       | Internet         | IPv4, IPv6, ICMP, ICMPv6, BGP, OSPF  |
| 2       | Data Link                | Frame        | Network Access   | Ethernet (802.3), Wi-Fi (802.11), PPP |
| 1       | Physical                 | Bit/Symbol   | Network Access   | 100BASE-T, OFDM, QAM, fiber optics   |

### Disambiguation Notes

| Confusion Point                | Clarification                                              |
|--------------------------------|------------------------------------------------------------|
| OSI is NOT implemented         | OSI is a reference model; real networks use TCP/IP stack   |
| "Layer 7 firewall"             | Uses OSI terminology — means application-aware inspection  |
| Where does TLS fit?            | Between L4 (transport) and L7 (application) — doesn't map cleanly to OSI |
| QUIC breaks the model          | QUIC integrates transport + crypto — is it L4 or L4+L6?   |
| "Layer 2.5"                    | Informal term for MPLS (between Ethernet and IP)           |

---

## 12.2 Ethernet — IEEE 802.3

### IEEE 802.3 Generations

| Generation          | Standard        | Speed       | Medium               | Max Distance |
|---------------------|-----------------|-------------|----------------------|--------------|
| 10BASE-T            | IEEE 802.3i     | 10 Mbps     | Cat3 UTP             | 100 m        |
| 100BASE-TX (Fast)   | IEEE 802.3u     | 100 Mbps    | Cat5 UTP             | 100 m        |
| 1000BASE-T (GbE)    | IEEE 802.3ab    | 1 Gbps      | Cat5e UTP            | 100 m        |
| 1000BASE-SX         | IEEE 802.3z     | 1 Gbps      | OM2 MMF              | 550 m        |
| 10GBASE-T           | IEEE 802.3an    | 10 Gbps     | Cat6A UTP            | 100 m        |
| 10GBASE-SR          | IEEE 802.3ae    | 10 Gbps     | OM3/OM4 MMF          | 300/400 m    |
| 25GBASE-T           | IEEE 802.3bq    | 25 Gbps     | Cat8 UTP             | 30 m         |
| 40GBASE-SR4         | IEEE 802.3ba    | 40 Gbps     | OM3 MMF (4 lanes)    | 100 m        |
| 100GBASE-SR4        | IEEE 802.3bm    | 100 Gbps    | OM4 MMF (4 lanes)    | 100 m        |
| 200GBASE-DR4        | IEEE 802.3bs    | 200 Gbps    | SMF (4 lanes)        | 500 m        |
| 400GBASE-SR16       | IEEE 802.3bs    | 400 Gbps    | OM4 MMF (16 lanes)   | 100 m        |
| 800GbE (draft)      | IEEE 802.3df    | 800 Gbps    | SMF                  | Various      |
| 100BASE-T1 (Auto)   | IEEE 802.3bw    | 100 Mbps    | Single UTP pair      | 15 m         |
| 1000BASE-T1 (Auto)  | IEEE 802.3bp    | 1 Gbps      | Single STP pair      | 15 m         |

### Ethernet Frame Format (IEEE 802.3)

```
┌──────────┬──────────┬──────┬──────────────────┬─────┬─────────┐
│ Preamble │ Dest MAC │ Src  │ EtherType/Length │ Data│ FCS     │
│ 8 bytes  │ 6 bytes  │MAC 6B│ 2 bytes          │46-1500│ 4 bytes │
└──────────┴──────────┴──────┴──────────────────┴─────┴─────────┘
```

---

## 12.3 Wi-Fi — IEEE 802.11

### Wi-Fi Generations

| Generation | Standard    | Max Speed    | Frequency     | Key Feature                          | Year  |
|------------|-------------|--------------|---------------|--------------------------------------|-------|
| —          | 802.11      | 2 Mbps       | 2.4 GHz       | Original (FHSS/DSSS)               | 1997  |
| Wi-Fi 1    | 802.11b     | 11 Mbps      | 2.4 GHz       | DSSS; first mass adoption           | 1999  |
| Wi-Fi 2    | 802.11a     | 54 Mbps      | 5 GHz         | OFDM; shorter range                 | 1999  |
| Wi-Fi 3    | 802.11g     | 54 Mbps      | 2.4 GHz       | OFDM at 2.4 GHz; backward compat   | 2003  |
| Wi-Fi 4    | 802.11n     | 600 Mbps     | 2.4/5 GHz     | MIMO (4×4), 40 MHz channels         | 2009  |
| Wi-Fi 5    | 802.11ac    | 3.5 Gbps     | 5 GHz only    | MU-MIMO (DL), 80/160 MHz, 256-QAM  | 2013  |
| Wi-Fi 6    | 802.11ax    | 9.6 Gbps     | 2.4/5 GHz     | OFDMA, UL MU-MIMO, BSS Coloring, TWT | 2020 |
| Wi-Fi 6E   | 802.11ax    | 9.6 Gbps     | 6 GHz         | New 6 GHz band (1200 MHz spectrum)  | 2021  |
| Wi-Fi 7    | 802.11be    | 46 Gbps      | 2.4/5/6 GHz   | MLO, 4096-QAM, 320 MHz channels    | 2024  |

### Wi-Fi Security Evolution

| Protocol    | Standard      | Encryption    | Key Mgmt        | Status          |
|-------------|---------------|---------------|-----------------|-----------------|
| WEP         | IEEE 802.11   | RC4 (64/128)  | Static keys     | **BROKEN** — do not use |
| WPA         | Wi-Fi Alliance| TKIP (RC4)    | PSK or 802.1X  | Deprecated      |
| WPA2        | IEEE 802.11i  | AES-CCMP      | PSK or 802.1X  | Current (minimum)|
| WPA3        | Wi-Fi Alliance| AES-GCMP-256  | SAE (Dragonfly) | Recommended     |
| OWE         | RFC 8110      | AES-CCMP      | Diffie-Hellman  | Open networks   |

---

## 12.4 3GPP Cellular Standards

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | 3rd Generation Partnership Project                             |
| Abbreviation                 | 3GPP                                                           |
| Entry Type                   | International Standards Development Organization (partnership) |
| Governance Tier              | L3 (consortium of 7 regional SDOs)                            |
| Organizational Partners      | ETSI (EU), ARIB+TTC (Japan), TSDSI (India), ATIS (N. America), CCSA (China), TTA (Korea) |
| Founded                      | December 1998                                                 |
| Output                       | Technical Specifications (TS) and Technical Reports (TR)      |
| Specification Access         | **Free** — portal.3gpp.org                                    |
| Working Groups               | RAN (Radio Access Network), CT (Core & Terminals), SA (System Architecture & Services) |
| Release Mechanism            | Numbered releases with frozen feature sets                    |

### 3GPP Release Timeline

| Release | Year  | Technology     | Key Features                                          |
|---------|-------|----------------|-------------------------------------------------------|
| Rel. 99 | 1999  | 3G UMTS/WCDMA  | 384 kbps, circuit-switched core                      |
| Rel. 5  | 2002  | HSDPA          | 14.4 Mbps DL, IMS                                   |
| Rel. 7  | 2007  | HSPA+          | MIMO, 64-QAM, 42 Mbps DL                            |
| Rel. 8  | 2008  | 4G LTE         | OFDMA DL, SC-FDMA UL, all-IP (EPC), 100 Mbps DL     |
| Rel. 10 | 2011  | LTE-Advanced   | Carrier aggregation, eICIC, 1 Gbps DL               |
| Rel. 13 | 2016  | LTE IoT        | Cat-M1 (eMTC), NB-IoT, LAA (unlicensed)             |
| Rel. 14 | 2017  | LTE-V2X        | Sidelink (PC5) for vehicle communication             |
| Rel. 15 | 2018  | **5G NR Phase 1** | NSA/SA modes, eMBB, uRLLC, mMTC, network slicing |
| Rel. 16 | 2020  | 5G Phase 2     | URLLC enhancements, NR-V2X, IIoT, positioning       |
| Rel. 17 | 2022  | 5G Phase 3     | NTN (satellite), RedCap, multicast/broadcast         |
| Rel. 18 | 2024  | **5G-Advanced** | AI/ML RAN, XR, network energy saving, ambient IoT  |
| Rel. 19 | 2025+ | Pre-6G         | Advanced positioning, AI-native air interface research |

### 5G Three Usage Scenarios

| Scenario | Full Name                        | Target KPI                          | Use Cases                     |
|----------|----------------------------------|-------------------------------------|-------------------------------|
| eMBB     | Enhanced Mobile Broadband        | 20 Gbps peak DL, 100 Mbps ubiquitous | Video streaming, XR, high-speed data |
| uRLLC    | Ultra-Reliable Low-Latency Comms | <1 ms latency, 99.999% reliability | Industrial automation, remote surgery, V2X |
| mMTC     | Massive Machine-Type Comms       | 1M devices/km², 10-year battery    | Smart city sensors, agriculture IoT |

### 5G Core (5GC) Architecture — Service-Based

| Function | Name                                    | Purpose                              |
|----------|-----------------------------------------|--------------------------------------|
| AMF      | Access and Mobility Management Function | Registration, connection, mobility   |
| SMF      | Session Management Function             | PDU session establishment/management |
| UPF      | User Plane Function                     | Packet routing/forwarding (data plane)|
| PCF      | Policy Control Function                 | QoS policies, charging rules         |
| UDM      | Unified Data Management                 | Subscriber profiles, authentication  |
| AUSF     | Authentication Server Function          | 5G-AKA, EAP-AKA' authentication     |
| NRF      | Network Repository Function             | Service registration/discovery       |
| NSSF     | Network Slice Selection Function        | Slice assignment for UE sessions     |
| NEF      | Network Exposure Function               | API exposure to external applications|
| NWDAF    | Network Data Analytics Function         | AI/ML-based network analytics        |

### 5G Network Slicing

```
Physical Infrastructure (shared gNB + 5GC)
    ├── Slice 1 (eMBB): High bandwidth, normal latency
    │     → Video streaming, enterprise office
    ├── Slice 2 (uRLLC): Low latency, high reliability
    │     → Factory automation, autonomous driving
    └── Slice 3 (mMTC): Low power, massive connections
          → IoT sensors, smart meters
          
Each slice: independent QoS, isolation, SLA
```

---

## 12.5 Bluetooth — Bluetooth SIG

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Bluetooth (Core Specification)                                |
| Governance                   | Bluetooth Special Interest Group (SIG)                        |
| Governance Tier              | L3 (industry consortium)                                      |
| Current Version              | Bluetooth 5.4 (2023)                                          |
| Frequency                    | 2.4 GHz ISM band (2400–2483.5 MHz)                          |
| Key Variants                 | Classic (BR/EDR: 1–3 Mbps), Low Energy (BLE: 125 kbps–2 Mbps)|
| Profiles                     | A2DP (audio), HFP (handsfree), GATT (BLE services), Mesh    |
| Bluetooth LE Audio           | LC3 codec, Auracast broadcast, multi-stream (Bluetooth 5.2+) |
| Competing                    | Wi-Fi Direct, UWB (ranging), Thread/Matter (IoT)             |

---

## 12.6 Transport Layer Protocols

### TCP vs. UDP vs. QUIC vs. SCTP

| Feature           | TCP (RFC 9293)      | UDP (RFC 768)       | QUIC (RFC 9000)     | SCTP (RFC 9260)     |
|-------------------|---------------------|---------------------|---------------------|---------------------|
| Connection        | Connection-oriented | Connectionless      | Connection-oriented | Connection-oriented |
| Reliability       | Guaranteed delivery | Best effort         | Guaranteed delivery | Guaranteed delivery |
| Ordering          | In-order            | No ordering         | Per-stream ordering | Per-stream ordering |
| Multiplexing      | Single stream       | No (app-level)      | **Multi-stream** (no HOL blocking) | Multi-stream |
| Congestion ctrl   | Yes (various: Reno, CUBIC, BBR) | No   | Yes (pluggable)     | Yes                 |
| Encryption        | Optional (TLS on top)| Optional           | **Built-in** (TLS 1.3 integrated) | Optional (DTLS) |
| Handshake         | 3-way (SYN-ACK)    | None                | **0-RTT or 1-RTT** | 4-way               |
| Head-of-line blocking | Yes             | No                  | **No** (per-stream) | No (per-stream)    |
| NAT traversal     | Difficult           | Easier              | **Built-in** (connection migration) | Difficult |
| Used by HTTP/3    | No                  | (transport for QUIC)| **Yes** — HTTP/3 runs over QUIC | No |

### QUIC (RFC 9000)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | QUIC (originally "Quick UDP Internet Connections" — now just "QUIC") |
| Standard                     | IETF RFC 9000 (QUIC transport), RFC 9001 (QUIC + TLS 1.3)   |
| Governance Tier              | L3 (IETF — Internet Standard track)                          |
| Transport                    | Runs over UDP (port 443 typically)                            |
| Key Innovation               | Integrates transport + crypto (TLS 1.3) + multiplexing in one protocol |
| 0-RTT                        | Can send data in first packet (previously established connection) |
| Connection Migration         | Connections survive IP address changes (mobile handover)      |
| Used By                      | HTTP/3 (RFC 9114), Google (YouTube, Search), Cloudflare, Meta |
| Origin                       | Google (2012 experiment); standardized IETF (2021)            |

---

## 12.7 Routing Protocols

| Protocol | Full Name                        | Type          | Scope        | RFC/Standard    | Algorithm       |
|----------|----------------------------------|---------------|--------------|-----------------|-----------------|
| BGP-4    | Border Gateway Protocol v4       | Path-vector   | Inter-AS (Internet backbone) | RFC 4271 | Policy-based path selection |
| OSPF v3  | Open Shortest Path First v3      | Link-state    | Intra-AS (enterprise) | RFC 5340 | Dijkstra SPF   |
| IS-IS    | Intermediate System to IS        | Link-state    | Intra-AS (SP/DC) | ISO 10589, RFC 5308 | Dijkstra SPF |
| EIGRP    | Enhanced Interior Gateway RP     | Hybrid (Cisco)| Intra-AS (Cisco) | RFC 7868 (informational) | DUAL algorithm |
| RIP v2   | Routing Information Protocol v2  | Distance-vector| Small nets  | RFC 2453        | Bellman-Ford    |
| SR-MPLS  | Segment Routing MPLS             | Source-routing| WAN/DC       | RFC 8402        | Segment lists   |
| SRv6     | Segment Routing over IPv6        | Source-routing| WAN/DC       | RFC 8986        | SRH (Segment Routing Header) |

---

## 12.8 Network Management Protocols

| Protocol   | Standard         | Transport           | Purpose                          | Model         |
|------------|------------------|---------------------|----------------------------------|---------------|
| SNMP v3    | RFC 3411–3418    | UDP (161/162)       | Device monitoring/config (legacy)| MIB (SMI)     |
| NETCONF    | RFC 6241         | SSH (830)           | Network configuration            | YANG models   |
| RESTCONF   | RFC 8040         | HTTPS               | REST-like network config         | YANG models   |
| gNMI       | OpenConfig       | gRPC                | Telemetry streaming + config     | YANG/OpenConfig|
| Syslog     | RFC 5424         | UDP (514)/TCP       | Log message transport            | Structured data|
| IPFIX      | RFC 7011         | TCP/UDP/SCTP        | Flow export (NetFlow successor)  | IE templates  |

### NETCONF vs. RESTCONF vs. gNMI

| Aspect        | NETCONF                    | RESTCONF                   | gNMI                        |
|---------------|----------------------------|----------------------------|-----------------------------|
| Transport     | SSH                        | HTTPS                      | gRPC (HTTP/2)               |
| Data encoding | XML                        | JSON or XML                | Protobuf (binary)           |
| Operations    | get, get-config, edit-config, lock, commit | GET, PUT, POST, DELETE, PATCH | Get, Set, Subscribe |
| Streaming     | Notifications (RFC 5277)   | SSE (RFC 8040)             | **Native streaming telemetry** |
| Governance    | IETF (L3)                  | IETF (L3)                  | OpenConfig (L6 — Google-led)|
| Best for      | Full configuration mgmt    | Lightweight/web integration| Real-time telemetry         |

---

## 12.9 Time-Sensitive Networking (TSN)

### IEEE 802.1 TSN Standards Suite

| Standard     | Title                                        | Function                              |
|--------------|----------------------------------------------|---------------------------------------|
| IEEE 802.1AS-Rev | Timing and Synchronization (gPTP)        | Sub-microsecond time synchronization  |
| IEEE 802.1Qbv | Enhancements for Scheduled Traffic (TAS)   | Time-Aware Shaper — deterministic time slots |
| IEEE 802.1Qbu | Frame Preemption                           | Interrupt low-priority for express traffic |
| IEEE 802.1CB  | Frame Replication and Elimination (FRER)    | Seamless redundancy for fault tolerance |
| IEEE 802.1Qcc | Stream Reservation Protocol enhancements   | Centralized/distributed/hybrid config |
| IEEE 802.1Qci | Per-Stream Filtering and Policing          | Ingress traffic control and protection|
| IEEE 802.1Qcr | Asynchronous Traffic Shaping (ATS)         | Urgency-based scheduling              |
| IEEE 802.1CS  | Link-local Registration Protocol            | Topology and resource discovery       |
| IEEE 802.1Qch | Cyclic Queuing and Forwarding (CQF)        | Bounded latency per hop               |

### TSN Use Cases by Industry

| Industry      | Use Case                                | Key TSN Standards Used              |
|---------------|------------------------------------------|------------------------------------|
| Automotive    | Sensor fusion (camera+radar+LiDAR)       | 802.1AS, 802.1Qbv, 802.1CB        |
| Industrial    | Motion control (servo sync <1μs jitter)  | 802.1AS, 802.1Qbv, 802.1Qci       |
| Avionics      | AFDX/ARINC 664 evolution                 | 802.1CB, 802.1AS                   |
| Professional AV| SMPTE ST 2110 media-over-IP             | 802.1AS (gPTP)                     |
| 5G fronthaul  | O-RAN fronthaul transport                | 802.1CM, 802.1Qbv                  |

---

## 12.10 Industrial Networking Protocols

| Protocol     | Standard           | Governance | Speed            | Topology    | Use Case                   |
|--------------|--------------------|------------|------------------|-------------|----------------------------|
| PROFINET IRT | IEC 61158/61784    | PI (Siemens-led) | 100M/1G   | Star/Line   | Real-time industrial Ethernet|
| EtherCAT     | IEC 61158 Type 12  | ETG (Beckhoff) | 100 Mbps   | Daisy-chain | Ultra-fast motion control  |
| EtherNet/IP  | IEC 61158 (CIP)    | ODVA         | 100M/1G        | Star        | Process/discrete automation|
| Modbus TCP   | Modbus.org         | L8 (de-facto)| 100 Mbps Eth   | Star        | Legacy SCADA/PLC           |
| Modbus RTU   | Modbus.org         | L8 (de-facto)| 9.6–115.2 kbps | Bus (RS-485)| Legacy field instruments   |
| PROFIBUS DP  | IEC 61158 Type 3   | PI           | 12 Mbps        | Bus (RS-485)| Legacy field bus           |
| HART         | FieldComm Group    | L3           | 1.2 kbps       | 4-20mA loop | Analog + digital instruments|
| CC-Link IE   | CLPA               | L3           | 1G/10G         | Ring/Star   | Japanese industrial        |
| POWERLINK    | EPSG               | L5 (open)    | 100 Mbps       | Hub/Line    | Open industrial Ethernet   |

---

## 12.11 Wireless IoT Protocols

| Protocol   | Governance      | Tier | Frequency    | Range       | Data Rate    | Topology  | Use Cases              |
|------------|-----------------|------|--------------|-------------|--------------|-----------|------------------------|
| ZigBee 3.0 | CSA             | L3   | 2.4 GHz      | 10–100 m    | 250 kbps     | Mesh      | Smart home, lighting   |
| Z-Wave LR  | Z-Wave Alliance | L3   | 868/908 MHz  | 30–100 m (LR: 1.6 km) | 100 kbps | Mesh | Home automation       |
| Thread      | Thread Group    | L3   | 2.4 GHz      | 30–100 m    | 250 kbps     | Mesh (6LoWPAN) | Matter backbone   |
| Matter      | CSA             | L3   | IP-based     | Network     | Varies       | IP mesh   | Smart home interop     |
| LoRaWAN     | LoRa Alliance  | L3   | 868/915 MHz  | 2–15 km     | 0.3–50 kbps  | Star      | LPWAN — meters, agri   |
| NB-IoT      | 3GPP Rel. 13   | L3   | Licensed LTE | 1–10 km     | 250 kbps     | Star (cellular) | Smart meters, city |
| LTE-M (Cat-M1) | 3GPP Rel. 13 | L3 | Licensed LTE | 1–10 km    | 1 Mbps       | Star (cellular) | Mobile IoT, trackers |
| Sigfox      | Sigfox S.A.    | L7   | 868/902 MHz  | 3–50 km     | 100 bps      | Star      | Ultra-low-power (legacy)|
| BLE 5.4     | Bluetooth SIG  | L3   | 2.4 GHz      | 10–400 m    | 2 Mbps       | Star/Mesh | Wearables, beacons     |
| UWB         | IEEE 802.15.4z | L1   | 3.1–10.6 GHz | 10–50 m     | 27.2 Mbps    | P2P       | Precise ranging, keys  |
| Wi-SUN      | Wi-SUN Alliance| L3   | Sub-GHz      | 1–5 km      | 300 kbps     | Mesh      | Smart grid, city       |

### Matter (CSA) — Smart Home Unification

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Matter (formerly Project CHIP — Connected Home over IP)        |
| Governance                   | Connectivity Standards Alliance (CSA, formerly Zigbee Alliance)|
| Governance Tier              | L3 (industry consortium)                                      |
| Transport                    | Thread (mesh), Wi-Fi (high bandwidth), Ethernet, BLE (commissioning) |
| IP Stack                     | IPv6 over all transports (6LoWPAN for Thread)                 |
| Key Innovation               | One standard for all smart home devices (replaces fragmentation) |
| Supporters                   | Apple, Google, Amazon, Samsung, IKEA (all major ecosystems)   |
| Competing                    | Proprietary ecosystems (HomeKit-only, Alexa-only)             |

---

## 12.12 SDN and NFV

### Software-Defined Networking (SDN)

| Technology      | Governance        | Tier | Description                                |
|-----------------|-------------------|------|--------------------------------------------|
| OpenFlow        | ONF               | L3   | First SDN southbound protocol (switch → controller) |
| P4              | P4.org (ONF)      | L5   | Data plane programming language            |
| OpenDaylight    | Linux Foundation  | L5   | Open-source SDN controller (Java)          |
| ONOS            | ONF               | L3   | SDN controller for service providers       |
| OVS             | Linux Foundation  | L5   | Open vSwitch — virtual switch for hypervisors |

### Network Functions Virtualization (NFV)

| Component        | ETSI NFV Standard  | Description                              |
|------------------|--------------------|------------------------------------------|
| NFVI             | ETSI GS NFV-INF   | NFV Infrastructure (compute, storage, network) |
| VNF              | ETSI GS NFV-SWA   | Virtual Network Function (software appliance) |
| MANO             | ETSI GS NFV-MAN   | Management and Orchestration (VNFM, NFVO, VIM) |
| MEC              | ETSI GS MEC       | Multi-Access Edge Computing (compute at network edge) |

---

## 12.13 DNS, DHCP, NTP/PTP — Infrastructure Protocols

| Protocol | Standard       | Purpose                              | Port      | Notes                    |
|----------|----------------|--------------------------------------|-----------|--------------------------|
| DNS      | RFC 1034/1035  | Domain name → IP resolution          | UDP/TCP 53| + DoH (443), DoT (853)  |
| DHCP v4  | RFC 2131       | Dynamic IP address assignment        | UDP 67/68 | Ubiquitous LAN protocol  |
| DHCPv6   | RFC 8415       | IPv6 address configuration           | UDP 546/547|                         |
| NTP v4   | RFC 5905       | Network time synchronization (~ms)   | UDP 123   | Internet time sync       |
| PTP/gPTP | IEEE 1588/802.1AS | Precision time protocol (sub-μs)  | UDP 319/320| TSN, telecom, industrial |
| SNTP     | RFC 4330       | Simple NTP (reduced accuracy client) | UDP 123   | Embedded devices         |

---

## 12.14 Networking Standards Master Crosswalk Table

| Protocol/Standard    | OSI Layer(s) | Governing Body | Tier | Key RFC/Standard     | Status  |
|----------------------|--------------|----------------|------|----------------------|---------|
| Ethernet (802.3)     | L1/L2        | IEEE 802.3     | L4   | IEEE 802.3           | Current |
| Wi-Fi (802.11)       | L1/L2        | IEEE 802.11    | L4   | IEEE 802.11be (Wi-Fi 7)| Current|
| Bluetooth            | L1/L2        | Bluetooth SIG  | L3   | Core Spec 5.4        | Current |
| IPv4                 | L3           | IETF           | L3   | RFC 791              | Active  |
| IPv6                 | L3           | IETF           | L3   | RFC 8200             | Current |
| TCP                  | L4           | IETF           | L3   | RFC 9293             | Current |
| UDP                  | L4           | IETF           | L3   | RFC 768              | Active  |
| QUIC                 | L4           | IETF           | L3   | RFC 9000             | Current |
| TLS 1.3              | L5/L6        | IETF           | L3   | RFC 8446             | Current |
| HTTP/3               | L7           | IETF           | L3   | RFC 9114             | Current |
| DNS                  | L7           | IETF           | L3   | RFC 1034/1035        | Active  |
| BGP-4                | L3 (routing) | IETF           | L3   | RFC 4271             | Active  |
| OSPF v3              | L3 (routing) | IETF           | L3   | RFC 5340             | Active  |
| MPLS                 | L2.5         | IETF           | L3   | RFC 3031             | Active  |
| NETCONF              | L7 (mgmt)    | IETF           | L3   | RFC 6241             | Active  |
| 5G NR                | L1–L3        | 3GPP           | L3   | TS 38.xxx            | Active  |
| NB-IoT               | L1–L3        | 3GPP           | L3   | Rel. 13 (TS 36.xxx) | Active  |
| PROFINET             | L1–L7        | PI / IEC TC 65 | L1   | IEC 61158/61784      | Active  |
| EtherCAT             | L1–L2        | ETG / IEC      | L1   | IEC 61158 Type 12    | Active  |
| TSN (802.1)          | L2           | IEEE 802.1     | L4   | 802.1AS/Qbv/CB/Qci  | Active  |
| Matter               | L3–L7        | CSA            | L3   | Matter 1.x           | Current |

---

## 12.15 Key Disambiguation Notes

### 1. Wi-Fi vs. IEEE 802.11

- **IEEE 802.11** = the technical standard (amendment letters: ax, be, etc.)
- **Wi-Fi** = the Wi-Fi Alliance certification brand
- Not all 802.11 devices are "Wi-Fi Certified" (certification is voluntary)
- Wi-Fi generation numbers (Wi-Fi 6, Wi-Fi 7) are marketing names by Wi-Fi Alliance

### 2. 5G NSA vs. SA

| Mode | Meaning                         | Architecture                          |
|------|---------------------------------|---------------------------------------|
| NSA  | Non-Standalone                  | 5G NR radio + 4G EPC core (Option 3x)|
| SA   | Standalone                      | 5G NR radio + 5G Core (5GC)          |

NSA was deployed first (faster time-to-market); SA enables full 5G features (slicing, uRLLC, edge).

### 3. LoRaWAN vs. NB-IoT vs. LTE-M

| Factor         | LoRaWAN              | NB-IoT                 | LTE-M (Cat-M1)         |
|----------------|----------------------|------------------------|------------------------|
| Spectrum       | Unlicensed (ISM)     | Licensed (in-band LTE) | Licensed (in-band LTE) |
| Deployment     | Private or public    | Operator-deployed      | Operator-deployed      |
| Mobility       | Limited              | Limited                | **Full mobility** (handover) |
| Voice          | No                   | No                     | Yes (VoLTE)            |
| Data rate      | <50 kbps             | ~250 kbps              | ~1 Mbps                |
| Battery life   | 10+ years            | 10+ years              | 5–10 years             |
| Best for       | Private IoT, rural   | Static meters, sensors | Mobile trackers, health|

### 4. Thread vs. ZigBee vs. Matter

- **ZigBee**: Application-layer protocol + network layer (ZigBee 3.0 uses 802.15.4)
- **Thread**: IP-based mesh networking (6LoWPAN over 802.15.4) — no application layer
- **Matter**: Application-layer protocol running OVER Thread (or Wi-Fi, Ethernet)
- Matter + Thread = the future of smart home (ZigBee is legacy)

---

*End of Chapter 12 — Networking & Telecommunications Standards*
*Next: Chapter 13 — Cybersecurity Standards & Frameworks*
