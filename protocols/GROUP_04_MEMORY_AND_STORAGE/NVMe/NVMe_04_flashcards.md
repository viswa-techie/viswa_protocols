# NVMe (Non-Volatile Memory Express) — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: NVMe | Document: 04 of 08
# Format: 180 cards across 9 decks for spaced repetition
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | What does NVMe stand for? | Non-Volatile Memory Express |
| 2 | What transport does NVMe natively use? | PCIe (PCI Express) |
| 3 | Year NVMe 1.0 was released? | 2011 |
| 4 | Why was NVMe created? | AHCI/SATA was designed for HDDs; bottlenecked flash (1 queue, 32 depth) |
| 5 | Max number of IO queues in NVMe? | 65,535 |
| 6 | Max entries per queue? | 65,536 |
| 7 | Size of a Submission Queue Entry (SQE)? | 64 bytes |
| 8 | Size of a Completion Queue Entry (CQE)? | 16 bytes |
| 9 | What is a Namespace? | Logical collection of addressable LBAs (like a LUN) |
| 10 | What register space exposes NVMe? | BAR0 (Base Address Register 0) — MMIO |
| 11 | NVMe PCIe Class Code? | 0x010802 |
| 12 | How many Admin queue pairs? | 1 (SQ0 + CQ0) |
| 13 | What does a doorbell register do? | Notifies controller of new commands (SQ Tail) or consumed completions (CQ Head) |
| 14 | What is the Phase Tag? | 1-bit field in CQE that alternates per ring pass; indicates new vs old entries |
| 15 | CPU cycles per NVMe IO (approx)? | ~10,000 (vs ~30,000 for AHCI) |
| 16 | NVMe read latency (typical SSD)? | 10-100 μs |
| 17 | Who maintains the NVMe spec? | NVM Express, Inc. (nvmexpress.org) |
| 18 | NVMe 2.0 key change? | Modular spec split: base + command sets + transports |
| 19 | Where are SQ and CQ located? | Host system memory (DRAM) |
| 20 | What interrupt mechanism does NVMe use? | MSI-X (Message Signaled Interrupts — Extended) |

---

## DECK 2: REGISTERS & CONTROLLER (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Offset of CAP register? | 0x00 (8 bytes) |
| 2 | What does CAP contain? | Max Queue Entries, Timeout, Doorbell Stride, supported command sets |
| 3 | Offset of CC (Controller Configuration)? | 0x14 |
| 4 | What does CC.EN do? | Enables controller (set to 1 to start) |
| 5 | What does CC.SHN control? | Shutdown Notification (01=normal, 10=abrupt) |
| 6 | Offset of CSTS (Controller Status)? | 0x1C |
| 7 | What does CSTS.RDY indicate? | Controller ready (1) or not ready (0) |
| 8 | What does CSTS.CFS indicate? | Controller Fatal Status — unrecoverable error |
| 9 | Offset of AQA? | 0x24 — Admin Queue Attributes (sizes) |
| 10 | Offset of ASQ? | 0x28 — Admin Submission Queue base address (64-bit) |
| 11 | Offset of ACQ? | 0x30 — Admin Completion Queue base address (64-bit) |
| 12 | Doorbell registers start at? | Offset 0x1000 |
| 13 | Formula for SQy Tail Doorbell offset? | 0x1000 + (2y × (4 << CAP.DSTRD)) |
| 14 | Formula for CQy Head Doorbell offset? | 0x1000 + ((2y+1) × (4 << CAP.DSTRD)) |
| 15 | What is CAP.DSTRD? | Doorbell Stride (typically 0 → 4 bytes) |
| 16 | What is CAP.TO? | Timeout — max wait for CSTS.RDY (in 500ms units) |
| 17 | What is CAP.MQES? | Max Queue Entries Supported (0-based) |
| 18 | What is CC.MPS? | Memory Page Size: page_size = 2^(12+MPS) |
| 19 | What is CC.AMS? | Arbitration Mechanism Selected (RR, WRR, vendor) |
| 20 | What is NSSR register for? | NVM Subsystem Reset (write 0x4E564D65 to reset) |

---

## DECK 3: COMMANDS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Admin Opcode for Identify? | 0x06 |
| 2 | Admin Opcode for Create IO SQ? | 0x01 |
| 3 | Admin Opcode for Create IO CQ? | 0x05 |
| 4 | Admin Opcode for Set Features? | 0x09 |
| 5 | Admin Opcode for Get Features? | 0x0A |
| 6 | Admin Opcode for Firmware Download? | 0x11 |
| 7 | Admin Opcode for Format NVM? | 0x14 |
| 8 | Admin Opcode for Sanitize? | 0x84 |
| 9 | IO Opcode for Read? | 0x02 |
| 10 | IO Opcode for Write? | 0x01 |
| 11 | IO Opcode for Flush? | 0x00 |
| 12 | IO Opcode for Dataset Management? | 0x09 |
| 13 | IO Opcode for Write Zeroes? | 0x08 |
| 14 | IO Opcode for Compare? | 0x05 |
| 15 | What is Identify CNS=0? | Identify Namespace structure |
| 16 | What is Identify CNS=1? | Identify Controller structure |
| 17 | What is Identify CNS=2? | Active Namespace ID list |
| 18 | What does FUA bit do in Read/Write? | Force Unit Access — bypass volatile write cache |
| 19 | What does AER (Async Event Request) do? | Registers for async notifications (health, error, vendor) |
| 20 | Max ranges in Dataset Management cmd? | 256 LBA ranges per command |

---

## DECK 4: DATA TRANSFER & DMA (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | What does PRP stand for? | Physical Region Page |
| 2 | What does SGL stand for? | Scatter-Gather List |
| 3 | PRP entry size? | 8 bytes (64-bit physical address) |
| 4 | SGL descriptor size? | 16 bytes |
| 5 | When does PRP2 point to a PRP List? | When transfer spans more than 2 pages |
| 6 | PRP alignment requirement? | Page-aligned (except offset in first page) |
| 7 | Which uses PRP: local or NVMe-oF? | Local PCIe NVMe uses PRP |
| 8 | Which uses SGL: local or NVMe-oF? | NVMe-oF (TCP, RDMA) requires SGL |
| 9 | What is MDTS? | Max Data Transfer Size (2^MDTS × MPS bytes) |
| 10 | If MDTS=5, MPS=4KB, max transfer? | 2^5 × 4KB = 128KB |
| 11 | What happens if IO > MDTS? | Driver must split into multiple commands |
| 12 | How does controller get SQE data? | DMA read from host memory (PCIe MRd TLP) |
| 13 | How does controller send read data? | DMA write to host PRP address (PCIe MWr TLP) |
| 14 | How does controller post completion? | DMA write 16B CQE to host CQ address |
| 15 | What is CMB? | Controller Memory Buffer — device-side memory host can use |
| 16 | What can host put in CMB? | SQs, PRP Lists, SGLs (reduces PCIe traffic) |
| 17 | What is PMR? | Persistent Memory Region — non-volatile device memory |
| 18 | PMR survives what? | Power loss (persistent) |
| 19 | SGL types include? | Data Block, Segment, Last Segment, Keyed SGL |
| 20 | PSDT field in SQE indicates? | PRP or SGL usage (00=PRP, 01/10=SGL) |

---

## DECK 5: QUEUES & ARBITRATION (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Who creates IO queues? | Host driver (via Admin commands after controller enable) |
| 2 | Must create CQ or SQ first? | CQ first (SQ references its associated CQ) |
| 3 | How many SQs can map to one CQ? | Multiple SQs can share one CQ |
| 4 | Typical Linux queue mapping? | 1 SQ + 1 CQ per CPU core |
| 5 | What is Round Robin arbitration? | All queues served equally in rotation |
| 6 | What is WRR? | Weighted Round Robin — priority classes with weights |
| 7 | WRR priority classes? | Urgent, High, Medium, Low |
| 8 | Urgent priority behavior? | Always served first (preempts others) |
| 9 | Feature ID for Number of Queues? | 0x07 |
| 10 | Feature ID for Interrupt Coalescing? | 0x08 |
| 11 | Feature ID for Arbitration? | 0x01 |
| 12 | Interrupt coalescing threshold? | Min completions before interrupt fires |
| 13 | Interrupt coalescing time? | Max wait before interrupt (100μs units) |
| 14 | What is io_poll / polling mode? | Host polls CQ directly (no interrupts) for lowest latency |
| 15 | Polling saves how much latency? | ~1-2 μs (interrupt delivery overhead) |
| 16 | SQ Head reported where? | In CQE (bytes 8-9) — flow control for host |
| 17 | Why does host need SQ Head from CQE? | To know how much SQ space is free (can submit more) |
| 18 | What is SQ Tail wrap? | When tail reaches end of ring, wraps to 0 |
| 19 | Queue full condition? | Tail + 1 == Head (one slot always empty) |
| 20 | blk-mq tag purpose? | Unique ID per in-flight IO; maps to CID in SQE/CQE |

---

## DECK 6: POWER & THERMAL (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | PS0 is what? | Maximum performance power state |
| 2 | Operational power states? | PS0-PS2 (zero entry/exit latency) |
| 3 | Non-operational power states? | PS3+ (device idle, significant wake latency) |
| 4 | What is APST? | Autonomous Power State Transition — auto idle → low power |
| 5 | Feature ID for APST? | 0x0C |
| 6 | How to disable APST? | Set Feature 0x0C value = 0 |
| 7 | CC.SHN=01 means? | Normal Shutdown Notification |
| 8 | CC.SHN=10 means? | Abrupt Shutdown Notification |
| 9 | CSTS.SHST=10b means? | Shutdown processing complete |
| 10 | What is WCTEMP? | Warning Composite Temperature Threshold |
| 11 | What is CCTEMP? | Critical Composite Temperature Threshold |
| 12 | Feature 0x10 is? | Host Controlled Thermal Management (TMT1, TMT2) |
| 13 | TMT1 triggers? | Light thermal throttle |
| 14 | TMT2 triggers? | Heavy thermal throttle |
| 15 | Automotive NVMe temp range? | -40°C to +105°C (AEC-Q100 grade) |
| 16 | Effect of thermal throttle? | Reduced write/read bandwidth, increased latency |
| 17 | How to detect throttling? | SMART temperature + performance drop correlation |
| 18 | NVMe typical active power (SSD)? | 5-25W |
| 19 | NVMe idle power (PS3/PS4)? | 5-50 mW |
| 20 | Automotive: disable deep sleep when? | During active driving (data logging needs instant response) |

---

## DECK 7: NAMESPACES & FEATURES (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | NSID range? | 1 to 0xFFFFFFFE (0 and 0xFFFFFFFF are special) |
| 2 | NSID=0xFFFFFFFF means? | Broadcast (applies to all namespaces) |
| 3 | NSZE field? | Namespace Size (total LBAs) |
| 4 | NCAP field? | Namespace Capacity (usable LBAs, thin provisioning) |
| 5 | FLBAS field? | Formatted LBA Size index (selects from LBA Format table) |
| 6 | Typical LBA sizes? | 512 bytes or 4096 bytes (4KB) |
| 7 | Can namespaces be shared? | Yes — between multiple controllers (if supported) |
| 8 | Namespace Management commands? | Create, Delete, Attach, Detach |
| 9 | What is a ZNS namespace? | Zoned Namespace — sequential write zones |
| 10 | Zone write requirement? | Must write sequentially within a zone |
| 11 | Zone states? | Empty, Implicitly Open, Explicitly Open, Closed, Full, Read-Only, Offline |
| 12 | ZNS benefit? | WAF≈1.0, no device GC, predictable latency |
| 13 | What is the KV command set? | Key-Value storage (native key-value interface on NVMe) |
| 14 | What is an Endurance Group? | Set of NAND sharing a common wear pool |
| 15 | Sanitize operations? | Block Erase, Overwrite, Crypto Erase |
| 16 | Format NVM does what? | Low-level format: change LBA size, erase data |
| 17 | Reservation types? | Write Exclusive, Exclusive Access, and registrant-only variants |
| 18 | Reservations used for? | Multi-host access coordination (cluster storage) |
| 19 | What is a Boot Partition (NVMe 1.3)? | Dedicated partition for firmware/bootloader storage |
| 20 | SR-IOV in NVMe? | Virtual Functions — share one SSD across VMs |

---

## DECK 8: LINUX & ANDROID (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Main NVMe driver file (PCIe)? | drivers/nvme/host/pci.c |
| 2 | NVMe core file? | drivers/nvme/host/core.c |
| 3 | Character device for controller? | /dev/nvme0 (admin commands via ioctl) |
| 4 | Block device for namespace? | /dev/nvme0n1 |
| 5 | Kernel config for NVMe? | CONFIG_BLK_DEV_NVME=y |
| 6 | NVMe multipath config? | CONFIG_NVME_MULTIPATH=y |
| 7 | Best IO scheduler for NVMe? | none (noop) — blk-mq already does per-CPU queuing |
| 8 | How to check PCIe link speed? | lspci -vv | grep LnkSta |
| 9 | How to read SMART data? | nvme smart-log /dev/nvme0 |
| 10 | How to identify controller? | nvme id-ctrl /dev/nvme0 |
| 11 | How to identify namespace? | nvme id-ns /dev/nvme0n1 |
| 12 | How to list namespaces? | nvme list |
| 13 | How to format namespace? | nvme format /dev/nvme0n1 --lbaf=1 |
| 14 | How to run sanitize? | nvme sanitize /dev/nvme0 --sanact=4 |
| 15 | How to update firmware? | nvme fw-download + nvme fw-commit |
| 16 | NVMe in Android block layer? | Same as Linux: blk-mq → nvme-pci |
| 17 | Android TRIM for NVMe? | fstrim via Vold (same as eMMC/UFS) |
| 18 | io_uring with NVMe? | Supported — fixed buffers + SQPOLL for lowest latency |
| 19 | NVMe hwmon interface? | /sys/class/hwmon/hwmonX/temp1_input (temperature) |
| 20 | nvme_reset_ctrl() does what? | Controller reset: CC.EN=0, wait, reconfigure, CC.EN=1 |

---

## DECK 9: AUTOMOTIVE & DEBUG (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | NVMe automotive form factor? | BGA (soldered) or M.2 with retention bracket |
| 2 | PCIe Gen3 x4 bandwidth? | ~3.9 GB/s (effective, ~32 Gbps raw) |
| 3 | PCIe Gen4 x4 bandwidth? | ~7.8 GB/s (effective, ~64 Gbps raw) |
| 4 | NVMe boot time overhead vs eMMC? | 200-500ms additional (PCIe enumeration + init) |
| 5 | How to check if NVMe detected? | lspci | grep "Non-Volatile"; lsblk | grep nvme |
| 6 | Link downgrade symptom? | Low performance; check lspci LnkSta vs LnkCap |
| 7 | PERST# signal purpose? | PCIe fundamental reset (active low) |
| 8 | Unsafe shutdown count from? | SMART log offset 144-159 |
| 9 | Available spare meaning? | Percentage of spare NAND blocks remaining |
| 10 | Percentage used meaning? | Endurance consumed; >100% means exceeded TBW |
| 11 | Media errors = 0 means? | No unrecoverable read errors (healthy) |
| 12 | AER stands for? | Asynchronous Event Request (background notifications) |
| 13 | Common AER events? | Health status change, temperature threshold, firmware update |
| 14 | How to check NVMe errors? | nvme error-log /dev/nvme0 |
| 15 | Controller fatal recovery? | CC.EN=0, wait RDY=0, reconfigure, CC.EN=1 |
| 16 | NVMe vs UFS for Android boot? | UFS preferred (faster init, native boot) |
| 17 | NVMe best for in automotive? | High-speed logging, maps, AI models, OTA staging |
| 18 | Deallocate (TRIM) performance impact? | Returns blocks to free pool → better GC → sustained write perf |
| 19 | Write cache flush frequency? | On fsync/fdatasync; FUA bypasses cache per-IO |
| 20 | ZNS for data logger advantage? | WAF=1.0, no GC, predictable latency, more capacity |

---

END OF DOCUMENT 04 — FLASHCARDS
