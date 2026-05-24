# MIPI CSI-2 — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 04 of 08
# Format: Q (front) / A (back) — 200+ cards in 8 decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: FUNDAMENTALS (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | What does CSI-2 stand for? | Camera Serial Interface 2 (MIPI Alliance standard) |
| 2 | Year CSI-2 v1.0 was released? | 2005 |
| 3 | Latest CSI-2 version? | v4.0 (2023) |
| 4 | Who defines CSI-2? | MIPI Alliance |
| 5 | CSI-2 data direction? | Unidirectional: Sensor → Host (SoC) |
| 6 | Two PHY options for CSI-2? | D-PHY and C-PHY |
| 7 | Max data lanes (D-PHY)? | 4 |
| 8 | Max D-PHY speed (v2.5)? | 4.5 Gbps per lane |
| 9 | Max aggregate bandwidth (4L D-PHY v2.5)? | 18 Gbps (4 × 4.5 Gbps) |
| 10 | Two packet types in CSI-2? | Short Packet (4B) and Long Packet (4+WC+2 B) |
| 11 | What carries pixel data? | Long Packets |
| 12 | What signals frame boundaries? | Short Packets (FS/FE) |
| 13 | How many VCs in standard CSI-2? | 4 (2-bit VC field) |
| 14 | How many VCs in extended (v3.0+)? | 32 |
| 15 | CSI-2 control channel? | Separate: CCI (I2C-based, Camera Control Interface) |
| 16 | CSI-2 vs parallel camera: advantage? | Fewer pins, higher speed, scalable lanes |
| 17 | Typical CSI-2 trace length? | < 15 cm (PCB) |
| 18 | Why short trace length? | High-speed differential signals attenuate over distance |
| 19 | How does automotive extend distance? | SerDes (GMSL2, FPD-Link) converts for 15m coax |
| 20 | What is a lane in D-PHY? | A differential pair (P/N) carrying serial data |
| 21 | How many wires for 4-lane D-PHY? | 10 (1 clock pair + 4 data pairs) |
| 22 | CSI-2 typical applications? | Smartphone cameras, automotive ADAS, drones, IoT |
| 23 | What replaced? | Legacy parallel camera interface (8-16 data + sync pins) |
| 24 | Main competitor to CSI-2? | SLVS-EC (Sony), Sub-LVDS (legacy) |
| 25 | Byte order on wire? | LSB first (within each byte) |
| 26 | CSI-2 operating voltage? | D-PHY: LP=1.2V, HS=200mV differential |
| 27 | Is CSI-2 a bus or point-to-point? | Point-to-point (one TX, one RX) |
| 28 | Can multiple sensors share CSI-2 lanes? | Not directly — need SerDes aggregator or separate ports |
| 29 | What is DPHY_DL? | Data Lane (carries pixel/meta data) |
| 30 | What is DPHY_CL? | Clock Lane (provides bit timing reference) |

---

# DECK 2: D-PHY PHYSICAL LAYER (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | D-PHY signaling in HS mode? | Differential: 200 mV swing (100 mV above/below common mode) |
| 2 | D-PHY signaling in LP mode? | Single-ended: 0V and 1.2V on each wire independently |
| 3 | What is LP-11? | Stop state (idle): Dp=HIGH, Dn=HIGH |
| 4 | What is LP-01? | HS-Request: Dp=LOW, Dn=HIGH |
| 5 | What is LP-00? | Bridge state: Both Dp=LOW, Dn=LOW |
| 6 | HS entry sequence? | LP-11 → LP-01 → LP-00 → HS-0 → HS-Data |
| 7 | What is SoT? | Start of Transmission: LP→HS transition + sync byte |
| 8 | What is EoT? | End of Transmission: HS→LP transition (trail + exit) |
| 9 | HS sync byte? | 0xB8 (for byte alignment at receiver) |
| 10 | D-PHY clock: SDR or DDR? | DDR (Double Data Rate) — data on both edges |
| 11 | T_LPX minimum? | 50 ns |
| 12 | T_HS-PREPARE range? | 40ns + 4×UI to 85ns + 6×UI |
| 13 | T_HS-ZERO purpose? | Allow receiver CDR to lock to HS bit rate |
| 14 | T_HS-TRAIL minimum? | max(8×UI, 60ns + 4×UI) |
| 15 | T_HS-EXIT minimum? | 100 ns |
| 16 | What is UI? | Unit Interval = 1/data_rate (e.g., 400 ps at 2.5 Gbps) |
| 17 | D-PHY termination? | 100Ω differential at receiver (HS mode only) |
| 18 | LP driver impedance? | High impedance (pull-ups, not terminated) |
| 19 | Common mode voltage? | 200 mV (HS mode) |
| 20 | Continuous vs non-continuous clock? | Continuous: CLK always in HS. Non-continuous: CLK returns to LP between packets |
| 21 | T_CLK-PRE purpose? | Clock must be in HS before data lane enters HS |
| 22 | T_CLK-POST purpose? | Clock stays in HS after data lane exits HS |
| 23 | Eye mask requirements? | Height ≥ 60 mV, Width ≥ 0.35 UI |
| 24 | Jitter budget? | Random < 0.15 UI, Deterministic < 0.15 UI |
| 25 | Clock-data skew tolerance? | Receiver must sample within ± 0.25 UI of clock |
| 26 | What is T_HS_SETTLE? | Time receiver waits after SoT before sampling (programmable at RX) |
| 27 | Wrong T_HS_SETTLE effect? | Too short: bit errors. Too long: miss data. Lane sync failure |
| 28 | Lane count detection? | Not auto-detected — must match TX config to RX device tree |
| 29 | Intra-pair skew tolerance? | < 10 ps (PCB length matching between P and N) |
| 30 | Inter-lane skew tolerance? | < 3 UI (corrected by lane alignment at receiver) |

---

# DECK 3: C-PHY (15 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | C-PHY wire count per trio? | 3 wires (A, B, C) — no separate clock |
| 2 | C-PHY signal levels? | Tri-level: +350mV, 0V, -350mV |
| 3 | Valid C-PHY states? | 6 (each with one HIGH, one MID, one LOW wire) |
| 4 | Bits per symbol in C-PHY? | ~2.28 bits (log₂(5) per transition) |
| 5 | C-PHY pins for same BW as 4L D-PHY? | 6 pins (2 trios) vs 10 pins (4 lanes + clock pair) |
| 6 | How is clock recovered in C-PHY? | From transitions between symbols (embedded clock) |
| 7 | C-PHY max symbol rate? | 3.5 Gsps (symbols per second) per trio |
| 8 | C-PHY max bit rate per trio? | ~5.7 Gbps (3.5 × 16/7 × 2.28) |
| 9 | C-PHY encoding overhead? | 16 bits per 7 symbols |
| 10 | Why 5 valid next states? | Can't repeat same state (would have no transition = no clock) |
| 11 | C-PHY advantage over D-PHY? | Higher bandwidth per pin, no dedicated clock lane |
| 12 | C-PHY disadvantage? | More complex receiver, harder PCB routing, less SoC support |
| 13 | Automotive D-PHY or C-PHY? | D-PHY (industry standard, SerDes designed for it) |
| 14 | C-PHY typical use? | High-resolution smartphone cameras (48MP+) |
| 15 | C-PHY+D-PHY combo? | Some SoCs support both on same port (config-selectable) |

---

# DECK 4: PACKETS & PROTOCOL (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Short packet structure? | 4 bytes: [Data ID (1B)] [Short Data (2B)] [ECC (1B)] |
| 2 | Long packet structure? | [PH: DI+WC+ECC (4B)] [Payload (WC bytes)] [CRC (2B)] |
| 3 | Data ID format (8 bits)? | [VC1:VC0 (2b)] [DT5:DT0 (6b)] |
| 4 | ECC type? | 6-bit Hamming code over 24-bit header |
| 5 | ECC correction capability? | 1-bit error correction |
| 6 | ECC detection capability? | 2-bit error detection (no correction) |
| 7 | CRC polynomial? | CRC-CCITT: x¹⁶+x¹²+x⁵+1 (0x1021) |
| 8 | CRC initial value? | 0xFFFF |
| 9 | Max Word Count (WC)? | 65535 bytes (16-bit field) |
| 10 | Frame Start DT code? | 0x00 |
| 11 | Frame End DT code? | 0x01 |
| 12 | Line Start DT code? | 0x02 |
| 13 | Line End DT code? | 0x03 |
| 14 | RAW8 DT code? | 0x2A |
| 15 | RAW10 DT code? | 0x2B |
| 16 | RAW12 DT code? | 0x2C |
| 17 | RAW14 DT code? | 0x2D |
| 18 | YUV422-8 DT code? | 0x1E |
| 19 | RGB888 DT code? | 0x24 |
| 20 | Embedded 8-bit DT code? | 0x12 |
| 21 | Null (padding) DT? | 0x10 |
| 22 | Frame number in FS/FE? | 16-bit counter in short packet data field |
| 23 | Purpose of embedded data? | Carries per-frame metadata (exposure, gain, temperature) |
| 24 | Typical embedded data size? | 2-8 lines × line_width bytes at start of frame |
| 25 | WC for 1920-wide RAW10? | 2400 bytes (1920 × 10 / 8) |
| 26 | WC for 3840-wide RAW12? | 5760 bytes (3840 × 12 / 8) |
| 27 | WC for 1920-wide YUV422? | 3840 bytes (1920 × 2 bytes/pixel) |
| 28 | What if ECC detects 2-bit error? | Packet discarded (can't correct) |
| 29 | What if CRC fails? | Line marked corrupted, ISP interpolates |
| 30 | Can a short packet have CRC? | No — only long packets have CRC (short has ECC only) |

---

# DECK 5: DATA TYPES & FORMATS (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | RAW10 packing? | 4 pixels → 5 bytes (MSBs first, LSBs packed in 5th byte) |
| 2 | RAW12 packing? | 2 pixels → 3 bytes (MSBs first, LSBs in 3rd byte) |
| 3 | RAW8 packing? | 1:1 — each pixel = 1 byte (no packing) |
| 4 | RAW14 packing? | 4 pixels → 7 bytes |
| 5 | What is Bayer pattern? | Color filter array: RGGB, BGGR, GRBG, or GBRG |
| 6 | Why is RAW10 most common? | Good balance of dynamic range (10-bit) vs bandwidth |
| 7 | RAW12 advantage? | Higher dynamic range (4096 levels vs 1024) for HDR |
| 8 | YUV422 vs RAW: which needs ISP? | RAW needs ISP (demosaic). YUV422 is already processed |
| 9 | When is YUV422 used over CSI-2? | When sensor has built-in ISP (reduces SoC load) |
| 10 | What is PDAF data? | Phase Detect Auto Focus — special pixels for AF |
| 11 | How is PDAF sent? | As embedded data (DT=0x12) or separate VC |
| 12 | Bits per pixel: RAW10? | 10 |
| 13 | Bits per pixel: YUV422? | 16 (2 bytes per pixel) |
| 14 | Bits per pixel: RGB888? | 24 (3 bytes per pixel) |
| 15 | What is UBWC format? | Universal Bandwidth Compression (Qualcomm) — lossy/lossless compressed |
| 16 | Where is UBWC applied? | ISP output → DDR (saves memory bandwidth, not on CSI-2) |
| 17 | Color depth: 10-bit? | 1024 levels per color component |
| 18 | Color depth: 12-bit? | 4096 levels per color component |
| 19 | Dynamic range gain of 12b vs 10b? | ~6 dB (2 extra stops) |
| 20 | Typical HDR sensor output? | DOL: Long exp (VC0) + Short exp (VC1), both RAW12 |
| 21 | Sensor output for DMS (IR)? | RAW10 or RAW8 (monochrome IR image, no Bayer) |
| 22 | What DT for generic 8-bit data? | Null (0x10) or User Defined (0x30-0x37) |
| 23 | Max effective resolution at 30fps, 4L 2.5Gbps? | ~16 MP (RAW10) after blanking |
| 24 | Line stride vs WC? | WC = raw pixel bytes. Stride may include padding for alignment |
| 25 | Planar vs packed in ISP output? | CSI-2 is always packed. ISP may output planar (Y/UV separate) |

---

# DECK 6: AUTOMOTIVE & SERDES (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Why SerDes for automotive cameras? | Long cable distance (5-15m) exceeds D-PHY range |
| 2 | GMSL2 vendor? | Analog Devices (formerly Maxim) |
| 3 | FPD-Link vendor? | Texas Instruments |
| 4 | GMSL2 max link rate? | 6 Gbps |
| 5 | GMSL2 max cable length? | 15 m (coaxial) |
| 6 | FPD-Link IV max speed? | 12.8 Gbps |
| 7 | What is MAX96717? | GMSL2 serializer (camera side) |
| 8 | What is MAX96712? | GMSL2 4:1 aggregation deserializer (SoC side) |
| 9 | What is 4:1 aggregation? | 4 camera inputs combined into 1 CSI-2 output (using VCs) |
| 10 | Power delivery over GMSL? | PoC (Power over Coax): SoC board powers remote camera via same cable |
| 11 | I2C over GMSL? | Tunneled: SoC's I2C commands routed to remote sensor through SerDes |
| 12 | GPIO over GMSL? | Tunneled: Frame sync, reset signals sent over same link |
| 13 | Coax vs STP for GMSL2? | Coax (single wire): cheaper, easier routing. STP: better EMI |
| 14 | SA8295P CSI-2 ports? | 7 CSIPHY ports |
| 15 | SA8295P max cameras? | 16 concurrent (via aggregation) |
| 16 | SA8295P max lane speed? | 2.5 Gbps per lane |
| 17 | Typical surround-view setup? | 4× fisheye cameras → MAX96712 4:1 → 1 CSI port (VC0-3) |
| 18 | Front ADAS camera bandwidth? | 3840×2160×RAW12×30fps ≈ 3.4 Gbps (need 4 lanes) |
| 19 | Automotive temp range? | -40°C to +105°C (camera module) |
| 20 | Camera startup time requirement? | < 2 seconds (rear-view, regulated in some markets) |
| 21 | CISPR 25 relevance? | EMI standard for automotive — camera links must pass |
| 22 | CSI-2 scrambling purpose? | Whiten data to spread EMI spectrum (pass CISPR 25) |
| 23 | Multi-camera sync method? | Hardware: FSIN/XVS GPIO pulse to all sensors |
| 24 | Sync accuracy (hardware)? | < 1 line period (< 30 µs) |
| 25 | Without sync, 60km/h movement per frame? | 56 cm (unacceptable for stitching) |
| 26 | Camera failure handling? | Detect via CRC errors/timeout → inform ADAS → degraded mode |
| 27 | ASIL level for camera system? | Typically ASIL-B (surround view) or ASIL-C (ADAS) |
| 28 | Redundancy strategy? | Multiple overlapping FOV cameras, watchdog monitoring |
| 29 | Typical automotive sensors? | IMX490 (Sony 5.4MP HDR), OX03F10 (OmniVision 3MP), AR0820 (ON 8MP) |
| 30 | HDR dynamic range target? | > 120 dB (tunnel exit, sun + shadow) |

---

# DECK 7: ISP & PROCESSING (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | CSIPHY function? | D-PHY receiver: lane sync, HS settling, deserialize bits → bytes |
| 2 | CSID function? | Protocol parser: ECC/CRC check, VC demux, extract pixel data |
| 3 | IFE function? | Real-time ISP: demosaic, 3A stats, lens correction, output to DDR |
| 4 | BPS function? | Offline Bayer processing: linearization, hot pixel, Bayer NR |
| 5 | IPE function? | Offline processing: YUV NR, MFNR, sharpening, face detect |
| 6 | 3A stands for? | Auto Exposure, Auto Focus, Auto White Balance |
| 7 | Where are 3A stats computed? | IFE (real-time, every frame) |
| 8 | What is MFNR? | Multi-Frame Noise Reduction: stack N frames, average noise |
| 9 | What is CamX? | Qualcomm's camera HAL framework (connects HAL3 to kernel) |
| 10 | What is Chi-CDK? | Customer-facing layer on top of CamX for OEM customization |
| 11 | ISP pipeline for preview? | CSIPHY → CSID → IFE → DDR → Display |
| 12 | ISP pipeline for snapshot? | CSIPHY → CSID → IFE → DDR → BPS → DDR → IPE → DDR → JPEG |
| 13 | ISP pipeline for ADAS (raw dump)? | CSIPHY → CSID → IFE → DDR → NPU (minimal processing) |
| 14 | Demosaic algorithm? | Interpolate missing color channels per pixel from Bayer pattern |
| 15 | Lens shading correction? | Compensate for brightness falloff at image edges (vignetting) |
| 16 | What is NV12? | YUV semi-planar: Y plane + interleaved UV plane (most common ISP output) |
| 17 | What is UBWC? | Qualcomm bandwidth compression: ~30-40% DDR BW savings |
| 18 | IFE output format? | UBWC NV12 (for display), plain RAW (for BPS), 3A stats |
| 19 | How many IFEs on SA8295P? | 4 (can process 4 independent streams in real-time) |
| 20 | Can IFE do downscale? | Yes — built-in scaler for video/preview streams |
| 21 | CSID overflow meaning? | Pixels arriving faster than downstream can consume → lines lost |
| 22 | CSID overflow fix? | Increase IFE/DDR clock, reduce sensor frame rate, enable compression |
| 23 | What is VFE? | Video Front End — older Qualcomm ISP name (pre-IFE) |
| 24 | HAL3 request model? | Per-frame capture request with settings (exposure, crop, stream outputs) |
| 25 | HAL3 result delivery? | CaptureResult callback with metadata + buffers when frame processed |

---

# DECK 8: LINUX/ANDROID STACK (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Linux camera framework? | V4L2 (Video4Linux2) + Media Controller |
| 2 | V4L2 device node? | /dev/video0, /dev/video1, ... (per stream) |
| 3 | Media controller device? | /dev/media0 (for pipeline configuration) |
| 4 | Subdevice node? | /dev/v4l-subdev0 (sensor, CSIPHY, CSID as subdevs) |
| 5 | VIDIOC_S_FMT purpose? | Set pixel format (resolution, fourcc code) on video device |
| 6 | VIDIOC_REQBUFS purpose? | Request kernel to allocate N frame buffers |
| 7 | VIDIOC_QBUF purpose? | Queue an empty buffer for driver to fill |
| 8 | VIDIOC_DQBUF purpose? | Dequeue a filled buffer (blocks until frame ready) |
| 9 | VIDIOC_STREAMON purpose? | Start streaming (hardware begins capturing) |
| 10 | VIDIOC_STREAMOFF purpose? | Stop streaming |
| 11 | media-ctl usage? | Configure pipeline links and formats between subdevices |
| 12 | v4l2-ctl usage? | Set/get controls, formats, stream capture |
| 13 | Camera sensor driver location? | drivers/media/i2c/ (e.g., imx577.c, imx490.c) |
| 14 | CSIPHY driver location? | drivers/media/platform/qcom/camss/ |
| 15 | Sensor probe sequence? | Power on → I2C read chip ID → Register sensor → Add subdev |
| 16 | Device tree CSI-2 properties? | data-lanes, clock-lanes, clock-frequency, link-frequencies |
| 17 | Android Camera2 API entry? | CameraManager.openCamera() |
| 18 | Camera HAL interface version? | ICameraDevice (HAL3) via AIDL/HIDL |
| 19 | Camera provider service? | android.hardware.camera.provider@2.7-service |
| 20 | CameraService location? | frameworks/av/services/camera/ |
| 21 | How to capture raw frame (debug)? | v4l2-ctl --stream-mmap --stream-count=1 --stream-to=frame.raw |
| 22 | How to check camera streams? | dumpsys media.camera |
| 23 | Kernel log for CSI errors? | dmesg | grep -i "csi\|csid\|csiphy\|cam" |
| 24 | Android camera trace? | atrace -c camera (systrace for camera pipeline) |
| 25 | GBM/ION for camera buffers? | ION/DMA-BUF: allocates physically contiguous memory for ISP DMA |

---

# DECK 9: ERRORS & DEBUGGING (20 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | ECC 1-bit error action? | Auto-corrected by hardware, continue normally |
| 2 | ECC 2-bit error action? | Packet discarded, error counter incremented |
| 3 | CRC error action? | Line marked corrupted, ISP interpolates, counter incremented |
| 4 | SoT sync failure meaning? | Receiver can't find 0xB8 sync byte — PHY timing issue |
| 5 | Frame drop cause: overflow? | Downstream (IFE/DDR) can't keep up with incoming data rate |
| 6 | Frame drop cause: sync loss? | PHY lost lane synchronization mid-frame |
| 7 | Green/pink image cause? | Lane swap or wrong Bayer pattern configured |
| 8 | All-black image cause? | Sensor not streaming, or exposure time = 0, or PHY not locked |
| 9 | Horizontal lines/noise cause? | CRC errors on specific lines (EMI or poor signal integrity) |
| 10 | Image shifted/skewed cause? | Wrong WC (Word Count) or lane count mismatch |
| 11 | How to check PHY lock status? | Read CSIPHY status register or sysfs debug node |
| 12 | How to check CRC error count? | cat /sys/devices/.../csid_debug or dmesg for IRQ logs |
| 13 | Oscilloscope test point? | SoC input pins (after connector/SerDes) for eye diagram |
| 14 | Logic analyzer for CSI-2? | Protocol analyzer (e.g., LeCroy/Teledyne CSI-2 decoder) |
| 15 | Common T_HS_SETTLE fix? | Increment/decrement by 1-2 steps until no SoT errors |
| 16 | SerDes lock failure debug? | Check GMSL link status register (I2C read from deserializer) |
| 17 | PoC fault (Power over Coax)? | Short on cable → overcurrent → link won't establish |
| 18 | Temperature-related frame drops? | Thermal throttling reduces ISP clock → overflow |
| 19 | Intermittent errors in car? | Vibration loosening connector → check mechanical retention |
| 20 | CSID FIFO overflow recovery? | Stop stream, reset CSID, re-init PHY, restart stream |

---

END OF DOCUMENT 04 — FLASHCARDS (210 cards across 9 decks)
