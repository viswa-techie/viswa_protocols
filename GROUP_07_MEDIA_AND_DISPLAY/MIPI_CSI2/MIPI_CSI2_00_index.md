# MIPI CSI-2 (Camera Serial Interface 2) — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 00 of 08 (Index & Overview)
# Domain: Embedded, Automotive (AAOS), Camera Systems, Qualcomm SA8295P
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | MIPI Camera Serial Interface 2 |
| Specification Body | MIPI Alliance |
| Current Version | CSI-2 v4.0 (2023) |
| First Release | CSI-2 v1.0 (2005) |
| Physical Layer | D-PHY (v2.5) or C-PHY (v2.0) |
| Topology | Point-to-point (TX → RX), unidirectional |
| Data Direction | Image sensor → SoC (camera → host processor) |
| Max Lanes | 4 data lanes (D-PHY) / 3 data trios (C-PHY) |
| Max Speed (D-PHY) | 4.5 Gbps/lane (D-PHY v2.5) |
| Max Speed (C-PHY) | 6.0 Gsps/trio → ~13.7 Gbps/trio (C-PHY v2.0) |
| Max Aggregate BW | 18 Gbps (4-lane D-PHY v2.5), 41 Gbps (3-trio C-PHY v2.0) |
| Typical Use Cases | Camera sensor, automotive surround-view, ADAS, rear-view |
| Automotive Relevance | SA8295P: 7× CSI-2 interfaces for multi-camera |
| Wire Count | D-PHY: 2(CLK) + 2×N(Data) = 2+2N; C-PHY: 3×N(trio) |
| Signal Type | Differential (D-PHY) / Tri-level (C-PHY) |
| Voltage Swing | D-PHY: 200 mV typical (HS); C-PHY: ~250 mV |

---

## PROTOCOL GENERATION COMPARISON

| Version | Year | PHY | Key Feature | Max Lane Rate |
|---------|------|-----|-------------|---------------|
| CSI-2 v1.0 | 2005 | D-PHY v1.0 | Basic camera interface | 1.0 Gbps/lane |
| CSI-2 v1.1 | 2012 | D-PHY v1.2 | Virtual Channel ext | 1.5 Gbps/lane |
| CSI-2 v2.0 | 2014 | D-PHY v1.2 | RAW20, USL (Ultra Short) | 2.5 Gbps/lane |
| CSI-2 v2.1 | 2017 | D-PHY v2.0 | Smart Region of Interest | 2.5 Gbps/lane |
| CSI-2 v3.0 | 2019 | D-PHY v2.1 / C-PHY v1.1 | RAW24, Line Blanking Reduction | 4.5 Gbps/lane |
| CSI-2 v4.0 | 2023 | D-PHY v2.5 / C-PHY v2.0 | USL v2, Scrambling, enhanced VC | 4.5 Gbps (D), 6.0 Gsps (C) |

---

## AUTOMOTIVE CSI-2 USE CASES

| Use Case | Cameras | Resolution | FPS | Lanes | Notes |
|----------|---------|-----------|-----|-------|-------|
| Surround View (SVS) | 4× fisheye | 1.3 MP (1280×960) | 30 | 2L each | Wide-angle, parking assist |
| Forward ADAS | 1× front | 8 MP (3840×2160) | 30 | 4L | Object detection, LDW |
| Driver Monitoring (DMS) | 1× interior | 2 MP (1920×1080) | 30 | 2L | IR + visible, drowsiness |
| Rear-View Camera (RVC) | 1× rear | 2 MP | 30 | 2L | Parking, FMVSS 111 |
| e-Mirror | 2× side | 2 MP each | 30 | 2L each | Replace physical mirrors |
| Multi-camera ADAS | 7-12× mixed | Various | 30-60 | Various | L2+ autonomous driving |
| Occupant Monitoring | 1-2× interior | 1-2 MP | 15-30 | 1-2L | Child presence, seatbelt |

---

## QUALCOMM SA8295P CSI-2 HARDWARE

| Feature | SA8295P Specification |
|---------|----------------------|
| CSI-2 Interfaces | 7× MIPI CSI-2 receivers |
| Max Lanes per Port | Up to 4 data lanes (D-PHY) |
| D-PHY Speed | Up to 2.5 Gbps/lane |
| C-PHY Support | Up to 2.0 Gsps/trio |
| Concurrent Cameras | Up to 16 (via virtual channels + ports) |
| ISP (Image Signal Processor) | Dual 14-bit Spectra ISP |
| Max Resolution | Up to 64 MP (single frame) |
| HDR Support | Staggered HDR, DOL-HDR, sensor-fused HDR |
| Output | YUV, NV12, UBWC compressed to DDR |
| CCI (I2C for sensor control) | Multiple CCI buses for sensor config |

---

## KEY TERMINOLOGY (50 Terms)

| # | Term | Definition |
|---|------|-----------|
| 1 | CSI-2 | Camera Serial Interface 2 — MIPI standard for camera data |
| 2 | D-PHY | MIPI physical layer using differential signaling (CLK + Data lanes) |
| 3 | C-PHY | MIPI physical layer using 3-wire trios (no separate clock) |
| 4 | Lane | Single data transmission path (2 wires for D-PHY, 3 wires for C-PHY) |
| 5 | Data Lane | Carries pixel/image data from sensor to host |
| 6 | Clock Lane | Dedicated DDR clock (D-PHY only; C-PHY embeds clock in data) |
| 7 | HS (High-Speed) | High-speed signaling mode (low-voltage differential) |
| 8 | LP (Low-Power) | Low-power signaling mode (single-ended, for control) |
| 9 | Virtual Channel (VC) | Logical multiplexing of multiple streams on same physical lanes |
| 10 | Data Type (DT) | 6-bit code identifying pixel format (RAW8/10/12, YUV, RGB, etc.) |
| 11 | Packet Header (PH) | 32-bit header: Data ID (VC+DT) + Word Count + ECC |
| 12 | Packet Footer (PF) | 16-bit CRC for error detection on long packets |
| 13 | Short Packet | Header-only packet (4 bytes): Frame Start, Frame End, Line number |
| 14 | Long Packet | Header + Payload + CRC: carries actual pixel data |
| 15 | Frame Start (FS) | Short packet signaling beginning of a frame |
| 16 | Frame End (FE) | Short packet signaling end of a frame |
| 17 | Line Start (LS) | Optional short packet signaling line beginning |
| 18 | Line End (LE) | Optional short packet signaling line end |
| 19 | ECC | Error Correcting Code (6-bit Hamming in packet header) |
| 20 | CRC | Cyclic Redundancy Check (16-bit on long packet payload) |
| 21 | SoT (Start of Transmission) | HS entry sequence (LP-11 → LP-01 → LP-00 → HS) |
| 22 | EoT (End of Transmission) | HS exit sequence (back to LP-11) |
| 23 | LP-11 | Stop state (both lines high) — idle |
| 24 | LP-00 | HS Request state |
| 25 | LP-01 | Bridge state (D-PHY HS entry) |
| 26 | Blanking | Period between active video (horizontal/vertical blanking) |
| 27 | RAW8/10/12/14 | Bayer raw pixel formats (bits per pixel) |
| 28 | YUV422 | Chroma-subsampled pixel format (16 bpp) |
| 29 | RGB888 | Full color pixel format (24 bpp) |
| 30 | Embedded Data | Non-image data (sensor registers, stats) sent in frame blanking |
| 31 | ISP | Image Signal Processor — processes RAW Bayer → final image |
| 32 | CCI | Camera Control Interface (I2C-based, for sensor register R/W) |
| 33 | CSIPHY | CSI PHY hardware block in SoC (deserializer) |
| 34 | CSID | CSI Decoder — parses CSI-2 packets, routes to ISP/VFE |
| 35 | VFE | Video Front End — ISP processing pipeline |
| 36 | IFE | Image Front End (Qualcomm: newer ISP naming) |
| 37 | SerDes | Serializer-Deserializer (for long-distance camera: GMSL/FPD-Link) |
| 38 | GMSL | Gigabit Multimedia Serial Link (Maxim/ADI) — extends CSI-2 over coax |
| 39 | FPD-Link III | TI serializer — extends CSI-2 over STP/coax |
| 40 | HDR | High Dynamic Range — multiple exposures combined |
| 41 | DOL-HDR | Digital Overlap HDR (staggered readout in single frame) |
| 42 | Staggered HDR | Long + short exposure frames interleaved |
| 43 | V4L2 | Video4Linux2 — Linux kernel video capture framework |
| 44 | Media Controller | Linux kernel framework for complex camera pipelines |
| 45 | VIDIOC | V4L2 ioctl commands (VIDIOC_STREAMON, QBUF, DQBUF) |
| 46 | Bayer Pattern | Color filter array: RGGB, BGGR, GRBG, GBRG |
| 47 | PDAF | Phase-Detect Autofocus (AF pixels embedded in sensor) |
| 48 | MIPI Alliance | Standards body defining CSI-2, DSI, D-PHY, C-PHY, etc. |
| 49 | USL | Unified Serial Link — CSI-2 extension for bidirectional |
| 50 | Scrambling | Data scrambling for EMI reduction (CSI-2 v4.0) |

---

## DOCUMENT MAP

| Doc# | Filename | Content |
|------|----------|---------|
| 00 | MIPI_CSI2_00_index.md | This file — overview, terminology, study guide |
| 01 | MIPI_CSI2_01_master_theory.md | Complete protocol theory (20+ sections) |
| 02 | MIPI_CSI2_02_diagrams.md | Visual diagrams (PHY, packets, timing, architecture) |
| 03 | MIPI_CSI2_03_interview_questions.md | 50+ questions (Junior → Staff) |
| 04 | MIPI_CSI2_04_flashcards.md | 200+ flashcards in 8 decks |
| 06 | MIPI_CSI2_06_cheatsheet.md | Quick reference (data types, rates, registers) |
| 07 | MIPI_CSI2_07_labs_debugging.md | Labs + debug scenarios (frame drops, CRC errors) |
| 08 | MIPI_CSI2_08_references.md | Specs, kernel paths, books, study plan |

---

## STUDY CHECKLIST

### Beginner (Week 1-2)
- [ ] What is MIPI CSI-2 and why is it needed for cameras?
- [ ] D-PHY basics: differential signaling, lanes, clock
- [ ] HS vs LP modes: when and why each is used
- [ ] Packet structure: short packets (FS/FE) and long packets (pixel data)
- [ ] Common data types: RAW10, RAW12, YUV422
- [ ] Frame timing: FS → line data → FE → blanking
- [ ] Virtual channels: multiplexing multiple cameras
- [ ] Basic Linux camera: V4L2 concepts (capture, buffer queue)

### Intermediate (Week 3-4)
- [ ] D-PHY state machine: LP-11, HS-request, HS-0, burst, EoT
- [ ] C-PHY basics: 3-wire, embedded clock, symbol encoding
- [ ] ECC and CRC: error detection/correction in packets
- [ ] Embedded data: what sensors send in blanking periods
- [ ] ISP pipeline: RAW → demosaic → denoise → tone map → YUV/RGB
- [ ] HDR modes: DOL, staggered, sensor fusion
- [ ] Android camera HAL: Camera2 API → HAL3 → kernel
- [ ] Automotive SerDes: GMSL2/FPD-Link III extending CSI-2

### Advanced (Week 5-6)
- [ ] Multi-camera synchronization (hardware sync, frame ID)
- [ ] Bandwidth calculation for automotive multi-cam (7+ cameras)
- [ ] CSI-2 v4.0: scrambling, USL, enhanced VC
- [ ] Qualcomm camera architecture: CSIPHY → CSID → IFE → BPS → IPE
- [ ] Debug: frame drops, CRC errors, lane sync failures
- [ ] Power optimization: clock gating, lane shutdown
- [ ] Thermal management: camera power budget in automotive
- [ ] ADAS camera requirements: latency, synchronization, reliability

---

END OF DOCUMENT 00 — INDEX & OVERVIEW
