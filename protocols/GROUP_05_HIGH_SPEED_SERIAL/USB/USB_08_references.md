# USB PROTOCOL — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 08 of 08
# Format: Specifications, source code paths, books, study schedule
# ════════════════════════════════════════════════════════════════════

---

## 1. OFFICIAL USB SPECIFICATIONS

| Specification | Version | Document | Source |
|---|---|---|---|
| USB 2.0 | Rev 2.0 | Universal Serial Bus Specification | usb.org |
| USB 3.2 | Rev 1.1 | Universal Serial Bus 3.2 Specification | usb.org |
| USB4 | Rev 2.0 | Universal Serial Bus 4 Specification | usb.org |
| USB Type-C | Rev 2.2 | USB Type-C Cable and Connector Specification | usb.org |
| USB Power Delivery | Rev 3.1 | USB Power Delivery Specification | usb.org |
| USB-C Authentication | Rev 1.1 | USB Type-C Authentication Specification | usb.org |
| xHCI | Rev 1.2 | eXtensible Host Controller Interface | intel.com |
| USB HID | Rev 1.11 | Device Class Definition for HID | usb.org |
| USB MSC | Rev 1.4 | Mass Storage Class Bulk-Only Transport | usb.org |
| USB Video Class | Rev 1.5 | USB Video Class Specification | usb.org |
| USB Audio Class | Rev 3.0 | USB Audio Device Class Specification | usb.org |
| USB CDC | Rev 1.2 | Communications Device Class | usb.org |
| USB NCM | Rev 1.0 | Network Control Model | usb.org |
| USB Battery Charging | Rev 1.2 | Battery Charging Specification | usb.org |
| BOT | Rev 1.0 | Bulk-Only Transport | usb.org |
| UASP | Rev 1.0 | USB Attached SCSI Protocol | usb.org |

---

## 2. LINUX KERNEL SOURCE PATHS

### USB Core
```
drivers/usb/core/
├── usb.c               # USB core module init
├── hub.c               # Hub driver, enumeration, port management
├── hcd.c               # Host Controller Driver framework
├── urb.c               # URB allocation, submission, completion
├── message.c           # Control/bulk message helpers
├── config.c            # Configuration/descriptor parsing
├── generic.c           # Generic device driver
├── devio.c             # /dev/bus/usb userspace interface (usbfs)
├── driver.c            # USB driver binding/matching
├── endpoint.c          # Endpoint sysfs representation
├── port.c              # Port abstraction
└── quirks.c            # Device quirk table
```

### xHCI Host Controller
```
drivers/usb/host/
├── xhci.c              # xHCI core driver
├── xhci-ring.c         # TRB ring management
├── xhci-hub.c          # Root hub emulation
├── xhci-mem.c          # Memory allocation (contexts, rings)
├── xhci-pci.c          # PCI glue layer
├── xhci-plat.c         # Platform device glue
├── xhci-dbg.c          # Debug helpers
└── xhci-trace.h        # Tracepoint definitions
```

### DWC3 Controller
```
drivers/usb/dwc3/
├── core.c              # DWC3 core initialization
├── core.h              # Register definitions, data structures
├── host.c              # Host mode (xHCI glue)
├── gadget.c            # Gadget mode driver
├── gadget.h            # Gadget-specific definitions
├── ep0.c               # Endpoint 0 (control) handling
├── io.h                # Register I/O helpers
├── debug.h             # Debug infrastructure
├── debugfs.c           # Debugfs entries
├── trace.h             # Tracepoints
├── drd.c               # Dual-Role Device mode switching
├── ulpi.c              # ULPI PHY interface
├── dwc3-qcom.c         # Qualcomm platform glue
├── dwc3-exynos.c       # Samsung platform glue
└── dwc3-of-simple.c    # Simple OF glue
```

### USB PHY (Qualcomm)
```
drivers/phy/qualcomm/
├── phy-qcom-qusb2.c       # QUSB2 USB 2.0 HS PHY
├── phy-qcom-qmp-usb.c     # QMP USB 3.x SS PHY
├── phy-qcom-qmp-combo.c   # Combined USB+DP PHY
├── phy-qcom-snps-femto-v2.c  # SNPS Femto USB 2.0 PHY
└── phy-qcom-usb-hs.c      # Legacy USB HS PHY
```

### USB Gadget Framework
```
drivers/usb/gadget/
├── composite.c         # Composite gadget framework
├── configfs.c          # ConfigFS interface
├── functions.c         # Function registration
├── udc/
│   └── core.c          # UDC core abstraction
├── function/
│   ├── f_mtp.c         # MTP function
│   ├── f_mass_storage.c # Mass storage function
│   ├── f_fs.c          # FunctionFS (userspace endpoints)
│   ├── f_ncm.c         # NCM networking function
│   ├── f_ecm.c         # ECM networking function
│   ├── f_rndis.c       # RNDIS networking function
│   ├── f_hid.c         # HID function
│   ├── f_uvc.c         # UVC video function
│   ├── f_uac2.c        # UAC2 audio function
│   └── f_acm.c         # ACM serial function
└── legacy/
    └── serial.c        # Legacy serial gadget
```

### Type-C Framework
```
drivers/usb/typec/
├── class.c             # Type-C class implementation
├── mux.c              # Pin MUX/switch control
├── bus.c              # Type-C bus
├── port-mapper.c      # Port to connector mapping
├── tcpm/
│   ├── tcpm.c          # Type-C Port Manager (state machine)
│   └── tcpci.c         # TCPC Interface (I2C register standard)
├── ucsi/
│   ├── ucsi.c          # USB-C Connector System Interface
│   └── ucsi_acpi.c     # ACPI-based UCSI
└── altmodes/
    └── displayport.c   # DisplayPort Alt Mode
```

### USB Class Drivers
```
drivers/usb/class/
├── usblp.c             # Printer class
└── cdc-acm.c           # CDC ACM serial

drivers/usb/storage/
├── usb.c               # USB storage core
├── transport.c         # BOT transport
├── scsiglue.c          # SCSI layer glue
└── uas.c               # USB Attached SCSI (UASP)

drivers/hid/
├── usbhid/
│   ├── hid-core.c     # USB HID core
│   └── hid-pidff.c    # Force feedback
└── hid-input.c         # Input layer integration

drivers/media/usb/uvc/
├── uvc_driver.c        # UVC driver registration
├── uvc_video.c         # Video streaming
├── uvc_ctrl.c          # Camera controls
└── uvc_v4l2.c          # V4L2 interface

sound/usb/
├── card.c              # USB audio card
├── stream.c            # Audio stream
├── endpoint.c          # Audio endpoints
└── pcm.c               # PCM interface

drivers/net/usb/
├── cdc_ncm.c           # CDC NCM driver
├── cdc_ether.c         # CDC ECM/EEM driver
├── rndis_host.c        # RNDIS host
└── ax88179_178a.c      # Realtek USB Ethernet
```

---

## 3. ANDROID AOSP SOURCE PATHS

### USB Framework (Java/Kotlin)
```
frameworks/base/services/usb/
├── java/com/android/server/usb/
│   ├── UsbService.java            # Main USB service
│   ├── UsbDeviceManager.java      # Gadget function management
│   ├── UsbHostManager.java        # Host-side device management
│   ├── UsbPortManager.java        # Type-C port state
│   ├── UsbPermissionManager.java  # Permission management
│   └── UsbProfileGroupSettingsManager.java

frameworks/base/core/java/android/hardware/usb/
├── UsbDevice.java          # USB device representation
├── UsbInterface.java       # Interface abstraction
├── UsbEndpoint.java        # Endpoint abstraction
├── UsbDeviceConnection.java # Host-side device access
├── UsbManager.java         # USB system service API
├── UsbPort.java            # Type-C port representation
├── UsbPortStatus.java      # Port status (roles, mode)
└── UsbAccessory.java       # AOA accessory representation
```

### USB HAL (Hardware Abstraction Layer)
```
hardware/interfaces/usb/
├── 1.0/                    # USB HAL v1.0
├── 1.1/                    # USB HAL v1.1
├── 1.2/                    # USB HAL v1.2
├── 1.3/                    # USB HAL v1.3
├── aidl/                   # AIDL-based USB HAL (modern)
│   ├── android/hardware/usb/
│   │   ├── IUsb.aidl
│   │   ├── IUsbCallback.aidl
│   │   ├── PortRole.aidl
│   │   └── PortStatus.aidl
│   └── default/
│       └── Usb.cpp         # Default implementation
└── gadget/
    ├── 1.0/
    └── aidl/               # USB Gadget HAL
        └── default/
            └── UsbGadget.cpp
```

### USB Init Scripts
```
system/core/rootdir/
├── init.usb.rc             # USB init triggers
├── init.usb.configfs.rc    # ConfigFS-based USB setup

# Vendor-specific (Qualcomm example):
vendor/qcom/proprietary/usb/
├── init.qcom.usb.rc       # Qualcomm USB init
├── init.qcom.usb.sh       # USB setup script
└── compositions/           # USB function compositions
```

### ADB
```
packages/modules/adb/
├── daemon/                 # adbd (device-side)
│   ├── main.cpp
│   ├── usb.cpp            # USB transport
│   └── auth.cpp           # RSA authentication
├── client/                 # adb (host-side)
│   ├── main.cpp
│   ├── usb_linux.cpp      # Linux USB backend
│   └── transport_usb.cpp  # USB transport layer
└── transport.cpp           # Transport abstraction
```

---

## 4. DEVICE TREE BINDING DOCUMENTATION

```
Documentation/devicetree/bindings/usb/
├── generic.yaml            # Generic USB device properties
├── usb-xhci.yaml          # xHCI controller
├── snps,dwc3.yaml          # DWC3 controller
├── qcom,dwc3.yaml          # Qualcomm DWC3 glue
├── usb-connector.yaml      # USB-C connector node
└── typec-tcpc.yaml         # Type-C Port Controller

Key properties:
- dr_mode: "host" | "peripheral" | "otg"
- maximum-speed: "super-speed-plus" | "super-speed" | "high-speed"
- usb-role-switch: Enable role switching
- snps,dis_u2_susphy_quirk: Disable U2 suspend PHY
- snps,dis_enblslpm_quirk: Disable LPM
- phys: phandle to PHY nodes
- phy-names: "usb2-phy", "usb3-phy"
```

---

## 5. TOOLS & SOFTWARE

| Tool | Type | Platform | Use |
|---|---|---|---|
| lsusb | CLI | Linux | List USB devices, dump descriptors |
| usb-devices | CLI | Linux | Human-readable device info |
| usbmon | Kernel module | Linux | Packet capture |
| Wireshark | GUI | Cross-platform | Protocol analysis (with usbmon/USBPcap) |
| USBPcap | Driver | Windows | USB packet capture |
| USB20CV | GUI | Windows | USB 2.0 compliance verification |
| USBCV | GUI | Windows | USB compliance (all versions) |
| Beagle USB 480 | Hardware | — | HS protocol analyzer |
| Beagle USB 5000 | Hardware | — | SS protocol analyzer |
| Ellisys USB Explorer | Hardware | — | Professional USB analyzer |
| LeCroy Voyager | Hardware | — | Full-speed USB4 analyzer |
| Power-Z KM003C | Hardware | — | USB-C PD tester/logger |
| Plugable USBC-TKEY | Hardware | — | USB-C cable tester |
| libusb | Library | Cross-platform | Userspace USB device access |
| pyusb | Library | Python | Python USB device access |
| USB Descriptor Dumper | Android app | Android | Dump descriptors from phone |
| Total Phase Data Center | GUI | Cross-platform | Beagle analyzer software |
| ViewUSB | GUI | Windows | USB device tree viewer |

---

## 6. BOOKS & LEARNING RESOURCES

### Books
| Title | Author | Focus |
|---|---|---|
| USB Complete (5th ed.) | Jan Axelson | Comprehensive USB reference |
| USB 3.0 Technology | Mike Jackson et al. | SuperSpeed deep dive |
| USB Type-C Cable and Connector Spec Guide | USB-IF | Type-C implementation |
| Linux Device Drivers (3rd ed.) | Corbet, Rubini, Kroah-Hartman | Ch. 13: USB Drivers |
| Essential Linux Device Drivers | Sreekrishnan Venkateswaran | USB chapter |
| USB Design by Example | John Hyde | Firmware development |

### Online Resources
| Resource | URL | Content |
|---|---|---|
| USB-IF Official | usb.org | Specs, compliance, certified products |
| kernel.org USB docs | kernel.org/doc/html/latest/usb/ | Linux USB documentation |
| Greg KH USB column | — | Kernel USB maintainer insights |
| Ben Eater USB series | YouTube | Hardware-level USB explanation |
| USB in a NutShell | beyondlogic.org | Concise USB tutorial |
| linux-usb mailing list | vger.kernel.org | Kernel USB development |

### Qualcomm-Specific
| Document | Content |
|---|---|
| QC USB Architecture Doc | DWC3 on Qualcomm platforms |
| SA8295P TRM (USB chapter) | Register maps, PHY details |
| QC USB PHY Programming Guide | QUSB2/QMP tuning parameters |
| QC USB PD Implementation Guide | TCPC/TCPM on QC platforms |
| QC USB Gadget Composition Guide | Android USB function setup |

---

## 7. STUDY PATH (6 WEEKS)

### Week 1: USB Fundamentals
```
Day 1-2: History, topology, terminology
  □ Read USB_00_index.md and first 5 sections of USB_01_master_theory.md
  □ Flashcard Deck 1 (Fundamentals)
  □ Lab 1: Device enumeration (explore sysfs)
  
Day 3-4: Physical layer (USB 2.0)
  □ Study signals, encoding (NRZI), speed detection, chirp
  □ Flashcard Deck 2 (Physical Layer)
  □ Understand all diagrams in sections 4-5 of USB_02_diagrams.md
  
Day 5-7: Protocol layer (packets, transactions)
  □ Study packet types, PIDs, transaction flow
  □ Flashcard Deck 4 (Protocol Layer)
  □ Lab 2: Packet capture with usbmon
  □ Interview Q1-Q15
```

### Week 2: Transfer Types & Descriptors
```
Day 1-2: Transfer types deep dive
  □ Control, Bulk, Interrupt, Isochronous
  □ Flashcard Deck 5 (Transfer Types)
  □ Understand bandwidth allocation
  
Day 3-4: Descriptors & enumeration
  □ Study descriptor hierarchy, all fields
  □ Flashcard Deck 6 (Descriptors)
  □ Parse binary descriptors from hexdump
  □ Interview Q8-Q9, Q26-Q28
  
Day 5-7: Hands-on enumeration
  □ Lab 1 (advanced): Parse descriptor bytes manually
  □ Lab 2: Capture full enumeration sequence
  □ Understand device state machine
  □ Cheatsheet sections 4-8
```

### Week 3: USB-C & Power Delivery
```
Day 1-2: USB Type-C connector and protocol
  □ 24-pin pinout, CC logic, orientation
  □ Flashcard Deck 3 (Type-C & PD)
  □ Study USB-C state machine diagram
  
Day 3-4: USB Power Delivery
  □ PD message types, PDOs, negotiation flow
  □ Study PD message exchange diagram
  □ Interview Q11, Q30-Q31, Q45
  
Day 5-7: USB-C hands-on
  □ Lab 5: Type-C role switching
  □ Lab 6: PD analysis
  □ Debug Scenario 7 (PD failure)
  □ Cheatsheet sections 9-10
```

### Week 4: USB 3.x, USB4 & Linux Stack
```
Day 1-2: SuperSpeed protocol
  □ Dual-bus, LTSSM, encoding, flow control
  □ Flashcard Deck 7 (USB 3.x & USB4)
  □ Study LTSSM state diagram
  
Day 3-4: Linux USB subsystem
  □ Driver architecture, URB lifecycle
  □ Flashcard Deck 8 (Linux & Android)
  □ Study Linux driver stack diagram
  □ Interview Q34-Q36
  
Day 5-7: USB4 & practical
  □ Tunneling architecture, Connection Manager
  □ Lab 4: Performance analysis
  □ Debug Scenarios 3, 4
  □ Cheatsheet sections 12-14 (DWC3, xHCI)
```

### Week 5: Android USB & Automotive
```
Day 1-2: Android USB framework
  □ USB HAL, UsbDeviceManager, ConfigFS setup
  □ Interview Q48 (Android Auto/CarPlay)
  □ Lab 7: Android USB debug session
  
Day 3-4: USB gadget development
  □ ConfigFS API, function drivers, composite
  □ Lab 3: Create USB gadget
  □ Debug Scenario 6 (gadget not visible)
  
Day 5-7: Automotive USB
  □ Flashcard Deck 9 (Automotive)
  □ Interview Q46 (system design), Q49 (EMC)
  □ Debug Scenario 8 (Android Auto)
  □ Study automotive topology diagram
```

### Week 6: Advanced Topics & Interview Prep
```
Day 1-2: xHCI deep dive
  □ Ring architecture, TRBs, doorbells
  □ Study xHCI architecture diagram
  □ Interview Q32-Q33
  
Day 3-4: Security, compliance, advanced debug
  □ BadUSB, USB-C Auth, compliance testing
  □ Interview Q51, Q58 (security design)
  □ Debug Scenario 1-2, 5
  
Day 5-7: Architecture questions & review
  □ Practice all Staff-level interview questions (Q57-Q60)
  □ Review all flashcard decks
  □ Mock interview: Design USB subsystem for cockpit
  □ Time-box cheatsheet review (30 min)
```

---

## 8. GLOSSARY

| Term | Definition |
|---|---|
| ACK | Acknowledgment handshake packet |
| ACM | Abstract Control Model (serial over USB) |
| ADB | Android Debug Bridge |
| Alt Mode | Alternate Mode: non-USB protocols over USB-C pins |
| AOA | Android Open Accessory protocol |
| BCD | Binary Coded Decimal (version encoding) |
| BMC | Biphase Mark Coding (PD on CC) |
| BOT | Bulk-Only Transport (mass storage) |
| BOS | Binary device Object Store descriptor |
| CC | Configuration Channel (Type-C) |
| CDC | Communications Device Class |
| CRC | Cyclic Redundancy Check |
| ConfigFS | Configuration Filesystem (kernel gadget interface) |
| DFP | Downstream Facing Port (host role) |
| DRD | Dual-Role Device (host or peripheral) |
| DRP | Dual-Role Port (alternates between DFP/UFP) |
| DWC3 | DesignWare Core USB 3.0 (Synopsys IP) |
| ECM | Ethernet Control Model |
| E-marker | Electronic marker chip in USB-C cable |
| EP | Endpoint |
| EPR | Extended Power Range (USB PD, >100W) |
| ERDY | Endpoint Ready (SS flow control) |
| EOP | End of Packet |
| FFS | FunctionFS (userspace gadget endpoints) |
| FS | Full Speed (12 Mbps) |
| GoodCRC | PD acknowledgment message |
| HID | Human Interface Device |
| HS | High Speed (480 Mbps) |
| iAP2 | iPod Accessory Protocol 2 (Apple) |
| IOC | Interrupt on Completion (TRB flag) |
| ITP | Isochronous Timestamp Packet (SS) |
| LFPS | Low Frequency Periodic Signaling |
| LPM | Link Power Management |
| LS | Low Speed (1.5 Mbps) |
| LTSSM | Link Training and Status State Machine |
| MTP | Media Transfer Protocol |
| MSC | Mass Storage Class |
| NAK | Negative Acknowledge (device busy) |
| NCM | Network Control Model |
| NRDI | Non-Return-to-Zero Inverted (encoding) |
| NRDY | Not Ready (SS flow control) |
| OTG | On-The-Go (legacy dual-role) |
| PD | Power Delivery |
| PDO | Power Data Object |
| PHY | Physical layer transceiver |
| PID | Packet Identifier (or Product ID, context-dependent) |
| PIPE | PHY Interface for PCI Express (SS PHY interface) |
| PPS | Programmable Power Supply |
| QMP | Qualcomm Multi-Protocol PHY |
| QUSB2 | Qualcomm USB 2.0 PHY |
| Rd | Pull-down resistance (UFP CC) |
| RNDIS | Remote Network Driver Interface Specification |
| Rp | Pull-up resistance (DFP CC) |
| SBU | Sideband Use (Type-C pins) |
| SE0 | Single-Ended Zero (both lines low) |
| SOF | Start of Frame |
| SPR | Standard Power Range (USB PD, ≤100W) |
| SS | SuperSpeed (5 Gbps) |
| SS+ | SuperSpeed Plus (10+ Gbps) |
| STALL | Error handshake (endpoint halted) |
| TCPC | Type-C Port Controller (hardware) |
| TCPM | Type-C Port Manager (software) |
| TRB | Transfer Request Block (xHCI) |
| UAC | USB Audio Class |
| UASP | USB Attached SCSI Protocol |
| UCSI | USB-C Connector System Interface |
| UDC | USB Device Controller |
| UFP | Upstream Facing Port (device role) |
| ULPI | UTMI+ Low Pin Interface |
| URB | USB Request Block (Linux kernel) |
| USB-IF | USB Implementers Forum |
| UTMI+ | USB 2.0 Transceiver Macrocell Interface (PHY bus) |
| UVC | USB Video Class |
| VBUS | USB power line (5-48V) |
| VCONN | Voltage for CC (powers E-marker) |
| VID | Vendor ID (16-bit, USB-IF assigned) |
| xHCI | eXtensible Host Controller Interface |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
