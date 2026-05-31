# PCI EXPRESS (PCIe) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: PCI Express (PCIe) | Document: 04 of 08
# Format: Q (front) / A (back) — 210+ cards across 9 decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: FUNDAMENTALS (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | What does PCIe stand for? | PCI Express (Peripheral Component Interconnect Express) |
| 2 | Who defines PCIe? | PCI-SIG (PCI Special Interest Group) |
| 3 | Year of PCIe 1.0? | 2003 |
| 4 | What did PCIe replace? | PCI and PCI-X parallel buses |
| 5 | PCIe topology? | Point-to-point (tree structure with switches) |
| 6 | PCIe duplex mode? | Full-duplex (simultaneous TX + RX) |
| 7 | What is a lane? | One TX differential pair + one RX differential pair |
| 8 | Common link widths? | x1, x2, x4, x8, x16 |
| 9 | Three PCIe layers? | Transaction, Data Link, Physical |
| 10 | Transaction Layer unit? | TLP (Transaction Layer Packet) |
| 11 | Data Link Layer unit? | DLLP (Data Link Layer Packet) |
| 12 | Physical Layer unit? | Ordered Sets (TS1, TS2, SKP, etc.) |
| 13 | What is Root Complex (RC)? | PCIe originator — typically CPU/SoC |
| 14 | What is Endpoint (EP)? | PCIe target device (NVMe, NIC, GPU) |
| 15 | What is a Switch? | Multi-port PCIe packet router |
| 16 | BDF stands for? | Bus:Device.Function (PCIe address) |
| 17 | BDF format? | 8-bit bus : 5-bit device . 3-bit function |
| 18 | What is ECAM? | Enhanced Config Access Mechanism (memory-mapped config) |
| 19 | Config space size (PCIe)? | 4096 bytes per function (256B legacy + 3840B extended) |
| 20 | Reference clock frequency? | 100 MHz |
| 21 | PCIe differential impedance? | 85-100Ω |
| 22 | Can PCIe hot-plug? | Yes (optional capability) |
| 23 | PCIe vs PCI software? | Backward compatible (same config space model) |
| 24 | What is PERST#? | PCIe fundamental reset (active-low GPIO) |
| 25 | PCIe predecessor? | PCI (parallel, shared bus, 33/66 MHz) |

---

# DECK 2: GENERATIONS & SPEED (20 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Gen1 data rate? | 2.5 GT/s per lane |
| 2 | Gen2 data rate? | 5 GT/s per lane |
| 3 | Gen3 data rate? | 8 GT/s per lane |
| 4 | Gen4 data rate? | 16 GT/s per lane |
| 5 | Gen5 data rate? | 32 GT/s per lane |
| 6 | Gen6 data rate? | 64 GT/s per lane |
| 7 | Gen1-2 encoding? | 8b/10b (20% overhead) |
| 8 | Gen3-5 encoding? | 128b/130b (1.54% overhead) |
| 9 | Gen6 signaling? | PAM4 (4 voltage levels, 2 bits/symbol) |
| 10 | Gen3 effective BW per lane? | ~985 MB/s (8 × 128/130 / 8) |
| 11 | Gen4 x4 total BW? | ~7.88 GB/s per direction |
| 12 | Gen3 x4 total BW? | ~3.94 GB/s per direction |
| 13 | Why Gen3 encoding change? | 20% overhead → 1.5% (nearly doubled efficiency) |
| 14 | Gen6 FLIT size? | 256 bytes (fixed-size flow control unit) |
| 15 | Gen6 FEC purpose? | Forward Error Correction (correct errors without retry) |
| 16 | GT/s meaning? | Giga-Transfers per second (raw symbol rate) |
| 17 | Link always starts at? | Gen1 speed (2.5 GT/s), upgrades via Recovery |
| 18 | Speed change mechanism? | Enter Recovery state → negotiate higher speed |
| 19 | SA8295P max PCIe gen? | Gen3 (some ports Gen4 capable) |
| 20 | PCIe 7.0 target rate? | 128 GT/s (PAM4) |

---

# DECK 3: TRANSACTION LAYER & TLPs (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | What is a TLP? | Transaction Layer Packet — main PCIe data unit |
| 2 | Three TLP categories? | Posted, Non-Posted, Completion |
| 3 | Posted examples? | Memory Write, Messages |
| 4 | Non-Posted examples? | Memory Read, Config Read/Write, IO Read/Write |
| 5 | Posted characteristic? | Fire-and-forget (no completion required) |
| 6 | Non-Posted characteristic? | Must wait for Completion response |
| 7 | TLP header size? | 3 DWORDs (12B) or 4 DWORDs (16B) |
| 8 | When 4DW header? | 64-bit addressing (address > 4GB) |
| 9 | Max TLP payload? | 4096 bytes (MPS field max) |
| 10 | What is MPS? | Max Payload Size (128-4096 bytes, configured) |
| 11 | What is MRRS? | Max Read Request Size (max single read size) |
| 12 | Tag field purpose? | Unique identifier for outstanding non-posted transactions |
| 13 | Tag size? | 8-bit (256 outstanding) or 10-bit extended (1024) |
| 14 | Requester ID contents? | 16-bit: BDF of transaction originator |
| 15 | Byte Enable purpose? | Specify which bytes valid in first/last DWORD |
| 16 | TD bit meaning? | TLP Digest present (ECRC appended) |
| 17 | EP bit meaning? | Data Poisoned (error flag, payload unreliable) |
| 18 | TC field meaning? | Traffic Class (0-7, for QoS priority) |
| 19 | CfgRd0 vs CfgRd1? | Type 0: local device. Type 1: forwarded downstream |
| 20 | Completion status codes? | SC (Success), UR (Unsupported Request), CA (Completer Abort), CRS |
| 21 | What is UR? | Unsupported Request — device doesn't support this transaction |
| 22 | What is CRS? | Configuration Retry Status — device not ready, try again |
| 23 | Relaxed Ordering effect? | Allows posted writes to pass other posted writes |
| 24 | No Snoop effect? | Skip CPU cache coherency (faster for DMA to non-cached) |
| 25 | Length field bits? | 10 bits (0-1023 DWORDs; 0 encodes 1024) |
| 26 | Memory address alignment? | 4-byte (DWORD) aligned (bottom 2 bits always 0) |
| 27 | What is ECRC? | End-to-end CRC (survives through switches, optional) |
| 28 | Message TLP types? | INTx, PM, Error, Slot Power, Vendor, etc. |
| 29 | Fmt field purpose? | Indicates 3DW/4DW header and with/without data |
| 30 | How does split completion work? | Read request split into multiple smaller completions (each ≤ RCB) |

---

# DECK 4: DATA LINK LAYER (20 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | DLL primary function? | Reliable TLP delivery (CRC + ACK/NAK + retry) |
| 2 | LCRC size? | 32 bits (covers entire TLP) |
| 3 | Sequence number size? | 12 bits (0-4095) |
| 4 | What is Replay Buffer? | Stores sent TLPs until ACKed (for retransmission) |
| 5 | ACK DLLP meaning? | All TLPs up to acknowledged sequence number received OK |
| 6 | NAK DLLP meaning? | Request retransmission from this sequence number |
| 7 | What is REPLAY_TIMER? | Timeout for auto-replay if no ACK received |
| 8 | What is REPLAY_NUM? | Max retransmission attempts (3-4) before link error |
| 9 | DLLP types? | ACK, NAK, InitFC1, InitFC2, UpdateFC, PM |
| 10 | Flow control DLLP purpose? | Advertise/update available buffer credits |
| 11 | 6 FC credit types? | PH, PD, NPH, NPD, CplH, CplD |
| 12 | Header credit unit? | 1 credit = 1 TLP header (any size) |
| 13 | Data credit unit? | 1 credit = 4 DWORDs = 16 bytes |
| 14 | What if credits = 0? | Sender STALLS until UpdateFC received |
| 15 | InitFC1 when sent? | During link initialization (first FC advertisement) |
| 16 | InitFC2 when sent? | During link init (confirms first FC values) |
| 17 | UpdateFC when sent? | When receiver frees buffer space |
| 18 | Infinite credits meaning? | Receiver guarantees always accepting (max credits advertised) |
| 19 | Can DLLPs be lost? | They don't have their own ACK mechanism (rely on periodic update) |
| 20 | DLL error → what state? | Recovery state (LTSSM) for link retrain |

---

# DECK 5: PHYSICAL LAYER & LTSSM (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | LTSSM stands for? | Link Training and Status State Machine |
| 2 | First LTSSM state? | Detect (sense receiver impedance) |
| 3 | Detect mechanism? | TX sends common-mode pulse, checks for ~50Ω load |
| 4 | Polling purpose? | Exchange TS1/TS2, achieve bit lock and symbol lock |
| 5 | Configuration purpose? | Negotiate link width and lane numbering |
| 6 | L0 state? | Normal active operation |
| 7 | Recovery purpose? | Re-train for speed change, error, equalization |
| 8 | L0s characteristics? | Low-latency standby, TX idle, <1µs exit |
| 9 | L1 characteristics? | Both directions idle, PLL may be off, 2-4µs exit |
| 10 | L2 characteristics? | Near power-off, Vaux only, WAKE# to exit |
| 11 | TS1 ordered set content? | Link#, Lane#, Speed, EQ settings, training control |
| 12 | TS2 ordered set content? | Confirms TS1 parameters (handshake complete) |
| 13 | SKP ordered set purpose? | Clock compensation (absorbs frequency difference) |
| 14 | FTS ordered set purpose? | Fast Training Sequence (quick exit from L0s) |
| 15 | EIEOS purpose? | Electrical Idle Exit Ordered Set (marks end of idle) |
| 16 | SDS purpose? | Start of Data Stream (transition from training to data) |
| 17 | Equalization phases? | Phase 0→1→2→3 (in Recovery.Equalization) |
| 18 | TX EQ type? | 3-tap FIR filter (pre-cursor, main, post-cursor) |
| 19 | RX EQ types? | CTLE (analog HF boost) + DFE (digital feedback) |
| 20 | Number of TX presets? | 11 (P0-P10) |
| 21 | Scrambler for Gen3+? | 23-bit LFSR |
| 22 | 128b/130b sync header? | 01 = Data block, 10 = Ordered Set block |
| 23 | 8b/10b comma character? | K28.5 (used for alignment) |
| 24 | Running Disparity purpose? | DC balance in 8b/10b (equal 1s and 0s over time) |
| 25 | Lane deskew requirement? | Receiver must compensate up to 20ns lane-to-lane skew |

---

# DECK 6: POWER MANAGEMENT (20 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | ASPM stands for? | Active State Power Management |
| 2 | ASPM L0s? | Automatic standby per direction (independent TX/RX) |
| 3 | ASPM L1? | Automatic deep idle (both directions must be idle) |
| 4 | L0s exit latency? | <1 µs |
| 5 | L1 exit latency? | 2-4 µs (typical) |
| 6 | L1.1 what's off? | PLL off, common mode maintained |
| 7 | L1.2 what's off? | PHY completely off, only ref clock buffer |
| 8 | L1.2 exit latency? | 32-64 µs |
| 9 | CLKREQ# purpose? | Device signals when reference clock needed |
| 10 | CLKREQ# required for? | L1 substates (L1.1/L1.2) |
| 11 | WAKE# purpose? | Device signals wake event to host (from L2/L3) |
| 12 | PM Capability ID? | 0x01 |
| 13 | D-states? | D0 (active), D1, D2, D3hot, D3cold |
| 14 | D3hot? | Device powered, link in L1/L2, config space accessible |
| 15 | D3cold? | Main power removed, only Vaux (if present) |
| 16 | Linux ASPM control? | `/sys/bus/pci/devices/XXX/link/l1_aspm` or kernel param |
| 17 | ASPM disable kernel param? | `pcie_aspm=off` |
| 18 | ASPM force kernel param? | `pcie_aspm=force` (override BIOS) |
| 19 | PM saves power how much? | L1.2: ~1% of L0 power consumption |
| 20 | Automotive PM concern? | Fast wake needed → L1.1 preferred over L1.2 for WiFi |

---

# DECK 7: CONFIGURATION & ENUMERATION (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Capabilities Pointer offset? | 0x34 (byte in config header) |
| 2 | PCIe Capability ID? | 0x10 |
| 3 | MSI Capability ID? | 0x05 |
| 4 | MSI-X Capability ID? | 0x11 |
| 5 | PM Capability ID? | 0x01 |
| 6 | AER Extended Cap ID? | 0x0001 |
| 7 | SR-IOV Extended Cap ID? | 0x0010 |
| 8 | L1SS Extended Cap ID? | 0x001E |
| 9 | Extended caps start at? | Offset 0x100 |
| 10 | Header Type 0? | Endpoint (6 BARs) |
| 11 | Header Type 1? | Bridge/Root Port (2 BARs + bus numbers) |
| 12 | Vendor ID = 0xFFFF means? | No device present (empty slot) |
| 13 | BAR sizing: write what? | 0xFFFFFFFF, then read back |
| 14 | BAR bit 0 = 0 means? | Memory space |
| 15 | BAR bit 0 = 1 means? | IO space |
| 16 | BAR bits [2:1] = 10? | 64-bit memory (uses next BAR too) |
| 17 | BAR bit 3 = prefetchable? | Yes: no side effects, can be combined/cached |
| 18 | Command Register bit 1? | Memory Space Enable |
| 19 | Command Register bit 2? | Bus Master Enable (allows DMA) |
| 20 | ECAM formula? | Base + (Bus<<20) + (Dev<<15) + (Fn<<12) + Reg |
| 21 | Max buses? | 256 (8-bit bus number) |
| 22 | Max devices per bus? | 32 (5-bit device number) |
| 23 | Max functions per device? | 8 (3-bit function number) |
| 24 | Multi-function bit? | Header Type bit 7 = 1 (device has >1 function) |
| 25 | Bridge secondary bus? | Bus number assigned to downstream link |

---

# DECK 8: INTERRUPTS & DMA (20 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | INTx: shared or dedicated? | Shared (level-triggered, slow) |
| 2 | MSI mechanism? | Memory Write TLP to interrupt controller address |
| 3 | MSI max vectors? | 32 per function |
| 4 | MSI-X max vectors? | 2048 per function |
| 5 | MSI-X table stored where? | In BAR memory (not config space) |
| 6 | MSI-X advantage over MSI? | Independent targeting per vector, table in BAR |
| 7 | MSI-X table entry size? | 16 bytes (addr_lo, addr_hi, data, control) |
| 8 | Bus Master Enable for DMA? | Must set Command Register bit 2 |
| 9 | DMA direction: device reads? | Device sends Memory Read TLP → gets Completion w/data |
| 10 | DMA direction: device writes? | Device sends Memory Write TLP (Posted) |
| 11 | IOMMU purpose? | Translate device DMA addresses, provide isolation |
| 12 | ARM IOMMU name? | SMMU (System Memory Management Unit) |
| 13 | Intel IOMMU name? | VT-d (Virtualization Technology for Directed I/O) |
| 14 | Stream ID derived from? | Requester ID (BDF) of the device |
| 15 | dma_alloc_coherent returns? | CPU virtual addr + DMA address (IOVA) |
| 16 | dma_map_single for? | Map existing buffer for streaming DMA |
| 17 | DMA_FROM_DEVICE meaning? | Device writes to memory (device → host) |
| 18 | DMA_TO_DEVICE meaning? | Device reads from memory (host → device) |
| 19 | 64-bit DMA mask? | DMA_BIT_MASK(64) — device can address full memory |
| 20 | Why readl after writel? | Forces write completion (read cannot pass write in PCIe) |

---

# DECK 9: AUTOMOTIVE & LINUX (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | SA8295P PCIe ports? | 2-3 Root Complex ports |
| 2 | Typical SA8295P PCIe0 use? | WiFi 6E module (Gen3 x1) |
| 3 | Typical SA8295P PCIe1 use? | NVMe SSD (Gen3 x4) |
| 4 | Qualcomm PCIe host driver? | pcie-qcom.c (drivers/pci/controller/dwc/) |
| 5 | DWC stands for? | DesignWare Core (Synopsys PCIe IP) |
| 6 | lspci command? | List all PCI devices |
| 7 | lspci -vvv? | Verbose: show capabilities, BARs, link status |
| 8 | setpci command? | Read/write config space registers |
| 9 | Linux PCIe sysfs path? | /sys/bus/pci/devices/DDDD:BB:DD.F/ |
| 10 | Check link speed? | `lspci -vvv | grep LnkSta` |
| 11 | PCIe DT compatible (Qcom)? | "qcom,pcie-sa8295p" |
| 12 | DT perst-gpios? | GPIO for PERST# control |
| 13 | DT num-lanes? | Number of PCIe lanes configured |
| 14 | DT max-link-speed? | Maximum gen supported (1=Gen1, 3=Gen3) |
| 15 | NVMe over PCIe benefit? | 3-7 GB/s vs UFS 2-4 GB/s, multi-queue |
| 16 | WiFi PCIe benefit? | DMA-capable, MSI-X, low latency |
| 17 | ath11k/ath12k? | Linux WiFi driver for Qualcomm PCIe WiFi chips |
| 18 | pci_enable_device()? | Enable device power, BARs, assign IRQ |
| 19 | pci_set_master()? | Enable Bus Master (allow DMA) |
| 20 | pci_alloc_irq_vectors()? | Allocate MSI/MSI-X interrupt vectors |
| 21 | AER in automotive? | Monitor link health, detect connector/EMI issues |
| 22 | Completion timeout auto? | Common: Device in ASPM, slow to respond |
| 23 | PCIe SSC in automotive? | -0.5% down-spread for EMI (CISPR 25) |
| 24 | SR-IOV automotive use? | Share NIC between QNX cluster + Android IVI VMs |
| 25 | CXL over PCIe PHY? | Compute Express Link — cache-coherent memory sharing |

---

END OF DOCUMENT 04 — FLASHCARDS (210 cards across 9 decks)
