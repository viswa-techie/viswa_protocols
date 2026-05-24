# Wi-Fi (IEEE 802.11) — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11a/b/g/n/ac/ax/be) | Document: 00 of 08
# Domain: Wireless LAN | Automotive + Mobile + Embedded
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Details |
|-------|---------|
| **Full Name** | Wireless Fidelity (Wi-Fi) / IEEE 802.11 |
| **Standard Body** | IEEE (Institute of Electrical and Electronics Engineers) |
| **Certification Body** | Wi-Fi Alliance |
| **First Release** | IEEE 802.11 — 1997 (2 Mbps) |
| **Latest Standard** | IEEE 802.11be — Wi-Fi 7 (2024) |
| **Frequency Bands** | 2.4 GHz, 5 GHz, 6 GHz (Wi-Fi 6E/7) |
| **Maximum Data Rate** | 46 Gbps (Wi-Fi 7, 320 MHz, 4096-QAM, 16×16 MIMO) |
| **Typical Range** | Indoor: 30-50m | Outdoor: 100-300m |
| **Topology** | Infrastructure (AP/STA), Ad-hoc (IBSS), Wi-Fi Direct (P2P), Mesh |
| **Access Method** | CSMA/CA (Carrier Sense Multiple Access / Collision Avoidance) |
| **Security** | WPA3 (SAE), WPA2 (AES-CCMP), OWE, 802.1X/EAP |
| **Automotive Use** | Hotspot (SAP), Phone Projection (Wi-Fi Direct/Miracast), OTA updates, V2X |
| **Key IC (Automotive)** | QCA6698AQ (Qualcomm combo WiFi 6E + BT 5.2) |
| **Linux Subsystem** | cfg80211 / mac80211 / nl80211 + vendor drivers |
| **Android Component** | WifiService → wpa_supplicant → kernel driver |

---

## GENERATION COMPARISON TABLE

| Generation | Standard | Year | Band | Max Rate | Channel BW | Key Technology |
|-----------|----------|------|------|----------|------------|----------------|
| — | 802.11 | 1997 | 2.4 GHz | 2 Mbps | 22 MHz | FHSS/DSSS |
| — | 802.11b | 1999 | 2.4 GHz | 11 Mbps | 22 MHz | CCK |
| — | 802.11a | 1999 | 5 GHz | 54 Mbps | 20 MHz | OFDM |
| — | 802.11g | 2003 | 2.4 GHz | 54 Mbps | 20 MHz | OFDM |
| **Wi-Fi 4** | 802.11n | 2009 | 2.4/5 GHz | 600 Mbps | 40 MHz | MIMO, HT |
| **Wi-Fi 5** | 802.11ac | 2013 | 5 GHz | 6.93 Gbps | 160 MHz | MU-MIMO DL, VHT, 256-QAM |
| **Wi-Fi 6** | 802.11ax | 2020 | 2.4/5 GHz | 9.6 Gbps | 160 MHz | OFDMA, UL MU-MIMO, 1024-QAM, TWT, BSS Color |
| **Wi-Fi 6E** | 802.11ax | 2021 | 2.4/5/6 GHz | 9.6 Gbps | 160 MHz | 6 GHz band (1200 MHz new spectrum) |
| **Wi-Fi 7** | 802.11be | 2024 | 2.4/5/6 GHz | 46 Gbps | 320 MHz | MLO, 4096-QAM, 16 spatial streams, Multi-RU |

---

## WiFi IN AUTOMOTIVE CONTEXT

### Use Cases in Modern Vehicles (AAOS / SA8295P)

| Use Case | Mode | Band | Typical Config |
|----------|------|------|----------------|
| **Passenger Hotspot** | SAP (SoftAP) | 5/6 GHz | Car provides internet to phones/tablets |
| **Phone Projection** | Wi-Fi Direct / P2P | 5 GHz | Android Auto Wireless, CarPlay Wireless |
| **OTA Updates** | STA | 5 GHz | Large firmware downloads (GB-scale) |
| **V2X (DSRC)** | 802.11p/bd | 5.9 GHz | Vehicle-to-everything communication |
| **In-Vehicle Network** | STA/AP | 5/6 GHz | ECU-to-ECU (replacing some Ethernet) |
| **Diagnostics** | STA | 2.4/5 GHz | Factory/service WiFi for diagnostics |
| **Rear-Seat Entertainment** | SAP | 5/6 GHz | Streaming to rear displays |
| **Tethering from Phone** | STA | 2.4/5 GHz | Car connects to phone hotspot |

### Qualcomm Automotive WiFi Hardware

| Chip | WiFi Standard | Bands | MIMO | Key Features |
|------|--------------|-------|------|--------------|
| **QCA6698AQ** | WiFi 6E | 2.4/5/6 GHz | 2×2 | Combo BT 5.2, automotive grade, -40°C to +105°C |
| QCA6696 | WiFi 6E | 2.4/5/6 GHz | 2×2 | Non-automotive variant |
| QCA6390 | WiFi 6 | 2.4/5 GHz | 2×2 | Older generation |
| SA8295P integration | — | — | — | PCIe interface to QCA6698AQ |

---

## KEY TERMS & CONCEPTS (60 Terms)

| # | Term | Definition |
|---|------|-----------|
| 1 | **AP** | Access Point — infrastructure device providing BSS |
| 2 | **STA** | Station — client device connecting to AP |
| 3 | **BSS** | Basic Service Set — AP + associated STAs |
| 4 | **BSSID** | BSS Identifier — AP's MAC address |
| 5 | **SSID** | Service Set Identifier — network name (0-32 bytes) |
| 6 | **ESS** | Extended Service Set — multiple APs with same SSID (roaming) |
| 7 | **IBSS** | Independent BSS — ad-hoc (no AP) |
| 8 | **SAP** | Soft Access Point — software AP (hotspot mode) |
| 9 | **P2P** | Peer-to-Peer (Wi-Fi Direct) — device-to-device without AP |
| 10 | **OFDM** | Orthogonal Frequency Division Multiplexing — multi-carrier modulation |
| 11 | **OFDMA** | OFDM Access — multi-user simultaneous access (WiFi 6) |
| 12 | **MIMO** | Multiple-Input Multiple-Output — multiple antennas |
| 13 | **MU-MIMO** | Multi-User MIMO — simultaneous multi-user spatial streams |
| 14 | **Beamforming** | Focusing signal toward specific STA (phased array) |
| 15 | **CSMA/CA** | Carrier Sense Multiple Access / Collision Avoidance |
| 16 | **RTS/CTS** | Request to Send / Clear to Send — hidden node mitigation |
| 17 | **NAV** | Network Allocation Vector — virtual carrier sense timer |
| 18 | **DCF** | Distributed Coordination Function — basic contention access |
| 19 | **EDCA** | Enhanced Distributed Channel Access — QoS with priorities |
| 20 | **QoS** | Quality of Service — traffic prioritization (AC_VO/VI/BE/BK) |
| 21 | **WMM** | Wi-Fi Multimedia — QoS certification (maps to EDCA) |
| 22 | **TWT** | Target Wake Time — scheduled sleep/wake (WiFi 6 power saving) |
| 23 | **BSS Color** | 6-bit identifier to distinguish overlapping BSSs (WiFi 6) |
| 24 | **OFDMA RU** | Resource Unit — smallest OFDMA allocation (26 subcarriers minimum) |
| 25 | **MLO** | Multi-Link Operation — aggregate links across bands (WiFi 7) |
| 26 | **Channel Bonding** | Combining adjacent channels (40/80/160/320 MHz) |
| 27 | **Guard Interval** | Gap between OFDM symbols (0.8µs / 0.4µs / 1.6µs / 3.2µs) |
| 28 | **MCS** | Modulation and Coding Scheme index (maps to rate) |
| 29 | **QAM** | Quadrature Amplitude Modulation (16/64/256/1024/4096) |
| 30 | **Spatial Streams** | Independent data streams via MIMO antennas (1-16) |
| 31 | **WPA3** | Wi-Fi Protected Access 3 (SAE + 192-bit, 2018) |
| 32 | **SAE** | Simultaneous Authentication of Equals (WPA3 handshake) |
| 33 | **OWE** | Opportunistic Wireless Encryption (open but encrypted) |
| 34 | **802.1X** | Port-based authentication (Enterprise: RADIUS + EAP) |
| 35 | **EAP** | Extensible Authentication Protocol (PEAP/TLS/TTLS) |
| 36 | **PMF** | Protected Management Frames (802.11w, mandatory in WPA3) |
| 37 | **4-Way Handshake** | EAPOL key exchange (derive PTK from PMK) |
| 38 | **PTK** | Pairwise Transient Key (unicast encryption) |
| 39 | **GTK** | Group Temporal Key (broadcast/multicast encryption) |
| 40 | **PMK** | Pairwise Master Key (derived from passphrase or 802.1X) |
| 41 | **CCMP** | Counter Mode CBC-MAC Protocol (AES-128 encryption, WPA2) |
| 42 | **GCMP** | Galois/Counter Mode Protocol (AES-256, WPA3-192bit) |
| 43 | **Beacon** | Periodic AP broadcast (SSID, rates, capabilities, timestamp) |
| 44 | **Probe Request/Response** | Active scanning (STA asks, AP replies) |
| 45 | **Association** | STA joins BSS (auth + assoc request/response) |
| 46 | **Roaming** | STA moves between APs in same ESS (reassociation) |
| 47 | **802.11r (FT)** | Fast BSS Transition — fast roaming (pre-auth keys) |
| 48 | **802.11k (RRM)** | Radio Resource Management (neighbor reports) |
| 49 | **802.11v (WNM)** | Wireless Network Management (BSS transition hints) |
| 50 | **DFS** | Dynamic Frequency Selection (avoid radar on 5 GHz) |
| 51 | **TPC** | Transmit Power Control (regulatory compliance) |
| 52 | **Wi-Fi Direct** | P2P connection without AP (uses GO + Client roles) |
| 53 | **GO** | Group Owner (acts as AP in Wi-Fi Direct) |
| 54 | **Miracast** | Display mirroring over Wi-Fi Direct (H.264/H.265 stream) |
| 55 | **wpa_supplicant** | Linux/Android userspace WPA/WPA2/WPA3 authenticator |
| 56 | **hostapd** | Linux userspace AP daemon (SoftAP) |
| 57 | **cfg80211** | Linux kernel WiFi configuration API |
| 58 | **mac80211** | Linux kernel WiFi MAC layer framework |
| 59 | **nl80211** | Netlink-based userspace↔kernel WiFi interface |
| 60 | **iw** | Linux command-line tool for WiFi (replaces iwconfig) |

---

## DOCUMENT MAP

| Doc # | File | Content Summary |
|--------|------|-----------------|
| 00 | WIFI_00_index.md | This file — protocol ID, key terms, roadmap |
| 01 | WIFI_01_master_theory.md | Complete WiFi theory (PHY→MAC→Security→Automotive) |
| 02 | WIFI_02_diagrams.md | 20+ diagrams (frame formats, state machines, architectures) |
| 03 | WIFI_03_interview_questions.md | 60+ questions (Junior → Staff) with detailed answers |
| 04 | WIFI_04_flashcards.md | 250+ spaced-repetition flashcards in 10 decks |
| 06 | WIFI_06_cheatsheet.md | Quick reference (MCS tables, channels, commands, frames) |
| 07 | WIFI_07_labs_debugging.md | Hands-on labs + real automotive debug scenarios |
| 08 | WIFI_08_references.md | Specs, AOSP paths, tools, 6-week study plan, glossary |

---

## STUDY CHECKLIST

### Beginner (Week 1-2)
- [ ] Understand 802.11 generations (b/g/n/ac/ax/be)
- [ ] Know frequency bands (2.4/5/6 GHz) and channel layout
- [ ] Understand BSS/AP/STA/SSID/BSSID concepts
- [ ] Know basic frame types (Management/Control/Data)
- [ ] Understand CSMA/CA and why WiFi uses it
- [ ] Know WPA2/WPA3 basics (passphrase → encryption)
- [ ] Use `iw` and `iwconfig` to check WiFi status

### Intermediate (Week 3-4)
- [ ] Understand OFDM and subcarrier structure
- [ ] Know MIMO and spatial streams concept
- [ ] Understand channel bonding (20/40/80/160 MHz)
- [ ] Know MCS index → data rate mapping
- [ ] Understand 4-way handshake and key hierarchy
- [ ] Know WiFi 6 features (OFDMA, TWT, BSS Color)
- [ ] Understand roaming (802.11r/k/v)
- [ ] Know WiFi Direct / P2P operation
- [ ] Capture and analyze WiFi frames in Wireshark

### Advanced (Week 5-6)
- [ ] Understand OFDMA resource unit allocation
- [ ] Know WiFi 7 MLO and 320 MHz channels
- [ ] Understand Android WiFi architecture (Framework→HAL→Driver)
- [ ] Know wpa_supplicant / hostapd internals
- [ ] Understand automotive WiFi use cases (hotspot + projection + OTA)
- [ ] Know WiFi/BT coexistence mechanisms
- [ ] Debug WiFi connectivity/throughput issues
- [ ] Understand DFS/TPC and regulatory domains
- [ ] Know 802.11p/V2X and automotive V2X evolution
- [ ] Understand WiFi power management (PS-Poll, U-APSD, TWT)

---

END OF DOCUMENT 00 — INDEX
