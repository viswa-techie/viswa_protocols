# Wi-Fi (IEEE 802.11) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 04 of 08
# Format: Q (front) / A (back) — 250+ cards in 10 decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: WiFi FUNDAMENTALS (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What does WiFi stand for? | Wi-Fi is a trademark of Wi-Fi Alliance; it doesn't officially stand for anything (commonly misattributed to "Wireless Fidelity") |
| 2 | What IEEE standard defines WiFi? | IEEE 802.11 (family of amendments: a/b/g/n/ac/ax/be) |
| 3 | What 3 frequency bands does WiFi use? | 2.4 GHz (ISM), 5 GHz (U-NII), 6 GHz (added by WiFi 6E) |
| 4 | What is a BSS? | Basic Service Set — single AP + associated STAs forming one network cell |
| 5 | What is an ESS? | Extended Service Set — multiple APs sharing same SSID, connected via Distribution System (wired backbone) |
| 6 | What is IBSS? | Independent BSS — ad-hoc network with no AP; peers communicate directly |
| 7 | What is SSID? | Service Set Identifier — human-readable network name (0-32 bytes) |
| 8 | What is BSSID? | Basic Service Set Identifier — the AP's MAC address (unique per radio interface) |
| 9 | What is a STA? | Station — any WiFi client device that connects to an AP |
| 10 | What is an AP? | Access Point — device that creates a BSS, bridges wireless to wired, manages STAs |
| 11 | What is SoftAP/SAP? | Software Access Point — device acting as AP using software (e.g., phone hotspot, car head unit) |
| 12 | WiFi 4 = which standard? | 802.11n (2009) — MIMO, 40 MHz, up to 600 Mbps |
| 13 | WiFi 5 = which standard? | 802.11ac (2013) — MU-MIMO DL, 80/160 MHz, 256-QAM, up to 6.9 Gbps |
| 14 | WiFi 6 = which standard? | 802.11ax (2020) — OFDMA, TWT, 1024-QAM, BSS Color, up to 9.6 Gbps |
| 15 | WiFi 6E = which standard? | 802.11ax operating in 6 GHz band (same PHY/MAC as WiFi 6) |
| 16 | WiFi 7 = which standard? | 802.11be (2024) — MLO, 320 MHz, 4096-QAM, up to 46 Gbps |
| 17 | What is a beacon frame? | Periodic broadcast from AP (every ~100ms) with network info: SSID, capabilities, TIM, security |
| 18 | Beacon interval in TU? | Typically 100 TU = 102.4 ms (1 TU = 1024 µs) |
| 19 | What is passive scanning? | STA listens on each channel for beacons (no transmissions from STA) |
| 20 | What is active scanning? | STA sends Probe Request on each channel; APs respond with Probe Response |
| 21 | What frequency range is 2.4 GHz band? | 2400–2483.5 MHz (83.5 MHz total) |
| 22 | What frequency range is 5 GHz band? | 5150–5850 MHz (700 MHz total, with gaps) |
| 23 | What frequency range is 6 GHz band? | 5925–7125 MHz (1200 MHz total) |
| 24 | How many non-overlapping channels in 2.4 GHz? | 3 (channels 1, 6, 11 with 20 MHz width) |
| 25 | Channel bandwidth of each 2.4 GHz channel? | 22 MHz (but channel centers spaced 5 MHz apart) |
| 26 | How many 20 MHz channels in 5 GHz? | ~25 (varies by region; US: 25, some DFS restricted) |
| 27 | How many 20 MHz channels in 6 GHz? | 59 channels |
| 28 | What is the Wi-Fi Alliance? | Industry body that certifies WiFi interoperability and manages WiFi trademark |
| 29 | What is a distribution system (DS)? | The wired network connecting APs in an ESS (typically Ethernet) |
| 30 | What is AID? | Association ID — unique 16-bit number assigned to each STA by AP upon association |

---

# DECK 2: PHYSICAL LAYER (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What modulation does WiFi use since 802.11a? | OFDM (Orthogonal Frequency Division Multiplexing) |
| 2 | What is OFDM? | Divides channel into many narrow orthogonal subcarriers; each carries low-rate data → resistant to multipath |
| 3 | Subcarrier spacing in legacy OFDM (11a/g/n/ac)? | 312.5 kHz (20 MHz / 64 FFT) |
| 4 | Subcarrier spacing in HE (WiFi 6)? | 78.125 kHz (20 MHz / 256 FFT) — 4× narrower |
| 5 | Why did WiFi 6 use narrower subcarriers? | Enables OFDMA (more subcarriers = finer granularity for RU allocation to multiple users) |
| 6 | OFDM symbol duration (legacy, 0.8µs GI)? | 3.2 µs data + 0.8 µs GI = 4.0 µs total |
| 7 | HE symbol duration (WiFi 6, 0.8µs GI)? | 12.8 µs data + 0.8 µs GI = 13.6 µs total |
| 8 | What is Guard Interval (GI)? | Cyclic prefix added to prevent ISI from multipath; durations: 0.8, 0.4, 1.6, 3.2 µs |
| 9 | What is QAM? | Quadrature Amplitude Modulation — encodes bits in both amplitude and phase of signal |
| 10 | Bits per symbol: BPSK? | 1 bit/symbol |
| 11 | Bits per symbol: QPSK? | 2 bits/symbol |
| 12 | Bits per symbol: 16-QAM? | 4 bits/symbol |
| 13 | Bits per symbol: 64-QAM? | 6 bits/symbol |
| 14 | Bits per symbol: 256-QAM? | 8 bits/symbol |
| 15 | Bits per symbol: 1024-QAM? | 10 bits/symbol (WiFi 6) |
| 16 | Bits per symbol: 4096-QAM? | 12 bits/symbol (WiFi 7) |
| 17 | What is MIMO? | Multiple-Input Multiple-Output — multiple TX/RX antennas for parallel spatial streams |
| 18 | What is spatial multiplexing? | Sending different data on each antenna simultaneously (requires multipath channel) |
| 19 | What is beamforming? | Adjusting phase/amplitude of antenna signals to focus energy toward receiver |
| 20 | SU-MIMO vs MU-MIMO? | SU: all streams to one STA; MU: streams to different STAs simultaneously |
| 21 | When was DL MU-MIMO introduced? | WiFi 5 (802.11ac Wave 2) |
| 22 | When was UL MU-MIMO introduced? | WiFi 6 (802.11ax) |
| 23 | What is channel bonding? | Combining adjacent 20 MHz channels: 40/80/160/320 MHz |
| 24 | Max channel width WiFi 5? | 160 MHz (or 80+80 MHz non-contiguous) |
| 25 | Max channel width WiFi 7? | 320 MHz (6 GHz band only) |
| 26 | What is MCS? | Modulation and Coding Scheme index — defines modulation + coding rate pair |
| 27 | What is coding rate? | Ratio of data bits to total bits (e.g., 5/6 = 5 data bits per 6 transmitted) → FEC redundancy |
| 28 | BCC vs LDPC? | Block Convolutional Coding vs Low-Density Parity-Check; LDPC is more efficient at high MCS |
| 29 | What is preamble puncturing (WiFi 7)? | Transmitting on a wide channel but leaving some 20 MHz sub-channels empty (punctured) to avoid interference |
| 30 | Number of spatial streams max (WiFi 6)? | 8 streams (but typically 2 for mobile, 4 for AP) |

---

# DECK 3: MAC PROTOCOL (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What medium access does WiFi use? | CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance) |
| 2 | What is DCF? | Distributed Coordination Function — basic CSMA/CA: listen before talk + random backoff |
| 3 | What is DIFS? | DCF Inter-Frame Space — wait time before starting backoff (34 µs for OFDM) |
| 4 | What is SIFS? | Short Inter-Frame Space — shortest gap (10 µs OFDM); used for ACK, CTS, fragments |
| 5 | What is SIFS used for? | Immediate responses: ACK after DATA, CTS after RTS, next fragment in burst |
| 6 | What is the contention window (CW)? | Range for random backoff: slot drawn from [0, CW]. Doubles on collision (CWmin → CWmax) |
| 7 | Slot time (OFDM)? | 9 µs |
| 8 | What is NAV? | Network Allocation Vector — virtual carrier sense; timer set from Duration field in overheard frames |
| 9 | What is RTS/CTS? | Request to Send / Clear to Send — optional mechanism to solve hidden node problem; reserves medium |
| 10 | What is EDCA? | Enhanced Distributed Channel Access — QoS extension with 4 Access Categories (different CW/AIFS) |
| 11 | Four EDCA Access Categories? | AC_VO (voice), AC_VI (video), AC_BE (best effort), AC_BK (background) |
| 12 | Which AC has highest priority? | AC_VO (smallest CW, shortest AIFS) |
| 13 | What is AIFS? | Arbitration IFS — per-AC wait time replacing DIFS; AIFS = SIFS + AIFSN × slot_time |
| 14 | AIFSN for AC_VO? | 2 (shortest wait) |
| 15 | AIFSN for AC_BK? | 7 (longest wait) |
| 16 | What is Block Acknowledgment? | Single BA frame acknowledges multiple MPDUs (bitmap of 64 sequences); reduces ACK overhead |
| 17 | What is A-MPDU? | Aggregate MPDU — packs multiple MAC frames into one PHY frame; individually ACKable via BA |
| 18 | What is A-MSDU? | Aggregate MSDU — packs multiple Ethernet frames into one MAC frame; single FCS; no selective retry |
| 19 | Max A-MPDU subframes (WiFi 6)? | 256 MPDUs |
| 20 | What are WiFi frame types? | Management (00), Control (01), Data (10) |
| 21 | Management frame examples? | Beacon, Probe Req/Resp, Auth, Assoc Req/Resp, Deauth, Disassoc, Action |
| 22 | Control frame examples? | ACK, RTS, CTS, Block Ack, Block Ack Request, CF-End |
| 23 | Data frame examples? | Data, QoS Data, Null Data (power save), QoS Null |
| 24 | What is fragmentation threshold? | Max frame size before splitting into fragments; rarely used now (aggregation preferred) |
| 25 | What is the retry bit? | Bit in Frame Control indicating a retransmitted frame (helps receiver detect duplicates) |
| 26 | Maximum retries before dropping? | Short retry limit: 7; Long retry limit: 4 (configurable) |
| 27 | What is CCA? | Clear Channel Assessment — PHY determination of channel busy/idle (-82 dBm threshold) |
| 28 | What is OBSS-PD? | Overlapping BSS Packet Detect — WiFi 6 adaptive CCA using BSS Color (threshold adjustable) |
| 29 | What is a Trigger Frame? | WiFi 6 AP frame that schedules uplink OFDMA/MU-MIMO transmission from multiple STAs |
| 30 | What is Multi-TID aggregation? | Aggregating frames from different Traffic IDs (applications) in same A-MPDU |

---

# DECK 4: SECURITY (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | WiFi security evolution (4 generations)? | WEP → WPA → WPA2 → WPA3 |
| 2 | Why is WEP broken? | Static 40/104-bit RC4 key + 24-bit IV reuse → key recovery in minutes (FMS, PTW attacks) |
| 3 | WPA encryption algorithm? | TKIP (RC4 with per-packet key mixing + MIC) — transitional, deprecated |
| 4 | WPA2 encryption algorithm? | AES-CCMP (Counter Mode + CBC-MAC, 128-bit key) |
| 5 | WPA3-Personal key exchange? | SAE (Simultaneous Authentication of Equals) — Dragonfly handshake |
| 6 | Why is SAE better than PSK? | Resistant to offline dictionary attacks; provides forward secrecy |
| 7 | WPA3-Enterprise encryption? | GCMP-256 (Galois/Counter Mode, 256-bit) — Suite-B 192-bit security |
| 8 | What is the 4-way handshake? | Key exchange after association: derives PTK from PMK + nonces; installs encryption keys |
| 9 | What is PMK? | Pairwise Master Key — derived from passphrase (PSK: PBKDF2) or 802.1X (MSK) |
| 10 | What is PTK? | Pairwise Transient Key — session key derived during 4-way handshake; used for unicast |
| 11 | PTK components? | KCK (Key Confirmation Key) + KEK (Key Encryption Key) + TK (Temporal Key) |
| 12 | What is GTK? | Group Temporal Key — shared key for multicast/broadcast; rotated periodically |
| 13 | What is IGTK? | Integrity Group Temporal Key — protects broadcast management frames (PMF) |
| 14 | What is PMF (802.11w)? | Protected Management Frames — encrypts/authenticates management frames; mandatory in WPA3 |
| 15 | What is 802.1X? | Port-based network access control — EAP framework for enterprise authentication |
| 16 | EAP-TLS? | EAP with mutual certificate authentication (client + server certs); strongest enterprise method |
| 17 | EAP-PEAP? | Protected EAP — server cert + tunneled MSCHAPv2 password; most common enterprise method |
| 18 | What is the KRACK attack? | Key Reinstallation Attack (2017) — forces nonce reuse in 4-way handshake; patched in all modern devices |
| 19 | What is OWE? | Opportunistic Wireless Encryption — DH key exchange on open networks; no password needed |
| 20 | What attack does PMF prevent? | Deauthentication/disassociation flooding (denial of service) |
| 21 | What is Dragonblood? | Side-channel attacks against SAE (2019); mitigated with implementation fixes |
| 22 | What is PN (Packet Number)? | 48-bit counter in CCMP/GCMP header; prevents replay attacks (must be monotonically increasing) |
| 23 | What is key hierarchy in WiFi? | MSK → PMK → PTK (KCK+KEK+TK) for unicast; PMK → GMK → GTK for multicast |
| 24 | WPS vulnerability? | Online brute-force of 8-digit PIN (split into 4+3 digits) → compromised in hours |
| 25 | Forward secrecy in WiFi? | WPA3 SAE provides it: each session uses unique ephemeral DH key pair |

---

# DECK 5: WiFi 6/6E/7 FEATURES (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What is OFDMA? | Orthogonal Frequency Division Multiple Access — divides channel into RUs, each allocated to a different user |
| 2 | Smallest RU size (WiFi 6)? | 26-tone RU (~2 MHz) |
| 3 | Largest RU in 20 MHz? | 242-tone (full 20 MHz channel) |
| 4 | Max users in 20 MHz OFDMA? | 9 users (9 × 26-tone RUs) |
| 5 | What is a Trigger Frame? | AP frame scheduling uplink OFDMA: tells STAs which RU to use, MCS, power target |
| 6 | What is TWT? | Target Wake Time — negotiated schedule: STA and AP agree on exact wake/sleep times |
| 7 | TWT benefit? | Extreme power savings (sleep 99%+) + reduced contention (fewer simultaneous STAs) |
| 8 | What is BSS Color? | 6-bit ID in HE-SIG-A; distinguishes own BSS from neighboring BSS for spatial reuse |
| 9 | How does BSS Color help? | Inter-BSS frames (different color) use higher CCA threshold → more concurrent transmissions |
| 10 | WiFi 6E: what's new vs WiFi 6? | Same PHY/MAC but operates in 6 GHz band (1200 MHz more spectrum, no legacy, no DFS) |
| 11 | What is MLO (WiFi 7)? | Multi-Link Operation — single connection spanning 2+ bands simultaneously |
| 12 | MLO benefit: throughput? | Aggregate bandwidth across links (e.g., 5 GHz + 6 GHz combined) |
| 13 | MLO benefit: latency? | If one link busy, packet sent on other link immediately → lower worst-case latency |
| 14 | MLO benefit: reliability? | Link failure → traffic shifts to surviving link seamlessly |
| 15 | What is STR? | Simultaneous Transmit and Receive — can TX on one link while RX on another (requires isolation) |
| 16 | What is NSTR? | Non-STR — cannot TX/RX simultaneously across links (interference between own radios) |
| 17 | What is eMLSR? | Enhanced Multi-Link Single Radio — listens on both links, TX on one; switch between links per frame |
| 18 | 320 MHz channels: which band? | 6 GHz only (needs 320 contiguous MHz) |
| 19 | 4096-QAM: bits per symbol? | 12 bits/symbol (vs 10 for 1024-QAM) → 20% throughput increase at close range |
| 20 | What is preamble puncturing? | Use wide channel (e.g., 160 MHz) but skip 20 MHz sub-channels with interference |
| 21 | Multi-RU (WiFi 7)? | Allocate multiple non-contiguous RUs to single STA → more flexible scheduling |
| 22 | WiFi 6 max throughput (theoretical)? | 9.6 Gbps (8 SS × 160 MHz × 1024-QAM × 5/6 coding) |
| 23 | WiFi 7 max throughput (theoretical)? | 46 Gbps (16 SS × 320 MHz × 4096-QAM × 5/6 coding) |
| 24 | What is AFC? | Automated Frequency Coordination — database that controls 6 GHz outdoor usage (replace DFS) |
| 25 | WiFi 6 uplink improvements? | UL OFDMA + UL MU-MIMO + Trigger-based PPDU (AP-coordinated uplink) |

---

# DECK 6: ANDROID & LINUX STACK (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Android WiFi service class? | WifiService (SystemServer) → delegates to WifiServiceImpl |
| 2 | Main STA state machine class? | ClientModeImpl (replaced WifiStateMachine in Android 12+) |
| 3 | What manages SoftAP in Android? | SoftApManager (in WifiService) → controls hostapd |
| 4 | What is wpa_supplicant's role? | Handles WPA authentication, key management, roaming, network selection; talks to kernel via nl80211 |
| 5 | What is hostapd? | Daemon that implements AP functionality: beacon, authentication, key management for SoftAP |
| 6 | Linux WiFi subsystem in kernel? | cfg80211 (configuration) + mac80211 (software MAC) + driver |
| 7 | What is nl80211? | Netlink-based interface between userspace (wpa_supplicant) and cfg80211 kernel subsystem |
| 8 | What is cfg80211? | Kernel WiFi configuration framework; provides API for scan, connect, AP mode, etc. |
| 9 | What is mac80211? | Kernel software MAC implementation; handles frame TX/RX, aggregation, rate control for SoftMAC drivers |
| 10 | Full-MAC vs Soft-MAC? | Full-MAC: firmware handles MAC (e.g., Qualcomm qcacld); Soft-MAC: kernel mac80211 handles MAC |
| 11 | Qualcomm automotive WiFi driver? | qcacld-3.0 (full-MAC; firmware handles MLME, scan, roaming) |
| 12 | WiFi HAL interface (Android 13+)? | AIDL: IWifi, IWifiChip, IWifiStaIface, IWifiApIface |
| 13 | Command to scan WiFi (Linux)? | `iw dev wlan0 scan` |
| 14 | Command to check connection? | `iw dev wlan0 link` (shows SSID, freq, signal, TX rate) |
| 15 | wpa_supplicant config file? | /data/misc/wifi/wpa_supplicant.conf (Android) or /etc/wpa_supplicant/wpa_supplicant.conf (Linux) |
| 16 | Android WiFi logs: key logcat tag? | `WifiService`, `WifiNative`, `wpa_supplicant`, `WifiConnectivityManager` |
| 17 | What is WifiNative? | Android framework class bridging Java WiFi service to native daemons (supplicant/HAL) |
| 18 | What is WifiConnectivityManager? | Auto-connect logic: periodic scan, evaluate networks, trigger connection |
| 19 | What is WifiScanner? | Hardware-offloaded scanning (PNO, batch scan, hotlist) via HAL |
| 20 | P2P interface name in Android? | `p2p-wlan0-0` or `p2p0` (varies by platform) |
| 21 | SoftAP interface name? | `wlan1` (typically; configured in HAL) |
| 22 | Android WiFi properties (system)? | `wifi.interface=wlan0`, `ro.hardware.wifi=<driver_name>` |
| 23 | What is WifiTracker? | UI component tracking network list for Settings; queries WifiManager for scan results |
| 24 | Country code source in Android? | Telephony MCC, user setting, or driver default; set via `iw reg set XX` or HAL |
| 25 | FW path sysfs (Qualcomm)? | `/sys/module/wlan/parameters/fwpath` (switch FW mode: STA/AP/P2P) |

---

# DECK 7: AUTOMOTIVE WiFi (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | WiFi chip in SA8295P platform? | QCA6698AQ (WiFi 6E 2×2 + BT 5.2, automotive grade) |
| 2 | QCA6698AQ interface to SoC? | PCIe Gen3 x1 |
| 3 | Automotive temp range for QCA6698AQ? | -40°C to +105°C (AEC-Q100 qualified) |
| 4 | Primary WiFi use in car? | Passenger hotspot (SoftAP providing internet via cellular backhaul) |
| 5 | What band for automotive SoftAP? | 5 GHz (Ch 36-48 preferred: no DFS, no BT conflict) |
| 6 | Why avoid DFS channels for car hotspot? | 60s CAC delay at startup + must vacate on radar → disrupts passengers |
| 7 | SoftAP internet source in car? | Cellular TCU (rmnet0) → NAT → SoftAP (wlan1) |
| 8 | Android Auto Wireless uses what? | WiFi Direct (P2P); Head unit = GO, Phone = Client |
| 9 | Car hotspot max clients (typical)? | 8-10 (limited by DHCP range and airtime) |
| 10 | WiFi + BT coexistence strategy? | Band separation: WiFi 5/6 GHz, BT 2.4 GHz; internal PTA for fallback |
| 11 | What is PTA? | Packet Traffic Arbitration — hardware arbiter that grants medium to WiFi or BT per slot |
| 12 | What is AFH for BT coexistence? | Adaptive Frequency Hopping — BT avoids channels overlapping with WiFi's 2.4 GHz |
| 13 | WiFi power after ignition off? | Stay active 30 min (configurable) for OTA downloads; then power down |
| 14 | OTA download WiFi strategy? | Auto-connect to known networks; HTTP range resume; chunk verification; cellular fallback |
| 15 | WiFi antenna location in car? | Shark-fin (roof), side mirrors, or embedded in rear window |
| 16 | Concurrent modes in automotive? | STA + SAP + P2P (all on single radio via SCC/MCC) |
| 17 | Preferred: SCC or MCC? | SCC (Same Channel Concurrency) — no time-sharing penalty |
| 18 | MCC penalty? | ~50% throughput per interface (radio alternates between channels) |
| 19 | Regulatory compliance in multi-region vehicle? | Runtime country code from GNSS/cellular; affects channels, TX power, DFS |
| 20 | SAR (Specific Absorption Rate) in car? | TX power reduced near occupants; antenna placement critical for SAR compliance |
| 21 | WiFi for V2X? | 802.11p (DSRC) at 5.9 GHz — being replaced by C-V2X in most regions |
| 22 | WiFi for diagnostics? | SoftAP for workshop tool connection (no physical cable needed) |
| 23 | Phone-as-Key WiFi role? | WiFi for fast wake + BLE ranging; or UWB for precise positioning |
| 24 | Fleet WiFi management? | Enterprise WPA3 + MDM-pushed certificates; centralized policy server |
| 25 | WiFi after sleep (car wakes)? | Fast reconnect: cached PMK → skip full auth → only 4-way handshake (~100ms) |

---

# DECK 8: WiFi DIRECT & P2P (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What is WiFi Direct? | Peer-to-peer WiFi: devices connect without infrastructure AP; one device becomes GO |
| 2 | What is GO? | Group Owner — device acting as AP in P2P group; runs DHCP, manages clients |
| 3 | How is GO selected? | GO Negotiation: both devices declare intent (0-15); higher intent wins; tie-breaker bit resolves ties |
| 4 | What intent does head unit use? | Intent 15 (forces head unit to be GO for predictable config) |
| 5 | P2P discovery mechanism? | Social channels (1, 6, 11 on 2.4 GHz) + device sends Probe with P2P IE |
| 6 | What is WPS in P2P? | Wi-Fi Protected Setup — exchange credentials (PBC or PIN) after GO negotiation |
| 7 | P2P DHCP range? | Typically 192.168.49.x/24 (GO assigns, hardcoded in some implementations) |
| 8 | P2P security? | WPA2/WPA3 with passphrase derived during WPS exchange |
| 9 | Persistent P2P group? | Group credentials saved; reconnection skips discovery + GO negotiation + WPS |
| 10 | P2P + STA concurrent? | Yes (SCC preferred); P2P client on same channel as STA connection |
| 11 | What is Miracast? | WiFi Display protocol over P2P: H.264 video stream + audio + input backchannel |
| 12 | Miracast port? | RTSP on TCP/7236 for session; RTP/UDP for media stream |
| 13 | WiFi Aware (NAN)? | Neighbor Awareness Networking — discovery without AP; precedes WiFi Direct data path |
| 14 | P2P Invitation Procedure? | GO invites known client to reconnect to persistent group (skips negotiation) |
| 15 | P2P concurrent with SoftAP? | Possible but complex; uses same or different interface; chip dependent |
| 16 | Android Auto P2P flow? | BT initial exchange → WFD info → P2P connect → TCP/5277 for AA protocol |
| 17 | P2P group frequency? | Negotiated in GO Negotiation; can be 2.4 or 5 GHz; social channels preferred for discovery |
| 18 | What is NOA? | Notice of Absence — GO announces absence periods (P2P power save) |
| 19 | What is Opportunistic PS? | GO sleeps between beacons if no clients have traffic; uses CT Window |
| 20 | P2P service discovery? | Bonjour/UPnP over P2P action frames (before connection) — discover services pre-connect |

---

# DECK 9: POWER MANAGEMENT (20 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | WiFi power states? | Active (TX/RX), Doze (sleeping, radio off), Idle (listening for beacons) |
| 2 | What is PS-Poll? | Power Save Poll — frame sent by STA to AP requesting buffered data delivery |
| 3 | How does STA know AP has buffered data? | TIM (Traffic Indication Map) in beacon; STA's AID bit set = data waiting |
| 4 | What is U-APSD? | Unscheduled Automatic Power Save Delivery — STA triggers delivery with uplink frame (no beacon wait) |
| 5 | U-APSD advantage over PS-Poll? | Lower latency (trigger anytime, not just after beacon); better for VoIP |
| 6 | What is S-APSD? | Scheduled APSD — AP delivers at fixed intervals (scheduled service period) |
| 7 | What is DTIM? | Delivery TIM — beacon after which AP sends buffered multicast/broadcast |
| 8 | DTIM period effect on power? | Higher DTIM period → STA can skip more beacons → better power savings |
| 9 | What is TWT? | Target Wake Time — WiFi 6 negotiated schedule for exact wake/sleep timing |
| 10 | TWT individual vs broadcast? | Individual: AP-STA negotiate; Broadcast: AP announces schedule for all |
| 11 | TWT power saving %? | Up to 99%+ sleep time for IoT devices with infrequent data |
| 12 | WiFi power consumption (typical)? | TX: 800-1500 mW, RX: 400-800 mW, Idle (listening): 100-200 mW, Sleep: <5 mW |
| 13 | Android Doze effect on WiFi? | WiFi scan frequency reduced; batch WiFi work; no constant connectivity |
| 14 | Keep-alive interval for connected car? | 15-30 min (longer in sleep); prevents NAT timeout while saving power |
| 15 | WiFi power in automotive (ignition on)? | Always active (power not a concern — 12V supply) |
| 16 | WiFi power in automotive (ignition off)? | Time-limited: 30 min for OTA then shutdown; RTC alarm for scheduled wake |
| 17 | Wake-on-WLAN? | STA in deep sleep; FW monitors for specific patterns → wakes host on match |
| 18 | Scan power optimization? | PNO (Preferred Network Offload): FW scans periodically; host sleeps until network found |
| 19 | 802.11v sleep mode? | Extended sleep: AP buffers more aggressively; STA announces longer absence |
| 20 | MIMO power save? | SMPS (Spatial Multiplexing Power Save): operate with fewer antennas when traffic low |

---

# DECK 10: TOOLS & DEBUGGING (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Command to list WiFi interfaces? | `iw dev` |
| 2 | Command to show WiFi link status? | `iw dev wlan0 link` |
| 3 | Command to show associated stations? | `iw dev wlan0 station dump` |
| 4 | Command to trigger WiFi scan? | `iw dev wlan0 scan trigger` then `iw dev wlan0 scan dump` |
| 5 | Command to set regulatory domain? | `iw reg set US` |
| 6 | Command to check regulatory? | `iw reg get` |
| 7 | Show WiFi statistics? | `iw dev wlan0 survey dump` (channel busy/noise) |
| 8 | wpa_cli status command? | `wpa_cli -i wlan0 status` (shows SSID, freq, key_mgmt, ip) |
| 9 | wpa_cli scan + results? | `wpa_cli scan` then `wpa_cli scan_results` |
| 10 | Connect with wpa_cli? | `wpa_cli add_network`, `set_network 0 ssid "name"`, `set_network 0 psk "pass"`, `select_network 0` |
| 11 | Wireshark WiFi filter: beacon? | `wlan.fc.type_subtype == 0x08` |
| 12 | Wireshark: probe request? | `wlan.fc.type_subtype == 0x04` |
| 13 | Wireshark: deauth? | `wlan.fc.type_subtype == 0x0c` |
| 14 | Wireshark: EAPOL (4-way)? | `eapol` |
| 15 | Enable monitor mode? | `iw dev wlan0 set type monitor` (after `ip link set wlan0 down`) |
| 16 | Android WiFi bug report section? | `dumpsys wifi` (in bugreport or via adb) |
| 17 | hostapd config file location? | `/data/vendor/wifi/hostapd/hostapd.conf` (Android) |
| 18 | Check FW version (Qualcomm)? | `cat /sys/module/wlan/parameters/fwpath` or `dmesg | grep -i "fw version"` |
| 19 | tcpdump on WiFi (Android)? | `tcpdump -i wlan0 -w /sdcard/capture.pcap` |
| 20 | iperf3 WiFi throughput test? | `iperf3 -c <server_ip> -t 30 -i 1 -P 4` (4 parallel streams, 30 sec) |
| 21 | Android WiFi score meaning? | 0-60: poor connectivity; used by ConnectivityService for network selection |
| 22 | Check if MCC active? | Compare frequencies: `iw dev wlan0 info` vs `iw dev wlan1 info`; different = MCC |
| 23 | Driver debug logs (Qualcomm)? | `echo 0x1f > /sys/module/wlan/parameters/debug_mask` |
| 24 | Key dmesg patterns for WiFi? | `dmesg | grep -iE "wlan|qca|cfg80211|ieee80211|disconnect|assoc"` |
| 25 | Android connectivity diagnostics? | `adb shell cmd connectivity connectivity-diagnostics` + `dumpsys connectivity` |

---

END OF DOCUMENT 04 — FLASHCARDS (250 cards across 10 decks)
