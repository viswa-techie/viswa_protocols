# CHAPTER 12 — DOMAIN: NETWORKING & TELECOMMUNICATIONS STANDARDS
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapters 2, 3, 5

---

## CHAPTER SCOPE

**Domain Coverage — Networking & Telecom:**
- Layer model: OSI 7-layer (ISO/IEC) vs. TCP/IP 4-layer
- Physical / Data Link: IEEE 802.x (Ethernet, Wi-Fi, Bluetooth, ZigBee, WiMAX)
- Network layer: IP (IPv4, IPv6), BGP, OSPF, IS-IS, MPLS
- Transport layer: TCP, UDP, QUIC, SCTP, DCCP
- Application protocols: DNS, DHCP, NTP/PTP, SNMP, NETCONF, YANG, gRFC
- SDN and NFV: OpenFlow (ONF), ETSI NFV, OpenDaylight, ONOS
- 3GPP cellular standards: 2G/3G/4G LTE/5G NR and 5G core architecture
- ETSI Telecom standards: NFV, MEC (Multi-Access Edge Computing)
- ITU-T Transport: OTN, DWDM, SDH/SONET
- Time-Sensitive Networking (TSN): IEEE 802.1 TSN task group
- Industrial networking: PROFINET, EtherCAT, Modbus, HART
- Wireless IoT: ZigBee, Z-Wave, Thread, Matter, LoRaWAN, NB-IoT, LTE-M

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class networking and telecommunications standards specialist.
Generate the complete **Chapter 12: Networking & Telecommunications Standards** encyclopedia.

### SECTION 12.1 — OSI vs. TCP/IP REFERENCE MODELS

Produce a precise comparison of the two dominant networking reference models:

| Layer # | OSI Model (ISO/IEC 7498)    | Protocol Data Unit | TCP/IP Model      | Key Protocols                    |
|---------|-----------------------------|--------------------|--------------------|----------------------------------|
| 7       | Application                 | Data               | Application        | HTTP, DNS, FTP, SMTP, SNMP       |
| 6       | Presentation                | Data               | Application        | TLS/SSL, MIME, ASCII, JPEG       |
| 5       | Session                     | Data               | Application        | RPC, NetBIOS, PPTP               |
| 4       | Transport                   | Segment/Datagram   | Transport          | TCP, UDP, QUIC, SCTP             |
| 3       | Network                     | Packet             | Internet           | IPv4, IPv6, ICMP, BGP, OSPF      |
| 2       | Data Link                   | Frame              | Network Access     | Ethernet (802.3), Wi-Fi (802.11), HDLC |
| 1       | Physical                    | Bit                | Network Access     | 100BASE-T, 1000BASE-T, OFDM      |

**Disambiguation:**
- OSI model: ISO/IEC 7498 — normative reference model; theoretical; rarely implemented exactly
- TCP/IP model: IETF de-facto — the real internet architecture; layers 5/6 collapsed into 4
- TLS sits between transport and application — doesn't fit cleanly in OSI
- "Layer 7 firewall" = application-aware; "Layer 3 firewall" = packet filtering — these use OSI terminology even in TCP/IP deployments

### SECTION 12.2 — ETHERNET IEEE 802.3 (DETAILED)

| Generation         | Standard       | Speed        | Medium              | Max Distance  |
|--------------------|----------------|--------------|---------------------|---------------|
| 10BASE-T           | IEEE 802.3i    | 10 Mbps      | Cat3 UTP            | 100 m         |
| 100BASE-TX (Fast)  | IEEE 802.3u    | 100 Mbps     | Cat5 UTP            | 100 m         |
| 1000BASE-T (Gigabit)| IEEE 802.3ab  | 1 Gbps       | Cat5e UTP           | 100 m         |
| 1000BASE-X (SFP)   | IEEE 802.3z    | 1 Gbps       | Fiber/copper        | 550 m / 5 km  |
| 10GBASE-T          | IEEE 802.3an   | 10 Gbps      | Cat6A UTP           | 100 m         |
| 10GBASE-SR         | IEEE 802.3ae   | 10 Gbps      | OM3/OM4 MMF         | 300 m         |
| 25GBASE-T          | IEEE 802.3bq   | 25 Gbps      | Cat8 UTP            | 30 m          |
| 40GBASE-SR4        | IEEE 802.3ba   | 40 Gbps      | OM3 MMF             | 100 m         |
| 100GBASE-SR4       | IEEE 802.3bm   | 100 Gbps     | OM4 MMF             | 100 m         |
| 200GBASE-DR4       | IEEE 802.3bs   | 200 Gbps     | SMF                 | 500 m         |
| 400GBASE-SR16      | IEEE 802.3bs   | 400 Gbps     | OM4 MMF             | 100 m         |
| 100BASE-T1 (Auto)  | IEEE 802.3bp   | 100 Mbps     | Single pair UTP     | 15 m (auto ECU)|
| 1000BASE-T1 (Auto) | IEEE 802.3bp   | 1 Gbps       | Single pair STP     | 15 m (auto ECU)|

### SECTION 12.3 — WI-FI IEEE 802.11 GENERATIONS

| Generation | Standard     | Max Speed     | Frequency      | Key Feature                      |
|------------|--------------|---------------|----------------|----------------------------------|
| Wi-Fi 1    | 802.11b      | 11 Mbps       | 2.4 GHz        | DSSS; first widely adopted       |
| Wi-Fi 2    | 802.11a      | 54 Mbps       | 5 GHz          | OFDM, less range than 802.11b   |
| Wi-Fi 3    | 802.11g      | 54 Mbps       | 2.4 GHz        | OFDM backward compatible        |
| Wi-Fi 4    | 802.11n      | 600 Mbps      | 2.4/5 GHz      | MIMO, 40 MHz channels           |
| Wi-Fi 5    | 802.11ac     | 3.5 Gbps      | 5 GHz          | MU-MIMO (DL), 80/160 MHz        |
| Wi-Fi 6    | 802.11ax     | 9.6 Gbps      | 2.4/5 GHz      | OFDMA, MU-MIMO (UL+DL), BSS Coloring, TWT |
| Wi-Fi 6E   | 802.11ax ext.| 9.6 Gbps      | 6 GHz band     | New 6 GHz band, low interference|
| Wi-Fi 7    | 802.11be     | 46 Gbps       | 2.4/5/6 GHz   | Multi-Link Operation (MLO), 4K QAM, 320 MHz |

### SECTION 12.4 — 3GPP CELLULAR STANDARDS

Full entry for 3GPP:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | 3rd Generation Partnership Project               |
| Abbreviation                 | 3GPP                                              |
| Entry Type                   | International SDO consortium                     |
| Governance Tier              | L3                                                |
| Organizational partners      | ETSI (EU), ARIB (Japan), TSDSI (India), ATIS (North America), CCSA (China), TTA (Korea), TSDSI |
| Founded                      | 1998                                              |
| Output                       | Technical Specifications (TS) and Technical Reports (TR) |
| Release numbering            | Release 99 (3G) → Rel. 4, 5, 6... → Rel. 16 (5G NR) → Rel. 17 → Rel. 18 (5G-Advanced) → Rel. 19 |
| Specification access         | Free — 3gpp.org                                  |

**3GPP Release and Technology Generations:**

| Release | Year    | Technology   | Key Features                                        |
|---------|---------|--------------|-----------------------------------------------------|
| Rel. 99 | 1999    | 3G UMTS      | WCDMA, HSDPA foundations                           |
| Rel. 7  | 2007    | HSPA+        | MIMO, 64QAM uplink                                 |
| Rel. 8  | 2008    | 4G LTE       | OFDMA DL, SC-FDMA UL, EPC, 100 Mbps DL target    |
| Rel. 9  | 2009    | LTE Adv      | Enhanced MBMS                                      |
| Rel. 10 | 2011    | LTE-A        | Carrier aggregation, Hetnet, CoMP                  |
| Rel. 13 | 2016    | LTE-M/NB-IoT | Cat-M1 (eMTC), NB-IoT for IoT devices              |
| Rel. 15 | 2018    | 5G NR Phase 1| Non-standalone (NSA) and standalone (SA) NR, eMBB, uRLLC, mMTC |
| Rel. 16 | 2020    | 5G Phase 2   | URLLC enhancements, V2X (C-V2X), NRPOS, IIoT      |
| Rel. 17 | 2022    | 5G Phase 3   | NTN (satellite), RedCap (reduced capability), SL-MIMO |
| Rel. 18 | 2024    | 5G-Advanced  | AI/ML in RAN, XR enhancements, network energy efficiency |

**5G Architecture Components:**

```
gNB (5G base station)
        ↓ Xn interface (gNB-gNB)
        ↓ NG interface
5G Core (5GC):
  AMF — Access and Mobility Management Function (entry point)
  SMF — Session Management Function (PDU sessions)
  UPF — User Plane Function (packet forwarding — data plane)
  PCF — Policy Control Function
  UDM — Unified Data Management (subscriber data)
  AUSF — Authentication Server Function
  NRF — Network Repository Function (service registration)
  NSSF — Network Slice Selection Function
  NEF — Network Exposure Function (API to external apps)
```

### SECTION 12.5 — TIME-SENSITIVE NETWORKING (TSN)

TSN is a set of IEEE 802.1 standards enabling deterministic Ethernet — critical for automotive, industrial automation, and avionics:

| Standard    | Title                                              | Function                             |
|-------------|----------------------------------------------------|--------------------------------------|
| IEEE 802.1AS| Timing and Synchronization (gPTP)                 | Sub-microsecond time sync            |
| IEEE 802.1Qbv| Enhancements for Scheduled Traffic (TAS)          | Time-Aware Shaper — time slots       |
| IEEE 802.1Qbu| Frame Preemption                                  | Interrupt low-priority frames for high-priority |
| IEEE 802.1CB| Frame Replication and Elimination (FRER)          | Redundancy for safety-critical      |
| IEEE 802.1Qcc| Stream Reservation Protocol (SRP) enhancements   | Centralized/distributed configuration|
| IEEE 802.1Qci| Per-stream filtering and policing                  | Ingress traffic control              |
| IEEE 802.1Qcr| Asynchronous Traffic Shaping (ATS)                | Credit-based shaper                  |
| IEEE 802.1CS| Link-local Registration Protocol                  | Topology discovery                   |

**TSN Use Cases:**
- **Automotive**: Camera, LiDAR, Radar sensor fusion over Automotive Ethernet
- **Industrial**: Motion control (servo synchronization <1 μs jitter)
- **Avionics**: AFDX (ARINC 664) replacement consideration
- **Professional AV**: SMPTE ST 2110 media-over-IP (uses gPTP)

### SECTION 12.6 — INDUSTRIAL NETWORKING PROTOCOLS

| Protocol     | Standard          | Tier | Speed             | Use Case                          |
|--------------|-------------------|------|-------------------|-----------------------------------|
| PROFINET     | IEC 61158/61784   | L1   | 100Mbps/1Gbps    | Industrial Ethernet (Siemens)    |
| EtherCAT     | IEC 61158 Type 12 | L1/L3| Up to 100 Mbps  | Ultra-fast motion control (Beckhoff)|
| Modbus RTU   | Modicon (de-facto)| L8   | 9.6–115.2 kbps   | Legacy PLC/sensor (RS-485)       |
| Modbus TCP   | Modicon + IANA    | L8   | 100 Mbps Eth     | Legacy PLC over Ethernet          |
| PROFIBUS     | IEC 61158 Type 3  | L1   | 12 Mbps          | Legacy field bus (Siemens)        |
| HART         | FieldComm Group   | L3   | 1.2 kbps over 4-20mA | Instrument analog + digital  |
| Foundation Fieldbus | FieldComm  | L3   | 31.25 kbps / 1 Mbps | Process automation          |
| DeviceNet    | ODVA (CIP)        | L3   | 125–500 kbps     | Industrial device bus (CAN-based)|
| EtherNet/IP  | ODVA (CIP over IP)| L3   | 100Mbps/1Gbps    | Industrial Ethernet (Rockwell/ODVA)|

### SECTION 12.7 — WIRELESS IOT STANDARDS

| Protocol   | Standard          | Tier | Frequency   | Range        | Use Cases                     |
|------------|-------------------|------|-------------|--------------|-------------------------------|
| ZigBee     | Zigbee Alliance   | L3   | 2.4 GHz     | 10–100 m     | Home automation, smart energy |
| Z-Wave     | Z-Wave Alliance   | L3   | 868/908 MHz | 30–100 m     | Home automation               |
| Thread     | Thread Group      | L3   | 2.4 GHz     | 30–100 m     | IoT mesh (Matter backbone)    |
| Matter     | CSA (Matter)      | L3   | IP-based    | Network-wide | Smart home interoperability   |
| LoRaWAN    | LoRa Alliance     | L3   | 868/915 MHz | 2–15 km      | LPWAN — meters, agriculture   |
| NB-IoT     | 3GPP Rel. 13      | L3   | Licensed LTE| 1–10 km      | Smart meters, city sensors    |
| LTE-M      | 3GPP Rel. 13      | L3   | Licensed LTE| 1–10 km      | Mobile IoT, healthcare        |
| Sigfox     | Sigfox            | L7   | 868/902 MHz | 3–50 km      | Legacy LPWAN (company)        |
| BLE (5.x)  | Bluetooth SIG     | L3   | 2.4 GHz     | 10–100 m     | IoT, health, location         |
| UWB         | IEEE 802.15.4a/z  | L3/L1| 3.1–10.6 GHz| 10–50 m     | Precision ranging, keyless    |

### SECTION 12.8 — SDN / NFV STANDARDS

| Technology/Body   | Standard/Project   | Description                                 | Governance |
|-------------------|--------------------|---------------------------------------------|------------|
| OpenFlow          | ONF                | First SDN southbound interface protocol     | L3 (ONF)   |
| P4                | P4.org             | Programming Protocol-independent Packet Processors | L5  |
| ETSI NFV          | ETSI ISG NFV       | Network Functions Virtualization architecture| L2/L3      |
| YANG data models  | IETF (RFC 6020/7950)| Data modeling language for NETCONF/RESTCONF | L3 (IETF) |
| NETCONF           | IETF (RFC 6241)    | Network device configuration protocol      | L3 (IETF)  |
| RESTCONF          | IETF (RFC 8040)    | REST variant of NETCONF                    | L3 (IETF)  |
| gNMI              | OpenConfig (Google-led) | gRPC network management interface      | L6         |
| OpenConfig        | OpenConfig WG      | Vendor-neutral network config models       | L6         |
| SR-MPLS / SRv6    | IETF SPRING WG     | Segment Routing (IPv6 extensions)          | L3 (IETF)  |

### SECTION 12.9 — NETWORKING STANDARDS MASTER CROSSWALK TABLE

| Protocol/Standard   | OSI Layer(s)    | Governing Body   | Tier | Key RFC/Standard    | Status     |
|---------------------|-----------------|------------------|------|---------------------|------------|
| Ethernet            | L1/L2           | IEEE 802.3       | L4   | IEEE 802.3          | Current    |
| IPv4                | L3              | IETF             | L3   | RFC 791             | Active     |
| IPv6                | L3              | IETF             | L3   | RFC 8200            | Current    |
| TCP                 | L4              | IETF             | L3   | RFC 9293            | Current    |
| UDP                 | L4              | IETF             | L3   | RFC 768             | Active     |
| QUIC                | L4              | IETF             | L3   | RFC 9000            | Current    |
| TLS 1.3             | L5/L6           | IETF             | L3   | RFC 8446            | Current    |
| DNS                 | L7              | IETF             | L3   | RFC 1034/1035       | Active     |
| BGP-4               | L3 routing      | IETF             | L3   | RFC 4271            | Active     |
| OSPF v3             | L3 routing      | IETF             | L3   | RFC 5340            | Active     |
| MPLS                | L2.5            | IETF             | L3   | RFC 3031            | Active     |
| NETCONF             | L7 mgmt         | IETF             | L3   | RFC 6241            | Active     |
| 5G NR               | L1-L3 (3GPP)    | 3GPP             | L3   | 3GPP TS 38.xxx      | Active     |
| Wi-Fi 6             | L1/L2           | IEEE 802.11      | L4   | IEEE 802.11ax       | Current    |
| Bluetooth 5.4       | L1/L2           | Bluetooth SIG    | L3   | Core Spec 5.4       | Current    |
| PROFINET            | L1-L7           | IEC TC 65        | L1   | IEC 61158/61784     | Active     |

---

## OUTPUT FORMAT REQUIREMENTS

- All IEEE 802 amendment numbers must be exact
- 3GPP Release numbers and years must be correct
- OSI layer numbers must be consistent (L1 = Physical, L7 = Application)
- IPv4 and IPv6 RFC numbers must be current (IPv4 = RFC 791; IPv6 = RFC 8200)
- Minimum length: 5,500 words
