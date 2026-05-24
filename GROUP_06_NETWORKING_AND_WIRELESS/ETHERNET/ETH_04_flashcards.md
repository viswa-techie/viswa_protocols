# AUTOMOTIVE ETHERNET — FLASHCARDS (250+)
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 04 of 08
# Format: Q: / A: pairs organized in 10 themed decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: IDENTITY & BASICS (25 cards)

**Q:** What is Automotive Ethernet?
**A:** IEEE 802.3 Ethernet adapted for vehicles — single twisted pair, 15m reach, PAM-3 coding, automotive-grade temp/EMC, full IP capability.

**Q:** Year BroadR-Reach was introduced?
**A:** 2011, by Broadcom. Became basis for IEEE 802.3bw (100BASE-T1) standardized in 2015.

**Q:** How many wire pairs does 100BASE-T1 use?
**A:** ONE single unshielded twisted pair (vs standard Ethernet's 2-4 pairs).

**Q:** Full-duplex on 1 pair — how?
**A:** Echo cancellation: each PHY knows its own TX signal, subtracts it from the received composite signal to recover remote TX.

**Q:** 100BASE-T1 maximum distance?
**A:** 15 meters (sufficient for in-vehicle point-to-point links).

**Q:** 1000BASE-T1 maximum distance?
**A:** 15m (UTP) or 40m (STP — shielded twisted pair).

**Q:** What is the OPEN Alliance?
**A:** Non-profit industry consortium (BMW, Broadcom, NXP, etc.) promoting automotive Ethernet standardization and interoperability.

**Q:** Standard for 100 Mbps automotive Ethernet?
**A:** IEEE 802.3bw (100BASE-T1), ratified 2015.

**Q:** Standard for 1 Gbps automotive Ethernet?
**A:** IEEE 802.3bp (1000BASE-T1), ratified 2016.

**Q:** Standard for 10BASE-T1S multidrop?
**A:** IEEE 802.3cg, ratified 2019. 10 Mbps, bus topology, PLCA access.

**Q:** Standard for multi-gigabit (2.5/5/10G)?
**A:** IEEE 802.3ch, ratified 2020.

**Q:** What does PAM-3 mean?
**A:** Pulse Amplitude Modulation with 3 levels: -1V, 0V, +1V. Used in 100BASE-T1 at 66.67 MBaud.

**Q:** Why PAM-3 instead of NRZ for automotive?
**A:** PAM-3 carries more bits/symbol (1.585), reducing baud rate for same bit rate → lower EMC emissions, better CISPR 25 compliance.

**Q:** What EMC standard must automotive Ethernet meet?
**A:** CISPR 25 Class 5 — the strictest automotive electromagnetic compatibility class.

**Q:** Temperature range for automotive Ethernet PHYs?
**A:** -40°C to +105°C (or +125°C for some grades). AEC-Q100 qualified.

**Q:** What replaced MOST in modern vehicles?
**A:** Automotive Ethernet (for infotainment backbone). MOST was expensive, proprietary, ring-topology, limited to 150 Mbps.

**Q:** What replaced LVDS camera links?
**A:** 100BASE-T1 or 1000BASE-T1. LVDS was point-to-point only, no IP, no switching capability.

**Q:** What is a zonal architecture?
**A:** Vehicle divided into physical zones (front, rear, left, right), each with a zone gateway ECU connecting local CAN/LIN sensors to central Ethernet backbone.

**Q:** Key advantage of Ethernet over CAN for bandwidth?
**A:** 100 Mbps to 10 Gbps (Ethernet) vs 2-8 Mbps (CAN FD). 50× to 5000× more bandwidth.

**Q:** Key advantage of Ethernet for OTA updates?
**A:** High bandwidth enables rapid firmware delivery: 50 MB firmware in seconds (vs hours over CAN).

**Q:** What is the minimum Ethernet frame size?
**A:** 64 bytes (excluding preamble/SFD). Payload padded to 46 bytes if shorter.

**Q:** Maximum standard Ethernet frame size?
**A:** 1518 bytes (or 1522 with VLAN tag). Jumbo frames extend to 9000+ bytes.

**Q:** What is EtherType?
**A:** 2-byte field identifying payload protocol: 0x0800=IPv4, 0x0806=ARP, 0x86DD=IPv6, 0x8100=VLAN, 0x88F7=PTP.

**Q:** MAC address format?
**A:** 48 bits (6 bytes), written as XX:XX:XX:XX:XX:XX. First 3 bytes = OUI (vendor), last 3 = unique per device.

**Q:** Broadcast MAC address?
**A:** FF:FF:FF:FF:FF:FF — frame delivered to ALL stations on the network/VLAN.

---

# DECK 2: PHYSICAL LAYER & PHY (30 cards)

**Q:** What does PHY stand for?
**A:** Physical layer transceiver — the chip that converts digital data to/from analog signals on the wire.

**Q:** 100BASE-T1 baud rate?
**A:** 66.67 MBaud (with PAM-3 encoding, yields 100 Mbps).

**Q:** 1000BASE-T1 encoding?
**A:** PAM-3×3 (3 levels on 3 overlapping symbols) at 750 MBaud effective. Also called 80B/81B encoding with PAM-3.

**Q:** Multi-Gig (802.3ch) encoding?
**A:** PAM-4 (4 voltage levels: -3, -1, +1, +3). 2 bits/symbol. Used for 2.5G, 5G, 10G.

**Q:** What is echo cancellation?
**A:** PHY technique that subtracts its own TX signal from the received composite signal (TX+RX mixed on same pair), extracting the remote end's data.

**Q:** What is a hybrid circuit?
**A:** Analog front-end that couples TX signal onto the wire while also extracting the received signal. Enables simultaneous TX/RX on one pair.

**Q:** PHY master vs slave — what's the difference?
**A:** Master provides the clock reference; slave synchronizes to it. One of each required per link. Not related to data priority.

**Q:** What happens if both PHYs are set to master?
**A:** Link will NOT establish. Must be one master, one slave. Common debug issue.

**Q:** How is master/slave configured?
**A:** Via MDIO register bits or auto-negotiation. Can be forced via Device Tree/AUTOSAR config.

**Q:** What is MDIO?
**A:** Management Data Input/Output — 2-wire serial bus (MDC + MDIO) between MAC and PHY for register access (16-bit registers, clause 22/45).

**Q:** PHY register address for ID?
**A:** Registers 2 and 3 (clause 22): PHY Identifier 1 & 2. Contains OUI + model + revision.

**Q:** Key PHY IC for 100BASE-T1?
**A:** NXP TJA1100/TJA1101. Most widely used 100M automotive Ethernet PHY.

**Q:** Key PHY IC for 1000BASE-T1?
**A:** Marvell 88Q2112 (most popular), also Broadcom BCM89810, NXP TJA1102.

**Q:** Key PHY IC for 10BASE-T1S?
**A:** TI DP83TD510E, NXP TJA1103, Microchip LAN8670/LAN8671.

**Q:** What is MII (Media Independent Interface)?
**A:** Standard interface between MAC and PHY. Variants: MII (4-bit, 25MHz), RMII (2-bit, 50MHz), RGMII (4-bit DDR, 125MHz), SGMII (serial, 1.25GHz).

**Q:** RGMII pin count?
**A:** 12 pins: TXD[3:0], TX_CLK, TX_CTL, RXD[3:0], RX_CLK, RX_CTL. Plus MDIO (2 pins).

**Q:** SGMII advantage over RGMII?
**A:** Fewer pins (1 serial pair each direction), easier PCB routing, supports longer traces, used for 1G+.

**Q:** What is USXGMII?
**A:** Universal Serial 10G MII — single SerDes lane at 10.3125 Gbps between MAC and PHY. For multi-gig PHYs.

**Q:** Common-mode choke purpose?
**A:** Suppresses common-mode noise on the twisted pair while passing differential signal. Critical for EMC compliance.

**Q:** What is return loss?
**A:** Measure of impedance mismatch. Higher return loss (dB) = better match. Poor return loss causes reflections → bit errors → EMC issues.

**Q:** Cable impedance for automotive Ethernet?
**A:** 100Ω characteristic impedance (single unshielded/shielded twisted pair).

**Q:** What connector type for 100BASE-T1?
**A:** MATEnet (TE Connectivity) or H-MTD (Rosenberger) — 2-pin, sealed, automotive-rated.

**Q:** PHY power-down mode purpose?
**A:** Sleep mode with <1mW consumption. PHY can still monitor for wake patterns. Essential for vehicle sleep management (reduces 12V battery drain).

**Q:** What is link training?
**A:** PHY startup phase: master/slave negotiation → timing recovery → equalizer convergence → BER check → link up. Takes 200-500ms typically.

**Q:** PHY loopback modes?
**A:** Internal loopback (data loops back inside PHY without going to wire) and external loopback (loops at connector). Used for diagnostics.

**Q:** What is SQI (Signal Quality Indicator)?
**A:** PHY register reporting link quality (0-7 scale). Based on SNR measurement. Useful for diagnosing marginal cables/connections.

**Q:** Eye diagram — what does it show?
**A:** Oscilloscope measurement overlaying all bit transitions. Shows timing margins (horizontal opening) and amplitude margins (vertical opening). Larger eye = better signal quality.

**Q:** What clock frequency does 100BASE-T1 PHY need?
**A:** 25 MHz reference clock (internal PLL generates 66.67 MHz symbol clock).

**Q:** What is PMA sublayer?
**A:** Physical Medium Attachment — handles symbol timing, clock recovery, scrambling/descrambling. Between PCS and PMD.

**Q:** What is the TC10 specification?
**A:** OPEN Alliance TC10 defines PHY sleep/wake behavior for automotive Ethernet. Standardizes how PHYs enter/exit low-power modes and detect wake events.

---

# DECK 3: FRAME STRUCTURE & SWITCHING (25 cards)

**Q:** Ethernet frame preamble content?
**A:** 7 bytes of 0xAA (alternating 10101010) — used for clock synchronization at receiver.

**Q:** What is SFD?
**A:** Start Frame Delimiter = 0xAB (10101011). Signals end of preamble, start of frame data.

**Q:** FCS field purpose?
**A:** Frame Check Sequence — 32-bit CRC over entire frame (Dst MAC → Payload). Detects bit errors in transmission.

**Q:** What is a VLAN tag?
**A:** 4-byte 802.1Q header inserted between Source MAC and EtherType. Contains: TPID (0x8100), PCP (3-bit priority), DEI (1-bit), VID (12-bit VLAN ID).

**Q:** How many VLANs possible?
**A:** 4094 usable (12-bit VID: 0 reserved, 4095 reserved, 1-4094 valid).

**Q:** PCP priority range?
**A:** 0-7 (3 bits). 7 = highest (network control), 0 = best effort. Automotive mapping: 7=critical control, 5-6=ADAS, 3-4=multimedia, 0-2=best effort.

**Q:** Ethernet switch forwarding logic?
**A:** Receive frame → learn source MAC+port → lookup destination MAC in table → if found, forward to that port → if not found, flood to all ports (except source).

**Q:** Cut-through vs store-and-forward switching?
**A:** Cut-through: starts forwarding after reading destination MAC (lower latency, ~1µs). Store-and-forward: receives entire frame, checks CRC, then forwards (catches errors, higher latency).

**Q:** What is port mirroring?
**A:** Switch copies all traffic from one port to a "mirror" port for monitoring/debug without disturbing original traffic flow.

**Q:** What is MAC address aging?
**A:** Switch removes learned MAC entries after timeout (default ~300s). In automotive, static MAC entries preferred (known topology, no aging needed).

**Q:** What is a trunk/tagged port?
**A:** Port that carries multiple VLANs with 802.1Q tags preserved. Used between switches or between switch and multi-VLAN ECU.

**Q:** What is an access/untagged port?
**A:** Port assigned to single VLAN. Switch adds tag on ingress, removes on egress. Connected device doesn't need VLAN awareness.

**Q:** What is Spanning Tree Protocol (STP)?
**A:** Loop prevention for redundant Ethernet topologies. Blocks redundant paths to create loop-free tree. Automotive variant: RSTP (rapid convergence) or replaced by TSN redundancy (802.1CB).

**Q:** Broadcast storm — what and how to prevent?
**A:** Excessive broadcast frames looping/flooding network. Prevention: storm control (rate limiting broadcasts per port), proper VLAN segmentation, STP/RSTP, loop detection.

**Q:** Switch queue scheduling types?
**A:** Strict Priority (highest queue always first), Weighted Round Robin (guaranteed bandwidth per queue), CBS (credit-based for AVB), TAS (time-gated for TSN).

**Q:** What is ingress policing?
**A:** Switch monitors incoming traffic rate per stream/port. If exceeds configured limit → excess frames dropped. Prevents babbling idiot.

**Q:** What is egress shaping?
**A:** Switch controls output rate to match downstream capacity. Smooths bursts. CBS and TAS are egress shaping mechanisms.

**Q:** Double VLAN tagging (Q-in-Q)?
**A:** Two 802.1Q tags stacked: outer tag (service provider) + inner tag (customer). Used in some automotive gateways for domain isolation.

**Q:** Multicast filtering in switches?
**A:** IGMP snooping: switch monitors IGMP join/leave messages, forwards multicast only to ports with subscribers (vs flooding all ports).

**Q:** Maximum MAC address table size for automotive switch?
**A:** Typically 1024-16384 entries. For automotive (known, fixed topology): hundreds of entries sufficient, usually statically configured.

**Q:** What is a priority code point (PCP) remap?
**A:** Switch maps incoming PCP to internal priority queue. Allows per-port or per-VLAN priority remapping for flexible QoS policies.

**Q:** Frame preemption express vs preemptable?
**A:** Express frames (high priority) can interrupt preemptable frames (low priority). Express = TC0/TC1 in 802.3br mapping.

**Q:** What is InterFrame Gap (IFG)?
**A:** 12-byte minimum idle time between frames (96 bit times). Required for receiver clock recovery between frames.

**Q:** EtherType 0x88F7?
**A:** PTP (Precision Time Protocol / IEEE 1588 / 802.1AS gPTP). Layer 2 time sync messages.

**Q:** EtherType 0x22F0?
**A:** IEEE 1722 (AVTP) — Audio/Video Transport Protocol for streaming media.

---

# DECK 4: SOME/IP & SERVICE-ORIENTED COMMUNICATION (25 cards)

**Q:** What does SOME/IP stand for?
**A:** Scalable service-Oriented MiddlewarE over IP.

**Q:** SOME/IP vs CAN signals — paradigm difference?
**A:** CAN = signal-oriented (broadcast values on bus). SOME/IP = service-oriented (offer/find services, subscribe to events, request/response).

**Q:** SOME/IP communication patterns (4)?
**A:** 1) Request/Response, 2) Fire-and-Forget (one-way), 3) Notification/Event (publish-subscribe), 4) Field (getter/setter/notifier).

**Q:** SOME/IP-SD purpose?
**A:** Service Discovery — dynamic mechanism for services to announce availability (OFFER) and consumers to find/subscribe (FIND/SUBSCRIBE).

**Q:** SOME/IP-SD multicast address?
**A:** 224.244.224.245, port 30490 (UDP).

**Q:** SOME/IP header fields?
**A:** Service ID (16bit), Method ID (16bit), Length, Client ID, Session ID, Protocol Version, Interface Version, Message Type, Return Code.

**Q:** SOME/IP message types?
**A:** REQUEST (0x00), REQUEST_NO_RETURN (0x01), NOTIFICATION (0x02), RESPONSE (0x80), ERROR (0x81), TP_REQUEST/RESPONSE (segmented).

**Q:** What is a SOME/IP eventgroup?
**A:** A set of events that can be subscribed together. Consumer subscribes to eventgroup, receives all events in that group.

**Q:** SOME/IP subscription lifecycle?
**A:** SUBSCRIBE → SUBSCRIBE_ACK → receive events → SUBSCRIBE_NACK (rejected) or TTL expiry → re-subscribe.

**Q:** What is vsomeip?
**A:** Open-source SOME/IP implementation for Linux (COVESA/GENIVI). C++ library providing the full SOME/IP + SD stack.

**Q:** SOME/IP transport protocol?
**A:** Uses TCP (reliable, for request/response and large data) or UDP (for events/notifications, lower latency).

**Q:** Service instance — what is it?
**A:** Multiple instances of same service can exist (e.g., service "DoorStatus" with instances for each door). Instance ID differentiates them.

**Q:** SOME/IP serialization?
**A:** Payload serialized using a defined format (like protobuf but simpler): basic types, structs, arrays, strings. Defined in FIBEX/ARXML.

**Q:** What is SOME/IP-TP?
**A:** Transport Protocol — segmentation for messages exceeding UDP MTU (>1400 bytes). Splits into multiple UDP datagrams with TP header (offset field).

**Q:** SOME/IP vs DDS comparison?
**A:** SOME/IP: automotive-native, lighter weight, explicit offer/find. DDS: data-centric, topic-based, QoS policies, used more in aerospace/robotics. Both service-oriented.

**Q:** SOME/IP port numbers?
**A:** Service ports are configurable (30000-40000 range typical). SD fixed at 30490. Each service instance gets unique port.

**Q:** What happens when SOME/IP service disappears?
**A:** SD STOP_OFFER sent (or TTL expires). Consumers detect loss, trigger error handler, may attempt to FIND again.

**Q:** SOME/IP initial wait phase?
**A:** After boot, service waits random time (INITIAL_DELAY) before first OFFER, avoids network storm. Configurable min/max delay.

**Q:** SOME/IP repetition phase?
**A:** After initial wait, OFFER sent repeatedly (REPETITION_BASE_DELAY × 2^n) for configured number of repetitions. Then enters main phase.

**Q:** SOME/IP main phase?
**A:** Periodic OFFER at CYCLIC_OFFER_DELAY interval (e.g., every 1s). Maintains service visibility for new subscribers.

**Q:** E2E protection over SOME/IP?
**A:** AUTOSAR E2E Profile adds: CRC, sequence counter, alive counter to SOME/IP payload. Receiver validates for data integrity, loss, repetition.

**Q:** What is a SOME/IP field?
**A:** Service element with getter (request current value), setter (change value), and notifier (event on change). Like a property with pub-sub.

**Q:** SOME/IP routing — how?
**A:** vsomeip has routing manager (one per ECU) that dispatches messages to local applications. On network: standard IP routing.

**Q:** SOME/IP major/minor version?
**A:** Service interface has major version (breaking changes) and minor version (compatible). Client checks version compatibility during discovery.

**Q:** Tool for SOME/IP analysis?
**A:** Wireshark (with SOME/IP dissector), Vector CANoe, PEAK SOME/IP Explorer, Technica Engineering Capture.

---

# DECK 5: DoIP & DIAGNOSTICS (20 cards)

**Q:** What does DoIP stand for?
**A:** Diagnostics over Internet Protocol (ISO 13400).

**Q:** DoIP port number?
**A:** TCP port 13400 (data), UDP port 13400 (vehicle identification/discovery).

**Q:** DoIP vs DoIP over CAN (UDS on CAN)?
**A:** Same UDS commands but: DoIP = Ethernet transport (fast, Mbps), UDS-on-CAN = CAN transport (slow, kbps). DoIP enables fast flash programming.

**Q:** DoIP routing activation — what is it?
**A:** Authentication step before diagnostic access. Tester sends routing activation request with tester ID; gateway validates and responds with confirmation.

**Q:** DoIP vehicle identification?
**A:** Tester broadcasts UDP discovery. DoIP entity responds with VIN, logical address, GID, further action required flag. Used to find vehicle on network.

**Q:** DoIP message structure?
**A:** Generic header: Protocol version (1B) + Inverse version (1B) + Payload type (2B) + Payload length (4B) + Payload data.

**Q:** DoIP payload types (key ones)?
**A:** 0x0001=Vehicle ID request, 0x0004=Vehicle ID response, 0x0005=Routing activation request, 0x0006=Routing activation response, 0x8001=Diagnostic message, 0x8003=Diagnostic ACK.

**Q:** Advantage of DoIP for firmware update?
**A:** ~100 Mbps throughput (vs ~500 kbps over CAN). 50 MB firmware: ~4 seconds via DoIP vs ~15 minutes via CAN.

**Q:** DoIP gateway role?
**A:** Routes diagnostic requests from external tester to target ECU. Maintains routing table (logical address → IP:port). Handles routing activation (security).

**Q:** What is a DoIP entity?
**A:** Any node that implements DoIP protocol: DoIP gateway (routes diagnostics) or DoIP node (directly responds to diagnostics).

**Q:** DoIP alive check?
**A:** Gateway periodically verifies tester connection is still active. If no response → session timeout → resources freed.

**Q:** DoIP transport layer?
**A:** TCP for diagnostic data (reliable delivery of UDS messages). UDP for vehicle identification (broadcast/discovery).

**Q:** DoIP and VLAN?
**A:** Diagnostic traffic typically on dedicated VLAN (e.g., VLAN 40) with lowest priority (doesn't interfere with ADAS/safety traffic).

**Q:** DoIP security considerations?
**A:** Routing activation = basic auth. Production: add TLS over TCP for encryption. Restrict diagnostic VLAN access. Rate limit requests.

**Q:** Can DoIP and SOME/IP coexist?
**A:** Yes — different ports, different purposes. SOME/IP for runtime communication, DoIP for diagnostic sessions. Often on same Ethernet link.

**Q:** DoIP in AUTOSAR stack?
**A:** DoIP module sits above SoAd/TcpIp. Configured with: source/target addresses, routing table, tester authentication, timing parameters.

**Q:** ISO 13400 parts?
**A:** Part 1: General info, Part 2: Transport protocol and network layer services, Part 3: Wired vehicle interface, Part 4: Ethernet-based high-speed comms.

**Q:** DoIP diagnostic message (0x8001) structure?
**A:** DoIP header + Source logical address (2B) + Target logical address (2B) + UDS data. Gateway uses target address for routing.

**Q:** DoIP timeout values?
**A:** T_TCP_Initial (2s), T_TCP_General (5s), T_TCP_Alive (500ms), configurable per implementation.

**Q:** Tools for DoIP testing?
**A:** Vector CANoe/CANalyzer (with Ethernet option), ETAS INCA, Softing DTS, custom Python scripts (doipclient library).

---

# DECK 6: TSN (Time-Sensitive Networking) (30 cards)

**Q:** What is TSN?
**A:** Time-Sensitive Networking — set of IEEE 802.1 standards adding determinism (bounded latency, zero loss, time sync) to standard Ethernet.

**Q:** gPTP standard number?
**A:** IEEE 802.1AS (generalized Precision Time Protocol). Automotive profile of IEEE 1588.

**Q:** gPTP accuracy target?
**A:** <1 microsecond end-to-end across network. Typical automotive: 100-500 ns.

**Q:** gPTP mechanism?
**A:** Grand Master sends Sync messages with precise timestamps. Each bridge measures and corrects for link delay (Peer Delay mechanism). End stations reconstruct GM time.

**Q:** What is Peer Delay in gPTP?
**A:** Each link measures its own propagation delay: Pdelay_Req/Pdelay_Resp exchange with timestamps → delay = [(t4-t1)-(t3-t2)]/2.

**Q:** Grand Master Clock election?
**A:** Best Master Clock Algorithm (BMCA) — based on priority, accuracy, clock class. Typically: ADAS HPC with GPS/GNSS or high-quality oscillator.

**Q:** TAS standard number?
**A:** IEEE 802.1Qbv (Time-Aware Shaper). Enhancements for scheduled traffic.

**Q:** How does TAS work?
**A:** Time divided into repeating cycles. Gate Control List (GCL) specifies which priority queues are open/closed at each time slot. Only open queues can transmit.

**Q:** TAS cycle time for automotive?
**A:** Typically 1-5 ms. Shorter = lower latency but more overhead. Must align with application periods (e.g., 1ms control loop).

**Q:** CBS standard number?
**A:** IEEE 802.1Qav (Credit-Based Shaper). Forwarding and queuing for time-sensitive streams.

**Q:** CBS mechanism?
**A:** Each stream has credit that increases when idle, decreases when sending. Can only send when credit ≥ 0. Guarantees average bandwidth without hard timing.

**Q:** CBS vs TAS — when to use which?
**A:** CBS: audio/video streaming (soft real-time, guaranteed bandwidth). TAS: control messages (hard real-time, guaranteed timing).

**Q:** Frame preemption standard?
**A:** IEEE 802.1Qbu (scheduling) + IEEE 802.3br (MAC merge sublayer).

**Q:** Frame preemption benefit?
**A:** Reduces worst-case latency from ~123µs (full 1518B frame at 100M) to <10µs. High-priority "express" frame interrupts low-priority transmission.

**Q:** Minimum preemptable fragment size?
**A:** 64 bytes (to maintain Ethernet minimum frame size for fragments on wire).

**Q:** 802.1Qci purpose?
**A:** Per-Stream Filtering and Policing — monitors each stream's rate/timing. Drops frames violating configured limits. "Babbling idiot" protection.

**Q:** 802.1CB purpose?
**A:** Frame Replication and Elimination for Reliability (FRER). Source sends frame on two paths. Destination eliminates duplicate. Zero-loss redundancy.

**Q:** 802.1CB use in automotive?
**A:** ADAS-critical sensor data replicated on dual paths. If one switch/link fails, other path delivers without any lost frames or switchover delay.

**Q:** What is stream identification in TSN?
**A:** Each TSN stream identified by: Source MAC + VLAN ID + priority (or explicitly by destination MAC + stream ID). Used by Qci/Qbv/CB to apply per-stream policies.

**Q:** TSN configuration methods?
**A:** Static (preconfigured GCL in switches), centralized (CNC computes schedule, pushes to switches via NETCONF/YANG), distributed (SRP signaling).

**Q:** What is CNC in TSN?
**A:** Central Network Controller — computes TSN schedules/gate lists, configures all switches. Single point of network intelligence.

**Q:** What is CUC in TSN?
**A:** Central User Configuration — represents end stations' requirements to CNC. Translates application needs to TSN stream parameters.

**Q:** Linux TSN support — which tools?
**A:** `tc` command with `taprio` qdisc (TAS), `cbs` qdisc (CBS), `etf` qdisc (earliest TxTime first). Requires NIC hardware support.

**Q:** ptp4l — what is it?
**A:** Linux PTP daemon implementing IEEE 1588/802.1AS. Synchronizes system clock to network Grand Master. Part of linuxptp package.

**Q:** phc2sys — what is it?
**A:** Linux tool that synchronizes system clock (CLOCK_REALTIME) to PTP hardware clock (PHC). Used with ptp4l for application time access.

**Q:** TSN worst-case latency calculation?
**A:** Per hop: max(serialization delay) + switch processing + guard band. Sum across all hops. With TAS: bounded by cycle time + queue delays.

**Q:** Guard band in TAS?
**A:** Time before gate close where no new frame starts (to avoid frame spanning gate boundary). Size = max frame serialization time.

**Q:** TSN for camera synchronization?
**A:** gPTP syncs all cameras to same time. Each camera timestamps frame capture. ADAS SoC uses timestamps to align frames for fusion (within 1ms).

**Q:** AVTP (IEEE 1722) and TSN relationship?
**A:** AVTP is the application protocol for streaming media. TSN (CBS/TAS) provides the network guarantees. AVTP relies on gPTP for presentation timestamps.

**Q:** TSN vs AVB?
**A:** AVB = earlier standard (802.1Qav CBS + SRP). TSN = superset adding TAS, preemption, policing, redundancy. TSN provides HARD real-time guarantees.

---

# DECK 7: AUTOSAR ETHERNET STACK (25 cards)

**Q:** AUTOSAR Ethernet MCAL module?
**A:** Eth (Ethernet controller/MAC driver). Handles frame TX/RX, buffer management, hardware access.

**Q:** EthTrcv module purpose?
**A:** Ethernet Transceiver driver — controls PHY chip (mode, link status, wake-up, configuration via MDIO).

**Q:** EthIf module purpose?
**A:** Ethernet Interface — abstraction layer above Eth/EthTrcv. Provides unified API regardless of hardware. Routes to upper layers.

**Q:** EthSwt module purpose?
**A:** Ethernet Switch driver — manages switch IC (VLAN config, MAC table, port mirroring, QoS, MACsec, TSN config).

**Q:** TcpIp module in AUTOSAR?
**A:** Implements TCP, UDP, IPv4/IPv6, ARP, ICMP, DHCP. Configured statically or dynamically. Provides socket-like API to upper layers.

**Q:** SoAd module purpose?
**A:** Socket Adaptor — bridges gap between PDU-based AUTOSAR COM and socket-based TcpIp. Maps PDU IDs to socket connections.

**Q:** SD module purpose?
**A:** Service Discovery — implements SOME/IP-SD protocol. Manages service offers, finds, subscriptions, eventgroups.

**Q:** DoIP module in AUTOSAR?
**A:** Implements ISO 13400 diagnostic transport. Handles vehicle identification, routing activation, diagnostic message forwarding.

**Q:** EthTSyn module purpose?
**A:** Time Synchronization over Ethernet — implements 802.1AS (gPTP). Provides synchronized time base to StbM (Synchronized Time Base Manager).

**Q:** StbM module purpose?
**A:** Synchronized Time Base Manager — provides global time to all BSW and application modules. Fed by EthTSyn (Ethernet) or CanTSyn (CAN).

**Q:** How does AUTOSAR handle Ethernet buffers?
**A:** Eth module manages TX/RX buffers (zero-copy preferred). Buffer provided to TcpIp, filled, then transmitted. Configured in EthGeneral container.

**Q:** AUTOSAR Ethernet configuration flow?
**A:** ARXML configuration → generated code (Cfg.c/.h) → compiled with BSW. Defines: controllers, transceivers, connections, VLANs, sockets, services.

**Q:** What is PDU in AUTOSAR?
**A:** Protocol Data Unit — generic data container. Contains: ID + data + length. Routed by PduR between modules (COM, DCM, DoIP, etc.).

**Q:** How does SoAd map PDUs to sockets?
**A:** Configuration defines socket connections (IP:port, TCP/UDP) and PDU routes (which PDU ID → which socket). SoAd_TxPdu() sends PDU through configured socket.

**Q:** AUTOSAR Ethernet and E2E?
**A:** E2E transformer applied at COM level BEFORE passing to SoAd/TcpIp. Adds CRC + counter to PDU payload. Receiver's E2E transformer validates.

**Q:** AUTOSAR Eth driver states?
**A:** UNINIT → INIT (EthDrv_Init called) → ACTIVE (controller enabled). EthIf manages transitions based on ComM/EcuM requests.

**Q:** SecOC over Ethernet in AUTOSAR?
**A:** Secure Onboard Communication adds MAC (Message Authentication Code) to PDUs. Applied at PduR level, transparent to Ethernet stack below.

**Q:** Network Management (NM) over Ethernet?
**A:** UdpNm — sends NM messages via UDP multicast to coordinate sleep/wake of Ethernet-connected ECUs. All awake ECUs repeat NM message.

**Q:** AUTOSAR Adaptive vs Classic for Ethernet?
**A:** Classic: static config, MCAL drivers, deterministic. Adaptive: Linux-based, ara::com API, dynamic services, POSIX sockets. Both use Ethernet.

**Q:** ara::com binding to SOME/IP?
**A:** Adaptive AUTOSAR's ara::com (communication API) typically uses SOME/IP as transport binding. Service proxy/skeleton generated from service interface definition.

**Q:** EthIf switch port assignment?
**A:** EthIf maps virtual controllers to physical switch ports. Configuration specifies: which EthCtrl → which EthSwt port → which VLAN.

**Q:** Ethernet wake-up in AUTOSAR?
**A:** EthTrcv detects wake pattern → signals EcuM via EthIf → EcuM transitions from SLEEP to RUN → full initialization → communication resumes.

**Q:** AUTOSAR Ethernet buffer size configuration?
**A:** EthCtrlRxBufLenByte, EthCtrlTxBufLenByte in Eth configuration. Must accommodate max frame (1522B with VLAN) + protocol headers.

**Q:** BswM and Ethernet?
**A:** BswM (BSW Mode Manager) coordinates: link up/down → mode switch → enable/disable communication. Rules-based: IF link_up AND mode=RUN THEN enable_SOMEIP.

**Q:** How does AUTOSAR handle multiple Ethernet controllers?
**A:** EthIf abstracts multiple Eth controllers (e.g., MCU with 2 MACs). Upper layers address via virtual controller ID; EthIf routes to correct physical driver.

---

# DECK 8: LINUX & ANDROID NETWORKING (25 cards)

**Q:** Linux Ethernet driver framework?
**A:** NAPI (New API): interrupt → poll mode for high traffic. `net_device` structure. Drivers in `drivers/net/ethernet/`. PHY handled by `phylib`.

**Q:** stmmac driver — what is it?
**A:** Synopsys DesignWare MAC Ethernet driver. Very common in automotive SoCs (Qualcomm, Renesas, etc.). Supports RGMII, TSN features.

**Q:** Device Tree entry for Ethernet?
**A:** Specifies: MAC compatible, reg (address), PHY mode (rgmii), PHY handle, clocks, interrupts, pinctrl. Example: `compatible = "snps,dwmac-4.20a"`.

**Q:** ethtool command — what info?
**A:** Link status, speed, duplex, auto-negotiation, driver info, PHY registers, hardware timestamps, RX/TX stats, ring buffer size, wake-on-LAN.

**Q:** How to check link status in Linux?
**A:** `ethtool eth0 | grep "Link detected"` or `ip link show eth0` (check for "state UP") or `cat /sys/class/net/eth0/carrier`.

**Q:** How to capture packets on Linux?
**A:** `tcpdump -i eth0 -w capture.pcap` or `tshark -i eth0`. Add filters: `tcpdump -i eth0 port 13400` (DoIP), `tcpdump -i eth0 vlan 10`.

**Q:** VLAN configuration on Linux?
**A:** `ip link add link eth0 name eth0.10 type vlan id 10` → creates VLAN 10 interface. Assign IP, bring up separately.

**Q:** Bridge configuration on Linux?
**A:** `ip link add br0 type bridge` → `ip link set eth0 master br0` → `ip link set eth1 master br0` → `ip link set br0 up`. Creates L2 switch.

**Q:** tc (traffic control) — what is it?
**A:** Linux tool for QoS, shaping, scheduling. Used for TSN: `tc qdisc add dev eth0 root taprio ...` (TAS), `tc qdisc add dev eth0 root cbs ...` (CBS).

**Q:** How to test throughput in Linux?
**A:** `iperf3 -s` (server) / `iperf3 -c <IP> -t 30` (client). Reports bandwidth, jitter, packet loss.

**Q:** Network namespace usage?
**A:** `ip netns add test` → isolated network stack. Useful for testing without affecting host. Can pair with veth for inter-namespace communication.

**Q:** PHY driver in Linux — how matched?
**A:** PHY ID read from MDIO registers 2&3. Matched against `mdio_device_id` table in PHY drivers. Example: NXP TJA1100 driver matches ID 0x0180DC40.

**Q:** How to read PHY registers?
**A:** `ethtool -d eth0` (dump) or `phytool read eth0/0/0x09` or via sysfs/debugfs. Clause 22: 32 registers. Clause 45: extended register space.

**Q:** Linux PTP (gPTP) setup?
**A:** `ptp4l -i eth0 -f /etc/ptp4l.conf` (time sync daemon). `phc2sys -s eth0 -c CLOCK_REALTIME` (sync system clock to PTP clock).

**Q:** Socket programming for SOME/IP?
**A:** Standard UDP/TCP sockets. For SOME/IP: create UDP socket, join multicast group 224.244.224.245 for SD. Or use vsomeip library (abstracts socket management).

**Q:** netlink — what is it?
**A:** Linux kernel-userspace communication mechanism for networking. `ip` and `tc` commands use netlink. rtnetlink for routing, generic netlink for custom.

**Q:** Raw socket use in automotive?
**A:** `socket(AF_PACKET, SOCK_RAW, ...)` — send/receive raw Ethernet frames. Used for: custom protocols, AVTP streaming, low-latency bypass of IP stack.

**Q:** Android EthernetManager?
**A:** Android framework API managing Ethernet interfaces. Handles: IP configuration, link detection, network scoring for connectivity selection.

**Q:** Android ConnectivityService and Ethernet?
**A:** ConnectivityService evaluates network (Ethernet, WiFi, Cell) and selects best. Ethernet typically gets highest priority in AAOS (always-on, wired).

**Q:** Vehicle HAL and networking?
**A:** Vehicle HAL may expose vehicle data that arrives via Ethernet (SOME/IP). HAL implementation subscribes to SOME/IP services, exposes to Android via HIDL/AIDL.

**Q:** NetworkStack permission for Ethernet in AAOS?
**A:** Privileged system apps can configure Ethernet via `android.net.EthernetManager`. Regular apps use standard `ConnectivityManager` API.

**Q:** Ethernet in Android init.rc?
**A:** `service ethernet /system/bin/ethernet_manager` or configured via `config_ethernet_interfaces` overlay. Static IP or DHCP specified in overlay.

**Q:** sysfs entries for Ethernet?
**A:** `/sys/class/net/eth0/`: address, carrier, speed, duplex, mtu, statistics/. Useful for monitoring without tools.

**Q:** How to set static IP without NetworkManager?
**A:** `ip addr add 192.168.1.10/24 dev eth0` + `ip route add default via 192.168.1.1`. In automotive: usually set at boot via init scripts or AUTOSAR config.

**Q:** Jumbo frame configuration on Linux?
**A:** `ip link set eth0 mtu 9000`. Requires driver and hardware support. All devices on path must support same MTU.

---

# DECK 9: SECURITY (20 cards)

**Q:** MACsec standard?
**A:** IEEE 802.1AE — provides Layer 2 encryption and integrity for Ethernet frames using AES-GCM.

**Q:** MACsec encryption algorithm?
**A:** AES-GCM-128 or AES-GCM-256. Provides both confidentiality (encryption) and integrity (authentication tag).

**Q:** MACsec key agreement protocol?
**A:** MKA (MACsec Key Agreement, IEEE 802.1X-2010). Derives per-link session keys (SAK) from pre-shared CAK.

**Q:** MACsec performance impact?
**A:** Near-zero — hardware acceleration in PHY or switch handles encryption at wire speed. Adds ~32 bytes overhead (SecTAG + ICV).

**Q:** 802.1X port authentication — what is it?
**A:** Port-based Network Access Control. Unknown device must authenticate (EAP) before switch grants network access. Prevents rogue ECU connection.

**Q:** TLS in automotive Ethernet?
**A:** TLS 1.3 secures application-layer communication (SOME/IP, HTTP, OTA). End-to-end encryption between ECUs. Certificate-based authentication.

**Q:** IPsec in automotive?
**A:** Network-layer encryption/authentication. Can create encrypted tunnels between ECUs. Less common than MACsec (adds processing overhead vs wire-speed MACsec).

**Q:** SecOC in AUTOSAR?
**A:** Secure Onboard Communication — adds cryptographic MAC (authentication) to AUTOSAR PDUs. Protects against spoofing and tampering.

**Q:** Hardware Security Module (HSM) role?
**A:** Stores MACsec keys, performs crypto operations, provides root of trust. Keys never exposed in software. Examples: SHE, EVITA Medium/Full, HSM IP in SoC.

**Q:** IDS/IPS for automotive Ethernet?
**A:** Intrusion Detection/Prevention System. Monitors traffic patterns, detects anomalies (unusual source, rate, content). Can alert or block. Often in central gateway.

**Q:** Replay attack protection?
**A:** MACsec: Packet Number in SecTAG prevents replay (monotonically increasing). TLS: sequence numbers. SOME/IP: session ID. E2E: alive counter.

**Q:** VLAN hopping attack prevention?
**A:** Disable trunk negotiation on access ports, use explicit VLAN assignment, prune VLANs from trunks, don't use VLAN 1 (default), 802.1X per-port.

**Q:** DoS protection in automotive Ethernet?
**A:** 802.1Qci rate policing, storm control, ingress ACLs, dedicated VLANs for critical traffic, CBS/TAS bandwidth reservation.

**Q:** Secure boot and Ethernet?
**A:** ECU verifies firmware/PHY firmware integrity at boot (signature check). Ensures no tampered code processes network traffic. Part of defense-in-depth.

**Q:** Certificate management for automotive TLS?
**A:** Vehicle PKI: OEM root CA → intermediate CA → per-ECU certificates. Provisioned during manufacturing. Renewed via OTA update channel.

**Q:** Firewall in vehicle network?
**A:** Gateway ECUs implement packet filtering (source/dest IP, port, protocol). Block unauthorized traffic between VLANs/domains.

**Q:** Attack surface of automotive Ethernet?
**A:** Physical tap on cable, compromised ECU (SW exploit), OBD port access, wireless gateway (V2X/WiFi). Defense: MACsec + VLAN + firewall + IDS + authentication.

**Q:** Ethernet security vs CAN security?
**A:** Ethernet: native support for MACsec, TLS, 802.1X (mature IT security). CAN: no built-in security (added via SecOC = limited). Ethernet is inherently more securable.

**Q:** Threat model for automotive Ethernet (STRIDE)?
**A:** Spoofing (fake ECU identity), Tampering (modify frames), Repudiation (deny actions), Information disclosure (eavesdrop), DoS (flood), Elevation (access higher-privilege VLAN).

**Q:** Key rotation in MACsec?
**A:** MKA protocol performs periodic SAK refresh (configurable interval). New key distributed, then cut-over. Minimizes exposure from compromised key.

---

# DECK 10: COMPARISONS & ADVANCED TOPICS (25 cards)

**Q:** 100BASE-T1 vs 100BASE-TX comparison?
**A:** T1: 1 pair, 15m, PAM-3, automotive, echo cancellation. TX: 2 pairs, 100m, MLT-3/4B5B, office, separate TX/RX pairs.

**Q:** Automotive Ethernet vs CAN FD?
**A:** Ethernet: 100M-10G, IP-based, switched, large frames. CAN FD: 2-8 Mbps, bus topology, 64-byte payload, no IP, simple/cheap.

**Q:** Automotive Ethernet vs FlexRay?
**A:** Ethernet: scalable speed, standard IT protocols, switching. FlexRay: 10 Mbps, deterministic TDMA, expensive, discontinued by most OEMs.

**Q:** When to keep CAN instead of Ethernet?
**A:** Low-data-rate sensors (<1 Mbps), simple nodes, cost-sensitive (CAN transceiver = $0.50 vs Ethernet PHY = $3-5), existing validated CAN networks.

**Q:** 10BASE-T1S vs CAN?
**A:** Both: bus topology, multiple nodes. T1S: 10 Mbps, IP-capable, PLCA (deterministic), larger frames. CAN: 1 Mbps, no IP, arbitration, 8-byte data. T1S is IP-enabled CAN replacement.

**Q:** MATEnet vs H-MTD connector?
**A:** MATEnet (TE Connectivity): 2-pin, common for 100M. H-MTD (Rosenberger): higher frequency, supports 1G+, more shielding options. Both sealed, automotive-rated.

**Q:** NXP SJA1110 vs Marvell 88Q5050?
**A:** SJA1110: integrated S32G ARM MCU + switch, programmable, NXP ecosystem. 88Q5050: standalone switch IC, higher port density, TSN, used with external MCU. Both automotive-grade.

**Q:** Compressed vs raw camera over Ethernet?
**A:** Compressed (H.264/H.265): ~15-30 Mbps per camera, fits 100BASE-T1, latency from encoding. Raw: ~200-500 Mbps, needs 1G, zero encoding latency but massive bandwidth.

**Q:** Ethernet vs MIPI CSI-2 for cameras?
**A:** CSI-2: short range (30cm), very high bandwidth (up to 40 Gbps), SoC-to-sensor only. Ethernet: 15m, lower bandwidth, supports switching/routing, true networking. Used differently: CSI-2 = local, Ethernet = remote.

**Q:** Software-Defined Vehicle network requirements?
**A:** High bandwidth (>1G backbone), dynamic service deployment (SOME/IP), OTA capability, centralized compute, network configurability (VLAN/QoS changes without hardware).

**Q:** gPTP vs NTP for automotive?
**A:** gPTP: Layer 2, hardware timestamps, <1µs accuracy, designed for LANs. NTP: Layer 3/application, software timestamps, ~1ms accuracy, designed for internet. Automotive needs gPTP for sensor fusion.

**Q:** AVB vs TSN?
**A:** AVB: subset (CBS+SRP+gPTP), soft real-time for A/V streaming. TSN: superset adding TAS, preemption, policing, redundancy. TSN provides HARD deterministic guarantees needed for ADAS.

**Q:** Ethernet in AAOS vs Classic AUTOSAR?
**A:** AAOS: Linux kernel networking, standard sockets, vsomeip, EthernetManager. Classic: AUTOSAR BSW stack (Eth→EthIf→TcpIp→SoAd), static config, MCAL drivers.

**Q:** SOA (Service-Oriented Architecture) over Ethernet?
**A:** Services offered/consumed dynamically (SOME/IP-SD). Decouples application from network details. Enables: feature addition via OTA, multi-sourcing ECUs, flexible deployment.

**Q:** Multicast efficiency for vehicle data distribution?
**A:** One send reaches all subscribers (vs N unicasts). Example: speed signal → sent once as multicast → IVI, ADAS, body all receive. Bandwidth: 1× vs N×.

**Q:** Zero-copy networking in Linux for automotive?
**A:** AF_XDP (XDP sockets) or mmap'd ring buffers. Frame data stays in kernel buffer, userspace gets pointer. Reduces CPU usage for high-throughput streams (cameras).

**Q:** Network slicing in automotive?
**A:** Different traffic types get guaranteed resources: VLAN (isolation) + PCP/DSCP (priority) + TSN (bandwidth/timing). Like 5G network slicing but at vehicle level.

**Q:** Ethernet for V2X (Vehicle-to-Everything)?
**A:** External: 802.11p/C-V2X radio. Internal: V2X data enters vehicle Ethernet on dedicated VLAN. Gateway translates V2X messages to internal SOME/IP events.

**Q:** Multi-Gig Ethernet use cases?
**A:** 2.5G: Aggregation of multiple 100M camera links. 5G: ADAS compute link. 10G: Backbone between central computers (HPC ↔ HPC).

**Q:** Ethernet power delivery (PoDL)?
**A:** Power over Data Line (IEEE 802.3bu): delivers power + data on same twisted pair. Eliminates separate power cable for remote sensors. Up to 50W.

**Q:** Future of automotive Ethernet?
**A:** 25G/50G standards emerging, all-Ethernet vehicles (no CAN/LIN), 10BASE-T1S replacing CAN, deeper TSN integration, AI-driven traffic management, tighter 5G integration.

**Q:** Automotive Ethernet testing tools?
**A:** Wireshark, Vector CANoe Ethernet, IXIA (traffic gen), Spirent (conformance), Technica Engineering (capture), Keysight (PHY/EMC), OPEN Alliance TC test suites.

**Q:** Cable requirements for automotive Ethernet?
**A:** OPEN Alliance TC2 spec: 100Ω impedance, max attenuation per standard, automotive temp rated, specific twist rate, UTP (100M) or STP (1G), halogen-free jacket.

**Q:** Ethernet switch port states?
**A:** Forwarding (normal operation), Blocking (STP loop prevention), Learning (building MAC table), Disabled (admin shutdown). Automotive: mostly forwarding + disabled.

**Q:** Time-triggered vs event-triggered communication?
**A:** Time-triggered: send at fixed schedule (TSN TAS). Event-triggered: send when data changes (SOME/IP notification). Modern systems mix both: TSN for control + events for status.

---

END OF DOCUMENT 04 — FLASHCARDS (250+)
