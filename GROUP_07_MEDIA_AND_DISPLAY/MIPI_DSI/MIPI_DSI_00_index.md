# MIPI DSI — PROTOCOL ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI (Display Serial Interface)
# Document: 00 of 08 — Master Index & Protocol Identity Card
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | MIPI Display Serial Interface (DSI) |
| Specification Body | MIPI Alliance |
| First Release | DSI v1.0 (2006) |
| Current Version | DSI-2 v2.1 (2022) |
| Direction | SoC → Display (Host → Peripheral); Bidirectional for commands |
| PHY Options | D-PHY (up to 4.5 Gbps/lane), C-PHY (up to 5.7 Gbps/trio) |
| Max Data Lanes | 4 (D-PHY) or 3 trios (C-PHY) |
| Operating Modes | Command Mode (GRAM-based), Video Mode (streaming) |
| Interface Type | Point-to-point, packet-based serial |
| Clock | D-PHY: Dedicated clock lane (DDR). C-PHY: Embedded in data |
| Control Channel | Same DSI lanes (LP mode for DCS commands) |
| Error Protection | ECC (header), CRC-16 (payload) |
| Primary Use | Smartphone displays, automotive HMI, infotainment |
| Automotive Relevance | Instrument cluster, center display, HUD, rear-seat, e-mirror |

---

## GENERATION COMPARISON

| Feature | DSI v1.0 (2006) | DSI v1.1 (2008) | DSI v1.2 (2012) | DSI v1.3 (2014) | DSI-2 v1.0 (2016) | DSI-2 v2.1 (2022) |
|---------|----------------|----------------|----------------|----------------|-------------------|-------------------|
| D-PHY Speed | 1 Gbps | 1 Gbps | 1.5 Gbps | 1.5 Gbps | 4.5 Gbps | 4.5 Gbps |
| C-PHY | No | No | No | No | Yes | Yes |
| Video Mode | Yes | Yes | Yes | Yes | Yes | Yes |
| Command Mode | Yes | Yes | Yes | Yes | Yes | Yes |
| Compressed Video | No | No | No | No | No | Yes (DSC/VDC-M) |
| Max Resolution | 720p | 1080p | 1440p | 4K | 4K+ | 8K (with DSC) |
| Scrambling | No | No | No | No | No | Yes |

---

## AUTOMOTIVE DISPLAY USE CASES

| Application | Resolution | Refresh | Lanes | Mode | Key Requirements |
|-------------|-----------|---------|-------|------|-----------------|
| Instrument Cluster | 1920×720 | 60 Hz | 4L | Video | Low latency, functional safety |
| Center Infotainment | 2560×1440 | 60 Hz | 4L | Video | High resolution, touch |
| Rear-Seat Entertainment | 1920×1080 | 60 Hz | 4L | Video | Video playback |
| Head-Up Display (HUD) | 1280×480 | 60 Hz | 2L | Video | Ultra-low latency |
| E-Mirror Display | 1920×480 | 60 Hz | 2L | Video | Camera-to-display latency |
| Passenger Display | 1920×1080 | 60 Hz | 4L | Video | Content isolation |

---

## SA8295P DSI HARDWARE

| Feature | Specification |
|---------|--------------|
| DSI Controllers | 2× DSI (DSI0, DSI1) |
| Max Lanes per Controller | 4 (D-PHY) |
| Max Speed per Lane | 2.5 Gbps |
| Max Aggregate (single) | 10 Gbps (4L × 2.5 Gbps) |
| Combined (dual-DSI) | 20 Gbps (for 4K+ displays) |
| PHY Type | D-PHY v2.1 |
| Video Mode | Supported (burst, non-burst sync pulse/event) |
| Command Mode | Supported (with TE signal) |
| Display Stream Compression | DSC 1.2a supported |
| Color Depth | 16/18/24/30-bit |
| Display Controller | DPU (Display Processing Unit) |

---

## 50 KEY TERMS

| # | Term | Definition |
|---|------|-----------|
| 1 | DSI | Display Serial Interface — MIPI standard for display connection |
| 2 | D-PHY | Physical layer using differential pairs (same as CSI-2) |
| 3 | C-PHY | Alternative 3-wire trio physical layer |
| 4 | Host | DSI transmitter (SoC/display controller) |
| 5 | Peripheral | DSI receiver (display panel/driver IC) |
| 6 | Video Mode | Continuous pixel streaming (real-time, like HDMI) |
| 7 | Command Mode | Write to panel GRAM, panel self-refreshes |
| 8 | DCS | Display Command Set — standard commands for panel control |
| 9 | GRAM | Graphics RAM — frame buffer inside the display panel |
| 10 | TE | Tearing Effect signal — panel → host sync for command mode |
| 11 | HS Mode | High-speed differential signaling for pixel data |
| 12 | LP Mode | Low-power mode for DCS commands and BTA |
| 13 | BTA | Bus Turnaround — direction reversal on data lane 0 |
| 14 | EoTp | End of Transmission Packet — marks end of HS burst |
| 15 | VSS/VSE | Virtual Sync Start/End — video mode frame timing |
| 16 | HSS/HSE | Horizontal Sync Start/End — line timing |
| 17 | HBP/HFP | Horizontal Back/Front Porch — blanking periods |
| 18 | VBP/VFP | Vertical Back/Front Porch — vertical blanking |
| 19 | BLLP | Blanking or Low-Level Power — idle between video data |
| 20 | Burst Mode | Send pixels faster than needed, idle in LP between lines |
| 21 | Non-Burst Sync Pulse | Pixel timing matches display timing exactly (sync pulses) |
| 22 | Non-Burst Sync Event | Like sync pulse but without explicit HSS/HSE |
| 23 | DSC | Display Stream Compression — visually lossless compression |
| 24 | VDC-M | VESA Display Compression-M (alternative to DSC) |
| 25 | Color Depth | Bits per pixel component (6/8/10 per R/G/B) |
| 26 | RGB888 | 24-bit color (8 bits per R, G, B channel) |
| 27 | RGB666 | 18-bit color (6 bits per channel, packed or loosely packed) |
| 28 | RGB565 | 16-bit color (5R, 6G, 5B) |
| 29 | Dual-DSI | Two DSI ports driving one display (for high-res/high-BW) |
| 30 | Split-Link | Single DSI controller, PHY split into sub-links |
| 31 | Panel Driver IC | IC on display that receives DSI and drives LCD/OLED |
| 32 | DPU | Display Processing Unit (Qualcomm — blending, scaling, color) |
| 33 | MDSS | Mobile Display Sub-System (Qualcomm legacy term) |
| 34 | SDE | Snapdragon Display Engine (Qualcomm) |
| 35 | INTF | Interface block connecting DPU to DSI controller |
| 36 | CTL | Control block in DPU (flush, timing) |
| 37 | Backlight | PWM or I2C-controlled LED/OLED brightness |
| 38 | MIPI DBI | Display Bus Interface — parallel legacy, superseded by DSI |
| 39 | MIPI DPI | Display Pixel Interface — RGB parallel interface |
| 40 | eDP | Embedded DisplayPort — alternative to DSI for large displays |
| 41 | LVDS | Low-Voltage Differential Signaling — legacy display interface |
| 42 | Panel Init Sequence | DCS command sequence to initialize panel after power-on |
| 43 | Gamma Correction | LUT-based brightness curve adjustment |
| 44 | CABC | Content Adaptive Brightness Control |
| 45 | AOD | Always-On Display (OLED, command mode, low power) |
| 46 | Partial Update | Refresh only changed portion (command mode only) |
| 47 | Rotation | Panel orientation (0°/90°/180°/270°) |
| 48 | ULPS | Ultra-Low Power State — PHY enters lowest power |
| 49 | Clock-Pre/Post | Clock lane timing relative to data lane HS transitions |
| 50 | LP-Rx | Low-power receiver on data lane 0 (host reads panel responses) |

---

## DOCUMENT MAP

| Doc # | Title | Content |
|-------|-------|---------|
| 00 | Index (this file) | Protocol identity, key terms, study guide |
| 01 | Master Theory | Complete DSI protocol theory (20+ sections) |
| 02 | Diagrams | ASCII art, Mermaid, timing diagrams (20 diagrams) |
| 03 | Interview Questions | 30+ Q&A (Junior → Staff level) |
| 04 | Flashcards | 180+ Q/A cards in 7 decks |
| 06 | Cheatsheet | DCS commands, timing params, quick reference |
| 07 | Labs & Debugging | Panel bringup, debug scenarios |
| 08 | References | Specs, kernel paths, tools, study plan |

---

## STUDY CHECKLIST

### Beginner
- [ ] Understand DSI vs DPI vs eDP (when to use which)
- [ ] Learn Video Mode vs Command Mode difference
- [ ] Study D-PHY layers (shared with CSI-2)
- [ ] Know basic DCS commands (sleep in/out, display on/off)
- [ ] Understand RGB888/666/565 color formats

### Intermediate
- [ ] Study DSI packet structure (short/long, DCS)
- [ ] Learn video mode timing (HSA/HBP/HFP/VSA/VBP/VFP)
- [ ] Understand BTA (Bus Turnaround) mechanism
- [ ] Know panel initialization sequence
- [ ] Study DRM/KMS framework for display
- [ ] Understand backlight control (PWM, WLED)

### Advanced
- [ ] Design dual-DSI for high-resolution displays
- [ ] Implement DSC (Display Stream Compression)
- [ ] Debug panel bringup issues (no display, flicker, tearing)
- [ ] Automotive: Latency optimization for cluster displays
- [ ] Multi-display architecture (SA8295P: cluster + IVI + HUD)
- [ ] Functional safety for instrument cluster (ASIL-B)

---

END OF DOCUMENT 00 — INDEX
