# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 07: HIGH-SPEED SERIAL PROTOCOLS                                      ║
# ║  PCIe, USB, Thunderbolt, DisplayPort, HDMI                                 ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

High-speed serial protocols replaced parallel buses in the early 2000s and now dominate all external and many internal connections in computing. PCIe is the universal internal bus; USB is the universal external connector; DisplayPort/HDMI handle all video output.

## PURPOSE
Provide high-bandwidth, low-latency, standardized interfaces for connecting processors to peripherals, displays, storage, networks, and other systems.

## ENGINEERING PROBLEMS SOLVED
- Bandwidth scaling without pin explosion (lane aggregation)
- Hot-plug and device discovery
- Power delivery over data cable (USB-PD, Thunderbolt)
- Protocol tunneling (Thunderbolt tunnels PCIe + DP + USB)
- Backward compatibility across generations
- Signal integrity at multi-GHz frequencies

---

## PROTOCOL FAMILY TABLE

| Protocol | Year | Current Gen | Speed/Lane | Max Lanes | Total BW | Domain |
|----------|------|-------------|-----------|-----------|----------|--------|
| PCIe 5.0 | 2019 | Gen5 | 32 GT/s | ×16 | 128 GB/s | Internal bus |
| PCIe 6.0 | 2022 | Gen6 | 64 GT/s | ×16 | 256 GB/s | Internal bus |
| USB 2.0 | 2000 | - | 480 Mbps | 1 | 480 Mbps | Peripheral |
| USB 3.2 | 2017 | Gen2×2 | 10 Gbps | ×2 | 20 Gbps | Peripheral |
| USB4 v2 | 2022 | - | 40/80 Gbps | Tunneled | 120 Gbps | Universal |
| Thunderbolt 4 | 2020 | TB4 | 40 Gbps | Tunneled | 40 Gbps | Universal |
| Thunderbolt 5 | 2024 | TB5 | 80/120 Gbps | Tunneled | 120 Gbps | Universal |
| DisplayPort 2.1 | 2022 | DP 2.1 | 20 Gbps | ×4 | 80 Gbps | Display |
| HDMI 2.1 | 2017 | HDMI 2.1 | 12 Gbps | ×4 | 48 Gbps | Display/AV |

---

## PCIe (PCI Express)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Peripheral Component Interconnect Express |
| Standard | PCI-SIG specifications |
| Gen5 (current) | 32 GT/s, 128b/130b encoding, NRZ |
| Gen6 (latest) | 64 GT/s, 242B/256B (FLIT), PAM-4 |
| Creator | PCI-SIG |
| Topology | Point-to-point with switches (tree) |
| Layers | Physical → Data Link → Transaction |
| Max Lanes | ×1, ×2, ×4, ×8, ×16 |
| Use | GPU, NVMe, NIC, FPGA, AI accelerators, CXL |

### PCIe Generation Summary
| Gen | Year | Rate/Lane | Encoding | BW/lane | ×16 BW |
|-----|------|-----------|----------|---------|--------|
| 1.0 | 2003 | 2.5 GT/s | 8b/10b | 250 MB/s | 4 GB/s |
| 2.0 | 2007 | 5 GT/s | 8b/10b | 500 MB/s | 8 GB/s |
| 3.0 | 2010 | 8 GT/s | 128b/130b | ~1 GB/s | 16 GB/s |
| 4.0 | 2017 | 16 GT/s | 128b/130b | ~2 GB/s | 32 GB/s |
| 5.0 | 2019 | 32 GT/s | 128b/130b | ~4 GB/s | 64 GB/s |
| 6.0 | 2022 | 64 GT/s | FLIT/PAM-4 | ~8 GB/s | 128 GB/s |
| 7.0 | 2025 | 128 GT/s | FLIT/PAM-4 | ~16 GB/s | 256 GB/s |

### PCIe Layer Architecture
```
┌─────────────────────────────────────────────────────────┐
│  Software Layer                                          │
│  ├── Device Driver                                       │
│  ├── Configuration Space (256B legacy / 4KB extended)    │
│  └── Memory-Mapped I/O (BAR regions)                     │
├─────────────────────────────────────────────────────────┤
│  Transaction Layer (TLP - Transaction Layer Packet)       │
│  ├── Memory Read/Write (MRd, MWr)                        │
│  ├── I/O Read/Write                                      │
│  ├── Configuration Read/Write                            │
│  ├── Completions (CplD)                                  │
│  ├── Messages (interrupts, errors, PM)                   │
│  └── Flow Control (credit-based)                         │
├─────────────────────────────────────────────────────────┤
│  Data Link Layer (DLLP)                                  │
│  ├── Sequence numbers                                    │
│  ├── CRC (LCRC: 32-bit)                                 │
│  ├── ACK/NAK protocol (retry on error)                   │
│  └── Flow control updates                                │
├─────────────────────────────────────────────────────────┤
│  Physical Layer                                          │
│  ├── Encoding (8b/10b → 128b/130b → FLIT)              │
│  ├── Scrambling (LFSR-based)                            │
│  ├── Lane bonding (×1 to ×16)                           │
│  ├── Equalization (Gen3+: Tx preset, Rx adaptation)      │
│  └── Electrical (differential, NRZ/PAM-4)               │
└─────────────────────────────────────────────────────────┘
```

### PCIe Configuration Space
- Legacy: 256 bytes (PCI-compatible, accessed via CF8/CFC I/O)
- Extended: 4096 bytes (memory-mapped via ECAM)
- Key registers: VendorID, DeviceID, BAR0-5, Capability pointers
- Capabilities: MSI/MSI-X, Power Management, AER, SRIOV, etc.

### PCIe in Linux
- `drivers/pci/` — PCI/PCIe core subsystem
- `/sys/bus/pci/devices/` — device enumeration
- `lspci -vvv` — detailed device info
- BAR mapping: `pci_iomap()`, `devm_ioremap()`
- DMA: `dma_alloc_coherent()`, IOMMU/SMMU

---

## USB (Universal Serial Bus)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Universal Serial Bus |
| Standard | USB-IF specifications |
| Versions | 1.0 (1996), 2.0 (2000), 3.0 (2008), 3.2 (2017), 4 (2019), 4v2 (2022) |
| Creator | USB Implementers Forum |
| Connector | Type-A, Type-B, Mini, Micro, Type-C |
| Power | USB-PD: up to 240W (48V/5A) |
| Topology | Host-device (hub tree), USB4: peer-to-peer |

### USB Speed Timeline
| Version | Marketing Name | Speed | Encoding |
|---------|---------------|-------|----------|
| USB 1.0 | Low-Speed | 1.5 Mbps | NRZI |
| USB 1.1 | Full-Speed | 12 Mbps | NRZI |
| USB 2.0 | Hi-Speed | 480 Mbps | NRZI |
| USB 3.0 | SuperSpeed (SS5) | 5 Gbps | 8b/10b |
| USB 3.1 | SuperSpeed+ (SS10) | 10 Gbps | 128b/132b |
| USB 3.2 Gen2×2 | SuperSpeed+ (SS20) | 20 Gbps | 128b/132b ×2 |
| USB4 Gen3 | USB4 40 | 40 Gbps | 128b/132b (tunneled) |
| USB4 v2 Gen4 | USB4 80 | 80 Gbps | PAM-3 |

### USB Protocol Layers
```
USB 2.0:
  Host Controller (EHCI/xHCI) → Hub → Device
  Transfers: Control, Bulk, Interrupt, Isochronous
  Polling: Host always initiates

USB 3.x/4:
  xHCI → SuperSpeed (separate Tx/Rx pairs)
  Asynchronous (device-initiated via ERDY)
  Streams (multiple data channels per endpoint)
  
USB4:
  Tunneling architecture (PCIe + DP + USB3 over one link)
  Based on Thunderbolt 3 protocol
```

### USB Type-C
- **Connector**: Reversible, 24-pin
- **Alt Modes**: DisplayPort, Thunderbolt, HDMI
- **USB-PD**: Power Delivery negotiation (5V/9V/15V/20V/48V)
- **CC pins**: Configuration channel (orientation, mode negotiation)
- **Not a speed spec**: Type-C is connector only (can be USB 2.0 to USB4)

### USB in Linux
- `drivers/usb/` — host, gadget, typec, storage, serial
- xHCI: `drivers/usb/host/xhci.c` (USB 3.x/4 host controller)
- USB gadget: `drivers/usb/gadget/` (device/peripheral mode)
- Device classes: HID, Mass Storage, CDC (serial), UAC (audio), UVC (video)
- `/sys/bus/usb/devices/`, `lsusb -v`

---

## Thunderbolt

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Thunderbolt |
| Standard | Intel/USB-IF (TB3/4 contributed to USB4) |
| Versions | TB1 (2011), TB2 (2013), TB3 (2016), TB4 (2020), TB5 (2024) |
| Connector | Mini-DP (TB1/2), USB-C (TB3+) |
| Speed | TB4: 40 Gbps, TB5: 80/120 Gbps |
| Tunneling | PCIe + DisplayPort + USB3 over single link |
| Daisy-chain | Up to 6 devices |
| Power | Up to 100W (TB3/4), 140W (TB5) |

### Thunderbolt Tunneling
```
┌──────────────────────────────────────────────┐
│  Thunderbolt Link (80 Gbps TB5)              │
├──────────────────────────────────────────────┤
│  ┌─────────┐ ┌──────────┐ ┌──────────────┐  │
│  │PCIe     │ │DisplayPort│ │USB 3.2/USB4  │  │
│  │Tunnel   │ │Tunnel     │ │Tunnel        │  │
│  │(eGPU,   │ │(monitors) │ │(storage,     │  │
│  │ NVMe)   │ │           │ │ peripherals) │  │
│  └─────────┘ └──────────┘ └──────────────┘  │
├──────────────────────────────────────────────┤
│  Protocol Adapter Layer (scheduling)          │
├──────────────────────────────────────────────┤
│  Physical Layer (USB-C connector, retimers)   │
└──────────────────────────────────────────────┘
```

---

## DisplayPort

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | DisplayPort |
| Standard | VESA DisplayPort |
| Versions | 1.0 (2006) → 1.4 (2016) → 2.0/2.1 (2022) |
| Creator | VESA (Video Electronics Standards Association) |
| Current | DP 2.1 UHBR20: 80 Gbps total |
| Lanes | 1, 2, or 4 main link lanes |
| Encoding | 8b/10b (DP 1.x), 128b/132b (DP 2.x) |
| Use | PC monitors, laptops, eDP (internal panels) |

### DP Link Rates
| Mode | Per-Lane | 4-Lane Total | Resolution Support |
|------|----------|-------------|-------------------|
| HBR (DP 1.1) | 2.7 Gbps | 10.8 Gbps | 2560×1600 |
| HBR2 (DP 1.2) | 5.4 Gbps | 21.6 Gbps | 4K@60 |
| HBR3 (DP 1.4) | 8.1 Gbps | 32.4 Gbps | 4K@120, 8K@30 |
| UHBR10 (DP 2.0) | 10 Gbps | 40 Gbps | 8K@60 |
| UHBR13.5 (DP 2.1) | 13.5 Gbps | 54 Gbps | 8K@60 HDR |
| UHBR20 (DP 2.1) | 20 Gbps | 80 Gbps | 16K@60 |

### eDP (Embedded DisplayPort)
- Internal display connection (laptop panels, automotive displays)
- Panel Self-Refresh (PSR): panel stores frame buffer, link sleeps
- Used in AAOS: cockpit displays connected via eDP

### DP Alt Mode (over USB-C)
- DisplayPort signal carried over USB Type-C connector
- Can use 1, 2, or 4 lanes (remaining for USB data)
- 4-lane: DP only (no USB3 data, only USB2)
- 2-lane: DP + USB3 simultaneously

---

## HDMI

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | High-Definition Multimedia Interface |
| Standard | HDMI Forum specification |
| Versions | 1.0 (2002) → 2.0 (2013) → 2.1 (2017) |
| Speed | HDMI 2.1: 48 Gbps (Fixed Rate Link) |
| Creator | HDMI Founders (Hitachi, Panasonic, Philips, Sony, Toshiba, Silicon Image) |
| Features | Audio + Video + CEC + ARC/eARC |
| Encoding | TMDS (1.x/2.0), FRL (2.1) |
| Use | TV, AV receivers, gaming monitors |

### HDMI 2.1 Features
- **FRL** (Fixed Rate Link): 48 Gbps → 4K@120, 8K@60
- **VRR** (Variable Refresh Rate): Gaming (like FreeSync/G-Sync)
- **ALLM** (Auto Low Latency Mode): Game mode auto-switch
- **eARC**: Full bitstream audio (Atmos, DTS:X) over ARC channel
- **DSC** (Display Stream Compression): Visually lossless compression

---

## COMPARISON TABLE

| Feature | PCIe 5.0 | USB4 v2 | TB5 | DP 2.1 | HDMI 2.1 |
|---------|----------|---------|-----|--------|----------|
| Max BW | 128 GB/s | 120 Gbps | 120 Gbps | 80 Gbps | 48 Gbps |
| Lanes | 16 | Tunneled | Tunneled | 4 | 4 |
| Primary use | Internal | External I/O | External I/O | Display | Display+Audio |
| Connector | Slot/M.2 | USB-C | USB-C | DP/USB-C | HDMI |
| Hot-plug | Yes | Yes | Yes | Yes | Yes |
| Power delivery | No (slot) | 240W | 140W | No | No (5V only) |
| Protocol | PCIe | Tunnel | Tunnel | Video | Video+Audio |

---

## FLASH CARDS (15)

| # | Front | Back |
|---|-------|------|
| 1 | PCIe Gen5 speed per lane? | 32 GT/s (~4 GB/s with 128b/130b) |
| 2 | PCIe Gen6 signaling? | PAM-4 (4 levels), 64 GT/s per lane |
| 3 | PCIe TLP? | Transaction Layer Packet (carries read/write/completion) |
| 4 | PCIe flow control? | Credit-based (receiver advertises buffer space) |
| 5 | USB4 based on? | Thunderbolt 3 protocol (tunneling architecture) |
| 6 | USB-C vs USB4? | Type-C = connector. USB4 = protocol. Separate specs |
| 7 | USB-PD max power? | 240W (48V × 5A, Extended Power Range) |
| 8 | Thunderbolt tunnels? | PCIe + DisplayPort + USB3 over one link |
| 9 | DP 2.1 max bandwidth? | 80 Gbps (UHBR20 × 4 lanes) |
| 10 | eDP purpose? | Embedded DisplayPort (internal laptop/auto panels) |
| 11 | HDMI vs DP? | HDMI: consumer AV. DP: PC/professional. DP is royalty-free |
| 12 | PCIe BAR? | Base Address Register (maps device memory to CPU space) |
| 13 | PCIe MSI-X? | Message Signaled Interrupts (up to 2048 vectors, per-queue) |
| 14 | USB xHCI? | eXtensible Host Controller Interface (USB 3.x/4 host) |
| 15 | PCIe ECAM? | Enhanced Configuration Access Mechanism (memory-mapped config space) |

---

## INTERVIEW QUESTIONS (5)

**Q1: Explain PCIe credit-based flow control and why it's used.**
A: PCIe uses credits to prevent buffer overflow. Receiver advertises available buffer space (credits) for each TLP type (Posted, Non-Posted, Completion) and each header/data. Transmitter decrements credits per TLP sent. When credits reach zero, transmitter stops. Receiver returns credits via DLLP (UpdateFC) after consuming TLPs. This allows maximum throughput without risk of packet drops — no NAK needed for flow control (NAK is only for errors).

**Q2: How does USB4 tunneling work and what are the bandwidth allocation implications?**
A: USB4 multiplexes three protocols over one physical link using time-division tunneling. A Protocol Adapter (PA) layer schedules bandwidth between PCIe tunnel, DP tunnel, and USB3 tunnel. Bandwidth is dynamically allocated — if no display is connected, all 80 Gbps goes to PCIe/USB. With a 4K display consuming ~25 Gbps, remaining ~55 Gbps is available for PCIe. This is why TB/USB4 eGPU performance varies depending on connected displays.

**Q3: What is PCIe equalization and why does Gen3+ require it?**
A: At 8+ GT/s, the channel (PCB traces, connectors, vias) causes frequency-dependent attenuation (insertion loss) and reflections. Without equalization, the data "eye" closes and BER increases. Gen3+ uses: (1) Tx equalization (pre-emphasis/de-emphasis via presets), (2) Rx equalization (CTLE + DFE adaptive filters). Link training negotiates optimal Tx presets through a multi-phase equalization process (Phase 0-3 in Gen3-5).

**Q4: Compare eDP and HDMI for automotive cockpit displays.**
A: eDP: designed for embedded panels (direct SoC→display), low latency, Panel Self-Refresh (battery savings), no HDCP overhead, simple (no audio), used for instrument cluster and center stack. HDMI: designed for consumer interconnect, carries audio, HDCP encryption, longer cable, used for rear-seat entertainment, external display output. In SA8295P AAOS: eDP for primary cockpit displays, HDMI for passenger screens.

**Q5: Why did PCIe Gen6 switch to PAM-4 and FLIT-based encoding?**
A: Doubling speed (32→64 GT/s) with NRZ would require channel bandwidth beyond practical PCB limits. PAM-4 transmits 2 bits per symbol (4 voltage levels), achieving 64 GT/s at only 32 GHz Nyquist frequency (same as Gen5 NRZ). Trade-off: PAM-4 has ~9.5 dB less SNR → higher BER. To compensate, Gen6 uses: (1) 256-byte FLITs with CRC and forward error correction (FEC), replacing per-TLP LCRC, (2) Lightweight FEC repairs most errors without retry, maintaining effective throughput.

---

END OF PART 07 — HIGH-SPEED SERIAL PROTOCOLS
