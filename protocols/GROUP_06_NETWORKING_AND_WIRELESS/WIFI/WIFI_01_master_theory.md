# Wi-Fi (IEEE 802.11) — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 01 of 08
# Comprehensive theory: PHY → MAC → Network → Security → Automotive
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS Wi-Fi?

Wi-Fi is a family of wireless network protocols based on the **IEEE 802.11** standards. It provides high-throughput local area networking over unlicensed radio spectrum. The term "Wi-Fi" is a trademark of the **Wi-Fi Alliance**, which certifies interoperability.

**Key characteristics:**
- Half-duplex (shared medium, one device transmits at a time per channel)
- Uses CSMA/CA (not CSMA/CD like Ethernet) — can't detect collisions on air
- Unlicensed ISM/U-NII bands (2.4 GHz, 5 GHz, 6 GHz)
- Variable data rates depending on conditions (rate adaptation)
- Supports infrastructure (AP-based), ad-hoc, and mesh topologies

---

## 2. HISTORY & EVOLUTION

| Year | Standard | Marketing Name | Key Milestone |
|------|----------|---------------|---------------|
| 1997 | 802.11 | — | First standard, 2 Mbps, FHSS + IR |
| 1999 | 802.11b | — | 11 Mbps, CCK, 2.4 GHz → mass adoption |
| 1999 | 802.11a | — | 54 Mbps, OFDM, 5 GHz (ahead of time) |
| 2003 | 802.11g | — | 54 Mbps, OFDM in 2.4 GHz |
| 2004 | 802.11i | — | WPA2 security (AES-CCMP) |
| 2009 | 802.11n | Wi-Fi 4 | MIMO, 40 MHz, 600 Mbps |
| 2012 | 802.11ad | WiGig | 60 GHz, 7 Gbps (short range) |
| 2013 | 802.11ac | Wi-Fi 5 | 5 GHz only, MU-MIMO DL, 160 MHz, 256-QAM |
| 2016 | 802.11ah | HaLow | Sub-1 GHz IoT (900 MHz, long range) |
| 2020 | 802.11ax | Wi-Fi 6 | OFDMA, TWT, 1024-QAM, BSS Color |
| 2021 | 802.11ax (6G) | Wi-Fi 6E | Extended to 6 GHz (1200 MHz new spectrum) |
| 2024 | 802.11be | Wi-Fi 7 | MLO, 320 MHz, 4096-QAM, 16 SS |
| Future | 802.11bn | Wi-Fi 8 | Ultra High Reliability (UHR) |

---

## 3. RADIO FUNDAMENTALS

### 3.1 Frequency Bands

**2.4 GHz Band (ISM)**
- Range: 2400–2483.5 MHz
- Channels: 14 total (channels 1-11 in US, 1-13 in EU, 1-14 in Japan)
- Non-overlapping: Ch 1, 6, 11 (20 MHz each with guard bands)
- Pros: Better wall penetration, longer range
- Cons: Crowded (BT, microwave, Zigbee, cordless phones)

**5 GHz Band (U-NII)**
- Range: 5150–5850 MHz (varies by region)
- Channels: 25 non-overlapping 20 MHz channels (US)
- Sub-bands: U-NII-1 (5150-5250), U-NII-2 (5250-5350), U-NII-2e (5470-5725), U-NII-3 (5725-5850)
- DFS required: U-NII-2 and U-NII-2e (radar avoidance)
- Pros: Many channels, less interference
- Cons: Shorter range, more attenuation through walls

**6 GHz Band (Wi-Fi 6E/7)**
- Range: 5925–7125 MHz (1200 MHz — doubles available spectrum)
- Channels: 59 new 20 MHz channels (US), 14 × 80 MHz, 7 × 160 MHz, 3 × 320 MHz
- No legacy devices (clean band)
- No DFS required (in most regions)
- Low power indoor (LPI) + Standard Power (with AFC)
- AFC: Automated Frequency Coordination (avoid incumbents)

### 3.2 Channel Widths & Bonding

| Width | Subcarriers (Data) | Wi-Fi 4+ | Wi-Fi 5+ | Wi-Fi 6+ | Wi-Fi 7 |
|-------|-------------------|-----------|-----------|-----------|---------|
| 20 MHz | 52 (HT), 234 (HE) | ✓ | ✓ | ✓ | ✓ |
| 40 MHz | 108 (HT), 468 (HE) | ✓ | ✓ | ✓ | ✓ |
| 80 MHz | 234 (VHT), 980 (HE) | — | ✓ | ✓ | ✓ |
| 160 MHz | 468 (VHT), 1960 (HE) | — | ✓ | ✓ | ✓ |
| 320 MHz | 3920 (EHT) | — | — | — | ✓ |

**Channel bonding**: Combining adjacent 20 MHz channels. Primary channel always 20 MHz (for backward compatibility and management frames). Secondary channels added for data.

### 3.3 Modulation & Coding

**OFDM (Orthogonal Frequency Division Multiplexing)**
- Divides wideband channel into many narrow subcarriers
- Each subcarrier carries independent data at low symbol rate
- Resistant to multipath fading (ISI eliminated by guard interval)
- FFT/IFFT used for efficient modulation/demodulation

**QAM Levels:**
| QAM | Bits/Symbol | SNR Required | WiFi Generation |
|-----|-------------|--------------|-----------------|
| BPSK | 1 | ~6 dB | All (lowest rate) |
| QPSK | 2 | ~10 dB | All |
| 16-QAM | 4 | ~16 dB | All |
| 64-QAM | 6 | ~22 dB | 802.11a/g+ |
| 256-QAM | 8 | ~28 dB | Wi-Fi 5+ |
| 1024-QAM | 10 | ~34 dB | Wi-Fi 6+ |
| 4096-QAM | 12 | ~40 dB | Wi-Fi 7 |

Higher QAM = more bits/symbol = higher data rate, but requires better SNR (shorter range or cleaner environment).

### 3.4 MIMO (Multiple-Input Multiple-Output)

**Spatial Multiplexing**: Multiple independent data streams sent simultaneously via different antennas. Receiver separates them using channel estimation.

| MIMO Config | Spatial Streams | Use |
|-------------|----------------|-----|
| 1×1 (SISO) | 1 | IoT, basic devices |
| 2×2 | 2 | Phones, laptops |
| 3×3 | 3 | High-end APs |
| 4×4 | 4 | Enterprise APs |
| 8×8 | 8 | Wi-Fi 6 enterprise |
| 16×16 | 16 | Wi-Fi 7 (theoretical max) |

**MU-MIMO (Multi-User)**:
- **Wi-Fi 5**: Downlink only (AP→STAs), up to 4 users
- **Wi-Fi 6**: Downlink + Uplink, up to 8 users
- **Wi-Fi 7**: Enhanced, up to 16 users
- AP uses beamforming to spatially separate users

**Beamforming**: AP uses CSI (Channel State Information) feedback from STA to steer signal. Concentrates energy toward intended receiver, improving SNR and range.

---

## 4. MAC LAYER ARCHITECTURE

### 4.1 Frame Types

| Type | Subtype | Purpose |
|------|---------|---------|
| **Management (00)** | Beacon | AP periodic announcement (10-100 TU) |
| | Probe Request | STA active scanning |
| | Probe Response | AP reply to probe |
| | Authentication | Open/SAE/FT auth frames |
| | Deauthentication | Force disconnect |
| | Association Request | STA joins BSS |
| | Association Response | AP accepts/rejects |
| | Reassociation Request | STA roaming |
| | Action | Various (spectrum, HT, VHT, HE, Block Ack) |
| **Control (01)** | RTS | Request to Send |
| | CTS | Clear to Send |
| | ACK | Acknowledge data frame |
| | Block Ack Request | Request block acknowledgment |
| | Block Ack | Acknowledge block of frames |
| | PS-Poll | Power save poll (retrieve buffered) |
| **Data (10)** | Data | Payload (LLC/SNAP + IP/ARP) |
| | Null | No data (used for power save signaling) |
| | QoS Data | Data with QoS header (WMM) |
| | QoS Null | Power save with QoS |

### 4.2 CSMA/CA (Channel Access)

```
WiFi cannot detect collisions during TX (unlike Ethernet).
Solution: AVOID collisions instead.

DCF (Distributed Coordination Function):
1. STA wants to TX → Listen (carrier sense)
2. If channel IDLE for DIFS period:
   → TX immediately (if backoff = 0)
3. If channel BUSY:
   → Wait until idle
   → Wait DIFS
   → Start random backoff countdown (CW × slot time)
   → Decrement only when channel idle
   → TX when backoff reaches 0
4. Receiver sends ACK after SIFS (shortest gap)
5. If no ACK received → double CW, retry

Timing:
  SIFS (Short IFS): 10 µs (802.11a/g/n/ac), 16 µs (802.11b)
  DIFS (DCF IFS): SIFS + 2 × slot time = 34 µs (802.11a/g)
  Slot Time: 9 µs (OFDM), 20 µs (802.11b)
  CW (Contention Window): CWmin=15, CWmax=1023 (doubles on retry)
```

### 4.3 EDCA (QoS — Wi-Fi Multimedia)

| Access Category | Traffic Type | CWmin | CWmax | AIFSN | TXOP Limit |
|----------------|--------------|-------|-------|-------|------------|
| AC_VO (Voice) | VoIP, calls | 3 | 7 | 2 | 1.504 ms |
| AC_VI (Video) | Video stream | 7 | 15 | 2 | 3.008 ms |
| AC_BE (Best Effort) | Web, email | 15 | 1023 | 3 | 0 |
| AC_BK (Background) | Bulk download | 15 | 1023 | 7 | 0 |

Lower CWmin + Lower AIFSN = Higher priority = Gets channel sooner.

### 4.4 Block Acknowledgment

Instead of ACKing each frame individually:
1. Sender: Transmits burst of frames (within TXOP)
2. Sender: Sends Block Ack Request
3. Receiver: Replies with Block Ack (bitmap showing which frames received)
4. Sender: Retransmits only failed frames

Improves efficiency by reducing per-frame ACK overhead.

---

## 5. CONNECTION LIFECYCLE

### 5.1 Scanning
- **Passive**: Listen for beacons on each channel (slow but power-efficient)
- **Active**: Send Probe Request → receive Probe Response (fast but uses airtime)

### 5.2 Authentication
- **Open System**: Always succeeds (no actual auth at this stage)
- **SAE (WPA3)**: Simultaneous Authentication of Equals (Dragonfly key exchange)
- **FT (802.11r)**: Fast Transition (pre-computed keys for roaming)

### 5.3 Association
- STA sends **Association Request** (supported rates, HT/VHT/HE capabilities, SSID, security)
- AP sends **Association Response** (AID, status, capabilities)
- If accepted: STA is now associated (can participate in 4-way handshake)

### 5.4 4-Way Handshake (Key Establishment)
```
PMK derivation:
  Personal: PMK = PBKDF2(passphrase, SSID, 4096, 256)
  Enterprise: PMK from RADIUS/EAP authentication

4-Way Handshake:
  Message 1: AP → STA: ANonce
  Message 2: STA → AP: SNonce + MIC (proves STA knows PMK)
  Message 3: AP → STA: GTK (encrypted) + MIC (proves AP knows PMK)
  Message 4: STA → AP: ACK
  
  PTK = PRF(PMK, "Pairwise key expansion", min(AA,SA) || max(AA,SA) || min(ANonce,SNonce) || max(ANonce,SNonce))
  
  PTK components:
    KCK (Key Confirmation Key): MIC calculation
    KEK (Key Encryption Key): GTK encryption
    TK (Temporal Key): Data encryption (AES-CCMP/GCMP)
```

### 5.5 Data Transfer
- Encrypted data frames flow (AES-CCMP or GCMP)
- Rate adaptation: MCS dynamically adjusted based on channel conditions
- Aggregation: A-MSDU (multiple MSDUs in one MPDU) and A-MPDU (multiple MPDUs in one PHY frame)

### 5.6 Roaming (802.11r/k/v)
- **802.11k**: AP provides neighbor report (which APs are nearby)
- **802.11v**: AP suggests BSS transition ("move to this AP")
- **802.11r**: Fast BSS Transition — pre-derive keys with target AP (roam in <50ms vs >200ms)

---

## 6. Wi-Fi 6 (802.11ax) — DEEP DIVE

### 6.1 OFDMA (Orthogonal Frequency Division Multiple Access)

**Problem (pre-WiFi 6)**: Only one device can transmit at a time per channel. In dense environments (stadium, apartment), devices compete heavily → high latency.

**Solution**: Divide channel into **Resource Units (RUs)** — smaller frequency chunks assigned to different devices simultaneously.

| Channel Width | Possible RU Allocations |
|--------------|------------------------|
| 20 MHz | 9×26-tone, 4×52-tone, 2×106-tone, 1×242-tone |
| 40 MHz | Up to 18×26-tone, ..., 1×484-tone |
| 80 MHz | Up to 37×26-tone, ..., 1×996-tone |
| 160 MHz | Up to 74×26-tone, ..., 1×2×996-tone |

**RU sizes (tones)**: 26, 52, 106, 242, 484, 996, 2×996

AP acts as scheduler: decides which STA gets which RU in each transmission opportunity.

### 6.2 Target Wake Time (TWT)

Devices negotiate specific wake times with AP:
- STA tells AP: "Wake me at time T, for duration D, every interval I"
- Between wake times: STA sleeps (extreme power savings)
- **Benefit**: IoT devices can last years on battery
- **Automotive use**: BLE-like power savings for in-vehicle WiFi sensors

### 6.3 BSS Coloring

**Problem**: In dense deployments, STAs defer to ANY detected WiFi signal (even from neighboring BSS) → reduced throughput.

**Solution**: Each BSS gets a 6-bit "color" (0-63):
- STA detects signal → checks color
- Same color → defer (intra-BSS)
- Different color → may transmit simultaneously (inter-BSS, OBSS)
- Result: Significant throughput improvement in apartments/dense areas

### 6.4 1024-QAM

- 10 bits per symbol (vs 8 for 256-QAM)
- 25% throughput improvement at close range
- Requires very high SNR (~34 dB) → only useful near AP

### 6.5 Uplink MU-MIMO & OFDMA

WiFi 6 enables **simultaneous uplink** from multiple STAs:
- Trigger Frame: AP sends trigger telling multiple STAs to transmit simultaneously
- STAs respond in assigned RUs (OFDMA) or spatial streams (MU-MIMO)
- AP combines/separates using advanced signal processing

---

## 7. Wi-Fi 7 (802.11be) — KEY INNOVATIONS

### 7.1 Multi-Link Operation (MLO)

Single logical connection uses multiple physical links (e.g., 2.4 + 5 + 6 GHz simultaneously):
- **Aggregation**: Data split across links → higher throughput
- **Low latency**: If one link busy, send on another immediately
- **Reliability**: Redundant transmission on multiple links
- Single MAC address, multiple PHY links

### 7.2 320 MHz Channels

- Only available in 6 GHz band (requires 320 MHz contiguous)
- Doubles throughput vs 160 MHz
- 4096-QAM + 320 MHz + 16 SS → theoretical 46 Gbps

### 7.3 4096-QAM

- 12 bits per symbol (vs 10 for 1024-QAM)
- 20% improvement over Wi-Fi 6 at very close range
- Extremely sensitive to noise (requires SNR > 40 dB)

### 7.4 Multi-RU / Preamble Puncturing

- **Multi-RU**: Assign non-contiguous RUs to same STA (more scheduling flexibility)
- **Preamble Puncturing**: Use 160/320 MHz channel even if some 20 MHz sub-channels are occupied (puncture them out)

---

## 8. SECURITY

### 8.1 Security Evolution

| Era | Protocol | Encryption | Auth | Status |
|-----|----------|-----------|------|--------|
| 1999 | WEP | RC4 (40/104-bit) | Shared Key | **BROKEN** — never use |
| 2003 | WPA | TKIP (RC4 wrapper) | PSK or 802.1X | **Deprecated** |
| 2004 | WPA2 | AES-CCMP (128-bit) | PSK or 802.1X | Still common |
| 2018 | WPA3 | AES-CCMP/GCMP (128/192-bit) | SAE or 802.1X | **Recommended** |
| 2020 | OWE | AES-CCMP | DH key exchange | Open networks (encrypted) |

### 8.2 WPA3-Personal (SAE)

**SAE (Simultaneous Authentication of Equals)** — Dragonfly handshake:
- Based on password but immune to offline dictionary attacks
- Even if attacker captures handshake, can't brute-force offline
- Provides forward secrecy (compromised password doesn't expose past sessions)
- **Process**: Both sides derive same secret from password + elliptic curve operations

### 8.3 WPA3-Enterprise (192-bit)

- Suite-B cryptography (government-grade)
- GCMP-256 encryption (AES-256-GCM)
- 384-bit ECDH key exchange (P-384 curve)
- SHA-384 for key derivation
- Mandatory 802.1X + EAP-TLS with certificates
- No PSK option — certificates required

### 8.4 Key Hierarchy

```
PSK/Passphrase or EAP
        │
        ▼
    PMK (Pairwise Master Key, 256-bit)
        │
        ▼ (4-Way Handshake)
    PTK (Pairwise Transient Key)
    ├── KCK (Key Confirmation Key) — MIC in EAPOL
    ├── KEK (Key Encryption Key) — encrypt GTK delivery
    └── TK (Temporal Key) — encrypt/decrypt data frames
    
    GTK (Group Temporal Key) — broadcast/multicast
    └── Distributed encrypted by KEK in Msg 3
    
    IGTK (Integrity GTK) — PMF (protected management frames)
```

### 8.5 802.1X / EAP (Enterprise)

```
STA (Supplicant) ←→ AP (Authenticator) ←→ RADIUS Server (Auth Server)

Flow:
1. STA associates to AP (open auth)
2. AP blocks all traffic except EAP
3. EAP exchange tunneled through AP to RADIUS:
   - EAP-TLS: Client + server certificates (strongest)
   - PEAP: Server cert + username/password in TLS tunnel
   - EAP-TTLS: Similar to PEAP (vendor-neutral)
4. RADIUS sends Accept + PMK to AP
5. 4-Way Handshake between AP and STA
6. Port opened — STA can send data
```

### 8.6 Known Vulnerabilities

| Vulnerability | Year | Impact | Mitigation |
|--------------|------|--------|------------|
| WEP cracking | 2001 | Full key recovery in minutes | Use WPA2/WPA3 |
| KRACK | 2017 | Key reinstallation (nonce reuse in 4-way) | Patch supplicant/AP |
| Dragonblood | 2019 | Side-channel on SAE (timing/cache) | Patch WPA3 implementation |
| FragAttacks | 2021 | Frame fragmentation vulnerabilities | Kernel/driver patches |
| WiFi deauth | Ongoing | DoS via spoofed deauth frames | PMF (802.11w) mandatory |

---

## 9. POWER MANAGEMENT

### 9.1 Legacy Power Save (PS-Poll)

1. STA informs AP: "I'm going to sleep" (null frame with PM=1)
2. AP buffers frames for sleeping STA
3. AP indicates buffered data in beacon TIM (Traffic Indication Map)
4. STA wakes for beacon, checks TIM
5. If data buffered: STA sends PS-Poll → AP delivers frame
6. Repeat until no more buffered data → sleep again

**Problem**: High latency (must wait for beacon interval, typically 100ms)

### 9.2 U-APSD (Unscheduled Automatic Power Save Delivery)

- STA triggers delivery by sending any uplink frame (QoS Data/Null)
- AP immediately delivers all buffered frames (no waiting for beacon)
- Better for real-time traffic (VoIP, gaming)
- Per-AC configuration (voice can be U-APSD, best-effort can be legacy)

### 9.3 TWT (Target Wake Time — Wi-Fi 6)

- Negotiated schedule: STA sleeps for long periods, wakes only at agreed times
- Individual TWT: AP and STA negotiate specific schedule
- Broadcast TWT: AP announces schedule for group of STAs
- **Automotive use**: In-vehicle sensors wake every 10 seconds to report

---

## 10. ANDROID WiFi ARCHITECTURE

### 10.1 Layer Stack

```
┌─────────────────────────────────────────────────────────────┐
│  Applications (Settings, SystemUI, OTA updater, Projection) │
├─────────────────────────────────────────────────────────────┤
│  WifiManager API (android.net.wifi)                          │
├─────────────────────────────────────────────────────────────┤
│  WifiService (com.android.server.wifi)                       │
│  ├── WifiStateMachine / ClientModeImpl                      │
│  ├── WifiNative (JNI bridge)                                │
│  ├── SoftApManager (hotspot)                                │
│  ├── WifiScanner (scan management)                          │
│  └── WifiConnectivityManager (auto-connect logic)           │
├─────────────────────────────────────────────────────────────┤
│  wpa_supplicant (AIDL/HIDL HAL)                             │
│  ├── Control interface (wlan0, p2p0)                        │
│  ├── WPA2/WPA3/SAE/802.1X                                  │
│  └── P2P (Wi-Fi Direct) management                         │
├─────────────────────────────────────────────────────────────┤
│  Vendor HAL (android.hardware.wifi)                          │
│  ├── AIDL IWifi, IWifiChip, IWifiStaIface, IWifiApIface   │
│  └── Vendor-specific features (roaming, offload, SAR)      │
├─────────────────────────────────────────────────────────────┤
│  Kernel WiFi Subsystem                                       │
│  ├── cfg80211 (configuration API)                           │
│  ├── mac80211 (SW MAC layer, optional)                      │
│  ├── nl80211 (netlink interface)                            │
│  └── Vendor driver (qcacld-3.0 for Qualcomm)              │
├─────────────────────────────────────────────────────────────┤
│  Firmware (QCA6698AQ)                                        │
│  ├── MAC offload (scan, roaming, power save)               │
│  ├── PHY processing                                         │
│  └── Regulatory enforcement                                 │
├─────────────────────────────────────────────────────────────┤
│  Hardware (RF + Antenna)                                     │
└─────────────────────────────────────────────────────────────┘
```

### 10.2 Key Android WiFi Classes

| Class | Package | Purpose |
|-------|---------|---------|
| WifiManager | android.net.wifi | App-facing API (scan, connect, info) |
| WifiInfo | android.net.wifi | Current connection state (SSID, RSSI, speed) |
| ScanResult | android.net.wifi | Scan result (BSSID, SSID, freq, capabilities) |
| WifiConfiguration | android.net.wifi | Saved network config (deprecated → WifiNetworkSuggestion) |
| WifiNetworkSpecifier | android.net.wifi | Request specific network (Android 10+) |
| WifiP2pManager | android.net.wifi.p2p | Wi-Fi Direct API |
| WifiAwareManager | android.net.wifi.aware | Neighbor Aware Networking (NAN) |
| ConnectivityManager | android.net | Network selection and routing |

### 10.3 WiFi Enable/Connect Flow (Android)

```
1. User enables WiFi:
   Settings → WifiManager.setWifiEnabled(true)
   → WifiService → WifiNative → load driver + firmware
   → wpa_supplicant started
   → Interface UP (wlan0)

2. Scanning:
   WifiService → WifiScanner → wpa_supplicant → driver → FW scan
   ← Results: ScanResult[] (SSID, BSSID, freq, RSSI, capabilities)

3. Connection:
   WifiService → WifiConnectivityManager (evaluates saved networks)
   → WifiNative → wpa_supplicant: SELECT_NETWORK <id>
   → Auth + Association + 4-Way Handshake
   → wpa_supplicant reports: CTRL-EVENT-CONNECTED
   → WifiService: state = CONNECTED

4. IP acquisition:
   → DHCP client (DhcpClient) → DHCPDISCOVER/OFFER/REQUEST/ACK
   → IP configured on wlan0
   → ConnectivityManager validates internet (captive portal check)
   → Network available to apps
```

---

## 11. AUTOMOTIVE WiFi — DETAILED

### 11.1 SoftAP (Vehicle Hotspot)

```
Architecture:
  QCA6698AQ ──PCIe──▶ SA8295P
  
  SA8295P runs:
  ├── hostapd (AP daemon) — manages SoftAP
  ├── dnsmasq (DHCP server) — assigns IPs to clients
  └── iptables/nftables — NAT for internet sharing
  
  Internet source:
  ├── Cellular modem (TCU) via rmnet interfaces
  ├── Or: WiFi STA on another interface (bridge)
  
  Configuration:
  - Band: 5 GHz preferred (avoid 2.4 GHz BT interference)
  - Channel: DFS-free channels preferred (Ch 36-48)
  - Width: 80 MHz (balance throughput vs interference)
  - Security: WPA3-Personal (SAE) or WPA2
  - Max clients: Typically 8-10
  - Isolation: Clients isolated from vehicle's internal network
```

### 11.2 Wi-Fi Direct (Phone Projection)

**Android Auto Wireless / Wireless CarPlay** use Wi-Fi Direct (P2P):

```
Flow:
1. Head unit advertises Wi-Fi Direct service (P2P GO intent)
2. Phone discovers head unit (via BT or NFC initial handshake)
3. P2P Group Formation:
   - GO Negotiation (who becomes AP?)
   - Head unit usually becomes GO (Group Owner = AP role)
4. WPS (Wi-Fi Protected Setup) for key exchange
5. DHCP: Phone gets IP from head unit
6. Application layer:
   - Android Auto: TCP/UDP streams (H.264 video + audio + control)
   - CarPlay: AirPlay protocol over P2P link

Concurrency challenge:
  QCA6698AQ must support simultaneously:
  - STA mode (connected to home WiFi for OTA)
  - P2P GO (phone projection)
  - SoftAP (passenger hotspot)
  = 3 virtual interfaces on same radio (requires MCC/SCC)
```

### 11.3 OTA Updates

```
Requirement: Download 2-5 GB system update reliably

Design:
- Prefer STA mode (connect to known WiFi: home, office)
- If no WiFi: Fall back to cellular (may be limited)
- Resume support: HTTP range requests for interrupted downloads
- Background download: Use Android JobScheduler + WiFi constraint
- Verify: Full image hash (SHA-256) before applying

Bandwidth planning:
- 802.11ac, 80 MHz, 2SS → ~400 Mbps practical
- 5 GB / 400 Mbps = ~100 seconds (< 2 minutes)
- With overhead/retries: ~3-5 minutes typical

Power consideration:
- Vehicle must remain powered during download
- Park mode: Keep WiFi active for X minutes after engine off
- Resume on next drive if interrupted
```

### 11.4 WiFi/BT Coexistence in Automotive

```
Challenge: QCA6698AQ is combo chip (shared antenna system, shared radio)

Scenarios:
┌──────────────────────────────────────────────────────┐
│ BT A2DP (music) + WiFi STA (OTA download)           │
│ BT HFP (call) + WiFi SoftAP (passenger hotspot)    │
│ BLE (Digital Key) + WiFi P2P (phone projection)     │
└──────────────────────────────────────────────────────┘

Solutions (internal to combo chip):
1. Frequency separation: WiFi on 5/6 GHz, BT on 2.4 GHz
   → No interference (different bands) ✓ Best solution

2. When WiFi on 2.4 GHz + BT:
   - Time-Division: BT gets reserved slots, WiFi works around them
   - Priority: BT SCO (voice) > WiFi > BT ACL (music)
   - AFH: BT hops away from WiFi channel
   
3. Antenna sharing:
   - Diplexer separates 2.4 GHz and 5 GHz paths
   - 2.4 GHz shared: PTA arbitration between WiFi/BT

Priority for automotive:
  1. BT SCO (phone call) — safety
  2. WiFi P2P (phone projection, if in-call audio)
  3. BT A2DP (music)
  4. WiFi SoftAP (passenger internet)
  5. WiFi STA (OTA — can pause/resume)
```

---

## 12. ROAMING

### 12.1 Why Roaming Matters in Automotive

Large vehicles (buses, trains) or vehicles in parking structures may have multiple APs:
- Factory/service WiFi: Vehicle moves through production line
- Fleet management: Depot WiFi coverage with multiple APs
- Home/office: Vehicle in garage connects to nearest AP

### 12.2 Roaming Mechanisms

**Pre-authentication (802.11r/FT)**:
```
Current AP                                    Target AP
    │                                             │
    │   STA determines target (RSSI/load)        │
    │──────── FT Authentication Request ─────────▶│
    │◀─────── FT Authentication Response ─────────│
    │                                             │
    │   STA reassociates                          │
    │──────── Reassociation Request ─────────────▶│
    │◀─────── Reassociation Response ─────────────│
    │                                             │
    │   Total roaming time: <50ms (vs >200ms)     │
    
Key pre-derivation: PMK-R1 derived from PMK-R0 shared between APs
No full 4-way handshake needed at target AP
```

**802.11k (Neighbor Report)**:
- STA requests: "Who are my neighboring APs?"
- AP responds with list (BSSID, channel, PHY type)
- STA doesn't need to scan all channels → faster roaming decision

**802.11v (BSS Transition Management)**:
- AP can suggest: "Move to AP X now" (load balancing or signal quality)
- STA can request: "Should I move?" → AP recommends target
- Enables AP-assisted roaming decisions

---

## 13. Wi-Fi DIRECT & P2P

### 13.1 Architecture

```
Roles:
  P2P Device: Unconnected device participating in discovery
  GO (Group Owner): Acts as AP for the P2P group
  P2P Client: Connects to GO (acts as STA)

Group Formation:
  1. Device Discovery: Probe Request/Response with P2P IE
  2. GO Negotiation: 3-frame exchange to decide who is GO
     - GO Negotiation Request (intent 0-15)
     - GO Negotiation Response
     - GO Negotiation Confirmation
     - Higher intent → becomes GO (tie: random bit)
  3. WPS Provisioning: Key exchange (PBC or PIN)
  4. Address assignment: GO runs DHCP
  5. Data transfer begins

Automotive (forced GO):
  Head unit always becomes GO (intent=15)
  Phone always becomes Client
  Simplifies configuration and NAT
```

### 13.2 Miracast (Display Mirroring)

```
Stack:
  Wi-Fi Direct (L2/L3 connectivity)
  └── RTSP (Real-Time Streaming Protocol) — session control
      └── RTP (Real-Time Protocol) — media transport
          └── MPEG-TS (transport stream)
              ├── H.264/H.265 video (display mirror)
              └── AAC/LPCM audio

Automotive use:
  - Rear-seat entertainment: Mirror phone to rear display
  - Replaced by: Android Auto/CarPlay (better integration)
  - Still used: Some aftermarket systems, generic mirroring
```

---

## 14. REGULATORY & DFS

### 14.1 Regulatory Domains

| Region | 2.4 GHz Channels | 5 GHz (non-DFS) | 5 GHz (DFS) | 6 GHz |
|--------|-------------------|------------------|--------------|-------|
| US (FCC) | 1-11 | 36-48, 149-165 | 52-144 | 5925-7125 |
| EU (ETSI) | 1-13 | 36-48 | 52-140 | 5925-6425 |
| Japan (MIC) | 1-13 | 36-48 | 52-144 | 5925-6425 |
| China | 1-13 | 36-48, 149-165 | 52-64 | Limited |

### 14.2 DFS (Dynamic Frequency Selection)

Required on channels overlapping with radar (aviation, weather):
1. Listen before transmit: 60-second CAC (Channel Availability Check)
2. While operating: Continuously monitor for radar pulses
3. If radar detected: Vacate channel within 10 seconds
4. Move to new channel (may trigger CAC again)

**Automotive impact**: DFS channels avoided for SoftAP (unacceptable 60s startup delay). Prefer channels 36-48, 149-165.

### 14.3 TPC (Transmit Power Control)

- Maximum TX power varies by channel and region
- Indoor vs outdoor limits (6 GHz: LPI vs SP)
- SAR (Specific Absorption Rate): Body proximity reduces TX power
- Automotive: Fixed installation → full power allowed

---

## 15. FRAME AGGREGATION & EFFICIENCY

### 15.1 A-MSDU (Aggregate MAC Service Data Unit)

Multiple Ethernet frames (MSDUs) packed into single MPDU:
- Reduces per-frame overhead (single MAC header)
- Maximum A-MSDU size: 3839 or 7935 bytes
- If any sub-frame corrupted → entire A-MSDU retransmitted
- Best for small frames (TCP ACKs, VoIP)

### 15.2 A-MPDU (Aggregate MAC Protocol Data Unit)

Multiple MPDUs packed into single PHY frame:
- Each MPDU has own MAC header + FCS
- Individually retransmittable (via Block Ack bitmap)
- Maximum: 64 MPDUs (802.11n), 256 MPDUs (802.11ac/ax)
- Primary aggregation method in modern WiFi

### 15.3 Combined: A-MSDU inside A-MPDU

```
PHY Frame
└── A-MPDU
    ├── MPDU 1 (for STA A)
    │   └── A-MSDU
    │       ├── Sub-frame 1 (Ethernet frame)
    │       └── Sub-frame 2 (Ethernet frame)
    ├── MPDU 2 (for STA A)
    │   └── A-MSDU
    │       └── Sub-frame 3
    └── MPDU 3 (for STA B, MU-MIMO)
        └── Data frame

Maximum efficiency: >95% airtime utilized for data
```

---

## 16. THROUGHPUT CALCULATION

### Practical Throughput Formula

```
Data Rate (PHY) depends on:
  Rate = N_SS × N_SD × N_BPSCS × CR / T_OFDM_symbol

Where:
  N_SS = Number of spatial streams
  N_SD = Number of data subcarriers
  N_BPSCS = Bits per subcarrier per symbol (depends on QAM)
  CR = Coding rate (1/2, 2/3, 3/4, 5/6)
  T_OFDM_symbol = Symbol duration (3.2µs + GI)

Example (Wi-Fi 6, 160 MHz, 2×2, MCS 11, 0.8µs GI):
  N_SS = 2
  N_SD = 1960
  N_BPSCS = 10 (1024-QAM)
  CR = 5/6
  T_OFDM = 3.2 + 0.8 = 4.0 µs
  
  Rate = 2 × 1960 × 10 × (5/6) / 4.0µs = 8,167 Mbps ≈ 2.4 Gbps

Practical throughput ≈ 50-70% of PHY rate:
  - MAC overhead (headers, IFS, ACK)
  - TCP/IP overhead
  - Retransmissions
  - Channel access contention

Real-world: Wi-Fi 6 2×2 80MHz → ~600-800 Mbps TCP throughput
```

---

## 17. MESH NETWORKING (802.11s)

```
Topology:
  Mesh STA (any device) ←→ Mesh STA ←→ Mesh STA
  │
  └── Mesh Gate (connection to wired/internet)

Key Protocols:
  - HWMP (Hybrid Wireless Mesh Protocol): Default path selection
  - Peering: Mesh peers establish links (authenticated)
  - Metric: Airtime Link Metric (considers rate, retries)

Automotive relevance:
  - Vehicle platooning (V2V mesh)
  - Parking structure coverage (mesh APs)
  - Limited: Most automotive uses infrastructure mode
```

---

## 18. V2X & 802.11p/bd

### 18.1 DSRC (Dedicated Short-Range Communications)

```
Standard: IEEE 802.11p (now 802.11bd)
Band: 5.9 GHz (5850-5925 MHz)
Purpose: Vehicle-to-Everything communication

Differences from regular WiFi:
  - No association required (OCB — Outside Context of BSS)
  - WAVE (802.11p) frames sent immediately (no auth/assoc)
  - 10 MHz channels (vs 20+ MHz for WiFi)
  - Lower data rate: 3-27 Mbps (prioritizes reliability over speed)
  - Range: 300-1000m (higher TX power allowed)
  
Applications:
  - V2V: Collision avoidance, platooning
  - V2I: Traffic light timing, road hazard warnings
  - V2P: Pedestrian detection
  
Competition: C-V2X (cellular-based V2X, 3GPP)
  - Uses LTE/5G sidelink instead of 802.11p
  - Debate ongoing: DSRC vs C-V2X for standard
  - Some regions mandate one or the other
```

---

## 19. WiFi POWER CONSUMPTION & OPTIMIZATION

### 19.1 Power States

| State | Current (typical) | Description |
|-------|-------------------|-------------|
| TX (20 dBm) | 300-500 mA | Transmitting data |
| RX | 100-200 mA | Receiving/listening |
| Idle (awake) | 50-80 mA | Listening for beacons |
| Doze (PS) | 1-5 mA | Sleeping, wakes for beacon |
| Deep sleep | <1 mA | Radio fully off |

### 19.2 Automotive Power Management

```
Vehicle States:
  RUNNING → WiFi fully active (all modes)
  PARKED  → WiFi can stay on for limited time (OTA download)
  ACC OFF → WiFi off (save 12V battery)
  
Strategy:
  - SoftAP: Active only when ignition ON
  - STA: Can remain for OTA, timeout after 30 min
  - P2P: Active only during phone projection
  - Scan: Reduce frequency when parked
  
Wake triggers:
  - User approaches (via BLE proximity)
  - Scheduled OTA window (Android AlarmManager)
  - Remote command (cellular push)
```

---

## 20. COMMON WiFi ISSUES & SOLUTIONS

| Issue | Root Cause | Diagnosis | Fix |
|-------|-----------|-----------|-----|
| Slow speed | Channel congestion | Scan neighbors, check channel utilization | Switch to 5/6 GHz, change channel |
| Frequent disconnects | Weak signal / interference | Check RSSI, look for deauth frames | Move AP, increase power, PMF |
| Can't connect (WPA3) | Incompatible devices | Check SAE support | Fall back to WPA2/WPA3 transition mode |
| DFS channel unavailable | Radar detected | Check dmesg/logs for radar events | Use non-DFS channels |
| SoftAP + STA conflict | Single radio contention | Check MCC (multi-channel concurrency) | Same channel if possible (SCC) |
| High latency | Buffer bloat / contention | Check queue depths, retry count | Enable WMM, reduce buffer sizes |
| Roaming slow (>1s) | No 802.11r support | Check FT capability | Enable FT, 802.11k/v |
| P2P disconnects | GO/Client role conflict | Check intent values | Force head unit as GO |
| SoftAP won't start | Channel unavailable (DFS) | Check regulatory domain | Use non-DFS channel |
| Poor range | Low TX power / antenna | Check TX power setting, antenna | Increase power, external antenna |

---

## 21. COMPARISON: WiFi vs OTHER WIRELESS

| Feature | WiFi 6 | Bluetooth 5.2 | 5G (Sub-6) | UWB |
|---------|--------|---------------|------------|-----|
| Range | 50-100m | 30-100m | km-scale | 10-30m |
| Data Rate | 9.6 Gbps | 2 Mbps | ~1 Gbps | 27 Mbps |
| Latency | 5-30ms | 10-50ms | 1-10ms | <1ms |
| Power | High (100-500mA) | Low (5-15mA) | High | Low |
| Topology | Infrastructure/P2P | Piconet/Mesh | Cellular | P2P |
| Primary Use | LAN/Internet | Peripherals/Audio | WAN/Mobile | Ranging |
| Automotive | Hotspot/Projection | Audio/Key | Connectivity | Digital Key |
| License | Unlicensed | Unlicensed | Licensed | Unlicensed |

---

## 22. FUTURE: WiFi 8 (802.11bn) AND BEYOND

```
Wi-Fi 8 (802.11bn) — "Ultra High Reliability" (UHR):
  - Target: 2028 release
  - Focus: Reliability + determinism (not just speed)
  - Features being considered:
    • Coordinated MIMO (multiple APs cooperate)
    • Enhanced MLO (more sophisticated link management)
    • Ultra-low latency (<1ms for AR/VR)
    • Better coexistence with 6 GHz incumbents
    • AI/ML-assisted channel/power management
    
Automotive implications:
  - Deterministic latency → suitable for safety functions
  - Coordinated AP → seamless in-vehicle coverage
  - Could replace some wired connections (Ethernet) in vehicle
```

---

## 23. SA8295P WiFi INTEGRATION DEEP DIVE

```
Hardware Path:
  QCA6698AQ ──PCIe Gen3 x1──▶ SA8295P SoC
  
  QCA6698AQ features:
  - WiFi 6E (2×2, 160 MHz, 2.4/5/6 GHz)
  - BT 5.2 (shared with WiFi, internal coexistence)
  - Automotive grade (-40°C to +105°C)
  - Target: AEC-Q100 qualified
  
  Firmware loading (at boot):
  1. PCIe enumeration → QCA6698AQ detected
  2. Driver loads: qcacld-3.0 (or cnss2 platform driver)
  3. Firmware: /lib/firmware/ath11k/QCA6698AQ/...
     - board.bin (calibration, regulatory)
     - amss.bin (firmware binary)
     - m3.bin (M3 coprocessor firmware)
  4. MAC address from NVM or device tree
  5. Interface created: wlan0 (STA), wlan1 (SAP), p2p0 (P2P)
  
  Concurrency:
  ┌─────────────────────────────────────────────┐
  │  wlan0 (STA) → connected to user's home WiFi │
  │  wlan1 (SAP) → passenger hotspot (5 GHz)    │
  │  p2p0 (P2P) → phone projection              │
  │  BT: A2DP + HFP + BLE simultaneous          │
  └─────────────────────────────────────────────┘
  
  MCC vs SCC:
  - SCC (Same Channel Concurrency): All interfaces on same channel
    → Best performance, no time-sharing
  - MCC (Multi-Channel Concurrency): Different channels
    → Time-shared → performance penalty (~50% per interface)
  - Strategy: Try SCC first, fall back to MCC if needed
```

---

## 24. TESTING & CERTIFICATION

### WiFi Alliance Certifications
| Program | Tests |
|---------|-------|
| Wi-Fi CERTIFIED 6 | OFDMA, TWT, MU-MIMO, BSS Color, WPA3 |
| Wi-Fi CERTIFIED 6E | 6 GHz operation, power modes |
| Wi-Fi CERTIFIED 7 | MLO, 320 MHz, 4096-QAM |
| WPA3 | SAE, PMF, 192-bit enterprise |
| Wi-Fi Direct | P2P formation, WPS, service discovery |
| Miracast | Display mirroring compliance |
| Passpoint (Hotspot 2.0) | Seamless hotspot roaming |
| Wi-Fi Aware (NAN) | Proximity-based discovery |

### Automotive-Specific Testing
| Test | Purpose |
|------|---------|
| Temperature cycling | -40°C to +105°C operation |
| EMC/EMI | Electromagnetic compatibility |
| Vibration | Road vibration endurance |
| RF coexistence | BT + WiFi + cellular simultaneous |
| OTA performance | Antenna pattern in vehicle body |
| Regulatory (FCC/CE) | RF emission limits per region |

---

END OF DOCUMENT 01 — MASTER THEORY
