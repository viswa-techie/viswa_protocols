# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 08: NETWORKING PROTOCOLS                                              ║
# ║  Ethernet, TCP/IP, UDP, HTTP/2/3, gRPC, QUIC                              ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Networking protocols form the backbone of all interconnected computing — from local Ethernet segments to the global Internet. The TCP/IP stack is the most widely deployed protocol suite in history, enabling everything from web browsing to autonomous vehicle V2X communication.

## PURPOSE
Enable reliable, scalable communication between networked devices across varying distances, speeds, and topologies.

## ENGINEERING PROBLEMS SOLVED
- Addressing and routing across billions of devices
- Reliable delivery over unreliable links (TCP)
- Congestion control (fair sharing of bandwidth)
- Network discovery and auto-configuration (DHCP, ARP, NDP)
- Quality-of-Service for mixed traffic
- Security (TLS, IPsec)

---

## OSI MODEL vs TCP/IP MAPPING

```
OSI Layer          TCP/IP Layer      Protocols
─────────────────────────────────────────────────────
7. Application     Application       HTTP, DNS, MQTT, gRPC
6. Presentation    Application       TLS, SSL, encoding
5. Session         Application       RPC, QUIC sessions
4. Transport       Transport         TCP, UDP, QUIC, SCTP
3. Network         Internet          IPv4, IPv6, ICMP, OSPF, BGP
2. Data Link       Network Access    Ethernet, Wi-Fi, PPP
1. Physical        Network Access    PHY (copper, fiber, radio)
```

---

## PROTOCOL FAMILY TABLE

| Protocol | Layer | Year | Purpose | Speed/Note | Status |
|----------|-------|------|---------|-----------|--------|
| Ethernet | L2 | 1973 | LAN framing | 10M→800G | Dominant |
| IPv4 | L3 | 1981 | Addressing/routing | - | Active |
| IPv6 | L3 | 1998 | Next-gen addressing | 128-bit | Growing |
| ARP | L2/L3 | 1982 | IP→MAC resolution | - | Active |
| ICMP | L3 | 1981 | Error/control (ping) | - | Active |
| TCP | L4 | 1981 | Reliable stream | - | Dominant |
| UDP | L4 | 1980 | Unreliable datagram | - | Active |
| QUIC | L4/L5 | 2021 | Modern transport (UDP-based) | - | Growing |
| HTTP/1.1 | L7 | 1997 | Web | Text-based | Legacy |
| HTTP/2 | L7 | 2015 | Multiplexed web | Binary frames | Active |
| HTTP/3 | L7 | 2022 | QUIC-based web | 0-RTT | Growing |
| gRPC | L7 | 2015 | RPC framework | Protobuf + HTTP/2 | Active |
| DNS | L7 | 1983 | Name resolution | - | Critical |
| DHCP | L7 | 1993 | Auto-config (IP lease) | - | Active |
| BGP | L7 (TCP) | 1994 | Inter-domain routing | - | Critical |
| OSPF | L3 | 1989 | Intra-domain routing | - | Active |

---

## ETHERNET (IEEE 802.3)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Ethernet (IEEE 802.3) |
| Year | 1973 (Xerox), 1983 (IEEE 802.3) |
| Creator | Xerox PARC / DIX (DEC, Intel, Xerox) |
| Speed Evolution | 10M → 100M → 1G → 10G → 25G → 40G → 100G → 400G → 800G |
| Frame | Preamble + Dest MAC + Src MAC + EtherType + Payload + FCS |
| MTU | 1500 bytes (standard), 9000 (jumbo) |
| Addressing | 48-bit MAC address |
| Topology | Star (switched), originally shared bus |

### Ethernet Frame Format
```
┌──────────┬──────────┬──────────┬──────────┬──────────┬──────────┬─────────┐
│Preamble  │ Dest MAC │ Src MAC  │EtherType │ Payload  │ FCS      │ IFG     │
│ 7+1 B    │ 6 bytes  │ 6 bytes  │ 2 bytes  │46-1500 B │ 4 bytes  │ 12 B    │
└──────────┴──────────┴──────────┴──────────┴──────────┴──────────┴─────────┘
```

### Ethernet Speed Standards
| Standard | Speed | PHY | Medium | Use |
|----------|-------|-----|--------|-----|
| 10BASE-T | 10 Mbps | Cat3 | Copper | Legacy |
| 100BASE-TX | 100 Mbps | Cat5 | Copper | Legacy |
| 1000BASE-T | 1 Gbps | Cat5e/6 | Copper | Desktop |
| 10GBASE-T | 10 Gbps | Cat6a/7 | Copper | Server |
| 25GBASE-SR | 25 Gbps | SFP28 | Fiber | Datacenter |
| 100GBASE-SR4 | 100 Gbps | QSFP28 | Fiber | Datacenter |
| 400GBASE-DR4 | 400 Gbps | QSFP-DD | Fiber | Datacenter |
| 800GBASE | 800 Gbps | OSFP | Fiber | AI/HPC |
| 100BASE-T1 | 100 Mbps | Single pair | Copper | Automotive |
| 1000BASE-T1 | 1 Gbps | Single pair | Copper | Automotive |

---

## TCP (Transmission Control Protocol)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | RFC 793 (1981), RFC 9293 (2022 revision) |
| Layer | Transport (L4) |
| Type | Connection-oriented, reliable, byte-stream |
| Features | Flow control (window), Congestion control, Ordering, Retransmission |
| Header | 20 bytes minimum (+ options up to 60) |
| Port range | 0-65535 |

### TCP Header
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│          Source Port          │       Destination Port            │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                        Sequence Number                            │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                     Acknowledgment Number                         │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│Offset│Res│Flags(CWR ECE URG ACK PSH RST SYN FIN)│    Window      │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│           Checksum            │        Urgent Pointer             │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                         Options (variable)                        │
└─────────────────────────────────────────────────────────────────────┘
```

### TCP 3-Way Handshake
```
Client                    Server
  │── SYN (seq=x) ──────────►│
  │◄── SYN-ACK (seq=y, ack=x+1) ──│
  │── ACK (ack=y+1) ────────►│
  │        [Connection Established]
```

### TCP Congestion Control Algorithms
| Algorithm | Year | Approach | Use |
|-----------|------|----------|-----|
| Tahoe | 1988 | Slow start + AIMD | Historical |
| Reno | 1990 | + Fast retransmit/recovery | Legacy |
| NewReno | 1999 | Improved Reno | Legacy |
| CUBIC | 2008 | Cubic function (BIC evolution) | Linux default |
| BBR | 2016 | Bottleneck Bandwidth & RTT | Google/datacenter |
| BBRv2 | 2022 | Improved fairness | Growing |

---

## UDP (User Datagram Protocol)

### Protocol Identity
| Field | Value |
|-------|-------|
| Standard | RFC 768 (1980) |
| Type | Connectionless, unreliable, datagram |
| Header | 8 bytes only (src port, dst port, length, checksum) |
| Use | DNS, DHCP, VoIP, gaming, streaming, QUIC |
| Advantage | No connection overhead, no head-of-line blocking |

### TCP vs UDP Decision
| Need | Choose |
|------|--------|
| Reliable delivery | TCP |
| Low latency (real-time) | UDP |
| Ordered delivery | TCP |
| Multicast/broadcast | UDP |
| Simple request-response | UDP (DNS) |
| Streaming with some loss OK | UDP (VoIP, video) |
| Modern web (HTTP/3) | QUIC over UDP |

---

## QUIC

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | QUIC (originally Quick UDP Internet Connections) |
| Standard | RFC 9000 (2021) |
| Creator | Google (2012 experiment), IETF (standardized) |
| Transport | Over UDP (port 443) |
| Features | Multiplexing, 0-RTT, built-in TLS 1.3, connection migration |
| Use | HTTP/3, Google services, Cloudflare, Meta |

### QUIC vs TCP+TLS
| Feature | TCP + TLS 1.3 | QUIC |
|---------|---------------|------|
| Handshake | 1-RTT (TCP) + 1-RTT (TLS) = 2 RTT | 1-RTT (combined), 0-RTT resumption |
| Head-of-line blocking | Yes (one lost packet blocks all) | No (per-stream) |
| Connection migration | IP change = new connection | Connection ID survives IP change |
| Encryption | Payload only | Headers + payload (everything) |
| Implementation | Kernel (TCP) + userspace (TLS) | All userspace |
| Multiplexing | HTTP/2 streams (but HoL at TCP) | True independent streams |

---

## HTTP/2 and HTTP/3

### HTTP Evolution
| Version | Year | Transport | Key Feature |
|---------|------|-----------|-------------|
| HTTP/1.0 | 1996 | TCP | One request per connection |
| HTTP/1.1 | 1997 | TCP | Keep-alive, pipelining (broken) |
| HTTP/2 | 2015 | TCP + TLS | Binary, multiplexed, server push |
| HTTP/3 | 2022 | QUIC + TLS 1.3 | No HoL blocking, 0-RTT |

### HTTP/2 Features
- **Binary framing**: Replaces text-based HTTP/1 (more efficient parsing)
- **Multiplexing**: Multiple requests/responses on one TCP connection
- **Header compression**: HPACK (reduces repetitive header overhead)
- **Server push**: Server proactively sends resources
- **Stream priority**: Indicate importance of resources

### HTTP/3 Features
- **QUIC transport**: Eliminates TCP head-of-line blocking
- **0-RTT**: Resume connections without handshake
- **QPACK**: Header compression (adapted for QUIC's streams)
- **Connection migration**: Seamless handoff (WiFi → cellular)

---

## gRPC

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | gRPC Remote Procedure Call |
| Creator | Google (2015, open-sourced) |
| Transport | HTTP/2 (→ considering HTTP/3) |
| Serialization | Protocol Buffers (protobuf) |
| Languages | C++, Java, Go, Python, Rust, C#, etc. |
| Patterns | Unary, Server-streaming, Client-streaming, Bidirectional |
| Use | Microservices, cloud APIs, mobile→server, inter-service |

### gRPC vs REST
| Feature | REST (HTTP+JSON) | gRPC (HTTP/2+Protobuf) |
|---------|-------------------|------------------------|
| Serialization | JSON (text, ~3-10× larger) | Protobuf (binary, compact) |
| Schema | OpenAPI (optional) | .proto (required, strict) |
| Streaming | Workarounds (SSE, WebSocket) | Native bidirectional |
| Code generation | Optional | Built-in (all languages) |
| Browser support | Native | Requires grpc-web proxy |
| Performance | Good | Better (2-10× smaller, faster) |
| Use | Public APIs | Internal microservices |

### gRPC in Android/AAOS
- Vehicle HAL communication (considered for AIDL replacement in some layers)
- Cloud services connectivity
- OTA update signaling
- Telematics backend

---

## LINUX NETWORKING STACK

```
┌────────────────────────────────────────────────────────────┐
│  Application (socket API: send/recv/connect/listen)         │
├────────────────────────────────────────────────────────────┤
│  Socket Layer (AF_INET, AF_INET6, AF_PACKET)               │
├────────────────────────────────────────────────────────────┤
│  Transport (TCP: net/ipv4/tcp*.c, UDP: net/ipv4/udp.c)     │
├────────────────────────────────────────────────────────────┤
│  Network (IP: routing, forwarding, fragmentation)           │
│  ├── Netfilter (iptables/nftables: firewalling)            │
│  ├── Routing (FIB: Forwarding Information Base)            │
│  └── Neighbor (ARP/NDP)                                    │
├────────────────────────────────────────────────────────────┤
│  Queue Discipline (tc: traffic shaping, QoS)               │
├────────────────────────────────────────────────────────────┤
│  Network Device (net_device, NAPI polling)                  │
├────────────────────────────────────────────────────────────┤
│  Driver (e1000e, ixgbe, mlx5, igc, stmmac)                │
├────────────────────────────────────────────────────────────┤
│  Hardware (NIC: DMA ring buffers, offload engines)          │
└────────────────────────────────────────────────────────────┘
```

### Key Linux Networking Concepts
- **NAPI**: New API — interrupt coalescing + polling for high-speed NICs
- **XDP (eXpress Data Path)**: eBPF programs at driver level (pre-stack)
- **TC (Traffic Control)**: QoS, rate limiting, classification
- **Netfilter**: Packet filtering framework (iptables/nftables)
- **Network namespaces**: Isolated network stacks (containers)
- **Socket buffer (sk_buff)**: Core data structure carrying packets

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | TCP 3-way handshake? | SYN → SYN-ACK → ACK |
| 2 | TCP vs UDP? | TCP: reliable, ordered. UDP: unreliable, fast |
| 3 | Ethernet MTU? | 1500 bytes (standard), 9000 (jumbo) |
| 4 | MAC address size? | 48 bits (6 bytes) |
| 5 | QUIC runs over? | UDP (port 443 typically) |
| 6 | HTTP/3 transport? | QUIC (not TCP) |
| 7 | QUIC 0-RTT? | Resume connection without handshake (session ticket) |
| 8 | gRPC serialization? | Protocol Buffers (protobuf) — binary, compact |
| 9 | TCP CUBIC? | Default Linux congestion control (cubic function window) |
| 10 | BBR measures? | Bottleneck Bandwidth and Round-trip propagation time |
| 11 | XDP purpose? | eBPF packet processing at driver level (pre-stack, fast) |
| 12 | ARP resolves? | IP address → MAC address (L3→L2) |
| 13 | BGP purpose? | Inter-domain routing (between autonomous systems) |
| 14 | NAPI? | Interrupt coalescing + polling for high-speed networking |
| 15 | Head-of-line blocking? | TCP: one lost packet blocks all streams. QUIC: no (per-stream) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Explain TCP head-of-line blocking and how QUIC solves it.**
A: In TCP, data is a single byte stream. If packet #5 is lost, packets #6-#10 (already received) cannot be delivered to the application until #5 is retransmitted — ALL streams multiplexed over that TCP connection are blocked. HTTP/2 suffers this badly. QUIC uses independent streams within one connection — loss in stream A only blocks stream A. Streams B, C, D continue unaffected. This is possible because QUIC implements reliability per-stream, not per-connection.

**Q2: What is TCP congestion control and how does BBR differ from CUBIC?**
A: Loss-based (CUBIC): Increases sending window until packet loss detected, then reduces by factor. Treats loss as congestion signal. Problem: buffers fill before loss (bufferbloat), doesn't work well on lossy links. BBR (Bottleneck Bandwidth and RTT): Measures actual bottleneck bandwidth and min RTT, sends at exactly that rate. Doesn't rely on loss. Better on lossy/high-BDP links, lower latency. Trade-off: BBR can be unfair to CUBIC flows.

**Q3: How does Ethernet switching differ from routing, and when is each used?**
A: Switching (L2): Forwards frames based on MAC address (CAM table). Same broadcast domain. Low latency, simple. Used within a LAN segment. Routing (L3): Forwards packets based on IP address (routing table). Different networks/subnets. Can cross WAN. Adds latency (TTL decrement, header rewrite). In practice: switches within datacenter rack, routers between networks/sites. Modern: L3 switches combine both in hardware.

**Q4: Explain Linux NAPI and why it's needed for high-speed networking.**
A: Traditional: each packet triggers a hardware interrupt → interrupt handler → softirq → packet processing. At 100 Gbps, millions of packets/sec would overwhelm CPU with interrupts. NAPI: on first packet, interrupt fires and DISABLES further interrupts. Then CPU polls the NIC (NAPI poll) processing packets in batches. When no more packets, re-enables interrupts. This converts interrupt-per-packet to batch processing, achieving line-rate performance.

**Q5: Compare gRPC and REST for automotive AAOS cloud services.**
A: REST: ubiquitous, browser-compatible, human-readable JSON, larger payloads, one request-response per HTTP connection (HTTP/1.1) or multiplexed (HTTP/2). Good for public APIs, simple CRUD. gRPC: binary protobuf (2-10× smaller = less bandwidth = cellular savings), strict schema (.proto = fewer bugs), native streaming (real-time telemetry), HTTP/2 multiplexing built-in. Better for: vehicle-to-cloud telemetry streams, OTA update signaling, high-frequency data reporting. AAOS typically uses gRPC for internal services and REST for partner/third-party APIs.

---

END OF PART 08 — NETWORKING PROTOCOLS
