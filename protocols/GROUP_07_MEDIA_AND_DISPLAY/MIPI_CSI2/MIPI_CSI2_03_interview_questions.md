# MIPI CSI-2 — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (12 Questions)

---

### Q1. What is MIPI CSI-2?
**A:** MIPI CSI-2 (Camera Serial Interface 2) is a high-speed serial protocol for transmitting image data from a camera sensor to an application processor. Defined by the MIPI Alliance, it's the industry standard for camera interfaces in smartphones, automotive, and embedded systems. It uses differential signaling (D-PHY) with up to 4 data lanes at up to 4.5 Gbps/lane.

---

### Q2. What is D-PHY and how many wires does it use?
**A:** D-PHY is the physical layer for CSI-2, using differential signaling:
- 1 clock lane (2 wires: CLK_P, CLK_N) — provides DDR bit clock
- 1-4 data lanes (2 wires each: Dx_P, Dx_N) — carry pixel data
- Total: 2 + 2×N wires (N=lanes). 4 lanes = 10 wires.
- Two modes: HS (high-speed, 200 mV differential) and LP (low-power, 1.2V single-ended)

---

### Q3. What are the two types of CSI-2 packets?
**A:**
- **Short Packet** (4 bytes): Header only (Data ID + 16-bit data + ECC). Used for synchronization: Frame Start, Frame End, Line Start, Line End.
- **Long Packet** (4 + N + 2 bytes): Header + Payload + CRC. Carries actual pixel data. Word Count field specifies payload length (up to 65535 bytes).

---

### Q4. What is a Virtual Channel in CSI-2?
**A:** Virtual Channels (VC) multiplex multiple independent streams on the same physical lanes. The 2-bit VC field in each packet's Data ID identifies which stream a packet belongs to. Use cases:
- VC0: Main image, VC1: HDR short exposure
- VC0-3: Four cameras via one deserializer (GMSL2 aggregation)

---

### Q5. What is the difference between HS and LP modes?
**A:**
| | HS Mode | LP Mode |
|-|---------|---------|
| Speed | 80 Mbps – 4.5 Gbps | ~10 Mbps |
| Signaling | Differential (200 mV) | Single-ended (0V/1.2V) |
| Purpose | Pixel data transfer | Control, state transitions |
| Power | Higher (constant current) | Very low |
| When used | During active line data | Between frames, idle |

---

### Q6. What does RAW10 mean?
**A:** RAW10 is a Bayer raw pixel format with 10 bits per pixel. The sensor outputs unprocessed pixel values directly from the image sensor array. "RAW" means no demosaicing/processing has been done. The "10" is bit depth — higher bit depth captures more dynamic range. RAW10 is the most common format for smartphone/automotive cameras. It's packed efficiently: 4 pixels in 5 bytes.

---

### Q7. What is ECC in CSI-2 packet header?
**A:** ECC (Error Correcting Code) is a 6-bit Hamming code appended to the 24-bit packet header. It can:
- **Correct** any single-bit error in the header
- **Detect** any 2-bit error (can't correct, but won't mis-route)
- Protects the critical Data ID and Word Count fields

---

### Q8. What is the role of CRC in CSI-2?
**A:** CRC-16 (polynomial x¹⁶+x¹²+x⁵+1) is appended to long packet payloads. It detects data corruption in the pixel payload. If CRC mismatches at the receiver:
- That line of pixel data is marked corrupted
- ISP can interpolate from adjacent lines or flag the frame
- Error counter incremented for monitoring

---

### Q9. What is Frame Start (FS) and Frame End (FE)?
**A:** Short packets that signal frame boundaries:
- **FS (DT=0x00)**: Marks beginning of a new frame. Contains frame number.
- **FE (DT=0x01)**: Marks end of frame. Contains matching frame number.
- Used by ISP/driver to track frame boundaries, detect frame drops (missing FS/FE), and correlate metadata.

---

### Q10. What is CCI?
**A:** Camera Control Interface — an I2C-based bus used by the SoC to configure camera sensor registers. Separate from CSI-2 data lanes. Used to:
- Set resolution, frame rate, exposure, gain
- Configure CSI-2 output (lane count, data type)
- Start/stop streaming
- Read sensor status/temperature

---

### Q11. Why can't CSI-2 directly connect a car roof camera to the trunk SoC?
**A:** D-PHY signals are designed for short distances (<15 cm PCB traces). At high speeds (2+ Gbps), signal attenuation and EMI over long cables (5-15 m in a car) would corrupt data. Solution: SerDes (Serializer-Deserializer) like GMSL2 or FPD-Link converts CSI-2 to a format suitable for long-distance coaxial or STP cable, then converts back to CSI-2 at the SoC end.

---

### Q12. What is ISP?
**A:** Image Signal Processor — hardware that converts raw Bayer sensor data into viewable images. Pipeline:
1. Black level subtraction
2. Lens shading correction
3. Demosaicing (Bayer → RGB)
4. White balance
5. Color correction
6. Noise reduction
7. Tone mapping / gamma
8. Output: YUV/NV12 for display/encoding

---

# PART B: MID-LEVEL (12 Questions)

---

### Q13. Walk through the complete D-PHY HS entry (SoT) sequence.
**A:**
```
1. LP-11 (Stop/Idle): Both Dp=1.2V, Dn=1.2V
2. LP-01 (HS-Request): Dp=0V, Dn=1.2V — duration ≥ T_LPX (≥50ns)
3. LP-00 (Bridge): Dp=0V, Dn=0V — duration T_HS-PREPARE (40-85ns + UI terms)
4. HS-0 (Preamble): Transition to differential HS — drive differential 0
   Duration: T_HS-ZERO (receiver CDR locking)
5. Sync byte: 0xB8 transmitted on each data lane (byte-level alignment)
6. Payload: HS data at full rate (e.g., 2.5 Gbps)
...
7. T_HS-TRAIL: Continue driving HS after last valid data (≥60ns)
8. Return to LP-11: EoT complete
```
The clock lane enters HS first (before data lanes) and exits HS last.

---

### Q14. Calculate the bandwidth needed for a 4K RAW12 camera at 30 fps.
**A:**
```
Resolution: 3840 × 2160 = 8,294,400 pixels
Bits per pixel: 12
Frames per second: 30
Raw data rate: 8,294,400 × 12 × 30 = 2,986,675,200 bps ≈ 2.99 Gbps

With 15% blanking overhead: 2.99 × 1.15 ≈ 3.43 Gbps

Lane configuration options:
  2 lanes @ 2.5 Gbps = 5.0 Gbps → utilization 69% ✓ (good margin)
  4 lanes @ 1.5 Gbps = 6.0 Gbps → utilization 57% ✓
  2 lanes @ 1.5 Gbps = 3.0 Gbps → utilization 114% ✗ (insufficient!)
```

---

### Q15. How does byte-to-lane distribution work in CSI-2?
**A:** Packet bytes are distributed round-robin across active data lanes:
- 4 lanes: Byte 0→Lane0, Byte 1→Lane1, Byte 2→Lane2, Byte 3→Lane3, Byte 4→Lane0, ...
- 2 lanes: Byte 0→Lane0, Byte 1→Lane1, Byte 2→Lane0, ...

At the receiver, the SoT sync byte (0xB8) on each lane establishes lane alignment. Receiver then recombines bytes in the correct order. If lane count doesn't match between TX and RX → completely garbled data.

---

### Q16. Explain DOL-HDR over CSI-2.
**A:** Digital Overlap HDR sends multiple exposure frames interleaved:
- Long exposure (VC0): Captures dark areas (high exposure time)
- Short exposure (VC1): Captures bright areas (low exposure time)
- Read out line-by-line: L0_long, L0_short, L1_long, L1_short, ...

Benefits: Near-simultaneous capture (no temporal offset between exposures per line).
Cost: 2× bandwidth (two streams). ISP merges using weighted combination.
Automotive need: Tunnel exit (interior dark, exterior bright) requires >100 dB DR.

---

### Q17. How does GMSL2 extend CSI-2 for automotive?
**A:** GMSL2 (Gigabit Multimedia Serial Link 2) by Analog Devices/Maxim:
- **Serializer** (at camera): Takes 4-lane CSI-2 input, serializes + encodes over single coax
- **Coaxial cable**: Carries data + power + control (power over coax) up to 15 m
- **Deserializer** (at SoC): Outputs CSI-2 (4-lane) to SoC CSIPHY
- **Aggregation**: MAX96712 (4:1) takes 4 cameras → outputs 1 CSI-2 port with VCs
- **Bandwidth**: Up to 6 Gbps per link
- **Extras**: Embedded I2C tunnel (configure remote sensor through GMSL), GPIO tunnel, error reporting

---

### Q18. Describe the Qualcomm camera ISP pipeline (IFE/BPS/IPE).
**A:**
```
IFE (Image Front End) — Real-time, per-frame:
  - Demosaic, black level, lens shading, WB, color correction
  - 3A statistics (AE/AF/AWB)
  - Output: Full-res or scaled to DDR

BPS (Bayer Processing Segment) — Offline:
  - Pedestal, linearization, hot pixel correction
  - Bayer-domain noise reduction (BNR)
  - Input: RAW from DDR; Output: Processed Bayer to IPE

IPE (Image Processing Engine) — Offline:
  - YUV-domain noise reduction, sharpening
  - Multi-frame NR (MFNR) — stack multiple frames
  - Face detect, HDR merge
  - Output: Final NV12/UBWC to DDR

Real-time path: CSID → IFE → DDR → Encoder/Display
Snapshot path: CSID → IFE → DDR → BPS → DDR → IPE → DDR → JPEG
```

---

### Q19. What is the V4L2 buffer flow for camera capture?
**A:**
```
1. REQBUFS: Allocate N buffers (kernel-managed DMA memory)
2. QBUF: Queue empty buffers to driver (buffer enters "queued" state)
3. STREAMON: Start capture — hardware begins filling buffers
4. DQBUF: Dequeue filled buffer (frame ready — blocks until available)
5. Process frame (ISP output in buffer)
6. QBUF: Re-queue buffer for reuse
7. Repeat 4-6 for continuous streaming
8. STREAMOFF: Stop capture

Buffer states: DEQUEUED → QUEUED → ACTIVE (being filled) → DONE → DEQUEUED
Minimum 3-4 buffers for smooth streaming (avoids frame drops)
```

---

### Q20. What happens if there's a CRC error on a CSI-2 line?
**A:**
1. CSID hardware detects CRC mismatch after receiving long packet payload
2. CSID raises CRC error interrupt
3. IRQ handler increments per-VC error counter
4. That specific line is flagged (corrupted pixels)
5. ISP may: interpolate from adjacent lines, or mark entire frame bad
6. Camera HAL reports partial frame or drops frame
7. If errors exceed threshold → PHY reset, lane re-initialization
8. Persistent CRC errors → signal integrity issue (PCB, cable, EMI)

---

### Q21. How do you configure CSIPHY timing (T_HS_SETTLE)?
**A:** T_HS_SETTLE determines when the receiver starts sampling HS data after SoT. It must match the transmitter's timing:
- Too short: Sample during unstable transition → bit errors
- Too long: Miss initial data → sync failure

```
Configuration (Qualcomm):
  - In device tree: qcom,csi-phy-settle = <value>;
  - Or dynamically in sensor driver: set via CCI or HAL property
  - Typical range: 0x0D – 0x1F (depends on data rate)
  
Calculation:
  T_HS_SETTLE ≈ T_HS_PREPARE(max) + T_HS_ZERO/2
  Must be within [85ns + 6×UI, 145ns + 10×UI] per D-PHY spec
```

---

### Q22. Explain embedded data in CSI-2 frames.
**A:** Sensor transmits non-pixel metadata as separate lines (DT=0x12, Embedded 8-bit) typically at the start of each frame (after FS, before image lines):
- **Content**: Exposure time, gain, sensor temperature, frame counter, register dump, histogram, PDAF data
- **Purpose**: ISP uses per-frame metadata for accurate 3A processing
- **Format**: Sensor-specific byte packing (varies by manufacturer)
- **Bandwidth**: Typically 2-8 lines × line_width bytes per frame
- **Parsing**: Driver/HAL must know sensor-specific embedded data format

---

### Q23. What is the difference between continuous and non-continuous clock?
**A:**
- **Continuous clock**: CLK lane stays in HS mode throughout the entire frame (between line packets too). Simpler for receiver (CDR always locked). Higher power.
- **Non-continuous clock**: CLK lane returns to LP-11 between line packets. Saves power. Receiver must re-lock CDR for each line (adds overhead: T_CLK-PRE, T_CLK-PREPARE, T_CLK-ZERO per line). Most automotive sensors use non-continuous to save power.

---

### Q24. How does multi-camera frame sync work in automotive?
**A:**
- **Hardware sync (FSIN/XVS)**: SoC GPIO generates periodic pulse (at frame rate). All sensors connect their Frame Sync Input pin to this signal. On rising edge, all sensors start exposure simultaneously. Result: <1 line period sync accuracy.
- **Requirement**: Surround-view stitching needs all 4 cameras within <1ms. At 60 km/h, 1 frame (33ms) = 56 cm of movement. Without sync, stitching has visible artifacts.
- **With SerDes**: GMSL2 can tunnel GPIO for sync signal over the coax cable.

---

# PART C: SENIOR / LEAD (8 Questions)

---

### Q25. Design a 7-camera automotive system on SA8295P. Specify ports, lanes, speeds, SerDes.
**A:**
```
System: 1× Front ADAS (8MP) + 4× Surround (1.3MP) + 1× Rear (2MP) + 1× DMS (2MP)

Bandwidth calculation:
  Front:    3840×2160×12×30×1.15 = 3.43 Gbps
  Surround: 1280×960×12×30×1.15  = 0.51 Gbps (×4 = 2.04 Gbps)
  Rear:     1920×1080×10×30×1.15 = 0.72 Gbps
  DMS:      1920×1080×10×30×1.15 = 0.72 Gbps

Port Assignment:
┌──────────┬───────────────┬───────┬───────────────────────────┐
│ CSI Port │ Camera(s)     │ Lanes │ Connection                 │
├──────────┼───────────────┼───────┼───────────────────────────┤
│ Port 0   │ Front 8MP     │ 4L    │ GMSL2 (MAX96717→MAX96712) │
│ Port 1   │ 4× Surround  │ 4L    │ MAX96712 (4:1 aggregator) │
│          │               │       │ VC0-3 for each camera     │
│ Port 2   │ Rear 2MP      │ 2L    │ GMSL2 (single link)       │
│ Port 3   │ DMS (interior)│ 2L    │ Direct-attach (short PCB) │
└──────────┴───────────────┴───────┴───────────────────────────┘

SerDes config:
  Port 0: MAX96712 (1 active link) → Front camera
  Port 1: MAX96712 (4 active links) → 4 surround cameras
  Port 2: MAX96714 (single deser) → Rear camera
  Port 3: No SerDes (direct sensor on PCB)

Lane speeds:
  Port 0: 4L × 1.5 Gbps = 6.0 Gbps (56% util for 3.43 Gbps) ✓
  Port 1: 4L × 1.5 Gbps = 6.0 Gbps (34% util for 2.04 Gbps) ✓
  Port 2: 2L × 1.5 Gbps = 3.0 Gbps (24% util for 0.72 Gbps) ✓
  Port 3: 2L × 1.5 Gbps = 3.0 Gbps (24% util for 0.72 Gbps) ✓
```

---

### Q26. How would you debug frame drops in a multi-camera automotive system?
**A:**
```
Systematic approach:

1. IDENTIFY which camera drops frames:
   - Check each CSID frame counter: cat /sys/devices/.../csid_debug
   - Compare frame_count vs expected (FPS × time)
   - Use V4L2: ioctl(VIDIOC_G_CTRL, V4L2_CID_FRAME_COUNTER)

2. CHECK CSI-2 ERRORS:
   - dmesg | grep -i "csi\|phy\|csid\|err"
   - CRC errors → signal integrity
   - ECC errors → EMI or PHY timing
   - FIFO overflow → downstream bottleneck

3. CHECK DDR BANDWIDTH:
   - All cameras writing to DDR simultaneously
   - 7 cameras × ~0.5-3 Gbps = heavy DDR load
   - Tool: Qualcomm bandwidth profiler
   - If DDR saturated → FIFO overflow → frame drop

4. CHECK ISP/VFE UTILIZATION:
   - IFE has finite processing rate
   - If multiple high-res cameras share one IFE → starvation
   - Verify IFE clock rate sufficient

5. CHECK SYNC SIGNALS:
   - Hardware frame sync (XVS) timing
   - If sync pulse missing → sensor free-runs → drift
   - Oscilloscope on FSIN pin

6. CHECK THERMAL THROTTLING:
   - At high temp, clock may reduce → lower bandwidth
   - cat /sys/class/thermal/thermal_zone*/temp
   - If throttling → frame drops occur at high temperature

7. FIX PRIORITY:
   a) DDR bandwidth: Enable UBWC compression (saves 40% BW)
   b) Signal integrity: Fix SerDes link (check cable/connector)
   c) ISP config: Ensure correct IFE assignment per camera
   d) Timing: Verify PHY settle time matches sensor data rate
```

---

### Q27. Explain CSI-2 scrambling (v4.0) and its automotive significance.
**A:**
```
Problem: Camera pixel data often has repetitive patterns:
  - Blue sky: Many similar pixel values in a row
  - Black regions: Long runs of zeros
  - These create spectral peaks in EMI → fail automotive CISPR 25 testing

Solution: CSI-2 v4.0 Scrambling
  - LFSR (Linear Feedback Shift Register) generates pseudorandom sequence
  - XOR with payload before transmission
  - Receiver XORs again with same LFSR to recover original data
  - LFSR seed: Re-initialized per packet (deterministic, no key exchange)

Benefits:
  - Whitened data → flat EMI spectrum → passes EMC tests
  - No bandwidth overhead (same data rate)
  - No latency (combinational XOR operation)
  
Automotive significance:
  - Cars must pass CISPR 25 (up to 2.5 GHz)
  - 8 cameras × 2.5 Gbps = significant EMI source
  - Without scrambling: Specific frequencies (data rate harmonics) spike
  - With scrambling: Energy spread across spectrum → below limits
  - Becoming mandatory for new automotive camera designs
```

---

### Q28. Design the camera power management for an automotive system.
**A:**
```
Requirements:
- Fast startup (rear-view camera must display within 2 seconds of reverse gear)
- Low standby power (parked vehicle)
- Thermal management (-40°C to +85°C ambient)
- Graceful degradation (if one camera fails, others continue)

Power States:
┌─────────────┬─────────────────┬──────────────────────────────┐
│ Vehicle State│ Camera State    │ Power                         │
├─────────────┼─────────────────┼──────────────────────────────┤
│ Ignition OFF│ Full power down  │ 0 mW (regulators off)        │
│ ACC/Standby │ Sensors standby  │ ~10 mW/camera (I2C alive)   │
│ IGN ON      │ Full streaming   │ ~500 mW/camera (sensor+PHY) │
│ Reverse gear│ Rear: instant-on │ Rear pre-warmed in standby  │
│ Parking     │ Surround: active │ 4 cams full power            │
└─────────────┴─────────────────┴──────────────────────────────┘

Fast startup strategy for rear camera:
  1. Keep rear camera in standby (sensor powered, streaming stopped)
  2. On reverse gear signal: Send stream-start via CCI (1 register write)
  3. First frame available: 1-2 frame periods (~33-66 ms)
  4. Total: < 150 ms from gear shift to display

Power sequencing per camera:
  - Controlled by PMIC GPIOs + Linux regulator framework
  - Each camera has independent power domain
  - If one camera fails (over-temperature) → isolate without affecting others

Thermal protection:
  - Monitor sensor temperature (via embedded data)
  - If > 105°C: Reduce frame rate (30→15 fps) to lower power
  - If > 120°C: Shut down camera, display "Camera unavailable"
```

---

### Q29. How would you handle a CSIPHY lane sync failure?
**A:**
```
Symptom: No frames received. CSIPHY reports "lane sync failure" or "SoT error"

Root causes:
1. PHY timing mismatch (T_HS_SETTLE wrong)
2. Lane polarity swapped (Dp/Dn crossed on PCB)
3. Lane mapping wrong (Lane 0 on sensor → Lane 2 on SoC)
4. Data rate mismatch (sensor running at speed SoC doesn't expect)
5. Sensor not actually streaming (mode_select not set)
6. Power sequencing incomplete (sensor not fully initialized)
7. SerDes link not established (GMSL lock not achieved)

Debug procedure:
1. Verify sensor streaming:
   i2ctransfer -f -y <bus> w2@<addr> 0x01 0x00 r1
   → Read mode_select, should be 0x01

2. Check CSIPHY status registers:
   cat /sys/devices/.../csiphy_status
   → Lane state should be LP-11 (if idle) or transitioning

3. Verify lane count matches:
   Sensor configured for: 2 lanes (csi_lane_mode register)
   SoC expecting: device tree "data-lanes = <1 2>;"

4. Check settle time:
   Adjust qcom,csi-phy-settle up/down
   Start with datasheet recommendation, tune ±2 steps

5. Check polarity:
   Device tree: "lane-polarities = <0 0 0>;"
   Some PCBs swap P/N for routing → set polarity inversion

6. Oscilloscope: Probe CLK and D0 at SoC pins
   → Should see LP-11 idle, periodic HS bursts
   → If no activity: Problem is upstream (sensor/SerDes)

Recovery:
  - Software reset CSIPHY
  - Re-initialize sensor (full power cycle if needed)
  - Restart streaming
```

---

### Q30. Explain C-PHY advantages and when to choose it over D-PHY.
**A:**
```
C-PHY advantages:
  1. Higher bandwidth per pin: 2.28 bits/wire/symbol vs 1 bit/wire/bit (D-PHY)
  2. No dedicated clock lane: Saves 2 pins
  3. For same bandwidth: Fewer total wires
     Example: 6 Gbps needs 4L D-PHY (10 pins) vs 2-trio C-PHY (6 pins)
  4. Embedded clock: No clock routing, no clock-data skew

When to choose C-PHY:
  - Very pin-constrained packages (small sensor modules)
  - Need high bandwidth but limited connector pins
  - Newer high-resolution sensors (50+ MP smartphones)

When to choose D-PHY:
  - Better signal integrity at distance (differential easier to route)
  - More SoC support (universal D-PHY, C-PHY less common)
  - Automotive (D-PHY dominates, SerDes designed for D-PHY)
  - Lower EMI (true differential pairs easier to control)
  - Simpler receiver design (standard CDR)

Automotive recommendation: D-PHY
  - Industry inertia: All SerDes chips (GMSL2, FPD-Link) interface D-PHY
  - Proven in automotive EMC environment
  - 4-lane D-PHY at 2.5 Gbps (10 Gbps) sufficient for current sensors
  - C-PHY may come for future 16+ MP automotive cameras
```

---

# PART D: STAFF / ARCHITECT (5 Questions)

---

### Q31. Architect a camera subsystem for an L3 autonomous driving platform with 12 cameras.
**A:**
```
Requirements:
- 12 cameras: 3× front (8MP telephoto, 4MP wide, 2MP fisheye)
                4× surround (2MP fisheye)
                2× side-mirror replacement (2MP)
                2× rear (2MP + 8MP)
                1× DMS interior (2MP)
- All cameras: RAW12, 30 fps minimum
- Front telephoto + wide: 60 fps (for ADAS at speed)
- Latency: Sensor-to-NPU < 50 ms
- Synchronization: < 1 ms across all exterior cameras

Architecture:
┌──────────────────────────────────────────────────────────────────┐
│ COMPUTE PLATFORM (Dual SA8295P or SA8650P)                        │
│                                                                    │
│ SoC-A (ADAS processing):                                          │
│  Port 0: MAX96712 ← Front telephoto 8MP (4L, 60fps) → 6.9 Gbps │
│  Port 1: MAX96712 ← Front wide 4MP + fisheye 2MP (2 links)     │
│  Port 2: MAX96712 ← Rear 8MP + 2MP (2 links)                    │
│  Port 3: MAX96712 ← 2× side-mirror (2 links)                    │
│                                                                    │
│ SoC-B (Surround + DMS):                                           │
│  Port 0: MAX96712 ← 4× Surround fisheye (4 links, VC0-3)       │
│  Port 1: Direct-attach DMS (2L)                                   │
│  Port 2: Spare (future sensors / lidar camera fusion)            │
│                                                                    │
│ Inter-SoC: PCIe/Ethernet for frame sharing                       │
│                                                                    │
│ Synchronization:                                                   │
│  • Master clock from SoC-A GPIO → all 12 FSIN (via SerDes GPIO) │
│  • GMSL2 tunnels sync + timestamp to each camera                 │
│  • < 1 line sync accuracy across all cameras                     │
│                                                                    │
│ Processing:                                                        │
│  • IFE: Real-time ISP (demosaic, 3A stats)                       │
│  • DDR: Frame buffers (UBWC compressed → save 40% BW)            │
│  • NPU: Object detection, lane detection, sign recognition       │
│  • GPU: Surround-view 3D stitching, rendering                    │
│  • Fusion: Combine multi-camera detections (tracking)            │
│                                                                    │
│ Redundancy:                                                        │
│  • Dual SoC: If one fails, other maintains basic ADAS            │
│  • Camera health monitoring: CRC error rate per camera           │
│  • Degraded mode: If camera fails → inform driver, adjust ADAS  │
└──────────────────────────────────────────────────────────────────┘
```

---

### Q32. How would you validate signal integrity for CSI-2 in an automotive environment?
**A:**
```
Validation Plan:

1. PRE-SILICON (Simulation):
   - IBIS model simulation of D-PHY drivers
   - SI simulation: Trace impedance (100Ω diff), length matching (<1mm skew)
   - Eye diagram simulation at receiver (must pass D-PHY mask)
   - Crosstalk: Between CSI-2 lanes and adjacent signals
   - EMI: Near-field simulation of cable/connector radiation

2. BENCH VALIDATION:
   a) Eye diagram measurement:
      - Instrument: Oscilloscope with D-PHY decoder (≥ 20 GHz BW for 4.5 Gbps)
      - Probe at SoC input (after SerDes/cable)
      - Measure: Eye height (≥60 mV), eye width (≥0.35 UI), jitter
   
   b) Timing compliance:
      - Measure all D-PHY timing parameters vs spec
      - T_HS-PREPARE, T_HS-ZERO, T_HS-TRAIL, T_CLK-PRE/POST
      - Tool: MIPI D-PHY compliance test software
   
   c) Protocol verification:
      - Capture CSI-2 packets with protocol analyzer
      - Verify: Correct DT, WC, ECC, CRC for each packet
      - Verify: FS/FE sequence, VC assignments

3. ENVIRONMENTAL TESTING:
   - Temperature sweep: -40°C to +105°C
   - At each temperature: Run 1-hour continuous streaming
   - Monitor: CRC error rate, frame drops, eye margin
   - Vibration: Random vibration profile (automotive spec)
   - Connector: Verify contacts maintain signal integrity under vibration

4. EMC TESTING:
   - Radiated emissions: CISPR 25 (automotive) up to 2.5 GHz
   - Immunity: Bulk current injection (BCI) on camera cables
   - ESD: ±8 kV contact, ±15 kV air (IEC 61000-4-2)

5. LONG-TERM RELIABILITY:
   - HTOL (High Temperature Operating Life): 1000 hrs at 125°C
   - Thermal cycling: -40↔125°C, 1000 cycles
   - Monitor: CRC error rate trending over time
   
Pass criteria:
   - Zero CRC errors in 24-hour streaming at 25°C
   - < 1 CRC error per 10^12 bits over temperature
   - Eye margin > 30% at worst-case temperature
```

---

# PART E: QUICK-FIRE (15 Questions)

| # | Question | Answer |
|---|----------|--------|
| 1 | CSI-2 direction? | Unidirectional: Sensor → SoC (camera to host) |
| 2 | D-PHY max speed (v2.5)? | 4.5 Gbps per lane |
| 3 | Max data lanes (D-PHY)? | 4 lanes |
| 4 | Short packet size? | 4 bytes (header only, no payload) |
| 5 | ECC can correct how many bits? | 1 bit (in header) |
| 6 | CRC polynomial? | CRC-CCITT (x¹⁶+x¹²+x⁵+1) |
| 7 | LP-11 means? | Stop state (idle) — both lines at 1.2V |
| 8 | VC field width (standard)? | 2 bits (4 virtual channels) |
| 9 | RAW10 bytes for 1920 pixels? | 2400 bytes (1920×10/8) |
| 10 | Most common automotive DT? | RAW10 (0x2B) or RAW12 (0x2C) |
| 11 | What starts a frame? | Frame Start short packet (DT=0x00) |
| 12 | Automotive SerDes examples? | GMSL2 (ADI), FPD-Link III/IV (TI) |
| 13 | Qualcomm ISP blocks? | CSIPHY → CSID → IFE → BPS → IPE |
| 14 | Linux camera framework? | V4L2 + Media Controller |
| 15 | D-PHY HS entry sync byte? | 0xB8 |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
