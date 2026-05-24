# MIPI DSI — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 04 of 08
# Format: Q (front) / A (back) — 180+ cards in 7 decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: FUNDAMENTALS (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | What does DSI stand for? | Display Serial Interface (MIPI Alliance standard) |
| 2 | DSI data direction? | Host → Peripheral (bidirectional on Lane 0 for reads) |
| 3 | DSI first version year? | 2006 (DSI v1.0) |
| 4 | Latest DSI version? | DSI-2 v2.1 (2022) |
| 5 | Who defines DSI? | MIPI Alliance |
| 6 | What is the Host in DSI? | SoC/Display controller (transmitter) |
| 7 | What is the Peripheral? | Display panel / driver IC (receiver) |
| 8 | Max D-PHY lanes for DSI? | 4 data lanes + 1 clock lane |
| 9 | Max speed per lane? | 4.5 Gbps (D-PHY v2.5) |
| 10 | Two operating modes? | Video Mode and Command Mode |
| 11 | Video Mode characteristic? | Continuous pixel streaming (host controls timing) |
| 12 | Command Mode characteristic? | Write to panel GRAM, panel self-refreshes |
| 13 | What replaced DSI? | Legacy parallel RGB/DPI (24+ pins) |
| 14 | DSI wire count (4-lane)? | 10 differential + RESET + TE = 12 |
| 15 | How are commands sent? | DCS packets in LP mode on same DSI link |
| 16 | What is DCS? | Display Command Set — standard panel commands |
| 17 | LP mode speed? | ~10 Mbps (low power, single-ended 1.2V) |
| 18 | HS mode speed? | 80 Mbps – 4.5 Gbps per lane (differential 200mV) |
| 19 | Panel control in CSI-2? | Separate I2C/CCI bus |
| 20 | Panel control in DSI? | Same link (LP mode DCS commands) |
| 21 | What is EoTp? | End of Transmission Packet — marks HS burst end |
| 22 | EoTp data type code? | 0x08 |
| 23 | DSI typical applications? | Smartphones, automotive displays, wearables, tablets |
| 24 | Alternative to DSI for display? | eDP, LVDS, HDMI |
| 25 | DSI max trace length? | < 15 cm (PCB, FPC) |
| 26 | Automotive distance solution? | SerDes (GMSL, FPD-Link) for DSI over cable |
| 27 | DSI color depth options? | 16-bit (RGB565), 18-bit (RGB666), 24-bit (RGB888), 30/36-bit |
| 28 | Most common automotive color? | RGB888 (24-bit, 8 bits per channel) |
| 29 | Is DSI a bus or point-to-point? | Point-to-point (one host, one panel) |
| 30 | Can multiple panels share DSI? | Not standard (use separate DSI ports per panel) |

---

# DECK 2: D-PHY & SIGNALING (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | D-PHY HS signaling? | Differential 200 mV swing, DDR (data on both clock edges) |
| 2 | D-PHY LP signaling? | Single-ended 0V/1.2V on each wire independently |
| 3 | LP-11 state meaning? | Stop/Idle: Both Dp=HIGH, Dn=HIGH |
| 4 | Which lane is bidirectional? | Data Lane 0 (host TX + panel LP-RX) |
| 5 | Lanes 1-3 direction? | Unidirectional: Host → Panel only |
| 6 | Clock lane direction? | Host → Panel (DDR bit clock) |
| 7 | HS entry sequence? | LP-11 → LP-01 → LP-00 → HS-0 → HS Data |
| 8 | BTA direction on D0? | Reverses: Host stops TX, panel starts LP-TX on D0 |
| 9 | Continuous clock meaning? | CLK lane stays in HS throughout frame |
| 10 | Non-continuous clock meaning? | CLK returns to LP between HS bursts (saves power) |
| 11 | ULPS entry signal? | LP-00 held on CLK and data lanes |
| 12 | ULPS exit signal? | Mark-1 (LP-10) for ≥1ms, then LP-11 |
| 13 | ULPS power consumption? | ~10 µA per lane |
| 14 | T_INIT after power-on? | ≥100 µs (LP-11 before any HS activity) |
| 15 | LP-10 meaning on D0? | BTA request (from host) or BTA acknowledge (from panel) |
| 16 | DSI vs CSI-2 PHY difference? | DSI: Lane 0 bidirectional. CSI-2: All lanes TX-only (sensor side) |
| 17 | Eye mask for DSI? | Same as CSI-2: Height ≥60mV, Width ≥0.35 UI |
| 18 | PLL in DSI PHY? | Generates bit clock from reference (e.g., 19.2 MHz → 1.5 GHz) |
| 19 | Spread-spectrum clock? | ±0.5-1% frequency modulation to reduce EMI peaks |
| 20 | Clock-data skew tolerance? | Receiver samples within ±0.25 UI of clock edge |
| 21 | DSI termination (HS)? | 100Ω differential at receiver |
| 22 | LP driver for BTA at panel? | Panel has LP-TX driver on D0 only |
| 23 | What if EoTp missing? | Panel may not properly exit HS → protocol error |
| 24 | How many wires total (4-lane)? | 10: (4 data pairs × 2) + (1 clock pair × 2) |
| 25 | Can DSI use C-PHY? | Yes (DSI-2 supports C-PHY as alternative to D-PHY) |

---

# DECK 3: PACKETS & PROTOCOL (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Short packet size? | 4 bytes: DI (1B) + Data0 (1B) + Data1 (1B) + ECC (1B) |
| 2 | Long packet structure? | PH (4B) + Payload (WC bytes) + CRC (2B) |
| 3 | ECC type? | 6-bit Hamming over 24-bit header (same as CSI-2) |
| 4 | CRC polynomial? | CRC-CCITT x¹⁶+x¹²+x⁵+1 |
| 5 | VSS (V-Sync Start) DT? | 0x01 |
| 6 | VSE (V-Sync End) DT? | 0x11 |
| 7 | HSS (H-Sync Start) DT? | 0x21 |
| 8 | HSE (H-Sync End) DT? | 0x31 |
| 9 | DCS Short Write 0-param DT? | 0x05 |
| 10 | DCS Short Write 1-param DT? | 0x15 |
| 11 | DCS Long Write DT? | 0x39 |
| 12 | DCS Read DT? | 0x06 |
| 13 | Packed Pixel RGB888 DT? | 0x3E |
| 14 | Packed Pixel RGB565 DT? | 0x0E |
| 15 | Packed Pixel RGB666 DT? | 0x1E (packed) or 0x2E (loosely packed) |
| 16 | Null packet DT? | 0x09 |
| 17 | Blanking packet DT? | 0x19 |
| 18 | Short Read Response (1B) DT? | 0x21 |
| 19 | Short Read Response (2B) DT? | 0x22 |
| 20 | Long Read Response DT? | 0x1A |
| 21 | Acknowledge & Error DT? | 0x02 |
| 22 | Max Word Count? | 65535 bytes (16-bit WC field) |
| 23 | WC for 1920 RGB888 line? | 5760 bytes (1920 × 3) |
| 24 | WC for 1920 RGB565 line? | 3840 bytes (1920 × 2) |
| 25 | When is EoTp sent? | After every HS burst (marks return to LP) |

---

# DECK 4: VIDEO MODE & TIMING (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | HSA meaning? | Horizontal Sync Active — sync pulse width (pixels) |
| 2 | HBP meaning? | Horizontal Back Porch — blanking after sync, before active |
| 3 | HFP meaning? | Horizontal Front Porch — blanking after active, before next sync |
| 4 | VSA meaning? | Vertical Sync Active — number of sync lines |
| 5 | VBP meaning? | Vertical Back Porch — blank lines after V-sync |
| 6 | VFP meaning? | Vertical Front Porch — blank lines before V-sync |
| 7 | Pixel clock formula? | H_total × V_total × FPS |
| 8 | H_total formula? | HSA + HBP + Width + HFP |
| 9 | V_total formula? | VSA + VBP + Height + VFP |
| 10 | Three video sub-modes? | Burst, Non-Burst Sync Pulse, Non-Burst Sync Event |
| 11 | Burst mode advantage? | Sends pixels fast, idles in LP → saves power |
| 12 | Non-Burst Sync Pulse sends? | HSS + blanking + HSE + blanking + pixels + blanking per line |
| 13 | Non-Burst Sync Event sends? | HSS only (no HSE) + blanking + pixels |
| 14 | What is BLLP? | Blanking or Low-Level Power period (LP idle in burst gap) |
| 15 | Burst mode constraint? | Must still send correct total pixels per frame (timing match) |
| 16 | What happens if video stops? | Panel goes blank (no GRAM in video-mode panels) |
| 17 | Pixel clock for 1080p@60? | ~148.5 MHz (standard HDTV timing) |
| 18 | Typical automotive cluster timing? | 1920×720, HSA=4, HBP=36, HFP=100, VSA=4, VBP=16, VFP=16 |
| 19 | DSI bit clock vs pixel clock? | Bit clock = Pixel clock × BPP / Lanes (for non-burst) |
| 20 | Example: 1920×720 RGB888 4L? | Bit clock = 148.5M × 24 / 4 = 891 MHz = 891 Mbps/lane (DDR: 445.5 MHz) |
| 21 | Can blanking be zero? | HFP/HBP typically ≥4 pixels minimum (panel-dependent) |
| 22 | What sync packets look like? | Short packets (4 bytes each): VSS, HSS, HSE, VSE |
| 23 | Video mode for automotive? | Yes — dominant mode for cluster and IVI (continuous image) |
| 24 | Video mode power vs command? | Higher for static content (constant streaming even if unchanged) |
| 25 | How panel knows frame rate? | From VSS-to-VSS timing interval (host-controlled) |

---

# DECK 5: DCS COMMANDS & PANEL (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | exit_sleep_mode code? | 0x11 |
| 2 | enter_sleep_mode code? | 0x10 |
| 3 | set_display_on code? | 0x29 |
| 4 | set_display_off code? | 0x28 |
| 5 | set_brightness code? | 0x51 (1 parameter: 0x00-0xFF) |
| 6 | set_pixel_format code? | 0x3A (0x55=RGB565, 0x66=RGB666, 0x77=RGB888) |
| 7 | soft_reset code? | 0x01 |
| 8 | write_memory_start code? | 0x2C (begin pixel write to GRAM) |
| 9 | set_column_address code? | 0x2A (4 params: X_start_hi, X_start_lo, X_end_hi, X_end_lo) |
| 10 | set_page_address code? | 0x2B (4 params: Y_start_hi, Y_start_lo, Y_end_hi, Y_end_lo) |
| 11 | set_tear_on code? | 0x35 (1 param: 0x00=V only, 0x01=V+H) |
| 12 | set_address_mode code? | 0x36 (rotation, mirror, color order bits) |
| 13 | Sleep exit delay? | 120 ms minimum (panel PLL + internal init) |
| 14 | Why 120ms sleep delay? | Panel needs time to stabilize PLL, voltages, GRAM |
| 15 | Can you skip 120ms delay? | No — panel behavior undefined if commands sent too early |
| 16 | Manufacturer command range? | 0xB0–0xFF (panel IC specific, not standardized) |
| 17 | Common panel ICs? | Synaptics (TD4330), Novatek (NT36672), Samsung (S6E3), Himax |
| 18 | What is GRAM? | Graphics RAM — frame buffer inside the display panel IC |
| 19 | GRAM typical size? | Full frame: Width × Height × BPP / 8 (e.g., 6 MB for FHD 24-bit) |
| 20 | Partial update in command mode? | Set column/page address to sub-region, write only changed pixels |
| 21 | AOD (Always-On Display)? | OLED command mode: Low-brightness, minimal content, ULPS on DSI |
| 22 | CABC meaning? | Content Adaptive Brightness Control — save power by adjusting BL+pixels |
| 23 | How to read panel ID? | DCS 0x04 (get_display_ID) → BTA → read 3-byte response |
| 24 | Panel init sequence source? | Panel vendor provides (NDA document or Linux driver) |
| 25 | What if wrong init sequence? | Panel may: not turn on, show wrong colors, flicker, or damage |

---

# DECK 6: AUTOMOTIVE & SYSTEM (25 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | SA8295P DSI controllers? | 2 (DSI0, DSI1) |
| 2 | SA8295P max lanes per DSI? | 4 |
| 3 | SA8295P DSI max speed? | 2.5 Gbps per lane |
| 4 | Typical cluster resolution? | 1920×720 or 1920×1080 |
| 5 | Typical IVI resolution? | 2560×1440 or 1920×1080 |
| 6 | Cluster ASIL requirement? | ASIL-B (functional safety) |
| 7 | IVI safety level? | QM (no safety requirement) |
| 8 | Cluster startup time target? | < 1 second from ignition |
| 9 | How achieve fast display? | Continuous splash from bootloader |
| 10 | Content isolation meaning? | Cluster display independent of IVI (IVI crash won't affect cluster) |
| 11 | DSI for long cable (auto)? | SerDes: DSI → MAX96755 (ser) → coax → MAX96752 (deser) → panel |
| 12 | Automotive display temp range? | -30°C to +80°C (operating) |
| 13 | Multi-display on SA8295P? | DSI0 (cluster) + DSI1 (IVI) + DP (HUD/rear-seat) |
| 14 | DPU meaning? | Display Processing Unit (Qualcomm — blending, scaling, color) |
| 15 | What is SSPP in DPU? | Source Surface Pipe — reads buffer, does scaling/CSC |
| 16 | What is LM in DPU? | Layer Mixer — alpha blending, Z-order composition |
| 17 | What is DSPP in DPU? | Display Post-Processing — color correction, gamma, dither |
| 18 | What is INTF in DPU? | Interface — timing generator connecting to DSI controller |
| 19 | Dual-DSI when needed? | When single DSI bandwidth insufficient (4K displays) |
| 20 | DSC compression ratio? | Typically 2:1 to 3:1 (visually lossless) |
| 21 | DSC benefit? | Reduce bandwidth → lower lane speed → less EMI, power |
| 22 | Automotive EMI standard? | CISPR 25 (radiated emissions for vehicles) |
| 23 | DSI scrambling purpose? | Whiten data → spread EMI spectrum → pass EMC tests |
| 24 | E-mirror display latency? | < 100 ms camera-to-display |
| 25 | Fail-safe for cluster? | Hardware watchdog → show stored warning image if host fails |

---

# DECK 7: LINUX/ANDROID DISPLAY STACK (30 Cards)

| # | Q (Front) | A (Back) |
|---|-----------|----------|
| 1 | Linux display framework? | DRM/KMS (Direct Rendering Manager / Kernel Mode Setting) |
| 2 | DRM device node? | /dev/dri/card0 |
| 3 | What is a CRTC in DRM? | CRT Controller — timing generator + pixel blending |
| 4 | What is a Plane in DRM? | Source buffer with position/format (primary, overlay, cursor) |
| 5 | What is an Encoder in DRM? | Converts CRTC output to specific format (DSI, DP, HDMI) |
| 6 | What is a Connector in DRM? | Physical output port (panel attached here) |
| 7 | What is drm_panel? | Kernel abstraction for display panel (init/enable/disable) |
| 8 | What is drm_mipi_dsi? | Kernel framework for DSI host and panel communication |
| 9 | Panel driver file location? | drivers/gpu/drm/panel/ (e.g., panel-simple.c) |
| 10 | DSI host driver location? | drivers/gpu/drm/msm/dsi/ (Qualcomm) |
| 11 | DPU driver location? | drivers/gpu/drm/msm/disp/dpu1/ |
| 12 | Key DRM ioctl for display? | DRM_IOCTL_MODE_ATOMIC (atomic commit: set planes+CRTCs) |
| 13 | Android display compositor? | SurfaceFlinger (uses HWComposer HAL for DRM) |
| 14 | HWComposer role? | Decides which layers go to HW (DPU) vs GPU composition |
| 15 | How to check display status? | `cat /sys/class/drm/card0-DSI-1/status` (connected/disconnected) |
| 16 | How to see modes? | `cat /sys/class/drm/card0-DSI-1/modes` |
| 17 | modetest tool? | libdrm utility to test DRM/KMS (list modes, set mode, display pattern) |
| 18 | modetest list connectors? | `modetest -c` (shows all connectors and modes) |
| 19 | Set mode with modetest? | `modetest -s <conn>:<mode> -P <plane>@<crtc>:<WxH>+0+0@<fmt>` |
| 20 | Android dumpsys display? | `adb shell dumpsys SurfaceFlinger` (shows layers, composition) |
| 21 | Check DSI errors? | `dmesg | grep -i "dsi\|mdss\|dpu\|panel"` |
| 22 | Panel init in DT? | Device tree: qcom,mdss-dsi-on-command = <...hex command bytes...> |
| 23 | Continuous splash DT flag? | `qcom,mdss-dsi-cont-splash-enabled;` |
| 24 | Backlight DT property? | `qcom,mdss-dsi-bl-pmic-control-type = "bl_ctrl_wled";` |
| 25 | Video mode DT flag? | `qcom,mdss-dsi-panel-type = "dsi_video_mode";` |
| 26 | Command mode DT flag? | `qcom,mdss-dsi-panel-type = "dsi_cmd_mode";` |
| 27 | Lane count DT? | `qcom,mdss-dsi-lane-0-state; ... qcom,mdss-dsi-lane-3-state;` |
| 28 | Panel timing in DT? | `qcom,mdss-dsi-h-back-porch = <36>;` (HBP, HFP, HSA, VBP, VFP, VSA) |
| 29 | DSI clock in DT? | `qcom,mdss-dsi-panel-clock-rate = <891000000>;` (bit clock Hz) |
| 30 | Atomic commit flow? | App→SF→HWC→DRM_ATOMIC→DPU programs→VSYNC→DSI streams frame |

---

END OF DOCUMENT 04 — FLASHCARDS (185 cards across 7 decks)
