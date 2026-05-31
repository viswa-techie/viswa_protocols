# MIPI CSI-2 (Camera Serial Interface 2) — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 01 of 08
# Scope: Complete protocol theory — PHY to application
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS MIPI CSI-2?

MIPI CSI-2 (Camera Serial Interface 2) is the dominant high-speed serial interface for connecting image sensors to application processors. Defined by the MIPI Alliance, it provides a standardized way to transfer pixel data from camera modules to SoC image processing pipelines.

**Why CSI-2 exists:**
- Cameras generate massive data: 4K@30fps RAW12 = 3840×2160×12×30 = ~3 Gbps
- Parallel interfaces (8/10/12 bit buses) need too many pins and emit EMI
- Need: Low pin count, high bandwidth, low power, standardized
- CSI-2 delivers: 4 lanes × 2.5 Gbps = 10 Gbps in just 10 wires (D-PHY)

**Market dominance:**
- Used in ~100% of smartphone cameras
- Standard for automotive camera systems (ADAS, surround view)
- Laptops, tablets, IoT, drones, medical imaging
- Supported by every major SoC vendor (Qualcomm, Samsung, MediaTek, Intel, TI, NXP)

---

## 2. PROTOCOL STACK & LAYERS

```
┌─────────────────────────────────────────────────────────┐
│ Application Layer                                        │
│ (Camera HAL, V4L2 driver, ISP pipeline control)         │
├─────────────────────────────────────────────────────────┤
│ CSI-2 Protocol Layer (Packet-based)                      │
│ • Packet Header (DI + WC + ECC)                         │
│ • Pixel Data Payload (various data types)               │
│ • Packet Footer (CRC-16)                                │
│ • Frame/Line synchronization (FS/FE/LS/LE)             │
│ • Virtual Channels (logical multiplexing)              │
├─────────────────────────────────────────────────────────┤
│ Lane Management Layer                                    │
│ • Lane merging/distribution                             │
│ • Byte-to-lane mapping                                  │
│ • Lane synchronization (SoT alignment)                 │
├─────────────────────────────────────────────────────────┤
│ Physical Layer (D-PHY or C-PHY)                          │
│ • High-Speed (HS) burst transmission                    │
│ • Low-Power (LP) signaling & escape mode               │
│ • Clock generation (D-PHY) / embedded clock (C-PHY)    │
│ • SoT/EoT sequences                                    │
└─────────────────────────────────────────────────────────┘
```

---

## 3. D-PHY PHYSICAL LAYER

### 3.1 Signaling Overview

D-PHY uses differential signaling for high-speed data with a dedicated clock lane:

```
Wires per lane:  Dp (positive), Dn (negative) — differential pair
Clock lane:      CLK_P, CLK_N — DDR clock (data valid on both edges)
Data lanes:      D0_P/D0_N, D1_P/D1_N, D2_P/D2_N, D3_P/D3_N (up to 4)
Total wires:     2 (CLK) + 2×N (Data) = 2 + 2×4 = 10 wires max
```

### 3.2 Operating Modes

| Mode | Voltage | Speed | Purpose |
|------|---------|-------|---------|
| HS (High-Speed) | 200 mV differential swing | 80 Mbps – 4.5 Gbps/lane | Pixel data transfer |
| LP (Low-Power) | 0V / 1.2V single-ended | ~10 Mbps | Control, state transitions |

### 3.3 D-PHY State Machine

```
LP-11 (Stop) → LP-01 (HS-Request) → LP-00 (Bridge) → [HS-0 Sync] → HS Data Burst
     ↑                                                                      │
     └──────────────────────── EoT (End of Transmission) ──────────────────┘

LP States:
  LP-11: Stop/Idle (both lines HIGH — 1.2V)
  LP-01: HS-Request (Dp=0, Dn=1.2V) — requesting HS mode
  LP-00: Bridge (both LOW) — transition into HS mode
  LP-10: Escape mode request / Turnaround

HS Entry Sequence (SoT):
  1. Start in LP-11 (idle)
  2. Drive LP-01 (HS-Request) — TREQ ≥ 40ns
  3. Drive LP-00 (Bridge) — TLP-00 ≥ 38ns
  4. Transition to HS-0 (preamble) — receiver starts CDR
  5. Send Sync byte (0xB8) on each data lane
  6. Begin HS data burst

HS Exit Sequence (EoT):
  1. Last valid HS data
  2. Drive LP-11 (return to Stop state)
  3. TEOT ≥ 2 UI
```

### 3.4 D-PHY Timing Parameters

| Parameter | Symbol | Min | Max | Notes |
|-----------|--------|-----|-----|-------|
| HS data rate | — | 80 Mbps | 4500 Mbps | Per lane |
| HS prepare time | T_HS-PREPARE | 40 ns + 4×UI | 85 ns + 6×UI | Before HS-0 |
| HS zero time | T_HS-ZERO | 145 ns + 10×UI | — | Preamble duration |
| HS trail time | T_HS-TRAIL | max(8×UI, 60ns+4×UI) | — | After last bit |
| Clock prepare | T_CLK-PREPARE | 38 ns | 95 ns | Clock lane HS entry |
| LP-11 stop time | T_STOP | 100 ns | — | Minimum stop duration |
| Receiver settle | T_HS-SETTLE | 85 ns + 6×UI | 145 ns + 10×UI | RX CDR lock time |

*UI = Unit Interval = 1/data_rate (e.g., at 2 Gbps, UI = 500 ps)*

### 3.5 Clock Lane

D-PHY clock lane provides the bit clock for data recovery:
- **DDR clock**: Data sampled on BOTH rising and falling edges
- Clock runs continuously during HS burst (or can be gated between packets)
- Clock frequency = data_rate / 2 (e.g., 2 Gbps data → 1 GHz clock)
- Clock must be stable before data lanes enter HS (T_CLK-PRE)

---

## 4. C-PHY PHYSICAL LAYER

### 4.1 Overview

C-PHY is an alternative PHY using 3-wire "trios" instead of differential pairs:

```
D-PHY: 2 wires per lane + dedicated clock = 10 wires for 4 lanes
C-PHY: 3 wires per trio, NO dedicated clock = 9 wires for 3 trios
       Higher efficiency: 2.28 bits/wire/symbol (vs 1 bit/wire/bit for D-PHY)
```

### 4.2 Tri-Level Signaling

Each trio uses 3 wires (A, B, C) with 6 possible states (3! permutations of which wire is high/mid/low):
- Each transition encodes log₂(5) ≈ 2.322 bits (only 5 valid transitions from any state)
- Symbol rate measured in Gsps (Giga-symbols per second)
- Bit rate = Symbol rate × 2.28 bits/symbol × 16/7 (encoding overhead)

### 4.3 C-PHY vs D-PHY Comparison

| Aspect | D-PHY | C-PHY |
|--------|-------|-------|
| Wires per lane | 2 (differential pair) | 3 (trio) |
| Clock | Separate clock lane | Embedded in data transitions |
| Bandwidth efficiency | 1 bit/wire/UI | 2.28 bits/wire/symbol |
| Max lanes/trios | 4 lanes | 3 trios |
| Max wires (data only) | 8 + 2(clk) = 10 | 9 |
| Throughput (max config) | 4×4.5 = 18 Gbps | 3×6.0×2.28×16/7 ≈ 41 Gbps |
| EMI | Lower (differential) | Higher (needs more care) |
| Pin count for same BW | More | Fewer |
| Typical use | Mobile camera | High-BW camera, where pins are very limited |

---

## 5. CSI-2 PACKET STRUCTURE

### 5.1 Packet Types

Two packet types in CSI-2:

**Short Packet (4 bytes):** Header only — no payload, no CRC
```
┌──────────┬──────────────────┬─────────┐
│ Data ID  │ Short Pkt Data   │  ECC    │
│ (1 byte) │ (2 bytes)        │ (1 byte)│
└──────────┴──────────────────┴─────────┘
  VC[1:0]+DT[5:0]  Frame/Line number    6-bit Hamming
```

**Long Packet (4 + N + 2 bytes):** Carries pixel data
```
┌──────────┬──────────────────┬─────────┬─────────────────────┬─────────┐
│ Data ID  │ Word Count (WC)  │  ECC    │    Data Payload     │  CRC    │
│ (1 byte) │ (2 bytes)        │ (1 byte)│ (WC bytes)          │ (2 bytes│
└──────────┴──────────────────┴─────────┴─────────────────────┴─────────┘
  VC[1:0]+DT[5:0]  Payload length       Pixel data/embedded     CRC-16
                   (0 to 65535 bytes)
```

### 5.2 Data ID (DI) Field

```
Bit 7  Bit 6  Bit 5  Bit 4  Bit 3  Bit 2  Bit 1  Bit 0
[VC1] [VC0] [ DT5 ] [ DT4 ] [ DT3 ] [ DT2 ] [ DT1 ] [ DT0 ]
  Virtual      Data Type (6 bits = 64 possible types)
  Channel
```

### 5.3 ECC (Error Correcting Code)

- 6-bit modified Hamming code over the 24-bit header (DI + WC/Short Data)
- Can **correct** any single-bit error in the header
- Can **detect** any 2-bit error in the header
- Ensures packet routing (VC/DT) and length (WC) are reliable

### 5.4 CRC-16

- Applied to long packet payload only
- Polynomial: x¹⁶ + x¹² + x⁵ + 1 (CRC-CCITT)
- Detects burst errors up to 16 bits in payload
- If CRC mismatch → line data corrupted (pixel errors visible)

---

## 6. DATA TYPES

### 6.1 Synchronization (Short Packets)

| DT Code | Name | Description |
|---------|------|-------------|
| 0x00 | Frame Start (FS) | Start of frame; Short Data = Frame Number |
| 0x01 | Frame End (FE) | End of frame; Short Data = Frame Number |
| 0x02 | Line Start (LS) | Optional line start marker |
| 0x03 | Line End (LE) | Optional line end marker |
| 0x04–0x07 | Reserved | — |

### 6.2 Image Data (Long Packets)

| DT Code | Format | BPP | Description |
|---------|--------|-----|-------------|
| 0x08 | Generic 8-bit | 8 | Non-image data |
| 0x0A | Null | — | Padding/blanking fill |
| 0x10 | Embedded 8-bit | 8 | Embedded data (sensor metadata) |
| 0x12 | YUV420 8-bit | 12 | Legacy format |
| 0x18 | YUV420 8-bit (CSPS) | 12 | Chroma-shifted |
| 0x1A | YUV420 10-bit (CSPS) | 15 | 10-bit YUV420 |
| 0x1E | YUV422 8-bit | 16 | **Common**: UYVY interleaved |
| 0x1F | YUV422 10-bit | 20 | 10-bit YUV422 |
| 0x22 | RGB565 | 16 | Legacy display |
| 0x24 | RGB888 | 24 | Full color RGB |
| 0x28 | RAW6 | 6 | 6-bit Bayer raw |
| 0x29 | RAW7 | 7 | 7-bit Bayer raw |
| 0x2A | RAW8 | 8 | 8-bit Bayer raw |
| 0x2B | RAW10 | 10 | **Most common**: 10-bit Bayer |
| 0x2C | RAW12 | 12 | **Common**: 12-bit Bayer (HDR sensors) |
| 0x2D | RAW14 | 14 | 14-bit Bayer (high-end) |
| 0x2E | RAW16 | 16 | 16-bit raw |
| 0x2F | RAW20 | 20 | 20-bit raw (CSI-2 v2.0+) |

### 6.3 RAW10 Packing

Most common format — 4 pixels packed into 5 bytes:
```
Pixel:    P0[9:0]    P1[9:0]    P2[9:0]    P3[9:0]
Byte 0:   P0[9:2]
Byte 1:   P1[9:2]
Byte 2:   P2[9:2]
Byte 3:   P3[9:2]
Byte 4:   P3[1:0] | P2[1:0] | P1[1:0] | P0[1:0]
           [7:6]     [5:4]     [3:2]     [1:0]

→ 4 pixels × 10 bits = 40 bits = 5 bytes (compact, no waste)
```

---

## 7. FRAME TRANSMISSION SEQUENCE

### 7.1 Single-Frame Flow

```
Time →

LP-11 (idle) │ SoT │ FS(frame_num) │ EoT │ LP-11
LP-11 (idle) │ SoT │ [Line 0 pixel data (Long Packet)] │ EoT │ LP-11
LP-11 (idle) │ SoT │ [Line 1 pixel data (Long Packet)] │ EoT │ LP-11
     ...
LP-11 (idle) │ SoT │ [Line N-1 pixel data (Long Packet)] │ EoT │ LP-11
LP-11 (idle) │ SoT │ FE(frame_num) │ EoT │ LP-11

Where each line long packet:
│ PH(DI=RAW10, WC=line_bytes) │ pixel_data[WC bytes] │ CRC │
```

### 7.2 Timing Diagram

```
              Frame Blanking (VBI)
              ←───────────────────→
Frame N:  ┌─────────────────────────────────────────────┐
          │FS│Line0│Line1│...│LineM│FE│    VBI    │FS│...
          └─────────────────────────────────────────────┘
               ←── Active Frame ──→   ←Blanking→

Within each line:
          │ Line Data (Long Pkt) │ HBI │ Line Data │ HBI │
          ←── Horizontal Active ──→←HB→←── Next Line ──→
```

### 7.3 Embedded Data

Sensors send metadata in blanking periods:
```
FS → Embedded Data Lines (DT=0x12) → Image Lines (DT=RAW10) → FE

Embedded data contains:
• Exposure time (current frame)
• Analog/digital gain
• Frame counter
• Sensor temperature
• Register dump (sensor configuration snapshot)
• Statistics (histogram, zone averages)
• PDAF data (phase-detect AF pixels)
```

---

## 8. VIRTUAL CHANNELS

### 8.1 Concept

Virtual Channels (VC) allow multiplexing multiple independent streams on the same physical lanes:

```
Physical lanes: ────────────────────────────────────────
  VC 0: Main camera RGB/RAW stream
  VC 1: HDR short-exposure frame
  VC 2: Embedded data / metadata
  VC 3: PDAF data

All share the same D-PHY lanes, distinguished by VC bits in Data ID
```

### 8.2 Use Cases

| VC Configuration | Application |
|-----------------|-------------|
| 1 VC (default) | Simple single-stream camera |
| 2 VC (VC0 + VC1) | DOL-HDR: Long exposure + Short exposure |
| 3 VC (VC0-2) | Staggered HDR: Long + Medium + Short |
| 2 VC (VC0 + VC2) | Image + embedded data (separate VC) |
| 4 VC (VC0-3) | Multi-sensor on single interface (e.g., stereo pair) |

### 8.3 Extended Virtual Channels (CSI-2 v3.0+)

Original: 2-bit VC field → 4 VCs (0-3)
Extended: Additional VC bits via "Extension" packet → up to 32 VCs

---

## 9. BANDWIDTH CALCULATION

### 9.1 Formula

```
Required_BW = Width × Height × BPP × FPS × (1 + Blanking_Overhead)

Where:
  Width, Height = image resolution
  BPP = bits per pixel (RAW10=10, RAW12=12, YUV422=16)
  FPS = frames per second
  Blanking_Overhead = typically 10-20% (HBI + VBI)
```

### 9.2 Example Calculations

| Camera | Resolution | Format | FPS | Raw BW | With 15% overhead |
|--------|-----------|--------|-----|--------|-------------------|
| Rear view | 1920×1080 | RAW10 | 30 | 622 Mbps | 715 Mbps |
| Surround (each) | 1280×960 | RAW12 | 30 | 442 Mbps | 509 Mbps |
| Front ADAS | 3840×2160 | RAW12 | 30 | 2.98 Gbps | 3.43 Gbps |
| DMS | 1920×1080 | RAW10 | 30 | 622 Mbps | 715 Mbps |
| 8MP HDR | 3840×2160 | RAW12×2VC | 30 | 5.97 Gbps | 6.86 Gbps |

### 9.3 Lane Configuration

```
Available BW per config:
  1 lane D-PHY @ 2.5 Gbps  = 2.5 Gbps
  2 lane D-PHY @ 2.5 Gbps  = 5.0 Gbps
  4 lane D-PHY @ 2.5 Gbps  = 10.0 Gbps
  2 lane D-PHY @ 1.5 Gbps  = 3.0 Gbps (common automotive)
  4 lane D-PHY @ 1.5 Gbps  = 6.0 Gbps

→ Rear view (715 Mbps): 1 lane @ 1.5 Gbps ✓ (47% utilization)
→ Surround (509 Mbps): 1 lane @ 1.5 Gbps ✓ (34% utilization)
→ Front ADAS (3.43 Gbps): 2 lane @ 2.5 Gbps ✓ (69% utilization)
→ 8MP HDR (6.86 Gbps): 4 lane @ 2.5 Gbps ✓ (69% utilization)
```

---

## 10. AUTOMOTIVE CAMERA ARCHITECTURE

### 10.1 Direct-Attach vs SerDes

**Direct-attach CSI-2** (short distance, <15 cm):
- Camera sensor directly connected to SoC CSI-2 port
- Use case: DMS (driver monitoring), interior cameras
- Simple, low latency, lowest cost
- Limited by cable length (~15 cm max for D-PHY at high speeds)

**SerDes-extended CSI-2** (long distance, up to 15 m):
- Camera → Serializer (GMSL2/FPD-Link) → Coax/STP → Deserializer → SoC CSI-2
- Use case: All exterior cameras (front, rear, surround, e-mirror)
- Enables long cable runs (roof-mounted to trunk SoC)
- Adds latency (~1-2 µs) but negligible for 30 fps cameras

### 10.2 SerDes Technologies

| Technology | Vendor | Max BW | Cable | Max Length | CSI-2 Support |
|-----------|--------|--------|-------|-----------|---------------|
| GMSL2 | Maxim/ADI (MAX96717/MAX96712) | 6 Gbps | Coax (50Ω) | 15 m | 4-lane CSI-2 output |
| FPD-Link III | TI (DS90UB953/960) | 4.16 Gbps | STP/Coax | 15 m | 4-lane CSI-2 output |
| FPD-Link IV | TI (DS90UB971/972) | 8.4 Gbps | STP/Coax | 15 m | 4-lane CSI-2 output |
| GMSL3 | ADI (future) | 12+ Gbps | Coax | 15 m | Enhanced |

### 10.3 SA8295P Multi-Camera Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│ SA8295P Camera Subsystem                                          │
│                                                                    │
│  CSI-2 Port 0 ← MAX96712 (4:1 deserializer) ← 4× Surround cams │
│  CSI-2 Port 1 ← MAX96712 (4:1 deserializer) ← Front + Rear + 2  │
│  CSI-2 Port 2 ← Direct-attach DMS camera                         │
│  CSI-2 Port 3 ← Direct-attach interior camera                    │
│  CSI-2 Port 4-6 ← Additional cameras / future expansion         │
│                                                                    │
│  Each port → CSIPHY → CSID → IFE (ISP) → DDR / GPU / NPU       │
│                                                                    │
│  Concurrent: Up to 16 cameras via VCs + ports                    │
└──────────────────────────────────────────────────────────────────┘
```

---

## 11. HDR (HIGH DYNAMIC RANGE) OVER CSI-2

### 11.1 Why HDR in Automotive

Automotive scenes have extreme dynamic range:
- Tunnel exit: Dark interior + bright exterior (>120 dB needed)
- Night: Headlights + dark road
- Standard camera: ~60 dB → HDR sensor: 100-140 dB

### 11.2 HDR Methods via CSI-2

**DOL-HDR (Digital Overlap):**
```
VC0: Long exposure line (1/30s)     → captures dark areas
VC1: Short exposure line (1/1000s)  → captures bright areas
VC2: Very short (1/10000s)          → captures headlights

Lines interleaved in readout:
  L0_long, L0_short, L0_vshort, L1_long, L1_short, L1_vshort, ...

Benefit: Near-simultaneous capture → less motion artifact
Cost: 2-3× bandwidth (multiple streams)
```

**Staggered HDR:**
```
Frame N:   Long exposure frame  (VC0, FS→FE)
Frame N+1: Short exposure frame (VC1, FS→FE)

Benefit: Simple, no sensor modification needed
Cost: Temporal offset between exposures → motion ghosting
```

**Sensor-fused HDR:**
```
Sensor internally combines exposures → outputs single HDR stream
Single VC, standard data type (RAW12/16)

Benefit: No extra bandwidth, simple ISP
Cost: Less ISP control over fusion algorithm
```

---

## 12. QUALCOMM CAMERA PROCESSING PIPELINE

### 12.1 Hardware Blocks

```
Camera Sensor → [CSIPHY] → [CSID] → [IFE] → [BPS] → [IPE] → DDR/Display

CSIPHY: PHY-level deserialization (D-PHY/C-PHY receiver)
  - Lane synchronization (SoT detection)
  - Symbol recovery, 8b/10b decoding
  - HS receiver with programmable settle time

CSID: CSI Decoder
  - Packet parsing (PH verification, ECC check)
  - CRC validation
  - VC demultiplexing
  - DT routing to appropriate pipeline
  - Frame/line counter

IFE: Image Front End (Real-time ISP)
  - Demosaic (Bayer → RGB)
  - Black level correction
  - Lens shading correction
  - White balance
  - Color correction
  - Gamma/tone mapping
  - Statistics (3A: AE, AF, AWB)
  - Output: Full resolution to DDR or BPS

BPS: Bayer Processing Segment (Offline ISP)
  - Pedestal removal
  - Linearization
  - Hot pixel correction
  - Noise reduction (Bayer domain)
  - Output: Full Bayer or partially processed to IPE

IPE: Image Processing Engine (Offline ISP)
  - Noise reduction (YUV domain)
  - Multi-frame noise reduction (MFNR)
  - Face detection
  - HDR merge
  - Output: Final YUV/NV12 to DDR
```

### 12.2 Processing Paths

```
Path 1: Real-time preview/video (IFE → DDR → Display/Encoder)
Path 2: Snapshot (IFE → BPS → IPE → DDR → JPEG encoder)
Path 3: RAW dump (CSID → DDR — bypass ISP for debug/tuning)
Path 4: ADAS (IFE → DDR → NPU/GPU for object detection)
```

---

## 13. LINUX KERNEL CAMERA SUBSYSTEM

### 13.1 V4L2 + Media Controller Architecture

```
Userspace:
  ┌──────────────────────────────────────────────┐
  │ Camera HAL / V4L2 application               │
  │ open(/dev/video0) → ioctl(VIDIOC_*) → mmap │
  └──────────────────────────┬───────────────────┘
                             │ ioctl
Kernel:
  ┌──────────────────────────▼───────────────────┐
  │ V4L2 Framework (videobuf2)                    │
  │ /dev/video0..N, /dev/v4l-subdev0..M          │
  ├──────────────────────────────────────────────┤
  │ Media Controller (/dev/media0)                │
  │ (Pipeline topology: pad links, formats)      │
  ├──────────────────────────────────────────────┤
  │ Subdevice drivers:                            │
  │ ┌────────┐ ┌──────┐ ┌─────┐ ┌───────────┐  │
  │ │Sensor  │→│CSIPHY│→│CSID │→│IFE/VFE    │  │
  │ │(I2C)   │ │      │ │     │ │           │  │
  │ └────────┘ └──────┘ └─────┘ └───────────┘  │
  ├──────────────────────────────────────────────┤
  │ Platform driver (DT/ACPI bindings)            │
  └──────────────────────────────────────────────┘
```

### 13.2 Key V4L2 Operations (Camera Capture)

```c
// 1. Open device
int fd = open("/dev/video0", O_RDWR);

// 2. Query capabilities
struct v4l2_capability cap;
ioctl(fd, VIDIOC_QUERYCAP, &cap);

// 3. Set format
struct v4l2_format fmt;
fmt.type = V4L2_BUF_TYPE_VIDEO_CAPTURE_MPLANE;
fmt.fmt.pix_mp.width = 1920;
fmt.fmt.pix_mp.height = 1080;
fmt.fmt.pix_mp.pixelformat = V4L2_PIX_FMT_NV12;
ioctl(fd, VIDIOC_S_FMT, &fmt);

// 4. Request buffers
struct v4l2_requestbuffers req;
req.count = 4;
req.type = V4L2_BUF_TYPE_VIDEO_CAPTURE_MPLANE;
req.memory = V4L2_MEMORY_MMAP;
ioctl(fd, VIDIOC_REQBUFS, &req);

// 5. Queue buffers
struct v4l2_buffer buf;
for (int i = 0; i < 4; i++) {
    buf.index = i;
    ioctl(fd, VIDIOC_QBUF, &buf);
}

// 6. Start streaming
ioctl(fd, VIDIOC_STREAMON, &type);

// 7. Dequeue filled buffer (frame ready)
ioctl(fd, VIDIOC_DQBUF, &buf);
// Process frame at buffer[buf.index]

// 8. Re-queue buffer
ioctl(fd, VIDIOC_QBUF, &buf);
```

### 13.3 Media Controller Pipeline Setup

```bash
# List all entities in camera pipeline
media-ctl -d /dev/media0 --print-topology

# Configure sensor output format
media-ctl -d /dev/media0 --set-v4l2 '"imx577 2-001a":0[fmt:SRGGB10_1X10/3840x2160]'

# Configure CSIPHY
media-ctl -d /dev/media0 --set-v4l2 '"msm_csiphy0":1[fmt:SRGGB10_1X10/3840x2160]'

# Link sensor → CSIPHY → CSID → VFE
media-ctl -d /dev/media0 -l '"imx577 2-001a":0->"msm_csiphy0":0[1]'
media-ctl -d /dev/media0 -l '"msm_csiphy0":1->"msm_csid0":0[1]'
media-ctl -d /dev/media0 -l '"msm_csid0":1->"msm_vfe0":0[1]'
```

---

## 14. ANDROID CAMERA ARCHITECTURE

### 14.1 Full Stack

```
┌─────────────────────────────────────────────────────┐
│ App Layer: CameraX / Camera2 API                     │
├─────────────────────────────────────────────────────┤
│ Framework: CameraService (native)                    │
│   → ICameraProvider (AIDL HAL interface)            │
├─────────────────────────────────────────────────────┤
│ Camera HAL3 (Vendor implementation)                  │
│   → Qualcomm: CamX / Chi-CDK framework             │
│   → Pipeline: Sensor → CSI → ISP → Output          │
├─────────────────────────────────────────────────────┤
│ Kernel: V4L2 + Media Controller                      │
│   → Sensor driver (I2C: CCI bus)                    │
│   → CSIPHY driver                                    │
│   → CSID driver                                      │
│   → VFE/IFE driver                                   │
├─────────────────────────────────────────────────────┤
│ Hardware: Sensor → D-PHY → CSIPHY → CSID → ISP     │
└─────────────────────────────────────────────────────┘
```

### 14.2 Qualcomm CamX/Chi-CDK

```
CamX: Camera hardware abstraction (Qualcomm proprietary)
  - Node-based pipeline architecture
  - Each ISP block = Node (IFE node, BPS node, IPE node, JPEG node)
  - Nodes connected via ports (input/output)
  - Request-based processing (per-frame settings)

Chi-CDK: Camera Hardware Interface - Component Development Kit
  - Override/extend CamX behavior
  - Custom nodes for OEM features
  - Use case selection (photo, video, surround-view)
  - Tuning interface (chromatix/tuning data)
```

---

## 15. SENSOR INITIALIZATION OVER CCI (I2C)

### 15.1 Camera Control Interface

Before CSI-2 data flows, the SoC configures the sensor via I2C (CCI):

```
SoC CCI Master ←──I2C───→ Camera Sensor Registers

Initialization sequence:
1. Power-on sequence (AVDD → DOVDD → DVDD → RESET → MCLK)
2. Read sensor ID register (verify communication)
3. Load register settings (resolution, frame rate, exposure)
4. Configure CSI-2 output (lane count, data rate, data type)
5. Start streaming (write to mode_select register)
6. Sensor begins outputting CSI-2 frames
```

### 15.2 Typical Sensor Registers (e.g., Sony IMX577)

| Register | Address | Function |
|----------|---------|----------|
| mode_select | 0x0100 | 0=standby, 1=streaming |
| image_orientation | 0x0101 | H/V flip |
| frame_length_lines | 0x0340 | Frame height + VBI |
| line_length_pck | 0x0342 | Line width + HBI (pixel clocks) |
| coarse_integration_time | 0x0202 | Exposure (in lines) |
| analog_gain | 0x0204 | Analog gain code |
| digital_gain | 0x020E | Digital gain |
| x_output_size | 0x034C | Horizontal resolution |
| y_output_size | 0x034E | Vertical resolution |
| csi_data_format | 0x0112 | CSI-2 data type |
| csi_lane_mode | 0x0114 | Number of lanes (0=1, 1=2, 3=4) |

---

## 16. ERROR HANDLING

### 16.1 Error Types

| Error | Detection | Impact | Recovery |
|-------|-----------|--------|----------|
| ECC 1-bit | ECC correction | None (auto-corrected) | Transparent |
| ECC 2-bit | ECC detection | Packet header unreliable | Drop packet, flag frame |
| CRC mismatch | CRC check | Line data corrupted | Flag frame, possible retry |
| SoT sync failure | PHY layer | Lane lost synchronization | Re-sync on next SoT |
| Frame drop | FS/FE counter gap | Missing frame | Report to HAL, adjust 3A |
| Lane sync loss | No valid HS | Complete data loss | PHY reset + re-init |
| Short packet error | ECC on short pkt | FS/FE lost | Frame boundary confusion |
| Overflow | FIFO full | Data lost (back-pressure) | Drop frame, reduce rate |

### 16.2 Error Reporting (Linux Kernel)

```c
// CSID error IRQ handler (Qualcomm)
static irqreturn_t csid_irq_handler(int irq, void *data)
{
    uint32_t irq_status = readl(csid_base + CSID_IRQ_STATUS);
    
    if (irq_status & CSID_IRQ_CRC_ERR)
        pr_err("CSI-2 CRC error on VC%d\n", vc);
    if (irq_status & CSID_IRQ_ECC_ERR)
        pr_err("CSI-2 ECC 2-bit error\n");
    if (irq_status & CSID_IRQ_FIFO_OVERFLOW)
        pr_err("CSI-2 FIFO overflow - frame dropped\n");
    if (irq_status & CSID_IRQ_FRAME_DROP)
        dev_warn(dev, "Frame drop detected\n");
    
    // Clear interrupt
    writel(irq_status, csid_base + CSID_IRQ_CLEAR);
    return IRQ_HANDLED;
}
```

---

## 17. POWER MANAGEMENT

### 17.1 Camera Power Domains

```
Camera sensor power-on sequence (order critical!):
  1. AVDD (Analog VDD): 2.8V → powers pixel array
  2. DOVDD (Digital I/O): 1.8V → powers I2C + CSI-2 I/O
  3. DVDD (Digital core): 1.05-1.2V → powers sensor logic
  4. RESET_N: De-assert (high) → sensor comes out of reset
  5. MCLK: Reference clock (6-27 MHz) → sensor PLL starts
  6. Wait: T_startup (sensor-specific, ~1-20 ms)
  7. I2C access possible → configure sensor
  8. Start streaming → CSI-2 active

Power-off: Reverse order (MCLK off → RESET low → DVDD → DOVDD → AVDD)
```

### 17.2 CSI-2 Power States

| State | PHY | Power | Description |
|-------|-----|-------|-------------|
| Active streaming | HS bursts + LP idle | Full power | Transmitting frame data |
| Inter-frame LP | LP-11 only | Medium | Between frames (sensor still running) |
| Standby | LP-11, sensor standby | Low | Sensor configured but not streaming |
| Power down | PHY off | Minimal | Only I2C alive for wake command |
| Full off | Everything off | Zero | Regulator disabled |

---

## 18. MULTI-CAMERA SYNCHRONIZATION

### 18.1 Why Synchronization Matters

For surround-view or ADAS, multiple cameras must capture at the same instant:
- Object at 60 km/h moves 0.56 m in one frame (at 30 fps)
- If cameras are 1 frame apart → stitching errors, phantom objects
- Target: <1 ms inter-camera sync

### 18.2 Hardware Sync Methods

```
Method 1: External hardware trigger (XVS/Frame sync)
  ┌────────────┐
  │ SoC GPIO   │───── XVS signal ────┬──→ Sensor 0 (FSIN pin)
  │ (Trigger)  │                      ├──→ Sensor 1 (FSIN pin)
  │            │                      ├──→ Sensor 2 (FSIN pin)
  │            │                      └──→ Sensor 3 (FSIN pin)
  └────────────┘
  All sensors start exposure at same instant

Method 2: Master-Slave sensor sync
  Sensor 0 (Master) → outputs VSYNC → Sensor 1,2,3 (Slaves)
  Slaves trigger readout on master's VSYNC

Method 3: Software sync (least accurate)
  Start streaming on all sensors within ~1 frame period
  Rely on frame timestamps for alignment
  Accuracy: ±1 frame (33ms at 30fps) — insufficient for ADAS
```

### 18.3 Timestamp Correlation

```
Each frame carries:
  - Frame number (from sensor or CSID)
  - SoC timestamp (when FS/FE received)
  - Sensor timestamp (from embedded data)

ISP/HAL matches frames by timestamp:
  If |timestamp_cam0 - timestamp_cam1| < threshold → same instant
```

---

## 19. CSI-2 v4.0 NEW FEATURES

### 19.1 Scrambling

- **Purpose**: Reduce EMI by whitening data pattern
- **Method**: XOR payload with LFSR-generated pseudorandom sequence
- **Benefit**: Eliminates repetitive patterns that create spectral peaks
- **Automotive relevance**: Helps pass EMC (electromagnetic compatibility) testing

### 19.2 USL v2 (Unified Serial Link)

- **Bidirectional**: Enables return channel (SoC → sensor) over CSI-2 lanes
- **Use case**: Send commands to sensor without separate I2C/CCI
- **Benefit**: Reduces wiring (especially with SerDes: one less wire per camera)

### 19.3 Enhanced Virtual Channels

- Up to 32 VCs (extended from 4)
- Better support for multi-sensor aggregation
- Per-VC flow control

### 19.4 Smart Region of Interest (SROI)

- Sensor can output different resolution per line group
- Full resolution for ROI + downsampled for rest
- Reduces bandwidth while maintaining detail where needed (e.g., license plates)

---

## 20. COMMON ISSUES IN AUTOMOTIVE CSI-2

| Issue | Root Cause | Solution |
|-------|-----------|----------|
| No frames received | Sensor not streaming / power sequence wrong | Verify power-on, check CCI access, mode_select=1 |
| CRC errors (intermittent) | Signal integrity (long PCB trace, EMI) | Improve routing, add termination, reduce speed |
| Frame drops | FIFO overflow (ISP too slow) | Increase FIFO, reduce resolution/FPS, check DDR BW |
| Color artifacts | Wrong Bayer pattern / DT mismatch | Verify sensor DT matches CSID/ISP config |
| Green/purple tint | Wrong data type (RAW10 vs RAW12 mismatch) | Match sensor output format to CSI RX config |
| Only first frame works | Sensor enters standby after one frame | Check continuous vs snapshot mode register |
| Lane sync failures | PHY timing mismatch (settle time) | Tune T_HS-SETTLE in CSIPHY config |
| Black frames | Lens cap / sensor exposure = 0 / gain = 0 | Check exposure/gain via CCI read |
| Blank lines in image | Lane distribution mismatch (1-lane vs 2-lane) | Configure correct lane count in sensor + SoC |
| EMI test failure | Repetitive data patterns | Enable CSI-2 v4.0 scrambling |

---

## 21. BANDWIDTH BUDGET (AUTOMOTIVE EXAMPLE)

```
SA8295P Multi-Camera System:
┌────────────────────────────────────────────────────────────┐
│ Camera          │ Res    │ Fmt   │ FPS │ BW     │ Port/Lanes│
├─────────────────┼────────┼───────┼─────┼────────┼──────────┤
│ Front ADAS      │ 8 MP   │ RAW12 │ 30  │ 3.4 Gbps│ Port0/4L │
│ Rear view       │ 2 MP   │ RAW10 │ 30  │ 0.7 Gbps│ Port1/2L │
│ Surround FL     │ 1.3 MP │ RAW12 │ 30  │ 0.5 Gbps│ Port2/2L │
│ Surround FR     │ 1.3 MP │ RAW12 │ 30  │ 0.5 Gbps│ Port2/2L │
│ Surround RL     │ 1.3 MP │ RAW12 │ 30  │ 0.5 Gbps│ Port3/2L │
│ Surround RR     │ 1.3 MP │ RAW12 │ 30  │ 0.5 Gbps│ Port3/2L │
│ DMS (interior)  │ 2 MP   │ RAW10 │ 30  │ 0.7 Gbps│ Port4/2L │
├─────────────────┼────────┼───────┼─────┼────────┼──────────┤
│ TOTAL           │        │       │     │ 6.8 Gbps│          │
└────────────────────────────────────────────────────────────┘

Note: Surround cameras typically go through MAX96712 deserializer
which aggregates 4 cameras onto single 4-lane CSI-2 port
```

---

## 22. CSI-2 vs OTHER CAMERA INTERFACES

| Feature | MIPI CSI-2 | Parallel (DVP) | LVDS | Sub-LVDS |
|---------|-----------|----------------|------|----------|
| Pins (for 10-bit) | 10 (4L) | 14+ (10 data + CLK + sync) | 8-12 pairs | 6-10 pairs |
| Max data rate | 18+ Gbps | ~500 Mbps | ~1.6 Gbps | ~1.6 Gbps |
| Power | Low (200 mV swing) | High (3.3V CMOS) | Medium | Medium |
| Standard | MIPI Alliance | Sensor-specific | Sensor-specific | Sensor-specific |
| SoC support | Universal | Legacy only | Rare (FPGA) | Rare (FPGA) |
| Market share | >95% (new designs) | <5% (legacy) | Niche | Niche |
| Cable distance | <15 cm (direct) | <10 cm | <30 cm | <30 cm |
| Protocol overhead | Yes (PH/PF/ECC/CRC) | None | Minimal | Minimal |

---

## 23. TESTING & VALIDATION

### 23.1 CSI-2 Compliance Testing

MIPI Alliance defines conformance test suites:
- **D-PHY CTS**: Eye diagram, timing parameters, common-mode
- **CSI-2 Protocol CTS**: Packet format, ECC, CRC, state machines
- **Tools**: LeCroy/Teledyne MIPI decoders, Keysight oscilloscopes

### 23.2 Automotive Camera Validation

| Test | Method | Pass Criteria |
|------|--------|---------------|
| Functional (basic capture) | Stream frames, verify content | No errors, correct resolution |
| Stress (long duration) | 24-hour continuous streaming | 0 frame drops, 0 CRC errors |
| Thermal | -40°C to +105°C operation | No functional degradation |
| EMC | CISPR 25 (automotive) | Below limits |
| Signal integrity | Eye diagram at receiver | Eye open ≥ 60% |
| Power sequence | Monitor rails with scope | Correct order, timing met |
| Multi-camera sync | External trigger test | <1 ms inter-camera |
| Error injection | Force CRC error, verify recovery | System recovers gracefully |

---

END OF DOCUMENT 01 — MASTER THEORY
