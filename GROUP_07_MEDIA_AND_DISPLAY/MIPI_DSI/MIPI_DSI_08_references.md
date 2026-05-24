# MIPI DSI — REFERENCES & RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI DSI | Document: 08 of 08
# Format: Curated links, paths, specs, study plan
# ════════════════════════════════════════════════════════════════════

---

## 1. MIPI ALLIANCE SPECIFICATIONS

| Spec | Version | Coverage | Access |
|------|---------|----------|--------|
| MIPI DSI | v1.3.2 | Display Serial Interface (core protocol) | MIPI member portal |
| MIPI DSI-2 | v2.1 | Next-gen: C-PHY support, higher bandwidth | MIPI member portal |
| MIPI DCS | v1.4 | Display Command Set (standard commands) | MIPI member portal |
| MIPI D-PHY | v2.5 | Physical layer (shared with CSI-2) | MIPI member portal |
| MIPI C-PHY | v2.0 | 3-wire PHY (alternative to D-PHY) | MIPI member portal |
| VESA DSC | v1.2a | Display Stream Compression | VESA member portal |

### Key Spec Sections
```
DSI v1.3.2 must-read sections:
  Chapter 3:  Architecture and Protocol Overview
  Chapter 4:  D-PHY Data Lane States (LP, HS, Escape)
  Chapter 5:  Packet Structure (Short/Long format)
  Chapter 6:  Data Types (complete table)
  Chapter 8:  Video Mode Timing
  Chapter 9:  Command Mode Operation
  Chapter 10: BTA (Bus Turn-Around)
  Chapter 11: Error Reporting
  Appendix A: Timing Parameters

DCS v1.4 must-read:
  Chapter 3:  Command Definitions (all DCS codes)
  Chapter 4:  Pixel Format
  Chapter 5:  Power Mode States
```

---

## 2. QUALCOMM SA8295P / SA8155P DOCUMENTATION

| Document | Description | Location |
|----------|-------------|----------|
| SA8295P TRM | Technical Reference Manual | Qualcomm Createpoint (NDA) |
| Display LLD | Low-Level Design for DPU/DSI | Qualcomm Createpoint |
| DSI PHY Spec | PHY programming guide | Qualcomm Createpoint |
| DPU Architecture | Display Processing Unit | Qualcomm Createpoint |
| DSC Integration | Compression configuration | Qualcomm Createpoint |
| Boot Display Guide | Continuous splash implementation | Qualcomm Createpoint |
| BSP Release Notes | Known display issues/fixes | Qualcomm Createpoint |

### Qualcomm Display Architecture Docs
```
Key documents to request from Qualcomm:
  80-PD458-1: Display DSI Programming Guide
  80-PD458-3: DPU Hardware Programming Guide
  80-PD458-5: Display Debug Guide
  80-PF789-1: Continuous Splash Boot Display
  80-PG012-1: Multi-Display Configuration Guide
  80-PH234-1: DSC Encoder Programming
```

---

## 3. LINUX KERNEL SOURCE PATHS

### DRM/KMS Core Framework
```
drivers/gpu/drm/
├── drm_mipi_dsi.c              ← MIPI DSI host framework
├── drm_panel.c                 ← Panel abstraction
├── drm_atomic.c                ← Atomic modesetting
├── drm_crtc.c                  ← CRTC management
├── drm_connector.c             ← Connector framework
├── drm_modes.c                 ← Display mode handling
└── drm_fb_helper.c             ← Framebuffer compatibility
```

### Qualcomm MSM Display Drivers
```
drivers/gpu/drm/msm/
├── dsi/
│   ├── dsi.c                   ← DSI core driver
│   ├── dsi_host.c              ← DSI host implementation
│   ├── dsi_manager.c           ← Multi-DSI management
│   ├── dsi_phy.c               ← PHY common code
│   ├── dsi_phy_14nm.c          ← PHY for older SoCs
│   ├── dsi_phy_10nm.c          ← PHY for SM8x50
│   ├── dsi_phy_7nm.c           ← PHY for SM8x50+
│   ├── dsi_cfg.c               ← DSI HW configuration
│   └── dsi.xml.h               ← Register definitions
├── disp/
│   └── dpu1/
│       ├── dpu_crtc.c          ← DPU CRTC implementation
│       ├── dpu_encoder.c       ← DPU encoder (DSI/DP)
│       ├── dpu_plane.c         ← DPU plane (SSPP)
│       ├── dpu_hw_catalog.c    ← HW capabilities table
│       ├── dpu_hw_intf.c       ← Interface (timing gen)
│       ├── dpu_hw_dsc.c        ← DSC encoder HW
│       ├── dpu_hw_lm.c         ← Layer mixer HW
│       ├── dpu_hw_sspp.c       ← Source pipe HW
│       └── dpu_kms.c           ← KMS entry point
├── msm_drv.c                   ← MSM DRM driver core
└── msm_gem.c                   ← GEM memory management
```

### Panel Drivers
```
drivers/gpu/drm/panel/
├── panel-simple.c              ← Generic simple panel driver
├── panel-samsung-s6e3ha2.c     ← Samsung OLED example
├── panel-novatek-nt36672a.c    ← Novatek LCD example
├── panel-boe-tv101wum-nl6.c    ← BOE automotive example
└── panel-lvds.c                ← LVDS comparison reference

# Qualcomm-specific panel path (vendor kernel):
techpack/display/msm/dsi/
├── dsi_panel.c                 ← Panel management
├── dsi_display.c               ← Display top-level
├── dsi_ctrl.c                  ← DSI controller
├── dsi_phy.c                   ← DSI PHY
└── dsi_drm.c                   ← DRM bridge
```

### Device Tree Bindings
```
Documentation/devicetree/bindings/display/
├── mipi-dsi-bus.yaml           ← DSI bus binding
├── panel/
│   ├── panel-simple.yaml       ← Generic panel
│   └── panel-simple-dsi.yaml   ← Generic DSI panel
└── msm/
    ├── dsi-controller.yaml     ← Qualcomm DSI ctrl
    ├── dsi-phy.yaml            ← Qualcomm DSI PHY
    └── dpu.yaml                ← Display Processing Unit

# Qualcomm vendor DT (typical paths):
arch/arm64/boot/dts/qcom/
├── sa8295p.dtsi                ← SoC base (DPU, DSI definitions)
├── sa8295p-display.dtsi        ← Display overlay
└── sa8295p-panel-xxxx.dtsi     ← Panel-specific config
```

---

## 4. ANDROID DISPLAY PATHS

### HAL & Framework
```
# Hardware Abstraction Layer
hardware/qcom/display/
├── composer/                    ← HWComposer HAL implementation
│   ├── hwc_session.cpp         ← Session management
│   ├── hwc_display.cpp         ← Per-display logic
│   ├── hwc_layers.cpp          ← Layer management
│   └── hwc_display_builtin.cpp ← Built-in display
├── sdm/
│   ├── libs/core/              ← SDM core library
│   │   ├── display_builtin.cpp ← Display management
│   │   ├── comp_manager.cpp    ← Composition strategy
│   │   └── hw_info.cpp         ← Hardware capabilities
│   └── libs/hwc2/              ← HWC2 implementation
└── gralloc/                    ← Buffer allocation

# Framework
frameworks/native/services/surfaceflinger/
├── SurfaceFlinger.cpp          ← Main compositor
├── DisplayDevice.cpp           ← Per-display state
├── Layer.cpp                   ← Layer (surface) management
├── Scheduler/                  ← VSYNC & frame scheduling
└── CompositionEngine/          ← Composition pipeline
```

### AAOS (Android Automotive) Display
```
# Multi-display for automotive
packages/services/Car/
├── car-lib/src/.../display/     ← Car display APIs
└── service/src/.../display/     ← CarDisplayService

# Display configuration
device/qcom/sa8295p/
├── display/                     ← Display config files
├── overlay/                     ← Resource overlays
└── init.sa8295p.rc             ← Display init sequence
```

---

## 5. TOOLS REFERENCE

### Hardware Tools

| Tool | Purpose | Use Case |
|------|---------|----------|
| Oscilloscope (4CH+) | Signal timing verification | PHY debug, reset pulse |
| Differential probe | D-PHY HS measurement | Eye diagram, bit clock |
| Protocol analyzer (Agilent/Tektronix) | DSI packet decode | Protocol-level debug |
| Logic analyzer (Saleae) | LP mode capture | Escape, BTA sequences |
| USB-DSI bridge (MIPI DisPHY) | DSI signal generation | Panel testing without SoC |
| Multimeter | Power rail verification | VCI/VDDI levels |
| Thermal camera | Temperature monitoring | Auto display thermal debug |

### Software Tools

| Tool | Command/Path | Purpose |
|------|-------------|---------|
| modetest | `modetest -M msm` | DRM/KMS testing |
| drm_info | `drm_info` | DRM device information |
| edid-decode | `edid-decode /sys/class/drm/*/edid` | EDID parsing |
| dmesg | `dmesg \| grep -i dsi` | Kernel log filtering |
| devmem2 | `devmem2 0xAE94000` | Direct register read |
| i2ctool | `i2cdetect -y 1` | I2C bus scan (if panel uses I2C) |
| ftrace | Display function tracing | Latency analysis |
| perf | `perf record -g` | Performance profiling |

### Android Tools

| Tool | Command | Purpose |
|------|---------|---------|
| dumpsys | `dumpsys SurfaceFlinger` | Compositor state |
| dumpsys display | `dumpsys display` | Display config |
| wm | `wm size` / `wm density` | Window manager info |
| screencap | `screencap -p /sdcard/sc.png` | Screenshot |
| screenrecord | `screenrecord /sdcard/rec.mp4` | Screen recording |
| setprop | `setprop debug.sf.showfps 1` | FPS overlay |
| service call | `service call SurfaceFlinger 1013` | Debug commands |
| atrace | `atrace -t 5 gfx view` | Graphics trace |
| systrace | Web-based trace viewer | Frame timing analysis |
| perfetto | `perfetto -c config.pbtxt -o trace` | Modern tracing |

---

## 6. SERDES & BRIDGE ICs (Automotive)

### Common DSI SerDes Solutions

| Vendor | Serializer | Deserializer | Link | Max BW | Use |
|--------|-----------|-------------|------|--------|-----|
| Maxim/ADI | MAX96755 | MAX96752 | GMSL2 | 6 Gbps | Automotive display |
| Maxim/ADI | MAX96789 | MAX96772 | GMSL2 | 12 Gbps | High-res display |
| TI | DS90UB947 | DS90UB948 | FPD-Link III | 3.36 Gbps | Display |
| TI | DS90UB941 | DS90UB948 | FPD-Link III | 2.78 Gbps | Legacy display |
| Rohm | BU18TL82 | BU18RL82 | V-by-One HS | 8 Gbps | Premium display |
| THine | THCV241 | THCV242 | V-by-One HS | 4 Gbps | IVI display |

### Typical Automotive Display Chain
```
[SA8295P SoC]                         [Panel Assembly]
 DPU → DSI Controller → D-PHY         TCON → LCD/OLED
      |                                  |
      ↓                                  ↑
 [GMSL Serializer]                [GMSL Deserializer]
  MAX96755/96789                   MAX96752/96772
      |                                  |
      └──── Coax Cable (up to 15m) ──────┘
           (Single GMSL2 link)
```

### Key Datasheets
```
Maxim/Analog Devices:
  MAX96755F: Automotive GMSL2 Serializer (DSI input)
  MAX96752: GMSL2 Deserializer (OLDI/LVDS output to TCON)
  MAX96789: Dual GMSL2 Serializer (DSI/DP input)
  MAX96772: Dual GMSL2 Deserializer

TI:
  DS90UB947-Q1: FPD-Link III DSI Serializer
  DS90UB948-Q1: FPD-Link III Deserializer
  DS90UB941AS-Q1: Dual FPD-Link III Serializer
```

---

## 7. PANEL IC DATASHEETS

| Panel IC | Vendor | Type | Typical Application |
|----------|--------|------|-------------------|
| TD4330 | Synaptics | LCD TDDI | Automotive IVI |
| NT36672A | Novatek | LCD | Smartphone, automotive |
| S6E3HAB | Samsung | OLED | Premium smartphone |
| HX8399 | Himax | LCD | Tablet, automotive |
| RM692C9 | Raydium | OLED | Smartphone |
| ILI9881C | ILI Technology | LCD | Budget panels |
| ST7701S | Sitronix | LCD | Small/medium displays |
| EK79007 | Fitipower | LCD | Automotive cluster |
| SSD2828 | Solomon Systech | DSI-to-RGB Bridge | Legacy panel adapt |

---

## 8. KERNEL CONFIGURATION

### Required Kconfig Options
```kconfig
# DRM/KMS Core
CONFIG_DRM=y
CONFIG_DRM_KMS_HELPER=y
CONFIG_DRM_MIPI_DSI=y
CONFIG_DRM_PANEL=y

# Qualcomm MSM Display
CONFIG_DRM_MSM=y
CONFIG_DRM_MSM_DSI=y
CONFIG_DRM_MSM_DSI_PLL=y
CONFIG_DRM_MSM_DPU=y

# Panel driver
CONFIG_DRM_PANEL_SIMPLE=y
# Or specific panel:
CONFIG_DRM_PANEL_BOE_TV101WUM=y

# Backlight
CONFIG_BACKLIGHT_CLASS_DEVICE=y
CONFIG_BACKLIGHT_PWM=y
CONFIG_BACKLIGHT_QCOM_WLED=y

# DSC compression
CONFIG_DRM_MSM_DSC=y

# Debug
CONFIG_DRM_MSM_DSI_STAGING=y      # Extra debug nodes
CONFIG_DEBUG_FS=y                   # debugfs support
CONFIG_DRM_FBDEV_EMULATION=y       # fbdev compatibility
```

### Optional Debug Config
```kconfig
# Extra display debug
CONFIG_DRM_MSM_REGISTER_LOGGING=y  # Register dump on error
CONFIG_DYNAMIC_DEBUG=y              # Dynamic debug messages
CONFIG_DRM_DEBUG_DP_MST_TOPOLOGY_REFS=y  # DP topology debug

# Enable at runtime:
echo "file drivers/gpu/drm/msm/dsi/* +p" > /sys/kernel/debug/dynamic_debug/control
echo "file drivers/gpu/drm/msm/disp/dpu1/* +p" > /sys/kernel/debug/dynamic_debug/control
```

---

## 9. STUDY PLAN (4-Week Display Engineer Track)

### Week 1: DSI Protocol Fundamentals
```
Day 1-2: Theory
  □ Read DSI spec Chapter 3-5 (Architecture, PHY, Packets)
  □ Study DCS v1.4 Chapter 3 (Command definitions)
  □ Review this encyclopedia: 01_master_theory.md

Day 3-4: Packet Understanding
  □ Manually decode 5 DSI packets (short write, long write, pixel)
  □ Calculate bandwidth for 3 resolutions
  □ Review: 04_flashcards.md Deck 3

Day 5: Video Mode Mastery
  □ Calculate timing for automotive panel (1920×720)
  □ Understand burst vs non-burst trade-offs
  □ Draw timing diagram from scratch
```

### Week 2: Linux DRM/KMS Display Stack
```
Day 1-2: Framework Study
  □ Read DRM documentation (kernel docs)
  □ Trace code: drm_mipi_dsi.c → dsi_host.c → dsi_phy.c
  □ Understand: CRTC → Encoder → Connector → Panel lifecycle

Day 3-4: Panel Driver Development
  □ Write simple panel driver (follow panel-simple.c template)
  □ Compile and test with modetest
  □ Debug: insert intentional error, diagnose

Day 5: Device Tree
  □ Write complete DSI panel DT from datasheet
  □ Understand clock tree: PLL → bit_clk → byte_clk → pixel_clk
  □ Practice: 07_labs_debugging.md Lab 1
```

### Week 3: Qualcomm DPU + Automotive
```
Day 1-2: DPU Architecture
  □ Study DPU pipeline: SSPP → LM → DSPP → INTF → DSI
  □ Read dpu_hw_catalog.c for SA8295P capabilities
  □ Understand plane assignment and layer mixing

Day 3-4: Multi-Display
  □ Configure 3-display system (Lab 2)
  □ Implement content isolation
  □ Test DSC for high-resolution IVI display

Day 5: Boot Display
  □ Implement continuous splash (Lab 3)
  □ Measure boot-to-display time
  □ Optimize for <1 second target
```

### Week 4: Debug Mastery & Production
```
Day 1-2: Systematic Debugging
  □ Work through all 8 debug scenarios
  □ Build personal debug flowchart
  □ Practice scope-based DSI PHY debugging

Day 3-4: Performance & EMI
  □ Bandwidth budget calculation for full system
  □ EMI measurement and mitigation techniques
  □ SerDes configuration for long-cable display

Day 5: Integration & Review
  □ Full system bring-up from scratch (timed)
  □ Review all flashcards (target: 90% recall)
  □ Mock interview: Senior DSI questions
```

---

## 10. INTERVIEW PRIORITY TOPICS

### Must-Know (asked in 90% of interviews)
```
1. DSI Video Mode vs Command Mode (differences, when to use)
2. DCS commands (exit_sleep, display_on, pixel_format)
3. Bandwidth calculation (formula + one worked example)
4. Black screen debugging (systematic approach)
5. D-PHY LP/HS states and transitions
6. Packet structure (short vs long, ECC, CRC)
7. Panel power-on sequence (with delays)
8. DRM/KMS architecture (CRTC, Encoder, Connector, Plane)
```

### Should-Know (asked in 60% of interviews)
```
9. Continuous splash / fast boot display
10. Dual-DSI configuration
11. DSC compression (when and why)
12. BTA mechanism (how reads work)
13. Video mode timing (HSA, HBP, HFP, VSA, VBP, VFP)
14. ULPS and power states
15. Tearing Effect (TE) handling
16. SerDes for automotive (GMSL, FPD-Link)
```

### Nice-to-Know (asked in senior/staff interviews)
```
17. Multi-display content isolation (ASIL-B cluster)
18. EMI reduction techniques (SSC, scrambling)
19. E-mirror latency optimization
20. Panel IC internals (GRAM, TCON, gamma)
21. DSI-2 / C-PHY differences
22. DPU resource management (SSPP, LM assignment)
23. Display color management (DSPP, PCC, IGC)
24. Automotive display functional safety architecture
```

---

## 11. RELATED PROTOCOLS & COMPARISON

| Protocol | Use | Vs DSI |
|----------|-----|--------|
| MIPI CSI-2 | Camera input | Same PHY, opposite direction, no DCS |
| eDP | Laptop/embedded display | Higher BW, more complex, no DCS |
| LVDS | Legacy flat panel | Parallel, lower speed, no compression |
| HDMI | Consumer display | Video+Audio, longer cable, HDCP |
| DisplayPort | PC/workstation | MST, higher BW, no panel control |
| SPI (display) | Small panels (<2") | Very low BW, simple, no PHY |
| Parallel RGB | Legacy MCU displays | 24+ pins, short distance, no compression |
| DBI (6800/8080) | MCU displays | 8/16-bit parallel, with GRAM commands |

---

## 12. GLOSSARY QUICK REFERENCE

| Term | Definition |
|------|-----------|
| AOD | Always-On Display |
| ASIL | Automotive Safety Integrity Level |
| BTA | Bus Turn-Around |
| CABC | Content Adaptive Brightness Control |
| CRTC | CRT Controller (DRM timing generator) |
| DCS | Display Command Set |
| DDR | Double Data Rate |
| DPU | Display Processing Unit |
| DRM | Direct Rendering Manager |
| DSC | Display Stream Compression |
| DSI | Display Serial Interface |
| DSPP | Display Post-Processing |
| ECC | Error-Correcting Code |
| EoTp | End of Transmission Packet |
| FPC | Flexible Printed Circuit |
| GMSL | Gigabit Multimedia Serial Link |
| GRAM | Graphics RAM |
| HBP | Horizontal Back Porch |
| HFP | Horizontal Front Porch |
| HS | High Speed (mode) |
| HSA | Horizontal Sync Active |
| HWC | Hardware Composer |
| INTF | Interface (DPU timing output) |
| KMS | Kernel Mode Setting |
| LM | Layer Mixer |
| LP | Low Power (mode) |
| MDSS | Mobile Display SubSystem |
| OLED | Organic Light Emitting Diode |
| PLL | Phase-Locked Loop |
| PWM | Pulse Width Modulation |
| SerDes | Serializer/Deserializer |
| SF | SurfaceFlinger |
| SSC | Spread-Spectrum Clocking |
| SSPP | Source Surface Pipe |
| TCON | Timing Controller |
| TDDI | Touch+Display Driver Integration |
| TE | Tearing Effect |
| ULPS | Ultra-Low Power State |
| VBP | Vertical Back Porch |
| VFP | Vertical Front Porch |
| VSA | Vertical Sync Active |
| WLED | White LED (backlight driver) |

---

END OF DOCUMENT 08 — REFERENCES & RESOURCES
═══════════════════════════════════════════════
MIPI DSI ENCYCLOPEDIA COMPLETE (8/8 documents)
