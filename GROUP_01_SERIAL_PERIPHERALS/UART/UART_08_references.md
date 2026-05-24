# UART — REFERENCES & LEARNING RESOURCES
# ════════════════════════════════════════════════════════════════════
# Protocol: UART | Document: 08 of 08 — A-Z Resource Guide
# Books, Standards, Websites, GitHub, Tools, Courses
# ════════════════════════════════════════════════════════════════════

---

# 1. OFFICIAL STANDARDS & SPECIFICATIONS

| Standard | Title | Publisher | Notes |
|----------|-------|-----------|-------|
| TIA-232-F (2012) | Interface Between DTE and DCE | TIA/EIA | The RS-232 standard (physical layer) |
| TIA-485-A (1998) | Electrical Characteristics of Generators and Receivers for Use in Balanced Digital Multipoint Systems | TIA/EIA | RS-485 differential |
| TIA-422-B | Electrical Characteristics of Balanced Voltage Digital Interface Circuits | TIA/EIA | RS-422 differential point-to-point |
| ITU-T V.24 | List of Definitions for Interchange Circuits | ITU | International RS-232 equivalent |
| ITU-T V.28 | Electrical Characteristics for Unbalanced Interchange Circuits | ITU | Voltage levels spec |
| 16550D Datasheet | PC16550D Universal Asynchronous Receiver/Transmitter with FIFOs | Texas Instruments | The standard UART IC reference |

### Where to Get Standards
- TIA standards: https://global.ihs.com (paid) or university library
- ITU-T recommendations: https://www.itu.int/rec/T-REC-V/en
- TI 16550 datasheet: https://www.ti.com/lit/ds/symlink/pc16550d.pdf (FREE)

---

# 2. BOOKS

## Essential (Must-Read)

| Book | Author | Why Read It |
|------|--------|-------------|
| **The Definitive Guide to ARM Cortex-M3/M4** | Joseph Yiu | Ch. 17: UART peripheral programming on ARM, best bare-metal reference |
| **Mastering STM32** | Carmine Noviello | Complete UART examples: polling, IRQ, DMA on real hardware |
| **Linux Device Drivers, 3rd Ed** | Corbet, Rubini, Kroah-Hartman | Ch. 18: TTY drivers, serial core architecture |
| **Serial Port Complete, 2nd Ed** | Jan Axelson | Entire book dedicated to serial comms; PC + embedded |
| **Embedded Systems: Real-Time Interfacing to ARM Cortex-M** | Jonathan Valvano | UART labs with TM4C, excellent for self-study |

## Advanced / Supplementary

| Book | Author | Why Read It |
|------|--------|-------------|
| **Linux Kernel Development, 3rd Ed** | Robert Love | IRQ handling, bottom halves — explains serial driver model |
| **Understanding the Linux Kernel, 3rd Ed** | Bovet & Cesati | Deep kernel internals for serial subsystem understanding |
| **Designing Embedded Hardware** | John Catsoulis | Hardware-level serial interface design, RS-232/485 circuits |
| **The Art of Electronics, 3rd Ed** | Horowitz & Hill | §14.7: Serial protocols, level converters, practical circuits |
| **High-Speed Serial Buses in Embedded Systems** | Fabian Kung | Signal integrity, timing margins for serial links |

## Automotive-Specific

| Book | Author | Why Read It |
|------|--------|-------------|
| **Automotive Embedded Systems Handbook** | Nicolas Navet | Context of UART in automotive diagnostics |
| **Understanding Automotive Electronics, 8th Ed** | William Ribbens | Where UART fits in vehicle architecture |

---

# 3. BEST WEBSITES & ONLINE REFERENCES

## Technical Deep-Dives

| Website | URL | What You Get |
|---------|-----|--------------|
| **DeepBlueMbedded** | https://deepbluembedded.com/stm32-uart-tutorial/ | Best STM32 UART tutorial series (polling, IRQ, DMA) |
| **Sparkfun Serial Tutorial** | https://learn.sparkfun.com/tutorials/serial-communication | Excellent visual beginner introduction |
| **Analog Devices UART Guide** | https://www.analog.com/en/resources/analog-dialogue/articles/uart-a-hardware-communication-protocol.html | Hardware-focused explanation |
| **NXP UART Application Notes** | https://www.nxp.com/docs/en/application-note/AN10369.pdf | Practical design notes |
| **ControllersTech** | https://controllerstech.com/uart-in-stm32/ | HAL + Register-level STM32 tutorials |
| **Bootlin Elixir** | https://elixir.bootlin.com/linux/latest/source/drivers/tty/serial | Browse Linux serial driver source |
| **Phil's Lab (YouTube)** | https://youtube.com/@PhilsLab | Hardware design for serial interfaces |
| **Ben Eater** | https://www.youtube.com/watch?v=eq5YpKHXJDM | Build UART from scratch with logic gates (legendary video) |

## Quick References

| Resource | URL | Use For |
|----------|-----|---------|
| **Wikipedia: UART** | https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter | Quick overview, history |
| **Wikipedia: RS-232** | https://en.wikipedia.org/wiki/RS-232 | Pinout, voltage specs |
| **devhints.io/termios** | https://devhints.io/ | Quick termios reference |
| **man7.org termios** | https://man7.org/linux/man-pages/man3/termios.3.html | Official Linux termios docs |
| **Serial Programming HOWTO** | https://tldp.org/HOWTO/Serial-Programming-HOWTO/ | Classic Linux serial guide |

## Forums & Q&A

| Platform | URL | Best For |
|----------|-----|----------|
| **EE Stack Exchange** | https://electronics.stackexchange.com/questions/tagged/uart | Hardware design questions |
| **Stack Overflow** | https://stackoverflow.com/questions/tagged/uart | Software/programming questions |
| **STM32 Community** | https://community.st.com | STM32-specific UART issues |
| **NXP Community** | https://community.nxp.com | NXP MCU serial questions |
| **Linux Kernel Mailing List** | https://lore.kernel.org/linux-serial/ | Serial driver development discussions |

---

# 4. GITHUB REPOSITORIES & OPEN SOURCE

## Educational / Learning

| Repository | URL | Description |
|-----------|-----|-------------|
| **libopencm3** | https://github.com/libopencm3/libopencm3 | Open-source ARM Cortex firmware library with excellent UART examples |
| **STM32CubeF4** | https://github.com/STMicroelectronics/STM32CubeF4 | Official STM32 HAL + examples (see UART_TwoBoards_*) |
| **stm32-rs** | https://github.com/stm32-rs | Rust embedded HAL — clean UART abstractions |
| **tinyusb** | https://github.com/hathach/tinyusb | USB-CDC (virtual serial) implementation — learn USB↔UART bridge |
| **miniterm (pyserial)** | https://github.com/pyserial/pyserial | Python serial library — read source for protocol understanding |
| **ben-eater/uart** | https://github.com/beneater/uart | Build UART from 74xx chips — best hardware understanding |

## Tools & Utilities

| Repository | URL | Description |
|-----------|-----|-------------|
| **minicom** | https://salsa.debian.org/minicom-team/minicom | Classic Linux serial terminal — study config code |
| **picocom** | https://github.com/npat-efault/picocom | Minimal serial terminal — clean codebase |
| **CoolTerm** | https://github.com/nickolay/coolterm | Cross-platform serial terminal |
| **Serial Studio** | https://github.com/Serial-Studio/Serial-Studio | Real-time serial data visualization |
| **sigrok/libsigrokdecode** | https://github.com/sigrokproject/libsigrokdecode | UART protocol decoder source — learn bit-level decoding |
| **serialplot** | https://github.com/hyOzd/serialplot | Plot real-time data from serial port |

## Linux Kernel Source (Key Files)

| File | Path in Kernel Tree | What It Does |
|------|-------------------|--------------|
| 8250_port.c | drivers/tty/serial/8250/8250_port.c | Main 8250/16550 UART driver |
| 8250_dma.c | drivers/tty/serial/8250/8250_dma.c | DMA support for 8250 |
| serial_core.c | drivers/tty/serial/serial_core.c | Generic serial framework |
| msm_serial.c | drivers/tty/serial/msm_serial.c | Qualcomm UART driver |
| imx.c | drivers/tty/serial/imx.c | NXP i.MX UART driver |
| tty_io.c | drivers/tty/tty_io.c | TTY layer above serial |
| n_tty.c | drivers/tty/n_tty.c | Line discipline (canonical mode) |

Browse online: https://elixir.bootlin.com/linux/latest/source/drivers/tty/serial/

## Embedded Frameworks with Good UART Implementations

| Framework | URL | Notes |
|-----------|-----|-------|
| **Zephyr RTOS** | https://github.com/zephyrproject-rtos/zephyr | See drivers/serial/ — clean async API |
| **FreeRTOS** | https://github.com/FreeRTOS/FreeRTOS | Demos with UART stream buffers |
| **RIOT OS** | https://github.com/RIOT-OS/RIOT | drivers/periph/uart — multi-platform |
| **Apache NuttX** | https://github.com/apache/nuttx | POSIX-like serial on MCUs |
| **Mbed OS** | https://github.com/ARMmbed/mbed-os | mbed::Serial class — object-oriented UART |

---

# 5. VIDEO COURSES & TUTORIALS

## Free

| Course/Channel | Platform | Content |
|--------|----------|---------|
| **Ben Eater: Build a UART** | YouTube | Build UART from logic gates — foundational understanding |
| **Fastbit EBA: STM32 UART** | YouTube/Udemy free previews | Register-level UART programming |
| **Phil's Lab: PCB + Serial Design** | YouTube | Hardware design for serial interfaces |
| **Jacob Sorber: Serial in C** | YouTube | Linux serial programming in C |
| **Low Level Learning** | YouTube | Embedded C UART concepts |
| **DigiKey TechForum** | YouTube | UART basics with scope demos |

## Paid (Worth It)

| Course | Platform | Content |
|--------|----------|---------|
| **Mastering MCU: Timers, PWM, UART** | Udemy (FastBit) | Complete STM32 peripheral mastery including UART DMA |
| **Embedded Systems Programming on ARM Cortex-M** | Udemy (Israel Manor) | UART bare-metal + RTOS integration |
| **Linux Kernel Programming** | Udemy (Kaiwan N Billimoria) | Writing serial/char drivers |
| **Embedded Linux Step by Step** | Udemy (Nayak) | Device tree + serial console for embedded Linux |

---

# 6. APPLICATION NOTES (Industry)

| Vendor | Document | Topic |
|--------|----------|-------|
| Texas Instruments | SLAA207A | RS-485 Layout Guidelines |
| TI | SNLA049B | RS-422 vs RS-485 Comparison |
| TI | SLLA037 | Long-Range RS-232 Design |
| Maxim/Analog | AN3362 | RS-232 vs RS-485 Comparison |
| Maxim/Analog | AN723 | Auto-Baud Detection Methods |
| NXP | AN10369 | UART/SCI Overview and Application |
| ST | AN3155 | USART Bootloader Protocol |
| ST | AN4031 | Using the STM32 DMA controller with UART |
| Microchip | AN774 | Async Communications with PIC |
| Qualcomm | 80-P2484-52 | Debug UART on Snapdragon (limited access) |
| Silicon Labs | AN0059 | UART/USART in EFM32 |

---

# 7. DEVELOPMENT TOOLS

## Hardware Tools

| Tool | Price Range | Use Case |
|------|------------|----------|
| FTDI FT232R breakout | $5-15 | USB↔UART converter (3.3V/5V) |
| CP2102 module | $2-5 | Cheap USB↔UART (3.3V) |
| Saleae Logic 8 | $400-500 | Protocol decode + timing analysis |
| FX2 clone + PulseView | $10-20 | Budget logic analyzer with UART decode |
| Bus Pirate | $30 | Multi-protocol tool, UART bridge/monitor |
| MAX232 breakout | $2 | TTL ↔ RS-232 level converter |
| RS-485 transceiver board | $2-5 | Half/full duplex RS-485 interface |
| Oscilloscope (Rigol DS1054Z) | $400 | Analog signal quality analysis |
| Total Phase Aardvark | $300 | Professional serial protocol analyzer |

## Software Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| minicom | Linux | Full-featured serial terminal |
| picocom | Linux | Lightweight serial terminal |
| PuTTY | Windows/Linux | Serial terminal with logging |
| Tera Term | Windows | Serial terminal + macros |
| RealTerm | Windows | Binary/hex serial display |
| CoolTerm | Cross-platform | Easy serial terminal |
| Serial Monitor | VS Code extension | IDE-integrated |
| screen | Linux | Quick serial session |
| socat | Linux | Virtual serial port pairs for testing |
| com0com | Windows | Virtual null modem for testing |
| PulseView | Cross-platform | Open-source protocol analysis |
| Wireshark | Cross-platform | Can decode serial-over-TCP |
| Serial Studio | Cross-platform | Real-time data dashboard from serial |

---

# 8. TESTING & SIMULATION

## Virtual Serial Ports (No Hardware Needed)

```bash
# Linux: Create virtual serial port pair
socat -d -d pty,raw,echo=0 pty,raw,echo=0
# Creates /dev/pts/X and /dev/pts/Y — write to one, read from other

# Alternative: using tty0tty kernel module
# https://github.com/freemed/tty0tty

# Test your code without hardware:
# Terminal 1: minicom -D /dev/pts/3
# Terminal 2: your_program /dev/pts/4
```

## Python Quick Test Scripts
```python
# uart_sender.py
import serial, time
ser = serial.Serial('/dev/ttyUSB0', 115200, timeout=1)
for i in range(100):
    ser.write(f"Message {i}\n".encode())
    time.sleep(0.01)
ser.close()

# uart_receiver.py
import serial
ser = serial.Serial('/dev/ttyUSB1', 115200, timeout=1)
while True:
    line = ser.readline()
    if line:
        print(f"RX: {line.decode().strip()}")
```

## Stress Testing
```bash
# Generate continuous data at max rate
dd if=/dev/urandom bs=1024 count=1000 > /dev/ttyS0

# Verify with checksums (loopback)
dd if=/dev/urandom bs=1024 count=100 | tee >(md5sum > /tmp/tx.md5) > /dev/ttyS0 &
dd if=/dev/ttyS0 bs=1024 count=100 | md5sum > /tmp/rx.md5
diff /tmp/tx.md5 /tmp/rx.md5
```

---

# 9. AUTOMOTIVE-SPECIFIC RESOURCES

| Resource | Type | Content |
|----------|------|---------|
| Qualcomm SA8155P TRM | Technical Reference Manual | Debug UART config, registers, mux |
| NXP S32G Reference Manual | TRM | LINFLEXD UART peripheral |
| Renesas R-Car H3 Manual | TRM | SCIF serial interface |
| AUTOSAR SWS | Specification | SCI (Serial Communication Interface) driver spec |
| Vector CANoe | Tool | Can monitor UART alongside CAN/LIN |
| dlt-daemon | Open source | Automotive logging over serial (GENIVI) |

---

# 10. STUDY PATH — RECOMMENDED ORDER

## Week 1: Foundations
1. Watch: Ben Eater "Build a UART" (YouTube)
2. Read: Sparkfun Serial Communication tutorial
3. Read: TI 16550 Datasheet (focus on register map)
4. Lab: Loopback test on any MCU

## Week 2: Embedded Programming
5. Read: "Mastering STM32" Ch. on UART (or equivalent for your MCU)
6. Study: libopencm3 UART examples on GitHub
7. Lab: Two-board communication
8. Lab: Ring buffer + ISR implementation

## Week 3: Advanced & Linux
9. Read: "Linux Device Drivers" Ch. 18 (TTY/Serial)
10. Study: Linux kernel source: 8250_port.c
11. Lab: Linux serial programming with termios
12. Lab: DMA-based UART on STM32

## Week 4: Automotive & Integration
13. Read: Platform-specific debug UART setup guide
14. Lab: Boot log capture and analysis
15. Lab: Protocol bridge (UART↔CAN)
16. Study: AUTOSAR SCI driver specification

---

# 11. QUICK REFERENCE CARD — KEEP ON DESK

```
╔══════════════════════════════════════════════════════════╗
║                  UART QUICK CARD                         ║
╠══════════════════════════════════════════════════════════╣
║ Frame: IDLE(1)→START(0)→D0..D7(LSB)→[PAR]→STOP(1)     ║
║ Config: 115200-8N1 (default)                            ║
║ Wiring: TX→RX (crossed!) + GND                         ║
║ Bit time: 1/baud (8.68µs @115200)                      ║
║ Efficiency: 80% (8 data / 10 total bits for 8N1)       ║
║ Errors: FE=baud wrong, OE=CPU slow, PE=noise           ║
║ LSR check: bit0=data ready, bit5=can write             ║
║ FIFO: 0xC7→FCR (enable+reset+trig14)                   ║
║ Linux: stty -F /dev/ttyS0 115200 raw -echo             ║
║ Debug: Send 0x55 ('U'), check alternating on scope     ║
╚══════════════════════════════════════════════════════════╝
```

---

END OF DOCUMENT 08 — REFERENCES & LEARNING RESOURCES
