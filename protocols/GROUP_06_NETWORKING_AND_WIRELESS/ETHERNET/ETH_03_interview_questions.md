# AUTOMOTIVE ETHERNET — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (25 Questions)

---

### Q1. What is Automotive Ethernet and how does it differ from standard Ethernet?
**A:** Automotive Ethernet is IEEE 802.3 Ethernet adapted for vehicles. Key differences: uses a **single unshielded twisted pair** (vs 2-4 pairs), **15m reach** (vs 100m), automotive-grade temp (-40 to +105°C), meets strict EMC (CISPR 25 Class 5), and uses **PAM-3 coding** with echo cancellation for full-duplex on one pair.

---

### Q2. Why was Automotive Ethernet developed?
**A:** Vehicle networks needed more bandwidth than CAN (2 Mbps) or MOST (150 Mbps) could provide. ADAS cameras, radar, LiDAR, OTA updates, and infotainment require 100 Mbps–10 Gbps. Automotive Ethernet provides scalable bandwidth, IP-based communication, standard protocols, and reduced cable weight (single pair vs 4-pair Cat5).

---

### Q3. How many twisted pairs does 100BASE-T1 use?
**A:** **One** single unshielded twisted pair — compared to standard 100BASE-TX which needs 2 pairs. This is the key innovation: full-duplex 100 Mbps on a single pair using PAM-3 coding and echo cancellation.

---

### Q4. What is the maximum cable length for 100BASE-T1?
**A:** **15 meters**. This is sufficient for in-vehicle point-to-point links (ECU to ECU). 1000BASE-T1 can reach 40m with shielded cable.

---

### Q5. Name the main IEEE standards for automotive Ethernet.
**A:**
- **802.3bw**: 100BASE-T1 (100 Mbps, 2015)
- **802.3bp**: 1000BASE-T1 (1 Gbps, 2016)
- **802.3cg**: 10BASE-T1S (10 Mbps multidrop, 2019)
- **802.3ch**: 2.5/5/10GBASE-T1 (Multi-Gig, 2020)

---

### Q6. What is PAM-3 coding?
**A:** Pulse Amplitude Modulation with 3 levels (-1, 0, +1 volt). At 66.67 MBaud, it achieves 100 Mbps (log₂(3) × 66.67 = 105.7 Mbps raw). Using 3 levels reduces the required bandwidth compared to NRZ (2 levels), improving EMC performance.

---

### Q7. How does full-duplex work on a single pair?
**A:** Both ends transmit and receive simultaneously on the same wire pair. A **hybrid circuit** combines TX and RX. An **echo canceller** in each PHY subtracts its own transmitted signal from the received signal, leaving only the remote end's data.

---

### Q8. What is an Ethernet frame?
**A:** The basic unit of data on Ethernet. Structure: Preamble (7B) + SFD (1B) + Destination MAC (6B) + Source MAC (6B) + EtherType (2B) + Payload (46-1500B) + FCS (4B). Minimum 64 bytes, maximum 1518 bytes (standard).

---

### Q9. What is a MAC address?
**A:** A 48-bit (6-byte) hardware address burned into the Ethernet controller. Format: `XX:XX:XX:XX:XX:XX`. First 3 bytes = OUI (vendor). Used for Layer 2 frame delivery. `FF:FF:FF:FF:FF:FF` = broadcast.

---

### Q10. What is VLAN and why is it used in vehicles?
**A:** VLAN (Virtual LAN, IEEE 802.1Q) logically segments one physical network into isolated domains. In vehicles: separate ADAS traffic from IVI from diagnostics → security isolation, QoS prioritization, and bandwidth control. A 4-byte tag with 12-bit VLAN ID is inserted in the Ethernet frame.

---

### Q11. What is the role of an Ethernet switch in a vehicle?
**A:** Forwards frames between ports based on MAC addresses. Provides: VLAN isolation, QoS/priority scheduling, TSN for determinism, port mirroring for debug, MACsec for security. Central switches connect all vehicle domains.

---

### Q12. What is SOME/IP?
**A:** Scalable service-Oriented MiddlewarE over IP. It's the application-layer protocol for automotive Ethernet that enables service-oriented communication (vs CAN's signal-oriented). Supports: Request/Response, Fire-and-Forget, Events/Notifications, Fields.

---

### Q13. What is DoIP?
**A:** Diagnostics over IP (ISO 13400). Enables vehicle diagnostics over Ethernet instead of CAN. Same UDS commands, but much faster transport (firmware update in minutes vs hours). Uses TCP port 13400.

---

### Q14. What is TSN?
**A:** Time-Sensitive Networking — a set of IEEE 802.1 standards that add determinism to Ethernet. Key features: time synchronization (802.1AS, <1µs), time-gated scheduling (802.1Qbv), frame preemption (802.3br), bandwidth reservation. Critical for ADAS real-time requirements.

---

### Q15. What is 10BASE-T1S and why is it important?
**A:** 10 Mbps Ethernet over a single pair with **multidrop bus topology** (like CAN). Up to 8 nodes share one bus, no switch needed. Uses PLCA for deterministic access. Target: replace CAN/LIN for sensor networks while keeping IP capability.

---

### Q16. What buses is Automotive Ethernet replacing?
**A:**
- **LVDS**: For camera links (LVDS = point-to-point only, no switching)
- **MOST**: For multimedia (MOST = expensive, proprietary ring)
- **CAN** (partially): For high-data functions (CAN = too slow for cameras/ADAS)
- **FlexRay**: For backbone (FlexRay = expensive, limited ecosystem)

---

### Q17. What is the OPEN Alliance SIG?
**A:** A non-profit industry alliance (formed 2012) promoting automotive Ethernet. Members include BMW, Broadcom, NXP, Marvell, Bosch, Continental, etc. They define compliance test specs, promote interoperability, and drive adoption.

---

### Q18. Name 3 automotive Ethernet PHY chip vendors.
**A:** NXP (TJA1100/1101 for 100M), Marvell (88Q2112 for 1G), Broadcom (BCM89810 for 1G). Also: TI (DP83TD510E for 10BASE-T1S), Microchip (LAN8770).

---

### Q19. What is RGMII?
**A:** Reduced Gigabit Media Independent Interface — a 12-pin interface between MAC controller and PHY chip for 10/100/1000 Mbps. Common in automotive SoCs (Qualcomm SA8155P uses RGMII to connect to automotive PHY).

---

### Q20. What protocol does AAOS use to get vehicle data like speed/gear?
**A:** Typically SOME/IP events over Ethernet. Vehicle HAL subscribes to SOME/IP services from the vehicle computer/gateway, receives notifications (events) when values change, and exposes them to Android applications.

---

### Q21. What is gPTP (IEEE 802.1AS)?
**A:** Generalized Precision Time Protocol — synchronizes clocks across all Ethernet nodes to <1µs accuracy. Essential for: camera frame alignment, sensor fusion timestamps, TSN gate scheduling. Every TSN-capable network needs gPTP.

---

### Q22. What is EtherType?
**A:** A 2-byte field in the Ethernet frame header that identifies the protocol of the payload. Key values: 0x0800 = IPv4, 0x0806 = ARP, 0x86DD = IPv6, 0x8100 = VLAN tag, 0x88F7 = PTP.

---

### Q23. What is the minimum Ethernet frame size and why?
**A:** **64 bytes** (excluding preamble). If payload < 46 bytes, it's padded to 46. This minimum ensures collision detection works in half-duplex networks (CSMA/CD). Though automotive Ethernet is full-duplex (no CSMA/CD), the minimum is still enforced for compatibility.

---

### Q24. What is a zonal architecture?
**A:** Modern vehicle design where the car is divided into physical zones (front, rear, left, right). Each zone has a gateway ECU that connects local sensors/actuators (via CAN/LIN/10BASE-T1S) to the central Ethernet backbone. Reduces wiring harness weight and complexity.

---

### Q25. What is the difference between Layer 2 and Layer 3 switching?
**A:**
- **Layer 2**: Forwards based on MAC address (Ethernet switch). Fast, simple.
- **Layer 3**: Forwards based on IP address (router). Can route between subnets, apply firewall rules. Some automotive switches do both (L2/L3 switch).

---

# PART B: MID-LEVEL (30 Questions)

---

### Q26. Explain the AUTOSAR Ethernet stack architecture.
**A:** Bottom-up: **Eth** (MAC driver, MCAL) → **EthTrcv** (PHY transceiver driver) → **EthIf** (interface abstraction, routes to multiple controllers) → **TcpIp** (TCP/UDP/IP/ARP/ICMP) → **SoAd** (Socket Adaptor, maps PDUs to sockets) → **SD** (SOME/IP Service Discovery) / **DoIP** / **COM** at application interface. EthSwt handles switch configuration.

---

### Q27. How does SOME/IP Service Discovery work?
**A:**
1. Provider multicasts **OFFER** (service ID, instance, IP, port, TTL)
2. Consumer multicasts **FIND** (looking for specific service)
3. Provider responds with unicast OFFER
4. Consumer sends **SUBSCRIBE** to specific eventgroup
5. Provider sends **SUBSCRIBE_ACK**
6. Provider starts sending events (notifications) to subscriber
7. OFFER/FIND repeat periodically (TTL-based lifecycle)

---

### Q28. What are the key TSN standards and their purposes?
**A:**
| Standard | Purpose |
|----------|---------|
| 802.1AS (gPTP) | Time sync <1µs |
| 802.1Qbv (TAS) | Time-gated queues (TDMA-like) |
| 802.1Qav (CBS) | Credit-based bandwidth reservation |
| 802.1Qbu/802.3br | Frame preemption |
| 802.1Qci | Per-stream policing (babbling idiot protection) |
| 802.1CB (FRER) | Seamless redundancy |

---

### Q29. How does frame preemption (802.3br) work?
**A:** A low-priority frame being transmitted can be **interrupted** by a high-priority frame. The low-priority frame is split into fragments (minimum 64B each), the high-priority "express" frame is sent immediately, then the remaining fragments resume. Receiver reassembles transparently. Reduces worst-case latency from ~123µs (full frame at 100M) to <10µs.

---

### Q30. Explain the difference between CBS (802.1Qav) and TAS (802.1Qbv).
**A:**
- **CBS (Credit-Based Shaper)**: Each stream has a "credit" that accumulates over time. Stream can only send when credit ≥ 0. Guarantees average bandwidth without hard timing.
- **TAS (Time-Aware Shaper)**: Time is divided into fixed slots (like TDMA). Each slot only allows specific priority queues to transmit. Provides **hard timing guarantees** (deterministic latency).
- CBS = soft real-time (AVB audio/video), TAS = hard real-time (ADAS control).

---

### Q31. How do you configure VLANs on a Linux-based automotive ECU?
**A:**
```bash
# Create VLAN interface
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 10.0.10.1/24 dev eth0.10
ip link set eth0.10 up

# Set VLAN priority (egress map: skb priority → PCP)
ip link set eth0.10 type vlan egress-qos-map 0:0 1:1 2:2 6:6 7:7

# Verify
cat /proc/net/vlan/eth0.10
```

---

### Q32. What is MACsec and why is it important for automotive?
**A:** MACsec (IEEE 802.1AE) provides Layer 2 encryption/authentication using AES-GCM. It encrypts each Ethernet frame hop-by-hop with integrity verification. Important for automotive because: physical Ethernet cables in vehicles are accessible (could be tapped), and safety-critical ADAS data must be protected from tampering. Hardware-accelerated = wire-speed = no performance penalty.

---

### Q33. How does a vehicle's Ethernet switch learn MAC addresses?
**A:** When a frame arrives on a port, the switch records the source MAC + port number in its MAC table. When forwarding, it looks up the destination MAC to find the output port. If not found, it floods to all ports (except source). Entries age out after timeout (typically 300s, shorter in automotive). Static entries can be configured for known ECUs.

---

### Q34. Explain PHY Master/Slave in automotive Ethernet.
**A:** In 100BASE-T1/1000BASE-T1, one PHY is **master** (provides clock reference) and the other is **slave** (synchronizes to master's clock). This is configured via MDIO registers or auto-negotiation. If both sides are set to the same role → link won't establish. Not related to data priority.

---

### Q35. How does DoIP routing work?
**A:** The DoIP gateway maintains a **routing table** mapping logical addresses (UDS target addresses like 0x1234) to IP addresses + ports. When tester sends diagnostic message with target 0x1234, gateway looks up the corresponding ECU's IP, establishes TCP connection, and forwards UDS payload. Supports routing activation (authentication before access).

---

### Q36. What Linux tools do you use for Ethernet debugging?
**A:**
- `ethtool eth0` — link status, speed, PHY info
- `ethtool -S eth0` — TX/RX counters, error stats
- `ip link show` — interface state
- `tcpdump -i eth0` — packet capture
- `wireshark` — GUI protocol analysis
- `iperf3` — bandwidth measurement
- `arping` — test Layer 2 connectivity
- `bridge fdb show` — switch MAC table
- `tc qdisc show` — traffic control / TSN

---

### Q37. What is AVTP (IEEE 1722)?
**A:** Audio/Video Transport Protocol — Layer 2 protocol for streaming media over Ethernet. Uses stream IDs, sequence numbers, and presentation timestamps (from gPTP). Carries: H.264/HEVC compressed video, raw camera data, PCM audio, sensor data. Used for surround-view cameras, rear-seat entertainment, digital microphones.

---

### Q38. How do you measure Ethernet bandwidth in an automotive system?
**A:**
```bash
# Server side (ECU A)
iperf3 -s

# Client side (ECU B)
iperf3 -c 192.168.1.10 -t 30 -i 1

# UDP (for real-time traffic measurement)
iperf3 -c 192.168.1.10 -u -b 100M -t 10

# Check for packet loss
ethtool -S eth0 | grep -i "drop\|error\|miss"
```

---

### Q39. What is the difference between unicast, multicast, and broadcast in automotive context?
**A:**
- **Unicast**: One sender → one receiver (e.g., DoIP diagnostic session)
- **Multicast**: One sender → group (e.g., SOME/IP-SD offers on 224.244.224.245)
- **Broadcast**: One sender → all (e.g., ARP, DHCP discover, ff:ff:ff:ff:ff:ff)

Automotive uses multicast extensively for SOME/IP events (efficient: one send, multiple ECUs receive).

---

### Q40. How does Linux handle Ethernet PHY management?
**A:** Linux PHY framework (`drivers/net/phy/`):
1. MDIO bus driver registers MDIO bus
2. PHY driver matches PHY ID from registers 2/3
3. `phylib` handles: link state machine, auto-negotiation, speed/duplex changes
4. MAC driver connects to phylib via `phy_connect()`
5. Device Tree specifies PHY address, mode (RGMII), master/slave
6. `ethtool` exposes PHY status to userspace

---

### Q41. Explain network namespace usage in automotive Linux testing.
**A:** Network namespaces create isolated network stacks for testing:
```bash
# Create namespace simulating another ECU
ip netns add ecu_sim
ip link add veth0 type veth peer name veth1
ip link set veth1 netns ecu_sim
ip addr add 192.168.1.1/24 dev veth0
ip netns exec ecu_sim ip addr add 192.168.1.2/24 dev veth1
ip netns exec ecu_sim ip link set veth1 up
# Now can test SOME/IP between namespaces
```

---

### Q42. What is the SoAd (Socket Adaptor) module in AUTOSAR?
**A:** SoAd maps AUTOSAR PDUs (Protocol Data Units) to TCP/UDP sockets. It's the bridge between signal-oriented AUTOSAR COM and socket-oriented TCP/IP. Configuration specifies: which PDU goes to which socket connection (IP:port, TCP vs UDP), routing groups, trigger conditions.

---

### Q43. How does OTA update work over automotive Ethernet?
**A:**
1. Vehicle connects to update server (via cellular/WiFi → gateway → Ethernet backbone)
2. Download via HTTPS/TLS to head unit or gateway
3. Gateway distributes update packages to target ECUs over Ethernet (DoIP or proprietary)
4. Each ECU receives firmware over TCP, validates signature, writes to flash
5. Benefits: 100 Mbps vs 500 kbps (CAN) → 200× faster firmware delivery

---

### Q44. What is Ethernet wake-up and how does it work?
**A:** In sleep mode, the PHY can be configured to detect specific patterns on the wire:
- Wake-on-LAN (WoL): Magic packet (6× 0xFF + 16× target MAC)
- Pattern-based: Specific frame pattern triggers wake
- PHY stays in low-power mode but monitors for wake pattern
- On detection: PHY wakes → signals MAC via interrupt → system powers up

---

### Q45. Explain the concept of "babbling idiot" protection in automotive Ethernet.
**A:** A "babbling idiot" is a faulty ECU that transmits excessively, flooding the network. Protection via:
- **802.1Qci (Per-Stream Filtering & Policing)**: Switch monitors each stream's rate; if exceeded, frames are dropped
- **VLAN isolation**: Babbling ECU only affects its VLAN
- **Port bandwidth limiting**: Switch caps maximum bandwidth per port
- Essential for safety-critical systems (ISO 26262 requirement)

---

### Q46. What is IGMP and why is it relevant to automotive?
**A:** Internet Group Management Protocol — manages multicast group membership. When an ECU wants SOME/IP events (multicast), it sends IGMP Join. Switch learns which ports need multicast and only forwards there (vs flooding all ports). Reduces unnecessary traffic. IGMP snooping in automotive switches is critical for bandwidth efficiency.

---

### Q47. How do automotive Ethernet connectors differ from RJ45?
**A:** Automotive connectors (MATEnet, H-MTD):
- Sealed against moisture/dust (IP67+)
- Vibration-resistant (automotive qualification)
- Temperature rated (-40°C to +125°C)
- Only 2 pins (single pair) vs RJ45's 8 pins
- Smaller, lighter than RJ45
- Designed for robotic assembly in production
- No latch mechanism (bayonet/press-fit instead)

---

### Q48. What is Ethernet Passive Monitoring (TAP)?
**A:** A hardware device inserted between two Ethernet nodes that copies all traffic to a monitoring port without disrupting communication. In automotive development: inserted between ECU and switch to capture traffic with a protocol analyzer. Unlike port mirroring (which requires switch support), TAPs work on any link.

---

### Q49. How does Quality of Service (QoS) work in automotive Ethernet?
**A:** Multiple mechanisms:
1. **VLAN PCP** (3 bits = 8 priority levels): Tags frame with priority
2. **DSCP** (IP layer): 6-bit differentiated services field
3. **Switch queues**: 4-8 output queues per port, strict priority or weighted scheduling
4. **TSN TAS**: Time-based guaranteed slots for critical traffic
5. **CBS**: Credit-based shaping for streaming (guaranteed bandwidth)

---

### Q50. What happens when an automotive Ethernet link goes down?
**A:**
1. PHY detects link loss → interrupts MAC
2. MAC driver reports carrier loss to network stack
3. Linux: `netif_carrier_off()` → routes invalidated
4. SOME/IP-SD: Service subscriptions timeout → consumers notified
5. AUTOSAR: EthIf reports link change → ComM/EcuM may initiate recovery
6. TSN: Redundancy (802.1CB) provides seamless switchover if dual-link
7. Safety: System enters degraded mode (uses last-known-good data or failsafe values)

---

### Q51. Explain the Ethernet boot sequence for an automotive ECU.
**A:**
1. Power on → PHY powers up, initializes internal state
2. PHY trains: master/slave negotiation, equalizer adaptation (~200-500ms)
3. Link established → PHY signals MAC
4. MAC initializes, starts accepting frames
5. IP configuration: static IP set or DHCP request
6. SOME/IP-SD: ECU offers its services / finds needed services
7. Application ready: starts normal communication

---

### Q52. What is Ethernet time synchronization accuracy requirement for ADAS?
**A:** IEEE 802.1AS (gPTP) provides <1µs accuracy across the network. ADAS needs this because:
- 8 cameras at 30fps: frames must be timestamped within 1ms for proper fusion
- Radar returns must align temporally with camera frames
- TSN gate schedules need nanosecond-level sync
- Typical automotive gPTP accuracy: 100-500ns end-to-end

---

### Q53. How do you implement SOME/IP in Linux?
**A:** Use vsomeip (GENIVI/COVESA reference implementation):
```cpp
// Service provider
auto app = vsomeip::runtime::get()->create_application("server");
app->offer_service(SERVICE_ID, INSTANCE_ID);
app->register_message_handler(SERVICE_ID, INSTANCE_ID, METHOD_ID,
    [](const std::shared_ptr<vsomeip::message> &msg) {
        // Handle request, send response
    });

// Consumer
auto app = vsomeip::runtime::get()->create_application("client");
app->request_service(SERVICE_ID, INSTANCE_ID);
```

---

### Q54. What is the relationship between AUTOSAR Adaptive and automotive Ethernet?
**A:** Adaptive AUTOSAR runs on Linux/POSIX and uses Ethernet natively:
- **ara::com**: Communication API, binds to SOME/IP over Ethernet
- **ara::diag**: Diagnostics, uses DoIP over Ethernet
- **ara::per**: Persistence (not Ethernet-specific)
- Service-oriented architecture aligns perfectly with SOME/IP
- Typically runs on high-performance ECUs (ADAS, IVI) connected via 1G+ Ethernet

---

### Q55. How does Ethernet compare to CAN for real-time control messages?
**A:**
| Aspect | CAN | Ethernet + TSN |
|--------|-----|----------------|
| Latency | Deterministic (bus arbitration) | Deterministic (TAS gates) |
| Bandwidth | 2-8 Mbps | 100M-10G |
| Overhead | 8B payload/frame | 46-1500B payload |
| Setup | Simple (2-wire bus) | Complex (switch, IP, VLAN) |
| Priority | ID-based (lower ID wins) | VLAN PCP + TSN scheduling |

Ethernet+TSN can match CAN's determinism but with vastly more bandwidth. Trade-off: complexity and cost.

---

# PART C: SENIOR / LEAD (25 Questions)

---

### Q56. Design the Ethernet network for an L3 autonomous vehicle.
**A:**
- **Backbone**: Dual 10GBASE-T1 between ADAS HPC and central switch (redundant)
- **Cameras** (8×): 100BASE-T1 or 1000BASE-T1 to switch (AVTP, compressed)
- **Radar** (4×): 100BASE-T1 per radar module
- **LiDAR** (2×): 1000BASE-T1 (high point-cloud data rate)
- **IVI**: 1000BASE-T1 to head unit
- **Zone gateways** (4×): 1000BASE-T1 (bridge to legacy CAN/LIN)
- **TSN**: 802.1Qbv for ADAS control path, 802.1AS for time sync
- **Redundancy**: 802.1CB (FRER) on ADAS-critical paths
- **Security**: MACsec on all links, VLAN isolation per domain
- **10BASE-T1S**: For low-speed sensor clusters (replacing CAN buses within zones)

---

### Q57. How would you debug intermittent Ethernet frame drops in a vehicle?
**A:**
1. **Identify scope**: Which ECU pair? Which direction? Which traffic class?
2. **ethtool -S**: Check RX/TX counters, CRC errors, oversized/undersized, drops
3. **Switch statistics**: Per-port drop counters, queue overflow, policing violations
4. **tcpdump with timestamps**: Capture and look for gaps in sequence numbers
5. **PHY diagnostics**: Link quality (SNR), error counters, re-training events
6. **EMC correlation**: Does it happen near high-current switching (motors)?
7. **Temperature**: Thermal testing — does it worsen at 85°C+?
8. **Cable/connector**: TDR test for impedance anomalies
9. **Buffer overflow**: Switch output queue full → check CBS/TAS configuration
10. **VLAN/QoS**: Traffic classified wrong priority → preempted/dropped

---

### Q58. Explain how to implement TSN (802.1Qbv) on Linux for automotive.
**A:**
```bash
# Configure Time-Aware Shaper using tc (traffic control)
tc qdisc replace dev eth0 parent root handle 100 taprio \
    num_tc 3 \
    map 2 2 1 0 2 2 2 2 2 2 2 2 2 2 2 2 \
    queues 1@0 1@1 1@2 \
    base-time 1000000000 \
    sched-entry S 01 125000 \    # TC0 open for 125µs (critical)
    sched-entry S 02 500000 \    # TC1 open for 500µs (video)
    sched-entry S 04 375000 \    # TC2 open for 375µs (best-effort)
    clockid CLOCK_TAI

# Enable hardware timestamping for gPTP
ethtool -T eth0  # Check HW timestamp capabilities
# Run ptp4l for 802.1AS sync
ptp4l -i eth0 -f automotive.cfg --transportSpecific 1
```
Requires: NIC with hardware TSN support, kernel ≥5.4, proper PTP clock.

---

### Q59. How do you architect security for an automotive Ethernet network?
**A:**
1. **Defense in Depth**:
   - L2: MACsec (802.1AE) on every physical link
   - L3: IPsec or DTLS for inter-ECU tunnels where needed
   - L7: TLS 1.3 for application data (OTA, diagnostics)
2. **Access Control**:
   - 802.1X port authentication (reject unknown devices)
   - VLAN isolation (ADAS can't reach IVI directly)
   - Firewall rules on gateway ECUs
3. **Intrusion Detection**:
   - IDS monitoring traffic patterns (anomaly-based)
   - 802.1Qci policing (rate violations = potential attack)
4. **Secure Boot Chain**:
   - PHY/MAC firmware verified at boot
   - MACsec keys derived from hardware root of trust (HSM)
5. **Key Management**:
   - Per-link MACsec keys (SAK) derived via MKA protocol
   - Key rotation at intervals

---

### Q60. Design bandwidth budgeting for a vehicle with 12 cameras, 5 radars, 2 LiDARs.
**A:**
```
Per-device bandwidth requirements:
  Camera (H.264, 1080p@30): ~15 Mbps each × 12 = 180 Mbps
  Camera (raw/ISP, 4MP@30): ~200 Mbps each (if uncompressed)
  Radar (processed detections): ~5 Mbps each × 5 = 25 Mbps
  Radar (raw ADC): ~200 Mbps (if raw data sent)
  LiDAR (point cloud): ~100 Mbps each × 2 = 200 Mbps
  SOME/IP control: ~10 Mbps (all services combined)
  DoIP/OTA: ~50 Mbps (burst)
  Overhead (headers, TSN, etc.): 20%

Total ADAS domain: ~500 Mbps (compressed cameras)
  → 1000BASE-T1 backbone sufficient for compressed
  → 10GBASE-T1 needed if raw camera data

Design:
  Camera aggregation switch (per-zone): 100M each → 1G uplink
  Central switch: 10G backbone to ADAS SoC
  TSN reserves: 60% for time-critical (cameras, radar)
  Best-effort: 40% for OTA, diagnostics
```

---

### Q61. Explain Ethernet switch silicon selection for automotive.
**A:**
Key criteria:
1. **Port count/speed**: Match architecture (8×1G, 2×10G uplinks)
2. **TSN feature set**: TAS (802.1Qbv), CBS, preemption, gPTP HW timestamp
3. **Security**: MACsec hardware acceleration (per-port)
4. **Temperature**: AEC-Q100 qualified (-40°C to +105°C)
5. **Management**: SPI/I2C for MCU control vs in-band
6. **Power**: Per-port power budget (automotive power constraints)
7. **AUTOSAR**: Existing EthSwt driver support
8. **Latency**: Cut-through vs store-and-forward (affects worst-case)
9. **Redundancy**: Support 802.1CB stream replication
10. **Cost**: Per-port cost × volume

Candidates: NXP SJA1110 (integrated MCU), Marvell 88Q5050/88Q6113, Microchip KSZ series.

---

### Q62. How does Ethernet integrate with ISO 26262 functional safety?
**A:**
- **ASIL decomposition**: Communication channel rated to target ASIL
- **End-to-end protection (E2E)**: AUTOSAR E2E profiles add CRC + sequence + alive counter over Ethernet PDUs
- **Redundancy**: Dual-path Ethernet (802.1CB FRER) for critical signals
- **Monitoring**: Switch policing detects babbling ECU (safety violation)
- **Timeout**: Missing event → safety state transition
- **Diagnostics**: Periodic ECU health checks over SOME/IP
- **Bus guardian**: Switch acts as bus guardian (per-port policing)
- **Freedom from interference**: VLAN isolation ensures ASIL-D traffic unaffected by QM traffic

---

### Q63. How do you handle Ethernet bring-up for a new automotive SoC?
**A:**
1. **Hardware validation**: Scope check — MAC/PHY signals (RGMII timing, MDIO)
2. **PHY register access**: Verify MDIO bus works (read PHY ID registers)
3. **PHY config**: Set master/slave, speed, auto-negotiation
4. **Link establishment**: Connect to known-good partner PHY, verify link LED
5. **Loopback test**: Internal/external loopback in PHY to verify data path
6. **Ping test**: Configure IP, ping partner → validates entire stack
7. **iperf3**: Bandwidth test → verify full line-rate
8. **Signal integrity**: Eye diagram at PHY output, measure against spec
9. **EMC**: Run CISPR 25 tests, tune common-mode choke
10. **Temperature sweep**: Verify link stability at -40°C and +105°C

---

### Q64. Explain how AVB streams are set up for surround-view cameras.
**A:**
1. Camera ECU registers as AVTP Talker with stream ID
2. Stream reservation: SRP (802.1Qat) or static config
3. CBS bandwidth reserved on each switch hop (802.1Qav)
4. gPTP time sync ensures all cameras share same time base
5. Camera sends AVTP frames with:
   - Stream ID (unique per camera)
   - Sequence number (detect drops)
   - Presentation timestamp (when to display)
   - H.264/MJPEG payload
6. ADAS ECU receives 4-8 streams simultaneously
7. Presentation timestamp aligns frames from all cameras
8. Result: Synchronized multi-camera view for surround-view rendering

---

### Q65. How would you implement Ethernet redundancy for safety-critical ADAS?
**A:** Use **IEEE 802.1CB (FRER — Frame Replication and Elimination for Reliability)**:
```
Source ECU → replicates frame to PATH A and PATH B
  PATH A: ECU → Switch1 → ADAS SoC (Port 1)
  PATH B: ECU → Switch2 → ADAS SoC (Port 2)

ADAS SoC receives both copies, eliminates duplicate:
  - First arrival is used
  - Second copy discarded (sequence number match)
  - If one path fails → zero-loss switchover (other path already delivering)
```
Zero recovery time, transparent to application. Required for ASIL-D sensor data.

---

### Q66. What is the impact of jumbo frames in automotive Ethernet?
**A:** Standard MTU = 1500B. Jumbo frames = up to 9000B+.
- **Pro**: Fewer frames for large payloads (camera data), less header overhead, less interrupt load
- **Con**: Longer serialization delay (72µs vs 12µs at 1Gbps), larger buffers in switch, preemption fragments larger
- **Automotive decision**: Most systems use standard MTU (1500B) for lower latency. Some camera links use 4000-9000B for efficiency.
- Must verify ALL devices on path support same MTU — mismatch = silent drops!

---

### Q67. How do you test automotive Ethernet EMC compliance?
**A:**
1. **Emissions (CISPR 25 Class 5)**:
   - Measure radiated emissions from Ethernet cable (antenna + spectrum analyzer)
   - Measure conducted emissions on power lines
   - Must pass at ALL speeds (100M, 1G)
2. **Immunity**:
   - Bulk Current Injection (BCI) per ISO 11452-4
   - Direct Power Injection (DPI)
   - ESD (ISO 10605): ±8kV contact, ±15kV air
3. **Mitigation techniques**:
   - Common-mode choke on twisted pair
   - Proper cable shielding (STP for 1G)
   - PCB layout: short traces between MAC and PHY, ground plane
   - Connector shielding and grounding

---

### Q68. Explain Ethernet in the context of Software-Defined Vehicle (SDV).
**A:** SDV principles enabled by Ethernet:
1. **OTA updates**: High-bandwidth delivery of new features
2. **Dynamic service deployment**: SOME/IP services can be added/moved at runtime
3. **Centralized compute**: One HPC runs multiple functions, communicates over Ethernet
4. **API-based access**: HTTP/REST APIs over vehicle Ethernet for diagnostics/configuration
5. **Virtual ECUs**: Functions migrate between ECUs (Ethernet-connected, IP-based)
6. **Network slicing**: VLAN/QoS reconfiguration without hardware changes
7. **Analytics**: Traffic monitoring, usage data collection over Ethernet

---

### Q69. How does time synchronization (gPTP) handle asymmetric delays?
**A:** gPTP (802.1AS) uses **Peer Delay mechanism**:
1. Node A sends Pdelay_Req with timestamp t1
2. Node B receives at t2, sends Pdelay_Resp at t3
3. Node A receives at t4
4. **Link delay = [(t4-t1) - (t3-t2)] / 2**

For asymmetric delays (different TX vs RX propagation):
- Hardware timestamping at PHY eliminates most asymmetry
- Remaining asymmetry (cable) is typically <1ns for automotive lengths
- Correction field in Sync messages accumulates all hop delays
- Grand Master election: BMCA (Best Master Clock Algorithm) selects most accurate source

---

### Q70. How do automotive ECUs handle network partition/split?
**A:**
1. **Detection**: Link-down interrupt, SOME/IP-SD subscription timeout, missing keepalives
2. **Graceful degradation**: Continue with last-known-good data (timeout-guarded)
3. **Safety action**: If critical data unavailable beyond threshold → safe state
4. **Healing**: When link recovers → re-subscribe, sync state, resume normal operation
5. **Design**: Redundant paths (802.1CB) prevent partition for critical ECUs
6. **Diagnostics**: Log partition events for DTC reporting

---

### Q71. What is the role of the Ethernet PHY in EMC performance?
**A:** The PHY is the PRIMARY EMC component:
- **Transmit**: PAM-3/PAM-4 shaping, rise-time control, common-mode voltage
- **Common-Mode Choke (CMC)**: Integrated or external, suppresses common-mode noise
- **Return Loss**: PHY impedance matching to 100Ω cable (affects reflections/emissions)
- **PSD (Power Spectral Density)**: PHY transmit spectrum must fit within mask
- **Receive sensitivity**: PHY must work despite ambient noise (immunity)
- **Vendor differentiator**: PHY EMC performance varies significantly between vendors

---

### Q72. How do you validate 100BASE-T1 interoperability between different PHY vendors?
**A:**
1. **OPEN Alliance compliance test**: TC1 (PHY), TC8 (ECU), TC12 (switch)
2. **Link establishment**: All master/slave combinations
3. **BER test**: Bit Error Rate <10⁻¹⁰ at reference channel
4. **PSD mask**: Transmit spectrum within allowed limits
5. **Return loss**: Verify impedance matching
6. **EMC**: Both PHYs pass CISPR 25 when connected
7. **Temperature**: -40°C to +105°C link stability
8. **Cable variation**: Test with min/max quality cables per OPEN Alliance spec
9. **Wake-up**: Verify wake pattern detection interoperability

---

# PART D: STAFF / ARCHITECT (10 Questions)

---

### Q73. Architect the complete networking solution for a next-gen autonomous vehicle platform.
**A:**
```
Architecture: Central + Zonal

Central Domain Controllers (3):
  - ADAS HPC (SA9000P): 2×10G to backbone
  - Cockpit HPC (SA8295P): 1×10G to backbone
  - Vehicle Computer: 1×10G to backbone

Central Backbone Switch:
  - Marvell 88Q6113: 4×10G + 12×1G/2.5G
  - Full TSN, MACsec, 802.1CB
  - Managed via vehicle computer (SPI + in-band)

Zone Gateways (6: FL, FR, RL, RR, Roof, Underbody):
  - NXP SJA1110 (integrated S32G MCU)
  - 1×1G uplink to backbone
  - 4×100M downlinks (cameras, radar)
  - 2×CAN + 1×LIN (legacy bridge)
  - 1×10BASE-T1S bus (local sensors)

Cameras (12): 100BASE-T1 → zone gateway (compressed H.265)
Radar (5): 100BASE-T1 → zone gateway
LiDAR (3): 1000BASE-T1 → backbone switch
V2X Module: 1×100M to backbone

Network Services:
  - gPTP: Grand Master in ADAS HPC
  - TSN: Qbv on ADAS paths, CBS on camera streams
  - Security: MACsec all links, TLS for OTA/diag
  - Redundancy: 802.1CB for ADAS paths
  - VLANs: ADAS(10), Cockpit(20), Body(30), Diag(40), OTA(50)
```

---

### Q74. How would you design the network for V2X (Vehicle-to-Everything) integration?
**A:**
- **External interface**: Cellular (5G) + DSRC/C-V2X (802.11p/PC5)
- **Firewall/DMZ**: V2X gateway ECU acts as security boundary
  - Deep packet inspection
  - Protocol validation
  - Rate limiting
  - Authenticated & encrypted only
- **Internal routing**: V2X data enters vehicle Ethernet on dedicated VLAN
- **Trust zones**: V2X VLAN isolated from ADAS (no direct access)
- **Data flow**: V2X → Gateway → Central Computer → (validated) → ADAS if relevant
- **Latency**: C-V2X expects <20ms response → dedicated TSN slot in network

---

### Q75. Design Ethernet monitoring and diagnostics for production fleet.
**A:**
1. **Per-ECU counters**: TX/RX bytes, frames, errors, drops (stored locally)
2. **Switch counters**: Per-port statistics, queue drops, policing events
3. **PHY counters**: Link up/down events, re-training count, SNR measurements
4. **Periodic reporting**: ECUs report health via SOME/IP to central logger
5. **DTC integration**: Network faults → Diagnostic Trouble Codes (UDS readable)
6. **Event logging**: Circular buffer of network events (last 1000)
7. **Remote access**: DoIP allows dealer to query network health
8. **Cloud analytics**: Aggregated fleet data identifies systemic issues
9. **Alert thresholds**: Error rate > X → proactive maintenance notification
10. **PCAP capture**: On-demand packet capture (debugger-triggered)

---

### Q76. How do you handle the transition from legacy CAN architecture to Ethernet?
**A:**
Phased approach:
1. **Phase 1** (current): Add Ethernet backbone, gateways bridge to existing CAN
   - Signal-to-service translation in gateway (CAN signal → SOME/IP event)
   - No changes to existing CAN ECUs
2. **Phase 2**: New ECUs (cameras, ADAS) native Ethernet; legacy still on CAN
   - Dual-stack gateway: routes between Ethernet and CAN domains
3. **Phase 3**: Migrate body/chassis ECUs to 10BASE-T1S or 100BASE-T1
   - Replace CAN buses with Ethernet bus (10BASE-T1S: same topology!)
4. **Phase 4**: All-Ethernet vehicle (new platform, no CAN)
   - CAN only retained for OBD-II connector (regulatory)

Key challenges: Ensuring safety equivalence, managing mixed-network timing, requalification.

---

### Q77. How would you implement deterministic Ethernet for a drive-by-wire system?
**A:**
Requirements: Control message delivered in <500µs, guaranteed, no drops.

Implementation:
1. **Dedicated VLAN** (highest priority PCP=7) for control messages
2. **TSN TAS (802.1Qbv)**: Reserve 100µs slot every 1ms for control traffic
3. **Frame preemption**: Ensure even if best-effort frame transmitting, control preempts
4. **802.1Qci policing**: Drop any non-control traffic attempting to use control slot
5. **802.1CB redundancy**: Dual path for zero-loss on link failure
6. **Worst-case analysis**: Calculate max latency through all switch hops
   - Wire delay: ~5ns/m × 15m = 75ns
   - Switch latency: ~1-5µs per hop (cut-through)
   - Serialization: 64B @ 100Mbps = 5.12µs
   - 3 hops: ~20µs total → well within 500µs budget
7. **Monitoring**: Watchdog on receiver — if no message in 2× period → safe state
8. **ISO 26262**: ASIL-D qualification of entire communication path

---

### Q78. Design the software architecture for an Ethernet gateway ECU.
**A:**
```
┌────────────────────────────────────────────────────────────┐
│                    GATEWAY ECU SOFTWARE                     │
├────────────────────────────────────────────────────────────┤
│ Application Layer:                                         │
│   - Signal routing engine (CAN↔ETH translation)           │
│   - SOME/IP service proxy (expose CAN signals as service) │
│   - DoIP routing table management                         │
│   - Network management (wake/sleep coordination)          │
├────────────────────────────────────────────────────────────┤
│ Middleware:                                                │
│   - AUTOSAR COM (signal routing)                          │
│   - SOME/IP-SD (service discovery)                        │
│   - PDU Router (ETH↔CAN PDU mapping)                     │
│   - SecOC (authenticated forwarding)                      │
├────────────────────────────────────────────────────────────┤
│ Communication Stack:                                       │
│   - TcpIp + SoAd (Ethernet side)                         │
│   - CanIf + CanDrv (CAN side)                            │
│   - LinIf + LinDrv (LIN side)                            │
│   - EthIf + EthSwt (switch management)                   │
├────────────────────────────────────────────────────────────┤
│ BSW/MCAL:                                                 │
│   - Eth (MAC), EthTrcv (PHY), CAN, LIN                  │
│   - Crypto (MACsec keys), HSM interface                  │
│   - NvM (routing tables, config)                         │
├────────────────────────────────────────────────────────────┤
│ Hardware:                                                  │
│   - MCU (S32G, TC3xx) + Ethernet switch (SJA1110)        │
│   - 1G uplink + 100M downlinks + CAN + LIN              │
└────────────────────────────────────────────────────────────┘
```

---

### Q79. How do you validate TSN timing guarantees across the network?
**A:**
1. **Modeling**: Network calculus / simulation (e.g., RTaW-Pegase) to calculate worst-case latency
2. **Static analysis**: Verify TAS gate lists don't overlap, all streams fit in schedule
3. **Hardware-in-loop**: Real switches + traffic generators, measure actual latency
4. **Timestamp injection**: Send frames with hardware timestamp, measure at receiver
5. **Jitter measurement**: Statistical analysis of latency distribution (must be bounded)
6. **Fault injection**: What happens when one stream exceeds allocation? (policing must catch)
7. **Temperature/corner**: Validate at extreme temperature (PHY timing varies)
8. **Long-duration**: Run for days to catch rare corner cases
9. **Certification evidence**: Document worst-case proof for ISO 26262 safety case

---

### Q80. Compare Ethernet solutions for sensor fusion in autonomous driving.
**A:**
| Approach | Architecture | Pros | Cons |
|----------|-------------|------|------|
| **Raw streaming** | All sensors → HPC over 10G | Flexible fusion, HPC does all processing | Massive bandwidth (needs 10G+), high power |
| **Edge processing** | Sensors process locally → send detections over 100M | Low bandwidth, distributed load | Less flexible, harder to update algorithms |
| **Hybrid** | Cameras=compressed, Radar=detections, LiDAR=filtered | Balanced bandwidth/flexibility | More complex protocol mix |
| **Time-triggered** | TSN schedules all sensor data in fixed slots | Deterministic, predictable | Rigid, harder to add sensors |

Trend: Hybrid with increasingly raw data as backbone speeds increase (10G).

---

# PART E: QUICK-FIRE (25 Questions)

| # | Question | Answer |
|---|----------|--------|
| F1 | Wires for 100BASE-T1? | 1 unshielded twisted pair |
| F2 | Max speed automotive Ethernet? | 10 Gbps (802.3ch) |
| F3 | 100BASE-T1 max reach? | 15 meters |
| F4 | 1000BASE-T1 max reach? | 15m UTP / 40m STP |
| F5 | PAM-3 voltage levels? | -1, 0, +1 |
| F6 | Full-duplex on 1 pair how? | Echo cancellation |
| F7 | Ethernet frame min size? | 64 bytes |
| F8 | Ethernet frame max size? | 1518 bytes (standard) |
| F9 | SOME/IP stands for? | Scalable service-Oriented MiddlewarE over IP |
| F10 | DoIP port number? | TCP 13400 |
| F11 | gPTP accuracy? | <1 µs |
| F12 | TSN TAS standard? | IEEE 802.1Qbv |
| F13 | 10BASE-T1S topology? | Multidrop bus (like CAN) |
| F14 | OPEN Alliance is? | Industry consortium for automotive Ethernet |
| F15 | VLAN tag size? | 4 bytes |
| F16 | PCP bits for priority? | 3 bits (0-7) |
| F17 | MAC address size? | 48 bits (6 bytes) |
| F18 | MACsec encryption? | AES-GCM-128/256 |
| F19 | EtherType for IPv4? | 0x0800 |
| F20 | EtherType for VLAN? | 0x8100 |
| F21 | PHY master/slave for? | Clock reference (not data priority) |
| F22 | SOME/IP-SD multicast? | 224.244.224.245:30490 |
| F23 | Standard automotive PHY? | NXP TJA1100 (100M), Marvell 88Q2112 (1G) |
| F24 | Automotive EMC standard? | CISPR 25 Class 5 |
| F25 | BroadR-Reach inventor? | Broadcom (2011) |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
