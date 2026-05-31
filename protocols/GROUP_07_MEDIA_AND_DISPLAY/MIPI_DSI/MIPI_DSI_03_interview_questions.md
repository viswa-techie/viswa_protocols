# MIPI DSI — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (10 Questions)

---

### Q1. What is MIPI DSI?
**A:** MIPI DSI (Display Serial Interface) is a high-speed serial protocol for connecting a display controller (SoC) to a display panel. It uses differential D-PHY signaling with 1-4 data lanes, supporting up to 4.5 Gbps per lane. Unlike legacy parallel RGB interfaces (24+ pins), DSI needs only 2-10 pins. It carries both pixel data (HS mode) and panel control commands (LP mode) on the same link.

---

### Q2. What is the difference between Video Mode and Command Mode?
**A:**
- **Video Mode**: Host continuously streams pixels every frame (like HDMI). Panel has no frame buffer — if host stops sending, display goes blank. Host controls all timing.
- **Command Mode**: Host writes pixels to panel's internal GRAM. Panel self-refreshes from GRAM at its own rate. Host only sends data when content changes (saves power). Uses TE signal to avoid tearing.

---

### Q3. What is DCS?
**A:** Display Command Set — a standardized set of commands for controlling any MIPI DSI panel. Commands like `exit_sleep_mode` (0x11), `set_display_on` (0x29), `set_brightness` (0x51) work across all DSI panels. Manufacturer-specific commands (0xB0-0xFF) handle panel-specific features (gamma, voltage). DCS commands are sent as DSI short/long packets in LP mode.

---

### Q4. How does DSI differ from CSI-2?
**A:**
- **Direction**: DSI is Host→Display (bidirectional on Lane 0 for reads). CSI-2 is Sensor→Host (unidirectional).
- **Control**: DSI commands travel on the same DSI link (LP mode). CSI-2 uses separate I2C/CCI.
- **Content**: DSI carries display pixels (RGB). CSI-2 carries camera pixels (RAW/YUV).
- **BTA**: DSI has Bus Turnaround for panel reads. CSI-2 has no read mechanism.

---

### Q5. What is EoTp and why is it needed?
**A:** End of Transmission Packet — a short packet (DT=0x08) sent after every HS data burst. It tells the panel receiver "this HS transmission is complete, I'm returning to LP." The panel uses it to:
- Confirm all expected data was received
- Detect if packets were lost (no EoTp = error)
- Prepare for LP state transition

---

### Q6. What are HS and LP modes in DSI?
**A:**
- **HS (High-Speed)**: Differential 200 mV signaling. Used for pixel data transmission at high bandwidth (up to 4.5 Gbps/lane). All lanes used.
- **LP (Low-Power)**: Single-ended 1.2V signaling. Used for DCS commands, BTA, and idle state. Only Lane 0 used for LP data. Much slower (~10 Mbps) but very low power.

---

### Q7. What is the Tearing Effect (TE)?
**A:** In command mode, tearing occurs when host writes to GRAM while panel reads from the same area — causing a visible horizontal line where old and new content meet. TE is a signal from panel to host indicating when it's safe to write (during panel's vertical blanking). Host waits for TE before sending new frame data.

---

### Q8. What pixel formats does DSI support?
**A:**
- RGB565 (16-bit): 5R + 6G + 5B — low bandwidth, reduced color
- RGB666 (18-bit): 6R + 6G + 6B — packed or loosely packed (24-bit aligned)
- RGB888 (24-bit): 8R + 8G + 8B — standard full color
- RGB101010 (30-bit): 10 bits per channel — HDR displays
- RGB121212 (36-bit): 12 bits per channel — professional

---

### Q9. What is ULPS?
**A:** Ultra-Low Power State — the lowest power state for DSI PHY while maintaining the link. CLK and data lanes enter LP-00. Power consumption drops to ~10 µA per lane. Used in command mode when panel refreshes from GRAM and host has nothing to send. Exit requires ~1 ms wake-up sequence.

---

### Q10. What are the video mode sync packets?
**A:**
- **VSS** (V-Sync Start): Frame begins
- **VSE** (V-Sync End): Vertical sync done
- **HSS** (H-Sync Start): Line begins
- **HSE** (H-Sync End): Horizontal sync done (Sync Pulse mode only)

These short packets mark timing boundaries. Panel uses them to synchronize its internal scanning with the incoming pixel data.

---

# PART B: MID-LEVEL (10 Questions)

---

### Q11. Explain the three video mode sub-types.
**A:**
1. **Non-Burst Sync Pulse**: HSS + blanking + HSE + pixel data sent at display rate. Panel receives explicit sync pulse edges (HSS→HSE duration = HSA). Most deterministic timing.

2. **Non-Burst Sync Event**: Only HSS (no HSE). Panel infers sync timing from HSS position. Slightly less overhead. Same effective rate as display.

3. **Burst Mode**: Pixel data sent at maximum PHY speed (faster than display needs). After data, PHY goes to LP until next line. Saves power (LP gaps) but requires precise line-timing control. Most common in smartphones.

---

### Q12. Calculate DSI bandwidth for 1920×720 cluster at 60 Hz, RGB888, 4 lanes.
**A:**
```
Active data: 1920 × 720 × 24 × 60 = 1.99 Gbps
Timing overhead (HBP=36, HFP=100, HSA=4, VBP=16, VFP=16, VSA=4):
  H_total = 1920 + 36 + 100 + 4 = 2060
  V_total = 720 + 16 + 16 + 4 = 756
  Blanking OH = (2060×756)/(1920×720) - 1 = 12.7%
  Total: 1.99 × 1.127 = 2.24 Gbps

Available: 4 lanes × 700 Mbps = 2.8 Gbps
Utilization: 2.24 / 2.8 = 80% ✓ (acceptable)

Minimum lane speed: 2.24 / 4 = 560 Mbps per lane
```

---

### Q13. What is BTA and when is it used?
**A:** Bus Turnaround reverses direction on Data Lane 0 so the panel can send data to the host. Sequence:
1. Host sends LP-10 on D0 (BTA request)
2. Host tri-states D0 driver
3. Panel drives LP-10 (acknowledge)
4. Panel sends response packet in LP
5. Panel returns D0 to LP-11, host resumes driving

Used for: DCS read commands (read panel ID, brightness, status), error reporting. BTA adds ~1-2 µs overhead per read. Avoid during active video if possible.

---

### Q14. Describe the panel initialization sequence.
**A:**
```
1. Power on: VCI (3.3V analog) → VDDI (1.8V digital) → wait 5ms
2. Reset: RESET_N pulse LOW (>10µs) → HIGH → wait 10ms
3. DSI LP-11: Host puts lanes in idle state → wait 100µs (T_INIT)
4. Exit sleep: DCS 0x11 → wait 120ms (panel PLL + GRAM init)
5. Configure: Pixel format (0x3A=0x77 for RGB888)
   Panel-specific: Gamma, voltage, timing (manufacturer commands)
6. Display on: DCS 0x29 → wait 20ms
7. Start video: Host begins HS pixel streaming
8. Backlight on: Enable PWM/WLED (after first frame visible)
```
Total: ~165 ms from power-on to visible display.

---

### Q15. How does continuous splash (seamless boot) work?
**A:**
- **Bootloader**: Initializes DSI, panel, shows splash image within 100-200ms of power-on
- **Kernel**: Detects "cont-splash" flag. Does NOT reset DSI PHY or panel. Inherits existing configuration. Only updates framebuffer pointer.
- **Result**: Zero flicker from bootloader → kernel → Android boot animation

Key: Kernel display driver must read current DSI state and match it, rather than doing full re-initialization. Device tree: `qcom,mdss-dsi-cont-splash-enabled;`

---

### Q16. What is DSC and when do you need it?
**A:** Display Stream Compression — VESA standard for visually lossless compression. Needed when raw pixel bandwidth exceeds DSI capacity:
- 2560×1440 @ 60 Hz RGB888 = 5.3 Gbps (exceeds 4L × 1 Gbps = 4 Gbps)
- With DSC 2:1 compression: 2.65 Gbps → fits in 4L × 1 Gbps

DSC compresses on SoC side, panel's driver IC decompresses. Both must support same DSC version. Adds minimal latency (1-2 lines). Compression ratio configurable (2:1, 2.5:1, 3:1).

---

### Q17. Explain Dual-DSI and when it's needed.
**A:** Two approaches:
1. **Split display**: DSI0 drives left half, DSI1 drives right half. Each at half-width. Panel has two receiver ports.
2. **Bonded DSI**: Two controllers act as one 8-lane logical link.

Needed when: Single DSI bandwidth insufficient.
Example: 3840×2160 @ 60 Hz RGB888 = 11.9 Gbps. Single DSI max = 10 Gbps at 2.5 Gbps/lane. Solution: Dual-DSI at ~6 Gbps each, or single DSI + DSC.

---

### Q18. How do you handle multiple displays in automotive (SA8295P)?
**A:**
```
SA8295P supports:
  DSI0: Instrument cluster (1920×720, 60Hz) — safety-critical
  DSI1: Center IVI (2560×1440, 60Hz) — infotainment
  DP0:  HUD projector or rear-seat — via DP/HDMI bridge

Architecture:
  • Separate DPU pipes per display (independent timing, content)
  • Content isolation: Cluster pipe cannot be affected by IVI crash
  • Priority: Cluster gets guaranteed bandwidth/processing
  • Watchdog: If DSI0 stops, hardware fail-safe shows warning
  • Fast boot: Cluster visible within 1 second (bootloader init)
```

---

### Q19. What errors can a DSI panel report?
**A:** Panel sends Acknowledge & Error Report (16-bit error field) after BTA:
- Bit 0-2: SoT/EoT sync errors (PHY timing issues)
- Bit 5: Peripheral timeout (panel too slow to process)
- Bit 8: ECC single-bit (corrected in header)
- Bit 9: ECC multi-bit (header corrupted)
- Bit 10: CRC error (payload corrupted)
- Bit 11: Data type not recognized
- Bit 13: Invalid transmission length

Host should periodically check panel health by doing BTA reads of status registers.

---

### Q20. How do you debug a black screen on DSI?
**A:**
```
Systematic checklist:
1. Power: Check regulator voltages (VCI 3.3V, VDDI 1.8V)
2. Reset: Verify RESET_N toggled correctly (scope)
3. DSI PHY: Is PLL locked? Check PHY status register.
4. LP-11: Are lanes in LP-11 after init? (Scope: both P/N at 1.2V)
5. DCS commands: Were init commands sent? (DSI controller FIFO status)
6. Panel response: Do BTA read → get panel ID. If no response → I2C? power?
7. Video mode: Is DPU generating frames? Check VSYNC interrupt firing.
8. Timing: Are timing parameters (HBP/HFP/VSA) correct for this panel?
9. Backlight: Is backlight actually on? (Common miss — display works but dark!)
10. Format: Is pixel format matching panel expectation (RGB888 vs RGB666)?
```

---

# PART C: SENIOR / LEAD (7 Questions)

---

### Q21. Design the display subsystem for an automotive cockpit with 3 displays.
**A:**
```
Requirements:
  • Cluster: 1920×720, 60Hz, ASIL-B, <1s startup, safety warnings
  • IVI: 2560×1440, 60Hz, touch, navigation, media
  • HUD: 1280×480, 60Hz, ultra-low latency (<16ms), transparent combiner

Hardware architecture (SA8295P):
┌─────────────────────────────────────────────────────────┐
│ DPU: 3 independent pipelines                             │
│   Pipe0 (Cluster): SSPP0+SSPP1 → LM0 → DSPP0 → INTF0 │
│   Pipe1 (IVI): SSPP2+SSPP3 → LM1 → DSPP1 → INTF1     │
│   Pipe2 (HUD): SSPP4 → LM2 → INTF2                    │
└─────────────────────────────────────────────────────────┘

Display connections:
  Cluster: DSI0, 4 lanes @ 700 Mbps (2.24 Gbps needed)
  IVI: DSI1, 4 lanes @ 1.5 Gbps + DSC 2:1 (2.65 Gbps compressed)
  HUD: DP0 → DP-to-DSI bridge → small DSI panel (or direct LVDS)

Safety:
  • Cluster pipeline isolated from IVI (separate DPU resources)
  • Hardware watchdog on INTF0: If no VSYNC for 100ms → show fail-safe
  • Continuous splash: Cluster visible from bootloader
  • CRC check: DPU computes frame CRC, compared against expected
  
Latency (HUD):
  • Single buffer mode (no double-buffering delay)
  • Direct path: GPU → DPU → DSI → Display (no intermediate copy)
  • Target: <16ms (1 frame) from render to photon
```

---

### Q22. How would you optimize display power consumption for an EV dashboard?
**A:**
```
Power optimization strategies:

1. Burst mode (DSI):
   • Send line data at max speed, LP idle between lines
   • LP gap power savings: ~15-20% vs non-burst

2. ULPS during static content:
   • Instrument cluster showing parked state → enter ULPS
   • Panel refreshes from GRAM (command mode capable panel)
   • Save 200-400 mW per DSI link in ULPS

3. DSC compression:
   • Reduce data rate → lower PHY clock → lower dynamic power
   • 2:1 DSC: Half the bit transitions → ~40% PHY power reduction

4. Adaptive refresh rate:
   • Static content: Drop to 30 Hz (cluster: speed=0, nav=stopped)
   • Active: Full 60 Hz (driving, animation)
   • Saves DPU + DSI + panel power

5. Backlight dimming:
   • Local dimming (mini-LED): Turn off zones with black content
   • CABC: Reduce backlight, boost pixel values (perceived same brightness)
   • Night mode: Minimal brightness (<5 nits) — huge savings

6. Content-aware:
   • Dark mode UI: OLED pixels off for black → save panel power
   • Reduce color depth in low-light (18-bit vs 24-bit)

7. Clock gating:
   • DPU pipes not in use: Clock-gate their LM/DSPP blocks
   • Unused DSI: Power down PHY PLL entirely

Total potential savings: 30-50% vs always-on full-brightness video mode
```

---

### Q23. Explain functional safety requirements for an instrument cluster display.
**A:**
```
ASIL-B requirements for cluster display:

1. Content Integrity:
   • DPU generates per-frame CRC of output pixels
   • Compare against expected CRC (known good frame reference)
   • Mismatch → trigger diagnostic, show fail-safe

2. Display Liveness:
   • Hardware watchdog monitors VSYNC output
   • If no VSYNC for N frames → assume display path failed
   • Fail-safe: Switch to hardware-generated warning image
   • Or: Panel shows last good frame from GRAM (command mode)

3. Communication Integrity:
   • DSI ECC/CRC detects transmission errors
   • Periodic BTA reads to verify panel alive and responding
   • Panel reports error flags → host logs and handles

4. Startup Requirements:
   • Cluster visible within 1 second of ignition
   • Show at minimum: Speed, critical warnings (brake, engine)
   • Bootloader renders minimal cluster (no OS dependency)

5. Independence:
   • Cluster pipeline fully independent from IVI
   • IVI crash/reset must NOT affect cluster display
   • Separate DPU pipes, separate DSI controllers
   • Hardware-enforced content isolation (firewall)

6. Degraded Mode:
   • If DPU fails: External fail-safe IC drives minimal warnings
   • If DSI link fails: Panel shows stored fail-safe image
   • If panel fails: Tell-tales (LEDs) still operational

7. Testing:
   • FMEA on full display path (GPU→DPU→DSI→Panel)
   • Fault injection: Simulate stuck pixel, frozen frame, link failure
   • Diagnostic coverage target: >90% (ASIL-B)
```

---

### Q24. How do you achieve <2 second rear-view display time?
**A:**
```
Budget breakdown (camera-to-display):
  Total: < 2000 ms from reverse gear signal

  Power-on sequence: 
    Camera power: 50 ms (pre-powered in standby)
    Panel power: 50 ms (pre-powered in standby)
    
  Camera first frame:
    Sensor init: 10 ms (already standby, just start stream)
    First frame: 33 ms (one frame period at 30 fps)
    ISP processing: 16 ms (one pipeline stage)
    
  Display first frame:
    DSI init: 5 ms (cont-splash, already running)
    DPU pipe switch: 1 ms (point to camera buffer)
    Panel latency: 16 ms (one display frame)
    
  Total theoretical: ~181 ms ← Well within 2s!

Key techniques:
  1. Keep camera + panel in STANDBY (not full power-off)
     • Sensor: mode_select=0 but powered (saves 50ms power-on)
     • Panel: Display off but GRAM retained + DSI link alive
     
  2. Dedicated fast path:
     • ADAS IFE always allocated for rear camera
     • DPU plane pre-configured for camera resolution
     • Just flip buffer pointer when first frame ready
     
  3. No OS dependency:
     • Bootloader can show rear camera (bypass Android entirely)
     • Hardware trigger: Reverse gear GPIO → start camera + display
     
  4. Optimized init sequence:
     • Skip full DCS init (panel already initialized)
     • Skip 120ms sleep-exit (panel never fully slept)
     
  Achieved: 150-300 ms typical (regulation requires < 2000 ms)
```

---

### Q25. Debug display flickering in automotive temperature range.
**A:**
```
Symptom: Display flickers at -20°C or +80°C but works at 25°C.

Root cause analysis:

1. PHY timing drift with temperature:
   • D-PHY PLL frequency shifts with temperature
   • T_HS_SETTLE timing changes (analog circuits)
   • Fix: Widen timing margins, use temperature-compensated PLL
   • Verify: Read PLL lock status at temperature extremes

2. Panel response time:
   • LCD liquid crystal response slower at cold temperatures
   • Pixels can't fully switch in one frame → ghosting/flicker
   • Fix: Increase panel drive voltage at cold temp, or
     reduce frame rate temporarily until warm

3. Voltage regulator stability:
   • Power supply output varies with temperature
   • Panel AVDD/VGH/VGL may go out of spec at extremes
   • Fix: Verify regulator output at temperature corners
   • Check: Panel datasheet voltage tolerances

4. DSI signal integrity:
   • PCB impedance changes with temperature (FR4 Dk varies)
   • Eye margin reduces at temperature extremes
   • Fix: Design with >30% eye margin at room temp
   • Verify: Eye diagram at -40°C and +105°C

5. Backlight driver:
   • LED efficiency drops at high temperature
   • PWM timing may drift (oscillator frequency shift)
   • Fix: Use temperature-compensated oscillator, or I2C WLED

Debug approach:
   a) Thermal chamber with display + scope access
   b) Sweep -40°C to +85°C, monitor for errors
   c) Log DSI error interrupts vs temperature
   d) Measure eye diagram at temperature corners
   e) Identify first failure temperature → root cause
```

---

### Q26. How would you implement a DSI-based e-mirror (camera → display pipeline)?
**A:**
```
E-Mirror: Replace physical side mirrors with camera + display

Requirements:
  • Camera: 1920×480 (wide aspect), 60 fps, HDR
  • Display: 1920×480 DSI panel (matches camera exactly)
  • Latency: < 100 ms (driver perception requirement)
  • Startup: < 2 seconds
  • Redundancy: Fallback if system fails

Pipeline:
  Camera(CSI-2) → CSIPHY → CSID → IFE → DDR → DPU → DSI → Panel
  
Latency budget:
  Sensor exposure: 16 ms (1/60s)
  Sensor readout: 8 ms
  ISP processing: 8 ms (IFE only, no BPS/IPE — minimal processing)
  DDR round-trip: 2 ms
  DPU → DSI: 8 ms (one display line scan period)
  Total: ~42 ms (well within 100 ms)

Optimization:
  • Skip BPS/IPE (no heavy post-processing for mirror)
  • IFE → DDR → DPU direct (no encoding/decoding)
  • Single buffer (accept occasional tearing vs latency)
  • Or: Double buffer with immediate flip on camera VSYNC

Startup:
  • Camera in standby (not full power-off)
  • Panel in standby (DSI link alive, display off)
  • On ignition: Start camera + display_on → <200ms to image

Redundancy:
  • Physical mirror position still functional (fold-out)
  • Watchdog: If no new frame for 100ms → alert driver
  • Fail-safe: Show last frame + "Camera error" overlay
```

---

### Q27. Explain DSI scrambling and EMC considerations.
**A:**
```
Problem:
  • Pixel data has patterns (solid colors, gradients)
  • Repetitive bit patterns → spectral peaks in EMI
  • Automotive: Must pass CISPR 25 (radiated emissions)
  • DSI at 2.5 Gbps × 4 lanes = significant RF source

DSI-2 Scrambling:
  • LFSR-based pseudo-random XOR on payload data
  • Whitens data → flat spectrum → lower peak EMI
  • Re-initialized per packet (deterministic, no negotiation)
  • Zero bandwidth overhead (just XOR in data path)

Additional EMC techniques:
  1. Spread-spectrum clocking (SSC):
     • Modulate PLL ±0.5-1% over 30-50 kHz triangle
     • Spreads clock harmonics → reduces peak by 6-10 dB
     
  2. Transition minimization:
     • Invert data for long sequences of same bit → fewer transitions
     • DSI controller can optionally invert polarity
     
  3. Non-continuous clock:
     • Clock returns to LP between lines → lower average EMI
     • But adds entry/exit overhead per line
     
  4. Shielding:
     • FPC (Flexible Printed Circuit) with ground planes
     • Shielded connector (automotive-grade, spring contacts)
     
  5. PCB routing:
     • Tight differential coupling (reduce radiation)
     • Guard traces between DSI and other signals
     • Short traces (< 10 cm ideally)

Testing:
  • Pre-compliance: Near-field scan over DSI connector
  • Full compliance: CISPR 25 chamber test (150 kHz - 2.5 GHz)
  • Design margin: Target 6 dB below limit
```

---

# PART D: QUICK-FIRE (10 Questions)

| # | Question | Answer |
|---|----------|--------|
| 1 | DSI direction? | Host → Panel (bidirectional on Lane 0 for reads) |
| 2 | D-PHY max speed? | 4.5 Gbps per lane (v2.5) |
| 3 | Max data lanes? | 4 |
| 4 | DCS exit_sleep_mode code? | 0x11 |
| 5 | DCS set_display_on code? | 0x29 |
| 6 | Sleep exit delay? | 120 ms (minimum) |
| 7 | EoTp data type? | 0x08 |
| 8 | Packed Pixel 24-bit DT? | 0x3E |
| 9 | What does BTA stand for? | Bus Turnaround |
| 10 | ULPS current per lane? | ~10 µA |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
