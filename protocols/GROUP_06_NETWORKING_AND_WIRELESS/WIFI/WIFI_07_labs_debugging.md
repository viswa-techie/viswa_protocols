# Wi-Fi (IEEE 802.11) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: Wi-Fi (802.11) | Document: 07 of 08
# Format: Progressive labs + real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: PROGRESSIVE LABS

---

## LAB 1: WiFi Scanning & Network Discovery

**Objective:** Discover available networks, understand scan results, compare passive vs active scanning.

**Setup:**
- Linux/Android device with WiFi (or car head unit with shell access)
- Multiple WiFi networks in range

**Steps:**

```bash
# 1. Check WiFi interface exists
iw dev
# Expected: wlan0 (or similar)

# 2. Ensure interface is up
ip link set wlan0 up

# 3. Perform a full scan
iw dev wlan0 scan | head -100

# 4. Parse key fields from scan results
iw dev wlan0 scan | grep -E "SSID:|freq:|signal:|RSN:|BSS " | head -50

# Sample output:
# BSS aa:bb:cc:dd:ee:ff(on wlan0)
#   freq: 5180          ← Channel 36
#   signal: -45.00 dBm  ← Excellent signal
#   SSID: HomeNetwork
#   RSN: Version: 1
#     Group cipher: CCMP
#     Pairwise ciphers: CCMP
#     Authentication suites: SAE  ← WPA3!

# 5. Find non-overlapping 2.4 GHz networks
iw dev wlan0 scan | grep "freq: 24" | sort | uniq -c | sort -rn
# Shows channel congestion in 2.4 GHz

# 6. List only 5 GHz networks
iw dev wlan0 scan | grep -B5 "freq: 5[0-9]" | grep "SSID:"

# 7. Check for DFS channels in use
iw dev wlan0 scan | grep -E "freq: (52[0-9]|5[3-6][0-9]|5700)" 

# 8. Via wpa_supplicant (Android)
wpa_cli -i wlan0 scan
sleep 3
wpa_cli -i wlan0 scan_results
```

**Analysis Questions:**
1. Which channels are most congested in your environment?
2. What security types do you see (Open, WPA2, WPA3)?
3. Are any DFS channels being used?
4. What's the strongest signal you can find? What determines this?

---

## LAB 2: WiFi Connection & Authentication Monitoring

**Objective:** Connect to a network and observe the full connection lifecycle.

**Steps:**

```bash
# 1. Start monitoring wpa_supplicant events
# Terminal 1: Watch supplicant events
wpa_cli -i wlan0 -a /dev/stdout &

# Terminal 2: Connect to network
wpa_cli -i wlan0 add_network
# Returns: 0 (network id)
wpa_cli -i wlan0 set_network 0 ssid '"TestNetwork"'
wpa_cli -i wlan0 set_network 0 psk '"MyPassword123"'
wpa_cli -i wlan0 select_network 0

# 2. Observe events (Terminal 1 output):
# <3>CTRL-EVENT-SCAN-STARTED
# <3>CTRL-EVENT-SCAN-RESULTS
# <3>Trying to associate with aa:bb:cc:dd:ee:ff (SSID='TestNetwork' freq=5180 MHz)
# <3>Associated with aa:bb:cc:dd:ee:ff
# <3>CTRL-EVENT-CONNECTED - Connection to aa:bb:cc:dd:ee:ff completed

# 3. Check connection details
iw dev wlan0 link
# Output:
# Connected to aa:bb:cc:dd:ee:ff (on wlan0)
#   SSID: TestNetwork
#   freq: 5180
#   RX: 1234567 bytes
#   TX: 567890 bytes
#   signal: -52 dBm
#   tx bitrate: 866.7 MBit/s VHT-MCS 9 80MHz short GI VHT-NSS 2

# 4. Detailed station info
iw dev wlan0 station dump
# Shows: inactive time, rx/tx bytes, signal, MCS, retries, aggregation

# 5. Check IP assignment
ip addr show wlan0
# Should show DHCP-assigned IP

# 6. Verify connectivity
ping -c 5 8.8.8.8
ping -c 5 google.com
```

**Expected Connection Sequence:**
```
Scanning → Auth → Assoc → 4-Way Handshake → DHCP → Connected
(~50ms)   (~10ms) (~10ms) (~40ms)          (~500ms)
Total: ~600-800ms typical
```

---

## LAB 3: SoftAP (Hotspot) Configuration

**Objective:** Set up a SoftAP, connect clients, monitor traffic.

**Steps:**

```bash
# 1. Create hostapd configuration
cat > /tmp/hostapd_test.conf << 'EOF'
interface=wlan1
driver=nl80211
ssid=Lab_Hotspot_5G
hw_mode=a
channel=36
country_code=US

# 802.11n
ieee80211n=1
ht_capab=[HT40+][SHORT-GI-20][SHORT-GI-40]

# 802.11ac
ieee80211ac=1
vht_oper_chwidth=1
vht_oper_centr_freq_seg0_idx=42
vht_capab=[SHORT-GI-80]

# Security
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP
wpa_passphrase=LabTest12345

# Client management
max_num_sta=8
ap_isolate=1

# WMM
wmm_enabled=1
EOF

# 2. Start SoftAP
hostapd /tmp/hostapd_test.conf -B

# 3. Assign IP and start DHCP
ip addr add 192.168.43.1/24 dev wlan1
ip link set wlan1 up

# Start dnsmasq (DHCP + DNS)
dnsmasq --interface=wlan1 \
         --dhcp-range=192.168.43.10,192.168.43.50,24h \
         --no-daemon &

# 4. Enable NAT (internet sharing from eth0/rmnet0)
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i wlan1 -o eth0 -j ACCEPT
iptables -A FORWARD -i eth0 -o wlan1 -m state --state RELATED,ESTABLISHED -j ACCEPT

# 5. Connect a phone to "Lab_Hotspot_5G"

# 6. Monitor connected clients
hostapd_cli all_sta
iw dev wlan1 station dump

# 7. Check client traffic
iptables -L FORWARD -v -n     # See byte counts
tcpdump -i wlan1 -c 20        # See packets
```

---

## LAB 4: WiFi Direct (P2P) Connection

**Objective:** Establish P2P connection, understand GO negotiation.

**Steps:**

```bash
# 1. Enable P2P in wpa_supplicant
wpa_cli -i wlan0 p2p_find
# Discovers nearby P2P devices

# 2. List discovered peers
wpa_cli -i wlan0 p2p_peers
# Returns MAC addresses of P2P-capable devices

# 3. Get peer details
wpa_cli -i wlan0 p2p_peer <MAC_ADDRESS>
# Shows: device name, config methods, GO intent

# 4. Connect with PBC (Push Button Config)
wpa_cli -i wlan0 p2p_connect <PEER_MAC> pbc go_intent=15
# go_intent=15 forces us to be GO

# 5. Watch for connection events
# <3>P2P-GO-NEG-SUCCESS
# <3>P2P-GROUP-STARTED p2p-wlan0-0 GO ssid="DIRECT-xx" freq=5180 passphrase="..."
# <3>AP-STA-CONNECTED <peer_mac>

# 6. Check P2P group status
iw dev p2p-wlan0-0 info
wpa_cli -i p2p-wlan0-0 status

# 7. Check peer connection
iw dev p2p-wlan0-0 station dump

# 8. Assign IP (GO provides DHCP)
ip addr add 192.168.49.1/24 dev p2p-wlan0-0
# Peer gets 192.168.49.x via DHCP

# 9. Test data transfer
iperf3 -s                     # On GO
iperf3 -c 192.168.49.1       # On Client

# 10. Disconnect
wpa_cli -i wlan0 p2p_group_remove p2p-wlan0-0
```

---

## LAB 5: Throughput Measurement & Optimization

**Objective:** Measure WiFi throughput, identify bottlenecks, optimize.

**Steps:**

```bash
# 1. Setup iperf3 server (on connected network)
iperf3 -s -p 5201

# 2. Basic TCP test (single stream)
iperf3 -c <SERVER_IP> -t 30 -i 1
# Record: avg bandwidth, retransmits

# 3. Multi-stream TCP (saturate airtime)
iperf3 -c <SERVER_IP> -t 30 -i 1 -P 4
# 4 parallel streams → better utilization

# 4. UDP test (raw PHY capacity)
iperf3 -c <SERVER_IP> -t 30 -i 1 -u -b 1G
# Record: bandwidth, jitter, packet loss

# 5. Reverse direction (download)
iperf3 -c <SERVER_IP> -t 30 -R

# 6. Check current PHY rate during test
watch -n 1 'iw dev wlan0 station dump | grep -E "signal|tx bitrate|rx bitrate"'

# 7. Channel utilization during test
iw dev wlan0 survey dump | grep -A5 "in use"
# "channel busy time" should correlate with throughput

# 8. Optimization experiments:
# a) Change from 40 MHz to 80 MHz (if AP supports)
# b) Move closer to AP (higher MCS)
# c) Disable other WiFi users (reduced contention)
# d) Switch to 5 GHz if on 2.4 GHz
# e) Enable A-MPDU if disabled

# 9. Calculate efficiency
# Efficiency = Measured throughput / PHY data rate
# Expected: 50-70% (good), <40% (problem exists)
# Example: TX bitrate 866 Mbps, measured 520 Mbps → 60% efficiency ✓
```

---

## LAB 6: Monitor Mode & Packet Capture

**Objective:** Capture WiFi frames, analyze protocol exchanges.

**Steps:**

```bash
# 1. Create monitor interface (if supported)
iw dev wlan0 interface add mon0 type monitor
ip link set mon0 up

# OR: Switch existing interface to monitor
ip link set wlan0 down
iw dev wlan0 set type monitor
ip link set wlan0 up

# 2. Set specific channel (to focus capture)
iw dev mon0 set channel 36 HT40+

# 3. Capture with tcpdump
tcpdump -i mon0 -w /tmp/wifi_capture.pcap -c 10000

# 4. Filter during capture
tcpdump -i mon0 'type mgt subtype beacon' -c 10  # Only beacons
tcpdump -i mon0 'type mgt subtype probe-req'      # Probe requests
tcpdump -i mon0 'ether host aa:bb:cc:dd:ee:ff'    # Specific device

# 5. Analyze with tshark (Wireshark CLI)
tshark -r /tmp/wifi_capture.pcap -Y "wlan.fc.type_subtype == 0x08" \
  -T fields -e wlan.ssid -e radiotap.dbm_antsignal -e wlan.bssid | head

# 6. Count frame types
tshark -r /tmp/wifi_capture.pcap -Y "wlan" \
  -T fields -e wlan.fc.type_subtype | sort | uniq -c | sort -rn

# 7. Extract 4-way handshake
tshark -r /tmp/wifi_capture.pcap -Y "eapol" -V

# 8. Detect deauth attacks
tshark -r /tmp/wifi_capture.pcap -Y "wlan.fc.type_subtype == 0x0c" | wc -l
# Many deauths from same source = possible attack

# 9. Return to managed mode when done
ip link set mon0 down
iw dev mon0 del
# OR:
ip link set wlan0 down
iw dev wlan0 set type managed
ip link set wlan0 up
```

---

## LAB 7: Roaming Analysis

**Objective:** Monitor and trigger roaming events, measure roaming time.

**Steps:**

```bash
# Setup: Two APs with same SSID on different channels, 802.11r enabled

# 1. Connect to AP1
wpa_cli -i wlan0 status
# Note: bssid, freq

# 2. Monitor events in background
wpa_cli -i wlan0 -a /dev/stdout > /tmp/roam_events.log &

# 3. Check roaming candidates (802.11k)
wpa_cli -i wlan0 wnm_bss_query 0
# AP sends neighbor report (if 11k enabled)

# 4. Walk toward AP2 / away from AP1
# Watch signal dropping:
watch -n 0.5 'iw dev wlan0 link | grep signal'

# 5. Observe roaming event in log:
# <3>CTRL-EVENT-SIGNAL-CHANGE above=0 signal=-75 noise=-90
# <3>SME: Trying to authenticate with bb:cc:dd:ee:ff:00
# <3>Trying to associate with bb:cc:dd:ee:ff:00
# <3>Associated with bb:cc:dd:ee:ff:00
# <3>CTRL-EVENT-CONNECTED

# 6. Measure roaming time
grep -A2 "Trying to authenticate" /tmp/roam_events.log
grep "CONNECTED" /tmp/roam_events.log
# Time difference = roaming duration
# With 802.11r: <50ms
# Without 802.11r: 200-500ms

# 7. Force roaming (for testing)
wpa_cli -i wlan0 roam <TARGET_BSSID>

# 8. Check if FT (Fast Transition) was used
wpa_cli -i wlan0 status | grep key_mgmt
# "FT-PSK" or "FT-SAE" = Fast Transition active

# 9. Scan and compare signals
wpa_cli -i wlan0 scan_results | grep <SSID>
# Compare signal levels of both APs
```

---

# PART B: DEBUG SCENARIOS

---

## DEBUG 1: Slow WiFi Throughput

**Symptoms:** User reports internet slow on car hotspot. iperf shows 30 Mbps instead of expected 400+ Mbps.

**Diagnosis:**

```bash
# Step 1: Check PHY rate
iw dev wlan0 station dump | grep "tx bitrate"
# FINDING: "tx bitrate: 72.2 MBit/s MCS 7"
# PROBLEM: Only getting MCS 7 on 20 MHz (should be MCS 9, 80 MHz)

# Step 2: Check why bandwidth limited
iw dev wlan0 info | grep "channel"
# FINDING: "channel 36 (5180 MHz), width: 20 MHz"
# PROBLEM: Only 20 MHz instead of 80 MHz!

# Step 3: Check why 20 MHz only
iw dev wlan0 link
# Check if AP advertises HT40/VHT80

# Step 4: Check for MCC (multi-channel concurrency)
iw dev wlan1 info | grep "channel"  # SoftAP
# FINDING: wlan0 on ch36 (STA), wlan1 on ch149 (SAP)
# PROBLEM: MCC active → time sharing → 50% airtime → forced to 20 MHz

# ROOT CAUSE: STA and SAP on different channels (MCC)
# The driver limits STA to 20 MHz during MCC to reduce switching overhead

# FIX:
# Option A: Move SAP to same channel as STA (SCC)
hostapd_cli chan_switch 1 5180 ht sec_channel_offset=1 center_freq1=5210 bandwidth=80

# Option B: If STA connected first, restart SAP on STA's channel
# Modify hostapd.conf: channel=36, then restart

# VERIFICATION:
iperf3 -c <SERVER_IP> -t 10 -P 4
# Should now show 400+ Mbps
```

---

## DEBUG 2: WiFi Disconnections (Intermittent)

**Symptoms:** Head unit STA disconnects from home WiFi every 10-30 minutes.

**Diagnosis:**

```bash
# Step 1: Check disconnect reason
dmesg | grep -i "disassoc\|deauth\|disconnect"
# FINDING: "deauthenticated from AP (Reason: 4=DISASSOC_DUE_TO_INACTIVITY)"

# Step 2: Check if keepalive is working
wpa_cli -i wlan0 status | grep "wpa_state"
# If COMPLETED but AP thinks we're inactive → keepalive issue

# Step 3: Check power save
iw dev wlan0 get power_save
# FINDING: "Power save: on"
# POSSIBLE: Power save causing missed beacons → AP thinks STA left

# Step 4: Check DTIM / beacon loss
dmesg | grep -i "beacon"
# FINDING: "beacon loss detected" → STA missing beacons

# ROOT CAUSE: Aggressive power save + high DTIM period
# STA sleeps through multiple beacons → AP assumes STA left → deauth

# FIX:
# Option A: Disable power save (automotive: power not critical)
iw dev wlan0 set power_save off

# Option B: Send null data frames as keepalive (driver parameter)
echo 30 > /sys/module/wlan/parameters/keepalive_interval

# Option C: Reduce DTIM period on AP (if you control it)
# In hostapd.conf: dtim_period=1

# PERMANENT FIX (automotive):
# In WCNSS_qcom_cfg.ini:
# gEnablePowerSave=0
# gMaxPsPoll=0
# gDTIMMultiplier=1
```

---

## DEBUG 3: SoftAP Won't Start

**Symptoms:** SoftAP (passenger hotspot) fails to start after boot.

**Diagnosis:**

```bash
# Step 1: Check hostapd status
systemctl status hostapd
# OR on Android:
logcat -s hostapd WifiService SoftApManager | tail -50

# Step 2: Check interface exists
iw dev | grep wlan1
# FINDING: wlan1 not present!

# Step 3: Check if driver created AP interface
iw phy phy0 info | grep "AP"
# Verify AP mode is in "Supported interface modes"

# Step 4: Check concurrency limits
iw phy phy0 info | grep -A10 "valid interface combinations"
# FINDING: "Allow: #STA ≤ 1, #AP ≤ 1" (but only in certain combinations)

# Step 5: Check firmware mode
cat /sys/module/wlan/parameters/fwpath
# If wrong mode → FW doesn't support AP interface

# Step 6: Try manual interface creation
iw dev wlan0 interface add wlan1 type __ap
# ERROR: "Operation not supported" → FW/driver doesn't allow this combination

# ROOT CAUSE: Firmware not loaded in concurrent mode (STA+AP)
# Driver started in STA-only mode

# FIX:
# 1. Ensure proper FW path for concurrency
echo "ap,sta" > /sys/module/wlan/parameters/fwpath

# 2. Reload driver
rmmod wlan
modprobe wlan

# 3. Or fix in init script (Android):
# In init.wifi.rc:
# write /sys/module/wlan/parameters/fwpath "ap,sta"

# 4. Verify
iw dev  # Both wlan0 and wlan1 should appear
```

---

## DEBUG 4: WiFi/BT Coexistence Issues

**Symptoms:** BT audio (A2DP) stutters when WiFi throughput test running on 2.4 GHz.

**Diagnosis:**

```bash
# Step 1: Confirm both on 2.4 GHz
iw dev wlan0 info | grep channel
# FINDING: channel 6 (2437 MHz) — 2.4 GHz!

hciconfig hci0
# BT is always 2.4 GHz (can't change)

# Step 2: Check BT AFH (Adaptive Frequency Hopping)
btmon | grep -i "channel map"
# Check if BT is avoiding WiFi's channel

# Step 3: Check PTA arbitration
dmesg | grep -i "coex\|pta\|arbitr"
# Look for PTA decisions / priority grants

# Step 4: Measure BT quality during WiFi load
# Play A2DP audio + run WiFi iperf simultaneously
iperf3 -c <server> -t 30 -b 50M &
# Listen for audio glitches

# ROOT CAUSE: WiFi on 2.4 GHz conflicts with BT A2DP
# Even with PTA, continuous WiFi TX starves BT of airtime

# FIX (Priority order):
# 1. Move WiFi to 5 GHz (BEST - eliminates conflict)
wpa_cli -i wlan0 disconnect
# Reconfigure to 5 GHz network
wpa_cli -i wlan0 set_network 0 freq_list "5180 5200 5220 5240"
wpa_cli -i wlan0 select_network 0

# 2. If 2.4 GHz unavoidable: Limit WiFi TX duty cycle
# In WCNSS_qcom_cfg.ini:
# gCoexPTADutyCycle=50  # WiFi gets max 50% airtime when BT active

# 3. Ensure BT has priority for SCO (voice)
# Driver should auto-prioritize: BT SCO > WiFi > BT ACL(music)

# VERIFICATION:
# After moving to 5 GHz:
iperf3 -c <server> -t 30  # Full speed WiFi
# Simultaneously play BT audio → no stuttering
```

---

## DEBUG 5: 4-Way Handshake Failure (Wrong Password)

**Symptoms:** Connection attempt fails repeatedly. `wpa_supplicant` log shows handshake timeout.

**Diagnosis:**

```bash
# Step 1: Watch supplicant events
wpa_cli -i wlan0 log_level DEBUG
logcat -s wpa_supplicant | grep -i "key\|handshake\|auth\|WPA"

# Expected log pattern for wrong password:
# <3>Trying to associate with aa:bb:cc:dd:ee:ff
# <3>Associated with aa:bb:cc:dd:ee:ff
# <3>CTRL-EVENT-EAP-STARTED
# <3>WPA: 4-Way Handshake failed - pre-shared key may be incorrect
# <3>CTRL-EVENT-DISCONNECTED reason=15 locally_generated=1

# Step 2: Confirm reason code
# Reason 15 = "4-way handshake timeout"
# locally_generated=1 means OUR side detected the failure

# Step 3: Check password encoding
wpa_cli -i wlan0 get_network 0 psk
# Verify password is correct (watch for special characters, encoding)

# Step 4: Verify security mode matches
iw dev wlan0 scan | grep -A10 "SSID: <target>"
# Check: RSN (WPA2) vs WPA vs SAE (WPA3)
# If AP is WPA3 (SAE) but we're trying WPA2-PSK → mismatch

# ROOT CAUSE OPTIONS:
# A: Wrong password
# B: Security type mismatch (WPA2 vs WPA3)
# C: PMF mismatch (AP requires PMF but STA doesn't support)

# FIX:
# For WPA3:
wpa_cli -i wlan0 set_network 0 key_mgmt SAE
wpa_cli -i wlan0 set_network 0 ieee80211w 2  # PMF required
wpa_cli -i wlan0 set_network 0 psk '"correct_password"'
wpa_cli -i wlan0 select_network 0
```

---

## DEBUG 6: DFS Channel Vacation (Radar Detection)

**Symptoms:** SoftAP suddenly switches channels, clients briefly disconnect.

**Diagnosis:**

```bash
# Step 1: Check dmesg for DFS events
dmesg | grep -i "radar\|dfs\|cac\|freq.*change"
# FINDING: "DFS radar detected on freq 5260 MHz"
#          "DFS channel switch to freq 5180"

# Step 2: Check regulatory events
iw event -t | grep -i "reg\|radar"
# Real-time monitoring of DFS events

# Step 3: Check current channel (after switch)
iw dev wlan1 info
# Now on channel 36 (5180) instead of 52 (5260)

# Step 4: Check if clients reconnected
hostapd_cli all_sta

# EXPLANATION:
# Channel 52 is DFS (radar sharing)
# Driver detected radar signature → mandatory vacation (10 sec to vacate)
# hostapd switched to non-DFS channel (36)
# Some clients may take 1-5 seconds to follow

# FIX (Prevention):
# 1. Use non-DFS channels for automotive SoftAP
# In hostapd.conf: channel=36 (or 40, 44, 48, 149-161)

# 2. Configure ACS to exclude DFS
# acs_exclude_dfs=1
# OR: chanlist=36 40 44 48 149 153 157 161

# 3. If DFS required: Enable background CAC (pre-check alternative channels)
# hostapd.conf: background_radar_detection=1
```

---

## DEBUG 7: Android Auto Wireless P2P Connection Failure

**Symptoms:** Phone and head unit discover each other but WiFi Direct connection fails.

**Diagnosis:**

```bash
# Step 1: Check P2P state
wpa_cli -i wlan0 p2p_peers
# Verify peer is discovered

# Step 2: Enable verbose P2P logging
wpa_cli -i wlan0 log_level DEBUG
logcat -s WifiP2pService wpa_supplicant | grep -i "p2p\|group\|nego"

# Step 3: Watch GO negotiation
# Expected sequence:
# P2P-FIND-STOPPED
# P2P-GO-NEG-REQUEST aa:bb:cc:dd:ee:ff dev_passwd_id=4
# P2P-GO-NEG-SUCCESS role=GO freq=5180
# P2P-GROUP-STARTED p2p-wlan0-0 GO ssid="DIRECT-xx"

# FAILURE patterns:
# A: "P2P-GO-NEG-FAILURE status=1" → config method mismatch
# B: "P2P-GO-NEG-FAILURE status=7" → rejected by peer
# C: "P2P-GROUP-FORMATION-FAILURE" → timeout during WPS

# Step 4: Check for frequency conflict
iw dev wlan0 info  # STA channel
# If STA on DFS channel and P2P can't use DFS → negotiation fails

# Step 5: Check concurrency support
iw phy phy0 info | grep -A10 "valid interface combinations"
# Need: #STA ≤ 1, #P2P-client ≤ 1 OR #P2P-GO ≤ 1

# ROOT CAUSE: Frequency conflict
# STA connected on channel 52 (DFS), P2P can't use DFS → no valid channel

# FIX:
# 1. Force P2P to use same channel as STA (SCC)
wpa_cli -i wlan0 p2p_connect <MAC> pbc freq=5260 go_intent=15

# 2. Or move STA to non-DFS first
wpa_cli -i wlan0 set_network 0 freq_list "5180 5200 5220 5240"
wpa_cli -i wlan0 reassociate

# 3. Automotive config: Force GO on preferred channel
# In p2p_supplicant.conf:
# p2p_oper_channel=36
# p2p_pref_chan=81:36
```

---

## DEBUG 8: Driver/Firmware Crash Recovery

**Symptoms:** WiFi completely stops working. All connections drop. Interface disappears.

**Diagnosis:**

```bash
# Step 1: Check kernel log
dmesg | tail -50
# FINDING: "wlan: SSR detected"  or "qcacld: Firmware assert"
#          "qcacld: WLAN driver recovery in progress"

# Step 2: Check interface presence
ip link show wlan0
# ERROR: "Device "wlan0" does not exist." → driver crashed

# Step 3: Check driver state
cat /sys/module/wlan/parameters/fwpath
lsmod | grep wlan
# If module still loaded but interface gone → FW crash + SSR in progress

# Step 4: Check SSR (SubSystem Restart) recovery
dmesg | grep -i "ssr\|subsys.*restart\|recovery"
# "WLAN recovery started"
# "WLAN recovery completed" (good → automatic recovery)

# Step 5: Wait for automatic recovery (typically 5-15 seconds)
sleep 15
ip link show wlan0
iw dev  # Check if interface is back

# IF AUTOMATIC RECOVERY FAILS:
# Step 6: Manual recovery
rmmod wlan
sleep 2
modprobe wlan
sleep 5
iw dev  # Should show wlan0

# Step 7: Restart services
systemctl restart wpa_supplicant
systemctl restart hostapd
# OR Android:
setprop ctl.restart vendor.wifi_hal_legacy

# ROOT CAUSE: Firmware assertion (bug in FW, rarely hardware)
# Common triggers: Invalid MCS in specific scenario, race condition

# PREVENTION:
# 1. Update to latest firmware version
# 2. Collect FW crash dump for vendor analysis:
#    /data/vendor/wifi/wlan_logs/ (Qualcomm)
# 3. Enable SSR (SubSystem Restart) for automatic recovery:
#    echo 1 > /sys/bus/msm_subsys/devices/subsys_<wlan>/restart_level
```

---

## DEBUG 9: Regulatory/Country Code Issues

**Symptoms:** Only channels 36-48 visible. Can't use 149-165. DFS channels blocked.

**Diagnosis:**

```bash
# Step 1: Check current regulatory
iw reg get
# FINDING: "country 00: DFS-UNSET" → NO country set!
# Or: "country JP" → Japan rules (different from US/EU)

# Step 2: What channels are available?
iw phy phy0 channels
# Shows enabled/disabled channels and power limits

# Step 3: Check who set regulatory
# Sources: driver default, wpa_supplicant, kernel, user
iw reg get | grep "set by"

# Step 4: Check if 11d (country from beacons) is enabled
grep -i "country\|11d" /vendor/firmware/wlan/qca_cld/WCNSS_qcom_cfg.ini

# ROOT CAUSE: Country code not set → default "world mode" (00)
# World mode: Only channels guaranteed available everywhere (36-48)

# FIX:
# 1. Set via iw
iw reg set US  # Or DE, CN, JP, etc.

# 2. Set in wpa_supplicant.conf
# country=US

# 3. Set in driver config (WCNSS_qcom_cfg.ini)
# gStaCountryCode=US

# 4. Dynamic from telephony (Android automotive):
# ConnectivityService reads MCC from SIM → sets WiFi country
# If no SIM: Use GNSS-based country detection

# VERIFICATION:
iw reg get  # Should show "country US: DFS-FCC"
iw phy phy0 channels | grep -c "disabled"  # Should be fewer
```

---

## DEBUG 10: High Retry Rate (Interference)

**Symptoms:** Connected but high latency and packet loss. Video calls choppy.

**Diagnosis:**

```bash
# Step 1: Check retry statistics
iw dev wlan0 station dump | grep -i "retry\|failed"
# FINDING: "tx retries: 45000" "tx failed: 2000"  (high!)

# Step 2: Check signal quality
iw dev wlan0 station dump | grep signal
# "signal: -58 dBm" → Signal OK, so not distance problem
# "signal avg: -58 dBm"

# Step 3: Survey — check channel busy time
iw dev wlan0 survey dump | grep -A5 "in use"
# "channel busy time: 800 ms" / "channel active time: 1000 ms"
# → 80% busy! Heavy congestion!

# Step 4: Identify interference source
# a) Other WiFi networks on same channel
iw dev wlan0 scan | grep -c "freq: 5180"  # Count APs on our channel
# b) Non-WiFi interference (check with spectrum analyzer if available)

# Step 5: Check if we're on 2.4 GHz
iw dev wlan0 link | grep freq
# If 2437 → 2.4 GHz channel 6 (likely very congested)

# ROOT CAUSE: Channel congestion
# Many APs/devices on same channel → excessive contention → retries

# FIX:
# 1. Switch to less congested channel
# If STA: connect to 5/6 GHz SSID
# If SAP: change to least-used 5 GHz channel
iw dev wlan0 survey dump  # Find channel with least busy time
# Change hostapd.conf: channel=149 (if less busy)

# 2. Use wider channel with better spreading
# 80 MHz has more subcarriers → slightly more resilient

# 3. Enable RTS/CTS threshold (helps with hidden nodes)
iw dev wlan0 set rts 500  # RTS for frames > 500 bytes

# 4. Adjust retry limits (reduce latency at cost of reliability)
# For real-time: fewer retries → lower latency but more drops
iw dev wlan0 set retry short 4  # Default: 7
```

---

# PART C: DIAGNOSTIC COMMANDS QUICK REFERENCE

```bash
# === ONE-LINER DIAGNOSTICS ===

# Full WiFi health check
echo "=== Interface ===" && iw dev wlan0 info && \
echo "=== Link ===" && iw dev wlan0 link && \
echo "=== Station ===" && iw dev wlan0 station dump && \
echo "=== Survey ===" && iw dev wlan0 survey dump | grep -A5 "in use"

# Check for common problems
echo "Signal:" && iw dev wlan0 link | grep signal && \
echo "Retries:" && iw dev wlan0 station dump | grep retry && \
echo "Channel busy:" && iw dev wlan0 survey dump | grep "busy" && \
echo "MCC check:" && iw dev wlan0 info | grep channel && iw dev wlan1 info 2>/dev/null | grep channel

# Android WiFi dump (via adb)
adb shell dumpsys wifi | grep -E "state|ssid|freq|rssi|score|link speed"

# Continuous monitoring (1 Hz)
watch -n 1 'iw dev wlan0 station dump | grep -E "signal|bitrate|retry|bytes"'
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
