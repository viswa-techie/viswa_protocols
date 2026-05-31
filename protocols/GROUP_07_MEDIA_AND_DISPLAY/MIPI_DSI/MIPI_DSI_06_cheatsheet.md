# MIPI DSI — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 06 of 08
# Format: Tables, one-liners, instant lookups
# ════════════════════════════════════════════════════════════════════

---

## 1. DCS COMMAND TABLE (Essential)

| Code | Command | Params | Direction | Description |
|------|---------|--------|-----------|-------------|
| 0x00 | NOP | 0 | H→P | No operation |
| 0x01 | soft_reset | 0 | H→P | Software reset panel |
| 0x04 | get_display_ID | 0 (read) | P→H | Read 3-byte panel ID |
| 0x0A | get_power_mode | 0 (read) | P→H | Read power state bits |
| 0x0B | get_address_mode | 0 (read) | P→H | Rotation/mirror status |
| 0x0C | get_pixel_format | 0 (read) | P→H | Current color depth |
| 0x0D | get_display_mode | 0 (read) | P→H | Normal/partial/idle/off |
| 0x10 | enter_sleep | 0 | H→P | Enter sleep (low power) |
| 0x11 | exit_sleep | 0 | H→P | Exit sleep → wait 120ms |
| 0x12 | enter_partial | 0 | H→P | Partial display mode |
| 0x13 | enter_normal | 0 | H→P | Normal display mode |
| 0x20 | display_inversion_off | 0 | H→P | Normal colors |
| 0x21 | display_inversion_on | 0 | H→P | Invert colors |
| 0x28 | display_off | 0 | H→P | Blank display (keep power) |
| 0x29 | display_on | 0 | H→P | Turn on display output |
| 0x2A | set_column_address | 4 | H→P | X start/end coordinates |
| 0x2B | set_page_address | 4 | H→P | Y start/end coordinates |
| 0x2C | write_memory_start | N | H→P | Begin pixel write to GRAM |
| 0x2E | read_memory_start | 0 (read) | P→H | Begin pixel read from GRAM |
| 0x30 | set_partial_rows | 4 | H→P | Partial display area rows |
| 0x33 | set_scroll_area | 6 | H→P | Scrolling area definition |
| 0x35 | set_tear_on | 1 | H→P | Enable TE (0=V, 1=V+H) |
| 0x36 | set_address_mode | 1 | H→P | Rotation/mirror/color order |
| 0x37 | set_scroll_start | 2 | H→P | Scroll start line |
| 0x38 | idle_mode_off | 0 | H→P | Exit idle mode |
| 0x39 | idle_mode_on | 0 | H→P | Enter idle mode |
| 0x3A | set_pixel_format | 1 | H→P | Color depth (0x55/0x66/0x77) |
| 0x3C | write_memory_continue | N | H→P | Continue pixel write |
| 0x44 | set_tear_scanline | 2 | H→P | TE signal at specific line |
| 0x51 | set_brightness | 1 | H→P | Backlight level (0-255) |
| 0x52 | get_brightness | 0 (read) | P→H | Read current brightness |
| 0x53 | write_ctrl_display | 1 | H→P | BL ctrl, dimming, BCTRL |
| 0x55 | write_CABC | 1 | H→P | CABC mode (off/UI/still/moving) |

---

## 2. PIXEL FORMAT CODES (DCS 0x3A)

| Code | Format | Bits/Pixel | Description |
|------|--------|-----------|-------------|
| 0x55 | RGB565 | 16 | 5R + 6G + 5B |
| 0x66 | RGB666 | 18 | 6R + 6G + 6B (packed) |
| 0x77 | RGB888 | 24 | 8R + 8G + 8B (standard) |
| 0x67 | RGB666/RGB888 | 18/24 | IF/OUT different (rare) |

---

## 3. DATA TYPE CODES (DSI Packets)

| DT | Type | Short/Long | Direction | Use |
|----|------|-----------|-----------|-----|
| 0x01 | V-Sync Start | Short | H→P | Frame begin |
| 0x11 | V-Sync End | Short | H→P | Frame end |
| 0x21 | H-Sync Start | Short | H→P | Line begin |
| 0x31 | H-Sync End | Short | H→P | Line end (Sync Pulse only) |
| 0x05 | DCS Short Write 0 | Short | H→P | Command only |
| 0x15 | DCS Short Write 1 | Short | H→P | Command + 1 param |
| 0x06 | DCS Read | Short | H→P | Request read (triggers BTA) |
| 0x39 | DCS Long Write | Long | H→P | Multi-byte command |
| 0x08 | EoTp | Short | H→P | End of HS transmission |
| 0x09 | Null | Long | H→P | Filler/padding |
| 0x19 | Blanking | Long | H→P | Blanking data |
| 0x0E | Pixel 16-bit | Long | H→P | RGB565 pixels |
| 0x1E | Pixel 18-bit packed | Long | H→P | RGB666 packed |
| 0x2E | Pixel 18-bit loose | Long | H→P | RGB666 (3 bytes/pixel) |
| 0x3E | Pixel 24-bit | Long | H→P | RGB888 pixels |
| 0x0B | Pixel 30-bit | Long | H→P | RGB101010 |
| 0x02 | Ack & Error | Short | P→H | Error report (after BTA) |
| 0x21 | Short Read 1B | Short | P→H | 1-byte read response |
| 0x22 | Short Read 2B | Short | P→H | 2-byte read response |
| 0x1A | Long Read | Long | P→H | Multi-byte read response |

---

## 4. BANDWIDTH CALCULATOR

```
Formula:
  BW = Width × Height × BPP × FPS × (1 + Blanking_OH)

Where Blanking_OH = (H_total × V_total)/(Width × Height) - 1

Quick reference:
┌────────────────┬────────┬───────┬──────┬──────────┬────────────────┐
│ Resolution     │ Color  │ FPS   │ OH%  │ Data Rate│ 4L Speed Needed│
├────────────────┼────────┼───────┼──────┼──────────┼────────────────┤
│ 720×1280       │ RGB888 │ 60    │ 12%  │ 1.24 Gbps│ 310 Mbps/lane  │
│ 1080×1920      │ RGB888 │ 60    │ 10%  │ 2.74 Gbps│ 685 Mbps/lane  │
│ 1080×2340      │ RGB888 │ 60    │ 10%  │ 3.34 Gbps│ 835 Mbps/lane  │
│ 1440×2560      │ RGB888 │ 60    │ 10%  │ 4.87 Gbps│ 1217 Mbps/lane │
│ 1920×720(auto) │ RGB888 │ 60    │ 12%  │ 2.24 Gbps│ 560 Mbps/lane  │
│ 2560×1440(auto)│ RGB888 │ 60    │ 10%  │ 4.87 Gbps│ 1217 Mbps/lane │
│ 3840×2160      │ RGB888 │ 60    │ 10%  │ 10.9 Gbps│ DUAL-DSI needed│
│ 2560×1440+DSC  │ 12-bit │ 60    │ 5%   │ 2.55 Gbps│ 637 Mbps/lane  │
└────────────────┴────────┴───────┴──────┴──────────┴────────────────┘

DSI bit clock = Required_BW / Lanes / 2 (DDR)
  Example: 2.24 Gbps / 4 / 2 = 280 MHz PHY clock
```

---

## 5. DEVICE TREE SNIPPET (Qualcomm DSI Panel)

```dts
&mdss_dsi0 {
    status = "ok";
    
    panel@0 {
        compatible = "vendor,panel-model";
        reg = <0>;
        
        /* Power supplies */
        vci-supply = <&pm8350_l13>;    /* 3.3V */
        vddi-supply = <&pm8350_l14>;   /* 1.8V */
        
        /* Control GPIOs */
        reset-gpios = <&tlmm 24 GPIO_ACTIVE_LOW>;
        
        /* DSI configuration */
        qcom,mdss-dsi-panel-type = "dsi_video_mode";
        qcom,mdss-dsi-lane-0-state;
        qcom,mdss-dsi-lane-1-state;
        qcom,mdss-dsi-lane-2-state;
        qcom,mdss-dsi-lane-3-state;
        
        /* Timing */
        qcom,mdss-dsi-panel-width = <1920>;
        qcom,mdss-dsi-panel-height = <720>;
        qcom,mdss-dsi-h-front-porch = <100>;
        qcom,mdss-dsi-h-back-porch = <36>;
        qcom,mdss-dsi-h-pulse-width = <4>;    /* HSA */
        qcom,mdss-dsi-v-front-porch = <16>;
        qcom,mdss-dsi-v-back-porch = <16>;
        qcom,mdss-dsi-v-pulse-width = <4>;    /* VSA */
        qcom,mdss-dsi-panel-framerate = <60>;
        
        /* Color */
        qcom,mdss-dsi-bpp = <24>;             /* RGB888 */
        qcom,mdss-dsi-color-order = "rgb_swap_rgb";
        
        /* Clock */
        qcom,mdss-dsi-panel-clock-rate = <891000000>;
        
        /* Features */
        qcom,mdss-dsi-tx-eot-append;
        qcom,mdss-dsi-cont-splash-enabled;
        qcom,mdss-dsi-traffic-mode = "burst_mode";
        
        /* Backlight */
        qcom,mdss-dsi-bl-pmic-control-type = "bl_ctrl_wled";
        qcom,mdss-dsi-bl-min-level = <1>;
        qcom,mdss-dsi-bl-max-level = <4095>;
        
        /* Init commands */
        qcom,mdss-dsi-on-command = [
            05 01 00 00 78 00 01 11    /* exit_sleep, wait 120ms */
            15 01 00 00 00 00 02 3A 77 /* pixel_format RGB888 */
            05 01 00 00 14 00 01 29    /* display_on, wait 20ms */
        ];
        
        qcom,mdss-dsi-off-command = [
            05 01 00 00 14 00 01 28    /* display_off, wait 20ms */
            05 01 00 00 78 00 01 10    /* enter_sleep, wait 120ms */
        ];
    };
};
```

---

## 6. COMMON LINUX/ANDROID COMMANDS

```bash
# ==========================================
# DRM/KMS Display Debugging
# ==========================================

# List DRM devices and connectors
ls /sys/class/drm/
cat /sys/class/drm/card0-DSI-1/status        # connected/disconnected
cat /sys/class/drm/card0-DSI-1/modes          # supported modes
cat /sys/class/drm/card0-DSI-1/enabled        # current state

# modetest (requires libdrm-tests)
modetest -M msm -c                            # List connectors
modetest -M msm -p                            # List planes
modetest -M msm -e                            # List encoders

# Display test pattern
modetest -M msm -s 51:1920x720 -P 35@49:1920x720@XR24

# ==========================================
# Panel & DSI Control
# ==========================================

# Read panel registers (via debugfs)
echo "0x0A 1" > /sys/kernel/debug/dsi0/cmd    # Read power_mode (1 byte)
cat /sys/kernel/debug/dsi0/cmd                 # Show response

# Write DCS command
echo "0x51 0xC8" > /sys/kernel/debug/dsi0/cmd # Set brightness to 200

# Check DSI status
cat /sys/kernel/debug/dsi0/status
cat /sys/kernel/debug/dsi0/phy_status          # PHY lock status

# ==========================================
# DPU / Display Pipeline
# ==========================================

# DPU debug info
cat /sys/kernel/debug/dri/0/debug/dump
cat /sys/kernel/debug/dri/0/state              # Atomic state

# Check VSYNC (confirms display active)
cat /sys/class/drm/card0-DSI-1/vblank_count    # Should increment

# ==========================================
# Backlight Control
# ==========================================

# Direct backlight control
echo 200 > /sys/class/backlight/panel0-backlight/brightness
cat /sys/class/backlight/panel0-backlight/max_brightness
cat /sys/class/backlight/panel0-backlight/actual_brightness

# ==========================================
# Android-Specific
# ==========================================

# Display info
adb shell dumpsys SurfaceFlinger              # Full compositor state
adb shell dumpsys display                     # Display configuration
adb shell wm size                             # Current resolution
adb shell wm density                          # Current DPI

# Force screen refresh
adb shell service call SurfaceFlinger 1
# Screenshot
adb shell screencap -p /sdcard/screen.png

# Display errors
adb shell dmesg | grep -i "dsi\|mdss\|dpu\|panel\|disp"
```

---

## 7. ERROR BITS (Acknowledge & Error Report)

| Bit | Error | Meaning | Action |
|-----|-------|---------|--------|
| 0 | SoT Error | HS start sync failed | Check PHY timing |
| 1 | SoT Sync Error | LP→HS transition bad | Check clock lane timing |
| 2 | EoT Sync Error | HS→LP transition bad | Verify EoTp sent |
| 3 | Escape Mode Entry Error | LP escape failed | Check LP driver |
| 4 | LP TX Sync Error | LP data sync issue | Reduce LP clock |
| 5 | Peripheral Timeout | Panel can't process fast enough | Increase blanking |
| 6 | False Control Error | Unexpected control sequence | Check DSI controller |
| 7 | Contention Detected | Bus conflict | BTA timing issue |
| 8 | ECC 1-bit (corrected) | Header had single bit error | Monitor rate |
| 9 | ECC Multi-bit | Header uncorrectable | Discard packet |
| 10 | CRC Error | Payload checksum failed | Check signal integrity |
| 11 | Data Type Not Recognized | Unknown DT code sent | Check packet generation |
| 12 | VC ID Invalid | Wrong virtual channel | Check DI byte |
| 13 | Invalid TX Length | WC doesn't match actual | Check DSI controller |
| 15 | DSI Protocol Violation | General protocol error | Full DSI debug |

---

## 8. POWER-ON/OFF SEQUENCE TEMPLATE

```
POWER ON:
  1. VCI  (3.0-3.3V analog)    → ON → wait 1ms
  2. VDDI (1.8V digital I/O)   → ON → wait 1ms
  3. RESET_N                   → LOW (10µs) → HIGH → wait 10ms
  4. DSI lanes → LP-11         → wait 100µs (T_INIT)
  5. DCS: exit_sleep (0x11)    → wait 120ms
  6. DCS: set_pixel_fmt (0x3A) → no wait
  7. DCS: panel-specific init  → varies (gamma, voltage, timing)
  8. DCS: display_on (0x29)    → wait 20ms
  9. Start HS video stream     → immediate
  10. Backlight ON             → after first frame (avoid flash)

POWER OFF:
  1. Backlight OFF             → immediate
  2. Stop HS video stream      → return to LP
  3. DCS: display_off (0x28)   → wait 20ms
  4. DCS: enter_sleep (0x10)   → wait 120ms
  5. DSI lanes → ULPS or LP-11
  6. RESET_N → LOW
  7. VDDI → OFF → wait 1ms
  8. VCI  → OFF

Total ON:  ~165 ms
Total OFF: ~160 ms
```

---

## 9. VIDEO MODE TIMING QUICK CALC

```
Given: Panel timing parameters from datasheet

Pixel Clock:
  pclk = (Width + HBP + HFP + HSA) × (Height + VBP + VFP + VSA) × FPS
  
DSI Bit Clock (non-burst):
  bit_clk = pclk × BPP / Lanes
  
DSI Byte Clock:
  byte_clk = bit_clk / 8
  
Example: 1920×720@60, RGB888, 4-lane:
  H_total = 1920 + 36 + 100 + 4 = 2060
  V_total = 720 + 16 + 16 + 4 = 756
  pclk = 2060 × 756 × 60 = 93,441,600 Hz ≈ 93.4 MHz
  bit_clk = 93.4M × 24 / 4 = 560.7 Mbps/lane
  byte_clk = 560.7M / 8 = 70.1 MHz
  PHY_clk (DDR) = 560.7 / 2 = 280.3 MHz

Burst mode:
  actual_bit_clk > required_bit_clk (run faster, idle in LP)
  Example: Set PHY to 891 Mbps/lane → burst utilization = 560.7/891 = 63%
           → 37% of line time in LP (power savings)
```

---

## 10. QUICK DIAGNOSTIC FLOWCHART

```
Black screen?
├─ Check power (VCI, VDDI with multimeter)
│  └─ Off? → Check regulator enable, PMIC config
├─ Check RESET toggle (scope)
│  └─ Stuck LOW? → GPIO config wrong
├─ Check DSI PHY PLL lock
│  └─ Not locked? → PLL config, reference clock missing
├─ Check LP-11 on lanes (scope: both P/N at 1.2V)
│  └─ Not LP-11? → PHY not initialized
├─ Check DCS commands sent (DSI FIFO status)
│  └─ FIFO full/error? → DSI controller config
├─ Read panel ID via BTA
│  └─ No response? → Panel not powered or reset
├─ Check video mode started (VSYNC interrupt)
│  └─ No VSYNC? → DPU not configured, INTF not started
└─ Check backlight
   └─ Off? → PWM/WLED not enabled (display works but DARK)

White screen?
├─ DSI active but no valid pixel data
├─ Check DPU pipe connected to DSI
├─ Check framebuffer allocated and mapped
└─ Check pixel format match (RGB888 vs RGB666)

Flickering?
├─ Timing mismatch → increase blanking margins
├─ PLL instability → check reference clock
├─ Backlight PWM frequency too low → increase to 30+ kHz
└─ Temperature drift → verify PLL lock across temperature

Tearing (command mode)?
├─ TE not enabled → DCS 0x35
├─ TE GPIO not connected → check hardware
└─ Host not syncing to TE → enable TE interrupt handling
```

---

## 11. SA8295P DISPLAY HARDWARE SUMMARY

```
DPU (Display Processing Unit):
  SSPP (Source Surface Pipe): 4× ViG (video/graphics) + 4× DMA (UI)
  Layer Mixers: 6 (can pair for dual-DSI)
  DSPP: Color processing per display
  INTF: 4 timing interfaces
  DSC encoders: 2 (for compressed output)

DSI Controllers: 2 (DSI0, DSI1)
  Each: 4 D-PHY lanes, max 2.5 Gbps/lane
  Features: Video mode, Command mode, DSC

DisplayPort: 2 (DP0, DP1)
  For: HUD, rear-seat, external monitors

Writeback: 1 (capture DPU output to memory)

Total displays: Up to 4 simultaneous
  DSI0 + DSI1 + DP0 + DP1

Clock domains:
  core_clk: DPU core (up to 460 MHz)
  mdp_clk: Pixel processing
  byte_clk: DSI byte clock (derived from PLL)
  pixel_clk: DSI pixel clock
  esc_clk: LP escape clock (~19.2 MHz)
```

---

END OF DOCUMENT 06 — CHEATSHEET
