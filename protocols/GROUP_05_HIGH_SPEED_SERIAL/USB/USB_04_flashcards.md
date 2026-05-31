# USB PROTOCOL — FLASHCARDS (SPACED REPETITION)
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 04 of 08
# Format: Q/A pairs organized in 10 decks, 250+ cards
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: USB FUNDAMENTALS (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | What does USB stand for? | Universal Serial Bus |
| 2 | Who manages the USB specification? | USB Implementers Forum (USB-IF) |
| 3 | What year was USB 1.0 released? | 1996 |
| 4 | What communication model does USB use? | Host-centric (host initiates all transactions) |
| 5 | What is USB's topology? | Tiered star (host at center, hubs extend to devices) |
| 6 | Maximum devices per USB host controller? | 127 (7-bit address, 0 reserved) |
| 7 | Maximum hub tiers? | 7 (including root hub) |
| 8 | Maximum non-root hubs between host and device? | 5 |
| 9 | What makes USB "hot-pluggable"? | Devices can be connected/disconnected without power cycling the host |
| 10 | What is a "self-describing" device? | Device reports its capabilities via descriptors during enumeration |
| 11 | Name the 4 USB transfer types | Control, Bulk, Interrupt, Isochronous |
| 12 | Which transfer type guarantees bandwidth? | Isochronous (reserved in scheduling) |
| 13 | Which transfer type has no error recovery? | Isochronous (no retransmission) |
| 14 | Which transfer type is reliable but has no bandwidth guarantee? | Bulk |
| 15 | What is an endpoint? | Addressable buffer in device that is source/sink of data |
| 16 | How many endpoints can a device have? | Up to 32 (16 IN + 16 OUT, including EP0) |
| 17 | What is special about Endpoint 0? | Mandatory, bidirectional, used for control transfers |
| 18 | What direction is "IN"? | Device → Host |
| 19 | What direction is "OUT"? | Host → Device |
| 20 | What is a pipe? | Logical connection between host endpoint buffer and device endpoint |
| 21 | What is a USB frame? | 1ms time slot (Full Speed), subdivided into microframes for HS |
| 22 | What is a microframe? | 125µs time slot within a frame (High Speed) |
| 23 | What starts each frame/microframe? | SOF (Start of Frame) packet |
| 24 | What is the maximum USB 2.0 cable length? | 5 meters per segment |
| 25 | What is the maximum USB 3.x cable length? | 3 meters (passive), longer with active cables |

---

# DECK 2: PHYSICAL LAYER & SIGNALING (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | USB 2.0 uses how many signal wires? | 4 (VBUS, D+, D-, GND) + shield |
| 2 | USB 3.x adds how many signal wires? | 5 additional: SS_TX+, SS_TX-, SS_RX+, SS_RX-, GND_DRAIN |
| 3 | What encoding does USB 2.0 use? | NRZI (Non-Return-to-Zero Inverted) |
| 4 | In NRZI, what does a logic '0' mean? | Transition (signal changes state) |
| 5 | In NRZI, what does a logic '1' mean? | No transition (signal stays same) |
| 6 | What is bit stuffing in USB? | Inserting a '0' after 6 consecutive '1's (forces transition for clock recovery) |
| 7 | How does host detect Low Speed device? | 1.5kΩ pull-up on D- |
| 8 | How does host detect Full Speed device? | 1.5kΩ pull-up on D+ |
| 9 | How is High Speed negotiated? | Chirp handshake during bus reset (device chirp K, host chirp K-J pairs) |
| 10 | What is the HS differential voltage? | 400mV (vs 3.3V for FS/LS) |
| 11 | What is SE0? | Single-Ended Zero: both D+ and D- low (reset, end of packet) |
| 12 | USB 3.0 Gen 1 encoding? | 8b/10b (25% overhead) |
| 13 | USB 3.2 Gen 2 encoding? | 128b/132b (3.1% overhead) |
| 14 | What is LFPS? | Low Frequency Periodic Signaling: used for SS link management (wake, reset) |
| 15 | What are the USB 3.x link power states? | U0 (active), U1 (standby-fast), U2 (standby-slow), U3 (suspend) |
| 16 | What wakes from U1/U2 states? | LFPS signaling |
| 17 | What is LTSSM? | Link Training and Status State Machine: manages SS link states |
| 18 | USB 2.0 FS signaling frequency? | 12 MHz |
| 19 | USB 2.0 HS signaling frequency? | 480 MHz |
| 20 | USB 3.0 Gen 1 signaling frequency? | 5 GHz |
| 21 | USB 3.2 Gen 2 signaling frequency? | 10 GHz |
| 22 | USB impedance target? | 90Ω differential (USB 2.0 and 3.x) |
| 23 | What is the USB 2.0 HS chirp K? | Device drives D- high (~800mV) during reset for 1-7ms |
| 24 | What polarity is the EOP (End of Packet) in USB 2.0? | SE0 for 2 bit times, then J state for 1 bit |
| 25 | How many lanes does USB 3.2 Gen 2×2 use? | 2 lanes each direction (dual-lane, 2×10 Gbps) |

---

# DECK 3: USB TYPE-C & POWER DELIVERY (30 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | How many pins on USB-C? | 24 |
| 2 | What makes USB-C reversible? | Symmetric pin layout — works in either orientation |
| 3 | What is the CC pin? | Configuration Channel: orientation detection, role assignment, PD messaging |
| 4 | How many CC pins in a receptacle? | 2 (CC1 and CC2) |
| 5 | What is Rp? | Pull-up resistance on DFP (host) CC pin |
| 6 | What is Rd? | Pull-down resistance on UFP (device) CC pin |
| 7 | How does CC determine orientation? | Only one CC pin sees valid Rp/Rd connection (other is open or VCONN) |
| 8 | What are the Rp current levels? | Default (56kΩ/500mA), 1.5A (22kΩ), 3A (10kΩ) |
| 9 | What is VCONN? | 5V power to cable E-marker chip, supplied on unused CC pin |
| 10 | What is an E-marker? | Electronic chip in cable that reports cable capabilities (speed, current, voltage) |
| 11 | When is E-marker required? | >3A cables, >USB 3.2 Gen 1, all USB4 cables |
| 12 | What is DFP? | Downstream Facing Port (host/source role) |
| 13 | What is UFP? | Upstream Facing Port (device/sink role) |
| 14 | What is DRP? | Dual Role Port: can be either DFP or UFP |
| 15 | What is USB PD? | Power Delivery: protocol negotiating voltage/current over CC |
| 16 | What encoding does PD use on CC? | BMC (Biphase Mark Coding) at 300 kbaud |
| 17 | Maximum USB PD power (standard)? | 100W (20V × 5A) in SPR |
| 18 | Maximum USB PD power (extended)? | 240W (48V × 5A) in EPR |
| 19 | What is a PDO? | Power Data Object: describes one voltage/current offering |
| 20 | What is PPS in USB PD? | Programmable Power Supply: allows fine voltage adjustment (20mV steps) |
| 21 | What is Power Role Swap? | Source and sink exchange power roles without disconnecting |
| 22 | What is Data Role Swap? | DFP and UFP exchange data roles without disconnecting |
| 23 | What is VCONN Swap? | Transfer of VCONN supply responsibility between ports |
| 24 | What is Alt Mode? | Using USB-C pins for non-USB protocols (DisplayPort, Thunderbolt, HDMI) |
| 25 | What is SBU pin used for? | Sideband Use: low-speed signals for Alt Modes (e.g., DP AUX) |
| 26 | What is GoodCRC? | Acknowledgment message sent within 195µs of receiving a PD message |
| 27 | What triggers Hard Reset? | Communication failure (no GoodCRC after retries) |
| 28 | What happens during Hard Reset? | VBUS goes to 0V briefly, then comes back at 5V (re-negotiate) |
| 29 | What is USB-C Authentication? | Cryptographic protocol to verify cable/device identity (prevent counterfeits) |
| 30 | What is the Ra resistance? | 1kΩ pull-down on VCONN pin for audio adapter accessories |

---

# DECK 4: PROTOCOL LAYER & PACKETS (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | Name the 4 USB 2.0 packet types | Token, Data, Handshake, Special (SOF, PRE) |
| 2 | What is a PID? | Packet ID: 4-bit value + 4-bit complement identifying packet type |
| 3 | PID for IN token? | 0x69 (1001 0110 with complement) |
| 4 | PID for OUT token? | 0xE1 |
| 5 | PID for SETUP token? | 0x2D |
| 6 | PID for DATA0? | 0xC3 |
| 7 | PID for DATA1? | 0x4B |
| 8 | PID for ACK? | 0xD2 |
| 9 | PID for NAK? | 0x5A |
| 10 | PID for STALL? | 0x1E |
| 11 | What does NAK mean? | Device is busy, host should retry later |
| 12 | What does STALL mean? | Device error/unsupported request, endpoint halted |
| 13 | What is the SYNC field? | 8-bit (FS/LS) or 32-bit (HS) synchronization pattern at packet start |
| 14 | What CRC protects token packets? | CRC-5 (covers ADDR + ENDP, 11 bits) |
| 15 | What CRC protects data packets? | CRC-16 (USB 2.0), CRC-32 (SuperSpeed) |
| 16 | What is the data toggle bit? | Alternating DATA0/DATA1 PID to detect duplicate packets |
| 17 | When is data toggle reset? | SETUP token, SET_CONFIGURATION, CLEAR_FEATURE(ENDPOINT_HALT) |
| 18 | What is SOF? | Start of Frame: token sent every 1ms (FS) or 125µs (HS) |
| 19 | SOF frame number size? | 11 bits (0-2047, wraps around) |
| 20 | What is a PRE token? | Preamble: tells HS hub to switch to FS/LS for next packet |
| 21 | SuperSpeed flow control mechanism? | NRDY/ERDY (device tells host when ready, replaces polling) |
| 22 | What is burst mode in SS? | Host sends multiple data packets before waiting for ACK |
| 23 | What is a route string in SS? | Header field routing packets through hubs (replaces broadcast) |
| 24 | What replaced SOF in USB 3.x? | ITP (Isochronous Timestamp Packet) for timing sync |
| 25 | Max payload size for HS bulk endpoint? | 512 bytes |

---

# DECK 5: TRANSFER TYPES & BANDWIDTH (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | Control transfer: typical use? | Device configuration, standard requests (GET_DESCRIPTOR, SET_ADDRESS) |
| 2 | Control transfer: how many stages? | 3: Setup, Data (optional), Status |
| 3 | Control transfer max packet size (HS)? | 64 bytes for EP0 |
| 4 | Bulk transfer: typical use? | Large data without time constraints (storage, printing) |
| 5 | Bulk transfer max packet size (HS)? | 512 bytes |
| 6 | Bulk transfer max packet size (SS)? | 1024 bytes |
| 7 | Bulk transfer error handling? | Automatic retry (3× typical), CRC + data toggle |
| 8 | Interrupt transfer: typical use? | Small periodic data with bounded latency (HID, status) |
| 9 | Interrupt transfer max packet (HS)? | 1024 bytes (up to 3 per microframe) |
| 10 | Interrupt polling interval range (HS)? | 1-255 microframes (125µs to ~32ms) |
| 11 | Isochronous transfer: typical use? | Audio/video streaming (constant rate, bounded latency) |
| 12 | Isochronous max packet (HS)? | 1024 bytes (up to 3 per microframe = 3072 bytes) |
| 13 | Does isochronous use handshake? | No (no ACK/NAK/STALL) |
| 14 | Does isochronous retry on error? | No (just skip the frame, accept data loss) |
| 15 | How much bandwidth can periodic (isoch+interrupt) reserve? | Up to 80% of frame time (20% for control+bulk) |
| 16 | USB 2.0 HS theoretical bandwidth? | 480 Mbps raw, ~384 Mbps usable (80% × protocol overhead) |
| 17 | USB 3.0 actual throughput? | ~400 MB/s typical (of 5 Gbps = 625 MB/s raw) |
| 18 | What is bandwidth allocation? | Host scheduler reserves bandwidth for periodic endpoints |
| 19 | What happens if isochronous bandwidth unavailable? | SET_INTERFACE returns error (-ENOSPC) |
| 20 | Bulk transfer priority? | Lowest (uses leftover bandwidth after periodic + control) |
| 21 | Control transfer priority? | Highest (guaranteed 10% of bandwidth minimum) |
| 22 | What is UASP? | USB Attached SCSI Protocol: queued commands over streams (faster than BOT) |
| 23 | What are USB 3.x streams? | Multiple logical channels on one bulk endpoint (for UASP parallel I/O) |
| 24 | Max stream IDs per endpoint? | Up to 65536 (16-bit stream ID) |
| 25 | How does SS burst improve throughput? | Multiple back-to-back packets without waiting for individual ACKs |

---

# DECK 6: DESCRIPTORS & ENUMERATION (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | Device descriptor size? | 18 bytes |
| 2 | Device descriptor: bLength field? | Always 18 (0x12) |
| 3 | Device descriptor: bDescriptorType? | 1 (DEVICE) |
| 4 | What is bcdUSB? | USB spec version in BCD (0x0200 = USB 2.0, 0x0310 = USB 3.1) |
| 5 | What is VID? | Vendor ID: 16-bit, assigned by USB-IF ($5000/year) |
| 6 | What is PID? | Product ID: 16-bit, assigned by vendor |
| 7 | What is bMaxPacketSize0? | Maximum packet size for EP0 (8, 16, 32, or 64 for FS; always 64 for HS; 512 for SS) |
| 8 | What is bNumConfigurations? | Number of configurations the device supports (usually 1) |
| 9 | Configuration descriptor: bDescriptorType? | 2 (CONFIGURATION) |
| 10 | What is wTotalLength? | Total length of configuration + all interface + all endpoint descriptors |
| 11 | What is bMaxPower? | Maximum bus current in units of 2mA (e.g., 250 = 500mA) |
| 12 | Interface descriptor: bDescriptorType? | 4 (INTERFACE) |
| 13 | What is bInterfaceClass? | USB class code (0x03=HID, 0x08=MSC, 0xFF=vendor-specific) |
| 14 | Endpoint descriptor: bDescriptorType? | 5 (ENDPOINT) |
| 15 | Endpoint descriptor size? | 7 bytes |
| 16 | What does bEndpointAddress contain? | Bit 7 = direction (1=IN), bits 3:0 = endpoint number |
| 17 | What does bmAttributes contain? | Transfer type (bits 1:0): 0=control, 1=isoch, 2=bulk, 3=interrupt |
| 18 | What is wMaxPacketSize? | Maximum bytes the endpoint can send/receive per transaction |
| 19 | What is bInterval? | Polling interval (meaning depends on speed: ms for FS, 2^(n-1) microframes for HS) |
| 20 | Default device address at power-on? | 0 |
| 21 | What is SET_ADDRESS? | Standard request that assigns unique address (1-127) to device |
| 22 | What is GET_DESCRIPTOR? | Standard request to retrieve device/config/string descriptors |
| 23 | What is SET_CONFIGURATION? | Standard request to activate a configuration (device enters Configured state) |
| 24 | What is BOS descriptor? | Binary device Object Store: reports USB 3.x and other extended capabilities |
| 25 | What must device do on receiving SETUP token? | Always ACK (cannot NAK or STALL the SETUP stage) |

---

# DECK 7: USB 3.x & USB4 (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | USB 3.0 marketing name? | USB 3.2 Gen 1 (5 Gbps SuperSpeed) — renamed multiple times |
| 2 | USB 3.1 Gen 2 speed? | 10 Gbps (SuperSpeed+) |
| 3 | USB 3.2 Gen 2×2 speed? | 20 Gbps (dual-lane SuperSpeed+) |
| 4 | USB4 Gen 3×2 speed? | 40 Gbps |
| 5 | USB4 Version 2 speed? | 80 Gbps (optionally 120 Gbps asymmetric) |
| 6 | What is dual-bus architecture? | SS has its own physical lanes separate from USB 2.0 D+/D- |
| 7 | What protocols can USB4 tunnel? | USB 3.x, DisplayPort, PCIe |
| 8 | What is USB4 Connection Manager? | Software/firmware that establishes and manages protocol tunnels |
| 9 | Is USB4 mandatory on USB-C? | USB4 requires USB-C; not all USB-C ports support USB4 |
| 10 | What is Thunderbolt's relationship to USB4? | TB3 contributed spec to USB4; TB4 is a superset of USB4 |
| 11 | USB 3.0 uses what scrambling? | LFSR-based scrambling (after 8b/10b encoding) |
| 12 | What is training sequence in SS? | TSEQ, TS1, TS2 ordered sets for link parameter negotiation |
| 13 | What is SKP ordered set? | Compensates for clock frequency differences between link partners |
| 14 | What is Compliance Mode? | LTSSM state for testing: outputs fixed test patterns |
| 15 | SuperSpeed hot reset procedure? | Hot Reset LFPS → both sides re-train from Polling state |
| 16 | What is U1 exit latency typical value? | <10 µs |
| 17 | What is U2 exit latency typical value? | <100 µs (varies by implementation) |
| 18 | What header field replaces broadcast in SS? | Route String (20-bit, encodes hub path) |
| 19 | How many hub tiers in SS? | 5 (fewer than USB 2.0's 7) |
| 20 | SS link width negotiation? | Gen 2×2 negotiates 1 or 2 lanes based on cable/PHY capability |
| 21 | What is a Link Command? | 2-symbol management command (e.g., LGOOD, LCRD, LGO_U1) |
| 22 | What is a Data Packet Header (DPH)? | 12-byte header for all SS data packets (type, route, length, etc.) |
| 23 | What is ERDY in SS? | Endpoint Ready: device asynchronously notifies host it has data/space |
| 24 | What is NRDY in SS? | Not Ready: device tells host it cannot accept/provide data now |
| 25 | USB4 minimum data rate? | 20 Gbps (Gen 2×2) |

---

# DECK 8: LINUX & ANDROID USB (30 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | Main Linux USB core module? | usbcore.ko |
| 2 | What is a URB? | USB Request Block: kernel data structure for USB transfers |
| 3 | What kernel subsystem manages USB transfers? | USB Core (usbcore) |
| 4 | Command to list USB devices in Linux? | `lsusb` (or `lsusb -t` for tree view) |
| 5 | USB sysfs path? | /sys/bus/usb/devices/ |
| 6 | How to read device descriptors from sysfs? | `cat /sys/bus/usb/devices/X-Y/descriptors` (binary) |
| 7 | What is xhci-hcd? | Linux driver for xHCI host controllers |
| 8 | What is dwc3-core? | Linux driver for Synopsys DWC3 USB controller |
| 9 | What is dwc3-qcom? | Qualcomm-specific glue layer for DWC3 |
| 10 | USB gadget sysfs path? | /sys/kernel/config/usb_gadget/ (ConfigFS) |
| 11 | How to create a USB gadget in Linux? | mkdir /config/usb_gadget/g1, write VID/PID/strings, create functions, link, write UDC |
| 12 | What is UDC? | USB Device Controller: hardware abstraction for gadget mode |
| 13 | What is ConfigFS? | Filesystem for dynamic kernel object configuration (used for gadget setup) |
| 14 | What is f_fs (FunctionFS)? | Gadget function that delegates endpoint handling to userspace (ADB uses this) |
| 15 | What is the Android USB HAL? | android.hardware.usb: manages USB port state, gadget functions |
| 16 | Android USB config property? | `sys.usb.config` (e.g., "mtp,adb") |
| 17 | How does Android handle USB mode switching? | init.rc triggers on sys.usb.config property change |
| 18 | What is adbd? | Android Debug Bridge Daemon: userspace process handling ADB protocol |
| 19 | Where is Android USB gadget configured? | init.usb.rc / init.usb.configfs.rc |
| 20 | What is USB tethering in Android? | Phone acts as USB network adapter (NCM/RNDIS) sharing mobile data |
| 21 | What file enables USB debugging? | Developer Options → USB Debugging (sets persist.sys.usb.config to include adb) |
| 22 | How does ADB authentication work? | RSA key exchange: host sends public key, user approves on device |
| 23 | What is usb_monitor in Android? | Framework service tracking USB port/device state changes |
| 24 | What is UsbDeviceManager? | Android framework class managing gadget USB functions |
| 25 | What is UsbHostManager? | Android framework class managing attached USB devices |
| 26 | Where are USB device permissions in Android? | /etc/permissions/platform.xml or dynamic via USB_DEVICE_ATTACHED intent filter |
| 27 | Android USB accessory mode? | Phone acts as USB device, accessory acts as host (Android Open Accessory) |
| 28 | What is AOA protocol? | Android Open Accessory: vendor requests 0x33-0x3A to switch phone to accessory mode |
| 29 | Linux USB tracing? | /sys/kernel/debug/tracing/events/xhci-hcd/ and events/gadget/ |
| 30 | How to capture USB traffic in Linux? | usbmon (kernel module) + Wireshark, or hardware analyzer |

---

# DECK 9: AUTOMOTIVE USB (20 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | USB challenges in automotive? | Temperature extremes, vibration, EMC, long cables, multi-user, always-on |
| 2 | Max recommended USB cable in vehicle? | 2 meters (signal integrity + EMC) |
| 3 | Automotive temperature range for USB? | -40°C to +85°C (or +105°C for under-hood) |
| 4 | How does Android Auto connect via USB? | AOA protocol → re-enumerate → bulk transfer video/audio/input |
| 5 | How does CarPlay connect via USB? | iAP2 → NCM (IP networking) → AirPlay + HID over IP |
| 6 | Key difference between AA and CarPlay USB? | AA uses custom bulk protocol; CarPlay uses IP networking (NCM) |
| 7 | Why use USB Ethernet (NCM) in automotive? | Connect modules (tuner, modem) without dedicated Ethernet hardware |
| 8 | What is the TCPC in automotive USB? | Type-C Port Controller: IC managing CC, PD, orientation (e.g., TPS65988) |
| 9 | EMC standard for automotive USB? | CISPR 25 (emissions) + ISO 11452 (immunity) |
| 10 | ESD protection for automotive USB ports? | TVS diodes, ISO 10605 compliant (±15kV air) |
| 11 | Why separate USB controllers per port? | Isolation, independent power management, no contention |
| 12 | USB in SA8295P: how many DWC3 controllers? | 4 (configurable as host/device/DRD) |
| 13 | What PHYs does SA8295P use for USB? | QUSB2 (USB 2.0 HS) + QMP (USB 3.x SS) |
| 14 | Automotive USB PD use case? | Fast phone charging (45W+) from cockpit USB-C port |
| 15 | USB camera in automotive? | UVC class for cabin monitoring, dashcam, surround view |
| 16 | USB audio in automotive? | UAC class for external microphone arrays (voice commands) |
| 17 | Why is MTP preferred over MSC in automotive? | MTP: phone retains filesystem control, safer, multiple access allowed |
| 18 | USB hub use in automotive? | Rear seat ports (2-4 ports from single controller via hub) |
| 19 | Common automotive USB failure mode? | Connector corrosion, cable chafing, intermittent connection from vibration |
| 20 | USB compliance testing for automotive? | USB-IF electrical + protocol, PLUS automotive EMC + environmental + ESD |

---

# DECK 10: TOOLS, DEBUGGING & HARDWARE (25 cards)

| # | Front (Question) | Back (Answer) |
|---|---|---|
| 1 | lsusb command function? | Lists all USB devices with VID:PID and bus info |
| 2 | lsusb -v shows what? | Verbose: full descriptor dump for all devices |
| 3 | lsusb -t shows what? | Tree: hierarchical view of USB bus topology |
| 4 | What is usbmon? | Linux kernel module that captures USB packets (like tcpdump for USB) |
| 5 | How to use usbmon with Wireshark? | modprobe usbmon, then Wireshark captures from usbmonX interface |
| 6 | What is USBPcap? | Windows-based USB packet capture (similar to usbmon) |
| 7 | Name a professional USB protocol analyzer | Ellisys USB Explorer, Beagle USB 480/5000, LeCroy USB |
| 8 | What does a USB protocol analyzer capture? | Every packet on the bus: PIDs, data, timing, errors, SOFs |
| 9 | How to read USB descriptors without driver? | `cat /sys/bus/usb/devices/X-Y/descriptors | python decode.py` or usb-devices command |
| 10 | DWC3 debug registers location? | /sys/kernel/debug/usb/dwc3.X/ (via debugfs) |
| 11 | How to force USB gadget mode? | Write to /config/usb_gadget/g1/UDC |
| 12 | How to check USB speed of attached device? | `cat /sys/bus/usb/devices/X-Y/speed` (returns 480, 5000, etc.) |
| 13 | How to reset a USB device from Linux? | Write to /sys/bus/usb/devices/X-Y/authorized (0 then 1), or use libusb reset |
| 14 | What is usb-devices command? | Prints human-readable USB device info (from usbutils package) |
| 15 | How to check USB power in sysfs? | /sys/bus/usb/devices/X-Y/power/ (runtime_status, control, autosuspend_delay_ms) |
| 16 | What kernel config enables USB debug? | CONFIG_USB_DEBUG, CONFIG_USB_MON, CONFIG_DYNAMIC_DEBUG |
| 17 | What is QUSB2 PHY? | Qualcomm USB 2.0 High-Speed PHY (UTMI+ interface to DWC3) |
| 18 | What is QMP PHY? | Qualcomm Multi-Protocol PHY: handles USB 3.x SuperSpeed (PIPE interface) |
| 19 | DWC3 mode configuration? | dr_mode device-tree property: "host", "peripheral", "otg" (DRD) |
| 20 | What is UTMI+ interface? | PHY interface between USB 2.0 transceiver and controller (8/16-bit data) |
| 21 | What is PIPE interface? | PHY Interface for PCI Express/USB 3.x: connects SS PHY to controller |
| 22 | How to trigger USB role switch? | Write to /sys/class/typec/portX/data_role (or via TCPM events) |
| 23 | xHCI debug capability? | DbC (Debug Capability): allows using USB3 port as debug channel |
| 24 | What is USB compliance test tool? | USB20CV (USB 2.0 Command Verifier) / USBCV (all versions) from USB-IF |
| 25 | How to stress test USB? | Loop: connect/disconnect, large transfers, concurrent devices, temperature cycling |

---

# BONUS DECK: MNEMONICS & MEMORY AIDS (5 cards)

| # | Front | Back |
|---|---|---|
| 1 | Remember PID nibble complement rule? | PID byte = lower nibble + complement of lower nibble (e.g., IN=1001 + 0110) |
| 2 | USB speeds mnemonic: "1-12-480-5-10-20-40-80" | LS-FS-HS-SS(Gen1)-SS+(Gen2)-SS+(Gen2×2)-USB4-USB4v2 in Mbps/Gbps |
| 3 | Transfer type priority? | "Control Is Bulk's Isochronous" → Control > Isochronous > Interrupt > Bulk (in guaranteed BW) |
| 4 | Descriptor hierarchy? | "DICE" → Device → Interface → (Configuration wraps them) → Endpoint |
| 5 | USB-C CC = "3 C's"? | Configuration (orient), Communication (PD), Current (advertisement) |

---

END OF DOCUMENT 04 — FLASHCARDS
