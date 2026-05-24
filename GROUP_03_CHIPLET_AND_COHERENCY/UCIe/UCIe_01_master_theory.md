# UCIe (Universal Chiplet Interconnect Express) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS UCIe?

UCIe is an open industry standard for die-to-die (D2D) interconnects, enabling chiplets from different vendors to communicate within a single package. Before UCIe, every company had proprietary D2D links (Intel EMIB/Foveros, AMD IF GMI, TSMC LIPINCON). UCIe provides a universal standard, analogous to what PCIe did for plug-in I/O cards.

**Key motivations**:
- Chiplet-based design is dominant (cost, yield, heterogeneous integration)
- No open standard existed for D2D communication
- Enable chiplet marketplace (mix vendors, IP blocks, process nodes)
- Reduce design cost (reusable chiplet IP)

---

## 2. THREE-LAYER ARCHITECTURE

### Layer 1: Protocol Layer
The upper layer carrying actual data semantics:
- **CXL**: Full CXL protocol (CXL.io + CXL.cache + CXL.mem) for coherent memory access
- **PCIe**: Standard PCIe TLP/DLLP for I/O connectivity
- **Streaming**: Simple packet-based interface for custom protocols (AI accelerators, etc.)
- **Raw**: Direct physical layer access without protocol overhead

### Layer 2: Adapter Layer (Die-to-Die Adapter)
Maps protocol flits to D2D physical layer:
- **Framing**: Packages protocol flits into D2D frames
- **CRC**: Error detection (per frame)
- **Retry**: Replay corrupted frames from buffer
- **Flow control**: Credit-based (prevents buffer overflow)
- **Protocol negotiation**: Discovers capabilities at link-up
- **Multi-protocol**: Can multiplex different protocols on same link

### Layer 3: Physical Layer
Electrical signaling and link training:
- **AFE (Analog Front End)**: Tx/Rx circuits
- **Clocking**: Forwarded clock (separate clock lane)
- **Training**: LFSR patterns, lane alignment, deskew
- **Sideband**: Low-speed management channel
- **Power management**: Active/idle/off states
- **Redundancy**: Spare lanes for repair

---

## 3. INTERFACES (FDI AND RDI)

```
Protocol IP ←──FDI──→ Adapter ←──RDI──→ PHY ←──bumps──→ Remote PHY
```

### FDI (Flit-aware Die-to-Die Interface)
- Between Protocol Layer and Adapter Layer
- Carries protocol flits with metadata (type, credit, etc.)
- Allows any protocol IP to plug into the UCIe adapter
- Standard interface enables IP reuse

### RDI (Raw Die-to-Die Interface)
- Between Adapter Layer and Physical Layer
- Carries raw bytes (adapter frames)
- Standard interface enables PHY IP reuse
- Decouples adapter logic from electrical design

---

## 4. PHYSICAL LAYER DETAILS

### Standard Packaging (≥ 25μm bump pitch)
| Parameter | Value |
|-----------|-------|
| Bump pitch | 25-55 μm |
| Substrate | Organic (standard PCB-like) |
| Reach | ~2-10 mm |
| Data rate | 4-32 GT/s per lane |
| Signaling | Single-ended or differential NRZ/PAM4 |
| Lanes per module | 16 data + sideband + clock |
| Energy | 0.5-1.0 pJ/bit |
| BW density | ~28 GB/s/mm (at 32GT/s) |

### Advanced Packaging (< 25μm bump pitch)
| Parameter | Value |
|-----------|-------|
| Bump pitch | 10-25 μm (or hybrid bonding: <10μm) |
| Substrate | Silicon interposer, EMIB, CoWoS, hybrid bond |
| Reach | ~0.5-2 mm |
| Data rate | 4-16 GT/s per lane |
| Signaling | Single-ended NRZ (short reach → simple) |
| Lanes per module | 64 data + sideband + clock |
| Energy | 0.25-0.5 pJ/bit |
| BW density | ~165 GB/s/mm (at 16GT/s) |

### Module Structure
A "module" is the basic building unit:
- Standard: 16 Tx + 16 Rx data lanes + 1 clock + sideband
- Advanced: 64 Tx + 64 Rx data lanes + 1 clock + sideband
- Multiple modules form a "cluster" for wider bandwidth

---

## 5. BANDWIDTH CALCULATIONS

```
Standard Module (16 lanes × 32 GT/s):
  Raw: 16 × 32 Gbps / 8 = 64 GB/s per direction
  Per module: 64 GB/s Tx + 64 GB/s Rx

Advanced Module (64 lanes × 16 GT/s):
  Raw: 64 × 16 Gbps / 8 = 128 GB/s per direction
  Per module: 128 GB/s Tx + 128 GB/s Rx

Bandwidth density (edge):
  Standard: ~28 GB/s per mm of die edge
  Advanced: ~165 GB/s per mm of die edge (6× better!)
```

---

## 6. SIDEBAND CHANNEL

The sideband is a low-speed, always-on management channel:
- Runs at lower frequency (separate from mainband)
- Used for: parameter negotiation, link training, power management, error reporting
- Critical for interoperability (discovers remote die capabilities)
- Protocol-independent (same sideband regardless of mainband protocol)
- Carries: SBINIT (initialization), MBINIT (mainband training control)

---

## 7. LINK TRAINING AND INITIALIZATION

```
Power On → Sideband Init → Mainband Init → Active (L0)

Sideband Init (SBINIT):
  Phase 1: Detect partner presence
  Phase 2: Exchange capabilities (speed, width, protocol)
  Phase 3: Configure parameters

Mainband Init (MBINIT):
  Phase 1: Tx/Rx calibration
  Phase 2: LFSR pattern training
  Phase 3: Lane-to-lane deskew
  Phase 4: Data rate negotiation
  Phase 5: Adapter layer bring-up (CRC/retry init)
  → L0 (operational)
```

---

## 8. ERROR HANDLING

### CRC Protection
- Adapter layer adds CRC to each frame
- Granularity: per-flit or per-multi-flit (configurable)
- Detects bit errors from electrical noise, cross-talk

### Retry Mechanism
- On CRC error: receiver signals NAK via sideband or mainband
- Sender replays from retry buffer
- Buffer depth determines max outstanding frames before ACK required
- Link-level retry (transparent to protocol layer)

### Lane Repair
- Spare/redundant lanes available in each module
- During training: identify bad lanes
- Remap data to spare lanes (transparent to upper layers)
- Critical for manufacturing yield in advanced packaging

---

## 9. PROTOCOL MAPPING

### CXL over UCIe
- CXL flits (256-byte or variable) mapped to UCIe adapter frames
- CXL.io, CXL.cache, CXL.mem all supported
- Enables CXL memory devices as chiplets (instead of PCB-mounted)
- Latency advantage: D2D ~2-5ns vs CXL over PCIe ~10-20ns

### PCIe over UCIe
- PCIe TLP/DLLP carried in UCIe frames
- Enables PCIe I/O chiplets (NIC, storage controller as chiplet)
- Lower latency than board-level PCIe

### Streaming Protocol
- Simple header + payload format
- No coherence overhead
- Designed for: AI/ML accelerators, custom ASICs, NPUs
- Minimal protocol overhead → maximum efficiency

---

## 10. PACKAGING TECHNOLOGIES

### 2.5D (Side-by-side on interposer)
- Chiplets placed side-by-side on Si interposer or bridge
- Examples: AMD MI300 (IF over interposer), Intel Ponte Vecchio (EMIB)
- UCIe lanes routed through interposer metal layers
- Moderate density, proven manufacturing

### EMIB (Intel Embedded Multi-die Interconnect Bridge)
- Small Si bridge embedded in organic substrate
- Connects only adjacent chiplets (localized)
- Lower cost than full interposer
- Used in Sapphire Rapids HBM, Ponte Vecchio

### CoWoS (TSMC Chip-on-Wafer-on-Substrate)
- Full Si interposer wafer
- High density (supports many chiplets + HBM)
- Used in: NVIDIA H100/H200, AMD MI300

### 3D Stacking (Foveros, Hybrid Bonding)
- Chiplets stacked vertically with TSVs or direct Cu bonds
- Highest density, shortest distance
- UCIe advanced packaging applies here
- Hybrid bonding: <2μm pitch, >1000 GB/s/mm²

---

## 11. UCIe vs PROPRIETARY D2D

| Feature | UCIe | Intel EMIB/Foveros | AMD IF (GMI) | TSMC LIPINCON |
|---------|------|-------------------|-------------|---------------|
| Open standard? | Yes | No | No | No |
| Multi-vendor? | Yes | Intel only | AMD only | TSMC customers |
| Protocol support | CXL/PCIe/Stream | Custom | IF protocol | Custom |
| Bump pitch | 10-55μm | 36-55μm | Varies | 9-25μm |
| BW density | Up to 165 GB/s/mm | ~100 GB/s/mm | ~50 GB/s/mm | ~200 GB/s/mm |
| Interoperability | Spec-guaranteed | N/A | N/A | N/A |
| Availability | 2022+ | 2019+ | 2017+ | 2021+ |

---

## 12. USE CASES AND ECOSYSTEM

### CPU Chiplets
- Compute tiles connected to I/O tile via UCIe
- Example: Heterogeneous CPU (ARM big.LITTLE as chiplets)

### Memory Expansion
- CXL memory chiplet: small die with DDR controller + CXL-over-UCIe
- Attach memory directly to CPU package (lower latency than board)

### AI/ML Accelerators
- NPU/TPU chiplets connected via UCIe streaming mode
- Scalable: add more compute chiplets for more performance

### Disaggregated SoC
- Separate compute, I/O, memory controller as chiplets
- Mix process nodes: compute on N3, I/O on N7 (cost optimization)

### IP Marketplace
- Third-party chiplet IP: buy a CXL controller chiplet, UCIe PHY chiplet
- Plug into your package design → faster time-to-market

---

## 13. PERFORMANCE CHARACTERISTICS

| Metric | Standard Pkg | Advanced Pkg |
|--------|-------------|--------------|
| Latency (D2D) | ~3-5 ns | ~2-3 ns |
| BW per module | 64 GB/s/dir | 128 GB/s/dir |
| Energy efficiency | 0.5-1.0 pJ/bit | 0.25-0.5 pJ/bit |
| BW density | ~28 GB/s/mm | ~165 GB/s/mm |
| Reach | 2-10 mm | 0.5-2 mm |
| Lanes/module | 16 | 64 |
| Lane redundancy | Yes (spare lanes) | Yes |
| Max data rate | 32 GT/s | 16 GT/s |

---

## 14. UCIe 1.1 UPDATES (August 2023)

- **Enhanced retimer support**: For longer reaches in standard packaging
- **Improved streaming protocol**: Better defined header format
- **Flit mode CXL**: Support for CXL 3.0 flit mode directly
- **Additional signaling options**: PAM4 at higher speeds
- **Compliance test refinements**: Better interoperability testing
- **Multi-module management**: Improved coordination across clusters

---

END OF DOCUMENT 01 — MASTER THEORY
