# Wi-Fi (IEEE 802.11) — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 08 of 08
# Format: Specs, source paths, books, tools, study plan
# ════════════════════════════════════════════════════════════════════

---

## 1. IEEE & Wi-Fi Alliance SPECIFICATIONS

| Document | Coverage | Access |
|----------|----------|--------|
| IEEE 802.11-2020 | Base standard (consolidated) | IEEE GET |
| IEEE 802.11ax-2021 | WiFi 6 (HE) amendment | IEEE |
| IEEE 802.11be (Draft) | WiFi 7 (EHT) | IEEE Draft |
| IEEE 802.11ac-2013 | WiFi 5 (VHT) | IEEE |
| IEEE 802.11n-2009 | WiFi 4 (HT) | IEEE |
| IEEE 802.11w-2009 | Protected Management Frames | IEEE |
| IEEE 802.11r-2008 | Fast BSS Transition | IEEE |
| IEEE 802.11k-2008 | Radio Resource Measurement | IEEE |
| IEEE 802.11v-2011 | Wireless Network Management | IEEE |
| IEEE 802.11i-2004 | Security (RSN) | IEEE |
| IEEE 802.11p-2010 | Vehicular (WAVE/DSRC) | IEEE |
| IEEE 802.11s-2011 | Mesh networking | IEEE |
| Wi-Fi Alliance WPA3 Spec | WPA3/SAE/OWE details | wfa.org |
| Wi-Fi Alliance Wi-Fi 6 | Certification program | wfa.org |
| Wi-Fi Alliance Wi-Fi 7 | Certification program | wfa.org |
| Wi-Fi Alliance WMM | QoS specification | wfa.org |
| Wi-Fi Alliance WiFi Direct | P2P specification | wfa.org |
| Wi-Fi Alliance Miracast | Display specification | wfa.org |

---

## 2. KERNEL SOURCE PATHS (Linux/Android)

```
# cfg80211 & nl80211 (WiFi configuration subsystem)
net/wireless/
├── core.c                    # cfg80211 core
├── nl80211.c                 # Netlink interface (userspace API)
├── scan.c                    # Scan handling
├── sme.c                     # Station Mode Entity (connection mgmt)
├── mlme.c                    # Management frame handling
├── chan.c                     # Channel/regulatory
├── reg.c                      # Regulatory database
├── util.c                    # Utility functions
└── wext-compat.c             # Wireless Extensions compatibility

# mac80211 (Software MAC implementation)
net/mac80211/
├── main.c                    # mac80211 core
├── rx.c                      # Frame reception
├── tx.c                      # Frame transmission
├── sta_info.c                # Station tracking
├── mlme.c                    # STA MLME (auth/assoc/roam)
├── agg-rx.c                  # RX aggregation (A-MPDU)
├── agg-tx.c                  # TX aggregation
├── key.c                     # Key management
├── michael.c                 # TKIP MIC
├── aes_ccm.c                 # AES-CCMP implementation
├── scan.c                    # Scan state machine
├── rate.c                    # Rate control framework
├── rc80211_minstrel_ht.c     # Minstrel rate algorithm
└── mesh.c                    # 802.11s mesh

# Qualcomm WiFi driver (qcacld-3.0)
vendor/qcom/opensource/wlan/qcacld-3.0/
├── core/
│   ├── hdd/src/              # Host Device Driver
│   │   ├── wlan_hdd_main.c   # Module init, probe
│   │   ├── wlan_hdd_cfg80211.c  # cfg80211 ops
│   │   ├── wlan_hdd_softap.c # SoftAP implementation
│   │   ├── wlan_hdd_p2p.c   # P2P/WiFi Direct
│   │   ├── wlan_hdd_power.c # Power management
│   │   └── wlan_hdd_scan.c  # Scan handling
│   ├── mac/src/              # MAC layer
│   ├── sme/src/              # Station Management Entity
│   └── wma/src/              # WMI-to-MAC adapter
├── components/
│   ├── mlme/                  # MLME component
│   ├── p2p/                  # P2P component
│   └── tdls/                 # TDLS component
└── Kconfig                    # Kernel config options

# Regulatory database
net/wireless/db.txt            # Built-in regulatory (CRDA)
/lib/firmware/regulatory.db    # Runtime regulatory database

# Wireless headers
include/net/cfg80211.h         # cfg80211 API
include/uapi/linux/nl80211.h   # nl80211 userspace API
include/net/mac80211.h         # mac80211 driver API
```

---

## 3. ANDROID AOSP PATHS

```
# WiFi Service (Framework - Java)
packages/modules/Wifi/
├── framework/
│   └── java/android/net/wifi/
│       ├── WifiManager.java           # Public API
│       ├── WifiInfo.java              # Connection info
│       ├── ScanResult.java            # Scan result data
│       └── WifiConfiguration.java     # Network config
├── service/
│   └── java/com/android/server/wifi/
│       ├── WifiServiceImpl.java       # Service entry point
│       ├── ClientModeImpl.java        # STA state machine
│       ├── SoftApManager.java         # SoftAP controller
│       ├── WifiNative.java            # Native bridge
│       ├── WifiConnectivityManager.java # Auto-connect
│       ├── WifiScanner.java           # Scan scheduler
│       ├── WifiNetworkSelector.java   # Network selection
│       ├── WifiTracker.java           # UI data provider
│       └── p2p/
│           └── WifiP2pServiceImpl.java # P2P service

# WiFi HAL (AIDL - Android 13+)
hardware/interfaces/wifi/aidl/
├── android/hardware/wifi/
│   ├── IWifi.aidl                     # Top-level WiFi HAL
│   ├── IWifiChip.aidl                 # Chip-level operations
│   ├── IWifiStaIface.aidl             # STA interface
│   ├── IWifiApIface.aidl              # AP interface
│   └── IWifiP2pIface.aidl             # P2P interface

# Supplicant HAL (AIDL)
hardware/interfaces/wifi/supplicant/aidl/
├── android/hardware/wifi/supplicant/
│   ├── ISupplicant.aidl
│   ├── ISupplicantStaIface.aidl
│   └── ISupplicantP2pIface.aidl

# wpa_supplicant & hostapd
external/wpa_supplicant_8/
├── wpa_supplicant/
│   ├── wpa_supplicant.c              # Main daemon
│   ├── events.c                       # Event handling
│   ├── scan.c                         # Scan logic
│   ├── bss.c                          # BSS list management
│   └── p2p_supplicant.c              # P2P integration
├── hostapd/
│   ├── hostapd.c                      # AP daemon
│   ├── ctrl_iface.c                   # Control interface
│   └── config_file.c                  # Config parsing
└── src/
    ├── drivers/driver_nl80211.c       # nl80211 driver interface
    ├── rsn_supp/                       # RSN supplicant (4-way)
    ├── eap_peer/                       # EAP methods
    ├── ap/                             # AP mode code
    └── p2p/                            # P2P protocol code

# Automotive WiFi extensions
packages/services/Car/
├── car-lib/src/.../wifi/              # Car WiFi APIs
└── service/src/.../wifi/              # Car WiFi service

# Connectivity (network selection)
packages/modules/Connectivity/
├── service/
│   └── java/com/android/server/
│       └── ConnectivityService.java   # Network scoring/selection
```

---

## 4. FIRMWARE & CONFIGURATION PATHS (Runtime)

```
# Qualcomm WiFi Firmware (on device)
/vendor/firmware/wlan/qca_cld/
├── wlan_mac.bin                       # MAC address file
├── WCNSS_qcom_cfg.ini                # Driver configuration (INI)
├── bdwlan.bin                         # Board data (cal, regulatory)
├── amss.bin                           # Firmware image (main FW)
└── m3.bin                             # Firmware helper

# Runtime WiFi data
/data/misc/wifi/
├── wpa_supplicant.conf                # Supplicant config
├── p2p_supplicant.conf                # P2P config
├── sockets/                           # Control sockets
├── WifiConfigStore.xml                # Saved networks (Android)
└── entropy.bin                        # Random seed

# SoftAP runtime
/data/vendor/wifi/hostapd/
├── hostapd.conf                       # AP config (runtime generated)
└── hostapd_wpa3.conf                  # WPA3 variant

# Logs
/data/vendor/wifi/wlan_logs/           # FW crash dumps
/data/misc/wifi/wpa_supplicant.log     # Supplicant log (if enabled)
/data/tombstones/                      # Native crashes

# sysfs entries
/sys/class/net/wlan0/                  # Network interface
/sys/module/wlan/parameters/           # Module parameters
  ├── fwpath                           # FW mode (sta, ap, ap,sta)
  ├── debug_mask                       # Debug level
  └── country_code                     # Regulatory
```

---

## 5. TOOLS & SOFTWARE

### Essential Tools

| Tool | Purpose | Platform |
|------|---------|----------|
| `iw` | WiFi config/monitoring (nl80211) | Linux |
| `wpa_supplicant` | Authentication daemon | Linux/Android |
| `wpa_cli` | supplicant command-line client | Linux/Android |
| `hostapd` | AP daemon | Linux/Android |
| `hostapd_cli` | AP command-line client | Linux/Android |
| `Wireshark` | Packet capture & analysis (GUI) | PC |
| `tshark` | Wireshark CLI version | PC/Linux |
| `tcpdump` | Packet capture | Linux/Android |
| `iperf3` | Throughput measurement | All |
| `aircrack-ng` | Security audit suite | Linux |
| `horst` | WiFi analyzer (terminal) | Linux |
| `wavemon` | WiFi monitor (ncurses) | Linux |

### Android-Specific

| Tool | Purpose |
|------|---------|
| `dumpsys wifi` | Full WiFi state dump |
| `cmd wifi` | WiFi shell commands |
| `logcat -s WifiService` | Framework logs |
| `logcat -s wpa_supplicant` | Supplicant logs |
| `settings get global wifi_on` | WiFi enable state |
| WiFi Analyzer (app) | Visual channel analysis |

### Development & Debug

| Tool | Purpose |
|------|---------|
| `QXDM` | Qualcomm diagnostics (FW level) |
| `QCAT` | Qualcomm log parser |
| `cnss_diag` | Qualcomm WiFi FW debug logs |
| `iwconfig` (deprecated) | Legacy wireless config |
| `rfkill` | Radio kill switch control |
| `nmcli` (NetworkManager) | High-level WiFi management |

---

## 6. BOOKS & REFERENCES

### Primary References

| Title | Author | Focus |
|-------|--------|-------|
| *802.11 Wireless Networks: The Definitive Guide* | Matthew Gast | Comprehensive 802.11 (O'Reilly) |
| *802.11ac: A Survival Guide* | Matthew Gast | WiFi 5 deep-dive |
| *802.11ax for Dummies* | David Coleman | WiFi 6 introduction |
| *Next Generation Wireless LANs* | Eldad Perahia, Robert Stacey | 802.11n/ac internals |
| *CWNA Study Guide (CWNA-109)* | David Coleman | Certification (comprehensive) |
| *CWSP Study Guide* | David Coleman | WiFi security specialization |
| *Real 802.11 Security* | Jon Edney, William Arbaugh | Security deep-dive |
| *Linux Wireless Networking* | Various | Kernel/driver implementation |

### Online Resources

| Resource | URL/Location | Content |
|----------|-------------|---------|
| Wi-Fi Alliance | wifi.org | Standards, certifications |
| IEEE GET | ieeexplore.ieee.org | Free access to published 802.11 |
| wireless.wiki.kernel.org | — | Linux WiFi documentation |
| hostap.epitest.fi | — | wpa_supplicant/hostapd official |
| Android WiFi docs | source.android.com/docs/core/connect/wifi | AOSP WiFi architecture |
| Qualcomm Developer Network | developer.qualcomm.com | QCA driver documentation |
| MRN-CCI WiFi Blog | mrn-cciew.com | CWNA/CWNP training |
| Revolution WiFi | revolutionwifi.net | WiFi capacity planning |

---

## 7. RFCs & RELATED STANDARDS

| RFC/Standard | Title | Relevance |
|-------------|-------|-----------|
| RFC 3748 | EAP (Extensible Authentication Protocol) | 802.1X auth |
| RFC 5216 | EAP-TLS | Enterprise certificate auth |
| RFC 7170 | EAP-TEAP | Modern enterprise auth |
| RFC 4764 | EAP-PSK | Pre-shared key EAP |
| RFC 7664 | Dragonfly Key Exchange | SAE (WPA3) basis |
| RFC 8110 | OWE | Enhanced Open |
| IEEE 802.1X-2020 | Port-Based Network Access | Enterprise framework |
| WPA3 Specification | WFA | Full WPA3 details |
| ETSI EN 301 893 | 5 GHz regulatory (EU) | European channels/power |
| FCC Part 15 | Unlicensed rules (US) | US regulatory |
| FCC 6 GHz Rules | AFC + indoor/outdoor | 6 GHz US regulatory |

---

## 8. SIX-WEEK STUDY PATH

### Week 1: Fundamentals
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | WiFi history, standards evolution | This encyclopedia: 01_master_theory §1-2 |
| 2 | Radio basics: frequencies, channels, bands | 01_master_theory §3-4 |
| 3 | OFDM modulation, subcarriers, QAM levels | 01_master_theory §5 |
| 4 | MIMO: spatial streams, beamforming | 01_master_theory §6 |
| 5 | BSS/ESS/IBSS topologies | 02_diagrams §2 |
| 6 | Review: Flashcards Deck 1-2 | 04_flashcards |
| 7 | Lab 1: WiFi scanning | 07_labs Lab 1 |

### Week 2: MAC & Connection
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | CSMA/CA, DCF, backoff | 01_master_theory §7 |
| 2 | EDCA, QoS, WMM | 01_master_theory §8 |
| 3 | Frame format, types, subtypes | 02_diagrams §6, 06_cheatsheet §5 |
| 4 | Connection lifecycle: scan → auth → assoc | 01_master_theory §9 |
| 5 | Aggregation: A-MSDU, A-MPDU, BA | 01_master_theory §14 |
| 6 | Review: Flashcards Deck 3 | 04_flashcards |
| 7 | Lab 2: Connection monitoring | 07_labs Lab 2 |

### Week 3: Security
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | WEP → WPA → WPA2 evolution | 01_master_theory §10 |
| 2 | 4-way handshake, key hierarchy | 02_diagrams §8, 01_master §10 |
| 3 | WPA3-Personal: SAE/Dragonfly | 01_master_theory §10 |
| 4 | WPA3-Enterprise: 802.1X, EAP-TLS | 01_master_theory §10 |
| 5 | PMF, known attacks, mitigations | 01_master_theory §10 |
| 6 | Review: Flashcards Deck 4 | 04_flashcards |
| 7 | Lab 5: Throughput testing | 07_labs Lab 5 |

### Week 4: WiFi 6/7 & Advanced
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | WiFi 6: OFDMA, RU allocation | 01_master_theory §11 |
| 2 | WiFi 6: TWT, BSS Color | 01_master_theory §11-12 |
| 3 | WiFi 7: MLO, 320 MHz, 4096-QAM | 01_master_theory §13 |
| 4 | Roaming: 802.11r/k/v | 01_master_theory §15 |
| 5 | Power management: PS-Poll, TWT | 01_master_theory §9 |
| 6 | Review: Flashcards Deck 5 | 04_flashcards |
| 7 | Lab 6: Packet capture | 07_labs Lab 6 |

### Week 5: Android & Automotive
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | Android WiFi architecture | 01_master_theory §16, 02_diagrams §14 |
| 2 | wpa_supplicant, hostapd deep-dive | Source code + 06_cheatsheet §10-11 |
| 3 | Automotive WiFi: SoftAP, OTA | 01_master_theory §17-18 |
| 4 | WiFi Direct, Android Auto Wireless | 01_master_theory §19, 02_diagrams §15 |
| 5 | Coexistence: WiFi + BT on QCA6698AQ | 01_master_theory §18 |
| 6 | Review: Flashcards Deck 6-7 | 04_flashcards |
| 7 | Lab 3: SoftAP + Lab 4: P2P | 07_labs Labs 3-4 |

### Week 6: Debug, Integration & Interview Prep
| Day | Topic | Resource |
|-----|-------|----------|
| 1 | Debug scenarios 1-5 | 07_labs Debug 1-5 |
| 2 | Debug scenarios 6-10 | 07_labs Debug 6-10 |
| 3 | Regulatory: DFS, country codes | 01_master_theory §20, Debug 6 & 9 |
| 4 | SA8295P integration review | 01_master_theory §22, 02_diagrams §18-19 |
| 5 | Interview prep: Junior + Mid | 03_interview_questions Part A-B |
| 6 | Interview prep: Senior + Staff | 03_interview_questions Part C-D |
| 7 | Full review: Cheatsheet + Quick-fire | 06_cheatsheet, 03_interview Part E |

---

## 9. GLOSSARY (Key Acronyms)

| Acronym | Expansion |
|---------|-----------|
| AC | Access Category (EDCA QoS) |
| ACK | Acknowledgment |
| AFC | Automated Frequency Coordination |
| AFH | Adaptive Frequency Hopping (BT) |
| AID | Association Identifier |
| AIFS | Arbitration Inter-Frame Space |
| A-MPDU | Aggregate MAC Protocol Data Unit |
| A-MSDU | Aggregate MAC Service Data Unit |
| AP | Access Point |
| BA | Block Acknowledgment |
| BCC | Block Convolutional Coding |
| BSS | Basic Service Set |
| BSSID | Basic Service Set Identifier |
| CAC | Channel Availability Check (DFS) |
| CCA | Clear Channel Assessment |
| CCMP | Counter Mode with CBC-MAC Protocol |
| CSMA/CA | Carrier Sense Multiple Access / Collision Avoidance |
| CW | Contention Window |
| DCF | Distributed Coordination Function |
| DFS | Dynamic Frequency Selection |
| DIFS | DCF Inter-Frame Space |
| DTIM | Delivery Traffic Indication Map |
| EAP | Extensible Authentication Protocol |
| EDCA | Enhanced Distributed Channel Access |
| EHT | Extremely High Throughput (WiFi 7) |
| eMLSR | Enhanced Multi-Link Single Radio |
| ESS | Extended Service Set |
| FCS | Frame Check Sequence |
| FT | Fast Transition (802.11r) |
| GCMP | Galois/Counter Mode Protocol |
| GI | Guard Interval |
| GO | Group Owner (P2P) |
| GTK | Group Temporal Key |
| HE | High Efficiency (WiFi 6) |
| HT | High Throughput (WiFi 4) |
| IBSS | Independent BSS (ad-hoc) |
| IGTK | Integrity Group Temporal Key |
| KCK | Key Confirmation Key |
| KEK | Key Encryption Key |
| LDPC | Low-Density Parity-Check |
| LLC | Logical Link Control |
| MCC | Multi-Channel Concurrency |
| MCS | Modulation and Coding Scheme |
| MIMO | Multiple-Input Multiple-Output |
| MLD | Multi-Link Device |
| MLO | Multi-Link Operation (WiFi 7) |
| MPDU | MAC Protocol Data Unit |
| MSDU | MAC Service Data Unit |
| MU-MIMO | Multi-User MIMO |
| NAV | Network Allocation Vector |
| NDP | Null Data Packet |
| NSTR | Non-Simultaneous TX/RX |
| OBSS-PD | Overlapping BSS Packet Detect |
| OFDM | Orthogonal Frequency Division Multiplexing |
| OFDMA | Orthogonal Frequency Division Multiple Access |
| OWE | Opportunistic Wireless Encryption |
| PMF | Protected Management Frames |
| PMK | Pairwise Master Key |
| PN | Packet Number |
| PPDU | PLCP Protocol Data Unit (PHY frame) |
| PSK | Pre-Shared Key |
| PTA | Packet Traffic Arbitration |
| PTK | Pairwise Transient Key |
| QAM | Quadrature Amplitude Modulation |
| RSN | Robust Security Network |
| RTS/CTS | Request to Send / Clear to Send |
| RU | Resource Unit (OFDMA) |
| SAE | Simultaneous Authentication of Equals |
| SAP | Software Access Point |
| SAR | Specific Absorption Rate |
| SCC | Same Channel Concurrency |
| SIFS | Short Inter-Frame Space |
| SSID | Service Set Identifier |
| SSR | SubSystem Restart |
| STA | Station |
| STR | Simultaneous Transmit and Receive |
| SU-MIMO | Single-User MIMO |
| TIM | Traffic Indication Map |
| TK | Temporal Key |
| TPC | Transmit Power Control |
| TWT | Target Wake Time |
| U-APSD | Unscheduled Automatic Power Save Delivery |
| VHT | Very High Throughput (WiFi 5) |
| WMM | Wi-Fi Multimedia |
| WPA | Wi-Fi Protected Access |
| WPS | Wi-Fi Protected Setup |

---

## 10. DOCUMENT MAP (This Encyclopedia)

| Doc | File | Content |
|-----|------|---------|
| 00 | WIFI_00_index.md | Protocol identity, terminology, study checklist |
| 01 | WIFI_01_master_theory.md | Complete theory (24 sections) |
| 02 | WIFI_02_diagrams.md | 20 visual diagrams (ASCII + Mermaid) |
| 03 | WIFI_03_interview_questions.md | 65 questions (Junior→Staff) |
| 04 | WIFI_04_flashcards.md | 250 cards in 10 decks |
| 06 | WIFI_06_cheatsheet.md | Quick-reference tables & commands |
| 07 | WIFI_07_labs_debugging.md | 7 labs + 10 debug scenarios |
| 08 | WIFI_08_references.md | This file: specs, paths, study plan |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
END OF WiFi (IEEE 802.11) ENCYCLOPEDIA — 8/8 DOCUMENTS COMPLETE
