# Intel UPI (Ultra Path Interconnect) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS UPI?

Ultra Path Interconnect (UPI) is Intel's cache-coherent CPU-to-CPU interconnect used in Xeon Scalable processors. It provides high-bandwidth, low-latency communication between sockets in multi-socket servers, enabling shared-memory multiprocessing with hardware-coherent caches.

**Evolution**: FSB → QPI (2008, Nehalem) → UPI (2017, Skylake-SP)

Key improvements over QPI:
- Higher efficiency (smaller flit overhead, better encoding)
- Lower latency (reduced protocol hops)
- Flexible power management (L0p partial-width mode)
- Improved snoop modes for better scalability

---

## 2. PROTOCOL LAYERS

UPI uses a 5-layer architecture (similar to networking OSI model):

### Physical Layer
- **Signaling**: Differential serial (NRZ or PAM4 in newer gens)
- **Width**: 20 data lanes + clock per direction (Tx and Rx separate)
- **Data rate**: 10.4-20 GT/s per lane
- **BW formula**: 20 lanes × data_rate / 8 bits = bytes/s/direction
- **Link training**: LTSSM (detect → polling → config → L0)
- **Features**: Lane reversal, polarity inversion, electrical idle detect

### Link Layer
- **Unit**: Flit (flow control unit) = 192 bits (3 slots of 72 bits each with some overhead)
- **CRC**: 16-bit CRC per flit for error detection
- **Retry**: On CRC error, replay from buffer (no NAK — uses credit-based)
- **Flow control**: Credit-based (sender must have credits to send)
- **Credits**: VNA (shared adaptive), VN0 (dedicated to message classes), VN1 (deadlock avoidance)

### Routing Layer
- **2S**: Direct link — no routing needed
- **4S**: Each CPU connects to other 3 via separate links (full mesh) or ring
- **8S**: Requires intermediate hops — routing tables determine next hop
- **Routing table**: Programmable per destination NodeID

### Protocol Layer (Transaction Layer)
- **Agents**: Caching Agent (CA), Home Agent (HA)
- **Message classes**: Request (REQ), Snoop (SNP), Response (RSP), Data (DATA), NCB/NCS (non-coherent)
- **Transactions**: Read, Write, Invalidate, Writeback
- **Coherence**: MESIF protocol with snoop filter optimization

### Upper layers (Software)
- Exposed as NUMA topology to OS
- Memory-mapped I/O for configuration

---

## 3. MESIF COHERENCE PROTOCOL

Intel extends standard MESI with the Forward (F) state:

| State | Meaning | Can supply data? |
|-------|---------|-----------------|
| M (Modified) | Dirty, exclusive | Yes (+ writeback) |
| E (Exclusive) | Clean, exclusive | Yes (→F or S) |
| S (Shared) | Clean, multiple holders | No (home supplies) |
| I (Invalid) | Not cached | No |
| F (Forward) | Clean, designated forwarder | Yes (only F responds) |

### Why F state?
In standard MESI with multiple sharers: who responds? All S-state holders could respond → conflicts. The F state designates exactly ONE sharer as the responder. This eliminates multiple responses and reduces bus traffic.

### Snoop Modes
Intel supports multiple snoop modes optimized for different topologies:

| Mode | Description | Best For |
|------|-------------|----------|
| Early Snoop | Snoops sent in parallel with home lookup | Low latency (2S) |
| Home Snoop | Home agent checks directory first, then snoops | Scalable (4S+) |
| Source Snoop | Requester sends snoops directly | 2S optimization |
| Directory/HitME | Snoop filter avoids unnecessary snoops | Large systems |

---

## 4. FLIT FORMAT

```
UPI Flit (192 bits total):
┌─────────────────────────────────────────────────────────┐
│  Slot 0 (72b)  │  Slot 1 (72b)  │  Slot 2 (72b)       │
│  + Header/CRC overhead                                   │
└─────────────────────────────────────────────────────────┘

Header Flit fields:
- Opcode (5-6 bits): message type
- Address (46 bits): physical address
- NodeID: source/destination
- Message class (3 bits): REQ/SNP/RSP/DATA/NCB/NCS
- HTID: Home Tracker ID (transaction tracking)

Data Flit:
- 64 bytes of cache line data (requires 4 data flits at 16B each)
```

### Message Classes
| Class | Abbr | Purpose |
|-------|------|---------|
| Request | REQ | Read/Write requests from CA to HA |
| Snoop | SNP | Coherence probes from HA to CAs |
| Response | RSP | Completion responses, acks |
| Data | DATA | Cache line data transfers |
| Non-Coherent Bypass | NCB | MMIO, config (no coherence) |
| Non-Coherent Standard | NCS | Interrupt, special |

---

## 5. TRANSACTION EXAMPLES

### Read (Clean line at remote home):
1. CA sends **RdData** (REQ) to Home Agent
2. HA checks snoop filter: no other socket caches it
3. HA fetches from DRAM
4. HA sends **DataC_E** (DATA) to CA (line in E state)
5. CA caches line, transaction complete

### Read (Dirty line at peer socket):
1. CA0 sends **RdData** (REQ) to HA
2. HA checks snoop filter: CA1 has line in M
3. HA sends **SnpData** (SNP) to CA1
4. CA1 sends **DataC_F** (DATA) directly to CA0 (peer-to-peer transfer)
5. CA1 transitions M→I (or S depending on snoop type)
6. CA1 sends **RspFwdI** (RSP) to HA
7. HA updates snoop filter, transaction complete

### Write (Invalidate other copies):
1. CA0 sends **RdInvOwn** (REQ) to HA
2. HA checks snoop filter: CA1, CA2 have line in S
3. HA sends **SnpInv** (SNP) to CA1, CA2
4. CA1, CA2 invalidate, send **RspI** (RSP) to HA
5. HA sends **DataC_E** (DATA) to CA0
6. CA0 gets line in E, writes → transitions to M

---

## 6. FLOW CONTROL (CREDITS)

UPI uses credit-based flow control to prevent buffer overflow:

### Virtual Networks
- **VN0**: Dedicated credits per message class (prevents deadlock between classes)
- **VN1**: Additional dedicated credits (used for anti-deadlock in multi-hop)
- **VNA**: Adaptive shared credits (any message class can use them — high utilization)

### Credit operation:
1. Receiver advertises initial credits (buffer space in flits)
2. Sender consumes 1 credit per flit sent
3. When credits = 0, sender stalls (no more flits until credit return)
4. Receiver processes flit → returns credit to sender

### Deadlock avoidance:
- VN0 prevents inter-message-class deadlock (REQ can't block SNP)
- VN1 prevents routing deadlock in multi-hop topologies
- Credit starvation: guaranteed minimum credits per class

---

## 7. LINK POWER MANAGEMENT

| State | Description | Exit Latency |
|-------|-------------|--------------|
| L0 | Full speed, all lanes active | — |
| L0p | Partial width (half lanes off) | ~10ns |
| L1 | Link idle, PLL off | ~1-10μs |

**L0p operation**: During low traffic, link dynamically shuts down half the lanes while keeping the other half active at full speed. Bandwidth halved but power significantly reduced. Transparent to protocol layer.

---

## 8. ON-DIE MESH AND CHA

Starting with Skylake-SP, Intel uses an on-die mesh interconnect:

- **Mesh**: 2D grid connecting cores, LLC slices, memory controllers, UPI agents
- **CHA** (Caching and Home Agent): Combined per-core node handling both local cache requests and acting as home agent for a slice of physical addresses
- **Snoop Filter**: Distributed across CHAs, tracking which remote sockets cache lines

### UPI-Mesh interaction:
1. Core misses in LLC → local CHA determines if address is local or remote
2. If remote: CHA sends request out via UPI agent → UPI link → remote socket
3. Remote socket's mesh delivers request to appropriate CHA (home agent)
4. Home CHA resolves coherence, responds via UPI back to requester

---

## 9. MULTI-SOCKET TOPOLOGIES

### 2-Socket (2S):
- 3-4 UPI links between two CPUs
- Total BW: 3×20.8 = 62.4 GB/s/dir (SKX) or 4×32 = 128 GB/s/dir (SPR)
- Direct connection — no routing needed
- Source snoop mode for lowest latency

### 4-Socket (4S):
- Each CPU has UPI links to all other 3 CPUs
- Requires 3 UPI ports per socket (minimum)
- Full mesh — any socket reaches any other in 1 hop
- Home snoop mode preferred (directory)

### 8-Socket (8S):
- Not all CPUs directly connected — some require 2-hop routing
- Routing tables programmed during boot
- Higher latency for remote hops
- Used in large mission-critical servers (rare in modern deployments)

---

## 10. SUB-NUMA CLUSTERING (SNC)

SNC splits a single socket into multiple NUMA domains:
- **SNC2**: Socket split into 2 NUMA nodes (half the LLC + half the MCs each)
- **SNC4**: Socket split into 4 NUMA nodes

Benefits:
- Reduced LLC lookup latency (closer to local memory controller)
- Better NUMA affinity for VMs
- Similar to AMD's NPS modes

Trade-off:
- Each node sees reduced LLC capacity
- Cross-cluster (same socket) has slight latency penalty

---

## 11. UPI vs QPI COMPARISON

| Feature | QPI | UPI |
|---------|-----|-----|
| Introduction | 2008 (Nehalem) | 2017 (Skylake-SP) |
| Flit size | 80 bits | 192 bits |
| Data rate | 6.4-9.6 GT/s | 10.4-20 GT/s |
| Width | 20 lanes | 20 lanes |
| Encoding | Flit + CRC | Flit + CRC (more efficient) |
| Power mgmt | L0/L0s/L1 | L0/L0p/L1 |
| VN support | VN0/VN1 | VN0/VN1/VNA |
| Max sockets | 8S | 8S |
| On-die | Ring bus | Mesh |

---

## 12. UPI IN SAPPHIRE RAPIDS (4TH GEN)

Key improvements:
- **16 GT/s** data rate (up from 10.4/11.2)
- **4 UPI links** standard in 2S (up from 3)
- **Total BW**: 4 × 32 GB/s = 128 GB/s per direction
- **HBM support**: On-package HBM2e (Xeon Max) accessible via mesh
- **CXL support**: Some UPI pins repurposed for CXL connectivity
- **Accelerator interfacing**: UPI-like coherent links to accelerators

---

## 13. RELIABILITY (RAS)

- **CRC**: Per-flit 16-bit CRC catches transmission errors
- **Retry**: Automatic replay of corrupted flits from retry buffer
- **Link failover**: If one UPI link fails, traffic reroutes to remaining links (degraded BW)
- **Viral error**: Propagating fatal error indication across links
- **Poison**: Data marked as corrupted (consumed = machine check)
- **Link width degradation**: Can operate with fewer lanes if lanes fail

---

## 14. PERFORMANCE CHARACTERISTICS

| Metric | Typical Value (Sapphire Rapids) |
|--------|-------------------------------|
| Link latency (one way) | ~50-60ns (idle) |
| Remote DRAM access | ~130-150ns |
| Local DRAM access | ~80-100ns |
| NUMA ratio | 1.3-1.7× |
| BW per link per direction | 32 GB/s |
| Total 2S BW (4 links) | 128 GB/s/dir |
| Max aggregate BW (bidirectional) | 256 GB/s |
| Flit efficiency | ~80% (header overhead) |

---

END OF DOCUMENT 01 — MASTER THEORY
