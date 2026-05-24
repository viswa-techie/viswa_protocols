# Wi-Fi (IEEE 802.11) — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (15 Questions)

---

### Q1. What is WiFi and what frequencies does it use?
**A:** WiFi is a wireless LAN technology based on IEEE 802.11 standards. It provides high-speed local connectivity over unlicensed spectrum:
- **2.4 GHz**: 14 channels (11 in US), better range, more crowded
- **5 GHz**: 25+ channels, faster, shorter range
- **6 GHz** (WiFi 6E/7): 59 new channels, cleanest spectrum, shortest range

---

### Q2. What is the difference between an AP and a STA?
**A:**
- **AP (Access Point)**: Infrastructure device that creates a BSS (network). Broadcasts beacons, manages associations, bridges wireless↔wired.
- **STA (Station)**: Client device that connects to an AP (phone, laptop, car head unit in STA mode).
- A device can be both: car runs SAP (SoftAP = acts as AP for passengers) while simultaneously being STA (connected to home WiFi).

---

### Q3. What are SSID and BSSID?
**A:**
- **SSID** (Service Set Identifier): Human-readable network name (0-32 bytes). Example: "MyHome_5G"
- **BSSID** (Basic Service Set Identifier): MAC address of the AP's radio interface. Unique per AP/band.
- Multiple APs can share same SSID (ESS for roaming) but each has unique BSSID.

---

### Q4. Why does WiFi use CSMA/CA instead of CSMA/CD?
**A:** In wired Ethernet (CSMA/CD), devices can detect collisions while transmitting. In wireless:
- A station **cannot hear while transmitting** (its own TX overwhelms RX)
- **Hidden node problem**: Two STAs can't hear each other but both reach AP
- Solution: **Avoid** collisions proactively: listen first, use random backoff, ACK every frame
- If no ACK received → assume collision → exponential backoff → retry

---

### Q5. What is WPA2 and how is it different from WPA3?
**A:**
- **WPA2** (2004): Uses AES-CCMP encryption, 4-way handshake, PSK or Enterprise. Still widely used.
- **WPA3** (2018): Uses SAE (Dragonfly) instead of simple PSK exchange. Benefits:
  - Resistant to offline dictionary attacks (can't brute-force captured handshake)
  - Forward secrecy (past sessions safe even if password leaked)
  - PMF mandatory (can't be deauthenticated by attacker)
  - 192-bit Enterprise mode (stronger crypto)

---

### Q6. What is a beacon frame?
**A:** Periodic broadcast from AP (typically every 102.4ms = 100 TU). Contains:
- SSID, supported rates, channel, timestamp
- Security capabilities (RSN IE)
- HT/VHT/HE capabilities
- TIM (Traffic Indication Map — shows which sleeping STAs have buffered data)
- Country, power constraint info

STAs use beacons to discover networks (passive scanning) and maintain synchronization.

---

### Q7. What are the WiFi generations (Wi-Fi 4, 5, 6, 7)?
**A:**
| Name | Standard | Key Feature | Speed |
|------|----------|------------|-------|
| Wi-Fi 4 | 802.11n | MIMO, 40 MHz | 600 Mbps |
| Wi-Fi 5 | 802.11ac | MU-MIMO DL, 160 MHz, 256-QAM | 6.9 Gbps |
| Wi-Fi 6 | 802.11ax | OFDMA, TWT, 1024-QAM, BSS Color | 9.6 Gbps |
| Wi-Fi 7 | 802.11be | MLO, 320 MHz, 4096-QAM | 46 Gbps |

---

### Q8. What is channel bonding?
**A:** Combining adjacent 20 MHz channels to increase bandwidth:
- 40 MHz = 2× channels (Wi-Fi 4+)
- 80 MHz = 4× channels (Wi-Fi 5+)
- 160 MHz = 8× channels (Wi-Fi 5+)
- 320 MHz = 16× channels (Wi-Fi 7, 6 GHz only)
Wider channel → more subcarriers → higher data rate, but also higher noise floor and fewer available channels.

---

### Q9. What is MIMO?
**A:** Multiple-Input Multiple-Output — using multiple antennas at transmitter and receiver:
- **Spatial Multiplexing**: Send independent data streams simultaneously (2×2 = 2 streams)
- **Beamforming**: Focus energy toward receiver (improve SNR)
- **Diversity**: Multiple antennas improve reliability (combine signals)
- Notation "2×2" = 2 TX antennas × 2 RX antennas = max 2 spatial streams

---

### Q10. What is the hidden node problem?
**A:** STA-A and STA-B can both reach AP but cannot hear each other. Both may transmit simultaneously → collision at AP. Neither detects the collision.
- **Solution**: RTS/CTS — STA sends Request to Send, AP replies Clear to Send (heard by all). Others set NAV timer and defer.

---

### Q11. What happens when you connect to a WiFi network?
**A:** Five-step process:
1. **Scanning**: Discover APs (passive/active)
2. **Authentication**: Open auth or SAE (WPA3)
3. **Association**: Exchange capabilities, get AID
4. **4-Way Handshake**: Derive encryption keys (PTK/GTK)
5. **DHCP**: Obtain IP address
After these steps, data can flow encrypted.

---

### Q12. What are the non-overlapping channels in 2.4 GHz?
**A:** Channels 1, 6, and 11 (in US/most regions). Each channel is 22 MHz wide with only 5 MHz spacing between channel centers. Only 1, 6, 11 have enough separation (25 MHz) to avoid interference with each other. Using overlapping channels (e.g., 1 and 3) is worse than co-channel (both on 1).

---

### Q13. What is DFS?
**A:** Dynamic Frequency Selection — required on 5 GHz channels that overlap with radar (aviation, weather). Before using a DFS channel:
1. AP must listen 60 seconds (CAC — Channel Availability Check)
2. If no radar → channel available
3. If radar detected during operation → must vacate within 10 seconds
4. Move to new channel → may need another 60s CAC
Impact: DFS channels have startup delay, not ideal for automotive SoftAP.

---

### Q14. What is Wi-Fi Direct?
**A:** Peer-to-peer WiFi without needing an AP. One device becomes **Group Owner (GO)** — acts as AP. The other becomes **Client**. Used for:
- Android Auto Wireless (head unit = GO, phone = client)
- Miracast (screen mirroring)
- File sharing (WiFi Direct transfer)
- Printing

---

### Q15. What is QoS/WMM in WiFi?
**A:** Wi-Fi Multimedia (WMM) provides traffic prioritization via 4 Access Categories:
- **AC_VO** (Voice): Highest priority, shortest delays (VoIP)
- **AC_VI** (Video): High priority (streaming, video call)
- **AC_BE** (Best Effort): Default (web, email)
- **AC_BK** (Background): Lowest priority (bulk download, backup)
Higher priority = smaller contention window = gets channel faster.

---

# PART B: MID-LEVEL (15 Questions)

---

### Q16. Explain the 4-way handshake in detail.
**A:**
```
Pre-condition: PMK known to both (from passphrase or 802.1X)

Message 1 (AP → STA): ANonce (random)
  → STA now has: PMK + ANonce + SNonce(generated) + AA + SA
  → STA computes: PTK = PRF-X(PMK, "Pairwise key expansion",
                   Min(AA,SA) || Max(AA,SA) || Min(ANonce,SNonce) || Max(ANonce,SNonce))
  → PTK yields: KCK(16B) + KEK(16B) + TK(16B) [+ more for TKIP]

Message 2 (STA → AP): SNonce + MIC(KCK) + RSN IE
  → AP computes PTK (same inputs) → verifies MIC → STA authentic

Message 3 (AP → STA): ANonce + MIC + GTK(encrypted with KEK) + RSN IE
  → STA verifies MIC → AP authentic → installs PTK + GTK

Message 4 (STA → AP): MIC (acknowledgment)
  → AP installs PTK
  → Encrypted communication begins
```
Key point: Neither side ever transmits the PMK or passphrase. Mutual authentication via MIC verification.

---

### Q17. Describe OFDMA and how it differs from OFDM.
**A:**
- **OFDM** (pre-WiFi 6): Entire channel (all subcarriers) allocated to ONE user at a time. Even for small packets (e.g., 50-byte ACK), entire 80 MHz used → wasteful.
- **OFDMA** (WiFi 6+): Channel divided into Resource Units (RUs). Multiple users transmit simultaneously on different RUs within same OFDM symbol.

Example (20 MHz): 9 IoT devices each get a 26-tone RU → all served in ONE transmission opportunity (vs 9 separate transmissions in legacy OFDM).

AP is the scheduler: assigns RUs via Trigger Frames (uplink) or directly (downlink).

---

### Q18. How does WiFi power management work? Compare PS-Poll, U-APSD, and TWT.
**A:**
| Method | Mechanism | Latency | Power Savings | Best For |
|--------|-----------|---------|---------------|----------|
| PS-Poll | Wake for beacon TIM, poll buffered | High (up to beacon interval) | Moderate | Legacy devices |
| U-APSD | STA triggers delivery with uplink frame | Low (immediate response) | Good | VoIP, interactive |
| TWT | Negotiated schedule (exact wake times) | Predictable | Excellent | IoT, sensors |

TWT is revolutionary: device can sleep 99%+ of time, wake only at pre-agreed instants. AP doesn't even buffer — both sides know the schedule.

---

### Q19. Explain 802.11r/k/v fast roaming.
**A:**
- **802.11k (RRM)**: AP tells STA about neighbor APs (BSSID, channel, PHY). STA doesn't need to scan all channels → faster roaming decision.
- **802.11v (WNM)**: AP can recommend specific target AP ("BSS Transition Management"). AP-assisted roaming (load balancing, quality).
- **802.11r (FT)**: Pre-derive encryption keys with target AP before reassociating. Eliminates full 4-way handshake during roam. Result: <50ms roam (vs >200ms without FT).

All three work together: k provides candidates, v recommends best target, r makes the transition fast.

---

### Q20. What is BSS Coloring and why does WiFi 6 need it?
**A:** In dense deployments (apartments, stadiums), STAs detect signals from neighboring BSSs and defer (CCA = Clear Channel Assessment). This over-caution reduces throughput.

**BSS Color**: 6-bit field in HE-SIG-A (PHY header):
- Same color (intra-BSS) → obey CCA, defer normally
- Different color (inter-BSS/OBSS) → apply higher threshold (e.g., -82 dBm → -62 dBm), can transmit simultaneously
- Effectively increases spatial reuse in dense environments
- AP assigns colors; should avoid neighbors using same color

---

### Q21. Describe WiFi frame aggregation (A-MSDU vs A-MPDU).
**A:**
- **A-MSDU**: Pack multiple Ethernet frames (MSDUs) into ONE MAC frame (MPDU). Single MAC header + FCS. If ANY sub-frame corrupted → entire A-MSDU retransmitted. Max: 7935 bytes.
- **A-MPDU**: Pack multiple MAC frames (MPDUs) into ONE PHY frame. Each MPDU has own header + FCS. Individually ACKable via Block Ack bitmap. Max: 256 MPDUs (WiFi 5/6). Can combine: A-MSDU inside A-MPDU.

A-MPDU is dominant in modern WiFi (selective retransmission).

---

### Q22. How does Android manage WiFi concurrency (STA + SAP + P2P)?
**A:**
Android WiFi uses virtual interfaces on single radio:
- `wlan0` — STA interface (connect to AP)
- `wlan1` — SoftAP interface (hotspot)
- `p2p0` — P2P/WiFi Direct interface

**Concurrency modes:**
1. **SCC** (Same Channel): All on same channel → best performance
2. **MCC** (Multi-Channel): Different channels → radio time-shares (~50% each)
3. **DBS** (Dual Band Simultaneous): Two radios → true simultaneous (premium chips)

Android's WifiNative/HAL negotiates concurrency. Strategy: try SCC first (force SAP to STA's channel). MCC as fallback.

---

### Q23. Explain the WiFi Direct group formation for Android Auto Wireless.
**A:**
1. Phone discovers head unit via BT (initial handshake over BT with IP/port info)
2. Head unit starts P2P Group (forces GO role, intent=15)
3. Phone initiates P2P connection as Client
4. WPS exchange (PBC or PIN for first time, cached for subsequent)
5. 4-way handshake → encrypted link
6. DHCP: GO assigns IP (192.168.49.x range)
7. Android Auto TCP connection established over P2P link
8. H.264 video + audio + input streams flow

Head unit always GO → predictable IP/DHCP setup → simpler firewall/NAT.

---

### Q24. What is the difference between 2.4 GHz and 5 GHz for automotive use?
**A:**
| Aspect | 2.4 GHz | 5 GHz |
|--------|---------|-------|
| Range | Better (penetrates obstacles) | Shorter |
| Interference | Heavy (BT, microwave, neighbors) | Less |
| Channels | 3 non-overlapping | 25 non-overlapping |
| BT coexistence | Problematic (same band!) | No conflict |
| Automotive preference | Avoid for hotspot | Preferred for SAP/P2P |
| Use case | Fallback, legacy devices | Primary: hotspot, projection |

**Automotive strategy**: WiFi on 5/6 GHz, BT on 2.4 GHz → zero coexistence issues.

---

### Q25. How does wpa_supplicant work?
**A:** `wpa_supplicant` is the Linux/Android userspace daemon that handles:
- WPA/WPA2/WPA3 authentication (4-way handshake, SAE)
- 802.1X/EAP (enterprise networks)
- Network selection (scan results → evaluate → connect)
- Key management (install PTK/GTK to driver)
- Roaming decisions (RSSI threshold → reassociate)
- P2P/Wi-Fi Direct management

**Interface**: Communicates with apps via control socket (wpa_cli) or in Android via AIDL HAL (ISupplicant). Communicates with kernel via nl80211 (netlink messages to cfg80211/driver).

---

### Q26. What is MCS index and how does it map to data rate?
**A:** MCS (Modulation and Coding Scheme) is an index that defines:
- Modulation type (BPSK → 4096-QAM)
- Coding rate (1/2, 2/3, 3/4, 5/6)
- Combined → determines bits per symbol

Example (WiFi 6, 80 MHz, 2 spatial streams, 0.8µs GI):
| MCS | Modulation | Coding | Rate (Mbps) |
|-----|-----------|--------|-------------|
| 0 | BPSK | 1/2 | 58.5 |
| 4 | 16-QAM | 3/4 | 351 |
| 7 | 64-QAM | 5/6 | 585 |
| 9 | 256-QAM | 5/6 | 780 |
| 11 | 1024-QAM | 5/6 | 975 |

Higher MCS = faster but requires better signal quality.

---

### Q27. Describe the Protected Management Frames (PMF / 802.11w).
**A:** Without PMF, management frames (deauth, disassoc) are unprotected. Attacker can spoof deauth → force disconnection (DoS).

**PMF** (mandatory in WPA3):
- Uses IGTK (Integrity Group Temporal Key) to protect broadcast management frames
- Uses PTK to protect unicast management frames (deauth, disassoc, action)
- Attacker can't forge valid management frames
- Prevents: Deauthentication attacks, forced roaming, BSS manipulation

---

### Q28. What is rate adaptation and how does it work?
**A:** WiFi dynamically adjusts MCS/PHY rate based on channel conditions:
- High RSSI, low noise → highest MCS (1024-QAM, 5/6 coding)
- Degrading conditions → lower MCS (more robust modulation, more coding)
- Algorithms: Minstrel (Linux), vendor proprietary (Qualcomm RA)

**Inputs**: Frame success/failure rate, RSSI, retry count, SNR estimate
**Output**: Selected MCS for next transmission

**Typical behavior**:
- Near AP: MCS 11 (1024-QAM) → max speed
- Medium distance: MCS 7 (64-QAM) → balanced
- Edge of range: MCS 0 (BPSK) → maximum robustness

---

### Q29. Explain the TIM and DTIM in WiFi beacons.
**A:**
- **TIM (Traffic Indication Map)**: Bitmap in every beacon showing which associated STAs have buffered frames at AP. STA checks TIM → if its AID bit set → sends PS-Poll to retrieve data.
- **DTIM (Delivery TIM)**: Special beacon at interval DTIM_period. After DTIM, AP delivers all buffered multicast/broadcast frames. STAs wanting broadcast must wake for DTIM beacons.
- **DTIM period**: 1-255 beacon intervals. Higher = more power savings but multicast delayed.

---

### Q30. What is OWE (Opportunistic Wireless Encryption)?
**A:** "Enhanced Open" — encryption for open networks without passwords:
- Uses Diffie-Hellman key exchange during association
- No user credentials needed (no password prompt)
- Each STA gets unique encryption key
- Protects against passive eavesdropping
- Does NOT authenticate AP (no MITM protection without server cert)
- Use case: Public WiFi (airport, cafe) — better than unencrypted open

---

# PART C: SENIOR / LEAD (10 Questions)

---

### Q31. Design the WiFi subsystem for an automotive digital cockpit (SA8295P + QCA6698AQ).
**A:**
```
Requirements:
1. Passenger hotspot (8-10 clients, 5/6 GHz)
2. Phone projection (WiFi Direct, 5 GHz)
3. OTA updates (STA mode, background)
4. BT audio + BLE Digital Key coexistence
5. Regulatory compliance (multi-region vehicle)

Architecture:
┌─────────────────────────────────────────────────────┐
│ AAOS Layer:                                          │
│ • CarWifiService (automotive-specific policies)     │
│ • Hotspot management (auto-start, client limits)   │
│ • OTA download scheduler (WiFi-preferred policy)   │
│ • Phone projection controller (WiFi Direct → AA)   │
├─────────────────────────────────────────────────────┤
│ WiFi Stack:                                          │
│ • wpa_supplicant: STA + P2P concurrent              │
│ • hostapd: SoftAP (WPA3-Personal)                  │
│ • Concurrency: STA + SAP + P2P on single radio     │
│ • Channel selection: DFS-free, away from BT        │
├─────────────────────────────────────────────────────┤
│ Driver: qcacld-3.0                                   │
│ • FW offload: Scan, roaming, power save            │
│ • Concurrency: SCC preferred, MCC fallback         │
│ • SAR: Antenna-specific TX power limits            │
├─────────────────────────────────────────────────────┤
│ QCA6698AQ (WiFi 6E + BT 5.2 combo):                │
│ • 2×2 MIMO, 160 MHz capable                        │
│ • Internal BT/WiFi coexistence (no external PTA)   │
│ • Automotive temp: -40°C to +105°C                 │
│ • PCIe Gen3 x1 interface to SA8295P                │
└─────────────────────────────────────────────────────┘

Key Decisions:
1. Band: SoftAP on 5 GHz (Ch 36-48) — no DFS, no BT conflict
2. P2P: Force same channel as SAP (SCC) when possible
3. STA: Opportunistic — connect when in range (home/office)
4. Security: WPA3-Personal (SAP), WPA2/WPA3 transition (STA)
5. Power: Shut down WiFi after ignition-off + 30 min timeout
6. Regulatory: Runtime country code from GNSS/cellular MCC
7. Antenna: Shark-fin (roof) with 2× 5GHz + 1× 2.4GHz elements
```

---

### Q32. How would you debug slow WiFi throughput on an automotive head unit?
**A:**
```
Systematic Debug Process:

1. MEASURE BASELINE:
   iperf3 -c <server> -t 30 -i 1 (from head unit)
   Expected: >400 Mbps (80MHz, 2SS, WiFi 6)
   Actual: 50 Mbps → 8× below expected

2. CHECK PHY LAYER:
   iw dev wlan0 link
   → MCS, bandwidth, signal level, TX bitrate
   If MCS low (e.g., MCS 2): Signal problem
   If MCS high but throughput low: MAC/software problem

3. CHECK SIGNAL:
   iw dev wlan0 station dump
   → RSSI (should be > -65 dBm for 80 MHz)
   → TX/RX MCS (should match expected)
   → TX/RX retries (high retries = interference)

4. CHECK CHANNEL:
   iw dev wlan0 survey dump
   → Channel utilization (busy time / total time)
   → If >50% busy from others → congested channel
   → Noise floor (should be < -90 dBm)

5. CHECK CONCURRENCY:
   Is MCC active? (STA on ch36, SAP on ch149?)
   → Only 50% airtime per interface
   → Fix: Move to SCC (same channel)

6. CHECK AGGREGATION:
   iw dev wlan0 station dump | grep "A-MPDU"
   → A-MPDU factor, subframes, failures
   → If aggregation disabled/limited → low throughput

7. CHECK CPU/SOFTWARE:
   top -n 1 | grep -i wifi
   → Is CPU bottleneck? (unlikely on SA8295P)
   → Check GRO/GSO offload: ethtool -k wlan0
   → Check TCP window: sysctl net.ipv4.tcp_wmem

8. CHECK FIRMWARE:
   dmesg | grep -i "qca\|wifi\|ath"
   → Firmware crashes? Rate limiting?
   → FW version: cat /sys/module/wlan/parameters/fwpath

Fix Priority:
1. Switch to 5/6 GHz if on 2.4 GHz
2. Ensure SCC (avoid MCC time-sharing)
3. Move to less congested channel
4. Verify aggregation enabled (A-MPDU)
5. Check antenna/cable integrity
6. Update firmware
```

---

### Q33. Explain WiFi 7 MLO and its benefits for automotive.
**A:**
```
MLO (Multi-Link Operation):
  Single logical connection spans multiple physical links
  (e.g., 5 GHz + 6 GHz simultaneously)

Benefits for Automotive:

1. HIGHER THROUGHPUT:
   Link 1 (5 GHz, 80 MHz): 600 Mbps
   Link 2 (6 GHz, 160 MHz): 1200 Mbps
   Combined: ~1800 Mbps aggregate
   → Faster OTA downloads

2. LOWER LATENCY:
   If Link 1 busy (other STA transmitting):
   → Send packet on Link 2 immediately (no wait)
   → Particularly important for phone projection
   → Video frame delay: <10ms guaranteed

3. RELIABILITY:
   Link 1 experiences interference:
   → Traffic automatically shifts to Link 2
   → No application-visible disruption
   → Critical for in-call phone projection

4. SEAMLESS COEXISTENCE:
   Link 1 (5 GHz): Phone projection
   Link 2 (6 GHz): Passenger hotspot
   → Different links for different purposes
   → No MCC time-sharing penalty

Automotive Implementation:
  - QCA6698AQ supports 2.4 + 5 + 6 GHz (single radio, so STR limited)
  - Next-gen chips (2026+): Dual/triple radio → full STR MLO
  - Software: Android WiFi framework MLO support (Android 14+)
  - Benefit realized: When AP also supports MLO (home router)
```

---

### Q34. Design a WiFi coexistence strategy for BT + WiFi on QCA6698AQ.
**A:**
```
Problem: Single combo chip, shared 2.4 GHz, shared antenna system

Scenario Matrix:
┌──────────────────┬────────────────┬──────────────────────────────┐
│ WiFi Mode        │ BT Mode        │ Coexistence Strategy          │
├──────────────────┼────────────────┼──────────────────────────────┤
│ STA 5/6 GHz     │ A2DP (2.4)    │ No conflict (different bands)│
│ SAP 5 GHz       │ HFP/SCO (2.4) │ No conflict (different bands)│
│ STA 2.4 GHz     │ A2DP           │ Internal PTA + AFH           │
│ STA 2.4 GHz     │ HFP/SCO       │ BT SCO priority (time-share) │
│ Scan (all bands) │ BLE adv       │ WiFi scan defers to BLE      │
└──────────────────┴────────────────┴──────────────────────────────┘

Design Decisions:

1. PRIMARY STRATEGY: Band separation
   WiFi: Always prefer 5/6 GHz
   BT: Always 2.4 GHz (can't change)
   → Zero interference in normal operation

2. FALLBACK (WiFi on 2.4 GHz unavoidable):
   a) Internal PTA (Priority Traffic Arbitration):
      - BT SCO (voice call) > WiFi > BT ACL (music)
      - WiFi scan pauses during BT SCO slots
      - BT AFH avoids WiFi's 20 MHz channel
   
   b) Time budget: 
      BT SCO: 7.5ms interval → 1.25ms TX slot
      WiFi: Can transmit between BT slots
      → WiFi effective throughput reduced ~30%

3. SCAN COEXISTENCE:
   WiFi scan on 2.4 GHz channels:
   → Brief (~20ms per channel)
   → Coordinate with BT: Don't scan during SCO slot
   → Use firmware scan offload (fewer host-driven scans)

4. ANTENNA:
   Diplexer: Separates 2.4 GHz (shared) from 5/6 GHz (WiFi only)
   2.4 GHz path: Time-shared between WiFi and BT via PTA

5. MONITORING:
   Track: BT error rate during WiFi TX
   Track: WiFi retry rate during BT activity
   If degradation > threshold: Reduce WiFi TX power on 2.4 GHz
```

---

### Q35. How would you handle OTA update delivery over WiFi in a fleet vehicle?
**A:**
```
Requirements:
- Update size: 2-5 GB
- Fleet: 10,000 vehicles
- Deadline: 7 days for 95% penetration
- Constraints: No cellular data cost, WiFi preferred
- Vehicle operating conditions: Parked 8-12 hours/day

Architecture:
┌─────────────────────────────────────────────────────────┐
│ Cloud:                                                    │
│ • CDN with edge servers (reduce latency)                │
│ • Differential updates (delta OTA: 200MB vs 5GB)        │
│ • Campaign management (staged rollout: 1% → 10% → 100%)│
│ • Vehicle eligibility check (battery, storage, version) │
└──────────────────────────────┬──────────────────────────┘
                               │ HTTPS (TLS 1.3)
┌──────────────────────────────▼──────────────────────────┐
│ Vehicle WiFi Strategy:                                    │
│                                                           │
│ 1. Auto-connect to known networks:                       │
│    - Home WiFi (user-configured, high priority)          │
│    - Office WiFi (PEAP/enterprise, auto-enrolled)        │
│    - Fleet depot WiFi (pre-configured, WPA3-Enterprise)  │
│                                                           │
│ 2. Download policy:                                       │
│    - Only on unmetered WiFi (not tethered/hotspot)       │
│    - Battery: Vehicle must be parked OR charging         │
│    - Storage: >10 GB free on /data partition             │
│    - Time: Prefer nighttime (01:00-05:00) to avoid       │
│      congesting user's home network during peak          │
│                                                           │
│ 3. Resume/Retry:                                          │
│    - HTTP Range requests (resume from last byte)         │
│    - Chunk verification (SHA-256 per 4MB chunk)          │
│    - Failed chunks: Retry 3×, then re-download chunk    │
│    - Connection lost: Resume on next WiFi session        │
│                                                           │
│ 4. Fallback (if WiFi unavailable for 5+ days):          │
│    - Cellular download (with user consent)               │
│    - Or: Dealership visit notification                   │
│                                                           │
│ 5. Power management:                                      │
│    - Keep WiFi active 30 min after ignition off          │
│    - If download in progress: Extend to 2 hours         │
│    - Monitor 12V battery: Stop if <11.5V                │
│    - Wake for download at scheduled time (RTC alarm)     │
└──────────────────────────────────────────────────────────┘

Security:
- TLS 1.3 with pinned certificates (prevent MITM)
- Full image: Ed25519 signature verification before install
- Rollback protection: Version monotonically increasing
- Secure boot chain: Verified boot from signed image
```

---

# PART D: STAFF / ARCHITECT (5 Questions)

---

### Q36. Architect a next-generation vehicle connectivity platform (WiFi 7 + 5G + BT + UWB).
**A:**
```
┌─────────────────────────────────────────────────────────────────────┐
│         NEXT-GEN VEHICLE CONNECTIVITY PLATFORM (2027+)              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────── Connectivity Domain Controller ──────────────┐  │
│  │ SoC: SA8795P (next-gen)                                        │  │
│  │                                                                 │  │
│  │  ┌──────────┐  ┌──────────┐  ┌────────┐  ┌──────────────┐   │  │
│  │  │  WiFi 7  │  │  5G/LTE  │  │BT 5.4  │  │    UWB       │   │  │
│  │  │  Stack   │  │  Modem   │  │ Stack   │  │  Stack       │   │  │
│  │  └────┬─────┘  └────┬─────┘  └───┬────┘  └──────┬───────┘   │  │
│  │       │              │            │              │             │  │
│  │  ┌────▼──────────────▼────────────▼──────────────▼─────────┐  │  │
│  │  │       UNIFIED CONNECTIVITY MANAGER                       │  │  │
│  │  │  • Policy engine (priority, power, security)            │  │  │
│  │  │  • Traffic steering (WiFi vs 5G vs BT)                 │  │  │
│  │  │  • Power optimization (vehicle state aware)            │  │  │
│  │  │  • Multi-link bonding (WiFi MLO + 5G CA)              │  │  │
│  │  │  • Security orchestration (zero-trust per flow)        │  │  │
│  │  └────────────────────────────────────────────────────────┘  │  │
│  └─────────────────────────────────────────────────────────────────┘  │
│                                                                       │
│  HARDWARE:                                                            │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ WiFi 7 Chip (next-gen QCA):                                      │ │
│  │ • Tri-band: 2.4 + 5 + 6 GHz (three radios → full STR MLO)    │ │
│  │ • 4×4 MIMO on 5/6 GHz, 2×2 on 2.4 GHz                        │ │
│  │ • 320 MHz support, 4096-QAM                                     │ │
│  │ • BT 5.4 integrated (LE Audio, Channel Sounding)               │ │
│  │ • Interface: PCIe Gen4                                           │ │
│  ├─────────────────────────────────────────────────────────────────┤ │
│  │ 5G Modem (Snapdragon X75 or next):                              │ │
│  │ • Sub-6 + mmWave                                                 │ │
│  │ • C-V2X sidelink (PC5)                                          │ │
│  │ • eSIM + physical SIM                                            │ │
│  ├─────────────────────────────────────────────────────────────────┤ │
│  │ UWB (NXP/Qorvo):                                                │ │
│  │ • 8 anchors (doors, trunk, dashboard, roof)                    │ │
│  │ • IEEE 802.15.4z + FiRa                                         │ │
│  │ • Ranging accuracy: ±10 cm                                      │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                       │
│  USE CASES:                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ • OTA: WiFi 7 MLO (2 Gbps+) → 5 GB in 20 seconds             │ │
│  │ • Projection: WiFi 7 P2P (4K, <5ms latency via MLO)          │ │
│  │ • Hotspot: WiFi 7 SAP (320 MHz, 20+ clients)                  │ │
│  │ • Calls: BT LE Audio CIS (low latency) + WiFi for data        │ │
│  │ • Key: BLE discovery → UWB ranging → BT Channel Sounding     │ │
│  │ • V2X: C-V2X sidelink (safety) + WiFi mesh (non-safety)      │ │
│  │ • Streaming: 5G ↔ WiFi seamless handover (MPTCP/ATSSS)       │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Q37. How would you ensure WiFi security in an automotive environment against known attacks?
**A:**
```
Threat Model:
┌─────────────────────────────────────────────────────────────────┐
│ 1. Passive eavesdropping (parked car, adjacent vehicle)         │
│ 2. Deauthentication DoS (force disconnect phone projection)     │
│ 3. Evil Twin AP (fake known network to intercept OTA)          │
│ 4. KRACK/FragAttacks (protocol vulnerabilities)                 │
│ 5. Rogue STA on hotspot (attack other passengers)              │
│ 6. Physical access (USB/JTAG access to extract keys)           │
└─────────────────────────────────────────────────────────────────┘

Defense-in-Depth:

Layer 1: RADIO
  • Minimize TX power (reduce exposure range)
  • Use 5/6 GHz (shorter range = smaller attack surface)
  • Disable 2.4 GHz if not needed

Layer 2: AUTHENTICATION & ENCRYPTION
  • SoftAP: WPA3-Personal (SAE) — no offline brute-force
  • STA: WPA3 preferred, WPA2 fallback with PMF
  • Enterprise (fleet): WPA3-Enterprise 192-bit + EAP-TLS (mutual cert)
  • PMF mandatory (prevent deauth attacks)
  • Disable WPS (known vulnerabilities)
  • Rotate GTK frequently (every 10 minutes)

Layer 3: NETWORK
  • Client isolation on SoftAP (STAs can't reach each other)
  • Firewall: Block access to internal vehicle network from WiFi
  • Separate VLAN: Passenger WiFi ≠ Vehicle ECU network
  • MAC filtering (optional, for known devices only)

Layer 4: TRANSPORT
  • TLS 1.3 for all vehicle↔cloud communication
  • Certificate pinning for OTA servers
  • mTLS for fleet management APIs

Layer 5: APPLICATION
  • OTA: Signed + encrypted images (verify before apply)
  • Phone projection: Application-layer auth (AA/CarPlay protocol)
  • Zero-trust: Each connection re-authenticated periodically

Layer 6: MONITORING
  • Log all (de)auth events → detect deauth attacks
  • Monitor for rogue APs (SSID matching vehicle's)
  • Alert on: Multiple failed WPA attempts (brute force)
  • Anomaly detection: Unusual traffic patterns from STA

Patch Management:
  • wpa_supplicant/hostapd: Track CVEs, OTA patch
  • Driver/FW: Vendor security patches quarterly
  • Known vulns: KRACK → patched. FragAttacks → patched.
```

---

# PART E: QUICK-FIRE (20 Questions)

| # | Question | Answer |
|---|----------|--------|
| F1 | WiFi frequency bands? | 2.4 GHz, 5 GHz, 6 GHz |
| F2 | Non-overlapping 2.4 GHz channels (US)? | 1, 6, 11 |
| F3 | WiFi 6 standard number? | 802.11ax |
| F4 | WiFi 7 standard number? | 802.11be |
| F5 | Maximum channel width WiFi 7? | 320 MHz |
| F6 | Highest QAM in WiFi 7? | 4096-QAM (12 bits/symbol) |
| F7 | What is OFDMA? | Multi-user access via sub-channel resource units |
| F8 | What is TWT? | Target Wake Time — scheduled sleep/wake (power save) |
| F9 | WPA3 key exchange? | SAE (Simultaneous Authentication of Equals) |
| F10 | What encryption does WPA2 use? | AES-CCMP (128-bit) |
| F11 | 4-way handshake derives what key? | PTK (Pairwise Transient Key) |
| F12 | What is PMF? | Protected Management Frames (802.11w) |
| F13 | SIFS duration (OFDM)? | 10 µs (802.11a/g/n/ac/ax) |
| F14 | Slot time (OFDM)? | 9 µs |
| F15 | What is MLO? | Multi-Link Operation (WiFi 7, multiple simultaneous links) |
| F16 | Linux WiFi config API? | cfg80211 / nl80211 |
| F17 | Android WiFi daemon? | wpa_supplicant |
| F18 | WiFi Direct AP role name? | Group Owner (GO) |
| F19 | What is BSS Color? | 6-bit ID to distinguish overlapping BSSs (WiFi 6) |
| F20 | DFS CAC duration? | 60 seconds (listen before transmit) |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
