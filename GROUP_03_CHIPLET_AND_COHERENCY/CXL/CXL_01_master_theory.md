# CXL — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS CXL

Compute Express Link is an open industry standard for high-speed CPU-to-device and CPU-to-memory interconnect. It enables:
- **Cache coherency** between CPU and accelerator caches
- **Memory expansion** beyond DIMM slots using CXL-attached memory
- **Memory pooling** where multiple hosts share a memory pool
- **Low latency** (~100-200ns for memory access, vs microseconds for PCIe MMIO)

Built on PCIe physical layer — uses same connectors, cables, PHY, but adds coherent protocols on top.

---

## §2: THREE SUB-PROTOCOLS

CXL defines three sub-protocols running simultaneously over the same physical link:

### CXL.io (PCIe Semantics)
- Standard PCIe transaction layer (TLP-based)
- Used for: device discovery, configuration, interrupts, DMA
- Mandatory for all CXL devices
- Essentially PCIe with CXL negotiation

### CXL.cache (Device → Host Coherence)
- Device caches host memory and maintains coherence with host
- Device issues coherent requests (RdShared, RdOwn, etc.)
- Host snoops device cache when needed
- Uses D2H (Device-to-Host) and H2D (Host-to-Device) channels

### CXL.mem (Host → Device Memory)
- Host CPU accesses device-attached memory (HDM)
- Memory appears in host physical address space
- Uses M2S (Master-to-Subordinate) and S2M (Subordinate-to-Master) channels
- Supports cacheable and non-cacheable access

---

## §3: CXL.cache DETAILED

### Channels:
| Channel | Direction | Purpose |
|---------|-----------|---------|
| D2H Request | Dev→Host | Device requests data (RdShared, RdOwn, etc.) |
| D2H Response | Dev→Host | Device responds to snoops |
| D2H Data | Dev→Host | Device sends dirty data |
| H2D Request | Host→Dev | Host snoops device cache |
| H2D Response | Host→Dev | Host grants coherence (GO-E, GO-S, GO-I) |
| H2D Data | Host→Dev | Host sends data to device |

### Coherence States (MESI at device):
| State | Meaning |
|-------|---------|
| M (Modified) | Device has dirty exclusive copy |
| E (Exclusive) | Device has clean exclusive copy |
| S (Shared) | Device has shared copy |
| I (Invalid) | Not in device cache |

### Key Transactions:
| D2H Request | Purpose | Host Response |
|-------------|---------|---------------|
| RdCurr | Read current (no allocate) | GO-I + Data |
| RdOwn | Read for ownership (M) | GO-E + Data |
| RdShared | Read shared (S or E) | GO-S/GO-E + Data |
| RdAny | Read any state | GO-E/GO-S + Data |
| ItoMWr | Invalid-to-Modified write (64B) | GO-WritePull |
| WrCur | Write current (no alloc) | GO-WritePull |
| CLFlush | Cache line flush | GO-I |

### Snoop from Host to Device:
| H2D Snoop | Purpose | Device Response |
|-----------|---------|-----------------|
| SnpInv | Invalidate device line | RspIHitI/RspIHitSE + data if dirty |
| SnpData | Get data, downgrade to S | RspSHitSE + data |
| SnpCur | Get data, no state change | RspV + data |

---

## §4: CXL.mem DETAILED

### Channels:
| Channel | Direction | Purpose |
|---------|-----------|---------|
| M2S Request | Host→Dev | Memory read/write commands |
| M2S RwD | Host→Dev | Request-with-Data (writes) |
| S2M NDR | Dev→Host | Non-Data Response (completion) |
| S2M DRS | Dev→Host | Data Response (read data) |

### Key Operations:
| M2S Opcode | Purpose |
|------------|---------|
| MemRd | Read from device memory |
| MemWr | Write to device memory |
| MemRdData | Read with data response |
| MemWrPtl | Partial write (byte-enable) |
| MemInv | Invalidate line at device |

### Meta-states (for Type 2 — device caches its own memory):
| Meta | Meaning |
|------|---------|
| Meta0 (I) | Line not cached anywhere |
| Meta1 (A) | Device has line in Any state |
| Meta2 (S) | Host has Shared, device may have too |
| Meta3 (E) | Host has Exclusive |

### Bias Modes (Type 2):
- **Host Bias**: Host is coherence owner — snoops device on conflict
- **Device Bias**: Device is coherence owner — faster device access, host must request
- **Bias can switch dynamically** based on workload phase

---

## §5: FLIT FORMAT

### CXL 1.x/2.0: 68-byte Flit
```
┌─────────────────────────────────────────┐
│ 64 bytes payload (slots) + 2B header + 2B CRC │
└─────────────────────────────────────────┘
- 16 slots × 4 bytes = 64B payload
- Slots carry different channel messages
- Multiplexed: CXL.cache + CXL.mem share flits
```

### CXL 3.0: 256-byte Flit (Latency-Optimized)
```
┌─────────────────────────────────────────┐
│ 236B payload + 16B header/control + 4B CRC │
└─────────────────────────────────────────┘
- More payload per flit = fewer flits needed
- Better amortization of CRC overhead
- Supports 32 GT/s (PCIe 6.0) and 64 GT/s (PCIe 7.0)
```

---

## §6: ARB/MUX LAYER

The ARB/MUX layer sits between protocol layer and PCIe link layer:
```
CXL.io (PCIe TLP)  CXL.cache  CXL.mem
       │                │         │
       ▼                ▼         ▼
    ┌──────────────────────────────────┐
    │         ARB/MUX Layer            │
    │  Arbitrates between sub-protocols │
    │  Packs messages into flits       │
    │  Credit management per channel   │
    └──────────────────┬───────────────┘
                       ▼
              PCIe Link Layer (DLLP/TLP framing)
                       ▼
              PCIe PHY (SERDES, electrical)
```

---

## §7: FLEX BUS PORT

CXL and PCIe share the same physical port (Flex Bus):
- At link training, port negotiates CXL or PCIe mode
- **CXL mode**: Full CXL protocol stack (io + cache + mem)
- **PCIe mode**: Standard PCIe device
- Negotiated via DVSEC capabilities and link training sequences
- Allows same server design to accept either CXL or PCIe devices

---

## §8: CXL SWITCHING (CXL 2.0+)

CXL switches enable multi-device topologies:
```
        Host CPU
           │
      CXL Switch (USP)
       /    |    \
    DSP0  DSP1  DSP2
      │     │     │
   Type3  Type3  Type2
   (Mem)  (Mem)  (GPU)
```
- Single-level switching (CXL 2.0)
- Multi-level switching (CXL 3.0)
- Fabric Manager (FM) configures routes and memory maps

---

## §9: MEMORY POOLING (CXL 2.0/3.0)

### Multi-Logical Device (MLD):
- Single physical device partitioned into multiple Logical Devices (LDs)
- Each LD assigned to different host
- Fabric Manager allocates/deallocates

### CXL 3.0 Shared Memory:
- Multiple hosts access same memory region
- Hardware coherence between hosts (Global Fabric Attached Memory)
- Enables shared-memory programming across servers

---

## §10: CXL SECURITY

### IDE (Integrity and Data Encryption):
- Encrypts CXL flits in transit (AES-GCM-256)
- Protects against physical interposer attacks
- Covers CXL.cache and CXL.mem traffic
- Key management via SPDM/DOE protocols

### Other Security:
- DVSEC-based capability discovery
- Component authentication (SPDM)
- Secure link establishment

---

## §11: LATENCY CHARACTERISTICS

| Access Type | Typical Latency |
|-------------|----------------|
| Local DRAM (same socket) | ~80-100ns |
| CXL Type 3 memory (direct) | ~150-250ns |
| CXL Type 3 (through switch) | ~200-350ns |
| Remote NUMA (2-socket) | ~150-200ns |
| PCIe MMIO read | ~1-2μs |

CXL memory is positioned between local DRAM and remote NUMA — much faster than PCIe MMIO.

---

## §12: CXL vs ALTERNATIVES

| Feature | CXL | CCIX | Gen-Z | OpenCAPI |
|---------|-----|------|-------|----------|
| PHY | PCIe | PCIe | Custom | Custom |
| Coherence | MESI | MOESI | No | Yes |
| Memory semantic | Yes | Yes | Yes | Yes |
| Ecosystem | Won | Dead | Dead | Dead |
| Switching | Yes (2.0+) | Yes | Yes | Limited |
| Spec version | 3.1 | 2.0 | — | — |

CXL has effectively won the cache-coherent interconnect market for CPU-to-device.

---

## §13: CXL 3.0/3.1 ADDITIONS

| Feature | Description |
|---------|-------------|
| Multi-level switching | Fabric with multiple switch hops |
| Shared memory (GFAM) | Hardware-coherent shared memory across hosts |
| 256B flit | Latency-optimized larger flit |
| Enhanced security | Improved IDE, per-LD isolation |
| Back-invalidate improvements | Faster BI for Type 2 devices |
| Peer-to-peer | Direct device-to-device without host |
| Memory sharing semantics | Acquire/Release for shared regions |

---

## §14: CXL IN REAL SYSTEMS

### Intel Sapphire Rapids / Granite Rapids:
- Native CXL 1.1/2.0 support
- CXL Type 3 memory expanders (Samsung, Micron, SK Hynix)

### AMD EPYC (Genoa/Turin):
- CXL 1.1+ support
- Compatible with CXL memory expanders

### Memory Expanders (Type 3):
- Samsung Memory-Semantic SSD
- Micron CZ120 (CXL memory module)
- SK Hynix CMM-D (DRAM expander)

### Accelerators (Type 2):
- Intel Ponte Vecchio (GPU with CXL)
- FPGA accelerators with CXL interface

---

END OF DOCUMENT 01 — MASTER THEORY
