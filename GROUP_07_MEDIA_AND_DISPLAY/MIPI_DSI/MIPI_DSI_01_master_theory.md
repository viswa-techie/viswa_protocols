# MIPI DSI — MASTER THEORY DOCUMENT
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 01 of 08
# Depth: Encyclopedia-level comprehensive coverage
# ════════════════════════════════════════════════════════════════════

---

## 1. WHAT IS MIPI DSI?

MIPI DSI (Display Serial Interface) is a high-speed serial protocol for connecting a display controller (host/SoC) to a display panel (peripheral). Defined by the MIPI Alliance, it replaced legacy parallel RGB/DBI interfaces with a low-pin-count, high-bandwidth serial link.

**Key characteristics:**
- **Direction**: Primarily Host → Peripheral (pixel data). Bidirectional on Lane 0 for commands/responses.
- **PHY**: Same D-PHY as CSI-2 (HS differential + LP single-ended), but bidirectional on data lanes.
- **Modes**: Video Mode (streaming) and Command Mode (GRAM-based).
- **Protocol**: Packet-based with DCS (Display Command Set) for panel control.

---

## 2. WHY DSI EXISTS

| Problem (Legacy Parallel) | DSI Solution |
|--------------------------|--------------|
| 24+ data pins (RGB888 parallel) | 2-10 pins (1-4 lanes + clock) |
| EMI from wide parallel bus | Differential signaling, fewer transitions |
| Fixed bandwidth | Scalable: 1-4 lanes, variable speed |
| No standard command set | DCS provides universal panel control |
| High power (many I/O drivers) | Low-power differential + ULPS |
| Short trace only | Still short, but SerDes exists for automotive |

**Market dominance**: >95% of smartphones use DSI. Growing in automotive (cluster, IVI, HUD).

---

## 3. DSI vs OTHER DISPLAY INTERFACES

| Feature | MIPI DSI | eDP | LVDS | HDMI | RGB Parallel |
|---------|----------|-----|------|------|-------------|
| Pins | 2-10 | 4-8 | 8-20 | 19 | 24-30 |
| Max BW | 18 Gbps | 32 Gbps | 3.4 Gbps | 48 Gbps | ~2 Gbps |
| Max Res | 4K@60 | 8K@60 | 1080p | 8K@60 | 720p |
| Direction | Bidirectional | Bidirectional | Unidirectional | Unidirectional | Unidirectional |
| Distance | <15 cm | <50 cm | <50 cm | <15 m | <10 cm |
| Panel Control | DCS (same link) | AUX channel | Separate I2C | CEC/DDC | Separate |
| Power | Very low | Low | Medium | Medium | High |
| Use Case | Mobile, auto | Laptop, auto | Legacy auto | TV, monitor | Legacy |
| Automotive | Cluster, IVI | Large IVI | Cluster (legacy) | Rear-seat | Legacy ECU |

---

## 4. PROTOCOL ARCHITECTURE

```
┌──────────────────────────────────────────────────────────────┐
│  APPLICATION LAYER                                            │
│  Display Framework (DRM/KMS, SurfaceFlinger, HWComposer)    │
├──────────────────────────────────────────────────────────────┤
│  DSI PROTOCOL LAYER                                          │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Processor-to-Peripheral Direction (Tx):                 │ │
│  │   Short Packets: DCS Short Write (1/2 params)          │ │
│  │   Long Packets: DCS Long Write, Pixel Stream           │ │
│  │   Video Mode: Sync packets + Pixel packets             │ │
│  │                                                         │ │
│  │ Peripheral-to-Processor Direction (Rx via BTA):        │ │
│  │   Short: DCS Short Read Response (1/2 bytes)           │ │
│  │   Long: DCS Long Read Response (N bytes)               │ │
│  │   Acknowledge & Error Report                           │ │
│  └────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│  LANE MANAGEMENT LAYER                                       │
│  Same as CSI-2: Round-robin byte distribution across lanes  │
├──────────────────────────────────────────────────────────────┤
│  PHY LAYER (D-PHY)                                           │
│  Same physical layer as CSI-2 BUT:                          │
│    - Data Lane 0: Bidirectional (host TX + panel LP-Rx)    │
│    - Data Lanes 1-3: Unidirectional (host TX only)         │
│    - Clock Lane: Host → Peripheral (DDR clock)             │
│    - LP mode: Used for DCS commands (not just transitions) │
└──────────────────────────────────────────────────────────────┘
```

---

## 5. VIDEO MODE vs COMMAND MODE

### Video Mode
- SoC continuously streams pixel data every frame (like HDMI/DPI)
- Panel has NO internal frame buffer (or doesn't use it)
- Display timing (HFP, HBP, HSA, VFP, VBP, VSA) fully controlled by host
- If host stops sending → display goes blank
- Three sub-modes: Burst, Non-Burst Sync Pulse, Non-Burst Sync Event

### Command Mode
- SoC writes pixel data to panel's internal GRAM (Graphics RAM)
- Panel continuously refreshes display from its own GRAM
- Host only sends data when content changes (power savings)
- TE (Tearing Effect) signal synchronizes host writes to panel refresh
- Partial update possible (refresh only changed region)
- Used for Always-On Display (AOD) on OLED smartphones

### Comparison

| Aspect | Video Mode | Command Mode |
|--------|-----------|--------------|
| Panel GRAM | Not required | Required |
| Host bandwidth | Continuous (full frame every refresh) | On-demand only |
| Power (static content) | Higher (constant streaming) | Lower (send once) |
| Power (video playback) | Similar | Similar |
| Tearing risk | None (sync pulses) | Without TE sync: tearing |
| Complexity | Simpler host | More complex panel IC |
| Partial update | No | Yes |
| Panel cost | Lower (no GRAM) | Higher (GRAM needed) |
| Automotive use | Cluster, IVI (always video) | Rarely used automotive |
| Smartphone use | Most phones | Some OLED phones (AOD) |

---

## 6. VIDEO MODE TIMING

```
Frame structure:
├── VSA ──┤── VBP ──┤────── Active Video Lines ─────────┤── VFP ──┤
│ (sync)  │(blanking)│  Line 0 ... Line H-1             │(blanking)│

Each active line:
├── HSA ──┤── HBP ──┤──── Active Pixels ────┤── HFP ──┤
│ (sync)  │(blanking)│  Pixel 0 ... Pixel W-1│(blanking)│

Timing parameters:
  HSA  = Horizontal Sync Active (pixels)
  HBP  = Horizontal Back Porch (pixels)
  HFP  = Horizontal Front Porch (pixels)
  VSA  = Vertical Sync Active (lines)
  VBP  = Vertical Back Porch (lines)
  VFP  = Vertical Front Porch (lines)
  Width = Active pixels per line
  Height = Active lines per frame

Total:
  H_total = HSA + HBP + Width + HFP
  V_total = VSA + VBP + Height + VFP
  Pixel Clock = H_total × V_total × FPS
```

### Video Mode Sub-Types

**Non-Burst Sync Pulse:**
- Explicit HSS (Horizontal Sync Start) and HSE (Horizontal Sync End) packets
- Pixel clock matches display timing exactly
- Blanking transmitted as blanking packets (LP or null)
- Panel uses sync pulses to maintain line timing

**Non-Burst Sync Event:**
- Only HSS packet (no HSE) — sync event marks line start
- Otherwise same as Sync Pulse
- Slightly less overhead

**Burst Mode:**
- Pixel data sent at maximum PHY speed (faster than display needs)
- After line data sent, PHY goes to LP or sends BLLP
- Advantage: Time in LP saves power, or allows sharing bandwidth
- Host must still maintain correct frame timing (send right amount per frame)
- Most common in smartphones (saves power in LP gaps)

---

## 7. DSI PACKET STRUCTURE

### Short Packet (Host → Peripheral, 4 bytes):
```
┌───────────┬──────────┬──────────┬─────────┐
│  Data ID  │  Data 0  │  Data 1  │   ECC   │
│  (1 byte) │ (1 byte) │ (1 byte) │ (1 byte)│
│ [VC|DT]   │          │          │         │
└───────────┴──────────┴──────────┴─────────┘
```
Used for: V-Sync Start, V-Sync End, H-Sync Start, H-Sync End, DCS Short Write, Turn-On/Off, EoTp

### Long Packet (Host → Peripheral, 4 + WC + 2 bytes):
```
┌───────────┬──────────────┬─────────┬──────────────────────────┬─────────┐
│  Data ID  │  Word Count  │   ECC   │        Payload           │  CRC-16 │
│  (1 byte) │  (2 bytes)   │(1 byte) │  (WC bytes)             │(2 bytes)│
│ [VC|DT]   │  (payload len)│         │  Pixel data or DCS cmd  │         │
└───────────┴──────────────┴─────────┴──────────────────────────┴─────────┘
```
Used for: Packed Pixel Stream, DCS Long Write, Null/Blanking

### Read Response (Peripheral → Host):
After BTA, peripheral sends response on Lane 0 in LP mode:
- Short Read Response: 4 bytes (1 or 2 data bytes)
- Long Read Response: 4 + N + 2 bytes (N data bytes)
- Acknowledge & Error Report: 4 bytes (error bits)

---

## 8. DATA TYPES (DSI-Specific)

| DT Code | Type | Direction | Description |
|---------|------|-----------|-------------|
| 0x01 | V-Sync Start (VSS) | H→P | Short packet — frame start |
| 0x11 | V-Sync End (VSE) | H→P | Short packet — frame end |
| 0x21 | H-Sync Start (HSS) | H→P | Short packet — line sync start |
| 0x31 | H-Sync End (HSE) | H→P | Short packet — line sync end |
| 0x08 | EoTp | H→P | End of Transmission Packet |
| 0x09 | Null (No Data) | H→P | Long packet — filler/blanking |
| 0x19 | Blanking | H→P | Long packet — blanking data |
| 0x05 | DCS Short Write (0 param) | H→P | Short packet — command only |
| 0x15 | DCS Short Write (1 param) | H→P | Short packet — command + 1 byte |
| 0x06 | DCS Read (0 param) | H→P | Short packet — request read |
| 0x39 | DCS Long Write | H→P | Long packet — multi-byte command |
| 0x0E | Packed Pixel 16-bit | H→P | Long packet — RGB565 pixels |
| 0x1E | Packed Pixel 18-bit | H→P | Long packet — RGB666 packed |
| 0x2E | Packed Pixel 18-bit loosely | H→P | Long packet — RGB666 loose (3B/pixel) |
| 0x3E | Packed Pixel 24-bit | H→P | Long packet — RGB888 pixels |
| 0x0B | Packed Pixel 30-bit | H→P | Long packet — RGB101010 |
| 0x1B | Packed Pixel 36-bit | H→P | Long packet — RGB121212 |
| 0x11 | Acknowledge | P→H | Short — command acknowledged |
| 0x21 | Short Read (1 byte) | P→H | Short — 1 byte read response |
| 0x22 | Short Read (2 bytes) | P→H | Short — 2 byte read response |
| 0x1A | Long Read Response | P→H | Long — multi-byte read response |
| 0x02 | Acknowledge & Error | P→H | Short — error report |

---

## 9. DCS (DISPLAY COMMAND SET)

Standard commands defined by MIPI for universal panel control:

| Command | Code | Parameters | Description |
|---------|------|-----------|-------------|
| NOP | 0x00 | 0 | No operation |
| soft_reset | 0x01 | 0 | Software reset |
| get_power_mode | 0x0A | 0 (read) | Read current power mode |
| get_display_mode | 0x0D | 0 (read) | Read display mode status |
| get_pixel_format | 0x0C | 0 (read) | Read pixel format |
| enter_sleep_mode | 0x10 | 0 | Enter sleep (display off, panel low power) |
| exit_sleep_mode | 0x11 | 0 | Exit sleep (wake up, prepare for display) |
| set_display_off | 0x28 | 0 | Turn display output off (panel still powered) |
| set_display_on | 0x29 | 0 | Turn display output on |
| set_column_address | 0x2A | 4 | Set X start/end for partial update |
| set_page_address | 0x2B | 4 | Set Y start/end for partial update |
| write_memory_start | 0x2C | N | Begin writing pixel data to GRAM |
| write_memory_continue | 0x3C | N | Continue writing pixel data |
| set_tear_on | 0x35 | 1 | Enable TE signal (V-only or V+H) |
| set_pixel_format | 0x3A | 1 | Set color depth (16/18/24 bit) |
| set_brightness | 0x51 | 1 | Set display brightness (0-255) |
| get_brightness | 0x52 | 0 (read) | Read current brightness |
| write_CABC | 0x55 | 1 | Content Adaptive Brightness Control |
| set_address_mode | 0x36 | 1 | Rotation, mirror, color order |

**Panel-specific (manufacturer) commands:** 0xB0-0xFF range, varies by panel IC (Synaptics, Novatek, Himax, Samsung).

---

## 10. BUS TURNAROUND (BTA)

DSI is primarily host-to-peripheral. To read from the panel, the bus direction must reverse:

```
Normal (H→P on all lanes):
  Host drives: CLK + D0 + D1 + D2 + D3

BTA sequence (on Data Lane 0 only):
  1. Host sends LP-10 (BTA request) on D0
  2. Host releases D0 (tri-states its driver)
  3. Peripheral drives LP-10 (BTA acknowledge) on D0
  4. Peripheral sends response in LP mode on D0
  5. Peripheral sends LP-11 (end of response)
  6. Host resumes driving D0

During BTA: Only Lane 0 reverses. Lanes 1-3 stay idle (LP-11).
BTA is required after every DCS read command.
```

**BTA timing**: Adds ~500ns-2µs overhead per read. Avoid excessive reads during active display.

---

## 11. TEARING EFFECT (TE)

**Problem**: In command mode, if host writes to GRAM while panel is reading from the same region → tearing artifact (half old frame, half new frame).

**Solution**: TE signal from panel to host:
- Panel asserts TE at the start of its vertical blanking period
- Host waits for TE before starting GRAM write
- Result: Write completes during blanking → no visible tearing

**TE Options:**
- TE via hardware pin (GPIO from panel to SoC) — most reliable
- TE via DSI BTA (panel sends TE event in-band) — no extra pin needed
- TE modes: V-blank only, or V-blank + H-blank

**Video mode**: TE not needed (host controls all timing via sync packets).

---

## 12. POWER STATES & ULPS

```
DSI Power States:

┌─────────────────────────────────────────────────┐
│  Full Active                                     │
│  CLK lane: HS (continuous or non-continuous)    │
│  Data lanes: HS (during video) or LP (commands) │
│  Power: Highest                                  │
├─────────────────────────────────────────────────┤
│  LP Mode (Idle)                                  │
│  CLK lane: LP-11 (stopped)                      │
│  Data lanes: LP-11                              │
│  Power: Medium (PHY bias still on)              │
├─────────────────────────────────────────────────┤
│  ULPS (Ultra-Low Power State)                    │
│  CLK lane: LP-00 (ULPS entry)                   │
│  Data lanes: LP-00 (ULPS entry)                 │
│  Power: Very low (~10 µA per lane)              │
│  Exit: Requires wake-up sequence (~1 ms)        │
├─────────────────────────────────────────────────┤
│  Full Power-Off                                  │
│  All supplies off, no signaling                 │
│  Power: Zero                                     │
│  Recovery: Full panel re-initialization          │
└─────────────────────────────────────────────────┘

ULPS use case:
  - Panel showing static content from GRAM (command mode)
  - Host enters ULPS → saves power while panel self-refreshes
  - Wake up when new content ready
```

---

## 13. DISPLAY STREAM COMPRESSION (DSC)

**Problem**: 4K@60Hz RGB888 requires ~12 Gbps raw — exceeds 4-lane DSI capacity at 2.5 Gbps (10 Gbps).

**Solution**: DSC (Display Stream Compression) — VESA standard for visually lossless compression:
- Compression ratio: Typically 2:1 to 3:1 (configurable)
- Visually lossless: No perceptible artifacts
- Fixed-rate (CBR): Predictable bandwidth
- Latency: 1-2 lines (very low)

**How it works:**
- SoC encodes RGB → compressed bitstream before DSI TX
- Panel driver IC decodes compressed bitstream → RGB to display
- Both must support same DSC version and parameters

**Example:**
```
Without DSC: 2560×1440×24×60 = 5.3 Gbps (need 4L @ 1.5 Gbps)
With DSC 2:1: 2560×1440×12×60 = 2.65 Gbps (can use 2L @ 1.5 Gbps!)
With DSC 3:1: 2560×1440×8×60 = 1.77 Gbps (can use 1L @ 2 Gbps!)
```

**DSC versions:**
- DSC 1.1: Basic (most panels support)
- DSC 1.2a: Improved quality at higher compression
- VDC-M: VESA alternative, better for high compression ratios

---

## 14. DUAL-DSI

For displays requiring more bandwidth than a single DSI port provides:

**Types:**
1. **Dual-DSI (split display)**: Left half on DSI0, right half on DSI1
   - Each controller sends half the width
   - Panel has 2 DSI input ports
   - Synchronization required between DSI0 and DSI1

2. **Bonded-DSI**: Two DSI controllers bonded as one logical interface
   - Acts as 8-lane DSI (4+4)
   - Bytes distributed across 8 lanes (appears as single wide interface)
   - Panel sees one logical link

**When needed:**
- 4K (3840×2160) @ 60 Hz @ 24-bit = 11.9 Gbps (> 10 Gbps single DSI limit)
- Solution: Dual-DSI at 6 Gbps each, or single DSI with DSC

---

## 15. PANEL INITIALIZATION SEQUENCE

```
Typical panel bring-up (Video Mode):

1. Power On:
   └── VCI (3.3V), VDDI (1.8V), VDD (regulator-specific)
   └── Wait T_POWER (5-20 ms)

2. Reset:
   └── RESET_N: HIGH → LOW (10µs) → HIGH
   └── Wait T_RESET (5-10 ms)

3. DSI Lanes to LP-11:
   └── Host puts CLK + data lanes in LP-11
   └── Wait T_INIT (> 100 µs)

4. Panel Init Commands (LP mode):
   └── Exit Sleep: DCS 0x11 (exit_sleep_mode)
   └── Wait 120 ms (panel boot time)
   └── Set pixel format: DCS 0x3A (set_pixel_format = 0x77 for RGB888)
   └── Set column/page address (full screen)
   └── Manufacturer-specific init (gamma, voltage, timing)
   └── Display On: DCS 0x29 (set_display_on)
   └── Wait 20 ms

5. Start Video Mode:
   └── Host begins HS video transmission
   └── Sync packets + pixel data at configured timing
   └── Display shows image

6. Backlight Enable:
   └── After first frame sent (avoid flash)
   └── PWM or I2C backlight ramp-up
```

---

## 16. QUALCOMM DISPLAY PIPELINE (SA8295P)

```
┌─────────────────────────────────────────────────────────────────┐
│  SurfaceFlinger / HWComposer                                     │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │ Layers: Status bar, App content, Navigation bar           │  │
│  └─────────────────────────────┬─────────────────────────────┘  │
│                                │ (buffer handles via Binder)     │
│  ┌─────────────────────────────▼─────────────────────────────┐  │
│  │ DPU (Display Processing Unit)                              │  │
│  │ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌──────────┐  │  │
│  │ │SSPP0│ │SSPP1│ │SSPP2│ │SSPP3│ │Mixer│ │DSC Encode│  │  │
│  │ │(Src)│→│(Src)│→│(Src)│→│(Src)│→│(Blend)→│(optional)│  │  │
│  │ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └──────────┘  │  │
│  │                                              │             │  │
│  │ SSPP: Source Surface Pipe (scaling, CSC, decimation)      │  │
│  │ Mixer: Alpha blending, Z-order                            │  │
│  │ DSC: Display Stream Compression encoder                   │  │
│  └─────────────────────────────┬─────────────────────────────┘  │
│                                │                                  │
│  ┌─────────────────────────────▼─────────────────────────────┐  │
│  │ DSI Controller                                              │  │
│  │ - Packs pixels into DSI long packets                       │  │
│  │ - Generates sync packets (VSS/HSS/HSE/VSE)                │  │
│  │ - Video mode timing engine                                  │  │
│  │ - Command mode FIFO                                        │  │
│  │ - DCS command interface                                    │  │
│  └─────────────────────────────┬─────────────────────────────┘  │
│                                │                                  │
│  ┌─────────────────────────────▼─────────────────────────────┐  │
│  │ DSI PHY (D-PHY)                                            │  │
│  │ - Clock lane: DDR (continuous or non-continuous)           │  │
│  │ - Data lanes: 1-4 lanes, HS + LP modes                    │  │
│  │ - PLL: Generates bit clock from reference                  │  │
│  └─────────────────────────────┬─────────────────────────────┘  │
│                                │ (differential pairs)            │
└────────────────────────────────┼────────────────────────────────┘
                                 ▼
┌────────────────────────────────────────────────────────────────┐
│  DISPLAY PANEL                                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ Panel Driver IC (Synaptics TD4330 / Samsung S6E3FC3)      │ │
│  │ - DSI receiver (D-PHY)                                     │ │
│  │ - Timing controller (TCON)                                 │ │
│  │ - Source driver (column voltages)                          │ │
│  │ - Gate driver (row scanning)                               │ │
│  │ - DSC decoder (if DSC used)                               │ │
│  │ - GRAM (command mode panels only)                         │ │
│  └───────────────────────────────────────────────────────────┘ │
│  Panel type: LCD (TFT), OLED (AMOLED), or Micro-LED          │
└────────────────────────────────────────────────────────────────┘
```

---

## 17. LINUX DRM/KMS DISPLAY ARCHITECTURE

```
Userspace:
  ┌─────────────────────────────────────────────┐
  │ Weston/Mutter (Compositor) or SurfaceFlinger│
  │ → DRM ioctl: Atomic commit (planes, CRTCs) │
  └──────────────────────┬──────────────────────┘
                         │ /dev/dri/card0
Kernel:
  ┌──────────────────────▼──────────────────────┐
  │ DRM Framework                                │
  │ ┌──────────┐ ┌──────────┐ ┌──────────┐    │
  │ │ Plane    │ │ CRTC     │ │ Encoder  │    │
  │ │(buffer + │→│(timing + │→│(format + │    │
  │ │ position)│ │ blending)│ │ output)  │    │
  │ └──────────┘ └──────────┘ └──────────┘    │
  │                                    │        │
  │ ┌──────────────────────────────────▼──────┐ │
  │ │ Connector (DSI)                          │ │
  │ │ - Panel driver (drm_panel)               │ │
  │ │ - DSI host driver (drm_mipi_dsi)         │ │
  │ └──────────────────────────────────────────┘ │
  └──────────────────────────────────────────────┘

Key objects:
  • Plane: Source buffer (framebuffer object)
  • CRTC: Timing generator + compositor
  • Encoder: Converts internal format to output (DSI packets)
  • Connector: Physical output (panel with its properties)
  • Bridge: Optional intermediate (e.g., DSI-to-LVDS bridge)
```

---

## 18. PANEL DRIVER IN LINUX

```c
/* Simplified DSI panel driver structure */

static const struct drm_display_mode default_mode = {
    .clock = 152340,      /* pixel clock kHz */
    .hdisplay = 1080,     /* active width */
    .hsync_start = 1080 + 20,   /* + HFP */
    .hsync_end = 1080 + 20 + 2, /* + HSA */
    .htotal = 1080 + 20 + 2 + 34, /* + HBP */
    .vdisplay = 2340,     /* active height */
    .vsync_start = 2340 + 4,    /* + VFP */
    .vsync_end = 2340 + 4 + 2,  /* + VSA */
    .vtotal = 2340 + 4 + 2 + 16, /* + VBP */
};

/* Panel init sequence */
static int panel_init(struct panel_data *panel) {
    struct mipi_dsi_device *dsi = panel->dsi;
    
    mipi_dsi_dcs_exit_sleep_mode(dsi);
    msleep(120);
    
    mipi_dsi_dcs_set_pixel_format(dsi, MIPI_DCS_PIXEL_FMT_24BIT);
    
    /* Manufacturer-specific commands */
    mipi_dsi_dcs_write(dsi, 0xB0, (u8[]){0x04}, 1);  /* Access cmd2 */
    mipi_dsi_dcs_write(dsi, 0xB6, gamma_data, sizeof(gamma_data));
    
    mipi_dsi_dcs_set_display_on(dsi);
    msleep(20);
    
    return 0;
}

/* Panel operations */
static const struct drm_panel_funcs panel_funcs = {
    .prepare = panel_prepare,   /* Power on + reset */
    .enable = panel_enable,     /* Init commands + display on */
    .disable = panel_disable,   /* Display off */
    .unprepare = panel_unprepare, /* Power off */
    .get_modes = panel_get_modes, /* Return supported modes */
};
```

---

## 19. AUTOMOTIVE DISPLAY CONSIDERATIONS

### Startup Time
- **Regulation**: Rear-view camera display must show image within 2 seconds
- **Target**: Cluster display within 1 second of ignition
- **Fast boot**: Skip full OS boot, show static cluster from bootloader
- **Early display**: DSI initialized in bootloader, handed off to kernel seamlessly

### Functional Safety (ASIL-B for Cluster)
- **Self-test**: Panel responds correctly to DCS read commands
- **Content verification**: CRC check on displayed content
- **Watchdog**: If host stops sending video → panel shows "fail-safe" image
- **Redundancy**: Dual-channel display path or hardware watchdog

### Multi-Display Architecture (SA8295P)
```
SA8295P can drive multiple displays simultaneously:
  DSI0 → Instrument Cluster (1920×720, 60Hz)
  DSI1 → Center IVI Display (2560×1440, 60Hz)
  DP   → Rear-Seat Entertainment (1080p, 60Hz via DP-to-HDMI)
  
  Each display has independent:
  - DPU pipeline (separate mixer)
  - Timing generator (CRTC)
  - Color calibration
  - Content isolation (security between cluster and IVI)
```

### Temperature Range
- Display operating: -30°C to +80°C
- DSI PHY timing varies with temperature
- Panel response time increases at cold temperatures
- Backlight efficiency decreases at temperature extremes

---

## 20. BANDWIDTH CALCULATION FOR DSI

```
Formula:
  Required BW = Width × Height × BPP × FPS × (1 + Blanking_OH)

Where:
  BPP = Bits per pixel (16 for RGB565, 18 for RGB666, 24 for RGB888)
  Blanking_OH = (H_total × V_total) / (Width × Height) - 1
  Typically 5-20% overhead

Available BW:
  D-PHY: Lanes × Speed × 8/8 (no encoding overhead for DSI)
  Effective: Lanes × Speed × (1 - LP_overhead)

Example: 1920×720 cluster @ 60 Hz, RGB888, 4 lanes:
  Raw data: 1920 × 720 × 24 × 60 = 1.99 Gbps
  With 10% blanking: 2.19 Gbps
  4 lanes @ 700 Mbps = 2.8 Gbps → 78% utilization ✓

Example: 2560×1440 IVI @ 60 Hz, RGB888, 4 lanes:
  Raw data: 2560 × 1440 × 24 × 60 = 5.31 Gbps
  With 10% blanking: 5.84 Gbps
  4 lanes @ 1.5 Gbps = 6.0 Gbps → 97% utilization ✗ (too tight!)
  Solution: Use DSC (2:1) → 2.92 Gbps → 4L @ 1 Gbps = 49% ✓
```

---

## 21. COMMON DSI ISSUES IN AUTOMOTIVE

| Issue | Root Cause | Solution |
|-------|-----------|----------|
| Black screen (no display) | Panel not initialized, DSI not configured | Check power sequence, init commands |
| White screen | DSI active but no pixel data | Check video mode timing, DPU configuration |
| Flickering | Timing mismatch (blanking too small) | Increase HBP/VBP, verify pixel clock |
| Tearing (horizontal lines) | Write during panel refresh (cmd mode) | Enable TE synchronization |
| Color shift | Wrong pixel format or gamma | Verify RGB888 vs RGB666, check gamma table |
| Display freeze | Host crashed, DSI stopped | Implement hardware watchdog, fail-safe image |
| Slow startup | Long init sequence, sleep delays | Optimize DCS sequence, reduce msleep() |
| EMI failure | High-speed DSI radiating | Enable scrambling, reduce lane speed, shielding |
| Panel burn-in (OLED) | Static content too long | Pixel shifting, screen saver, CABC |
| No backlight | PWM/WLED not configured | Check GPIO/I2C backlight driver |

---

## 22. DSI SERIALIZER/BRIDGE FOR AUTOMOTIVE

For long-distance display connections (cockpit-to-display >30 cm):

| Chip | Role | Interface | Distance | Use Case |
|------|------|-----------|----------|----------|
| SN65DSI86 | DSI-to-eDP bridge | DSI→eDP | PCB | Large automotive display |
| TC358775 | DSI-to-LVDS bridge | DSI→LVDS | Cable | Legacy cluster panels |
| MAX96755 | DSI-to-GMSL serializer | DSI→Coax | 15 m | Remote display |
| MAX96752 | GMSL-to-DSI/LVDS deser | Coax→DSI | 15 m | Remote display |
| DS90UB941 | DSI-to-FPD-Link | DSI→STP | 15 m | TI automotive display link |
| DS90UB948 | FPD-Link-to-OpenLDI deser | STP→LVDS | 15 m | Display side |

Architecture for remote display:
```
SoC ──DSI──→ MAX96755 ══coax (5m)══→ MAX96752 ──DSI──→ Display Panel
              (serializer)             (deserializer)
```

---

## 23. EoTp (END OF TRANSMISSION PACKET)

DSI requires an End of Transmission packet after every HS transmission:
- **Short packet**: DT = 0x08
- **Purpose**: Tells receiver "HS burst is over, returning to LP"
- **Mandatory**: After every HS packet in most implementations
- **Receiver uses it**: To validate packet count and detect errors

Some panels require EoTp, some don't. Device tree property: `qcom,mdss-dsi-tx-eot-append;`

---

## 24. COLOR MANAGEMENT

```
Color pipeline:
  App (sRGB) → Color Space Conversion → DPU → DSI → Panel

Qualcomm DPU color features:
  • SSPP: CSC (Color Space Conversion), e.g., YUV→RGB
  • DSPP: PA (Picture Adjustment), PCC (Polynomial Color Correction)
  • DSPP: Gamut mapping (sRGB ↔ DCI-P3 ↔ Display P3)
  • DSPP: IGC/GC (Inverse Gamma Correction / Gamma Correction)
  • Panel-side: Gamma LUT (loaded via DCS commands)

Automotive requirements:
  • Cluster: Color accuracy for warning indicators (red/amber/green)
  • IVI: Wide color gamut (DCI-P3) for media
  • Consistent across temperature (-30°C to +80°C)
  • Aging compensation (OLED degradation over time)
```

---

END OF DOCUMENT 01 — MASTER THEORY
