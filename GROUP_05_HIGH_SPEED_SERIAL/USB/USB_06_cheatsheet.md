# USB PROTOCOL — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: USB (Universal Serial Bus) | Document: 06 of 08
# Format: Tables, one-liners, register maps, command references
# ════════════════════════════════════════════════════════════════════

---

## 1. SPEED GRADES QUICK REFERENCE

| Generation | Marketing Name | Speed | Encoding | Lanes | Connector |
|---|---|---|---|---|---|
| USB 1.0 | Low Speed | 1.5 Mbps | NRZI | 1 | A/B |
| USB 1.1 | Full Speed | 12 Mbps | NRZI | 1 | A/B |
| USB 2.0 | High Speed | 480 Mbps | NRZI | 1 | A/B/C |
| USB 3.2 Gen 1 | SuperSpeed | 5 Gbps | 8b/10b | 1 | A/B/C |
| USB 3.2 Gen 2 | SuperSpeed+ | 10 Gbps | 128b/132b | 1 | A/B/C |
| USB 3.2 Gen 2×2 | SuperSpeed+ 20G | 20 Gbps | 128b/132b | 2 | C only |
| USB4 Gen 3×2 | USB4 40G | 40 Gbps | 128b/132b+tunnel | 2 | C only |
| USB4 v2 | USB4 80G | 80 Gbps | PAM-3 | 2 | C only |

---

## 2. USB TYPE-C PINOUT

```
        A12 A11 A10 A9  A8  A7  A6  A5  A4  A3  A2  A1
        GND RX2+RX2-VBS SBU1 D-  D+  CC1 VBS TX1-TX1+GND
        
        B1  B2  B3  B4  B5  B6  B7  B8  B9  B10 B11 B12
        GND TX2+TX2-VBS CC2  D+  D-  SBU2VBS RX1-RX1+GND
```

| Pin Function | Count | Notes |
|---|---|---|
| GND | 4 | Power return |
| VBUS | 4 | Up to 48V/5A (240W) |
| CC | 2 | Orientation, roles, PD messaging |
| D+/D- | 2 pairs | Only one pair active (orientation) |
| TX/RX | 4 pairs | SuperSpeed differential (2 used at a time for ≤Gen2) |
| SBU | 2 | Sideband (Alt Mode: DP AUX, audio) |

---

## 3. PID TABLE (Packet Identifier)

| PID Name | Value | Type | Description |
|---|---|---|---|
| OUT | 0xE1 | Token | Host→Device address/EP |
| IN | 0x69 | Token | Host requests data from device |
| SOF | 0xA5 | Token | Start of frame (frame number) |
| SETUP | 0x2D | Token | Host→Device control setup |
| DATA0 | 0xC3 | Data | Even data packet |
| DATA1 | 0x4B | Data | Odd data packet |
| DATA2 | 0x87 | Data | High-bandwidth isoch (HS) |
| MDATA | 0x0F | Data | Split transaction data |
| ACK | 0xD2 | Handshake | Transfer accepted |
| NAK | 0x5A | Handshake | Device busy (retry) |
| STALL | 0x1E | Handshake | Error / not supported |
| NYET | 0x96 | Handshake | Not yet (HS split) |
| PRE/ERR | 0x3C | Special | Preamble / Error |
| SPLIT | 0x78 | Special | High-speed split token |
| PING | 0xB4 | Special | HS flow control check |

---

## 4. STANDARD DEVICE REQUESTS

| bRequest | Value | Name | wValue | wIndex | Description |
|---|---|---|---|---|---|
| 0 | GET_STATUS | 0 | 0/IF/EP | Get device/IF/EP status (2 bytes) |
| 1 | CLEAR_FEATURE | Feature | 0/IF/EP | Clear feature (e.g., halt) |
| 3 | SET_FEATURE | Feature | 0/IF/EP | Set feature (e.g., remote wakeup) |
| 5 | SET_ADDRESS | Address | 0 | Assign device address (1-127) |
| 6 | GET_DESCRIPTOR | Type<<8\|Idx | LangID/0 | Get descriptor |
| 7 | SET_DESCRIPTOR | Type<<8\|Idx | LangID/0 | Set descriptor (rarely used) |
| 8 | GET_CONFIGURATION | 0 | 0 | Get current config value |
| 9 | SET_CONFIGURATION | ConfigValue | 0 | Set/activate configuration |
| 10 | GET_INTERFACE | 0 | Interface | Get alt setting |
| 11 | SET_INTERFACE | AltSetting | Interface | Select alt setting |
| 12 | SYNCH_FRAME | 0 | Endpoint | Get sync frame number |

---

## 5. DESCRIPTOR TYPE CODES

| Type | Value | Size | Key Fields |
|---|---|---|---|
| DEVICE | 1 | 18B | bcdUSB, VID, PID, bMaxPacketSize0, bNumConfigs |
| CONFIGURATION | 2 | 9B+ | wTotalLength, bNumInterfaces, bMaxPower |
| STRING | 3 | var | bString (UTF-16LE) |
| INTERFACE | 4 | 9B | bInterfaceNumber, bInterfaceClass/Subclass/Protocol |
| ENDPOINT | 5 | 7B | bEndpointAddress, bmAttributes, wMaxPacketSize, bInterval |
| DEVICE_QUALIFIER | 6 | 10B | Other-speed device info (USB 2.0) |
| OTHER_SPEED_CONFIG | 7 | 9B | Config descriptor for other speed |
| INTERFACE_POWER | 8 | var | Power management (rarely used) |
| OTG | 9 | 5B | OTG capabilities |
| BOS | 15 | 5B+ | Binary Object Store (USB 3.x capabilities) |
| SS_EP_COMPANION | 48 | 6B | MaxBurst, MaxStreams (SS endpoint addon) |

---

## 6. USB CLASS CODES

| Class | Code | Subclass | Protocol | Examples |
|---|---|---|---|---|
| Audio | 0x01 | 1=Control, 2=Stream | — | Speakers, microphones, DACs |
| CDC | 0x02 | 2=ACM, 6=ETH, 13=NCM | — | Serial ports, modems, networking |
| HID | 0x03 | 0=None, 1=Boot | 1=Keyboard, 2=Mouse | Input devices |
| Physical | 0x05 | — | — | Force feedback |
| Image | 0x06 | 1=Still | 1=PTP | Cameras, scanners |
| Printer | 0x07 | 1=Unidirectional | — | Printers |
| Mass Storage | 0x08 | 6=SCSI | 0x50=BOT, 0x62=UASP | Flash drives, HDDs |
| Hub | 0x09 | 0=FS, 1=HS | 1=ST, 2=MT | USB hubs |
| CDC-Data | 0x0A | — | — | CDC data channel |
| Smart Card | 0x0B | — | — | Card readers |
| Video | 0x0E | 1=Control, 2=Stream | — | Webcams, capture |
| Audio/Video | 0x10 | — | — | A/V streaming |
| Wireless | 0xE0 | 1=RF | 1=BT | Bluetooth adapters |
| Vendor | 0xFF | — | — | ADB, custom protocols |

---

## 7. ENDPOINT DESCRIPTOR FIELDS CHEATSHEET

```
bEndpointAddress (1 byte):
  Bit 7:   Direction (0=OUT, 1=IN)
  Bits 3-0: Endpoint number (0-15)
  Example: 0x81 = EP1 IN, 0x02 = EP2 OUT

bmAttributes (1 byte):
  Bits 1-0: Transfer type
    00 = Control
    01 = Isochronous
    10 = Bulk
    11 = Interrupt
  Bits 3-2: Sync type (isoch only)
    00 = No sync, 01 = Async, 10 = Adaptive, 11 = Sync
  Bits 5-4: Usage type (isoch only)
    00 = Data, 01 = Feedback, 10 = Implicit feedback

wMaxPacketSize (2 bytes):
  Bits 10-0: Max packet size in bytes
  Bits 12-11: Additional transactions per microframe (HS isoch/interrupt)
    00 = 1 (none), 01 = 2 additional, 10 = 3 additional
```

---

## 8. MAX PACKET SIZES

| Speed | Control | Bulk | Interrupt | Isochronous |
|---|---|---|---|---|
| Low Speed | 8 | — | 8 | — |
| Full Speed | 8/16/32/64 | 8/16/32/64 | ≤64 | ≤1023 |
| High Speed | 64 | 512 | ≤1024 (×3) | ≤1024 (×3) |
| SuperSpeed | 512 | 1024 | 1024 | 1024 (×48 burst) |

---

## 9. USB-C CC RESISTANCE VALUES

| Role | Resistor | Value | Current Advertised |
|---|---|---|---|
| DFP (host) Rp | Pull-up to VCONN/3.3V | 56kΩ | Default USB (500/900mA) |
| DFP (host) Rp | Pull-up | 22kΩ | 1.5A |
| DFP (host) Rp | Pull-up | 10kΩ | 3.0A |
| UFP (device) Rd | Pull-down to GND | 5.1kΩ | — |
| Audio Adapter Ra | Pull-down to GND | ≤1kΩ | Audio accessory mode |
| No connection | — | Open/floating | Unconnected |

---

## 10. USB PD MESSAGE TYPES

| Category | Message | Direction | Purpose |
|---|---|---|---|
| Control | GoodCRC | Both | Acknowledge receipt |
| Control | Accept | Src/Snk | Accept request |
| Control | Reject | Src/Snk | Reject request |
| Control | PS_Ready | Source | VBUS at new voltage |
| Control | Soft_Reset | Both | Reset PD protocol layer |
| Control | DR_Swap | Both | Data role swap request |
| Control | PR_Swap | Both | Power role swap request |
| Control | VCONN_Swap | Both | VCONN supply swap |
| Data | Source_Capabilities | Source | Available PDOs |
| Data | Request | Sink | Requested PDO + current |
| Data | Sink_Capabilities | Sink | Power requirements |
| Data | Vendor_Defined | Both | Vendor messages (Alt Mode) |

---

## 11. LINUX USB COMMANDS

```bash
# List devices
lsusb                           # Brief list (bus, device, VID:PID)
lsusb -v                        # Verbose (all descriptors)
lsusb -t                        # Tree topology view
lsusb -d VID:PID               # Filter by VID:PID
usb-devices                     # Detailed human-readable list

# sysfs exploration
ls /sys/bus/usb/devices/        # All USB devices
cat /sys/bus/usb/devices/1-1/speed          # Speed (12, 480, 5000)
cat /sys/bus/usb/devices/1-1/manufacturer   # Manufacturer string
cat /sys/bus/usb/devices/1-1/product        # Product string
cat /sys/bus/usb/devices/1-1/serial         # Serial number
cat /sys/bus/usb/devices/1-1/idVendor       # VID
cat /sys/bus/usb/devices/1-1/idProduct      # PID
cat /sys/bus/usb/devices/1-1/bConfigurationValue  # Active config

# Power management
cat /sys/bus/usb/devices/1-1/power/runtime_status   # active/suspended
echo auto > /sys/bus/usb/devices/1-1/power/control  # Enable autosuspend
echo on > /sys/bus/usb/devices/1-1/power/control    # Disable autosuspend
echo 2000 > /sys/bus/usb/devices/1-1/power/autosuspend_delay_ms

# USB monitor (packet capture)
modprobe usbmon
cat /sys/kernel/debug/usb/usbmon/1u        # Bus 1 text output
# Or use Wireshark with usbmon interface

# Device bind/unbind
echo "1-1:1.0" > /sys/bus/usb/drivers/usb-storage/unbind
echo "1-1:1.0" > /sys/bus/usb/drivers/usb-storage/bind

# Reset device
echo 0 > /sys/bus/usb/devices/1-1/authorized && echo 1 > /sys/bus/usb/devices/1-1/authorized

# USB gadget (ConfigFS)
GADGET=/sys/kernel/config/usb_gadget/g1
mkdir -p $GADGET
echo 0x18D1 > $GADGET/idVendor
echo 0x4E11 > $GADGET/idProduct
mkdir -p $GADGET/strings/0x409
echo "Google" > $GADGET/strings/0x409/manufacturer
echo "Pixel" > $GADGET/strings/0x409/product
mkdir -p $GADGET/functions/mtp.gs0
mkdir -p $GADGET/configs/c.1/strings/0x409
echo "MTP" > $GADGET/configs/c.1/strings/0x409/configuration
ln -s $GADGET/functions/mtp.gs0 $GADGET/configs/c.1/
echo "a600000.dwc3" > $GADGET/UDC    # Enable gadget
```

---

## 12. DWC3 KEY REGISTERS

| Register | Offset | Description |
|---|---|---|
| GSBUSCFG0 | 0xC100 | Global SoC Bus Configuration |
| GSBUSCFG1 | 0xC104 | Bus Configuration (burst length) |
| GTXTHRCFG | 0xC108 | TX Threshold Config |
| GRXTHRCFG | 0xC10C | RX Threshold Config |
| GCTL | 0xC110 | Global Control (PrtCapDir, ScaleDown) |
| GSTS | 0xC118 | Global Status |
| GUSB2PHYCFG | 0xC200 | USB 2.0 PHY Config (UTMI) |
| GUSB3PIPECTL | 0xC2C0 | USB 3.x PIPE Control |
| GEVNTADRLO(n) | 0xC400+16n | Event Buffer Address Low |
| GEVNTADRHI(n) | 0xC404+16n | Event Buffer Address High |
| GEVNTSIZ(n) | 0xC408+16n | Event Buffer Size |
| GEVNTCOUNT(n) | 0xC40C+16n | Event Count (unprocessed) |
| DCFG | 0xC700 | Device Configuration (speed, address) |
| DCTL | 0xC704 | Device Control (run/stop, test modes) |
| DSTS | 0xC70C | Device Status (speed, connect, suspend) |
| DALEPENA | 0xC720 | Device Active Endpoint Enable |
| DEPCMDPAR2(n) | 0xC800+16n | Endpoint Command Parameter 2 |
| DEPCMDPAR1(n) | 0xC804+16n | Endpoint Command Parameter 1 |
| DEPCMDPAR0(n) | 0xC808+16n | Endpoint Command Parameter 0 |
| DEPCMD(n) | 0xC80C+16n | Endpoint Command |

**GCTL.PrtCapDir**: 01=Host, 10=Device, 11=OTG  
**DCTL.RunStop**: Set to start device controller  
**DCFG.Speed**: 0=HS, 1=FS, 4=SS, 5=SS+ 

---

## 13. DWC3 ENDPOINT COMMANDS

| Command | Value | Description |
|---|---|---|
| DEPCFG | 1 | Configure endpoint (set type, max packet, interval) |
| DEPXFERCFG | 2 | Transfer resource configuration |
| DEPGETENDSTATE | 3 | Get endpoint state |
| DEPSSTALL | 4 | Set stall |
| DEPCSTALL | 5 | Clear stall |
| DEPSTRTXFER | 6 | Start transfer (provide TRB pointer) |
| DEPUPDXFER | 7 | Update transfer |
| DEPENDXFER | 8 | End transfer |
| DEPSTARTCFG | 9 | Start new endpoint configuration |

---

## 14. xHCI REGISTER MAP

| Register | Offset | Description |
|---|---|---|
| CAPLENGTH | 0x00 | Capability Register Length |
| HCIVERSION | 0x02 | xHCI Version |
| HCSPARAMS1 | 0x04 | Structural Params (MaxSlots, MaxIntrs, MaxPorts) |
| HCSPARAMS2 | 0x08 | Structural Params 2 |
| HCSPARAMS3 | 0x0C | Structural Params 3 (latencies) |
| HCCPARAMS1 | 0x10 | Capability Params (64-bit, bandwidth, etc.) |
| DBOFF | 0x14 | Doorbell Array Offset |
| RTSOFF | 0x18 | Runtime Register Space Offset |
| USBCMD | Op+0x00 | Command (Run/Stop, HC Reset, INTE, EWE) |
| USBSTS | Op+0x04 | Status (HCH, HSE, EINT, PCD, CNR) |
| CRCR | Op+0x18 | Command Ring Control (pointer + RCS) |
| DCBAAP | Op+0x30 | Device Context Base Address Array Pointer |
| CONFIG | Op+0x38 | Configuration (MaxSlotsEn) |
| PORTSC(n) | Op+0x400+16n | Port Status/Control (CCS, PED, Speed, Power) |

---

## 15. COMMON VID:PID VALUES

| VID:PID | Device |
|---|---|
| 0x18D1:0x4EE1 | Google Pixel (MTP) |
| 0x18D1:0x4EE2 | Google Pixel (MTP + ADB) |
| 0x18D1:0x2D00 | Android Accessory |
| 0x18D1:0x2D01 | Android Accessory + ADB |
| 0x04E8:0x6860 | Samsung Galaxy (MTP) |
| 0x04E8:0x6861 | Samsung Galaxy (MTP + ADB) |
| 0x05AC:0x12A8 | Apple iPhone (PTP) |
| 0x1D6B:0x0002 | Linux Foundation USB 2.0 Root Hub |
| 0x1D6B:0x0003 | Linux Foundation USB 3.0 Root Hub |
| 0x8087:0x0032 | Intel AX201 Bluetooth |
| 0x0BDA:0x8153 | Realtek USB 3.0 Ethernet |
| 0x046D:0xC077 | Logitech Mouse |

---

## 16. USB DEVICE STATES

```
                VBUS detected
Attached ─────────────────────────▶ Powered
                Hub port enable
Powered  ─────────────────────────▶ Default (address=0)
                SET_ADDRESS
Default  ─────────────────────────▶ Address (address=1-127)
                SET_CONFIGURATION
Address  ─────────────────────────▶ Configured (fully operational)
                No bus activity 3ms
Any      ─────────────────────────▶ Suspended (low power)
                Bus Reset
Any      ─────────────────────────▶ Default (back to address 0)
```

---

## 17. ANDROID USB PROPERTIES & PATHS

```bash
# System properties
getprop sys.usb.config              # Current USB config (e.g., "mtp,adb")
getprop sys.usb.state               # Current USB state
getprop persist.sys.usb.config      # Persistent USB config
getprop sys.usb.controller          # UDC name (e.g., "a600000.dwc3")
getprop sys.usb.configfs            # "1" if using configfs

# Key paths
/config/usb_gadget/g1/              # ConfigFS gadget root
/config/usb_gadget/g1/UDC           # Write controller name to enable
/config/usb_gadget/g1/functions/    # Available function instances
/dev/usb-ffs/adb/                   # ADB FunctionFS endpoint files

# ADB specific
/data/misc/adb/adb_keys             # Authorized host public keys
adb usb                              # Switch to USB transport
adb devices                          # List connected devices

# HAL
/vendor/bin/hw/android.hardware.usb@1.x-service
/vendor/etc/init/android.hardware.usb@1.x-service.rc
```

---

## 18. USB POWER SUMMARY

| Source | Voltage | Current | Power | Connector |
|---|---|---|---|---|
| USB 2.0 (unit load) | 5V | 100mA | 0.5W | A |
| USB 2.0 (configured) | 5V | 500mA | 2.5W | A |
| USB 3.x (unit load) | 5V | 150mA | 0.75W | A/C |
| USB 3.x (configured) | 5V | 900mA | 4.5W | A/C |
| USB-C (default) | 5V | 1.5A or 3A | 7.5-15W | C |
| USB PD (standard) | 5-20V | up to 5A | up to 100W | C |
| USB PD (EPR) | 5-48V | up to 5A | up to 240W | C |

---

## 19. TROUBLESHOOTING QUICK REFERENCE

| Symptom | Check | Fix |
|---|---|---|
| Device not detected | CC pins, VBUS, pull-ups | Check cable, TCPC, PHY power |
| FS instead of HS | Chirp handshake | PHY tuning (amplitude), cable quality |
| Enumeration fails | GET_DESCRIPTOR response | Descriptors, EP0 max packet, timing |
| Disconnect under load | VBUS voltage, thermal | Power supply, cable gauge, cooling |
| No SS (falls to HS) | QMP PHY, PIPE interface | PHY init, lane config, cable SS wires |
| STALL on control | Request handling | Check class driver, bRequest support |
| Slow bulk transfer | URB size, scheduling | Increase URB count, check SS burst |
| Gadget not visible | ConfigFS, UDC | Check UDC write, function links |
| PD negotiation fail | CC, BMC signaling | TCPC FW, PDO config, cable E-marker |
| Role switch stuck | TCPM, DWC3 mode | extcon/typec events, dr_mode DT |

---

## 20. DEVICE TREE (DT) USB PROPERTIES (Qualcomm)

```dts
/* DWC3 controller node */
usb0: usb@a600000 {
    compatible = "qcom,dwc3";
    reg = <0x0a600000 0xf8c>;
    #address-cells = <1>;
    #size-cells = <1>;
    
    dwc3@a600000 {
        compatible = "snps,dwc3";
        reg = <0x0a600000 0xcd00>;
        interrupts = <0 133 4>;
        dr_mode = "otg";           /* host | peripheral | otg */
        usb-role-switch;
        maximum-speed = "super-speed-plus";
        snps,dis_u2_susphy_quirk;
        snps,dis_enblslpm_quirk;
        snps,has-lpm-erratum;
        
        port {
            usb_dwc3_role_switch: endpoint {
                remote-endpoint = <&typec_role>;
            };
        };
    };
};

/* USB 2.0 PHY */
usb2_phy0: phy@88e2000 {
    compatible = "qcom,qusb2-v2-phy";
    reg = <0x088e2000 0x400>;
    vdd-supply = <&pm8150_l5>;
    vdda18-supply = <&pm8150_l12>;
    vdda33-supply = <&pm8150_l2>;
    qcom,tune-usb2-disc-thres = /bits/ 8 <0x07>;
    qcom,tune-usb2-amplitude = /bits/ 8 <0x08>;
};

/* USB 3.x PHY */
usb3_phy0: phy@88e8000 {
    compatible = "qcom,qmp-usb3-phy";
    reg = <0x088e8000 0x1c00>;
    vdda-phy-supply = <&pm8150_l9>;
    vdda-pll-supply = <&pm8150_l18>;
};

/* Type-C port controller */
typec_port0: typec-port {
    compatible = "usb-c-connector";
    power-role = "dual";
    data-role = "dual";
    try-power-role = "sink";
    
    source-pdos = <PDO_FIXED(5000, 3000, ...)>;
    sink-pdos = <PDO_FIXED(5000, 3000, ...)
                 PDO_FIXED(9000, 3000, ...)>;
    op-sink-microwatt = <15000000>;
};
```

---

## 21. USB ERROR CODES (Linux)

| Error | Code | Meaning |
|---|---|---|
| -ENODEV | -19 | Device disconnected |
| -ENOMEM | -12 | Memory allocation failed |
| -EBUSY | -16 | Device busy |
| -EPIPE | -32 | Endpoint stalled |
| -EOVERFLOW | -75 | Data overrun (babble) |
| -EPROTO | -71 | Protocol error (bitstuff, CRC) |
| -EILSEQ | -84 | CRC/timeout (electrical) |
| -ETIMEDOUT | -110 | Transfer timeout |
| -ESHUTDOWN | -108 | HC shut down (disconnect during transfer) |
| -ECONNRESET | -104 | Transfer cancelled (URB unlinked) |
| -ENOENT | -2 | URB was already unlinked |
| -EXDEV | -18 | Short packet (partial transfer) |
| -EREMOTEIO | -121 | Short transfer (less data than expected) |

---

## 22. KERNEL CONFIG OPTIONS

```
# Core USB
CONFIG_USB=y                    # USB support
CONFIG_USB_XHCI_HCD=y          # xHCI host controller
CONFIG_USB_DWC3=y               # DWC3 controller
CONFIG_USB_DWC3_QCOM=y          # Qualcomm DWC3 glue
CONFIG_USB_GADGET=y             # USB gadget support
CONFIG_USB_CONFIGFS=y           # ConfigFS gadget interface

# PHY
CONFIG_USB_QCOM_QUSB2_PHY=y    # Qualcomm USB 2.0 PHY
CONFIG_USB_QCOM_QMP_PHY=y      # Qualcomm USB 3.x PHY

# Type-C
CONFIG_TYPEC=y                  # USB Type-C support
CONFIG_TYPEC_TCPM=y             # Type-C Port Manager
CONFIG_TYPEC_UCSI=y             # USB-C Connector System Interface

# Class drivers
CONFIG_USB_STORAGE=y            # Mass storage
CONFIG_USB_HID=y                # Human Interface Device
CONFIG_USB_VIDEO_CLASS=y        # UVC video
CONFIG_SND_USB_AUDIO=y          # USB audio
CONFIG_USB_NET_CDC_NCM=y        # CDC NCM networking
CONFIG_USB_ACM=y                # CDC ACM serial

# Gadget functions
CONFIG_USB_F_MTP=y              # MTP function
CONFIG_USB_F_ADB=y              # ADB function (functionfs)
CONFIG_USB_F_NCM=y              # NCM networking function
CONFIG_USB_F_MASS_STORAGE=y     # Mass storage function

# Debug
CONFIG_USB_MON=y                # USB monitor (usbmon)
CONFIG_USB_DEBUG=y              # Verbose USB debug messages
CONFIG_DYNAMIC_DEBUG=y          # Dynamic debug (per-module)
```

---

END OF DOCUMENT 06 — CHEATSHEET
