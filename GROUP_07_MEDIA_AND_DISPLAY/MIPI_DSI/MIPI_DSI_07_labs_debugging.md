# MIPI DSI — LABS & DEBUGGING SCENARIOS
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 07 of 08
# Format: Hands-on labs + Real-world debug scenarios
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: First Panel Bringup (Video Mode)

### Objective
Bring up a new DSI video-mode panel on SA8295P from bare hardware.

### Prerequisites
- SA8295P development board with DSI FPC connector
- Panel with datasheet (timing, init sequence, power)
- Oscilloscope (4+ channels) with differential probe
- JTAG/SWD debugger
- Linux kernel source with Qualcomm DRM/DSI drivers

### Steps

#### Step 1: Verify Hardware Connections
```bash
# Check power rails with multimeter
# VCI (analog): Measure at panel connector → expect 3.0-3.3V (PMIC L13)
# VDDI (digital I/O): Measure → expect 1.8V (PMIC L14)
# RESET_N: Check with scope → should be HIGH after boot

# Verify DSI lanes continuity
# Use TDR or continuity test for all 10 differential lines
# Max trace length: 15cm for DSI speeds
```

#### Step 2: Configure Device Tree
```dts
/* File: arch/arm64/boot/dts/qcom/sa8295p-display.dtsi */
&mdss_dsi0 {
    status = "ok";
    
    lab_panel: panel@0 {
        compatible = "lab,first-panel";
        reg = <0>;
        
        /* Power - match panel datasheet */
        vci-supply = <&pm8350_l13>;
        vddi-supply = <&pm8350_l14>;
        reset-gpios = <&tlmm 24 GPIO_ACTIVE_LOW>;
        
        /* Mode */
        qcom,mdss-dsi-panel-type = "dsi_video_mode";
        qcom,mdss-dsi-traffic-mode = "burst_mode";
        
        /* Resolution from datasheet */
        qcom,mdss-dsi-panel-width = <1920>;
        qcom,mdss-dsi-panel-height = <720>;
        
        /* Timing from datasheet */
        qcom,mdss-dsi-h-front-porch = <100>;
        qcom,mdss-dsi-h-back-porch = <36>;
        qcom,mdss-dsi-h-pulse-width = <4>;
        qcom,mdss-dsi-v-front-porch = <16>;
        qcom,mdss-dsi-v-back-porch = <16>;
        qcom,mdss-dsi-v-pulse-width = <4>;
        qcom,mdss-dsi-panel-framerate = <60>;
        
        /* 4 lanes, RGB888 */
        qcom,mdss-dsi-lane-0-state;
        qcom,mdss-dsi-lane-1-state;
        qcom,mdss-dsi-lane-2-state;
        qcom,mdss-dsi-lane-3-state;
        qcom,mdss-dsi-bpp = <24>;
        
        /* Clock from bandwidth calculation */
        qcom,mdss-dsi-panel-clock-rate = <891000000>;
        
        /* Init sequence from panel vendor */
        qcom,mdss-dsi-on-command = [
            05 01 00 00 78 00 01 11   /* exit_sleep, 120ms delay */
            15 01 00 00 00 00 02 3A 77 /* pixel_format = RGB888 */
            05 01 00 00 14 00 01 29   /* display_on, 20ms delay */
        ];
        
        qcom,mdss-dsi-off-command = [
            05 01 00 00 14 00 01 28   /* display_off, 20ms */
            05 01 00 00 78 00 01 10   /* enter_sleep, 120ms */
        ];
        
        /* EoTp */
        qcom,mdss-dsi-tx-eot-append;
    };
};
```

#### Step 3: Write Panel Driver
```c
/* drivers/gpu/drm/panel/panel-lab-first.c */
#include <drm/drm_mipi_dsi.h>
#include <drm/drm_panel.h>

struct lab_panel {
    struct drm_panel panel;
    struct mipi_dsi_device *dsi;
    struct gpio_desc *reset;
    struct regulator *vci;
    struct regulator *vddi;
};

static int lab_panel_prepare(struct drm_panel *panel)
{
    struct lab_panel *p = container_of(panel, struct lab_panel, panel);
    
    /* Power on sequence */
    regulator_enable(p->vci);
    usleep_range(1000, 2000);
    regulator_enable(p->vddi);
    usleep_range(1000, 2000);
    
    /* Reset pulse */
    gpiod_set_value_cansleep(p->reset, 1);  /* Assert */
    usleep_range(10, 20);
    gpiod_set_value_cansleep(p->reset, 0);  /* Deassert */
    msleep(10);
    
    return 0;
}

static int lab_panel_enable(struct drm_panel *panel)
{
    struct lab_panel *p = container_of(panel, struct lab_panel, panel);
    struct mipi_dsi_device *dsi = p->dsi;
    
    mipi_dsi_dcs_exit_sleep_mode(dsi);
    msleep(120);
    
    mipi_dsi_dcs_set_pixel_format(dsi, MIPI_DCS_PIXEL_FMT_24BIT);
    
    mipi_dsi_dcs_set_display_on(dsi);
    msleep(20);
    
    return 0;
}

static int lab_panel_disable(struct drm_panel *panel)
{
    struct lab_panel *p = container_of(panel, struct lab_panel, panel);
    
    mipi_dsi_dcs_set_display_off(p->dsi);
    msleep(20);
    mipi_dsi_dcs_enter_sleep_mode(p->dsi);
    msleep(120);
    
    return 0;
}

static int lab_panel_unprepare(struct drm_panel *panel)
{
    struct lab_panel *p = container_of(panel, struct lab_panel, panel);
    
    gpiod_set_value_cansleep(p->reset, 1);
    regulator_disable(p->vddi);
    usleep_range(1000, 2000);
    regulator_disable(p->vci);
    
    return 0;
}
```

#### Step 4: Verify with Scope
```
Probe points:
  CH1: RESET_N → verify pulse timing
  CH2: CLK_P (differential) → verify HS entry
  CH3: D0_P → verify LP→HS→LP transitions
  CH4: TE pin (if available)

Expected sequence on boot:
  1. RESET pulse: LOW 10µs → HIGH
  2. LP-11 for >100µs
  3. LP escape sequence (DCS commands)
  4. HS burst (video data)
  5. Continuous HS or burst+LP pattern
```

#### Step 5: Validate
```bash
# Check panel detected
cat /sys/class/drm/card0-DSI-1/status
# Expected: "connected"

# Check mode
cat /sys/class/drm/card0-DSI-1/modes
# Expected: "1920x720"

# Display test pattern
modetest -M msm -s 51:1920x720 -P 35@49:1920x720@XR24
# Should see colored bars on panel

# Verify VSYNC
cat /proc/interrupts | grep vsync
# Should see incrementing count
```

### Expected Results
- Panel displays test pattern
- No error messages in dmesg
- Stable 60 FPS (16.67 ms per frame)
- No visible artifacts

---

## LAB 2: Multi-Display Automotive Setup

### Objective
Configure SA8295P for 3-display automotive cockpit:
- DSI0: Instrument cluster (1920×720@60, video mode)
- DSI1: IVI center stack (2560×1440@60, video mode with DSC)
- DP0: Rear-seat entertainment (1920×1080@60)

### Steps

#### Step 1: Display Topology Configuration
```dts
/* DPU resource allocation */
&mdss {
    status = "ok";
    
    /* Cluster: SSPP VIG0 → LM0 → DSPP0 → INTF0 → DSI0 */
    /* IVI:     SSPP VIG1+VIG2 → LM2+LM3 → DSC0 → INTF1 → DSI1 */
    /* Rear:    SSPP VIG3 → LM4 → INTF2 → DP0 */
};

&mdss_dsi0 {
    status = "ok";
    /* Cluster panel config (same as Lab 1) */
    cluster_panel: panel@0 { ... };
};

&mdss_dsi1 {
    status = "ok";
    ivi_panel: panel@0 {
        compatible = "vendor,ivi-panel-2560x1440";
        /* 2560×1440 requires DSC for 4-lane DSI */
        qcom,mdss-dsi-panel-width = <2560>;
        qcom,mdss-dsi-panel-height = <1440>;
        qcom,mdss-dsi-compression-mode = "dsc";
        qcom,mdss-dsc-slice-height = <16>;
        qcom,mdss-dsc-slice-width = <1280>;
        qcom,mdss-dsc-slice-per-pkt = <2>;
        qcom,mdss-dsc-bit-per-component = <8>;
        qcom,mdss-dsc-bit-per-pixel = <12>;  /* 2:1 compression */
        qcom,mdss-dsi-panel-clock-rate = <1000000000>;
        ...
    };
};
```

#### Step 2: Verify Independence
```bash
# Verify each display runs independently
cat /sys/class/drm/card0-DSI-1/status  # Cluster
cat /sys/class/drm/card0-DSI-2/status  # IVI
cat /sys/class/drm/card0-DP-1/status   # Rear-seat

# Check VSYNC is independent
cat /sys/kernel/debug/dri/0/crtc-0/state  # Cluster CRTC
cat /sys/kernel/debug/dri/0/crtc-1/state  # IVI CRTC
cat /sys/kernel/debug/dri/0/crtc-2/state  # Rear CRTC
```

#### Step 3: Content Isolation Test
```bash
# Crash test: Kill IVI process, verify cluster unaffected
kill -9 $(pidof ivi_app)
# Cluster should continue displaying without any glitch
# Monitor cluster VSYNC count - should never pause:
watch -n 0.1 "cat /sys/class/drm/card0-DSI-1/vblank_count"
```

### Success Criteria
- All 3 displays active simultaneously
- Cluster unaffected by IVI/rear-seat crash
- DSC active on IVI (verify via debugfs)
- Total DPU bandwidth within budget

---

## LAB 3: Continuous Splash (Fast Boot Display)

### Objective
Achieve display-on within 1 second from power-on for instrument cluster.

### Architecture
```
Bootloader (XBL/ABL) → Initialize DSI → Show splash image
        ↓ (handoff)
Kernel (DRM/KMS) → Take over running DSI → No flicker
        ↓ (handoff)
Android (SurfaceFlinger) → Start compositing → Seamless transition
```

### Steps

#### Step 1: Bootloader Panel Init
```c
/* In XBL/ABL display driver */
/* Must match EXACT same settings as kernel DT */
static void panel_early_init(void) {
    /* Power on (same sequence as kernel) */
    pmic_enable_ldo(L13, 3300);  // VCI
    delay_ms(1);
    pmic_enable_ldo(L14, 1800);  // VDDI
    delay_ms(1);
    
    /* Reset */
    gpio_set(RESET_PIN, 0);
    delay_us(10);
    gpio_set(RESET_PIN, 1);
    delay_ms(10);
    
    /* DSI PHY + controller init */
    dsi_phy_init(4_LANES, 891_MBPS);
    
    /* Panel init */
    dsi_cmd_write(0x11, NULL, 0);  // exit_sleep
    delay_ms(120);
    dsi_cmd_write(0x3A, (uint8_t[]){0x77}, 1);  // RGB888
    dsi_cmd_write(0x29, NULL, 0);  // display_on
    delay_ms(20);
    
    /* Start video mode (splash image in framebuffer) */
    dpu_setup_pipe(SPLASH_FB_ADDR, 1920, 720, RGB888);
    dsi_video_start();
}
```

#### Step 2: Kernel Handoff
```dts
/* Device tree: Tell kernel bootloader already initialized display */
&mdss_dsi0 {
    qcom,mdss-dsi-cont-splash-enabled;  /* Critical flag */
    
    panel@0 {
        /* All settings MUST match bootloader exactly */
        /* If mismatch: screen will glitch during handoff */
    };
};
```

#### Step 3: Verify Timing
```bash
# Add timestamps to boot log
dmesg | grep -i "splash\|display\|panel\|dsi" | head -20

# Expected timeline:
# [0.0s] Power on
# [0.2s] Bootloader initializes display
# [0.5s] Splash image visible (<1s target met)
# [3.0s] Kernel takes over (no flicker)
# [6.0s] Android SurfaceFlinger starts compositing

# Verify no re-initialization
dmesg | grep "cont_splash"
# Should see: "Continuous splash enabled, skipping panel init"
```

#### Step 4: Measure with Scope
```
Probe: Panel backlight enable pin
Trigger: Power-on (ignition signal)
Measure: Time from trigger to backlight stable HIGH
Target: < 1000 ms

If >1s: Optimize bootloader splash path:
  - Pre-configure PMIC in XBL (saves regulator delays)
  - Minimize panel sleep-exit delay (some panels: 60ms instead of 120ms)
  - Parallelize: Load splash image while waiting for panel
```

---

## LAB 4: DSI Error Monitoring & Recovery

### Objective
Implement robust error detection and automatic recovery for production automotive display.

### Steps

#### Step 1: Enable Error Interrupts
```c
/* In DSI host driver: Enable all error interrupts */
static void dsi_enable_error_irqs(struct dsi_ctrl *ctrl) {
    /* Acknowledge/Error report IRQ */
    writel(0xFFFF, ctrl->base + DSI_ERR_INT_MASK0);
    
    /* FIFO overflow/underflow */
    writel(0x3F, ctrl->base + DSI_FIFO_STATUS_MASK);
    
    /* Timeout IRQs */
    writel(0x0F, ctrl->base + DSI_TIMEOUT_STATUS_MASK);
    
    /* PHY errors */
    writel(0xFF, ctrl->base + DSI_PHY_ERR_MASK);
}
```

#### Step 2: Error Counter and Threshold
```bash
# Monitor error rates
watch -n 1 "cat /sys/kernel/debug/dsi0/error_count"

# Error threshold for recovery:
#   CRC errors > 10/sec → signal integrity issue
#   Timeout > 3 consecutive → panel hung
#   Underflow > 1 → bandwidth issue

# Create monitoring script
#!/bin/bash
while true; do
    errors=$(cat /sys/kernel/debug/dsi0/error_count)
    if [ "$errors" -gt 10 ]; then
        echo "DSI error threshold exceeded: $errors"
        echo 1 > /sys/kernel/debug/dsi0/reset  # Trigger recovery
    fi
    sleep 1
done
```

#### Step 3: Recovery Sequence
```c
/* Recovery implementation */
static int dsi_error_recovery(struct dsi_ctrl *ctrl) {
    pr_warn("DSI: Error recovery initiated\n");
    
    /* 1. Stop video stream */
    dsi_video_stop(ctrl);
    
    /* 2. Reset PHY */
    dsi_phy_reset(ctrl);
    msleep(1);
    
    /* 3. Reconfigure PHY PLL */
    dsi_phy_pll_setup(ctrl);
    
    /* 4. Re-send panel init */
    dsi_panel_reinit(ctrl);
    
    /* 5. Restart video */
    dsi_video_start(ctrl);
    
    /* 6. Verify (read panel status) */
    uint8_t power_mode;
    dsi_dcs_read(ctrl, 0x0A, &power_mode, 1);
    if (power_mode != 0x9C) {
        pr_err("DSI: Recovery failed, power_mode=0x%02x\n", power_mode);
        return -EIO;
    }
    
    pr_info("DSI: Recovery successful\n");
    return 0;
}
```

---

## LAB 5: DSI Latency Measurement (E-Mirror)

### Objective
Measure and optimize camera-to-display latency for electronic mirror application.
Target: < 100 ms end-to-end.

### Test Setup
```
Camera (CSI-2) → ISP → GPU/DPU → DSI → Panel

Measurement method:
  1. LED + photodiode at camera input
  2. Photodiode at panel output
  3. Measure time between LED flash and display response
```

### Steps

#### Step 1: Baseline Measurement
```bash
# Check current pipeline depth
# Camera: V4L2 buffer count
v4l2-ctl -d /dev/video0 --get-parm
# Buffer count: 3 = 3×16.67ms = 50ms latency

# Display: DRM pending flip count
cat /sys/kernel/debug/dri/0/crtc-0/state
# vblank_count delta to commit = 1-2 frames = 16-33ms

# Total baseline: ~80-100ms
```

#### Step 2: Optimize Pipeline
```bash
# Reduce camera buffers to 2 (minimum for streaming)
v4l2-ctl -d /dev/video0 --set-parm=2

# Use fence-based zero-copy path
# Camera → DMA-BUF → DPU (skip GPU copy)

# Enable low-latency mode
echo "low_latency" > /sys/class/graphics/fb0/display_mode
```

#### Step 3: Verify Final Latency
```
Optimized pipeline:
  Camera capture: 16.67 ms (1 frame exposure)
  ISP processing: 8 ms
  Buffer transfer: 1 ms (DMA-BUF zero-copy)
  DPU composition: 0.5 ms
  DSI transmission: 8 ms (1920×720×24 / 4×2.5Gbps)
  Panel response: 10 ms (LCD pixel response)
  ─────────────────────────────────────
  Total: ~44 ms (well under 100 ms target)
```

---

---

# DEBUG SCENARIOS

---

## SCENARIO 1: Black Screen After Boot

### Symptoms
- Panel backlight ON (screen is lit)
- No image content visible
- dmesg shows "DSI-1: connected"

### Diagnostic Steps

```bash
# Step 1: Is video stream active?
cat /sys/kernel/debug/dri/0/crtc-0/state
# Look for: "active=1"

# Step 2: Check DSI controller status
cat /sys/kernel/debug/dsi0/status
# Look for: VIDEO_MODE_ENGINE=ON, CMD_MODE_ENGINE=OFF

# Step 3: Read panel power mode
echo "0x0A 1" > /sys/kernel/debug/dsi0/cmd
cat /sys/kernel/debug/dsi0/cmd
# Expected: 0x9C (normal mode, display on)
# Got 0x10? Panel in sleep mode → init failed

# Step 4: Check DPU pipe allocation
cat /sys/kernel/debug/dri/0/debug/dump
# Look for: SSPP connected to LM connected to INTF connected to DSI

# Step 5: Check for underflow
dmesg | grep -i "underflow\|underrun"
# If underflow: DPU can't feed pixels fast enough
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| Panel power_mode = 0x10 | Sleep exit failed | Check 120ms delay after 0x11 |
| Panel power_mode = 0x08 | Panel in partial mode | Send 0x13 (enter_normal) |
| No SSPP in pipe | DPU not configured | Check DT topology assignment |
| Underflow errors | Clock too slow or bandwidth exceeded | Increase core_clk or reduce resolution |
| DSI FIFO error | Init commands not reaching panel | Check LP clock frequency |

---

## SCENARIO 2: White Screen (All White)

### Symptoms
- Panel shows uniform white (or single color)
- Backlight working
- Some sign of DSI activity

### Diagnostic Steps

```bash
# Step 1: Is panel receiving valid pixel data?
# White = panel powered and DSI link active, but pixels are wrong

# Step 2: Check framebuffer content
cat /dev/fb0 | md5sum
# If all FF: Buffer not being written by GPU/SurfaceFlinger

# Step 3: Check color format mismatch
cat /sys/kernel/debug/dsi0/panel_info | grep bpp
# Must match: Panel expects RGB888 (0x77) AND host sends 0x3E DT

# Step 4: Check if panel in test mode
# Some panels have vendor-specific test mode commands
# Read manufacturer registers

# Step 5: Verify DPU output format
cat /sys/kernel/debug/dri/0/debug/dump | grep -i format
# Check plane format matches DSI output format
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| FB content is valid | DSI color format mismatch | Fix DCS 0x3A and DT bpp |
| FB is all zeros/FF | No app rendering to buffer | Check GPU / SurfaceFlinger |
| Panel in test mode | Init sequence puts panel in test mode | Remove vendor test command |
| DSI link but wrong data | Lane swap/polarity error | Fix DT lane mapping |
| DPU format RGB but panel needs BGR | Color order wrong | Add `qcom,mdss-dsi-color-order = "rgb_swap_bgr"` |

---

## SCENARIO 3: Display Flickering

### Symptoms
- Image visible but flickering (whole screen or bands)
- Periodic at frame rate or sub-frame rate

### Diagnostic Steps

```bash
# Step 1: Is it full-frame or partial flicker?
# Full frame: Likely VSYNC/timing issue
# Horizontal bands: Likely DSI link issue

# Step 2: Check PLL stability
cat /sys/kernel/debug/dsi0/phy_status
# PLL_LOCK should be stable 1

# Step 3: Measure frame rate consistency
cat /sys/kernel/debug/dri/0/crtc-0/fps
# Should be stable 60.0 ± 0.1

# Step 4: Check for repeated underflow
dmesg | grep -c underflow
# Any underflow = momentary blank → flicker

# Step 5: Backlight PWM check
cat /sys/class/backlight/panel0-backlight/type
# PWM frequency should be >20 kHz (invisible flicker)
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| PLL unlock intermittent | Reference clock jitter | Check crystal/PLL loop filter |
| Underflow in dmesg | Bandwidth contention | Increase clocks or reduce layers |
| FPS varying ±5% | VSYNC source unstable | Check INTF timing generator config |
| Flicker only in cold | Component value drift at temperature | Add PLL temperature compensation |
| Backlight PWM at 200 Hz | Visible PWM | Increase to 30+ kHz |

---

## SCENARIO 4: Tearing (Image Split)

### Symptoms
- Top and bottom portions of screen show different frames
- Moving content shows horizontal "tear" line
- More visible with fast-changing content

### Diagnostic Steps

```bash
# Step 1: What display mode?
cat /sys/kernel/debug/dsi0/panel_info | grep type
# Video mode: Should NOT tear (continuous sync)
# Command mode: Common if TE not handled

# Step 2: For command mode - check TE
cat /sys/kernel/debug/dsi0/te_status
# TE enabled? TE GPIO connected? TE interrupt firing?

# Step 3: For video mode - check double buffering
cat /sys/kernel/debug/dri/0/crtc-0/state | grep -i flip
# Atomic flips should be VSYNC-aligned

# Step 4: Check if app is doing page flip
adb shell dumpsys SurfaceFlinger | grep -i "flip\|fence"
# Fence signaling should align with VSYNC
```

### Root Causes & Fixes

| Mode | Root Cause | Fix |
|------|-----------|-----|
| Command | TE not enabled | Send DCS 0x35, enable TE GPIO IRQ |
| Command | TE GPIO not wired | Add HW connection or use timer-based TE |
| Video | SurfaceFlinger not synced | Enable VSYNC-aligned atomic commits |
| Video | GPU rendering >16.67ms | Reduce GPU load or enable triple buffering |
| Both | Clock drift between host and panel | Use panel's TE for master timing |

---

## SCENARIO 5: Color Shift / Wrong Colors

### Symptoms
- Image visible but colors are wrong
- Red appears blue, green appears red, etc.
- Or overall color cast (too warm/cool)

### Diagnostic Steps

```bash
# Step 1: Display known pattern
modetest -M msm -s 51:1920x720 -P 35@49:1920x720@RG24
# RG24 = RGB ordering. Try BG24 and see which looks correct

# Step 2: Check pixel format chain
# GPU output format → DPU plane format → DSI data type → Panel expected format
adb shell dumpsys SurfaceFlinger | grep -i format
cat /sys/kernel/debug/dri/0/debug/dump | grep format

# Step 3: Check DCS pixel format
# Panel should be set to 0x77 (RGB888)
echo "0x0C 1" > /sys/kernel/debug/dsi0/cmd  # get_pixel_format
cat /sys/kernel/debug/dsi0/cmd

# Step 4: Check color order in DT
grep "color-order" /sys/kernel/debug/dsi0/panel_info
```

### Root Causes & Fixes

| Symptom | Root Cause | Fix |
|---------|-----------|-----|
| R↔B swapped | RGB vs BGR byte order | Change DT color-order or panel init |
| Washed out | 18-bit treated as 24-bit | Fix pixel_format DCS command to 0x77 |
| Green tint | Gamma table wrong | Fix vendor-specific gamma init |
| Dark/bright | Wrong BPP packing | Match DT bpp with actual data |
| Color bands | DSI lane swap | Fix lane mapping in DT / PHY config |

---

## SCENARIO 6: Frozen Display (No Updates)

### Symptoms
- Static image displayed
- Touch works but screen content doesn't change
- Or: Last frame before freeze remains visible

### Diagnostic Steps

```bash
# Step 1: Is DPU still running?
cat /sys/class/drm/card0-DSI-1/vblank_count
sleep 1
cat /sys/class/drm/card0-DSI-1/vblank_count
# If same number: DPU or DSI stopped

# Step 2: Check for GPU hang
dmesg | grep -i "gpu\|adreno\|hang\|timeout"
# GPU fault → no new frames rendered → frozen

# Step 3: Check SurfaceFlinger
adb shell dumpsys SurfaceFlinger --latency
# If no new frames: SF or app frozen

# Step 4: DSI interrupt status
cat /sys/kernel/debug/dsi0/irq_status
# Underflow IRQ stuck? Command timeout?

# Step 5: Panel still alive?
echo "0x0A 1" > /sys/kernel/debug/dsi0/cmd
cat /sys/kernel/debug/dsi0/cmd
# 0x9C = panel normal (link is fine, problem is upstream)
# No response = panel died or link broken
```

### Root Causes & Fixes

| Finding | Root Cause | Fix |
|---------|-----------|-----|
| VBLANK incrementing | Upstream frozen (GPU/SF) | Debug GPU/app level |
| VBLANK stopped | DPU halted | Check for underflow→halt |
| Panel no response | DSI link lost | Trigger DSI recovery |
| GPU hang in dmesg | GPU crash | GPU reset / zap shader |
| SF not compositing | SF deadlock | Check binder/fence timeout |

---

## SCENARIO 7: Slow Display Startup (>2 seconds)

### Symptoms
- Display works but takes too long to show first image
- Requirement: <1 second for automotive cluster

### Diagnostic Steps

```bash
# Step 1: Add boot timestamps
dmesg | grep -i "dsi\|panel\|splash\|display" | head -30
# Map exact time of each step

# Step 2: Common delay contributors
# Power sequencing delays: VCI + VDDI + reset = 12ms minimum
# Panel sleep exit: 120ms (MANDATORY, cannot reduce)
# PLL lock time: 1-5ms
# First frame render: depends on content

# Step 3: Check if continuous splash active
dmesg | grep "cont_splash"
# If NOT active: kernel re-inits panel from scratch (~300ms wasted)
```

### Optimization Table

| Delay | Typical | Optimized | Method |
|-------|---------|-----------|--------|
| Power rails | 2ms | 0ms | Pre-configure PMIC in bootloader |
| Reset | 10ms | 5ms | Reduce if panel datasheet allows |
| Sleep exit (0x11) | 120ms | 60ms | Some panels support fast_sleep |
| PLL lock | 5ms | 1ms | Optimize PLL parameters |
| Kernel DSI init | 200ms | 0ms | Continuous splash handoff |
| SF first frame | 500ms | 100ms | Pre-render splash in bootloader |
| **Total** | **837ms** | **166ms** | Requires bootloader display |

---

## SCENARIO 8: Backlight Issues

### Symptoms
- Various: Too dim, flickering, uneven, no backlight

### Diagnostic Steps

```bash
# Step 1: Check backlight subsystem
ls /sys/class/backlight/
cat /sys/class/backlight/panel0-backlight/brightness
cat /sys/class/backlight/panel0-backlight/max_brightness

# Step 2: Direct backlight control
echo 255 > /sys/class/backlight/panel0-backlight/brightness
# Panel should go to max brightness

# Step 3: Check DCS brightness
echo "0x52 1" > /sys/kernel/debug/dsi0/cmd  # read brightness register
cat /sys/kernel/debug/dsi0/cmd
# Should match what was written via 0x51

# Step 4: Check backlight type
cat /sys/class/backlight/panel0-backlight/type
# "raw" = direct PWM, "firmware" = PMIC controlled

# Step 5: For WLED (White LED) backlight
cat /sys/kernel/debug/regmap/*/registers | grep -i wled
```

### Root Causes & Fixes

| Symptom | Root Cause | Fix |
|---------|-----------|-----|
| No backlight at all | BL enable GPIO not set | Check DT `bl-enable-gpios` |
| Flicker at low brightness | PWM frequency too low | Increase PWM period in DT |
| Uneven brightness | LED string imbalance | Check WLED string configuration |
| Backlight on but dim | Wrong scale factor | Fix max_brightness and scaling |
| Backlight before image | BL enabled before first frame | Delay BL enable until VSYNC |
| Buzzing noise | PWM in audible range (1-10kHz) | Change to >20 kHz |

---

## PERFORMANCE TUNING: DPU Bandwidth Budget

```
SA8295P DPU total bandwidth: ~30 GB/s (theoretical)
Practical limit: ~20 GB/s with contention

Budget calculation per display:
  Read BW = Width × Height × BPP × FPS × num_pipes × (1 + compression_OH)
  
Example multi-display budget:
┌─────────────┬────────────┬──────┬────┬──────────────┐
│ Display     │ Resolution │ BPP  │FPS │ Read BW      │
├─────────────┼────────────┼──────┼────┼──────────────┤
│ Cluster     │ 1920×720   │ 4    │ 60 │ 0.33 GB/s    │
│ IVI         │ 2560×1440  │ 4    │ 60 │ 0.88 GB/s    │
│ IVI overlay │ 2560×1440  │ 4    │ 60 │ 0.88 GB/s    │
│ Rear-seat   │ 1920×1080  │ 4    │ 60 │ 0.50 GB/s    │
│ HUD         │ 1280×480   │ 4    │ 60 │ 0.15 GB/s    │
├─────────────┼────────────┼──────┼────┼──────────────┤
│ TOTAL       │            │      │    │ 2.74 GB/s    │
└─────────────┴────────────┴──────┴────┴──────────────┘

Safety margin: Stay below 70% of peak → 14 GB/s budget
2.74 GB/s << 14 GB/s → Safe

If tight:
  - Enable DSC (reduces write bandwidth to DSI)
  - Use rotator for portrait panels (dedicates memory)
  - Reduce overlay layers (GPU composition for lower priority)
```

---

## PERFORMANCE TUNING: EMI Reduction

```
DSI clock harmonics can cause EMC failure (CISPR 25 automotive)

Techniques:
┌──────────────────────────────────┬──────────────────────────────────┐
│ Technique                        │ Effect                           │
├──────────────────────────────────┼──────────────────────────────────┤
│ Spread-spectrum clock (SSC)      │ -10 to -15 dB peak reduction     │
│ DSI scrambling enable            │ -5 to -8 dB (whitens data)       │
│ Burst mode (lower HS duty cycle) │ -3 to -5 dB (less HS time)      │
│ Impedance matching (100Ω)        │ Reduces reflections/ringing      │
│ Common-mode choke on lanes       │ -15 to -20 dB at specific freq   │
│ SerDes (move DSI inside shield)  │ Eliminates DSI radiation on FPC  │
│ Controlled impedance FPC         │ Reduces EMI from trace mismatch  │
│ Shorter trace length             │ Less antenna effect               │
└──────────────────────────────────┴──────────────────────────────────┘

DT settings for EMI:
  qcom,mdss-dsi-panel-clock-ssc-mode = "down_spread";
  qcom,mdss-dsi-panel-clock-ssc-ppm = <5000>;  /* 0.5% */
  qcom,mdss-dsi-dma-scramble;
  qcom,mdss-dsi-traffic-mode = "burst_mode";   /* Minimize HS time */
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
