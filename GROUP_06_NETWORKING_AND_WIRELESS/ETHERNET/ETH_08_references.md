# AUTOMOTIVE ETHERNET — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 08 of 08
# Standards, books, tools, repos, and structured study plan
# ════════════════════════════════════════════════════════════════════

---

## 1. IEEE STANDARDS (Primary Sources)

| Standard | Title | Year | Focus |
|----------|-------|------|-------|
| IEEE 802.3bw | 100BASE-T1 | 2015 | 100 Mbps single-pair |
| IEEE 802.3bp | 1000BASE-T1 | 2016 | 1 Gbps single-pair |
| IEEE 802.3cg | 10BASE-T1S/10BASE-T1L | 2019 | 10 Mbps multidrop/long reach |
| IEEE 802.3ch | Multi-Gig (2.5/5/10G) | 2020 | High-speed backbone |
| IEEE 802.3bu | PoDL (Power over Data Line) | 2016 | Power delivery on data pair |
| IEEE 802.1Q | VLAN tagging | 2018 | VLAN, QoS, bridges |
| IEEE 802.1AS | gPTP (Time Sync) | 2020 | <1µs precision time protocol |
| IEEE 802.1Qbv | Time-Aware Shaper | 2015 | Deterministic scheduling |
| IEEE 802.1Qav | Credit-Based Shaper | 2009 | Bandwidth reservation (AVB) |
| IEEE 802.1Qbu | Frame Preemption (bridge) | 2016 | Interrupt low-priority frames |
| IEEE 802.3br | Frame Preemption (MAC) | 2016 | MAC merge sublayer |
| IEEE 802.1Qci | Per-Stream Filtering/Policing | 2017 | Babbling idiot protection |
| IEEE 802.1CB | Frame Replication/Elimination | 2017 | Seamless redundancy (FRER) |
| IEEE 802.1AE | MACsec | 2018 | Layer 2 encryption |
| IEEE 802.1X | Port Authentication | 2020 | Network access control |
| IEEE 1722 | AVTP | 2016 | Audio/Video transport |

---

## 2. ISO / AUTOMOTIVE STANDARDS

| Standard | Title | Relevance |
|----------|-------|-----------|
| ISO 13400 | DoIP (Diagnostics over IP) | Diagnostic transport protocol |
| ISO 14229 | UDS (Unified Diagnostic Services) | Diagnostic services (carried by DoIP) |
| ISO 11898 | CAN | Legacy bus (bridge to Ethernet) |
| ISO 26262 | Functional Safety | Safety requirements for Ethernet comm |
| ISO/SAE 21434 | Cybersecurity Engineering | Security requirements for vehicle networks |
| AUTOSAR SWS Eth | Ethernet Driver | Classic AUTOSAR MAC driver spec |
| AUTOSAR SWS EthTrcv | Ethernet Transceiver Driver | PHY driver spec |
| AUTOSAR SWS EthIf | Ethernet Interface | Interface abstraction spec |
| AUTOSAR SWS EthSwt | Ethernet Switch Driver | Switch management spec |
| AUTOSAR SWS TcpIp | TCP/IP Stack | TCP/UDP/IP implementation spec |
| AUTOSAR SWS SoAd | Socket Adaptor | PDU-to-socket mapping spec |
| AUTOSAR SWS SD | Service Discovery | SOME/IP-SD implementation spec |
| AUTOSAR SWS DoIP | Diagnostics over IP | DoIP implementation spec |
| AUTOSAR SWS EthTSyn | Ethernet Time Sync | gPTP implementation spec |
| AUTOSAR PRS SOME/IP | SOME/IP Protocol Spec | Message format and behavior |

---

## 3. OPEN ALLIANCE SPECIFICATIONS

| Spec | Title | Purpose |
|------|-------|---------|
| TC1 | PHY Test Spec | 100BASE-T1 PHY interoperability tests |
| TC2 | Cable/Connector Spec | Cable and connector requirements |
| TC8 | ECU Test Spec | End-to-end ECU Ethernet compliance |
| TC10 | Sleep/Wake Spec | PHY power management (sleep/wake behavior) |
| TC12 | Switch Test Spec | Switch interoperability testing |
| TC14 | 1000BASE-T1 PHY Test | Gigabit PHY compliance |

Website: https://www.opensig.org/

---

## 4. BOOKS

### Essential Reading
| Title | Author(s) | Year | Focus |
|-------|-----------|------|-------|
| Automotive Ethernet (3rd ed.) | Kirsten Matheus, Thomas Königseder | 2021 | Comprehensive overview (THE reference book) |
| In-Vehicle Networking | Dominique Paret | 2012 | All automotive buses including Ethernet |
| Ethernet: The Definitive Guide | Charles Spurgeon, Joann Zimmerman | 2014 | Ethernet fundamentals |
| TCP/IP Illustrated Vol. 1 | W. Richard Stevens | 2011 | TCP/IP protocol deep dive |
| Time-Sensitive Networking | IEEE Press | 2021 | TSN standards and applications |
| AUTOSAR Compendium (Eth chapters) | Various (AUTOSAR) | 2022 | AUTOSAR Ethernet stack reference |

### Supplementary Reading
| Title | Focus |
|-------|-------|
| Computer Networking (Kurose & Ross) | Network fundamentals |
| Understanding Linux Network Internals | Linux networking stack |
| Linux Device Drivers (LDD3 + online updates) | PHY/MAC driver development |
| Embedded Networking with CAN and CANopen | Legacy bus context |

---

## 5. ONLINE RESOURCES

### Specifications & Documentation
- AUTOSAR Classic/Adaptive specs: https://www.autosar.org/standards
- IEEE GET Program (free standards): https://ieeexplore.ieee.org/browse/standards/get-program
- SOME/IP spec (AUTOSAR PRS): Via AUTOSAR.org (free registration)
- vsomeip documentation: https://github.com/COVESA/vsomeip/wiki

### Learning & Tutorials
- Vector Knowledge Base (Ethernet): https://www.vector.com/int/en/know-how/
- NXP Automotive Ethernet resources: https://www.nxp.com/applications/automotive/automotive-ethernet
- Marvell Automotive Ethernet: https://www.marvell.com/automotive/
- Texas Instruments 10BASE-T1S: https://www.ti.com/technologies/automotive-ethernet.html
- OPEN Alliance YouTube channel: Webinars on automotive Ethernet
- SAE MOBILUS papers: Automotive Ethernet application papers

### Blogs & Articles
- Technica Engineering Blog: Automotive Ethernet deep dives
- Excelfore Blog: TSN and AVB in automotive
- embeddedartistry.com: Embedded networking articles
- lwn.net: Linux networking subsystem articles

---

## 6. TOOLS

### Protocol Analysis
| Tool | Purpose | Platform |
|------|---------|----------|
| Wireshark | Packet capture & analysis (SOME/IP, DoIP dissectors) | Win/Linux/Mac |
| tcpdump | CLI packet capture | Linux |
| Vector CANoe (Ethernet option) | Professional automotive network analysis | Windows |
| PEAK SOME/IP Explorer | SOME/IP traffic monitoring | Windows |
| Technica Engineering Capture ONE | Automotive Ethernet TAP + analyzer | Hardware |
| Spirent TestCenter | Conformance testing, traffic generation | Hardware |

### Development & Testing
| Tool | Purpose | Notes |
|------|---------|-------|
| iperf3 | Bandwidth measurement | Open source |
| ethtool | PHY/MAC diagnostics | Linux built-in |
| linuxptp (ptp4l, phc2sys) | gPTP implementation | Open source |
| vsomeip | SOME/IP stack for Linux | COVESA, open source |
| CommonAPI | SOME/IP/D-Bus abstraction | COVESA |
| python-doipclient | DoIP testing library | Open source (pip install) |
| python-someip | SOME/IP testing library | Open source |
| tc (iproute2) | TSN traffic control (taprio, cbs) | Linux built-in |

### PHY/Hardware Testing
| Tool | Purpose |
|------|---------|
| Keysight/Tektronix Oscilloscope | Eye diagram, signal integrity |
| OPEN Alliance TC1 Test Suite | PHY compliance |
| Rohde & Schwarz EMC test | CISPR 25 compliance |
| Saleae Logic Analyzer | MDIO bus debugging |
| TDR (Time Domain Reflectometer) | Cable fault location |

### Simulation
| Tool | Purpose |
|------|---------|
| Vector vVIRTUALtarget | Virtual ECU with Ethernet |
| ETAS ISOLAR | AUTOSAR configuration with Ethernet |
| RTaW-Pegase | Network scheduling / worst-case analysis |
| OMNeT++ (INET/CoRE4INET) | Network simulation with TSN |
| QEMU + virtual Ethernet | Embedded Linux Ethernet testing |

---

## 7. GITHUB REPOSITORIES

| Repository | Description |
|-----------|-------------|
| COVESA/vsomeip | Reference SOME/IP implementation (C++) |
| COVESA/capicxx-someip-runtime | CommonAPI C++ SOME/IP binding |
| COVESA/vehicle_signal_specification | Vehicle data model |
| linuxptp/linuxptp | IEEE 1588 / 802.1AS Linux implementation |
| torvalds/linux (drivers/net/phy/) | Linux PHY drivers (TJA1100, etc.) |
| torvalds/linux (drivers/net/ethernet/) | Linux MAC/Ethernet drivers |
| wireshark/wireshark | Protocol dissectors (SOME/IP, DoIP, AVTP) |
| nxp-auto-linux/linux (automotive branch) | NXP automotive Linux (S32G, i.MX8) |
| renesas-rcar/linux-bsp | Renesas R-Car Linux (automotive Ethernet) |
| AVnu/gptp | Intel gPTP implementation |
| automotive-grade-linux/meta-agl | AGL with networking |
| GENIVI/dlt-daemon | Diagnostic Log and Trace (over Ethernet) |

---

## 8. VENDOR DOCUMENTATION

### NXP
- TJA1100/1101 datasheet & application notes
- SJA1105/SJA1110 switch configuration guide
- S32G reference manual (Ethernet chapters)
- AN12697: Automotive Ethernet EMC design guide

### Marvell
- 88Q2112 datasheet (1000BASE-T1 PHY)
- 88Q5050/88Q6113 switch reference
- Automotive Ethernet PHY evaluation board guide

### Broadcom
- BCM89810 datasheet
- BroadR-Reach reference design

### Texas Instruments
- DP83TD510E datasheet (10BASE-T1S)
- Application note: 10BASE-T1S system design
- PHY design guide (automotive EMC)

### Qualcomm
- SA8155P/SA8295P platform Ethernet interface
- QCA Ethernet switch integration

---

## 9. CONFERENCES & TRAINING

| Event | Focus | When |
|-------|-------|------|
| IEEE 802.3 Automotive Ethernet Congress | Latest standards, OEM case studies | Annual (Munich) |
| Vector Congress | Tools, AUTOSAR, Ethernet | Annual |
| SAE World Congress | Automotive technology papers | Annual |
| Embedded World | Embedded Ethernet demos | Annual (Nuremberg) |
| OPEN Alliance workshops | Interoperability events | Periodic |
| COVESA All Members Meeting | vsomeip, vehicle networking | Bi-annual |

### Online Training
- Vector Academy: SOME/IP, DoIP, Ethernet courses
- AUTOSAR Training: Ethernet BSW modules
- Udemy/Coursera: Computer networking fundamentals
- NXP University: Automotive Ethernet hardware design

---

## 10. STRUCTURED STUDY PATH (6 Weeks)

### Week 1: Fundamentals
- [ ] Read: Ethernet frame structure, MAC addressing, switching
- [ ] Read: TCP/IP basics (IP addressing, TCP vs UDP, ARP)
- [ ] Lab: Set up 2 Linux boards, establish link, ping (Lab 1)
- [ ] Lab: Bandwidth testing with iperf3 (Lab 2)
- [ ] Study: ETH_01_master_theory sections 1-6

### Week 2: Physical Layer
- [ ] Read: 100BASE-T1 PHY operation (PAM-3, echo cancellation)
- [ ] Read: Master/slave, MDIO registers, link training
- [ ] Lab: Read PHY registers, configure master/slave
- [ ] Study: PHY datasheets (TJA1100, 88Q2112)
- [ ] Review: ETH_04_flashcards Deck 2

### Week 3: Automotive Protocols
- [ ] Read: SOME/IP specification (AUTOSAR PRS)
- [ ] Read: SOME/IP-SD lifecycle (offer, find, subscribe)
- [ ] Lab: vsomeip service/client (Lab 5)
- [ ] Read: DoIP (ISO 13400) basics
- [ ] Lab: DoIP session simulation (Lab 6)
- [ ] Review: ETH_04_flashcards Decks 4-5

### Week 4: TSN & Real-Time
- [ ] Read: IEEE 802.1AS (gPTP) mechanism
- [ ] Read: 802.1Qbv (TAS), 802.3br (preemption)
- [ ] Lab: gPTP setup with linuxptp (Lab 7)
- [ ] Lab: TSN tc commands (taprio, cbs)
- [ ] Study: ETH_01_master_theory sections 14-15
- [ ] Review: ETH_04_flashcards Deck 6

### Week 5: AUTOSAR & Integration
- [ ] Read: AUTOSAR Ethernet SWS documents (Eth, EthIf, TcpIp, SoAd, SD)
- [ ] Study: Module interaction diagrams
- [ ] Read: AUTOSAR Adaptive ara::com SOME/IP binding
- [ ] Lab: VLAN configuration and isolation (Lab 3)
- [ ] Review: ETH_04_flashcards Deck 7
- [ ] Practice: ETH_03_interview_questions (Mid-level)

### Week 6: Security, Debug & Advanced
- [ ] Read: MACsec (802.1AE), 802.1X port authentication
- [ ] Read: Automotive threat model and countermeasures
- [ ] Lab: Packet capture and analysis (Lab 4)
- [ ] Lab: Debug scenarios 1-7 (systematically)
- [ ] Review: ETH_06_cheatsheet (use as reference during debugging)
- [ ] Practice: ETH_03_interview_questions (Senior + Staff level)
- [ ] Self-test: Can you explain vehicle Ethernet architecture end-to-end?

### Post-6-Weeks: Continuous Learning
- [ ] Follow OPEN Alliance announcements
- [ ] Read IEEE 802.3 meeting minutes (upcoming amendments)
- [ ] Contribute to vsomeip or linuxptp (open source)
- [ ] Build: SOME/IP service on real automotive hardware
- [ ] Build: DoIP tester tool with full UDS support
- [ ] Study: 10BASE-T1S (emerging, high interview value)

---

## 11. CERTIFICATION & VALIDATION

| Certification | Provider | Relevance |
|--------------|----------|-----------|
| OPEN Alliance TC1/TC8 compliance | OPEN Alliance | PHY/ECU interoperability |
| Vector SOME/IP Certified | Vector | SOME/IP expertise |
| AUTOSAR certification | AUTOSAR | BSW Ethernet modules |
| CCNA/CCNP (networking) | Cisco | General networking (supplementary) |
| Wireshark WCNA | Wireshark Univ | Protocol analysis skills |

---

## 12. ACRONYM GLOSSARY

| Acronym | Meaning |
|---------|---------|
| AVB | Audio Video Bridging |
| AVTP | Audio Video Transport Protocol (IEEE 1722) |
| BMCA | Best Master Clock Algorithm |
| CBS | Credit-Based Shaper (802.1Qav) |
| CMC | Common-Mode Choke |
| CNC | Central Network Controller (TSN) |
| DoIP | Diagnostics over Internet Protocol (ISO 13400) |
| FRER | Frame Replication and Elimination for Reliability (802.1CB) |
| gPTP | Generalized Precision Time Protocol (802.1AS) |
| ICV | Integrity Check Value (MACsec) |
| IGMP | Internet Group Management Protocol |
| MACsec | MAC Security (802.1AE) |
| MDIO | Management Data Input/Output |
| MII | Media Independent Interface |
| MKA | MACsec Key Agreement |
| MTU | Maximum Transmission Unit |
| PAM-3 | Pulse Amplitude Modulation, 3 levels |
| PCP | Priority Code Point (802.1Q) |
| PHY | Physical layer transceiver |
| PLCA | Physical Layer Collision Avoidance (10BASE-T1S) |
| PoDL | Power over Data Line |
| PSFP | Per-Stream Filtering and Policing (802.1Qci) |
| RGMII | Reduced Gigabit Media Independent Interface |
| SAK | Secure Association Key (MACsec) |
| SD | Service Discovery (SOME/IP-SD) |
| SGMII | Serial Gigabit Media Independent Interface |
| SoAd | Socket Adaptor (AUTOSAR) |
| SOME/IP | Scalable service-Oriented MiddlewarE over IP |
| SQI | Signal Quality Indicator |
| SRP | Stream Reservation Protocol |
| TAS | Time-Aware Shaper (802.1Qbv) |
| TSN | Time-Sensitive Networking |
| UDS | Unified Diagnostic Services (ISO 14229) |
| UTP | Unshielded Twisted Pair |
| VLAN | Virtual Local Area Network |
| VID | VLAN Identifier |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
