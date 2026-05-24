# MIPI CSI-2 — REFERENCES & STUDY GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: MIPI CSI-2 | Document: 08 of 08
# Format: Specifications, kernel paths, tools, study plan
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL SPECIFICATIONS

| Document | Version | Access | Notes |
|----------|---------|--------|-------|
| MIPI CSI-2 Specification | v4.0 (2023) | MIPI Alliance members only | Full protocol spec |
| MIPI D-PHY Specification | v2.5 (2020) | MIPI Alliance members only | Physical layer |
| MIPI C-PHY Specification | v2.0 (2019) | MIPI Alliance members only | Alternative PHY |
| MIPI CCI Specification | v1.1 | MIPI Alliance members only | I2C camera control |
| D-PHY Compliance Test Spec | v1.2 | MIPI Alliance | PHY testing procedures |

**Access**: MIPI specs require membership. Alternative: Use SoC/sensor datasheets which contain sufficient implementation details.

---

## 2. QUALCOMM DOCUMENTATION

| Document | Location | Content |
|----------|----------|---------|
| Camera Architecture Overview | Qualcomm CRM (internal) | ISP pipeline, IFE/BPS/IPE blocks |
| CSIPHY Programming Guide | Qualcomm CRM | PHY register map, settle time config |
| CSID Programming Guide | Qualcomm CRM | Packet parser, VC demux, error handling |
| CamX/Chi Developer Guide | vendor/qcom/proprietary/camx/doc/ | HAL framework, node architecture |
| Camera Sensor Driver Guide | vendor/qcom/proprietary/camx/doc/ | Sensor bringup, I2C config |
| SA8295P TRM | Qualcomm CRM | Full SoC technical reference |
| Camera Device Tree Bindings | Documentation/devicetree/bindings/media/ | DT node format for camera |

---

## 3. LINUX KERNEL SOURCE PATHS

```
Camera subsystem (Qualcomm CAMSS):
  drivers/media/platform/qcom/camss/
  ├── camss.c                    # Platform driver, resource management
  ├── camss-csiphy.c             # CSIPHY driver (D-PHY receiver)
  ├── camss-csid.c               # CSID driver (packet parser)
  ├── camss-vfe.c                # VFE/IFE driver (ISP front-end)
  ├── camss-ispif.c              # ISPIF (legacy, pre-SDM845)
  └── camss-video.c              # V4L2 video device

Sensor drivers:
  drivers/media/i2c/
  ├── imx219.c                   # Sony IMX219 (Raspberry Pi)
  ├── imx290.c                   # Sony IMX290 (low-light)
  ├── imx477.c                   # Sony IMX477
  ├── ov5640.c                   # OmniVision OV5640
  ├── ov5670.c                   # OmniVision OV5670
  ├── ar0521.c                   # ON Semi AR0521
  └── max9271.c                  # GMSL serializer driver

V4L2 framework:
  drivers/media/v4l2-core/
  ├── v4l2-dev.c                 # Video device registration
  ├── v4l2-ioctl.c               # ioctl handling
  ├── v4l2-subdev.c              # Subdevice framework
  ├── v4l2-fwnode.c              # Firmware/DT parsing
  └── videobuf2-core.c           # Buffer management

Media Controller:
  drivers/media/mc/
  ├── mc-entity.c                # Entity/pad/link management
  └── mc-devnode.c               # /dev/media device

Device Tree bindings:
  Documentation/devicetree/bindings/media/
  ├── qcom,camss.yaml            # Qualcomm camera subsystem
  ├── i2c/sony,imx219.yaml       # Sensor bindings
  └── video-interfaces.yaml      # Standard endpoint properties

Camera headers:
  include/media/
  ├── v4l2-subdev.h              # Subdev ops
  ├── v4l2-mediabus.h            # Media bus formats (SRGGB10_1X10, etc.)
  ├── v4l2-fwnode.h              # Firmware node parsing
  └── media-entity.h             # Entity/pad definitions
  
  include/uapi/linux/
  ├── videodev2.h                # V4L2 userspace API
  ├── media.h                    # Media controller userspace API
  └── v4l2-subdev.h              # Subdev userspace API
```

---

## 4. ANDROID CAMERA SOURCE PATHS

```
Camera Framework:
  frameworks/av/camera/                    # Camera client library
  frameworks/av/services/camera/           # CameraService
  frameworks/base/core/java/android/hardware/camera2/  # Camera2 API

Camera HAL:
  hardware/interfaces/camera/              # AIDL/HIDL interfaces
  hardware/interfaces/camera/provider/     # Camera provider HAL
  hardware/interfaces/camera/device/       # Camera device HAL

Qualcomm Camera HAL (vendor):
  vendor/qcom/proprietary/camx/            # CamX framework
  ├── src/core/                            # Pipeline, session, node base
  ├── src/hwl/                             # Hardware layer (IFE, BPS, IPE)
  ├── src/swl/                             # Software layer (JPEG, stats)
  └── src/sensor/                          # Sensor module, actuator

  vendor/qcom/proprietary/chi-cdk/         # Chi Customer Dev Kit
  ├── core/                                # Chi framework
  ├── topology/                            # Pipeline topology XML
  └── sensor/                              # Sensor drivers (.bin configs)

Kernel (Android):
  kernel/msm-5.15/drivers/media/platform/msm/  # Qualcomm camera
  kernel/msm-5.15/techpack/camera/             # Camera techpack

Camera Configuration:
  vendor/qcom/proprietary/chi-cdk/sensor/
  ├── imx577_sensor.xml                    # Sensor config
  ├── imx577_chromatix.xml                 # ISP tuning
  └── module_config.xml                    # Module (sensor + lens + actuator)
```

---

## 5. SERDES DOCUMENTATION

| Document | Source | Content |
|----------|--------|---------|
| MAX96712 Datasheet | Analog Devices | 4:1 deserializer, register map |
| MAX96717 Datasheet | Analog Devices | Serializer, CSI-2 input |
| GMSL2 Design Guide | ADI (AN-2306) | PCB layout, impedance, routing |
| DS90UB960 Datasheet | Texas Instruments | FPD-Link III 4:1 deser |
| DS90UB953 Datasheet | Texas Instruments | FPD-Link III serializer |
| GMSL2 Linux Driver | kernel: drivers/media/i2c/max9286.c | Open-source reference |
| MAX96712 Evaluation Kit | ADI website | Hardware eval + software |

---

## 6. SENSOR DATASHEETS (Common Automotive)

| Sensor | Vendor | Resolution | Interface | HDR | Application |
|--------|--------|-----------|-----------|-----|-------------|
| IMX490 | Sony | 5.4 MP | 4L CSI-2 | DOL 120dB | ADAS (front) |
| IMX728 | Sony | 8.3 MP | 4L CSI-2 | DOL 120dB | ADAS (front) |
| IMX390 | Sony | 2.45 MP | 4L CSI-2 | DOL 120dB | Surround view |
| IMX623 | Sony | 5.4 MP | 4L CSI-2 | DOL | DMS (near-IR) |
| AR0820 | ON Semi | 8.3 MP | 4L CSI-2 | Multi-exp | ADAS |
| AR0234 | ON Semi | 2.3 MP | 4L CSI-2 | LED flicker | Surround |
| OX03F10 | OmniVision | 3.0 MP | 4L CSI-2 | Staggered HDR | Surround |
| OX08B40 | OmniVision | 8.3 MP | 4L CSI-2 | Staggered HDR | ADAS |

---

## 7. TOOLS

### Hardware Tools
| Tool | Purpose | Vendor |
|------|---------|--------|
| D-PHY Protocol Analyzer | Capture/decode CSI-2 packets | Teledyne LeCroy |
| Oscilloscope (≥20 GHz) | Eye diagram, timing | Keysight, Tektronix |
| MIPI D-PHY Exerciser | Generate CSI-2 test patterns | Agilent/Keysight |
| GMSL2 Evaluation Board | SerDes prototyping | Analog Devices |
| Logic Analyzer | I2C/CCI debug | Saleae |

### Software Tools
| Tool | Purpose | Command |
|------|---------|---------|
| v4l2-ctl | V4L2 capture, format config | `v4l2-ctl -d /dev/video0 --stream-mmap` |
| media-ctl | Pipeline topology, links | `media-ctl -d /dev/media0 -p` |
| v4l2-compliance | V4L2 driver compliance test | `v4l2-compliance -d /dev/video0` |
| i2ctransfer | Direct I2C register access | `i2ctransfer -y 2 w2@0x1a 0x01 0x00 r1` |
| i2cdetect | Scan I2C bus for devices | `i2cdetect -y 2` |
| devmem2 | Direct register read/write | `devmem2 0xAC65000 w` (CSIPHY base) |
| cam (libcamera) | Alternative capture tool | `cam -c 1 --capture=10` |
| yavta | Yet Another V4L2 Test App | `yavta -f SRGGB10 -s 1920x1080 /dev/video0` |
| raw2rgb (custom) | Convert RAW to viewable | Python script (see Lab 1) |
| ftrace | Kernel tracing | `trace-cmd record -e v4l2` |
| perf | Performance profiling | `perf stat -e cache-misses` |

### Android-Specific Tools
| Tool | Purpose | Command |
|------|---------|---------|
| dumpsys media.camera | Camera service state | `adb shell dumpsys media.camera` |
| logcat CamX | HAL debug logs | `adb logcat -s CamX Chi` |
| atrace camera | Systrace camera events | `adb shell atrace -c camera` |
| camxoverridesettings | Runtime HAL tunables | `/vendor/etc/camera/camxoverridesettings.txt` |
| setprop persist.camera | Camera properties | `adb shell setprop persist.camera.hal.debug 5` |

---

## 8. KEY KERNEL CONFIG OPTIONS

```kconfig
# Camera subsystem
CONFIG_VIDEO_V4L2=y
CONFIG_MEDIA_SUPPORT=y
CONFIG_MEDIA_CAMERA_SUPPORT=y
CONFIG_VIDEO_V4L2_SUBDEV_API=y
CONFIG_MEDIA_CONTROLLER=y

# Qualcomm camera
CONFIG_VIDEO_QCOM_CAMSS=y
CONFIG_VIDEO_QCOM_VENUS=y     # Video encoder (for recording)

# Sensor drivers
CONFIG_VIDEO_IMX219=m
CONFIG_VIDEO_IMX290=m
CONFIG_VIDEO_OV5640=m

# DMA buffer support
CONFIG_DMA_SHARED_BUFFER=y
CONFIG_ION=y                   # Or DMA-BUF heaps (newer)

# Debug
CONFIG_VIDEO_ADV_DEBUG=y       # Advanced debug ioctls
CONFIG_MEDIA_CONTROLLER_DVB=y  # Full topology info
```

---

## 9. STUDY PLAN

### Week 1: Foundation
- [ ] Read CSI-2 overview (understand protocol layers)
- [ ] Study D-PHY states and HS entry/exit sequence
- [ ] Understand packet structure (short vs long)
- [ ] Learn data types (RAW10/12, YUV, embedded)
- [ ] Practice bandwidth calculations

### Week 2: Linux/V4L2
- [ ] Study V4L2 framework architecture
- [ ] Read Media Controller documentation
- [ ] Practice with v4l2-ctl and media-ctl
- [ ] Understand buffer management (REQBUFS, QBUF, DQBUF)
- [ ] Read a sensor driver (e.g., imx219.c)

### Week 3: Qualcomm/Automotive
- [ ] Study CSIPHY/CSID/IFE pipeline
- [ ] Understand SerDes (GMSL2) architecture
- [ ] Learn Virtual Channel multiplexing
- [ ] Study multi-camera system design
- [ ] Understand HDR (DOL) capture

### Week 4: Debugging & Integration
- [ ] Practice error diagnosis (CRC, overflow, sync)
- [ ] Study power management sequence
- [ ] Learn camera HAL3 integration
- [ ] Practice capture latency measurement
- [ ] Design a multi-camera system (exercise)

### Advanced Topics (Ongoing)
- [ ] CSI-2 v4.0 features (scrambling, USL)
- [ ] C-PHY deep dive
- [ ] Signal integrity and EMC for automotive
- [ ] Functional safety (ASIL) considerations
- [ ] Multi-SoC camera sharing architectures

---

## 10. INTERVIEW PREPARATION PRIORITY

| Topic | Frequency | Difficulty | Priority |
|-------|-----------|-----------|----------|
| D-PHY basics (lanes, HS/LP) | Very High | Low | ★★★★★ |
| Packet structure (ECC/CRC) | High | Medium | ★★★★★ |
| Bandwidth calculation | Very High | Medium | ★★★★★ |
| Virtual Channels | High | Medium | ★★★★☆ |
| SerDes (GMSL2/FPD-Link) | High (automotive) | Medium | ★★★★☆ |
| ISP Pipeline (IFE/BPS/IPE) | Medium (Qualcomm) | High | ★★★★☆ |
| V4L2 buffer management | Medium | Medium | ★★★☆☆ |
| RAW pixel packing | Medium | Low | ★★★☆☆ |
| Multi-camera sync | Medium (automotive) | High | ★★★☆☆ |
| HDR/DOL | Medium | Medium | ★★★☆☆ |
| C-PHY | Low | Medium | ★★☆☆☆ |
| CSI-2 v4.0 features | Low | High | ★★☆☆☆ |
| Power management | Low | Medium | ★★☆☆☆ |
| Android HAL3 | Medium (Android) | High | ★★★☆☆ |

---

## 11. RELATED PROTOCOLS TO STUDY

| Protocol | Relationship to CSI-2 | Priority |
|----------|----------------------|----------|
| MIPI DSI | Display counterpart (SoC → Display) | High |
| I2C/CCI | Control channel for sensor configuration | High |
| GMSL2 | SerDes extension for automotive cameras | High (automotive) |
| FPD-Link III/IV | Alternative SerDes (TI) | Medium |
| V4L2 | Linux framework for camera capture | High |
| USB Video Class | Alternative camera interface (webcams) | Low |
| GigE Vision | Industrial camera interface | Low |
| Automotive Ethernet | Network camera (future, ADAS fusion) | Medium |
| PCIe | Camera attached via PCIe (data center) | Low |

---

## 12. GLOSSARY OF ACRONYMS

| Acronym | Full Form |
|---------|-----------|
| ADAS | Advanced Driver Assistance Systems |
| BPS | Bayer Processing Segment |
| CCI | Camera Control Interface |
| CDR | Clock Data Recovery |
| CRC | Cyclic Redundancy Check |
| CSI | Camera Serial Interface |
| CSID | CSI Decoder |
| CSIPHY | CSI Physical Layer |
| DDR | Double Data Rate |
| DMS | Driver Monitoring System |
| DOL | Digital Overlap (HDR) |
| DSI | Display Serial Interface |
| DT | Data Type |
| ECC | Error Correcting Code |
| EMC | Electromagnetic Compatibility |
| EMI | Electromagnetic Interference |
| EoT | End of Transmission |
| FPD-Link | Flat Panel Display Link |
| FS/FE | Frame Start / Frame End |
| GMSL | Gigabit Multimedia Serial Link |
| HAL | Hardware Abstraction Layer |
| HDR | High Dynamic Range |
| HS | High Speed |
| IFE | Image Front End |
| IPE | Image Processing Engine |
| ISP | Image Signal Processor |
| LFSR | Linear Feedback Shift Register |
| LP | Low Power |
| MFNR | Multi-Frame Noise Reduction |
| MIPI | Mobile Industry Processor Interface |
| NPU | Neural Processing Unit |
| PDAF | Phase Detect Auto Focus |
| PHY | Physical Layer |
| PoC | Power over Coax |
| SerDes | Serializer-Deserializer |
| SoC | System on Chip |
| SoT | Start of Transmission |
| UBWC | Universal Bandwidth Compression |
| UI | Unit Interval |
| USL | Unified Serial Link |
| V4L2 | Video for Linux 2 |
| VC | Virtual Channel |
| VFE | Video Front End |
| WC | Word Count |
| XVS | External Vertical Sync |

---

END OF DOCUMENT 08 — REFERENCES & STUDY GUIDE

═══════════════════════════════════════════════════════
MIPI CSI-2 ENCYCLOPEDIA COMPLETE (8/8 Documents)
═══════════════════════════════════════════════════════
