# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 09: WIRELESS PROTOCOLS                                                ║
# ║  WiFi, BLE, 5G/LTE, Zigbee, LoRa, UWB, NFC                               ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Wireless protocols eliminate physical cabling, enabling mobility, flexible deployment, and massive-scale sensor networks. The spectrum ranges from short-range (NFC: cm) to global (satellite: worldwide), with fundamental trade-offs between range, bandwidth, power consumption, and latency.

## PURPOSE
Provide untethered communication for devices ranging from tiny battery-powered sensors (years of operation) to high-speed mobile broadband (multi-Gbps).

## ENGINEERING TRADE-OFFS
```
High Bandwidth ←──────────────────────→ Low Power
    WiFi 7, 5G                              BLE, Zigbee, LoRa

Short Range ←──────────────────────────→ Long Range
    NFC, UWB                                LoRa, Satellite, 5G

Low Latency ←──────────────────────────→ High Capacity
    UWB, 5G URLLC                           LoRa, Sigfox
```

---

## PROTOCOL FAMILY TABLE

| Protocol | Frequency | Range | Speed | Power | Topology | Domain |
|----------|-----------|-------|-------|-------|----------|--------|
| WiFi 6E (802.11ax) | 2.4/5/6 GHz | 100m | 9.6 Gbps | High | Star/Mesh | LAN |
| WiFi 7 (802.11be) | 2.4/5/6 GHz | 100m | 46 Gbps | High | Star/Mesh | LAN |
| Bluetooth 5.3 | 2.4 GHz | 100m | 2 Mbps | Low | P2P/Mesh | PAN |
| BLE (Bluetooth LE) | 2.4 GHz | 50m | 2 Mbps | Very Low | Star/Mesh | IoT |
| 5G NR | Sub-6/mmWave | 1-10km | 20 Gbps | High | Cell | WAN |
| 4G LTE | 700-2600 MHz | 10km | 1 Gbps | High | Cell | WAN |
| Zigbee (802.15.4) | 2.4 GHz | 100m | 250 kbps | Very Low | Mesh | IoT |
| Thread (802.15.4) | 2.4 GHz | 30m | 250 kbps | Very Low | Mesh | Smart home |
| Z-Wave | 868/908 MHz | 100m | 100 kbps | Low | Mesh | Smart home |
| LoRa/LoRaWAN | Sub-GHz | 15km | 50 kbps | Very Low | Star | LPWAN |
| Sigfox | Sub-GHz | 50km | 100 bps | Ultra Low | Star | LPWAN |
| NB-IoT | Licensed LTE | 10km | 250 kbps | Low | Cell | LPWAN |
| UWB (802.15.4z) | 3.1-10.6 GHz | 30m | 27 Mbps | Low | P2P | Ranging |
| NFC | 13.56 MHz | 10cm | 424 kbps | Minimal | P2P | Payment/ID |
| DSRC (802.11p) | 5.9 GHz | 1km | 27 Mbps | Moderate | Ad-hoc | V2X |
| C-V2X (PC5) | 5.9 GHz | 1km | 100+ Mbps | Moderate | Ad-hoc | V2X |
| Satellite (LEO) | Ka/Ku band | Global | 100+ Mbps | High | Star | WAN |

---

## WiFi 6/6E/7 (IEEE 802.11ax/be)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | IEEE 802.11ax (WiFi 6), 802.11be (WiFi 7) |
| Frequency | 2.4 GHz + 5 GHz + 6 GHz (6E/7) |
| Max Speed | WiFi 6: 9.6 Gbps, WiFi 7: 46 Gbps |
| Modulation | OFDMA, up to 4096-QAM (WiFi 7) |
| Channel Width | 20/40/80/160/320 MHz (WiFi 7) |
| MIMO | 8×8 MU-MIMO, 16×16 (WiFi 7) |
| Key Feature | OFDMA (multi-user per frame), BSS Coloring, TWT |
| Use | Home, enterprise, automotive (IVI WiFi) |

### WiFi 7 Key Features
- **320 MHz channels**: Double WiFi 6 maximum → double throughput
- **4096-QAM**: 12 bits/symbol (vs 1024-QAM = 10 bits)
- **MLO (Multi-Link Operation)**: Use multiple bands simultaneously
- **Preamble puncturing**: Use spectrum around interferers
- **16×16 MU-MIMO**: More spatial streams

### WiFi in Automotive (AAOS)
- Passenger internet hotspot (tethered via cellular)
- WiFi Direct for phone mirroring (Android Auto wireless)
- V2I communication (infrastructure)
- OTA updates (parked, home WiFi)

---

## Bluetooth / BLE

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Bluetooth (Classic + Low Energy) |
| Standard | Bluetooth SIG Core Specification |
| Version | 5.4 (2023) |
| Frequency | 2.4 GHz ISM band (79 channels × 1 MHz) |
| Classic Speed | 3 Mbps (EDR) |
| BLE Speed | 2 Mbps (LE 2M PHY) |
| Range | Classic: 10-100m, BLE: 50m typical |
| Power | BLE: µW-level (years on coin cell) |

### Bluetooth Stack
```
┌────────────────────────────────────────┐
│  Application (GATT profiles, A2DP, HFP)│
├────────────────────────────────────────┤
│  Host Stack                             │
│  ├── GAP (Generic Access Profile)      │
│  ├── GATT (Generic Attribute Profile)  │  ← BLE services/characteristics
│  ├── ATT (Attribute Protocol)          │
│  ├── SMP (Security Manager)           │
│  ├── L2CAP (Logical Link Control)     │
│  └── HCI (Host Controller Interface)  │  ← UART/USB/SDIO to chip
├────────────────────────────────────────┤
│  Controller (chip firmware)             │
│  ├── Link Layer (advertising, scanning)│
│  ├── PHY (1M, 2M, Coded)             │
│  └── Radio (2.4 GHz, freq hopping)    │
└────────────────────────────────────────┘
```

### BLE Key Concepts
- **GATT**: Service/Characteristic model (heart rate, battery, etc.)
- **Advertising**: Device broadcasts periodically (discoverable)
- **Connection intervals**: 7.5ms to 4s (trade power vs latency)
- **Coded PHY (LE Coded)**: Long range (4× sensitivity at lower rate)
- **Bluetooth Mesh**: Multi-hop mesh for large-scale IoT (lighting)
- **LE Audio (LC3)**: New codec, multi-stream, broadcast audio (Auracast)

### BLE in Automotive
- Phone-as-key (digital car key)
- Tire pressure sensors (TPMS)
- In-cabin presence detection
- Android Auto initial connection

### Linux Bluetooth
- `net/bluetooth/` — BlueZ kernel support
- `drivers/bluetooth/` — HCI transport drivers
- BlueZ userspace: `bluetoothd`, `bluetoothctl`
- HCI over UART: `hci_uart`, `btattach`

---

## 5G NR (New Radio)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | 5G New Radio |
| Standard | 3GPP Release 15+ |
| Frequency | FR1: 410 MHz–7.125 GHz, FR2 (mmWave): 24.25–52.6 GHz |
| Peak Speed | 20 Gbps DL, 10 Gbps UL |
| Latency | eMBB: ~4ms, URLLC: <1ms |
| Use Cases | eMBB (speed), URLLC (low-latency), mMTC (massive IoT) |

### 5G Service Categories
| Category | Full Name | Use | Requirement |
|----------|-----------|-----|-------------|
| eMBB | Enhanced Mobile Broadband | Streaming, AR/VR | High BW |
| URLLC | Ultra-Reliable Low-Latency | Autonomous driving, remote surgery | <1ms, 99.999% |
| mMTC | Massive Machine-Type Comm | IoT sensors (1M devices/km²) | Low power |

### 5G in Automotive
- **C-V2X (Cellular V2X)**: Vehicle-to-everything over 5G sidelink (PC5)
- **Telematics**: High-bandwidth connectivity for OTA, streaming
- **HD Maps**: Real-time map updates
- **Remote driving**: URLLC enables teleoperation
- **5G modem in SA8295P**: Qualcomm Snapdragon X55/X65 companion

---

## LoRa / LoRaWAN

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Long Range (LoRa PHY), LoRaWAN (MAC) |
| Standard | LoRa Alliance specification |
| Frequency | Sub-GHz (868 MHz EU, 915 MHz US, 433 MHz Asia) |
| Range | Urban: 2-5 km, Rural: 15+ km |
| Speed | 0.3 – 50 kbps |
| Power | Years on battery (10-year coin cell) |
| Topology | Star-of-stars (devices → gateway → server) |
| Modulation | CSS (Chirp Spread Spectrum) |
| Use | Smart city, agriculture, asset tracking, utilities |

### LoRa vs Cellular IoT
| Feature | LoRa | NB-IoT | Cat-M1 |
|---------|------|--------|--------|
| Spectrum | Unlicensed | Licensed | Licensed |
| Cost/device | $2-5 | $5-10 | $10-15 |
| Range | 15 km | 10 km | 10 km |
| Speed | 50 kbps | 250 kbps | 1 Mbps |
| Battery | 10+ years | 10+ years | 5+ years |
| Mobility | Limited | Stationary | Mobile |
| Latency | Seconds | 1-10s | 100ms |
| Infrastructure | Private gateways | Carrier network | Carrier network |

---

## UWB (Ultra-Wideband)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | IEEE 802.15.4z (HRP UWB) |
| Frequency | 3.1 – 10.6 GHz |
| Bandwidth | 500 MHz+ (per channel) |
| Range | 10-30m (indoor) |
| Precision | ±10 cm ranging accuracy |
| Speed | 6.8 – 27.2 Mbps |
| Modulation | Impulse Radio (narrow pulses) |
| Use | Precise location, car keys, AirTag, spatial awareness |

### UWB in Automotive
- **Digital car key**: UWB ranging (precise location → anti-relay attack)
- **Secure vehicle access**: Know which door driver approaches
- **In-cabin child detection**: Precise presence sensing
- **Parking assist**: Ultra-precise distance measurement
- **Car Connectivity Consortium (CCC)**: Digital Key 3.0 standard uses UWB

---

## NFC (Near Field Communication)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | ISO 14443, ISO 18092, NFC Forum |
| Frequency | 13.56 MHz |
| Range | <10 cm (intentionally short for security) |
| Speed | 106/212/424 kbps |
| Power | Target can be passive (powered by reader field) |
| Modes | Reader/Writer, Peer-to-Peer, Card Emulation |
| Use | Payment (NFC pay), transit cards, pairing, access |

---

## AUTOMOTIVE V2X: DSRC vs C-V2X

| Feature | DSRC (802.11p) | C-V2X (PC5 sidelink) |
|---------|----------------|---------------------|
| Standard | IEEE 802.11p / ETSI ITS-G5 | 3GPP Release 14+ |
| Frequency | 5.9 GHz | 5.9 GHz |
| Range | ~300m | ~450m |
| Latency | ~2ms | ~5-10ms (improving) |
| Non-line-of-sight | Weak | Better (HARQ, coding) |
| Evolution | Limited (no clear roadmap) | 5G NR-V2X (R16+) |
| Status | Mandated by some (EU), declining | Growing (5G backing) |
| Advantage | Mature, proven | Cellular evolution path |

---

## COMPARISON: SHORT-RANGE WIRELESS

| Feature | WiFi | BLE | UWB | NFC | Zigbee |
|---------|------|-----|-----|-----|--------|
| Range | 100m | 50m | 30m | 10cm | 100m |
| Speed | 9.6G | 2M | 27M | 424k | 250k |
| Power | High | µW | Low | Passive | µW |
| Topology | Star | Star/Mesh | P2P | P2P | Mesh |
| Best for | Internet | Wearables | Ranging | Payment | Sensors |
| Latency | ms | ms | ns(ranging) | ms | ms |

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | WiFi 7 max channel? | 320 MHz |
| 2 | BLE GATT? | Generic Attribute Profile (services + characteristics) |
| 3 | 5G URLLC latency? | <1ms (ultra-reliable low-latency) |
| 4 | LoRa modulation? | CSS (Chirp Spread Spectrum) |
| 5 | LoRa range? | 15+ km rural, 2-5 km urban |
| 6 | UWB precision? | ±10 cm ranging accuracy |
| 7 | NFC range? | <10 cm (by design, security) |
| 8 | C-V2X vs DSRC? | C-V2X: cellular evolution, better NLOS. DSRC: mature, IEEE |
| 9 | 5G FR2? | mmWave: 24.25-52.6 GHz (high speed, short range) |
| 10 | BLE advertising? | Device broadcasts packets periodically for discovery |
| 11 | WiFi OFDMA? | Orthogonal Frequency Division Multiple Access (multi-user) |
| 12 | Bluetooth frequency? | 2.4 GHz ISM band (79 channels, frequency hopping) |
| 13 | UWB in cars? | Digital key (ranging, anti-relay), child detection |
| 14 | LoRaWAN topology? | Star-of-stars (devices → gateways → network server) |
| 15 | 5G mMTC? | Massive Machine-Type Communication (1M devices/km²) |

---

## INTERVIEW QUESTIONS (5)

**Q1: How does BLE achieve years of battery life while WiFi drains batteries in hours?**
A: BLE design principles: (1) Connection intervals of seconds (device sleeps between), (2) Short packets (max 251 bytes), (3) Fast TX/RX (wake→transmit→sleep in <3ms), (4) Low duty cycle (<1% radio on-time), (5) Simple protocol (no association, fast connection). WiFi: always listening for beacons, high TX power (100mW vs 1mW), large frames, complex CSMA/CA. BLE typical: 10-50 µA average vs WiFi: 50-200 mA active.

**Q2: Explain OFDMA in WiFi 6/7 and why it improves dense environments.**
A: Pre-WiFi 6: OFDM — one device uses all subcarriers per frame. In a room with 50 devices, each waits its turn (high latency). OFDMA divides the channel into Resource Units (RUs) — multiple devices transmit simultaneously on different subcarrier groups within ONE frame. AP schedules which device gets which RU. Result: lower latency in crowded environments, better spectral efficiency for small packets (IoT).

**Q3: Why does UWB provide centimeter-level ranging while WiFi/BLE cannot?**
A: Ranging precision ∝ 1/bandwidth. UWB uses 500 MHz+ bandwidth → time resolution ~2ns → distance precision ~60cm/2 ≈ 30cm, improved to ±10cm with algorithms. WiFi: 20-80 MHz bandwidth → meter-level precision. BLE: 2 MHz → poor. UWB's impulse radio (very short pulses) enables precise Time-of-Flight measurement. Also resistant to multipath (can resolve individual reflections due to high time resolution).

**Q4: Compare LoRaWAN and NB-IoT for a smart city deployment.**
A: LoRaWAN: private network (deploy own gateways), unlicensed spectrum (free), lower cost per device ($2-5), longer battery (simpler protocol), but higher latency (seconds), no guaranteed QoS, 50kbps max. NB-IoT: carrier network (no gateway deployment), licensed spectrum (reliable), better coverage (indoor), lower latency, firmware updates easier, but monthly subscription cost per device, higher chip cost. Choose LoRaWAN for: private, dense sensor networks (parking, agriculture). Choose NB-IoT for: critical monitoring needing guaranteed delivery (utilities, safety).

**Q5: How does C-V2X improve on DSRC for automotive V2X communication?**
A: C-V2X advantages: (1) HARQ retransmission → better reliability at edge of range, (2) Better non-line-of-sight performance (signal processing gains), (3) Evolution path to 5G NR-V2X (Release 16: sidelink enhancements, higher throughput, lower latency), (4) Shared infrastructure with cellular (dual-use modem). DSRC advantage: proven, simpler, no cellular dependency, lower latency (2ms vs 5-10ms initial C-V2X). Industry trend: C-V2X winning (China mandated, US shifted from DSRC mandate).

---

END OF PART 09 — WIRELESS PROTOCOLS
