# USB PROTOCOL — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 03 of 08
# Levels: Junior → Mid → Senior → Staff/Architect
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (25 Questions)

---

### Q1. What is USB and what are its key characteristics?
**A:** USB (Universal Serial Bus) is a serial, host-centric communication standard. Key characteristics: (1) **Host-initiated** — host polls all devices, (2) **Hot-pluggable** — connect/disconnect without power-off, (3) **Self-describing** — devices report capabilities via descriptors, (4) **Tiered star topology** — up to 127 devices via hubs, (5) **Power delivery** — provides power from host to devices (up to 240W with PD), (6) **Class-based** — standardized device classes for driverless operation.

---

### Q2. List the USB speed grades.
**A:**
- **Low Speed**: 1.5 Mbps (USB 1.0, keyboards/mice)
- **Full Speed**: 12 Mbps (USB 1.1, audio, legacy devices)
- **High Speed**: 480 Mbps (USB 2.0, storage, cameras)
- **SuperSpeed (Gen 1)**: 5 Gbps (USB 3.0/3.2 Gen 1)
- **SuperSpeed+ (Gen 2)**: 10 Gbps (USB 3.1/3.2 Gen 2)
- **SuperSpeed+ (Gen 2×2)**: 20 Gbps (USB 3.2 Gen 2×2)
- **USB4**: 40/80/120 Gbps

---

### Q3. What signals does a USB 2.0 cable carry?
**A:** Four conductors plus shield: **VBUS** (+5V power), **D+** (data positive), **D-** (data negative), **GND** (ground), and cable **shield**. D+ and D- form a differential pair for data signaling.

---

### Q4. How does the host detect device speed?
**A:** Via pull-up resistors on the device: **Low Speed** has 1.5kΩ pull-up on D-, **Full Speed** has 1.5kΩ pull-up on D+. **High Speed** is detected via chirp handshake protocol during bus reset — the device sends a chirp K, host responds with K-J pairs, then both switch to 480 Mbps signaling (400mV differential).

---

### Q5. What is the USB topology?
**A:** Tiered star topology. A single host controller (with root hub) is at the center. Hubs extend the bus to more devices. Maximum 7 tiers, 5 hubs deep, 127 devices per controller, 5m max cable per segment (USB 2.0). Devices cannot communicate directly with each other — all traffic goes through the host.

---

### Q6. What are the four USB transfer types?
**A:**
- **Control**: Configuration/commands (bidirectional, guaranteed delivery)
- **Bulk**: Large data without timing (storage — reliable, no bandwidth guarantee)
- **Interrupt**: Small periodic data with guaranteed latency (HID — keyboard/mouse)
- **Isochronous**: Streaming with guaranteed bandwidth (audio/video — no retransmission)

---

### Q7. What is an endpoint?
**A:** An addressable buffer in a USB device that is the source or sink of data. Identified by endpoint number (0-15) and direction (IN=device→host, OUT=host→device). Every device must have Endpoint 0 (bidirectional control endpoint). A device can have up to 30 additional endpoints (15 IN + 15 OUT).

---

### Q8. What is enumeration?
**A:** The process where the host discovers and configures a newly attached device: (1) Detect attach via D+/D- pull-up, (2) Bus Reset, (3) GET_DESCRIPTOR to learn device capabilities, (4) SET_ADDRESS to assign unique address, (5) Read full descriptors, (6) SET_CONFIGURATION to activate device. After this, the device is ready for class-specific operation.

---

### Q9. What is a USB descriptor?
**A:** A data structure the device provides to describe its capabilities. Hierarchy: **Device** (VID/PID/class) → **Configuration** (power, interfaces) → **Interface** (function, class) → **Endpoint** (address, type, max packet size). Also: String descriptors (human-readable names) and BOS (capabilities).

---

### Q10. What is the difference between USB-A and USB-C?
**A:** USB-A: Rectangle, non-reversible, host-only (DFP) connector, max USB 3.x speed. USB-C: 24-pin oval, **reversible** (works either way), supports both host and device roles (DRD), USB4 speeds (80 Gbps), Power Delivery up to 240W, and Alt Modes (DisplayPort, Thunderbolt).

---

### Q11. What is USB Power Delivery?
**A:** A protocol running over the USB-C CC (Configuration Channel) pin that negotiates power between source and sink. Supports: 5V, 9V, 15V, 20V (standard), up to 48V (Extended Power Range). Maximum 240W (48V × 5A). Uses structured messages (Source_Capabilities, Request, Accept, PS_Ready).

---

### Q12. What is NRZI encoding?
**A:** Non-Return-to-Zero Inverted — USB 2.0's line encoding. A logical 0 causes a **transition** on the line; a logical 1 means **no transition**. Combined with bit stuffing (insert a 0 after 6 consecutive 1s), this ensures enough transitions for the receiver to recover the clock without a separate clock line.

---

### Q13. What are SOF packets?
**A:** Start of Frame — marker packets sent by the host every 1ms (Full Speed) or 125µs (High Speed microframes). Contain an 11-bit frame number. Purposes: (1) Provide timing reference for isochronous devices, (2) Keep devices awake (prevent suspend), (3) Synchronize device internal clocks.

---

### Q14. What is a USB hub?
**A:** A device that extends the bus by providing additional downstream ports. It repeats/routes packets between upstream (host side) and downstream (device side) ports. Handles speed translation (HS hub with FS/LS devices). Adds one tier to the topology. Maximum 5 hubs between host and device.

---

### Q15. What USB device classes do you know?
**A:** Key classes: **HID** (0x03) — keyboard/mouse/gamepad; **MSC** (0x08) — storage/flash drives; **CDC** (0x02) — serial/modem/network; **UVC** (0x0E) — cameras; **UAC** (0x01) — audio; **Hub** (0x09); **Vendor-specific** (0xFF) — ADB, proprietary protocols.

---

### Q16. What is HID class?
**A:** Human Interface Device — standardized protocol for input devices. Uses interrupt transfers with short polling intervals. Device describes its data format via HID Report Descriptor. Advantages: OS includes built-in drivers (driverless), standardized reports for keyboard/mouse/gamepad/touchscreen.

---

### Q17. What is mass storage class?
**A:** USB MSC allows access to block devices (flash drives, external HDDs). Uses bulk transfers. Two transport protocols: **BOT** (Bulk-Only Transport) — simple, sequential commands; **UASP** (USB Attached SCSI Protocol) — queued commands, faster, uses SS streams.

---

### Q18. What is the maximum number of devices on one USB bus?
**A:** 127 — because device addresses are 7 bits (0 reserved for default address during enumeration, 1-127 for assigned devices). In practice, bandwidth and hub tier limits are reached before 127 devices.

---

### Q19. What happens when you plug in a USB device?
**A:** (1) Hub detects D+/D- voltage change (pull-up), (2) Hub reports port status change to host, (3) Host enables port, (4) Host issues bus reset, (5) Host communicates with device at address 0, (6) Host assigns address (SET_ADDRESS), (7) Host reads descriptors, (8) Host loads appropriate class driver, (9) SET_CONFIGURATION activates the device.

---

### Q20. What is ADB and how does it use USB?
**A:** Android Debug Bridge — a debug/development tool that communicates over USB using vendor-specific class (0xFF) with two bulk endpoints (IN + OUT). The ADB protocol uses: CNXN (connect/auth), AUTH (RSA authentication), OPEN/WRITE/CLOSE (stream management). Requires USB debugging enabled in Developer Options and RSA key approval.

---

### Q21. What is MTP?
**A:** Media Transfer Protocol — file access protocol where the device exposes a file-level view (not raw blocks like MSC). Device retains control of filesystem. Advantages: Multiple apps can access files simultaneously, device can protect filesystem integrity. Used in Android for file transfer to PC.

---

### Q22. What is USB OTG?
**A:** On-The-Go — allows a device (like a phone) to act as either host or peripheral. In USB 2.0: uses ID pin (GND=host, floating=device). Replaced in USB 3.x by DRD (Dual-Role Device) with USB-C CC pin logic and USB PD data role swap.

---

### Q23. What is the CC pin in USB-C?
**A:** Configuration Channel — used for: (1) Detecting cable plug orientation, (2) Establishing DFP/UFP roles (host vs device via Rp/Rd), (3) Advertising available current (Rp value), (4) Carrying USB PD messages (BMC encoded), (5) Managing cable E-marker communication.

---

### Q24. What is a CRC and where is it used in USB?
**A:** Cyclic Redundancy Check — error detection:
- **CRC-5**: Protects token packets (ADDR + ENDP fields, 11 bits)
- **CRC-16**: Protects data packets (payload bytes)
- **CRC-32**: Used in SuperSpeed data packets
Receiver computes CRC on received data; mismatch = error → retransmit (except isochronous).

---

### Q25. What is USB suspend?
**A:** Power saving state. If no SOF/traffic for 3ms, device must enter suspend within 10ms. In suspend: device draws ≤2.5mA from VBUS. Resume: host drives K state for 20ms, or device sends remote wakeup signaling (if enabled). USB 3.x has additional U1/U2 states for faster low-power transitions.

---

# PART B: MID-LEVEL (25 Questions)

---

### Q26. Explain the complete enumeration of a USB 2.0 High Speed device.
**A:**
1. Device connects → D+ pull-up (Full Speed)
2. Hub detects, reports to host
3. Host enables port → reset (SE0 for 10-50ms)
4. During reset → **HS chirp handshake**: Device chirp K (1-7ms) → Host chirps K-J (3+ pairs) → Both switch to HS
5. Host: GET_DESCRIPTOR(Device, 64 bytes) at address 0 → reads bMaxPacketSize0
6. Optional: Second bus reset
7. Host: SET_ADDRESS(N) → device responds from 0, then switches to N
8. GET_DESCRIPTOR(Device, 18B) → full device descriptor (VID/PID/class)
9. GET_DESCRIPTOR(Config, 9B) → get wTotalLength
10. GET_DESCRIPTOR(Config, wTotalLength) → all interfaces/endpoints
11. GET_DESCRIPTOR(String × N) → manufacturer, product, serial
12. SET_CONFIGURATION(1) → device enters configured state
13. Class-specific initialization (e.g., HID: SET_IDLE, SET_PROTOCOL)

---

### Q27. Describe the DATA0/DATA1 toggle mechanism and its purpose.
**A:** Each endpoint maintains a data toggle bit (alternates DATA0/DATA1):
- Sender sends DATA0, receiver ACKs → both toggle to expect DATA1
- Sender sends DATA1, receiver ACKs → both toggle to expect DATA0
- **Purpose**: Detects duplicate packets (lost ACK scenario)
- If receiver gets unexpected toggle (e.g., DATA0 when expecting DATA1) → it's a retransmission → ACK but discard data
- Toggle reset on: SETUP token (always DATA0), SET_CONFIGURATION, CLEAR_FEATURE(ENDPOINT_HALT)

---

### Q28. Explain the three phases of a control transfer.
**A:**
1. **SETUP phase**: Host sends SETUP token + DATA0 (8-byte request structure with bmRequestType, bRequest, wValue, wIndex, wLength). Device MUST ACK (cannot NAK or STALL SETUP).
2. **DATA phase** (optional): One or more IN/OUT transactions carrying the requested data. Toggle starts at DATA1. Direction matches bmRequestType bit 7.
3. **STATUS phase**: Opposite direction to DATA phase. Zero-length DATA1 packet. Confirms transfer completion. Host→Device for reads, Device→Host for writes.

---

### Q29. How does USB 3.x SuperSpeed differ from USB 2.0 at the protocol level?
**A:** Major differences:
- **Separate physical bus**: SS has dedicated Tx/Rx pairs (not shared with USB 2.0 D+/D-)
- **No polling**: Instead of host polling, device sends ERDY/NRDY asynchronously
- **Burst mode**: Multiple packets before waiting for ACK (up to burst count)
- **Packet structure**: Route string for hub routing (not broadcast); larger headers
- **Flow control**: Credit-based (host tracks device buffer availability)
- **No SOF**: Replaced by ITP (Isochronous Timestamp Packet)
- **Link layer**: 8b/10b (Gen 1) or 128b/132b (Gen 2) encoding, CRC-32
- **Power states**: LFPS-based U1/U2/U3 (faster than USB 2.0 suspend)

---

### Q30. Explain USB Power Delivery negotiation.
**A:** PD runs on CC pin using BMC (Biphase Mark Coding):
1. Source sends **Source_Capabilities** (list of PDOs: voltage/current combinations)
2. Sink evaluates PDOs, sends **Request** for desired PDO (object position + current)
3. Source checks if it can supply → sends **Accept** (or Reject/Wait)
4. Source transitions VBUS to new voltage (must complete in tSrcTransition)
5. Source sends **PS_Ready** (power supply stable at new voltage)
6. Sink begins drawing power at negotiated level

Additional PD features: Power Role Swap, Data Role Swap, VCONN Swap, Hard Reset (when things go wrong).

---

### Q31. How does USB-C determine plug orientation?
**A:** The receptacle has two CC pins (CC1, CC2). The plug connects only ONE CC through (the other becomes VCONN for cable E-marker). When the DFP's Rp pull-up meets the UFP's Rd pull-down on a specific CC pin:
- If CC1 sees valid voltage → "normal" orientation → use TX1/RX1 SS pairs
- If CC2 sees valid voltage → "flipped" orientation → use TX2/RX2 SS pairs
- A MUX in the controller routes SS signals to the correct physical lane

---

### Q32. Describe the xHCI ring-based architecture.
**A:** xHCI uses three types of rings in host memory:
- **Command Ring** (host→xHC): Host writes command TRBs (Enable Slot, Address Device, Configure Endpoint); rings doorbell; xHC processes
- **Transfer Ring** (host→xHC, per endpoint): Host queues data TRBs; rings doorbell for that endpoint's slot; xHC DMAs data and executes transfer
- **Event Ring** (xHC→host): xHC posts completion events (Transfer Event, Command Completion, Port Status Change); triggers MSI-X interrupt

The cycle bit in each TRB indicates ownership (producer/consumer).

---

### Q33. What is a TRB and what does it contain?
**A:** Transfer Request Block — 16-byte structure in host memory:
- **Data Buffer Pointer** (64-bit): DMA address of data
- **Transfer Length** (17-bit): Bytes for this TRB
- **TRB Type** (6-bit): Normal, Setup Stage, Data Stage, Status Stage, Link, etc.
- **Control flags**: Cycle bit (ownership), IOC (interrupt on complete), Chain (scatter-gather), IDT (immediate data in TRB itself)

TRBs form linked lists (rings) with Link TRBs pointing back to start for circular operation.

---

### Q34. Explain the DWC3 controller's role in Qualcomm platforms.
**A:** DWC3 (Synopsys DesignWare USB 3.x) is the USB controller IP:
- **Host mode**: Exposes xHCI registers to Linux xhci-hcd driver
- **Device mode**: Custom register interface, handled by dwc3 gadget driver
- **DRD mode**: Can switch between host/device based on Type-C events
- **Qualcomm glue** (dwc3-qcom.c): Handles platform-specific init (clocks, power domains, interrupts, PHY control, wakeup IRQs)
- Connected to **QUSB2 PHY** (USB 2.0) and **QMP PHY** (USB 3.x) via UTMI+ and PIPE interfaces

---

### Q35. How does USB role switching work in Android/Linux?
**A:**
1. **Type-C Port Controller (TCPC)** detects CC state change (Rp/Rd)
2. TCPC reports to **TCPM** (Type-C Port Manager) framework in kernel
3. TCPM determines role: DFP=host or UFP=device
4. TCPM triggers **USB role switch** driver
5. DWC3 driver receives notification:
   - Host: Starts xHCI, enables VBUS supply
   - Device: Starts gadget, D+ pull-up
6. Alternatively: USB PD **Data Role Swap** message can trigger role change mid-connection
7. Android `UsbPortManager` reflects state via `UsbPort.getStatus()`

---

### Q36. What is the USB gadget framework in Linux?
**A:** The device-side (peripheral) USB stack:
- **UDC** (USB Device Controller) driver: Hardware abstraction (dwc3-gadget)
- **Composite framework**: Combines multiple USB functions into one device
- **Function drivers**: Individual class implementations (f_mtp, f_mass_storage, f_hid, f_ncm, f_fs for ADB)
- **ConfigFS**: Userspace interface to dynamically configure gadget (create functions, bind to config, set VID/PID, enable UDC)
- Android uses init.rc triggers to configure gadget based on `persist.sys.usb.config` property

---

### Q37. Explain isochronous transfers and where they're used in automotive.
**A:** Isochronous = constant-rate streaming with guaranteed bandwidth:
- No retransmission (data loss acceptable)
- Bandwidth reserved during SET_INTERFACE
- Bounded latency (delivered every frame/microframe)
- **Automotive uses**: USB Audio Class (external microphones for voice control), USB Video Class (cabin camera, dashcam), USB Audio output (external DAC for premium audio system)
- Key challenge: If bandwidth isn't available → SET_INTERFACE fails → must reduce quality/resolution

---

### Q38. What is USB LPM (Link Power Management)?
**A:** USB 2.0 LPM adds L1 state between L0 (active) and L2 (suspend):
- **L0**: Active
- **L1**: Sleep (50-200µs resume, device maintains some state)
- **L2**: Suspend (10ms+ resume, traditional USB suspend)
- Host sends LPM token → device ACKs → enters L1
- Advantage: Much faster resume than full suspend (µs vs ms)

USB 3.x has U0/U1/U2/U3 with similar graduated power savings, using LFPS for wake signaling.

---

### Q39. How does a USB protocol analyzer work?
**A:** A hardware device inserted between host and device (non-intrusive tap):
- Electrically passes all signals through (transparent)
- Captures and timestamps every packet on both USB 2.0 and SS buses
- Decodes: PID, address, endpoint, data, CRC, timing
- Software displays: Transaction view, packet view, transfer view
- Triggers: On specific PID, address, data pattern, error
- Examples: Beagle 480 (HS), Ellisys USB Explorer (SS), Wireshark+USBPcap (software-only)

---

### Q40. Describe the USB reset process and its effects.
**A:** Host drives SE0 (both D+/D- low) for 10-50ms:
- Device resets address to 0
- All endpoints reset to default state
- Data toggles reset
- Device returns to Default state
- Any in-progress transfers are aborted
- For HS devices: Chirp handshake re-negotiates speed
- For SS: Hot Reset via LTSSM (different mechanism, link-level)

---

### Q41. What is split transaction and why is it needed?
**A:** When a HS hub connects to a FS/LS device downstream:
- Hub can't translate speed on-the-fly (packet timing different)
- **Split transaction**: Host sends SPLIT token to hub → hub handles FS/LS transaction separately → hub buffers response → host collects later
- **Start-Split**: Host→Hub "start this FS/LS transaction"
- **Complete-Split**: Host→Hub "give me the result"
- Without splits, the slow device would block the entire HS bus

---

### Q42. Explain the difference between USB NCM, ECM, and RNDIS.
**A:** All provide IP networking over USB:
- **ECM** (Ethernet Control Model): Simple Ethernet frames in individual USB packets. Clean spec, good compatibility.
- **NCM** (Network Control Model): Aggregates multiple Ethernet frames per USB transfer (much higher throughput). Used by CarPlay and modern Android.
- **RNDIS** (Remote NDIS): Microsoft proprietary, wraps Ethernet frames with RNDIS headers. Used by Windows USB tethering. Less efficient than NCM.

Performance: NCM >> ECM ≈ RNDIS (NCM aggregation reduces USB overhead significantly)

---

### Q43. How does USB selective suspend work in Linux?
**A:**
- Per-device autosuspend: `echo auto > /sys/bus/usb/devices/X-Y/power/control`
- Delay: `echo 2000 > .../power/autosuspend_delay_ms` (2s idle before suspend)
- Driver must support it: `driver->supports_autosuspend = 1`
- Runtime PM framework tracks activity
- Remote wakeup: device signals host via resume signaling (if feature enabled)
- Interface-level suspend: Individual interfaces can suspend independently
- USB 3.x: Hardware LPM (U1/U2) is separate, lower-latency mechanism

---

### Q44. What is the SETUP packet format?
**A:** 8 bytes:
| Byte | Field | Bits | Description |
|------|-------|------|-------------|
| 0 | bmRequestType | D7=direction, D6:5=type (0=std,1=class,2=vendor), D4:0=recipient (0=device,1=interface,2=endpoint) |
| 1 | bRequest | Request code (0=GET_STATUS, 5=SET_ADDRESS, 6=GET_DESCRIPTOR, 9=SET_CONFIGURATION) |
| 2-3 | wValue | Request-specific parameter (e.g., descriptor type<<8 | index) |
| 4-5 | wIndex | Index (e.g., interface number, endpoint address) |
| 6-7 | wLength | Number of bytes to transfer in data phase (0 = no data phase) |

---

### Q45. What is an E-marker in USB-C cables?
**A:** An electronically-marked chip inside USB-C cables that identifies cable capabilities via communication over VCONN+CC:
- Reports: Max speed (USB 2.0/3.1/3.2), max current (3A/5A), max voltage (20V/48V)
- Required for: Cables carrying >3A, cables supporting >USB 3.2 Gen 1, USB4 cables
- Powered by VCONN (5V supplied by DFP on the unused CC pin)
- Host reads E-marker via USB PD Discover Identity message
- Prevents: Overcurrent damage with low-quality cables

---

# PART C: SENIOR / LEAD (15 Questions)

---

### Q46. Design the USB subsystem for an automotive digital cockpit (SA8295P).
**A:**
```
Requirements:
- Front USB-C: Phone connection (AA/CarPlay), charging (45W PD)
- Front USB-A: Media import (USB 3.0 flash drive)
- Rear 2× USB-C: Passenger charging (27W each)
- Internal: BT/WiFi module, camera, microphone
- Debug: ADB access for development

Architecture:
┌─────────────────────────────────────────────────────┐
│ DWC3 #0 (Host, USB 3.1 Gen 2)                      │
│  └── USB-C (front console)                          │
│      • TCPC (TPS65988): Handles CC, PD, Alt Mode    │
│      • PD Source: 5V/3A, 9V/3A, 15V/3A, 20V/2.25A │
│      • MUX: SS lane + DP Alt Mode (if display)      │
│      • Android Auto detection (AOA protocol)        │
│      • CarPlay detection (CDC-NCM class)            │
├─────────────────────────────────────────────────────┤
│ DWC3 #1 (Host, USB 3.0)                            │
│  └── USB-A (front glovebox)                         │
│      • Standard host, 5V/900mA                      │
│      • Mass storage + media player integration      │
├─────────────────────────────────────────────────────┤
│ DWC3 #2 (DRD, USB 3.1 Gen 2)                       │
│  └── USB-C (debug/engineering port)                 │
│      • Default: Device mode (ADB)                   │
│      • With OTG adapter: Host mode (USB peripherals)│
│      • PD Sink: Accept external power               │
├─────────────────────────────────────────────────────┤
│ DWC3 #3 (Host, USB 2.0 only)                       │
│  └── Internal hub → BT module + WiFi module         │
│      • Low power, always on                         │
│      • No external connector                        │
├─────────────────────────────────────────────────────┤
│ Separate USB 2.0 controller (Host)                  │
│  └── Internal: UVC camera (cabin) + UAC mic array   │
└─────────────────────────────────────────────────────┘

Key design decisions:
- Separate controller per role (no sharing = no conflicts)
- TCPC chip (TPS65988/FUSB302) for Type-C compliance
- TVS protection on all external ports (ESD + load dump)
- EMC: Common-mode chokes on all external cables
- Power: Each port has independent current limiting IC
```

---

### Q47. How would you debug a USB device that enumerates at Full Speed instead of High Speed?
**A:**
1. **Protocol analyzer capture**: Look for HS chirp handshake during reset
2. **Check chirp timing**: Device chirp K must be 1-7ms; if too short/long → host won't respond
3. **PHY investigation**:
   - QUSB2 PHY might not be generating proper 400mV chirp
   - Check `qcom,tune-usb2-amplitude` PHY tuning parameter
   - Read PHY status register: Is HS negotiation even attempted?
4. **Signal integrity**: Eye diagram at HS. If marginal → host rejects chirp
5. **Hub involvement**: Some hubs have HS chirp bugs. Test direct to host.
6. **Power issue**: If VBUS < 4.4V during chirp → device can't drive proper levels
7. **Kernel logs**: `dmesg | grep -i "high\|full\|speed"` — look for speed negotiation events
8. **Force HS**: Some controllers have register to force speed mode for debug

---

### Q48. Explain how Android Auto and CarPlay work over USB at the protocol level.
**A:**
**Android Auto:**
1. Phone enumerates normally (VID=Google or Samsung etc.)
2. Head unit sends control transfer: AOA identify (vendor request 0x33-0x39)
3. Sends AOA start (vendor request 0x35 with accessory strings)
4. Phone re-enumerates as Android Auto Accessory (VID=0x18D1, PID=0x2D00-0x2D05)
5. Head unit opens bulk endpoints → AA protocol starts
6. Video: H.264 1080p stream over bulk
7. Audio: PCM or AAC over bulk
8. Input: Touch events, button presses (protobuf)

**CarPlay:**
1. iPhone enumerates with Apple VID (0x05AC)
2. Head unit detects Apple device, checks for CarPlay support
3. Establishes **iAP2** (iPod Accessory Protocol 2) over bulk
4. iAP2 session negotiates CarPlay over **NCM** (USB networking)
5. CarPlay traffic flows over IP/NCM: AirPlay for video/audio, HID for input
6. Key difference: CarPlay is IP-based (networking), AA is custom bulk protocol

---

### Q49. How do you handle USB EMC issues in automotive?
**A:**
```
Common EMC problems:
1. Radiated emissions from USB cables (acting as antenna)
2. Conducted emissions on VBUS
3. Susceptibility to external RF (near antenna modules)

Solutions:
┌────────────────────────────────────────────────────────┐
│ Signal Lines (D+/D-, SS pairs):                        │
│   • Common-mode choke at connector entry point         │
│   • Controlled impedance (90Ω ±10%) PCB traces        │
│   • Short stub lengths (<5mm for HS, <2mm for SS)     │
│   • Shield cable to chassis GND at connector          │
├────────────────────────────────────────────────────────┤
│ VBUS:                                                  │
│   • LC filter (ferrite bead + capacitors)             │
│   • TVS diode (ESD + load dump protection)            │
│   • Current limiting IC (overcurrent protection)      │
│   • Meets CISPR 25 Class 5 emissions                  │
├────────────────────────────────────────────────────────┤
│ PCB Layout:                                            │
│   • USB PHY close to connector (<25mm)                │
│   • Dedicated ground plane under USB traces           │
│   • No high-speed digital crossing USB traces         │
│   • Separate analog and digital grounds, star point   │
├────────────────────────────────────────────────────────┤
│ Cable:                                                  │
│   • Shielded cables mandatory in vehicle              │
│   • Shield terminated at both ends (360° connection)  │
│   • Maximum length: 2m in vehicle (shorter preferred) │
│   • Automotive-rated cables (temperature, vibration)  │
└────────────────────────────────────────────────────────┘
```

---

### Q50. Describe the DWC3 event handling mechanism.
**A:**
1. DWC3 has an **event buffer** in system memory (DMA accessible)
2. Controller writes 32-bit event entries when things happen
3. Driver sets up event buffer address/size in DWC3 GEVNTADRHI/LO registers
4. **Device Events** (DEVT): Connect, Disconnect, Reset, Connection Done, Link State Change, Wakeup, Suspend, SOF
5. **Endpoint Events** (DEPEVT): XferComplete, XferNotReady, XferInProgress, Stream
6. Event buffer is circular; GEVNTCOUNT register tracks unprocessed count
7. On interrupt: `dwc3_thread_interrupt()` → `dwc3_process_event_buf()` → dispatches per type
8. After processing: write GEVNTCOUNT to acknowledge (decrements count)
9. Threaded IRQ model: Quick hardirq masks, threaded handler processes

---

### Q51. How would you implement USB compliance testing for an automotive product?
**A:**
```
Compliance Test Plan:

1. ELECTRICAL (USB-IF requirements):
   - HS eye diagram: ≥25% eye opening, ≤500ps jitter
   - SS eye diagram at 5/10 Gbps: Meets USB 3.x mask
   - Impedance: 90Ω ±15% (TDR measurement)
   - LFPS signaling: Amplitude, timing within spec
   - Power: Inrush current, VBUS ripple

2. PROTOCOL (USB20CV / USBCV tool):
   - Chapter 9 compliance (all standard requests)
   - Descriptor correctness (all mandatory fields)
   - Enumeration timing (within spec limits)
   - Suspend/resume behavior
   - Reset recovery

3. TYPE-C/PD (USB-C compliance):
   - CC resistor values (Rp/Rd accuracy)
   - VBUS timing (turn-on/off, transition)
   - PD message compliance (GoodCRC timing, etc.)
   - Attach/detach debounce
   - Cable E-marker communication

4. INTEROPERABILITY:
   - Test with 20+ popular phones/drives/accessories
   - Android Auto/CarPlay certification
   - Hot-plug stress testing (10000 cycles)
   - Multiple hubs + devices simultaneously

5. AUTOMOTIVE-SPECIFIC:
   - Temperature: -40°C to +85°C operation
   - Vibration: SAE J1455 or equivalent
   - EMC: CISPR 25 emissions + ISO 11452 immunity
   - Voltage transients: ISO 7637 (load dump, cranking)
   - ESD: ISO 10605 (±15kV air, ±8kV contact)
```

---

### Q52. Explain USB4 tunneling architecture.
**A:** USB4 multiplexes multiple protocols over one physical link:
- **USB 3.x tunnel**: Carries USB SuperSpeed data (bulk/interrupt/isoch/control)
- **DisplayPort tunnel**: Carries DP video streams (multiple lanes configurable)
- **PCIe tunnel**: Carries PCIe TLPs (for NVMe, eGPU, etc.)
- **Host Interface tunnel**: Internal management

**How it works:**
1. USB4 Connection Manager (CM) establishes tunnels
2. Each tunnel gets allocated bandwidth (dynamic, can change)
3. Transport layer encapsulates protocol-specific packets
4. Link layer provides flow control and reliability
5. Physical layer transmits all tunnels over same 2×2 lanes

**Bandwidth allocation example (40 Gbps link):**
- DP tunnel: 25.92 Gbps (4K@60Hz HDR)
- USB 3.x tunnel: 10 Gbps
- PCIe tunnel: 0 Gbps (not in use)
- Overhead: ~4 Gbps

---

### Q53. How does USB suspend/resume interact with Android power management?
**A:**
```
Android Power Path for USB:

1. Screen off → Android enters Doze/Deep Sleep:
   - PowerManagerService → USB device suspend
   - USB host ports: Suspend downstream devices
   - USB device mode (ADB): Enter USB suspend (stop SOF)
   
2. Kernel USB autosuspend:
   - Runtime PM suspends idle devices after delay
   - HCD stops SOF → device detects lack of SOF → device suspends
   - PHY enters low-power mode (QUSB2/QMP power down PLL)
   
3. Remote wakeup:
   - Device (e.g., phone) initiates resume signaling
   - Hub detects resume → reports to host
   - Host generates resume downstream
   - System wakes from suspend (wakeup IRQ through GIC → PowerManager)
   
4. Complications in automotive:
   - Android Auto must stay active during phone charge
   - Camera (UVC) must work regardless of screen state
   - ADB must be available for diagnostics
   - Solution: Per-port wakeup sources, keep specific ports active
   
5. USB in Android Automotive suspend:
   - AAOS has different power policies (cabin off ≠ full suspend)
   - USB ports may need to stay powered for phone charging
   - Configured via power.policy.xml and USB HAL
```

---

### Q54. Design the USB error handling strategy for a production automotive system.
**A:**
```
Error Types and Handling:

1. ENUMERATION FAILURE:
   Action: Retry 3× with increasing reset duration
   Fallback: Try FS if HS fails, disable port if all fail
   User feedback: "Device not supported" notification
   Logging: Record VID/PID, error type, retry count

2. TRANSFER ERRORS (CRC, timeout):
   Action: Automatic retransmission (hardware/driver handles)
   For bulk: Up to 3 retries per URB, then report error to class driver
   For isoch: No retry (skip frame), count error rate
   Threshold: >1% error rate → signal quality issue alert

3. OVERCURRENT:
   Action: Immediately disable port (hardware protection IC)
   Recovery: Wait 5s, re-enable, re-enumerate
   Persistent: Disable port, flag error, notify user
   Safety: Never exceed connector/cable current rating

4. HOT-PLUG RACE CONDITIONS:
   Action: Debounce (100ms minimum after state change)
   Guard: Reference counting on all device structures
   Kernel: Cancel all pending URBs on disconnect
   Userspace: Handle ENODEV gracefully, don't cache handles

5. DISCONNECT DURING TRANSFER:
   Action: URB completes with -ENODEV/-ESHUTDOWN
   Class driver: Cleanup, close files, release resources
   User: "Device disconnected" notification
   Recovery: Full re-enumeration on re-attach

6. SUSPEND/RESUME FAILURE:
   Action: If resume fails → reset port → re-enumerate
   If device doesn't suspend → mark as non-suspendable
   Android: UsbService handles re-binding on resume failure
```

---

### Q55. Explain the USB PHY tuning process for a new hardware design.
**A:**
```
PHY Tuning Methodology (QUSB2 / QMP):

1. INITIAL MEASUREMENT:
   - Eye diagram at HS (480 Mbps) with reference cable
   - Eye diagram at SS (5/10 Gbps) with reference cable
   - Compare to USB-IF spec mask

2. TX TUNING (drive strength, pre-emphasis):
   - Adjust qcom,tune-usb2-amplitude (signal level)
   - Adjust qcom,tune-usb2-preemphasis (compensate cable loss)
   - For SS: TX de-emphasis, TX amplitude per lane
   - Goal: Center eye in compliance mask

3. RX TUNING (equalizer, sensitivity):
   - Adjust squelch detect threshold (qcom,tune-usb2-disc-thres)
   - For SS: CTLE (continuous time linear equalizer) settings
   - For SS: DFE (decision feedback equalizer) taps
   - Goal: Reliable detection at worst-case cable + connector

4. TIMING (rise/fall, crossover):
   - Adjust qcom,tune-usb2-rise-fall (edge rate)
   - Adjust qcom,tune-usb2-crossover (voltage level at transition)
   - Balance: Fast edges = better eye but more EMI

5. PLL TUNING:
   - Reference clock accuracy (±500ppm for USB 2.0)
   - Jitter: Must meet spec (±75ps for HS)
   - Lock time: Important for resume latency

6. VALIDATION:
   - Re-measure eye diagram with tuned settings
   - Test across temperature range (-40°C to +85°C)
   - Test with 5+ different cables (good quality + worst-case)
   - Test with compliance test fixtures
   - Interoperability: Test with 20+ devices
```

---

### Q56. How do you debug a USB disconnect that happens during large file transfers?
**A:**
1. **Capture**: Enable USB kernel traces: `echo 1 > /sys/kernel/debug/tracing/events/xhci-hcd/enable`
2. **Check dmesg**: Look for error patterns:
   - "cannot submit urb" → driver issue
   - "babble" → device sending too much data
   - "transaction error" → CRC/timeout
   - "port reset" → hub resetting port
3. **VBUS measurement**: Use scope on VBUS during transfer — voltage dip under load?
4. **Signal integrity**: Eye diagram during transfer (high current = more noise)
5. **Thermal**: PHY temperature during sustained transfer (thermal shutdown?)
6. **Power management**: Check if autosuspend is interfering (`power/control = on`)
7. **xHCI registers**: Read port status after disconnect — what was the error?
8. **Root cause tree**:
   - Cable: Try known-good cable → if fixes → cable impedance/length issue
   - Power: Measure VBUS under full load → if drops → power delivery issue
   - EMI: Does it correlate with other activity (display, WiFi)?
   - Thermal: Cool the board → if fixes → thermal throttling/shutdown
   - Software: Increase URB timeout, check for memory pressure (allocation failures)

---

# PART D: STAFF / ARCHITECT (5 Questions)

---

### Q57. Architect the complete USB subsystem for next-gen AAOS cockpit supporting USB4.
**A:**
```
Requirements:
- USB4 40Gbps for external displays + data
- USB PD 3.1 (240W) for laptop-like devices
- Backward compatible: USB 2.0/3.x devices
- Multiple zones: Driver, Passenger, Rear
- Security: No unauthorized data transfer
- Reliability: Automotive-grade (-40°C to +105°C)

Architecture:
┌─────────────────────────────────────────────────────────────────┐
│                 Next-Gen Cockpit SoC                              │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ USB4 Controller (Intel/Synopsys USB4 IP)                    ││
│  │  • 2× USB4 ports (Type-C)                                  ││
│  │  • Tunnels: USB 3.2 + DP 2.0 + PCIe Gen 4                 ││
│  │  • Connection Manager (CM) in firmware                      ││
│  │  • IOMMU protection for PCIe tunnels                       ││
│  └───────────┬─────────────────────────────┬───────────────────┘│
│              │                             │                     │
│  ┌───────────▼──────┐          ┌──────────▼──────────┐         │
│  │ USB4 Port A      │          │ USB4 Port B          │         │
│  │ (Driver Console) │          │ (Passenger)          │         │
│  │ • DP Alt Mode    │          │ • USB 3.2 data       │         │
│  │   (2nd display)  │          │ • PD 100W charging   │         │
│  │ • USB 3.2 Gen 2  │          │ • Dock support       │         │
│  │ • PD 45W charge  │          │                      │         │
│  └──────────────────┘          └─────────────────────┘         │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ USB 3.2 Controller (DWC3)                                   ││
│  │  • 4× USB 3.2 Gen 1 ports (backward compat)               ││
│  └───┬─────────┬─────────┬──────────┬─────────────────────────┘│
│      │         │         │          │                           │
│  ┌───▼───┐ ┌──▼───┐ ┌──▼───┐ ┌───▼───────┐                   │
│  │USB-C  │ │USB-A │ │USB-A │ │Internal   │                   │
│  │(Debug)│ │(Rear)│ │(Rear)│ │Hub (BT/   │                   │
│  │DRD    │ │Host  │ │Host  │ │WiFi/Cam)  │                   │
│  └───────┘ └──────┘ └──────┘ └───────────┘                   │
│                                                                   │
│  Security Layer:                                                  │
│  • USB device whitelist (VID/PID policy)                         │
│  • SELinux MAC on USB device nodes                               │
│  • USB-C Authentication for cables/chargers                      │
│  • IOMMU isolation for USB4 PCIe tunnels                        │
│  • Disable USB data in "valet mode"                              │
│                                                                   │
│  Power Architecture:                                              │
│  • Per-port load switches with OCP                               │
│  • PD controller per USB-C port (dedicated TCPC IC)             │
│  • Vehicle power integration: Start/stop resilient               │
│  • Battery-backed USB during cranking (maintain connection)       │
└─────────────────────────────────────────────────────────────────┘
```

---

### Q58. Design a USB security framework for automotive that prevents BadUSB attacks while maintaining usability.
**A:**
```
Threat Model:
- BadUSB: Reprogrammed device acts as HID (keystroke injection)
- Data exfiltration: Malicious device reads sensitive storage
- Malware delivery: Infected USB drive auto-executes
- Power attack: Malicious charger + data access (juice jacking)

Multi-Layer Defense:

Layer 1: Hardware
┌─────────────────────────────────────────────────────┐
│ • USB-C Authentication (crypto certificates)         │
│ • Charge-only mode hardware switch (disable D+/D-)  │
│ • Physical port lockout (production vehicles)        │
│ • IOMMU for all USB DMA (prevent direct memory access)│
└─────────────────────────────────────────────────────┘

Layer 2: Kernel
┌─────────────────────────────────────────────────────┐
│ • USBGuard policy engine:                            │
│   - Whitelist known VID/PID combinations            │
│   - Block HID class on data ports                   │
│   - Allow only MSC class on media ports             │
│ • SELinux: USB device node access control            │
│ • Limit interfaces: MAX_INTERFACES per device       │
│ • Rate-limit enumeration (prevent DoS)              │
└─────────────────────────────────────────────────────┘

Layer 3: Framework (Android)
┌─────────────────────────────────────────────────────┐
│ • UsbDeviceManager policy:                           │
│   - User confirmation for new device classes        │
│   - "Trust this device?" dialog (like ADB auth)     │
│ • Drive scanning: AV scan before mount              │
│ • MTP: Read-only unless explicitly authorized       │
│ • Android Auto: Only certified devices              │
└─────────────────────────────────────────────────────┘

Layer 4: User Experience
┌─────────────────────────────────────────────────────┐
│ • "Charge Only" as default USB mode                  │
│ • Explicit user action to enable data               │
│ • Visual indicator when USB data active             │
│ • Valet Mode: All USB data disabled                 │
│ • Fleet Management: OTA policy updates              │
└─────────────────────────────────────────────────────┘

Implementation Priority:
1. Charge-only default (immediate, zero risk)
2. USBGuard class filtering (blocks 90% of attacks)
3. USB-C Auth (long-term, ecosystem maturity needed)
4. IOMMU (silicon support required)
```

---

### Q59. How would you architect USB power management for an AAOS system that must charge phones even when the vehicle is "off"?
**A:**
```
Challenge: Vehicle "off" means infotainment sleeps, but users expect
phone charging to continue. Must balance: battery drain vs. usability.

Architecture:
┌─────────────────────────────────────────────────────────────────┐
│ Vehicle States vs USB Power:                                     │
│                                                                   │
│ ┌─────────────┬────────────┬────────────┬──────────────────────┐│
│ │ Vehicle     │ SoC State  │ USB State  │ Power Source         ││
│ │ State       │            │            │                      ││
│ ├─────────────┼────────────┼────────────┼──────────────────────┤│
│ │ Running     │ Full on    │ Full USB   │ Alternator           ││
│ │             │            │ (data+pwr) │                      ││
│ ├─────────────┼────────────┼────────────┼──────────────────────┤│
│ │ ACC (key on)│ On         │ Full USB   │ Battery              ││
│ ├─────────────┼────────────┼────────────┼──────────────────────┤│
│ │ Parked      │ Suspended  │ Charge only│ Battery (timer-      ││
│ │ (doors      │ (deep      │ (VBUS on,  │ limited: max 4hr)   ││
│ │  locked)    │  sleep)    │  no data)  │                      ││
│ ├─────────────┼────────────┼────────────┼──────────────────────┤│
│ │ Long park   │ Off        │ Off        │ Battery protection   ││
│ │ (>4 hours)  │            │ (all off)  │ (prevent drain)      ││
│ └─────────────┴────────────┴────────────┴──────────────────────┘│
│                                                                   │
│ Hardware Design:                                                  │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │           Always-On Domain                                   │ │
│ │  ┌──────────────────────────────────────────────────────┐   │ │
│ │  │ Micro-controller (Cortex-M0, <1mW)                   │   │ │
│ │  │  • Monitors vehicle state (CAN wakeup)               │   │ │
│ │  │  • Controls VBUS switches per port                    │   │ │
│ │  │  • Timer: Auto-off after 4 hours                     │   │ │
│ │  │  • Battery voltage monitor (cutoff at 11.5V)         │   │ │
│ │  └──────────────────────────────────────────────────────┘   │ │
│ │                                                              │ │
│ │  ┌──────────┐    ┌──────────┐    ┌──────────┐              │ │
│ │  │VBUS Sw 1 │    │VBUS Sw 2 │    │VBUS Sw 3 │              │ │
│ │  │(Port A)  │    │(Port B)  │    │(Rear)    │              │ │
│ │  └─────┬────┘    └─────┬────┘    └─────┬────┘              │ │
│ │        │               │               │                    │ │
│ └────────┼───────────────┼───────────────┼────────────────────┘ │
│          │               │               │                      │
│  ════════▼═══════════════▼═══════════════▼═══ USB-C Ports ════  │
│                                                                   │
│ Software (when SoC is on):                                       │
│  • UsbPowerPolicy service monitors vehicle state                 │
│  • On suspend: Negotiate PD to lower power (5V/1.5A vs 20V/3A) │
│  • On resume: Re-negotiate full power                            │
│  • Battery protection: MCU shuts off if V_bat < 11.5V           │
│  • Wake source: Phone removal triggers SoC wake (for UX)        │
│                                                                   │
│ Battery Budget:                                                   │
│  • Phone charging: ~7.5W average (5V/1.5A trickle in sleep)     │
│  • Always-on MCU: <1mW                                          │
│  • Total drain: ~8W for 4 hours = 32Wh                          │
│  • 60Ah battery at 12V = 720Wh → 4.4% drain (acceptable)       │
└─────────────────────────────────────────────────────────────────┘
```

---

### Q60. Design a zero-downtime USB firmware update system for automotive ECUs.
**A:**
```
Requirements:
- Update USB controller firmware (PHY firmware, TCPC firmware)
- No user-visible interruption during OTA update
- Rollback capability if update fails
- Secure (signed firmware only)

Architecture:
┌─────────────────────────────────────────────────────────────────┐
│ Dual-Bank USB Firmware Update                                    │
│                                                                   │
│ ┌─────────────────────┐    ┌─────────────────────┐             │
│ │ Bank A (Active)      │    │ Bank B (Staging)     │             │
│ │ • TCPC FW v1.2      │    │ • TCPC FW v1.3      │ ← New      │
│ │ • PHY FW v2.0       │    │ • PHY FW v2.1       │             │
│ │ • DWC3 params       │    │ • DWC3 params       │             │
│ └─────────┬───────────┘    └──────────┬──────────┘             │
│           │                            │                         │
│           ▼                            │                         │
│  ┌────────────────┐                   │                         │
│  │ Boot Selector  │ ← Switch after ───┘                         │
│  │ (OTP pointer)  │    validation                               │
│  └────────────────┘                                              │
│                                                                   │
│ Update Flow:                                                      │
│ 1. Download new firmware to Bank B (background, no disruption)  │
│ 2. Verify signature (OEM key) and CRC                           │
│ 3. Run compatibility check (DT bindings match)                  │
│ 4. Wait for "safe moment" (no active USB transfer)              │
│ 5. Quick switch: Update boot pointer A→B                        │
│ 6. Reset USB subsystem (DWC3 soft reset)                        │
│ 7. Re-enumerate devices (reconnect takes ~200ms)                │
│ 8. If failure: Revert pointer B→A, reset again                  │
│                                                                   │
│ TCPC Firmware Update (in-field):                                 │
│ • TCPC has dual-bank internal flash                              │
│ • I2C/SPI update while running from other bank                  │
│ • Switch + reset takes <50ms (PD session lost briefly)          │
│ • Phone maintains charge (VBUS stays up via hardware)            │
│                                                                   │
│ Validation Criteria (post-update):                               │
│ • TCPC responds to I2C with correct FW version                  │
│ • PHY passes loopback test                                       │
│ • DWC3 enumerates test device within 5 seconds                  │
│ • PD negotiation succeeds with reference charger                │
│ • If ANY check fails → automatic rollback within 30 seconds     │
│                                                                   │
│ Security:                                                         │
│ • Firmware signed with OEM RSA-4096 key                         │
│ • Public key hash fused in SoC (root of trust)                  │
│ • Anti-rollback counter (prevent downgrade attacks)              │
│ • Encrypted in transit (TLS from cloud to device)               │
└─────────────────────────────────────────────────────────────────┘
```

---

# PART E: QUICK-FIRE (20 Questions)

| # | Question | Answer |
|---|----------|--------|
| F1 | USB stands for? | Universal Serial Bus |
| F2 | USB-IF stands for? | USB Implementers Forum |
| F3 | USB 2.0 High Speed rate? | 480 Mbps |
| F4 | USB 3.0 SuperSpeed rate? | 5 Gbps |
| F5 | USB4 max speed? | 80 Gbps (v2) or 120 Gbps (asymmetric) |
| F6 | Max devices per controller? | 127 (7-bit address) |
| F7 | Max hub tiers? | 7 |
| F8 | USB 2.0 encoding? | NRZI + bit stuffing |
| F9 | USB 3.0 encoding? | 8b/10b |
| F10 | USB-C pin count? | 24 |
| F11 | CC pin purpose? | Configuration Channel (orientation, role, PD) |
| F12 | USB PD max power? | 240W (48V × 5A EPR) |
| F13 | SOF interval (FS)? | 1 ms |
| F14 | Microframe interval (HS)? | 125 µs |
| F15 | CRC on token packets? | CRC-5 |
| F16 | CRC on data packets? | CRC-16 (USB 2.0), CRC-32 (SS) |
| F17 | Default device address? | 0 |
| F18 | EP0 transfer type? | Control |
| F19 | xHCI stands for? | eXtensible Host Controller Interface |
| F20 | DWC3 stands for? | DesignWare Core USB 3.0 (Synopsys) |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
