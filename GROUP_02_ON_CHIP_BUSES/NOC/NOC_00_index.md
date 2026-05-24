# NOC (Network-on-Chip) — COMPLETE ENCYCLOPEDIA
# ════════════════════════════════════════════════════════════════════
# Protocol: Network-on-Chip | Documents: 00-08 Combined
# ════════════════════════════════════════════════════════════════════

---

# DOCUMENT 00: INDEX

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | Network-on-Chip |
| Category | On-chip communication architecture |
| Key Feature | Packet-switched, routed network replacing bus/crossbar |
| Motivation | Buses don't scale beyond ~10 masters; NOC scales to 100+ |
| Implementations | ARM CMN-600/700, Qualcomm NOC, Intel mesh, Arteris FlexNOC |
| SA8295P Usage | Qualcomm NOC interconnects all subsystems |
| Topology | Mesh, ring, tree, or hybrid |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | NOC | Network-on-Chip |
| 2 | Router | Switching element at each node |
| 3 | Link | Physical connection between routers |
| 4 | Flit | Flow control digit (smallest unit) |
| 5 | Phit | Physical digit (one clock cycle on wire) |
| 6 | Packet | Complete message (header + payload + tail) |
| 7 | Header flit | Contains routing info (dest, src, type) |
| 8 | Body flit | Payload data |
| 9 | Tail flit | End of packet marker |
| 10 | Mesh topology | 2D grid of routers |
| 11 | Ring topology | Circular connection |
| 12 | Tree topology | Hierarchical connection |
| 13 | Torus | Mesh with wraparound links |
| 14 | XY routing | Deterministic: first X then Y |
| 15 | Adaptive routing | Route based on congestion |
| 16 | Source routing | Path encoded in header |
| 17 | Virtual channel (VC) | Multiple logical channels per link |
| 18 | Wormhole routing | Head flit reserves path, body follows |
| 19 | Store-and-forward | Full packet buffered before forwarding |
| 20 | Cut-through | Forward after header decoded |
| 21 | Credit-based flow | Downstream sends credits when buffer free |
| 22 | On/off flow | Backpressure signal (valid/ready or req/ack) |
| 23 | Network Interface (NI) | Converts protocol to NOC packets |
| 24 | Ingress | Entry point to NOC |
| 25 | Egress | Exit point from NOC |
| 26 | Arbiter | Resolves port contention in router |
| 27 | Crossbar (in router) | Internal switch matrix |
| 28 | Deadlock | Circular wait for resources |
| 29 | Livelock | Packets routed forever, never arrive |
| 30 | Starvation | Node never granted access |
| 31 | QoS | Quality of Service (priority, bandwidth) |
| 32 | Latency | Cycles from source to destination |
| 33 | Throughput | Data rate through network |
| 34 | Bandwidth | Link capacity (bits/cycle × links) |
| 35 | Congestion | Traffic exceeds link capacity |
| 36 | Hotspot | Node receiving disproportionate traffic |
| 37 | Bisection bandwidth | BW across network cut in half |
| 38 | Hop count | Number of routers traversed |
| 39 | Pipeline stage | Router internal pipeline (RC/VA/SA/ST) |
| 40 | RC | Route Computation stage |
| 41 | VA | Virtual channel Allocation |
| 42 | SA | Switch Allocation (arbiter) |
| 43 | ST | Switch Traversal |
| 44 | LT | Link Traversal |
| 45 | Injection rate | Rate packets enter network |
| 46 | Deflection routing | Misroute on contention |
| 47 | NIC/NIU | Network Interface Controller/Unit |
| 48 | Transaction layer | Protocol (AXI/CHI) transaction handling |
| 49 | Transport layer | Packetization, routing |
| 50 | Power gating | Shut off unused NOC regions |

---

# DOCUMENT 01: MASTER THEORY

## §1: WHY NOC

Traditional buses (AHB, AXI crossbar) have limits:
- **Bus**: Shared medium, 1 transaction at a time → doesn't scale
- **Crossbar**: Full connectivity, but O(N²) wires → too expensive for N>8
- **NOC**: Packet-switched, only local wires, scales to 100+ nodes

```
Scalability:    Bus < Crossbar < NOC
Complexity:     Bus < NOC < Crossbar (for large N)
Bandwidth:      Bus < Crossbar ≈ NOC (for large N)
```

## §2: NOC ARCHITECTURE

```
┌────────────────────────────────────────────────┐
│                                                │
│  ┌─────┐     ┌─────┐     ┌─────┐             │
│  │ NI  │─────│  R  │─────│  R  │─── ...      │
│  │(CPU)│     │(0,0)│     │(1,0)│             │
│  └─────┘     └──┬──┘     └──┬──┘             │
│                  │            │                │
│              ┌──┴──┐     ┌──┴──┐             │
│              │  R  │─────│  R  │─── ...      │
│              │(0,1)│     │(1,1)│             │
│              └──┬──┘     └──┬──┘             │
│                 │            │                │
│              ┌──┴──┐     ┌──┴──┐             │
│              │ NI  │     │ NI  │             │
│              │(Mem)│     │(GPU)│             │
│              └─────┘     └─────┘             │
│                                                │
│  R = Router, NI = Network Interface            │
└────────────────────────────────────────────────┘
```

## §3: NETWORK INTERFACE (NI)

The NI bridges between protocol world (AXI/CHI) and NOC packets:

```
AXI Master → [NI: Packetizer] → NOC Packet → Router → ... → Router → [NI: Depacketizer] → AXI Slave
```

NI responsibilities:
1. **Packetization**: Convert AXI transaction into NOC flits (header + data)
2. **Depacketization**: Reassemble flits into AXI transaction
3. **Reordering**: Handle out-of-order responses
4. **Flow control**: Interface between protocol flow (READY/VALID) and NOC credits
5. **QoS**: Tag packets with priority

## §4: ROUTER ARCHITECTURE

A typical NOC router has:
- **Input ports**: N directions + local (e.g., 5 for 2D mesh: N,S,E,W,Local)
- **Output ports**: Same N+1 directions
- **Input buffers**: Store incoming flits (per VC)
- **Crossbar**: Connect any input to any output
- **Arbiter**: Resolve contention when multiple inputs want same output
- **Route computation**: Determine output port from destination

### Router Pipeline (typical 3-5 stages):
```
Flit arrives → RC → VA → SA → ST → LT → Next router
               │     │     │     │     │
         Route  VC    Switch Switch Link
         Compute Alloc Alloc  Trav  Trav
```

## §5: ROUTING ALGORITHMS

### Deterministic: XY Routing
```
1. Compare current_X with dest_X → route East/West until X matches
2. Compare current_Y with dest_Y → route North/South until Y matches

Properties:
- Simple, low area
- Deadlock-free (no circular dependency in 2D mesh)
- Not adaptive (ignores congestion)
```

### Adaptive Routing
- Choose from multiple valid paths based on congestion
- More complex, requires escape channels for deadlock freedom
- Better load balancing

### Source Routing
- Entire path encoded in header flit
- Router just reads next hop, no computation needed
- Used in Qualcomm NOC (paths programmed at boot)

## §6: FLOW CONTROL

### Credit-Based
```
Downstream has B buffer slots → gives B credits to upstream
Upstream sends 1 flit = consume 1 credit
Downstream frees buffer = return 1 credit
Credits = 0 → upstream must stop
```

### Virtual Channels (VC)
Multiple logical channels sharing one physical link:
```
Physical link → VC0 (buffer set 0) ← Priority traffic
             → VC1 (buffer set 1) ← Normal traffic
             → VC2 (buffer set 2) ← Low priority
```

Benefits:
- Deadlock avoidance (different VCs for different message classes)
- QoS (priority VCs)
- Head-of-line blocking avoidance

## §7: SWITCHING TECHNIQUES

| Technique | Buffering | Latency | Throughput |
|-----------|-----------|---------|------------|
| Store-and-forward | Full packet | High | Lower |
| Cut-through | Header only | Low | Higher |
| Wormhole | 1-2 flits | Low | High |

**Wormhole** (most common in NOC):
- Header flit reserves path through routers
- Body flits follow like a "worm"
- Tail flit releases resources
- Low buffer requirements (only 1-2 flit deep)

## §8: DEADLOCK

**Cause**: Circular dependency in resource (buffer/VC) allocation.
```
Router A waiting for buffer in Router B
Router B waiting for buffer in Router C
Router C waiting for buffer in Router A → DEADLOCK!
```

**Prevention**:
1. **XY routing**: No turns that create cycles
2. **Virtual channels**: Separate request/response VCs (break protocol-level deadlock)
3. **Escape channels**: Dedicated deadlock-free VC (e.g., dimension-ordered)

**Protocol deadlock**: Request depends on response, response blocked by request → Need separate VCs for request and response traffic.

## §9: QOS IN NOC

| Mechanism | How it works |
|-----------|-------------|
| Priority levels | VC or flit priority field, higher priority wins arbitration |
| Bandwidth regulation | Rate limiters at NI (tokens/leaky bucket) |
| Latency targets | Priority + dedicated paths |
| Starvation prevention | Age-based priority boost |

Qualcomm NOC QoS:
- Multiple urgency levels (0-7)
- Bandwidth limiter per master
- Danger/safe thresholds (back-pressure to masters)

## §10: NOC IN SA8295P (QUALCOMM)

Qualcomm uses proprietary NOC:
- **System NOC**: Connects CPU, GPU, DDR, peripherals
- **Config NOC**: Low-bandwidth configuration access (like APB role)
- **Memory NOC**: High-bandwidth DDR access paths
- **Features**: Source routing, QoS urgency levels, power collapse regions, BIMC (Bus Integrated Memory Controller)

Programmer-visible registers:
- QoS priority registers per master port
- Bandwidth limiter registers
- Route table registers (programmed at boot)

## §11: NOC vs ALTERNATIVES

| Feature | Bus (AHB) | Crossbar (AXI) | NOC |
|---------|-----------|----------------|-----|
| Wires | O(N) | O(N²) | O(N) |
| Max masters | ~3-4 | ~8-16 | 100+ |
| Bandwidth | 1 path | N paths | Many paths |
| Latency | 1 cycle | 1-2 cycles | Multi-hop |
| Area | Small | Large (N>8) | Medium |
| Power | Low | High | Medium |
| Clock | Single | Single/async | Can be multi-clock |
| Typical use | Periph subsys | IP block connect | System-level |

## §12: NOC PERFORMANCE METRICS

| Metric | Formula/Definition |
|--------|-------------------|
| Latency | Hop_count × (router_pipe + link) + serialization |
| Throughput | Min(injection_rate, bisection_BW / traffic_pattern) |
| Bisection BW | Links_crossing_bisection × link_BW |
| Link BW | Flit_width × Frequency |
| Hop count (mesh) | |Δx| + |Δy| (Manhattan distance) |
| Zero-load latency | Hop_count × pipe_stages + packet_length/link_BW |

---

# DOCUMENT 02: DIAGRAMS

## 2D Mesh Topology (4×4)
```
(0,3)──(1,3)──(2,3)──(3,3)
  │      │      │      │
(0,2)──(1,2)──(2,2)──(3,2)
  │      │      │      │
(0,1)──(1,1)──(2,1)──(3,1)
  │      │      │      │
(0,0)──(1,0)──(2,0)──(3,0)

Each intersection = Router
Each line = bidirectional link
```

## Router Internal Architecture
```
         North In    East In
            │           │
            ▼           ▼
     ┌──────────────────────┐
     │  ┌───┐     ┌───┐    │
     │  │IB │     │IB │    │  IB = Input Buffer (per VC)
     │  └─┬─┘     └─┬─┘    │
     │    │  RC/VA   │      │
     │    ▼          ▼      │
     │  ┌──────────────┐    │
     │  │   ARBITER     │    │
     │  └──────┬───────┘    │
     │         │  SA        │
     │  ┌──────┴───────┐    │
     │  │   CROSSBAR    │    │  5×5 for 2D mesh
     │  └──┬────────┬──┘    │
     │     │   ST   │       │
     └─────┼────────┼───────┘
           ▼        ▼
       South Out  West Out
```

## XY Routing Example
```
Source: (0,0) → Destination: (3,2)

(0,0) →X→ (1,0) →X→ (2,0) →X→ (3,0)
                                  │
                                  Y
                                  ▼
                               (3,1)
                                  │
                                  Y
                                  ▼
                               (3,2) ← DESTINATION

Hop count = |3-0| + |2-0| = 5 hops
First route X (East×3), then route Y (North×2)
```

## Packet/Flit Structure
```
┌─────────────────────────────────────────────┐
│                 PACKET                       │
├──────────┬──────────┬───────┬──────────────┤
│  Header  │  Body₀   │ Body₁ │    Tail      │
│  Flit    │  Flit    │ Flit  │    Flit      │
├──────────┼──────────┼───────┼──────────────┤
│ Dest     │ Data[0]  │Data[1]│ Data[2]+EOM  │
│ Src      │          │       │              │
│ Type     │          │       │              │
│ Length   │          │       │              │
│ QoS      │          │       │              │
└──────────┴──────────┴───────┴──────────────┘
```

## Credit-Based Flow Control
```
Router A                    Router B
  │     ← Credit (buffer free) ←  │
  │     ← Credit                ←  │
  │                                │
  │     → Flit (consume credit) →  │
  │     → Flit (consume credit) →  │
  │                                │
  │     (credits=0, must wait)     │
  │                                │
  │     ← Credit (B freed buf) ←   │
  │     → Flit                  →  │
```

---

# DOCUMENT 03: INTERVIEW QUESTIONS

## JUNIOR (5)
**Q1**: Why use NOC instead of a bus? **A:** Buses don't scale — shared medium limits to ~3-4 masters. NOC uses packet switching with local wires, scales to 100+ nodes.
**Q2**: What is a flit? **A:** Flow control digit — smallest unit of data transferred between routers (typically 64-256 bits wide).
**Q3**: What is XY routing? **A:** Deterministic routing for 2D mesh: first route in X-direction, then Y-direction. Simple and deadlock-free.
**Q4**: What is a virtual channel? **A:** Logical sub-channel sharing a physical link, with its own buffer. Used for QoS, deadlock avoidance.
**Q5**: What is the role of Network Interface? **A:** Converts between protocol (AXI/CHI) transactions and NOC packets (packetization/depacketization).

## MID (5)
**Q6**: How does wormhole routing work? **A:** Header flit reserves path through routers. Body flits follow in pipeline fashion. Tail flit releases resources. Low buffer needs (1-2 flits per VC).
**Q7**: How does credit-based flow control prevent overflow? **A:** Receiver tells sender how many buffer slots available (credits). Sender can only send if credits > 0. Each flit consumes a credit; freed buffer returns a credit.
**Q8**: How is deadlock prevented in NOC? **A:** (1) Restricted routing (XY — no cycles). (2) Virtual channels — separate VCs for different message types break protocol-level cycles. (3) Escape VCs with dimension-ordered routing.
**Q9**: What is bisection bandwidth? **A:** Total bandwidth of all links crossing when you cut the network in half. Limits worst-case throughput for all-to-all traffic.
**Q10**: How does QoS work in NOC? **A:** Priority VCs, urgency fields in flit headers (higher priority wins arbitration), rate limiters at NI, age-based promotion to prevent starvation.

## SENIOR (3)
**Q11**: Design a router for a 2D mesh supporting 4 VCs, wormhole routing, and credit-based flow control. What's the area/latency trade-off?
**Q12**: How does Qualcomm's NOC handle multiple clock domains and power collapse?
**Q13**: Compare ARM CMN-700 (CHI mesh) with Qualcomm NOC in terms of topology, routing, and QoS mechanisms.

## QUICK-FIRE (10)
| # | Q | A |
|---|---|---|
| 1 | NOC packet smallest unit? | Flit |
| 2 | Mesh hop count formula? | |Δx| + |Δy| |
| 3 | Router pipeline stages? | RC, VA, SA, ST, LT |
| 4 | Most common NOC switching? | Wormhole |
| 5 | VC purpose? | Deadlock avoidance + QoS |
| 6 | Deadlock in XY routing? | Impossible (no cycles) |
| 7 | NI stands for? | Network Interface |
| 8 | Credits exhausted → ? | Sender must stop |
| 9 | Qualcomm NOC routing? | Source routing |
| 10 | NOC vs bus wires? | O(N) vs O(N) but NOC is pipelined/parallel |

---

# DOCUMENT 04: FLASHCARDS (60 cards, 3 decks)

## DECK 1: FUNDAMENTALS (20)
| # | Front | Back |
|---|-------|------|
| 1 | NOC stands for? | Network-on-Chip |
| 2 | Why NOC over bus? | Scalability (100+ nodes) |
| 3 | Why NOC over crossbar? | Wire complexity O(N) vs O(N²) |
| 4 | Flit? | Flow control digit (transfer unit) |
| 5 | Phit? | Physical digit (1 cycle on wire) |
| 6 | Packet structure? | Header + Body + Tail flits |
| 7 | Header flit carries? | Dest, Src, Type, Length, QoS |
| 8 | Router? | Switching element, buffers + crossbar + arbiter |
| 9 | Link? | Wire between adjacent routers |
| 10 | Hop? | One router traversal |
| 11 | NI/NIU? | Network Interface (protocol↔NOC conversion) |
| 12 | Ingress? | Entry to NOC |
| 13 | Egress? | Exit from NOC |
| 14 | Topology? | Physical arrangement of routers+links |
| 15 | 2D Mesh? | Grid topology (most common) |
| 16 | Ring? | Circular topology |
| 17 | Torus? | Mesh with wraparound |
| 18 | Tree? | Hierarchical topology |
| 19 | Flit width? | 64-512 bits (implementation) |
| 20 | Link bandwidth? | Flit_width × frequency |

## DECK 2: ROUTING & FLOW CONTROL (20)
| # | Front | Back |
|---|-------|------|
| 1 | XY routing? | First X, then Y (deterministic, deadlock-free) |
| 2 | Adaptive routing? | Choose path based on congestion |
| 3 | Source routing? | Path encoded in header by sender |
| 4 | Deadlock? | Circular wait for buffers/VCs |
| 5 | Livelock? | Packet endlessly misrouted |
| 6 | Wormhole routing? | Head reserves path, body follows |
| 7 | Store-and-forward? | Buffer full packet, then forward |
| 8 | Cut-through? | Forward after header decoded |
| 9 | Virtual channel? | Logical sub-channel with own buffer |
| 10 | VC for deadlock? | Separate VCs break dependency cycles |
| 11 | Credit-based flow? | Sender needs credit to send flit |
| 12 | Credit = ? | One buffer slot available |
| 13 | On/off flow? | Binary backpressure signal |
| 14 | Head-of-line blocking? | Blocked flit blocks entire VC |
| 15 | VC solves HOL? | Yes (blocked on VC0 doesn't block VC1) |
| 16 | Escape VC? | Deadlock-free VC for guaranteed progress |
| 17 | Router pipeline? | RC→VA→SA→ST→LT |
| 18 | RC? | Route Computation |
| 19 | SA? | Switch Allocation (arbitration) |
| 20 | ST? | Switch Traversal (crossbar) |

## DECK 3: SYSTEM & PERFORMANCE (20)
| # | Front | Back |
|---|-------|------|
| 1 | Zero-load latency? | Hops × pipe_stages + serialization |
| 2 | Bisection BW? | BW of links crossing center cut |
| 3 | Throughput limit? | Min(injection, bisection_BW/pattern) |
| 4 | Hotspot? | Node receiving excess traffic |
| 5 | Congestion? | Traffic exceeds link capacity |
| 6 | QoS mechanism? | Priority VC + rate limiter + urgency |
| 7 | Qualcomm NOC type? | Proprietary, source-routed |
| 8 | ARM CMN-600? | CHI mesh interconnect |
| 9 | Arteris FlexNOC? | Commercial NOC IP |
| 10 | Power gating NOC? | Can shut off unused regions |
| 11 | CDC in NOC? | Async FIFOs at clock domain boundaries |
| 12 | NOC clock? | Can be independent (async crossings) |
| 13 | NOC verification? | Traffic generators + protocol checkers |
| 14 | NOC debug? | Transaction trace, latency counters |
| 15 | SA8295P system NOC? | Connects CPU/GPU/DDR/peripherals |
| 16 | Config NOC? | Low-BW register access |
| 17 | Memory NOC? | High-BW DDR paths |
| 18 | Injection rate? | Packets/cycle entering network |
| 19 | Saturation? | Network throughput plateaus |
| 20 | Average latency? | Increases sharply near saturation |

---

# DOCUMENT 06: CHEATSHEET

## ROUTER PIPELINE
```
RC → VA → SA → ST → LT
(1)   (1)   (1)   (1)  (1)  = 5 cycles typical per hop
```

## MESH HOP COUNT
```
Hops = |src_x - dst_x| + |src_y - dst_y|
```

## LATENCY FORMULA
```
Latency = H × t_r + L/b
  H = hop count
  t_r = per-router delay (pipeline stages)
  L = packet length (flits)
  b = link bandwidth (flits/cycle)
```

## BANDWIDTH
```
Link BW = flit_width × frequency
Bisection BW = num_bisection_links × link_BW
```

## VIRTUAL CHANNEL USAGE
```
VC0: Request messages (read/write requests)
VC1: Response messages (data, completions)  
VC2: Snoop messages (coherence)
VC3: Low-priority / best-effort

→ Separating request/response VCs prevents protocol deadlock
```

## QUALCOMM NOC QoS REGISTERS
```
Priority:  NOC_QOS_PRIORITY      (per master, 0-7 urgency)
Limiter:   NOC_QOS_LIMITER       (bandwidth limit, saturation/threshold)
Mode:      NOC_QOS_MODE          (fixed/limiter/bypass/regulator)
```

---

# DOCUMENT 07: LABS & DEBUG

## DEBUG SCENARIO 1: NOC TIMEOUT
**Symptoms**: Transaction timeout, AXI SLVERR or DECERR.
**Diagnosis**: Check NOC error registers (ERRLOG), identify which path failed.
**Common causes**: Target powered down, invalid address (no route), NOC clock gated.

## DEBUG SCENARIO 2: PERFORMANCE DEGRADATION
**Symptoms**: High latency, low throughput.
**Diagnosis**: Check NOC performance counters (saturation, outstanding transactions). Check QoS settings — low-priority master may be starved or critical path congested.
**Fix**: Tune QoS priority, adjust bandwidth limiters, check for hotspots.

## DEBUG SCENARIO 3: NOC DEADLOCK
**Symptoms**: System hang, multiple masters stuck.
**Diagnosis**: Check if request VC full everywhere (circular dependency). Verify VC separation for request/response. Check for missing clock/reset in NOC fabric.
**Fix**: Ensure VC protocol separation; add timeout + error path.

---

# DOCUMENT 08: REFERENCES

## Key Resources
| Resource | Description |
|----------|-------------|
| "Networks-on-Chip" (Dally & Towles) | Foundational textbook |
| ARM CoreLink CMN-600 TRM | CHI mesh NOC implementation |
| ARM CoreLink CMN-700 TRM | Latest ARM NOC |
| Arteris FlexNOC docs | Commercial NOC IP |
| Qualcomm NOC register docs | Platform-specific (NDA) |

## Study Plan (1 week)
| Day | Topic |
|-----|-------|
| 1 | Motivation, topology (mesh/ring/tree) |
| 2 | Routing (XY, adaptive, source) |
| 3 | Flow control (credits, VC, wormhole) |
| 4 | Router architecture (pipeline, arbiter) |
| 5 | Deadlock/QoS/performance |
| 6 | Platform-specific: Qualcomm NOC, ARM CMN |
| 7 | Debug (registers, trace, performance counters) |

## Glossary (15 terms)
| Term | Definition |
|------|------------|
| NOC | Network-on-Chip |
| Router | Switching node in NOC |
| Flit | Flow control unit |
| Packet | Header+body+tail flits |
| VC | Virtual Channel |
| NI | Network Interface |
| XY routing | Deterministic dimension-ordered routing |
| Wormhole | Head reserves, body follows |
| Credit | Flow control token |
| Deadlock | Circular buffer dependency |
| Bisection BW | Bandwidth across center cut |
| QoS | Quality of Service |
| Hotspot | Congested node |
| Mesh | 2D grid topology |
| Arbiter | Resolves port contention |

---

END — NOC ENCYCLOPEDIA COMPLETE (8/8 combined)
