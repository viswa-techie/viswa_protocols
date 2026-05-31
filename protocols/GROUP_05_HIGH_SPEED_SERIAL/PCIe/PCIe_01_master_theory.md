# PCI EXPRESS (PCIe) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 01 of 08
# Scope: Complete theory — Architecture to Automotive Applications
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS PCIe?

PCI Express is a **high-speed, serial, point-to-point, packet-based** interconnect standard that replaced the parallel PCI/PCI-X bus. It provides:

- **Full-duplex communication** (simultaneous TX and RX)
- **Scalable bandwidth** (add lanes: x1, x4, x8, x16)
- **Scalable speed** (Gen1 2.5GT/s → Gen6 64GT/s)
- **Software backward compatibility** with PCI (same config space model)
- **Reliable delivery** (CRC + ACK/NAK + retry)
- **Quality of Service** (traffic classes, virtual channels)
- **Power management** (ASPM, L-states, clock gating)

### PCIe as a Layered Protocol
```
┌─────────────────────────────────────────┐
│  SOFTWARE / DEVICE DRIVER               │  ← OS driver, user-space
├─────────────────────────────────────────┤
│  TRANSACTION LAYER (TL)                 │  ← TLPs: Read, Write, Completion
├─────────────────────────────────────────┤
│  DATA LINK LAYER (DLL)                  │  ← ACK/NAK, CRC, Flow Control
├─────────────────────────────────────────┤
│  PHYSICAL LAYER (PHY)                   │  ← Encoding, Serialization, Electrical
│  ┌───────────────┬───────────────────┐  │
│  │ Logical Sub   │ Electrical Sub    │  │
│  │ (encoding,    │ (driver,receiver, │  │
│  │  scrambling,  │  equalization,    │  │
│  │  framing)     │  PLL)             │  │
│  └───────────────┴───────────────────┘  │
└─────────────────────────────────────────┘
```

---

## 2. WHY PCIe REPLACED PCI

| Issue with PCI/PCI-X | How PCIe Solves It |
|----------------------|-------------------|
| Shared bus (contention) | Point-to-point links (no arbitration) |
| Parallel bus (skew at speed) | Serial lanes (no skew between data bits) |
| Half-duplex | Full-duplex (TX+RX simultaneously) |
| Fixed 33/66 MHz clock | Multi-GHz with per-gen speed scaling |
| Limited to 32/64 devices | Hierarchical topology (thousands of devices) |
| No reliability at link level | CRC + ACK/NAK + replay buffer |
| Clock distributed to all devices | Embedded clock (CDR per lane) |
| 64-bit bus = 64 pins + control | x1 = 4 pins total (2 TX + 2 RX) |
| No flow control | Credit-based flow control |
| Limited power management | ASPM, L-states, clock gating |

---

## 3. TOPOLOGY & COMPONENTS

### PCIe Tree Structure
```
                    ┌─────────┐
                    │   CPU   │
                    │(Root Cmplx)│
                    └────┬────┘
              ┌──────────┼──────────┐
              │          │          │
         ┌────┴────┐ ┌──┴───┐ ┌───┴────┐
         │RP Port 0│ │RP P1 │ │RP Port2│   ← Root Ports
         └────┬────┘ └──┬───┘ └───┬────┘
              │          │         │
         ┌────┴────┐    │    ┌───┴────┐
         │  NVMe   │    │    │ Switch │
         │(Endpoint)│    │    └───┬────┘
         └─────────┘    │   ┌────┼────┐
                        │   │    │    │
                   ┌────┴┐ ┌┴─┐ ┌┴──┐ ┌┴──┐
                   │WiFi │ │EP│ │EP │ │EP │
                   └─────┘ └──┘ └───┘ └───┘
```

### Component Roles

| Component | Role | Example |
|-----------|------|---------|
| Root Complex (RC) | Originates transactions, owns ECAM | SoC PCIe controller |
| Root Port (RP) | Downstream port of RC (one per link) | SA8295P has 2-3 RPs |
| Endpoint (EP) | Target device with BARs | NVMe, WiFi, GPU |
| Switch | Multi-port packet router | PLX/Broadcom PCIe switch |
| Bridge | Protocol conversion | PCIe-to-PCI bridge |

### Addressing: Bus-Device-Function (BDF)
```
Format: BB:DD.F (hex)
  BB = Bus number (0-255)
  DD = Device number (0-31)
  F  = Function number (0-7)

Example: 01:00.0
  Bus 1, Device 0, Function 0 — typical first device behind RP

Root Complex: Bus 0
Root Ports: Bus 0, Device N
Devices behind RP: Bus 1+ (assigned during enumeration)
```

---

## 4. TRANSACTION LAYER — TLP TYPES

### TLP Categories

| Category | Types | Direction | Needs Completion? |
|----------|-------|-----------|-------------------|
| **Posted** | Memory Write, Message | Requester→Completer | No (fire-and-forget) |
| **Non-Posted** | Memory Read, IO Read/Write, Config Read/Write | Req→Comp | Yes (must return Cpl) |
| **Completion** | Completion, Completion w/ Data | Completer→Requester | N/A (is the response) |

### TLP Format (3 or 4 DW Header)
```
┌─────────────────────────────────────────────────────────────┐
│ Byte 0       │ Byte 1       │ Byte 2       │ Byte 3       │
├─────────────────────────────────────────────────────────────┤
│ Fmt[2:0]│Type[4:0]│TC│Attr│TH│TD│EP│Attr│AT│Length[9:0]  │ DW0 (Header)
├─────────────────────────────────────────────────────────────┤
│ Requester ID (16-bit BDF)   │ Tag (8/10-bit)│ Last/1st BE │ DW1
├─────────────────────────────────────────────────────────────┤
│ Address [63:32] or Address [31:2] + reserved               │ DW2 (or DW2+DW3)
├─────────────────────────────────────────────────────────────┤
│                    Data Payload (0-4096 bytes)              │ 0 to MPS
├─────────────────────────────────────────────────────────────┤
│                    ECRC (optional, 32-bit)                  │
└─────────────────────────────────────────────────────────────┘
```

### Key TLP Fields

| Field | Bits | Purpose |
|-------|------|---------|
| Fmt | 3 | Format: 3DW/4DW header, with/without data |
| Type | 5 | Transaction type (MRd, MWr, CfgRd, etc.) |
| TC | 3 | Traffic Class (0-7, for QoS) |
| TD | 1 | TLP Digest present (ECRC) |
| EP | 1 | Data poisoned (error marker) |
| Length | 10 | Payload length in DWORDs (0=1024 DW=4096 bytes) |
| Requester ID | 16 | BDF of originator |
| Tag | 8-10 | Unique transaction identifier |
| 1st/Last DW BE | 4+4 | Byte enables for first/last DWORD |

### TLP Type Encoding

| Type | Fmt | Purpose | Posted? |
|------|-----|---------|---------|
| MRd (Memory Read) | 000/001 + 00000 | Read from device/memory | No |
| MWr (Memory Write) | 010/011 + 00000 | Write to device/memory | Yes |
| IORd (IO Read) | 000 + 00010 | Legacy IO space read | No |
| IOWr (IO Write) | 010 + 00010 | Legacy IO space write | No |
| CfgRd0 (Config Read Type 0) | 000 + 00100 | Config read (local) | No |
| CfgWr0 (Config Write Type 0) | 010 + 00100 | Config write (local) | No |
| CfgRd1 (Config Read Type 1) | 000 + 00101 | Config read (downstream) | No |
| CfgWr1 (Config Write Type 1) | 010 + 00101 | Config write (downstream) | No |
| Cpl (Completion no data) | 000 + 01010 | Response without data | N/A |
| CplD (Completion w/ data) | 010 + 01010 | Response with data | N/A |
| Msg (Message) | 001 + 1xxxx | Sideband message | Yes |

---

## 5. MEMORY, IO, AND CONFIGURATION ADDRESS SPACES

### Three Address Spaces

| Space | Size | Access Method | Use |
|-------|------|---------------|-----|
| Memory | 32-bit or 64-bit | Memory-mapped (BAR) | Device registers, DMA buffers |
| IO | 16-bit (64KB) | IO instructions (legacy) | Legacy devices (rare today) |
| Configuration | 4KB per function | ECAM (memory-mapped) | Device discovery, setup |

### Base Address Registers (BARs)

BARs are in Configuration Space (offset 0x10-0x27 for Type 0 headers):
```
Each function can have up to 6 BARs (32-bit) or 3 BARs (64-bit)

BAR format (Memory):
┌───────────────────────────────────────────────────┐
│ Base Address [31:N] │ Reserved │ Prefetch │ Type │ 0 │
└───────────────────────────────────────────────────┘
  Bit 0 = 0: Memory space
  Bit 2:1 = Type: 00=32-bit, 10=64-bit (uses next BAR too)
  Bit 3 = Prefetchable: 1=no side effects (cacheable)

BAR format (IO):
┌───────────────────────────────────────────────────┐
│ Base Address [31:2] │ Reserved │ 1 │
└───────────────────────────────────────────────────┘
  Bit 0 = 1: IO space

BAR sizing (discovery):
  1. Write all 1s to BAR
  2. Read back — low bits stuck at 0 indicate size
  3. Size = ~(readback & mask) + 1
  Example: Readback 0xFFF00000 → size = 1MB
```

### Configuration Space Layout

```
Type 0 Header (Endpoint) — 64 bytes:
Offset  Field
0x00    Vendor ID (16) | Device ID (16)
0x04    Command (16) | Status (16)
0x08    Revision ID (8) | Class Code (24)
0x0C    Cache Line (8) | Latency Timer (8) | Header Type (8) | BIST (8)
0x10    BAR0
0x14    BAR1
0x18    BAR2
0x1C    BAR3
0x20    BAR4
0x24    BAR5
0x28    Cardbus CIS Pointer
0x2C    Subsystem Vendor ID (16) | Subsystem ID (16)
0x30    Expansion ROM Base Address
0x34    Capabilities Pointer (8)
0x3C    Interrupt Line (8) | Interrupt Pin (8) | Min_Gnt (8) | Max_Lat (8)

Extended Configuration Space: 0x100 - 0xFFF (PCIe-specific capabilities)
```

---

## 6. FLOW CONTROL — CREDIT-BASED MECHANISM

### Why Flow Control?
- Receiver has **limited buffer space** (for incoming TLPs)
- Without FC: Receiver overflow → data loss → retransmission → reduced throughput
- PCIe solution: **Sender can only send if it has credits**

### Credit Types (6 total)

| Category | Header Credits (HdrFC) | Data Credits (DataFC) |
|----------|----------------------|---------------------|
| Posted (P) | PH | PD |
| Non-Posted (NP) | NPH | NPD |
| Completion (Cpl) | CplH | CplD |

### How Credits Work
```
Initial credit advertisement:
  Receiver → Sender: "I have space for X headers + Y data units"
  (sent via InitFC1, InitFC2 DLLPs during link init)

During operation:
  Sender: Deducts credits when sending TLP
  Receiver: Processes TLP, frees buffer, sends UpdateFC DLLP
  Sender: Adds returned credits to available pool

Credit units:
  Header: 1 credit = 1 TLP header (any size: 3DW or 4DW)
  Data: 1 credit = 4 DWORDs = 16 bytes

Infinite credits:
  If receiver advertises max credits → no flow control limiting
  (receiver guarantees it can always accept)
```

### Flow Control Impact on Performance
```
If credits run out: Sender STALLS (no TLPs sent until credits return)
  This is the #1 cause of PCIe performance below theoretical max

Optimization:
  - Increase receiver buffers (more credits)
  - Reduce completion latency (faster credit return)
  - Pipeline: Use multiple outstanding tags
  - Match MPS to avoid wasted credit per TLP
```

---

## 7. DATA LINK LAYER — RELIABILITY

### ACK/NAK Protocol
```
Sender:                              Receiver:
  1. Assigns sequence number (12-bit)
  2. Calculates LCRC (32-bit)
  3. Stores TLP in Replay Buffer
  4. Transmits: [SeqNum|TLP|LCRC]
                    ─────────────────────→
                                         5. Checks LCRC
                                         6a. Good → ACK DLLP ←──────
                                         6b. Bad  → NAK DLLP ←──────
  7a. ACK: Remove from Replay Buffer
  7b. NAK: Retransmit from Replay Buffer
```

### DLLP Types

| DLLP | Purpose | When Sent |
|------|---------|-----------|
| ACK | Acknowledge received TLPs | After successful TLP receipt |
| NAK | Request retransmission | On LCRC/sequence error |
| InitFC1 | Initial flow control (first) | During link training |
| InitFC2 | Initial flow control (second) | During link training |
| UpdateFC | Update available credits | When buffer space freed |
| PM_Enter_L1 | Request L1 power state | Power management |
| PM_Request_Ack | Acknowledge power request | Power management |
| Vendor-Specific | Custom DLLP | Implementation-specific |

### Sequence Number & Replay
```
- 12-bit sequence number (0-4095, wraps)
- Replay buffer holds all un-ACKed TLPs
- On NAK: Replay ALL TLPs from NAK'd sequence onwards
- REPLAY_TIMER: If no ACK received within timeout → auto-replay
- REPLAY_NUM: Max replay attempts before link error (3 or 4)
```

---

## 8. PHYSICAL LAYER — ENCODING & SIGNALING

### Gen1-2: 8b/10b Encoding
```
- Each 8-bit data byte → 10-bit symbol (25% overhead)
- Running Disparity (RD): Maintains DC balance
- Special characters: K28.5 (comma), K27.7, K29.7, K30.7
- Overhead: 20% (8 useful bits per 10 transmitted)
- Bandwidth: 2.5 GT/s × 8/10 = 2.0 Gbps = 250 MB/s per lane (Gen1)
```

### Gen3-5: 128b/130b Encoding
```
- 128-bit block + 2-bit sync header = 130 bits
- Sync header: 01 = Data block, 10 = Ordered Set block
- Scrambler: LFSR-based (no special characters needed)
- Overhead: 1.54% (128 useful bits per 130 transmitted)
- Bandwidth: 8 GT/s × 128/130 = 7.877 Gbps ≈ 985 MB/s per lane (Gen3)

Block format:
┌────┬─────────────────────────────────────────┐
│ 01 │ 128 bits of scrambled data              │  Data Block
├────┼─────────────────────────────────────────┤
│ 10 │ 128 bits of ordered set                 │  OS Block
└────┴─────────────────────────────────────────┘
```

### Gen6: PAM4 + FLIT
```
- PAM4: 4 voltage levels per symbol (2 bits/symbol)
- Raw rate: 32 GBaud × 2 bits = 64 GT/s
- FEC: 1-bit Galois Field based (adds latency, removes errors)
- FLIT (Flow Control Unit): Fixed 256-byte packet
  - Header + payload + CRC all within 256 bytes
  - Simplifies error recovery (whole FLIT retransmit)
- Effective bandwidth: ~7.56 GB/s per lane
```

### Signal Characteristics

| Parameter | Gen1 | Gen3 | Gen5 | Gen6 |
|-----------|------|------|------|------|
| Data rate | 2.5 GT/s | 8 GT/s | 32 GT/s | 64 GT/s |
| Signaling | NRZ | NRZ | NRZ | PAM4 |
| Vdiff (TX) | 800-1200 mV | 800-1200 mV | 800-1200 mV | ~200 mV/level |
| Ref Clock | 100 MHz | 100 MHz | 100 MHz | 100 MHz |
| Channel loss budget | 5 dB | 14 dB | 28 dB | 36 dB |
| Equalization | None | 3-tap TX EQ + RX | CTLE+DFE | CTLE+DFE+FEC |

---

## 9. LINK TRAINING & STATUS STATE MACHINE (LTSSM)

### LTSSM States Overview
```
                    RESET
                      │
                      ▼
                  ┌────────┐
                  │ Detect │ ← Receiver detection
                  └───┬────┘
                      │ Receiver found
                      ▼
                  ┌────────┐
                  │Polling │ ← TS1/TS2 exchange, bit lock, symbol lock
                  └───┬────┘
                      │ TS2 received
                      ▼
                  ┌──────────────┐
                  │Configuration │ ← Lane numbering, width negotiation
                  └───┬──────────┘
                      │ Link number + lane agreed
                      ▼
                  ┌────────┐
            ┌────→│   L0   │←──────────────────┐  Active State
            │     └───┬────┘                    │
            │         │                         │
            │    ┌────┼────────┐                │
            │    │    │        │                │
            │    ▼    ▼        ▼                │
            │ ┌────┐┌────┐ ┌────────┐          │
            │ │L0s ││ L1 │ │Recovery│──────────┘
            │ └────┘└────┘ └────────┘
            │                  ↑
            │                  │ Speed change / Error
            └──────────────────┘
```

### Key LTSSM States

| State | Purpose | Duration |
|-------|---------|----------|
| **Detect** | Check if device present (impedance sense) | 12 ms timeout |
| **Polling** | Achieve bit/symbol lock, exchange TS1→TS2 | ~1-2 ms |
| **Configuration** | Agree on link width, lane numbers | ~1 ms |
| **L0** | Normal operation (active) | Indefinite |
| **Recovery** | Re-train (speed change, error, EQ) | ~1-2 ms |
| **L0s** | Low-latency standby (fast exit) | Exit: <1 µs |
| **L1** | Deep idle (both directions idle) | Exit: 2-4 µs |
| **L2** | Near power-off (only Vaux) | Exit: >100 ms |
| **Disabled** | Link intentionally disabled | Until reset |
| **Loopback** | Test mode (loop TX→RX) | Debug only |

### Link Training Sequence
```
1. DETECT:
   - TX driver sends common-mode voltage pulse
   - RX impedance detected (~50Ω) → device present

2. POLLING:
   - Both sides send TS1 ordered sets
   - Achieve: Bit lock (CDR), Symbol/Block lock
   - When TS2 received + TS2 sent → move to Configuration
   
3. CONFIGURATION:
   - Negotiate: Link number, Lane numbers, Width
   - Upstream port proposes, downstream confirms
   - Both agree on final link width (e.g., x4)
   
4. L0 (Active):
   - Normal TLP/DLLP traffic
   - Speed starts at Gen1 (2.5 GT/s)
   - If both support higher → initiate speed change via Recovery

5. RECOVERY (Speed Change):
   - Enter Recovery.RcvrLock → Recovery.Speed
   - Change to target speed (Gen2/3/4/5)
   - For Gen3+: Equalization phases (Phase 0→1→2→3)
   - Return to L0 at new speed
```

### Gen3+ Equalization
```
Problem: At 8+ GT/s, channel loss is significant → ISI (inter-symbol interference)
Solution: TX pre-emphasis + RX equalization

Equalization Phases (in Recovery.Equalization):
  Phase 0: RC sends preset values to EP
  Phase 1: EP adjusts RX, reports back
  Phase 2: EP requests TX coefficient changes
  Phase 3: RC adjusts, both verify → back to L0

TX Equalization: 3-tap FIR filter
  Pre-cursor (C-1): Compensates post-cursor ISI
  Main cursor (C0): Main signal amplitude
  Post-cursor (C+1): Compensates pre-cursor ISI
  
  11 presets defined (P0-P10): Pre-defined C-1, C0, C+1 values

RX Equalization:
  CTLE: Continuous Time Linear Equalizer (high-pass boost)
  DFE: Decision Feedback Equalizer (remove ISI from known bits)
```

---

## 10. POWER MANAGEMENT — ASPM & L-STATES

### Power State Hierarchy
```
L0:   Active — Full bandwidth, full power
L0s:  Standby — Fast entry/exit (~1µs), TX idle
L1:   Low Power — Both directions idle, PLL may be off
L1.1: L1 substate — PLL off, common clock off (deeper than L1)
L1.2: L1 substate — PHY off, only ref clock buffer active (deepest idle)
L2:   Near Off — Only aux power, wake via WAKE# or PERST#
L3:   Off — No power, device lost (full re-enumeration needed)
```

### ASPM (Active State Power Management)
```
ASPM enables AUTOMATIC transition to low-power states:
  - L0s: Entered independently per direction (TX or RX)
  - L1: Entered when BOTH directions idle

Configuration:
  Link Capabilities Register [11:10] = ASPM Support
    00 = Not supported
    01 = L0s supported
    10 = L1 supported (also implies L0s)
    11 = L0s and L1 supported

  Link Control Register [1:0] = ASPM Control
    00 = Disabled
    01 = L0s enabled
    10 = L1 enabled
    11 = L0s and L1 enabled

Linux control:
  /sys/bus/pci/devices/XXXX:XX:XX.X/link/l0s_aspm
  /sys/bus/pci/devices/XXXX:XX:XX.X/link/l1_aspm
  Kernel parameter: pcie_aspm=force|off|default
```

### L1 Substates (PCIe 1.1+ feature, common in mobile/automotive)
```
L1.1 (L1 with PLL off):
  - Clock recovery not needed (common clock architecture)
  - Exit latency: ~4-32 µs
  - Power: ~10% of L0

L1.2 (L1 with PHY off):
  - Only reference clock buffer powered
  - Exit latency: ~32-64 µs
  - Power: ~1-5% of L0
  - Most power savings, longer wake time

CLKREQ# pin required:
  - Device asserts CLKREQ# LOW when it needs clock
  - Host can turn off reference clock when CLKREQ# HIGH
  - Essential for L1.2 entry
```

### Automotive Power Considerations
```
Automotive always-on requirements:
  - Some PCIe devices (WiFi) may need fast wake for incoming connections
  - NVMe needs to flush data before power loss → register for shutdown
  - ASPM L1.2 saves significant power during idle (parked car)
  - PERST# must be controlled carefully during sleep/wake cycles
  
Typical automotive power sequence:
  IGNITION ON:
    1. Main power rail → ON
    2. PERST# released → device powers up
    3. LTSSM: Detect → Polling → Config → L0
    4. Enumeration: ~50-200ms
    5. Driver probe: device-specific
    
  IGNITION OFF (graceful):
    1. OS suspends device (D3hot)
    2. Link enters L2 or L3
    3. PERST# asserted
    4. Power rail → OFF
```

---

## 11. INTERRUPTS — MSI AND MSI-X

### Legacy INTx vs MSI vs MSI-X

| Feature | INTx (Legacy) | MSI | MSI-X |
|---------|--------------|-----|-------|
| Mechanism | Dedicated wire | Memory write TLP | Memory write TLP |
| Sharing | Shared (multiple devices) | Dedicated per function | Dedicated per vector |
| Max vectors | 1 per pin (4 pins/bus) | 1, 2, 4, 8, 16, or 32 | Up to 2048 |
| Steering | IRQ controller | Address targets CPU | Per-vector CPU targeting |
| Performance | Slow (level-triggered) | Better (edge) | Best (per-queue) |
| Modern use | Legacy only | Common (simple devices) | Preferred (NVMe, NICs) |

### MSI Mechanism
```
Device writes a specific value to a specific memory address:
  - Address: Configured by host in MSI Capability (Cap 0x05)
  - Data: 16-bit value (identifies which interrupt)
  
The write goes over PCIe as a normal Memory Write TLP!
  → Received by CPU's interrupt controller (GIC/APIC)
  → Translated to IRQ number → handler invoked

MSI Capability Structure:
  Offset+0: Capability ID (0x05) | Next Cap Ptr
  Offset+4: Message Control (enable, # vectors, 64-bit capable)
  Offset+8: Message Address (lower 32-bit)
  Offset+C: Message Upper Address (if 64-bit)
  Offset+10: Message Data (interrupt vector value)
```

### MSI-X Mechanism
```
More flexible than MSI:
  - Vectors stored in BAR-mapped table (not config space)
  - Each vector has independent address + data + mask
  - Ideal for multi-queue devices (NVMe: 1 vector per IO queue)

MSI-X Table (in BAR memory):
  Entry N:
    [0x00] Message Address Lower (32-bit)
    [0x04] Message Address Upper (32-bit)
    [0x08] Message Data (32-bit)
    [0x0C] Vector Control (bit 0 = mask)

MSI-X PBA (Pending Bit Array):
  Indicates which masked interrupts are pending
```

---

## 12. DMA AND IOMMU/SMMU

### PCIe DMA Architecture
```
Device (Endpoint) can be Bus Master:
  - Device initiates Memory Read/Write TLPs
  - Accesses system memory directly (DMA)
  - CPU not involved in data transfer (high efficiency)

DMA flow:
  1. Driver allocates DMA buffer (dma_alloc_coherent / dma_map_single)
  2. Driver writes buffer physical address to device register
  3. Device reads/writes system memory via PCIe MWr/MRd TLPs
  4. Device signals completion via MSI-X interrupt
  5. Driver processes data in buffer
```

### IOMMU / SMMU (ARM)
```
Problem: PCIe device DMA uses physical addresses
  → Security risk: Malicious device can read/write any memory
  → Virtualization: VM sees different physical addresses

Solution: IOMMU (Intel VT-d) / SMMU (ARM)
  Device DMA address → IOMMU → Translated physical address

Benefits:
  1. Isolation: Device can only access mapped pages
  2. Virtualization: Device sees Guest Physical Address
  3. Scatter-gather: Contiguous IOVA → scattered physical pages
  4. Security: Prevents DMA attacks

ARM SMMU (System Memory Management Unit):
  - SMMU v2/v3 on Qualcomm SA8295P
  - StreamID = Requester ID (BDF) identifies device
  - Context Bank = Translation table set per device/VM
  
Linux API:
  dma_alloc_coherent() → allocates + maps via SMMU
  iommu_domain_alloc() → create isolation domain
  iommu_attach_device() → bind device to domain
```

---

## 13. PCIe CONFIGURATION & ENUMERATION

### Enumeration Process (Linux Boot)
```
1. BIOS/UEFI/Firmware discovers Root Complex
2. Root Complex has Bus 0 pre-assigned
3. For each Root Port on Bus 0:
   a. Assign secondary bus number
   b. Send CfgRd to Bus:0.0 (check Vendor/Device ID)
   c. If device present → read Header Type
   d. If Bridge (Header Type 1) → recurse with new bus number
   e. If Endpoint (Header Type 0) → size BARs, assign resources
4. Assign: Bus numbers, Memory windows, IO windows
5. Program: BARs, bridge base/limit registers
6. Enable: Bus Master, Memory Space, IO Space (Command Register)

In Linux:
  pci_scan_bus() → pci_scan_slot() → pci_scan_single_device()
  pci_assign_unassigned_resources()
  pci_enable_device() → pci_set_master()
```

### ECAM (Enhanced Configuration Access Mechanism)
```
Memory-mapped access to configuration space:
  Address = ECAM_BASE + (Bus << 20) + (Device << 15) + (Function << 12) + Register

Each function: 4KB mapped (full extended config space)
Total for 256 buses: 256 × 32 × 8 × 4KB = 256 MB

Device Tree (ARM):
  reg = <ECAM_BASE  ECAM_SIZE>;  /* e.g., 0x60000000 0x10000000 */
  ranges = <0x01000000 0x0 IO_BASE   0x0 IO_BASE   0x0 IO_SIZE>,
           <0x02000000 0x0 MEM_BASE  0x0 MEM_BASE  0x0 MEM_SIZE>;
```

---

## 14. LINUX PCIe DRIVER MODEL

### Driver Structure
```c
#include <linux/pci.h>
#include <linux/module.h>

static const struct pci_device_id my_pci_ids[] = {
    { PCI_DEVICE(0x1234, 0x5678) },  /* Vendor ID, Device ID */
    { 0, }
};
MODULE_DEVICE_TABLE(pci, my_pci_ids);

static int my_pci_probe(struct pci_dev *pdev, const struct pci_device_id *id)
{
    int ret;
    void __iomem *bar0;
    
    /* 1. Enable device */
    ret = pci_enable_device(pdev);
    
    /* 2. Request memory regions (BARs) */
    ret = pci_request_regions(pdev, "my_driver");
    
    /* 3. Map BAR0 to kernel virtual address */
    bar0 = pci_iomap(pdev, 0, pci_resource_len(pdev, 0));
    
    /* 4. Enable bus mastering (for DMA) */
    pci_set_master(pdev);
    
    /* 5. Set DMA mask */
    ret = dma_set_mask_and_coherent(&pdev->dev, DMA_BIT_MASK(64));
    
    /* 6. Allocate MSI-X vectors */
    ret = pci_alloc_irq_vectors(pdev, 1, 16, PCI_IRQ_MSIX | PCI_IRQ_MSI);
    
    /* 7. Register interrupt handler */
    ret = request_irq(pci_irq_vector(pdev, 0), my_handler, 0, "my_dev", priv);
    
    return 0;
}

static void my_pci_remove(struct pci_dev *pdev)
{
    free_irq(pci_irq_vector(pdev, 0), priv);
    pci_free_irq_vectors(pdev);
    pci_iounmap(pdev, bar0);
    pci_release_regions(pdev);
    pci_disable_device(pdev);
}

static struct pci_driver my_pci_driver = {
    .name     = "my_pci_driver",
    .id_table = my_pci_ids,
    .probe    = my_pci_probe,
    .remove   = my_pci_remove,
};
module_pci_driver(my_pci_driver);
```

### Key Linux PCI APIs

| API | Purpose |
|-----|---------|
| `pci_enable_device()` | Enable device, power on, enable BARs |
| `pci_request_regions()` | Claim BAR memory regions |
| `pci_iomap()` | Map BAR to kernel virtual address |
| `pci_set_master()` | Enable bus mastering (DMA) |
| `pci_alloc_irq_vectors()` | Allocate MSI/MSI-X vectors |
| `pci_irq_vector()` | Get Linux IRQ number for vector N |
| `dma_alloc_coherent()` | Allocate DMA-coherent buffer |
| `dma_map_single()` | Map existing buffer for DMA |
| `pci_read_config_dword()` | Read config space register |
| `pci_write_config_dword()` | Write config space register |
| `pci_find_capability()` | Find capability in config space |
| `pci_enable_pcie_error_reporting()` | Enable AER |

---

## 15. NVMe OVER PCIe — COMPLETE FLOW

### Why NVMe Matters
```
NVMe (Non-Volatile Memory Express):
  - Protocol designed specifically for flash storage over PCIe
  - Replaces AHCI/SATA: Purpose-built for parallelism
  - 64K I/O queues × 64K commands each (vs AHCI: 1 queue × 32)
  - Direct memory-mapped submission/completion queues
  - Typical automotive: UFS being replaced by NVMe for performance
```

### NVMe Architecture over PCIe
```
┌─────────────────────────────────────┐
│         Application / VFS            │
├─────────────────────────────────────┤
│      NVMe Block Layer Driver         │
│    (drivers/nvme/host/pci.c)         │
├─────────────────────────────────────┤
│        PCIe Transaction Layer        │
│    (Memory Read/Write TLPs)          │
├─────────────────────────────────────┤
│     PCIe Data Link + Physical        │
├─────────────────────────────────────┤
│      NVMe Controller (SSD)           │
│    BAR0: NVMe Registers (64KB)       │
│    Submission Queues (in host DRAM)   │
│    Completion Queues (in host DRAM)   │
└─────────────────────────────────────┘

I/O Flow:
  1. Driver writes command to Submission Queue (SQ) in host memory
  2. Driver rings doorbell: MWr TLP to NVMe controller BAR0
  3. Controller fetches command: MRd TLP from host memory
  4. Controller performs flash operation
  5. Controller writes completion to CQ: MWr TLP to host memory
  6. Controller sends MSI-X interrupt
  7. Driver reads CQ entry, processes result
```

### NVMe Performance on SA8295P
```
Typical automotive NVMe (e.g., Samsung PM991a, Kioxia BG5):
  Sequential Read: 3,000-3,500 MB/s
  Sequential Write: 2,000-2,500 MB/s
  Random 4K Read: 400K-500K IOPS
  Random 4K Write: 300K-400K IOPS
  
PCIe Gen3 x4 limit: 3,940 MB/s (sufficient for current NVMe)
PCIe Gen4 x4 limit: 7,880 MB/s (future headroom)
```

---

## 16. PCIe CAPABILITIES MECHANISM

### Capability Linked List
```
Starting at offset 0x34 (Capabilities Pointer) in config header:
  → Points to first capability
  → Each capability has: ID (1B) | Next Ptr (1B) | Capability Data

Standard Capabilities (in first 256 bytes):
  ID 0x01: PM (Power Management)
  ID 0x05: MSI
  ID 0x10: PCIe Capability (MUST have for PCIe devices)
  ID 0x11: MSI-X
  
Extended Capabilities (offset 0x100+):
  ID 0x0001: AER (Advanced Error Reporting)
  ID 0x0002: VC (Virtual Channel)
  ID 0x000D: ACS (Access Control Services)
  ID 0x000E: ARI (Alternative Routing-ID Interpretation)
  ID 0x0010: SR-IOV (Single Root I/O Virtualization)
  ID 0x001E: L1 PM Substates
  ID 0x0025: Data Link Feature
  ID 0x0026: Physical Layer 16.0 GT/s
  ID 0x0027: Physical Layer 32.0 GT/s
```

### PCIe Capability Structure (ID 0x10)
```
Offset within capability:
  +0x00: Cap ID (0x10) | Next
  +0x02: PCIe Capabilities Register
         - Device/Port Type [7:4]: RC, EP, Switch UP/DP
         - Slot Implemented [8]
         - Interrupt Message Number [13:9]
  +0x04: Device Capabilities
         - Max Payload Size Supported [2:0]
         - Phantom Functions [4:3]
         - Extended Tag Supported [5]
         - Endpoint L0s/L1 Latency [8:6], [11:9]
  +0x08: Device Control
         - Max Payload Size [7:5] (programmed)
         - Max Read Request Size [14:12]
         - Extended Tag Enable [8]
  +0x0A: Device Status
         - Correctable Error [0], Non-Fatal [1], Fatal [2], URQ [3]
  +0x0C: Link Capabilities
         - Max Link Speed [3:0] (Gen1=1, Gen2=2, Gen3=3, ...)
         - Max Link Width [9:4] (x1=1, x4=4, x8=8, ...)
         - ASPM Support [11:10]
         - L0s Exit Latency [14:12]
         - L1 Exit Latency [17:15]
  +0x10: Link Control
         - ASPM Control [1:0]
         - Read Completion Boundary [3]
         - Link Disable [4]
         - Retrain Link [5]
         - Common Clock Configuration [6]
  +0x12: Link Status
         - Current Link Speed [3:0]
         - Negotiated Link Width [9:4]
         - Link Training [11]
         - Slot Clock Configuration [12]
```

---

## 17. ADVANCED ERROR REPORTING (AER)

### Error Classification

| Category | Severity | Examples | Default Action |
|----------|----------|----------|----------------|
| Correctable | Minor | Bad TLP (corrected), Replay timeout, REPLAY_NUM rollover | Log only |
| Non-Fatal Uncorrectable | Medium | Completion timeout, Unexpected Completion, ECRC error | Report + continue |
| Fatal Uncorrectable | Severe | LCRC error (link failure), Malformed TLP, Receiver overflow | Link reset |

### AER Registers (Extended Capability 0x0001)
```
+0x00: Extended Cap Header (ID=0x0001)
+0x04: Uncorrectable Error Status (32-bit, each bit = one error type)
+0x08: Uncorrectable Error Mask
+0x0C: Uncorrectable Error Severity (0=Non-Fatal, 1=Fatal)
+0x10: Correctable Error Status
+0x14: Correctable Error Mask
+0x18: Advanced Error Capabilities & Control
+0x1C: Header Log (4 DWs of TLP header that caused error)
+0x2C: Root Error Command (RC only)
+0x30: Root Error Status (RC only)
+0x34: Error Source Identification (RC only)
```

### Common PCIe Errors in Automotive
```
1. Completion Timeout (most common):
   - Device didn't respond within timeout
   - Cause: Device hung, ASPM latency, DMA error
   - Debug: Check device power state, increase timeout

2. Bad TLP:
   - LCRC mismatch on received TLP
   - Cause: Signal integrity issue, connector problem
   - Debug: Check link status, eye diagram, retrain

3. Receiver Overflow:
   - Incoming data exceeds buffer
   - Cause: Flow control misconfiguration
   - Debug: Check FC credits, receiver buffer size

4. Surprise Down:
   - Link went down unexpectedly
   - Cause: Power glitch, connector disconnect, device failure
   - Debug: Check power rails, PERST#, physical connection

5. ECRC Error:
   - End-to-end CRC mismatch (through switches)
   - Cause: Bit flip in switch fabric
   - Debug: Replace switch, check power integrity
```

---

## 18. SR-IOV — VIRTUALIZATION

### Single Root I/O Virtualization
```
Purpose: Share one physical device among multiple VMs/containers

Architecture:
  Physical Function (PF): Full-featured function (config, management)
  Virtual Function (VF): Lightweight function (data path only)

┌─────────────────────────────────────┐
│          Hypervisor / Host           │
├──────────┬──────────┬───────────────┤
│   VM 1   │   VM 2   │     VM 3     │
│   VF 0   │   VF 1   │     VF 2     │
└──────┬───┴──────┬───┴───────┬───────┘
       │          │           │
┌──────┴──────────┴───────────┴───────┐
│  Physical Function (PF)              │
│  PCIe Endpoint (e.g., 10GbE NIC)     │
└─────────────────────────────────────┘

SR-IOV in automotive:
  - Multiple VMs for different domains (cluster, IVI, ADAS)
  - Each VM gets its own VF → independent NIC queue
  - QNX (cluster) + Android (IVI) sharing one Ethernet NIC
```

---

## 19. PCIe DEVICE TREE CONFIGURATION (ARM/Qualcomm)

### Typical DT for Qualcomm PCIe RC
```dts
pcie0: pcie@1c00000 {
    compatible = "qcom,pcie-sa8295p";
    reg = <0x0 0x01c00000 0x0 0x3000>,      /* PARF registers */
          <0x0 0x01c06000 0x0 0x2000>,      /* DBI registers */
          <0x0 0x60000000 0x0 0xf1d>,       /* ECAM config space */
          <0x0 0x01c03000 0x0 0x1000>;      /* ELBI registers */
    reg-names = "parf", "dbi", "config", "elbi";
    
    #address-cells = <3>;
    #size-cells = <2>;
    ranges = <0x01000000 0x0 0x60200000 0x0 0x60200000 0x0 0x100000>,   /* IO */
             <0x02000000 0x0 0x60300000 0x0 0x60300000 0x0 0xd00000>;   /* MEM */
    
    interrupts = <GIC_SPI 141 IRQ_TYPE_LEVEL_HIGH>;
    interrupt-names = "msi";
    #interrupt-cells = <1>;
    interrupt-map-mask = <0 0 0 0x7>;
    interrupt-map = <0 0 0 1 &intc 0 0 0 149 4>,   /* INTA */
                    <0 0 0 2 &intc 0 0 0 150 4>;   /* INTB */
    
    /* Clocks */
    clocks = <&gcc GCC_PCIE_0_AUX_CLK>,
             <&gcc GCC_PCIE_0_CFG_AHB_CLK>,
             <&gcc GCC_PCIE_0_MSTR_AXI_CLK>,
             <&gcc GCC_PCIE_0_SLV_AXI_CLK>,
             <&gcc GCC_PCIE_0_PIPE_CLK>;
    clock-names = "aux", "cfg", "bus_master", "bus_slave", "pipe";
    
    /* Resets */
    resets = <&gcc GCC_PCIE_0_BCR>;
    reset-names = "pci";
    
    /* Power */
    vdda-supply = <&vreg_l5a_0p88>;    /* PHY analog */
    vddpe-3v3-supply = <&vreg_l17a>;   /* 3.3V for slot */
    
    /* PCIe specific */
    perst-gpios = <&tlmm 35 GPIO_ACTIVE_LOW>;
    wake-gpios = <&tlmm 37 GPIO_ACTIVE_LOW>;
    
    /* Link configuration */
    num-lanes = <4>;
    max-link-speed = <3>;   /* Gen3 */
    
    /* Power management */
    aspm-no-l0s;            /* Disable L0s (latency sensitive) */
    
    status = "ok";
};
```

---

## 20. PCIe FOR Wi-Fi IN AUTOMOTIVE (QCA6696 / WCN685x)

### Architecture
```
┌─────────────────┐    PCIe Gen3 x1    ┌────────────────┐
│   SA8295P SoC   │◄──────────────────►│  QCA6696       │
│                 │    (985 MB/s)       │  Wi-Fi 6E      │
│   PCIe RC       │                     │  PCIe EP       │
│   + SMMU        │                     │  + WLAN FW     │
└─────────────────┘                     └────────────────┘

Data path (RX):
  1. Wi-Fi radio receives 802.11 frame
  2. WLAN FW processes, strips 802.11 header
  3. DMA: FW writes Ethernet frame to host memory (PCIe MWr)
  4. MSI-X interrupt to host
  5. WLAN driver (ath11k/ath12k) picks up from DMA ring
  6. Frame delivered to Linux networking stack

Key PCIe features used:
  - Bus Master DMA (bulk data transfer)
  - MSI-X (per-ring interrupts: 16+ vectors)
  - ASPM L1.2 (power save when idle)
  - AER (detect link issues in harsh automotive environment)
```

---

## 21. PERFORMANCE OPTIMIZATION

### Key Parameters

| Parameter | Register | Impact | Optimal Value |
|-----------|----------|--------|---------------|
| MPS (Max Payload Size) | Dev Control [7:5] | Larger = fewer TLPs for same data | 256 or 512 (match hierarchy) |
| MRRS (Max Read Req Size) | Dev Control [14:12] | Larger = more outstanding reads | 512 or 4096 |
| Read Completion Boundary (RCB) | Link Control [3] | 64 vs 128 byte split | 128 (match cache line) |
| Extended Tag | Dev Control [8] | 8→10 bit tags (4× outstanding) | Enable if both support |
| Relaxed Ordering | TLP Attr bit | Allow reorder for throughput | Enable for streaming |
| No Snoop | TLP Attr bit | Skip cache coherency check | Use for DMA to non-cached |
| NFTS (N_FTS) | LTSSM training | # fast training sequences for L0s exit | Minimize for fast wake |

### Bandwidth Efficiency Calculation
```
Theoretical vs Actual bandwidth:

Gen3 x4 theoretical: 8 GT/s × 4 lanes × 128/130 = 31.51 Gbps = 3.94 GB/s

Actual overhead:
  TLP header: 12-16 bytes per TLP
  LCRC: 4 bytes per TLP
  Framing: 2 bytes per 130 (sync header)
  DLLP: ACK/FC packets consume bandwidth
  
For 256-byte payload MPS:
  Efficiency = 256 / (256 + 16 + 4) ≈ 92.8%
  Actual BW = 3.94 × 0.928 = 3.66 GB/s

For 128-byte payload MPS:
  Efficiency = 128 / (128 + 16 + 4) ≈ 86.5%
  Actual BW = 3.94 × 0.865 = 3.41 GB/s

Lesson: Larger MPS = higher efficiency (fewer headers per byte)
```

---

## 22. PCIe RESET & INITIALIZATION SEQUENCE

### Reset Types
```
1. Cold Reset (Fundamental Reset):
   - Power-on or PERST# assertion
   - ALL state lost, full re-enumeration
   - Device returns to Gen1 speed

2. Warm Reset:
   - PERST# toggled without power cycle
   - Similar to cold but power maintained
   
3. Hot Reset:
   - Downstream port sends TS1 with Hot Reset bit
   - Link retrained, device config preserved partially
   - In-band (no external signal needed)
   
4. Function Level Reset (FLR):
   - Reset single function without affecting others
   - Set via Device Control register bit 15
   - Only that function's state reset
```

### PERST# Timing
```
Power-on sequence:
  1. Power rails stable (3.3V Vaux, 12V/3.3V main)
  2. Reference clock stable (100 MHz ±300 ppm)
  3. Hold PERST# LOW for ≥100 µs after power stable
  4. Release PERST# (HIGH) — device begins initialization
  5. Wait T_PVPERL (100ms) for device internal init
  6. LTSSM begins: Detect state
  7. Link training: 20-100 ms typical
  8. Device accessible: CfgRd returns valid Vendor/Device ID
```

---

## 23. PCIe CLOCK ARCHITECTURE

### Clock Modes
```
1. Common Clock:
   - Both RC and EP share same reference clock source
   - Lower jitter budget (same clock tree)
   - Common in on-board devices (SA8295P + soldered WiFi)
   - Link Control: Common Clock Configuration bit = 1

2. Data Clock (Separate Reference Clocks):
   - RC and EP have independent 100 MHz clocks
   - Must tolerate ±300 ppm frequency offset
   - More jitter tolerance needed in CDR
   - Used for: Add-in cards, separate board devices

3. SRIS (Separate Reference clock, Independent SSC):
   - Each end has its own SSC (spread-spectrum)
   - Requires larger elasticity buffer
   - Emerging in automotive (SerDes + PCIe bridge)

SSC (Spread-Spectrum Clocking):
   - -0.5% down-spread at 30-33 kHz modulation
   - Reduces EMI peaks by ~10 dB
   - Both ends must use SSC or neither (common clock)
   - If separate: receiver CDR tracks frequency modulation
```

---

## 24. PCIe IN ANDROID AUTOMOTIVE OS (AAOS)

### PCIe Device Initialization in AAOS
```
Boot sequence:
  1. [Bootloader] PCIe RC initialized, basic enumeration
  2. [Kernel] Full enumeration: pci_scan_bus(), resource assignment
  3. [Kernel] Device-specific drivers probe (NVMe, WiFi, ETH)
  4. [Android Init] HAL services start (WiFi HAL, Storage HAL)
  5. [Framework] System services use HAL abstractions

Key devices in AAOS over PCIe:
  - Wi-Fi: hostapd / wpa_supplicant ← ath11k driver ← PCIe
  - Storage: ext4/f2fs ← block layer ← nvme driver ← PCIe
  - Ethernet: NetworkManager ← macb/stmmac driver ← PCIe
  - AI Accelerator: NNAPI ← vendor HAL ← PCIe driver
```

### Android Storage on NVMe
```
Traditional: UFS (Universal Flash Storage) via UFS host controller
Modern AAOS: NVMe SSD over PCIe Gen3/4

Advantages of NVMe for automotive:
  - Higher throughput: 3-7 GB/s vs UFS 2-4 GB/s
  - Lower latency: Direct memory-mapped queues
  - Better parallelism: Multi-queue → better multi-app performance
  - Standard: Same NVMe across different SoC vendors
  - Power: NVMe PS3/PS4 power states for parking

Android mount points on NVMe:
  /dev/nvme0n1p1 → /system
  /dev/nvme0n1p2 → /vendor
  /dev/nvme0n1p3 → /data (user data, encrypted)
  /dev/nvme0n1p4 → /metadata
```

---

## 25. COMMON PCIe ISSUES & DEBUG APPROACHES

| Issue | Symptoms | Debug Approach |
|-------|----------|----------------|
| Link training failure | Device not enumerated, lspci empty | Check PERST#, clock, PHY status |
| Link at lower speed | Gen1 instead of Gen3 | Check equalization, retrain, eye diagram |
| Link at lower width | x1 instead of x4 | Check lane mapping, DT config, connector |
| Completion Timeout | Driver timeout errors in dmesg | Check device power state, ASPM, MPS |
| DMA failures | Data corruption, SMMU faults | Check IOMMU mapping, address width |
| Intermittent errors | AER correctable errors accumulating | Signal integrity, temperature, power noise |
| Device not resetting | Stuck after PERST# toggle | Check GPIO configuration, timing |
| MSI not working | Device works but no interrupts | Check MSI capability, GIC/interrupt-map |
| Power state stuck | Device won't wake from D3/L2 | Check CLKREQ#, WAKE# wiring |
| Hot temperature errors | Errors increase with temperature | Add cooling, reduce link speed |

---

END OF DOCUMENT 01 — MASTER THEORY
