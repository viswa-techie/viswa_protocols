# Wi-Fi (IEEE 802.11) — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 06 of 08
# Format: Tables, one-liners, instant lookup
# ════════════════════════════════════════════════════════════════════

---

## 1. MCS INDEX TABLE — WiFi 6 (HE) — PER SPATIAL STREAM

| MCS | Modulation | Coding | 20 MHz | 40 MHz | 80 MHz | 160 MHz |
|-----|-----------|--------|--------|--------|--------|---------|
| 0 | BPSK | 1/2 | 8.6 | 17.2 | 36.0 | 72.1 |
| 1 | QPSK | 1/2 | 17.2 | 34.4 | 72.1 | 144.1 |
| 2 | QPSK | 3/4 | 25.8 | 51.6 | 108.1 | 216.2 |
| 3 | 16-QAM | 1/2 | 34.4 | 68.8 | 144.1 | 288.2 |
| 4 | 16-QAM | 3/4 | 51.6 | 103.2 | 216.2 | 432.4 |
| 5 | 64-QAM | 2/3 | 68.8 | 137.6 | 288.2 | 576.5 |
| 6 | 64-QAM | 3/4 | 77.4 | 154.9 | 324.3 | 648.5 |
| 7 | 64-QAM | 5/6 | 86.0 | 172.1 | 360.3 | 720.6 |
| 8 | 256-QAM | 3/4 | 103.2 | 206.5 | 432.4 | 864.7 |
| 9 | 256-QAM | 5/6 | 114.7 | 229.4 | 480.4 | 960.7 |
| 10 | 1024-QAM | 3/4 | 129.0 | 258.1 | 540.4 | 1080.9 |
| 11 | 1024-QAM | 5/6 | 143.4 | 286.8 | 600.5 | 1201.0 |

*Rates in Mbps, 1 spatial stream, 0.8 µs GI. Multiply by Nss for multiple streams.*

---

## 2. MCS TABLE — WiFi 7 (EHT) — ADDITIONS

| MCS | Modulation | Coding | 320 MHz (1SS) |
|-----|-----------|--------|---------------|
| 12 | 4096-QAM | 3/4 | 2882.4 |
| 13 | 4096-QAM | 5/6 | 3202.6 |

*WiFi 7 adds MCS 12-13 (4096-QAM). 320 MHz × 2SS = 6.4 Gbps per link.*

---

## 3. CHANNEL REFERENCE — 5 GHz

| Channel | Center (MHz) | Band | DFS? | Notes |
|---------|-------------|------|------|-------|
| 36 | 5180 | U-NII-1 | No | **Preferred automotive** |
| 40 | 5200 | U-NII-1 | No | |
| 44 | 5220 | U-NII-1 | No | |
| 48 | 5240 | U-NII-1 | No | |
| 52 | 5260 | U-NII-2 | **Yes** | |
| 56 | 5280 | U-NII-2 | **Yes** | |
| 60 | 5300 | U-NII-2 | **Yes** | |
| 64 | 5320 | U-NII-2 | **Yes** | |
| 100 | 5500 | U-NII-2e | **Yes** | |
| 104-140 | 5520-5700 | U-NII-2e | **Yes** | |
| 149 | 5745 | U-NII-3 | No | Available most regions |
| 153 | 5765 | U-NII-3 | No | |
| 157 | 5785 | U-NII-3 | No | |
| 161 | 5805 | U-NII-3 | No | |
| 165 | 5825 | U-NII-3 | No | 20 MHz only |

**Bonding groups (80 MHz):** 36-48, 52-64, 100-112, 116-128, 132-144*, 149-161

---

## 4. CHANNEL REFERENCE — 6 GHz (WiFi 6E/7)

| Width | Channels Available | Notes |
|-------|-------------------|-------|
| 20 MHz | 59 channels (1-233) | All indoor without AFC |
| 40 MHz | 29 channels | |
| 80 MHz | 14 channels | |
| 160 MHz | 7 channels | |
| 320 MHz | 3 channels | WiFi 7 only |

First channels per width: 1(20), 1(40), 1(80), 1(160), 1(320)

---

## 5. FRAME TYPE/SUBTYPE VALUES

| Type | Subtype | Name | Hex (type_subtype) |
|------|---------|------|-------------------|
| 00 (Mgmt) | 0000 | Association Request | 0x00 |
| 00 | 0001 | Association Response | 0x01 |
| 00 | 0010 | Reassociation Request | 0x02 |
| 00 | 0011 | Reassociation Response | 0x03 |
| 00 | 0100 | Probe Request | 0x04 |
| 00 | 0101 | Probe Response | 0x05 |
| 00 | 1000 | Beacon | 0x08 |
| 00 | 1010 | Disassociation | 0x0a |
| 00 | 1011 | Authentication | 0x0b |
| 00 | 1100 | Deauthentication | 0x0c |
| 00 | 1101 | Action | 0x0d |
| 01 (Ctrl) | 1000 | Block Ack Request | 0x18 |
| 01 | 1001 | Block Ack | 0x19 |
| 01 | 1011 | RTS | 0x1b |
| 01 | 1100 | CTS | 0x1c |
| 01 | 1101 | ACK | 0x1d |
| 10 (Data) | 0000 | Data | 0x20 |
| 10 | 0100 | Null (no data) | 0x24 |
| 10 | 1000 | QoS Data | 0x28 |
| 10 | 1100 | QoS Null | 0x2c |

---

## 6. IFS & TIMING VALUES

| Parameter | OFDM (a/g/n/ac/ax) | DSSS (b) |
|-----------|--------------------|---------| 
| Slot Time | 9 µs | 20 µs |
| SIFS | 10 µs | 10 µs |
| DIFS | 28 µs (SIFS + 2×Slot) | 50 µs |
| EIFS | SIFS + ACK_time + DIFS | |
| AIFS[AC_VO] | 10 + 2×9 = 28 µs | |
| AIFS[AC_VI] | 10 + 2×9 = 28 µs | |
| AIFS[AC_BE] | 10 + 3×9 = 37 µs | |
| AIFS[AC_BK] | 10 + 7×9 = 73 µs | |

---

## 7. EDCA PARAMETERS (Default)

| AC | CWmin | CWmax | AIFSN | TXOP Limit |
|----|-------|-------|-------|-----------|
| AC_BK (Background) | 15 | 1023 | 7 | 0 |
| AC_BE (Best Effort) | 15 | 1023 | 3 | 0 |
| AC_VI (Video) | 7 | 15 | 2 | 3.008 ms |
| AC_VO (Voice) | 3 | 7 | 2 | 1.504 ms |

---

## 8. COMMON LINUX/ANDROID WiFi COMMANDS

```bash
# === INTERFACE INFO ===
iw dev                              # List interfaces
iw dev wlan0 info                   # Interface details (freq, type, channel)
iw phy phy0 info                    # PHY capabilities (bands, modes)
ip link show wlan0                  # Interface state
ifconfig wlan0                      # IP address (legacy)

# === SCANNING ===
iw dev wlan0 scan                   # Full scan (requires root)
iw dev wlan0 scan trigger           # Trigger scan
iw dev wlan0 scan dump              # Get cached results
wpa_cli -i wlan0 scan              # Scan via supplicant
wpa_cli -i wlan0 scan_results      # Results via supplicant

# === CONNECTION STATUS ===
iw dev wlan0 link                   # Link info (SSID, signal, rate)
iw dev wlan0 station dump          # Detailed peer stats (TX/RX bytes, MCS, retries)
wpa_cli -i wlan0 status           # Supplicant state (bssid, freq, key_mgmt)

# === CONNECT/DISCONNECT ===
wpa_cli -i wlan0 add_network       # Returns network ID
wpa_cli -i wlan0 set_network 0 ssid '"MyNet"'
wpa_cli -i wlan0 set_network 0 psk '"password"'
wpa_cli -i wlan0 enable_network 0
wpa_cli -i wlan0 select_network 0
wpa_cli -i wlan0 disconnect

# === SOFTAP (HOSTAPD) ===
hostapd /path/to/hostapd.conf      # Start AP
hostapd_cli status                  # AP status
hostapd_cli all_sta                 # List connected clients

# === REGULATORY ===
iw reg get                          # Current regulatory domain
iw reg set US                       # Set country code

# === MONITOR MODE ===
ip link set wlan0 down
iw dev wlan0 set type monitor
ip link set wlan0 up
tcpdump -i wlan0 -w capture.pcap

# === ANDROID-SPECIFIC ===
adb shell cmd wifi status           # WiFi status
adb shell cmd wifi set-wifi-enabled enabled
adb shell dumpsys wifi              # Full WiFi dump
adb shell dumpsys connectivity      # Network selection info
adb shell logcat -s WifiService WifiNative wpa_supplicant
adb shell settings get global wifi_on
adb shell svc wifi enable
```

---

## 9. WIRESHARK DISPLAY FILTERS

```
# Frame types
wlan.fc.type == 0                   # Management frames only
wlan.fc.type == 1                   # Control frames only
wlan.fc.type == 2                   # Data frames only

# Specific subtypes
wlan.fc.type_subtype == 0x08        # Beacons
wlan.fc.type_subtype == 0x04        # Probe Requests
wlan.fc.type_subtype == 0x05        # Probe Responses
wlan.fc.type_subtype == 0x0b        # Authentication
wlan.fc.type_subtype == 0x00        # Association Request
wlan.fc.type_subtype == 0x0c        # Deauthentication
wlan.fc.type_subtype == 0x0a        # Disassociation
wlan.fc.type_subtype == 0x28        # QoS Data

# Security
eapol                               # 4-way handshake / EAPOL
wlan.rsn.pcs.type == 4              # CCMP encryption
wlan.rsn.pcs.type == 8              # GCMP-128
wlan.rsn.pcs.type == 9              # GCMP-256
wlan.wfa.ie.wpa.type == 2           # WPA2 RSN IE

# Filters by address
wlan.addr == aa:bb:cc:dd:ee:ff      # Any address field matches
wlan.sa == aa:bb:cc:dd:ee:ff        # Source address
wlan.da == aa:bb:cc:dd:ee:ff        # Destination address
wlan.bssid == aa:bb:cc:dd:ee:ff     # BSSID

# By SSID
wlan.ssid == "MyNetwork"            # Specific SSID

# Retries
wlan.fc.retry == 1                  # Retransmitted frames

# Signal strength (radiotap)
radiotap.dbm_antsignal < -70        # Weak signal frames

# Data only (no overhead)
wlan.fc.type == 2 && !wlan.fc.type_subtype == 0x24  # Data minus Null
```

---

## 10. HOSTAPD CONFIGURATION TEMPLATE (Automotive SoftAP)

```ini
# /data/vendor/wifi/hostapd/hostapd.conf

interface=wlan1
driver=nl80211
ssid=CarHotspot_5G
hw_mode=a
channel=36
country_code=US

# 802.11n (HT)
ieee80211n=1
ht_capab=[HT40+][SHORT-GI-20][SHORT-GI-40][DSSS_CCK-40]

# 802.11ac (VHT)
ieee80211ac=1
vht_oper_chwidth=1
vht_oper_centr_freq_seg0_idx=42
vht_capab=[SHORT-GI-80][SU-BEAMFORMEE]

# 802.11ax (HE) — if supported
ieee80211ax=1
he_oper_chwidth=1
he_oper_centr_freq_seg0_idx=42

# Security
wpa=2
wpa_key_mgmt=SAE WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP
wpa_passphrase=SecureCarWiFi123
ieee80211w=2
sae_require_mfp=1

# Client management
max_num_sta=10
ap_isolate=1

# WMM / QoS
wmm_enabled=1

# Logging
logger_syslog=-1
logger_syslog_level=2
```

---

## 11. WPA_SUPPLICANT CONFIGURATION TEMPLATE

```ini
# /data/misc/wifi/wpa_supplicant.conf

ctrl_interface=/data/misc/wifi/sockets
update_config=1
country=US
p2p_disabled=0
pmf=1

# Home network (WPA3)
network={
    ssid="HomeNetwork"
    key_mgmt=SAE
    psk="mypassword"
    ieee80211w=2
    priority=10
}

# Office (Enterprise)
network={
    ssid="CorpWiFi"
    key_mgmt=WPA-EAP
    eap=PEAP
    identity="user@corp.com"
    password="password"
    ca_cert="/data/misc/wifi/ca.pem"
    phase2="auth=MSCHAPV2"
    ieee80211w=1
    priority=5
}

# Open network with OWE
network={
    ssid="CoffeeShop"
    key_mgmt=OWE
    ieee80211w=2
    priority=1
}
```

---

## 12. KEY ANDROID WiFi PROPERTIES

| Property | Description | Example |
|----------|-------------|---------|
| `wifi.interface` | Primary STA interface | wlan0 |
| `wifi.concurrent.interface` | Secondary interface | wlan1 |
| `wifi.p2p.interface` | P2P interface | p2p0 |
| `ro.hardware.wifi` | WiFi driver name | qcacld |
| `persist.vendor.wifi.config.bandpreference` | Band preference | 5GHz |
| `vendor.wifi.ftm.daemon` | WiFi RTT daemon | yes |
| `wifi.supplicant_scan_interval` | Scan interval (sec) | 15 |

---

## 13. THROUGHPUT CALCULATION FORMULA

```
Throughput = Nsd × Nbpscs × R × Nss / (Tsym + TGI)

Where:
  Nsd    = Number of data subcarriers (depends on BW)
  Nbpscs = Bits per subcarrier per symbol (depends on QAM)
  R      = Coding rate (1/2, 2/3, 3/4, 5/6)
  Nss    = Number of spatial streams
  Tsym   = Symbol duration (12.8 µs for HE)
  TGI    = Guard interval (0.8 / 1.6 / 3.2 µs)

Example: WiFi 6, 80 MHz, 2×2 MIMO, MCS 11, 0.8 µs GI
  Nsd = 980, Nbpscs = 10 (1024-QAM), R = 5/6, Nss = 2
  Rate = 980 × 10 × (5/6) × 2 / (12.8 + 0.8) µs
       = 16333.3 / 13.6 × 10^6
       = 1201 Mbps (theoretical max)
  Real-world: ~600-800 Mbps (50-65% efficiency)
```

---

## 14. OFDMA RU SIZE TABLE

| RU Size (tones) | Bandwidth (~) | Max Users per 20 MHz | Use Case |
|-----------------|---------------|---------------------|----------|
| 26 | 2 MHz | 9 | IoT, ACK, small packets |
| 52 | 4 MHz | 4 | Web browsing |
| 106 | 8 MHz | 2 | Video streaming |
| 242 | 20 MHz | 1 | Bulk data (legacy equivalent) |
| 484 | 40 MHz | — | High bandwidth (in 40+ MHz channels) |
| 996 | 80 MHz | — | Maximum single-user (80 MHz) |
| 2×996 | 160 MHz | — | Maximum single-user (160 MHz) |
| 4×996 | 320 MHz | — | WiFi 7 maximum |

---

## 15. WiFi SECURITY QUICK REFERENCE

| Security | Encryption | Key Exchange | Year | Status |
|----------|-----------|-------------|------|--------|
| Open | None | None | 1997 | Insecure |
| WEP | RC4 (40/104-bit) | Shared Key | 1997 | **BROKEN** |
| WPA | TKIP (RC4) | PSK or 802.1X | 2003 | Deprecated |
| WPA2-Personal | AES-CCMP (128) | PSK (4-way) | 2004 | Acceptable |
| WPA2-Enterprise | AES-CCMP (128) | 802.1X/EAP | 2004 | Good |
| WPA3-Personal | AES-CCMP (128) | SAE (4-way) | 2018 | **Recommended** |
| WPA3-Enterprise | AES-GCMP (256) | 802.1X/EAP-TLS | 2018 | **Best** |
| OWE | AES-CCMP (128) | DH (no auth) | 2018 | For open nets |

---

## 16. COMMON ERROR CODES & REASONS

| Code/Reason | Meaning | Common Cause |
|-------------|---------|--------------|
| Status 12 | Association denied (no room) | AP at max clients |
| Status 13 | Not authenticated | Out-of-order frames |
| Status 15 | 4-way handshake timeout | Wrong password / PMK mismatch |
| Status 17 | Association denied (capability) | Capability mismatch (HT/VHT) |
| Reason 1 | Unspecified | Generic error |
| Reason 2 | Previous auth no longer valid | AP restarted / expired |
| Reason 3 | Deauth: STA leaving | STA disconnecting |
| Reason 4 | Disassoc: inactivity | No keepalive |
| Reason 6 | Class 2 frame from non-auth STA | STA not authenticated yet |
| Reason 7 | Class 3 frame from non-assoc STA | STA not associated yet |
| Reason 15 | 4-way handshake timeout | Key exchange failed |
| Reason 16 | Group key handshake timeout | GTK rotation failed |
| WRONG_KEY | wpa_supplicant | Incorrect PSK/passphrase |
| FREQ_CONFLICT | Driver | MCC not supported for requested config |

---

## 17. POWER LEVELS & RANGE ESTIMATES

| Scenario | TX Power | Expected Range | Notes |
|----------|----------|---------------|-------|
| Indoor AP (2.4 GHz) | 20 dBm (100 mW) | 35-50 m | Through walls |
| Indoor AP (5 GHz) | 23 dBm (200 mW) | 15-30 m | Less penetration |
| Outdoor AP (5 GHz) | 30 dBm (1 W) | 100-300 m | Line of sight |
| Automotive SAP | 17-20 dBm | 10-30 m | Inside cabin focus |
| WiFi Direct (P2P) | 20 dBm | 30-50 m | Phone to head unit |
| Client (phone) | 15-17 dBm | — | Limited by device |

**RSSI interpretation:**
| RSSI | Quality | Expected Performance |
|------|---------|---------------------|
| > -50 dBm | Excellent | Maximum MCS, full throughput |
| -50 to -65 | Good | High MCS, reliable |
| -65 to -75 | Fair | Medium MCS, some retries |
| -75 to -85 | Poor | Low MCS, many retries |
| < -85 dBm | Unusable | Disconnection likely |

---

## 18. AUTOMOTIVE WiFi DECISION MATRIX

| Use Case | Mode | Band | Channel | Security | Priority |
|----------|------|------|---------|----------|----------|
| Passenger Hotspot | SAP | 5 GHz | 36-48 | WPA3-SAE | Medium |
| Phone Projection (AA) | P2P GO | 5 GHz | Same as SAP | WPA2-PSK | High |
| OTA Download | STA | Any | Auto | WPA2/3 | Low |
| Workshop Diagnostics | SAP | 5 GHz | 36 | WPA2-PSK | Medium |
| V2X (future) | IBSS/Mesh | 5.9 GHz | 172-184 | SAE/Open | Critical |
| Rear Seat Entertainment | SAP | 5/6 GHz | 36-48 | WPA3-SAE | Medium |

---

## 19. KEY KERNEL/DRIVER PATHS (Qualcomm Automotive)

```
# Driver source
vendor/qcom/opensource/wlan/qcacld-3.0/

# Firmware
/vendor/firmware/wlan/qca_cld/
  ├── WCNSS_qcom_cfg.ini          # Driver config (features, thresholds)
  ├── wlan_mac.bin                 # MAC address
  └── bdwlan.bin                   # Board data (regulatory, calibration)

# Kernel config
CONFIG_QCA_CLD_WLAN=m
CONFIG_CFG80211=y
CONFIG_NL80211_TESTMODE=y
CONFIG_WLAN=y

# Android HAL
hardware/interfaces/wifi/aidl/
vendor/qcom/proprietary/wigig/     # WiFi HAL vendor impl

# wpa_supplicant
external/wpa_supplicant_8/
  ├── wpa_supplicant/
  └── hostapd/

# Runtime paths
/data/misc/wifi/                    # wpa_supplicant runtime
/data/vendor/wifi/hostapd/         # hostapd runtime
/sys/class/net/wlan0/              # Network interface sysfs
/sys/module/wlan/parameters/       # Module parameters
/proc/net/wireless                  # Legacy wireless stats
```

---

## 20. ONE-PAGE PROTOCOL SUMMARY

```
╔══════════════════════════════════════════════════════════════════╗
║  WiFi (IEEE 802.11) — AT A GLANCE                               ║
╠══════════════════════════════════════════════════════════════════╣
║  Bands:     2.4 GHz | 5 GHz | 6 GHz                            ║
║  Access:    CSMA/CA (listen before talk + random backoff)       ║
║  Modulation: OFDM (subcarriers), QAM (up to 4096-QAM)         ║
║  MIMO:      Up to 16 spatial streams (WiFi 7)                   ║
║  Max BW:    320 MHz (WiFi 7, 6 GHz)                            ║
║  Max Rate:  46 Gbps (WiFi 7 theoretical)                       ║
║  Range:     10-100 m indoor (band/power dependent)             ║
║  Security:  WPA3 (SAE + AES-CCMP/GCMP + PMF)                  ║
║  Key features per generation:                                    ║
║    WiFi 4: MIMO + channel bonding                               ║
║    WiFi 5: MU-MIMO + 80/160 MHz + 256-QAM                     ║
║    WiFi 6: OFDMA + TWT + BSS Color + 1024-QAM                 ║
║    WiFi 7: MLO + 320 MHz + 4096-QAM + preamble puncturing     ║
║  Automotive: QCA6698AQ, STA+SAP+P2P, 5 GHz preferred          ║
║  Stack: App→WifiService→wpa_supplicant→nl80211→driver→FW      ║
╚══════════════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 06 — CHEATSHEET & QUICK REFERENCE
