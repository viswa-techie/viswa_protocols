# AUTOMOTIVE ETHERNET — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 06 of 08
# One-page-per-topic rapid lookup reference
# ════════════════════════════════════════════════════════════════════

---

## 1. SPEED TIERS AT A GLANCE

| Standard | Speed | Pairs | Reach | Coding | Year | Use Case |
|----------|-------|-------|-------|--------|------|----------|
| 10BASE-T1S (802.3cg) | 10 Mbps | 1 UTP | 25m bus | PAM-3/DMME | 2019 | CAN replacement (multidrop) |
| 100BASE-T1 (802.3bw) | 100 Mbps | 1 UTP | 15m | PAM-3 | 2015 | Cameras, radar, sensors |
| 1000BASE-T1 (802.3bp) | 1 Gbps | 1 STP/UTP | 15-40m | PAM-3×3 | 2016 | ADAS, IVI, backbone |
| 2.5GBASE-T1 (802.3ch) | 2.5 Gbps | 1 STP | 15m | PAM-4 | 2020 | Multi-cam aggregation |
| 5GBASE-T1 (802.3ch) | 5 Gbps | 1 STP | 15m | PAM-4 | 2020 | ADAS compute link |
| 10GBASE-T1 (802.3ch) | 10 Gbps | 1 STP | 15m | PAM-4 | 2020 | HPC backbone |

---

## 2. ETHERNET FRAME FORMAT

```
┌─────────┬─────┬──────────┬──────────┬────────────┬───────────┬────────────────┬──────┐
│Preamble │ SFD │ Dst MAC  │ Src MAC  │ [VLAN Tag] │ EtherType │   Payload      │ FCS  │
│  7B     │ 1B  │  6B      │  6B      │  [4B]      │  2B       │  46-1500B      │ 4B   │
└─────────┴─────┴──────────┴──────────┴────────────┴───────────┴────────────────┴──────┘

Min frame: 64B | Max frame: 1518B (1522 with VLAN) | IFG: 12B
```

---

## 3. KEY ETHERTYPE VALUES

| EtherType | Protocol |
|-----------|----------|
| 0x0800 | IPv4 |
| 0x0806 | ARP |
| 0x8100 | 802.1Q VLAN Tag |
| 0x86DD | IPv6 |
| 0x88F7 | PTP (IEEE 1588 / 802.1AS) |
| 0x22F0 | AVTP (IEEE 1722) |
| 0x88E5 | MACsec (802.1AE) |
| 0x888E | 802.1X Authentication |
| 0x88CC | LLDP |

---

## 4. VLAN / QoS PRIORITY MAPPING (Automotive Typical)

| PCP | Priority | Traffic Type | Example |
|-----|----------|-------------|---------|
| 7 | Highest | Network Control | STP, LLDP |
| 6 | Critical | Safety-critical control | Brake-by-wire, steering |
| 5 | Video | ADAS camera streams | AVTP, TSN class A |
| 4 | Voice/Controlled | Time-sensitive | gPTP, SOME/IP events |
| 3 | Excellent Effort | IVI multimedia | Audio streaming |
| 2 | Spare | — | Reserved |
| 1 | Background | OTA, logging | Firmware download |
| 0 | Best Effort | General | Diagnostics, HTTP |

**Typical automotive VLANs:**
| VLAN ID | Domain |
|---------|--------|
| 10 | ADAS |
| 20 | IVI / Cockpit |
| 30 | Body / Comfort |
| 40 | Diagnostics |
| 50 | OTA / Connectivity |

---

## 5. SOME/IP QUICK REFERENCE

```
Header (16 bytes):
┌──────────────┬──────────────┬──────────┬──────────┬─────┬─────┬──────┬────────┐
│ Service ID   │ Method ID    │ Length   │Client ID │Sess │Proto│ Msg  │Return  │
│   (16b)      │   (16b)      │  (32b)  │  (16b)   │(16b)│Ver  │Type  │Code    │
└──────────────┴──────────────┴──────────┴──────────┴─────┴─────┴──────┴────────┘

Message Types:
  0x00 = REQUEST          0x01 = REQUEST_NO_RETURN
  0x02 = NOTIFICATION     0x80 = RESPONSE
  0x81 = ERROR

Service Discovery:
  Multicast: 224.244.224.245:30490 (UDP)
  Entries: OFFER, FIND, SUBSCRIBE, SUBSCRIBE_ACK

Communication Patterns:
  Request/Response:   Client → REQUEST → Server → RESPONSE → Client
  Fire-and-Forget:    Client → REQUEST_NO_RETURN → Server
  Event/Notification: Server → NOTIFICATION → Subscribers (on change)
  Field:              Getter + Setter + Notifier
```

---

## 6. DoIP QUICK REFERENCE

```
Port: TCP 13400 (data) | UDP 13400 (discovery)

Header (8 bytes):
┌──────────────┬──────────────┬──────────────┬────────────────┐
│ Proto Version│ Inv. Version │ Payload Type │ Payload Length  │
│    (1B)      │    (1B)      │    (2B)      │     (4B)       │
└──────────────┴──────────────┴──────────────┴────────────────┘

Key Payload Types:
  0x0001 = Vehicle Identification Request (UDP broadcast)
  0x0004 = Vehicle Identification Response (VIN, IP)
  0x0005 = Routing Activation Request
  0x0006 = Routing Activation Response
  0x8001 = Diagnostic Message (contains UDS data)
  0x8002 = Diagnostic Message Positive ACK
  0x8003 = Diagnostic Message Negative ACK

Flow: Discovery(UDP) → TCP Connect → Routing Activation → Diagnostic Messages
```

---

## 7. TSN STANDARDS QUICK MAP

| Standard | Name | Purpose |
|----------|------|---------|
| 802.1AS | gPTP | Time sync (<1µs accuracy) |
| 802.1Qbv | TAS | Time-gated scheduling (TDMA-like) |
| 802.1Qav | CBS | Credit-based bandwidth shaping |
| 802.1Qbu + 802.3br | Preemption | Interrupt low-pri for high-pri |
| 802.1Qci | PSFP | Per-stream policing (rate guard) |
| 802.1CB | FRER | Seamless frame redundancy |
| 802.1Qcc | Config | TSN configuration models |
| 802.1Qat | SRP | Stream Reservation Protocol |

---

## 8. LINUX COMMANDS CHEATSHEET

### Link & PHY
```bash
# Check link status
ethtool eth0                        # Speed, duplex, link, PHY
ethtool -S eth0                     # HW statistics (drops, errors)
ip link show eth0                   # Interface state
cat /sys/class/net/eth0/carrier     # 1=link up, 0=down
cat /sys/class/net/eth0/speed       # Link speed in Mbps

# PHY diagnostics
ethtool -d eth0                     # Dump PHY registers
phytool read eth0/0/0x09            # Read specific register
ethtool --set-priv-flags eth0 master on  # Set master mode (PHY-specific)
```

### IP & VLAN
```bash
# IP configuration
ip addr add 192.168.1.10/24 dev eth0
ip route add default via 192.168.1.1
ip addr show eth0

# VLAN
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 10.0.10.1/24 dev eth0.10
ip link set eth0.10 up
cat /proc/net/vlan/eth0.10
```

### Capture & Debug
```bash
# Packet capture
tcpdump -i eth0 -w capture.pcap
tcpdump -i eth0 -n port 13400      # DoIP traffic
tcpdump -i eth0 -n ether proto 0x88f7  # PTP frames
tcpdump -i eth0 -n vlan 10         # Specific VLAN

# Bandwidth test
iperf3 -s                           # Server
iperf3 -c 192.168.1.10 -t 30 -i 1  # Client (TCP)
iperf3 -c 192.168.1.10 -u -b 95M   # UDP at 95Mbps

# ARP/connectivity
arping -I eth0 192.168.1.1          # L2 ping
ping -I eth0 192.168.1.1            # L3 ping
```

### TSN / QoS
```bash
# TAS (Time-Aware Shaper)
tc qdisc replace dev eth0 parent root handle 100 taprio \
    num_tc 3 \
    map 2 2 1 0 2 2 2 2 2 2 2 2 2 2 2 2 \
    queues 1@0 1@1 1@2 \
    base-time 1000000000 \
    sched-entry S 01 125000 \
    sched-entry S 02 500000 \
    sched-entry S 04 375000 \
    clockid CLOCK_TAI

# CBS (Credit-Based Shaper)
tc qdisc add dev eth0 parent root handle 1: mqprio \
    num_tc 3 map 2 2 1 0 2 2 2 2
tc qdisc replace dev eth0 parent 1:1 cbs \
    idleslope 50000 sendslope -50000 \
    hicredit 50 locredit -50

# gPTP time sync
ptp4l -i eth0 -f /etc/automotive_gPTP.cfg -m
phc2sys -s eth0 -c CLOCK_REALTIME -O 0
```

### Switch (if managed via Linux bridge)
```bash
# Create bridge
ip link add br0 type bridge
ip link set eth0 master br0
bridge fdb show                     # MAC table
bridge vlan show                    # VLAN table
bridge -s link show                 # Port statistics
```

---

## 9. AUTOSAR MODULE MAP

```
┌─────────────────────────────────────────────────┐
│ Application / SWC                               │
├─────────────────────────────────────────────────┤
│ RTE                                             │
├──────────┬──────────┬───────────┬───────────────┤
│ COM      │ SD       │ DoIP      │ EthTSyn       │
├──────────┴──────────┴───────────┴───────────────┤
│ SoAd (Socket Adaptor)                           │
├─────────────────────────────────────────────────┤
│ TcpIp (TCP/UDP/IP/ARP/ICMP)                    │
├─────────────────────────────────────────────────┤
│ EthIf (Ethernet Interface)                      │
├──────────────────┬──────────────────────────────┤
│ EthSwt           │ Eth (MAC Driver - MCAL)      │
│ (Switch Driver)  │ EthTrcv (PHY Driver)         │
├──────────────────┴──────────────────────────────┤
│ Hardware: MAC + PHY + Switch IC                 │
└─────────────────────────────────────────────────┘

Key Configurations:
  Eth:     Controller ID, buffer sizes, MAC address
  EthTrcv: PHY address (MDIO), mode, wake-up
  EthIf:   VCtrl-to-port mapping, VLAN assignment
  EthSwt:  Port config, VLAN table, MAC filtering
  TcpIp:   IP addresses, sockets, DHCP/static
  SoAd:    Socket↔PDU routing, trigger mode
  SD:      Service instances, eventgroups, timing
  DoIP:    Logical addresses, routing table, auth
```

---

## 10. PHY IC QUICK REFERENCE

| PHY | Vendor | Speed | Key Features |
|-----|--------|-------|-------------|
| TJA1100 | NXP | 100M | First automotive PHY, OPEN Alliance compliant |
| TJA1101 | NXP | 100M | Successor, lower power, TC10 sleep/wake |
| TJA1103 | NXP | 10M | 10BASE-T1S, PLCA coordinator capable |
| 88Q2112 | Marvell | 1G | Most popular 1G automotive PHY |
| 88Q2220 | Marvell | Multi-Gig | 2.5G/5G/10G support |
| BCM89810 | Broadcom | 1G | BroadR-Reach origin, proven |
| DP83TD510E | TI | 10M | 10BASE-T1S, low power |
| LAN8770 | Microchip | 100M | Budget 100BASE-T1 option |

**PHY Register Quick Map (Clause 22):**
| Register | Purpose |
|----------|---------|
| 0 | Control (reset, speed, AN enable) |
| 1 | Status (link status, AN complete) |
| 2-3 | PHY Identifier (vendor + model) |
| 4-5 | AN advertisement/partner ability |
| 9 | Master/Slave control |
| 10 | Master/Slave status |
| 15+ | Vendor-specific (varies) |

---

## 11. CONNECTOR & CABLE REFERENCE

| Connector | Vendor | Pins | Use |
|-----------|--------|------|-----|
| MATEnet | TE Connectivity | 2 | 100M standard, most common |
| H-MTD | Rosenberger | 2-8 | 1G+, shielded, high-freq |
| Mini50 | Molex | 2 | Compact form factor |
| HSD (FAKRA) | Rosenberger | 4 | High-Speed Data (older) |
| SPE | Various | 2 | Single Pair Ethernet IEC 63171 |

**Cable specs:**
- Impedance: 100Ω ±10%
- 100M: UTP (unshielded), single pair
- 1G: STP (shielded) recommended for automotive
- Twist rate: per OPEN Alliance TC2 spec
- Temperature: -40°C to +105°C
- Max 15m (in-vehicle point-to-point)

---

## 12. PORT NUMBERS & ADDRESSES

| Service | Protocol | Port/Address |
|---------|----------|-------------|
| SOME/IP-SD | UDP multicast | 224.244.224.245:30490 |
| DoIP | TCP | 13400 |
| DoIP discovery | UDP | 13400 |
| gPTP (802.1AS) | Layer 2 | EtherType 0x88F7 |
| AVTP (1722) | Layer 2 | EtherType 0x22F0 |
| LLDP | Layer 2 | EtherType 0x88CC |
| MACsec | Layer 2 | EtherType 0x88E5 |
| IGMP | IP | Protocol 2 |
| SOME/IP services | UDP/TCP | 30000-40000 (typical) |

---

## 13. TIMING REFERENCE

| Event | Typical Duration |
|-------|-----------------|
| PHY link training (100BASE-T1) | 200-500 ms |
| PHY link training (1000BASE-T1) | 300-700 ms |
| gPTP sync accuracy | 100-500 ns |
| TSN TAS cycle (automotive) | 1-5 ms |
| Frame serialization (64B @ 100M) | 5.12 µs |
| Frame serialization (1518B @ 100M) | 121.4 µs |
| Frame serialization (64B @ 1G) | 0.51 µs |
| Frame serialization (1518B @ 1G) | 12.1 µs |
| Switch cut-through latency | 1-5 µs |
| Switch store-and-forward latency | 5-15 µs |
| MACsec encryption overhead | <1 µs (HW) |
| SOME/IP-SD initial delay | 0-100 ms (configurable) |
| DoIP routing activation | 100-500 ms |

---

## 14. TROUBLESHOOTING QUICK GUIDE

| Symptom | Check First | Likely Cause |
|---------|------------|--------------|
| No link | ethtool (link detected?) | Master/slave mismatch, cable, PHY power |
| Link flapping | ethtool -S (error counters) | EMI, loose connector, marginal cable |
| No ping | ip addr, arp table | Wrong IP/subnet, VLAN mismatch |
| Slow throughput | iperf3, ethtool -S drops | MTU mismatch, half-duplex, queue overflow |
| SOME/IP no service | SD multicast group join? | IGMP not working, firewall, wrong port |
| DoIP timeout | TCP connect works? | Routing activation failed, wrong logical addr |
| TSN jitter | ptp4l sync status | gPTP not locked, bad GM source |
| Packet loss | switch counters | Policing, queue drop, babbling idiot |
| Wake failure | PHY register (wake status) | Wrong wake pattern, PHY not in listen mode |

---

## 15. WIRESHARK DISPLAY FILTERS

```
# SOME/IP
someip
someip.serviceid == 0x1234
someip.messagetype == 0x02          # Notifications only

# DoIP
doip
doip.payload_type == 0x8001         # Diagnostic messages

# VLAN
vlan.id == 10                       # Specific VLAN
vlan.priority >= 5                  # High priority

# PTP
ptp                                 # All PTP/gPTP
eth.type == 0x88f7

# General
eth.src == 00:1a:2b:3c:4d:5e       # From specific MAC
ip.addr == 192.168.1.10            # Specific IP
tcp.port == 13400                   # DoIP port
udp.port == 30490                   # SOME/IP-SD

# Error analysis
eth.fcs.status == "Bad"             # CRC errors
tcp.analysis.retransmission         # TCP retransmits
```

---

END OF DOCUMENT 06 — CHEATSHEET
