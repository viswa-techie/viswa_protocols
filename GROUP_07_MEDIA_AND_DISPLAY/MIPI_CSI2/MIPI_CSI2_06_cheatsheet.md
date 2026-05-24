# MIPI CSI-2 — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 06 of 08
# Format: Tables, one-liners, instant lookups
# ════════════════════════════════════════════════════════════════════

---

## 1. DATA TYPE CODE TABLE (Complete)

| DT Code | Type | Description |
|---------|------|-------------|
| 0x00 | Frame Start | Short packet — begin of frame (contains frame#) |
| 0x01 | Frame End | Short packet — end of frame |
| 0x02 | Line Start | Short packet — begin of line |
| 0x03 | Line End | Short packet — end of line |
| 0x10 | Null | Long packet — padding/filler |
| 0x11 | Blanking | Long packet — blanking data |
| 0x12 | Embedded 8-bit | Long packet — sensor metadata (exposure, gain) |
| 0x18 | YUV420 8-bit | Long packet |
| 0x19 | YUV420 10-bit | Long packet |
| 0x1A | YUV420 8-bit (legacy) | Long packet |
| 0x1C | YUV420 8-bit CSPS | Long packet |
| 0x1D | YUV420 10-bit CSPS | Long packet |
| 0x1E | YUV422 8-bit | Long packet — UYVY |
| 0x1F | YUV422 10-bit | Long packet |
| 0x20 | RGB444 | Long packet |
| 0x21 | RGB555 | Long packet |
| 0x22 | RGB565 | Long packet |
| 0x23 | RGB666 | Long packet |
| 0x24 | RGB888 | Long packet |
| 0x28 | RAW6 | Long packet |
| 0x29 | RAW7 | Long packet |
| 0x2A | RAW8 | Long packet |
| 0x2B | RAW10 | Long packet — most common automotive |
| 0x2C | RAW12 | Long packet — HDR cameras |
| 0x2D | RAW14 | Long packet |
| 0x2E | RAW16 | Long packet |
| 0x2F | RAW20 | Long packet |
| 0x30-0x37 | User Defined 1-8 | Long packet — custom data |

---

## 2. D-PHY TIMING PARAMETERS

| Parameter | Symbol | Min | Max | Notes |
|-----------|--------|-----|-----|-------|
| LP pulse width | T_LPX | 50 ns | — | LP-01 duration |
| HS prepare | T_HS-PREPARE | 40ns+4×UI | 85ns+6×UI | Before bridge |
| HS zero | T_HS-ZERO | 105ns+6×UI-T_HS-PREP | — | CDR lock time |
| HS trail | T_HS-TRAIL | max(8×UI, 60ns+4×UI) | — | After last data |
| HS exit | T_HS-EXIT | 100 ns | — | LP-11 before next SoT |
| CLK prepare | T_CLK-PREPARE | 38 ns | 95 ns | Clock lane |
| CLK zero | T_CLK-ZERO | 300ns-T_CLK-PREP | — | Clock lane |
| CLK pre | T_CLK-PRE | 8×UI | — | CLK HS before data HS |
| CLK post | T_CLK-POST | 60ns+52×UI | — | CLK HS after data LP |
| CLK trail | T_CLK-TRAIL | 60 ns | — | Clock lane trail |
| Init (power-on) | T_INIT | 100 µs | — | After power stable |

**UI = Unit Interval = 1/data_rate**
- At 1.5 Gbps: UI = 667 ps
- At 2.0 Gbps: UI = 500 ps
- At 2.5 Gbps: UI = 400 ps

---

## 3. BANDWIDTH CALCULATOR

```
Formula:
  Data Rate (bps) = Width × Height × BitsPerPixel × FPS × (1 + BlankingOverhead)

Blanking overhead: typically 10-20% (use 15% as default)

Quick table:
┌────────────┬───────┬───────┬──────┬──────────┬────────────────────┐
│ Resolution │ BPP   │ FPS   │ OH%  │ Data Rate│ Lanes Needed       │
├────────────┼───────┼───────┼──────┼──────────┼────────────────────┤
│ 1280×720   │ RAW10 │ 30    │ 15%  │ 0.32 Gbps│ 1L@1.5G            │
│ 1920×1080  │ RAW10 │ 30    │ 15%  │ 0.72 Gbps│ 1L@1.5G or 2L@1G  │
│ 1920×1080  │ RAW12 │ 30    │ 15%  │ 0.86 Gbps│ 2L@1.5G            │
│ 2560×1440  │ RAW10 │ 30    │ 15%  │ 1.27 Gbps│ 2L@1.5G            │
│ 3840×2160  │ RAW10 │ 30    │ 15%  │ 2.86 Gbps│ 2L@2.5G or 4L@1.5G│
│ 3840×2160  │ RAW12 │ 30    │ 15%  │ 3.43 Gbps│ 4L@1.5G            │
│ 3840×2160  │ RAW12 │ 60    │ 15%  │ 6.87 Gbps│ 4L@2.5G            │
│ 4096×3072  │ RAW10 │ 30    │ 15%  │ 4.34 Gbps│ 4L@2.0G            │
└────────────┴───────┴───────┴──────┴──────────┴────────────────────┘

Lane utilization guideline: Keep < 80% for margin
  Available BW = Lanes × Speed × 0.8
```

---

## 4. WORD COUNT (WC) QUICK REFERENCE

```
WC = (Width × BitsPerPixel) / 8

┌────────────┬───────┬──────────┐
│ Width      │ Format│ WC (bytes)│
├────────────┼───────┼──────────┤
│ 640        │ RAW10 │ 800      │
│ 1280       │ RAW10 │ 1600     │
│ 1920       │ RAW10 │ 2400     │
│ 1920       │ RAW12 │ 2880     │
│ 2560       │ RAW10 │ 3200     │
│ 3840       │ RAW10 │ 4800     │
│ 3840       │ RAW12 │ 5760     │
│ 4096       │ RAW10 │ 5120     │
│ 1920       │ YUV422│ 3840     │
│ 1920       │ RGB888│ 5760     │
└────────────┴───────┴──────────┘
```

---

## 5. COMMON SENSOR REGISTERS (Generic)

| Register (16-bit addr) | Name | Typical Value | Purpose |
|------------------------|------|---------------|---------|
| 0x0100 | mode_select | 0x00=standby, 0x01=stream | Start/stop streaming |
| 0x0103 | sw_reset | 0x01 | Software reset (self-clearing) |
| 0x0016 | chip_id_high | varies | Sensor identification |
| 0x0017 | chip_id_low | varies | Sensor identification |
| 0x0112 | csi_data_format_h | 0x0A (RAW10) | CSI-2 output data type |
| 0x0113 | csi_data_format_l | 0x0A | (pair with above) |
| 0x0114 | csi_lane_mode | 0x03 (4-lane) | Number of CSI-2 lanes - 1 |
| 0x0202 | coarse_integ_time | varies | Exposure (in line periods) |
| 0x0204 | analog_gain | varies | Analog gain code |
| 0x0340 | frame_length_lines | varies | VTS (controls frame rate) |
| 0x0342 | line_length_pck | varies | HTS (line time in pixels) |
| 0x034C | x_output_size | width | Output width |
| 0x034E | y_output_size | height | Output height |
| 0x0381 | x_odd_inc | 0x01 | Binning/skip (1=full) |
| 0x0383 | x_even_inc | 0x01 | Binning/skip |

---

## 6. SA8295P CSI HARDWARE QUICK REF

```
CSIPHY ports:       7 (CSIPHY0 – CSIPHY6)
Max lanes/port:     4 (D-PHY)
Max speed/lane:     2.5 Gbps
Max simultaneous:   16 cameras (via SerDes VC aggregation)
ISP (IFE) count:    4
Max resolution:     200 MP (single shot)
PHY type:           D-PHY v2.1 (C-PHY v2.0 on some ports)

CSID instances:     7 (one per CSIPHY)
BPS instances:      2
IPE instances:      2

Typical port assignment:
  Port 0-1: 4-lane (high-bandwidth cameras)
  Port 2-5: 2-lane (medium cameras)
  Port 6:   2-lane (spare/DMS)
```

---

## 7. DEVICE TREE SNIPPET (Qualcomm Camera)

```dts
/* Sensor node */
imx577: sensor@1a {
    compatible = "sony,imx577";
    reg = <0x1a>;           /* I2C address */
    
    clocks = <&camcc CAM_CC_MCLK0>;
    clock-frequency = <24000000>;    /* 24 MHz MCLK */
    
    /* Power supplies */
    avdd-supply = <&pm8350_l7>;      /* 2.8V analog */
    dovdd-supply = <&pm8350_l6>;     /* 1.8V I/O */
    dvdd-supply = <&pm8350_l2>;      /* 1.05V digital */
    
    reset-gpios = <&tlmm 100 GPIO_ACTIVE_LOW>;
    
    port {
        imx577_out: endpoint {
            remote-endpoint = <&csiphy0_in>;
            data-lanes = <1 2 3 4>;         /* 4-lane */
            clock-lanes = <0>;
            link-frequencies = /bits/ 64 <600000000>; /* 1.2 Gbps DDR */
        };
    };
};

/* CSIPHY node */
csiphy0: csiphy@ac65000 {
    port {
        csiphy0_in: endpoint {
            remote-endpoint = <&imx577_out>;
            data-lanes = <1 2 3 4>;
            clock-lanes = <0>;
            /* lane-polarities = <0 0 0 0 0>; for P/N swap */
        };
    };
};
```

---

## 8. COMMON LINUX COMMANDS

```bash
# List media devices
ls /dev/media* /dev/video* /dev/v4l-subdev*

# Show media pipeline topology
media-ctl -d /dev/media0 --print-topology

# Configure pipeline link (enable sensor → CSIPHY → CSID → IFE)
media-ctl -d /dev/media0 -l "'imx577 0-001a':0 -> 'msm_csiphy0':0[1]"
media-ctl -d /dev/media0 -l "'msm_csiphy0':1 -> 'msm_csid0':0[1]"
media-ctl -d /dev/media0 -l "'msm_csid0':1 -> 'msm_vfe0_rdi0':0[1]"

# Set format on sensor subdev
media-ctl -d /dev/media0 --set-v4l2 "'imx577 0-001a':0[fmt:SRGGB10_1X10/1920x1080]"

# Set format on video device
v4l2-ctl -d /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=RG10

# Capture single frame to file
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1 --stream-to=frame.raw

# Capture continuous (10 frames)
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=10 --stream-to=capture.raw

# Check supported formats
v4l2-ctl -d /dev/video0 --list-formats-ext

# Read sensor register (via I2C)
i2ctransfer -f -y 2 w2@0x1a 0x01 0x00 r1    # Read reg 0x0100

# Write sensor register
i2ctransfer -f -y 2 w3@0x1a 0x01 0x00 0x01   # Write 0x01 to reg 0x0100

# Check CSI errors in kernel log
dmesg | grep -i "csi\|csid\|csiphy\|cam_err\|overflow"

# Check camera HAL (Android)
adb shell dumpsys media.camera

# Camera trace (Android)
adb shell atrace --async_start -c camera hal
```

---

## 9. PIXEL FORMAT FOURCC CODES

| FourCC | V4L2 Define | Description |
|--------|-------------|-------------|
| RG10 | V4L2_PIX_FMT_SRGGB10 | 10-bit Bayer RGGB |
| RG12 | V4L2_PIX_FMT_SRGGB12 | 12-bit Bayer RGGB |
| BA10 | V4L2_PIX_FMT_SBGGR10 | 10-bit Bayer BGGR |
| GB10 | V4L2_PIX_FMT_SGBRG10 | 10-bit Bayer GBRG |
| GR10 | V4L2_PIX_FMT_SGRBG10 | 10-bit Bayer GRBG |
| YUYV | V4L2_PIX_FMT_YUYV | YUV 4:2:2 packed |
| NV12 | V4L2_PIX_FMT_NV12 | YUV 4:2:0 semi-planar |
| NV21 | V4L2_PIX_FMT_NV21 | YVU 4:2:0 semi-planar |
| RG10P | V4L2_PIX_FMT_SRGGB10P | 10-bit Bayer packed (CSI-2 native) |
| RG12P | V4L2_PIX_FMT_SRGGB12P | 12-bit Bayer packed |

---

## 10. SERDES QUICK REFERENCE

| Chip | Role | Vendor | Inputs | Outputs | Max Speed | Cable |
|------|------|--------|--------|---------|-----------|-------|
| MAX96717 | Serializer | ADI | 1× CSI-2 (4L) | 1× GMSL2 | 6 Gbps | Coax 15m |
| MAX96712 | Deserializer (4:1) | ADI | 4× GMSL2 | 1× CSI-2 (4L) | 6 Gbps/link | — |
| MAX96714 | Deserializer (1:1) | ADI | 1× GMSL2 | 1× CSI-2 (4L) | 6 Gbps | — |
| MAX9295A | Serializer | ADI | 1× CSI-2 (4L) | 1× GMSL2 | 6 Gbps | Coax 15m |
| MAX9296A | Deserializer (2:1) | ADI | 2× GMSL2 | 2× CSI-2 (4L) | 6 Gbps/link | — |
| DS90UB953 | Serializer | TI | 1× CSI-2 (4L) | 1× FPD-Link III | 4.16 Gbps | STP 15m |
| DS90UB960 | Deserializer (4:1) | TI | 4× FPD-Link | 2× CSI-2 (4L) | 4.16 Gbps/link | — |
| DS90UB971 | Serializer | TI | 1× CSI-2 (4L) | 1× FPD-Link IV | 12.8 Gbps | STP |

---

## 11. ERROR CODES & MEANINGS

| Error | CSID IRQ Bit | Meaning | Action |
|-------|-------------|---------|--------|
| ECC 1-bit | BIT(0) | Header single-bit error (corrected) | Log, monitor rate |
| ECC 2-bit | BIT(1) | Header multi-bit error (uncorrectable) | Discard packet |
| CRC mismatch | BIT(2) | Payload data corrupted | Mark line bad |
| FIFO overflow | BIT(3) | Data arriving faster than consumed | Reduce rate or increase clock |
| Frame drop | BIT(4) | Incomplete frame (missing FE) | Re-sync on next FS |
| SoT error | CSIPHY | Can't find sync byte (0xB8) | Check T_HS_SETTLE |
| Lane sync fail | CSIPHY | Lanes not aligned | Check lane count/polarity |
| PHY timeout | CSIPHY | No HS activity after init | Sensor not streaming |

---

## 12. POWER SEQUENCE TEMPLATE

```
Power-ON order:
  1. AVDD (Analog)    → 2.8V    → wait 1ms
  2. DOVDD (I/O)     → 1.8V    → wait 1ms
  3. DVDD (Digital)  → 1.05V   → wait 1ms
  4. RESET_N         → HIGH    → wait 1ms
  5. MCLK            → 24MHz   → wait 10ms (PLL lock)
  6. I2C accessible  → read chip ID to verify
  7. Load init regs  → 100-500 writes
  8. mode_select=1   → streaming starts

Power-OFF order (REVERSE):
  1. mode_select=0   → stop streaming
  2. MCLK off
  3. RESET_N LOW
  4. DVDD off
  5. DOVDD off
  6. AVDD off

Timing constraints:
  • Each supply ramp: < 1ms (fast regulator)
  • Total power-on to first frame: ~50-100ms
  • Fast wakeup (standby): Skip step 1-5, just write mode_select → 33ms
```

---

## 13. FRAME RATE CALCULATION

```
Frame Rate = Pixel Clock / (HTS × VTS)

Where:
  Pixel Clock = MCLK × PLL_multiplier / PLL_divider
  HTS = Line Length (pixels) = active_width + H_blanking
  VTS = Frame Length (lines) = active_height + V_blanking

Example (IMX577, 1920×1080 @ 30fps):
  Pixel Clock = 24MHz × 100 / 4 = 600 MHz
  HTS = 1920 + 1180 = 3100 pixels (line_length_pck register)
  VTS = 1080 + 68 = 1148 lines (frame_length_lines register)
  FPS = 600,000,000 / (3100 × 1148) = 168.6 fps (max)
  
  For 30 fps: Set VTS = 600,000,000 / (3100 × 30) = 6451 lines
    (increase V_blanking to slow down frame rate)

Exposure limit: coarse_integ_time ≤ VTS - margin (typically VTS-4)
```

---

## 14. QUICK DIAGNOSTIC FLOWCHART

```
No image output?
├─ Check sensor streaming → i2c read mode_select (0x0100)
│  └─ Not 0x01? → Write 0x01, check power sequence
├─ Check CSIPHY lock → sysfs status or dmesg
│  └─ Not locked? → Check T_HS_SETTLE, lane count, polarity
├─ Check CSID receiving → frame counter incrementing?
│  └─ Not incrementing? → PHY issue or sensor not actually streaming
├─ Check IFE processing → /dev/video output
│  └─ No output? → media-ctl pipeline links correct?
└─ Check format match → sensor output format == IFE input format?
   └─ Mismatch? → Fix DT/resolution in media-ctl

Wrong colors?
├─ Check Bayer pattern → RGGB vs BGGR vs GRBG vs GBRG
├─ Check lane swap → lane-polarities in device tree
└─ Check data type → RAW10 mismatch (packed vs unpacked)

Frame drops?
├─ Check DDR bandwidth → bandwidth profiler
├─ Check CSID overflow → dmesg for overflow IRQ
├─ Check thermal → cat thermal_zone temp
└─ Check IFE clock → sufficient for resolution × FPS
```

---

END OF DOCUMENT 06 — CHEATSHEET
