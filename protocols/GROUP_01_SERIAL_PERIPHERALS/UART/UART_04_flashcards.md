# UART — FLASH CARD SYSTEM
# ════════════════════════════════════════════════════════════════════
# Protocol: UART | Document: 04 of 08 — Flashcards (250+ cards)
# Format: FRONT / BACK / Tags / Difficulty
# ════════════════════════════════════════════════════════════════════

---

## DECK 1 — IDENTITY & METADATA (20 cards)

**CARD 1-01** | FRONT: What does UART stand for? | BACK: Universal Asynchronous Receiver/Transmitter | Tags: basics, acronym | Difficulty: Beginner

**CARD 1-02** | FRONT: Is UART synchronous or asynchronous? | BACK: Asynchronous — no shared clock line. Both sides agree on baud rate independently. Re-syncs on each start bit falling edge. | Tags: basics, timing | Difficulty: Beginner

**CARD 1-03** | FRONT: Who invented UART? When? | BACK: DEC (Digital Equipment Corporation), 1960s. First single-chip IC: Western Digital WD1402A (1971). Standard RS-232 published by EIA in 1962. | Tags: history | Difficulty: Beginner

**CARD 1-04** | FRONT: What OSI layers does UART cover? | BACK: Layer 1 (Physical — signaling) and partial Layer 2 (Data Link — framing with start/stop/parity). Everything above is undefined by UART. | Tags: theory, OSI | Difficulty: Beginner

**CARD 1-05** | FRONT: What is the standard body for UART/RS-232? | BACK: EIA/TIA (Electronic Industries Alliance / Telecom Industry Association). Standard: TIA-232-F (2012). Also ITU-T V.24/V.28. | Tags: standards | Difficulty: Beginner

**CARD 1-06** | FRONT: What is the topology of UART? | BACK: Point-to-point. One transmitter to one receiver. NOT a bus. For multi-device: need RS-485 or 9-bit addressing mode. | Tags: basics, topology | Difficulty: Beginner

**CARD 1-07** | FRONT: Max speed of UART? | BACK: Traditionally 115.2 kbps. Modern SoCs: 1–5 Mbps. Some: 12–15 Mbps. Limit is electrical (cable capacitance, driver) not protocol. | Tags: performance | Difficulty: Beginner

**CARD 1-08** | FRONT: Max distance: RS-232 vs RS-485? | BACK: RS-232: 15m (spec). RS-485: 1200m (at ≤100 kbps). TTL on-board: 1-2m. | Tags: physical | Difficulty: Beginner

**CARD 1-09** | FRONT: Name 3 direct competitors to UART. | BACK: SPI (faster, synchronous), I2C (multi-device, 2-wire), USB (PC-facing, hot-plug). | Tags: comparison | Difficulty: Beginner

**CARD 1-10** | FRONT: Is UART open standard or proprietary? | BACK: Open standard. No licensing fees. Any manufacturer can implement. | Tags: basics | Difficulty: Beginner

**CARD 1-11** | FRONT: What encoding does UART use? | BACK: NRZ (Non-Return-to-Zero). Logic 1 = HIGH entire bit period. Logic 0 = LOW entire bit period. No mid-bit transitions. | Tags: physical, encoding | Difficulty: Intermediate

**CARD 1-12** | FRONT: What replaced UART for PC peripherals? | BACK: USB (Universal Serial Bus). But UART remains universal for embedded debug, bootloader, and module communication. | Tags: history | Difficulty: Beginner

**CARD 1-13** | FRONT: Name the most famous UART IC. | BACK: 16550A (National Semiconductor, 1987). Introduced 16-byte FIFO. Still the register-map standard for all modern PC-compatible UARTs. | Tags: hardware, history | Difficulty: Intermediate

**CARD 1-14** | FRONT: How many wires for full-duplex UART? | BACK: 3 minimum: TX, RX, GND. 5 with flow control: TX, RX, RTS, CTS, GND. | Tags: physical | Difficulty: Beginner

**CARD 1-15** | FRONT: Is UART full-duplex or half-duplex? | BACK: Full-duplex (TX and RX can operate simultaneously on separate wires). RS-485 is half-duplex (shared differential pair). | Tags: basics | Difficulty: Beginner

**CARD 1-16** | FRONT: Name 5 protocols built on top of UART. | BACK: AT commands, NMEA 0183 (GPS), Modbus RTU, Bluetooth HCI H4, Linux console/shell. Also: MIDI, DMX512, SLIP/PPP, LIN. | Tags: ecosystem | Difficulty: Intermediate

**CARD 1-17** | FRONT: What is the UART's safety classification? | BACK: None inherent. UART has no built-in safety mechanisms. For safety applications: add E2E CRC, watchdog, redundancy on top. Not suitable for ASIL-C/D direct use. | Tags: safety | Difficulty: Intermediate

**CARD 1-18** | FRONT: What is USART vs UART? | BACK: USART = Universal Synchronous/Asynchronous RT. Adds optional synchronous mode with clock output pin. Most MCU "USART" peripherals are used in UART (async) mode 99% of the time. | Tags: comparison | Difficulty: Beginner

**CARD 1-19** | FRONT: Name the physical medium types for UART. | BACK: TTL/CMOS (on-board copper), RS-232 (±12V single-ended copper), RS-422/485 (differential copper pair), IrDA (infrared optical), Current loop (20mA legacy). | Tags: physical | Difficulty: Intermediate

**CARD 1-20** | FRONT: Current UART standard revision? | BACK: TIA-232-F (2012). Adds 460800 and 921600 baud rates. Maintains backward compatibility with all prior revisions. | Tags: standards | Difficulty: Intermediate

---

## DECK 2 — PHYSICAL LAYER (25 cards)

**CARD 2-01** | FRONT: TTL UART: logic 1 voltage? | BACK: VCC (3.3V or 5V). This is the IDLE state. | Tags: voltage | Difficulty: Beginner

**CARD 2-02** | FRONT: TTL UART: logic 0 voltage? | BACK: 0V (GND). This is the START bit level. | Tags: voltage | Difficulty: Beginner

**CARD 2-03** | FRONT: RS-232: logic 1 (MARK) voltage? | BACK: NEGATIVE: -3V to -15V. INVERTED from TTL! | Tags: voltage, RS-232 | Difficulty: Intermediate

**CARD 2-04** | FRONT: RS-232: logic 0 (SPACE) voltage? | BACK: POSITIVE: +3V to +15V. INVERTED from TTL! | Tags: voltage, RS-232 | Difficulty: Intermediate

**CARD 2-05** | FRONT: Why is RS-232 inverted from TTL? | BACK: Historical: RS-232 defined "MARK" (idle) as negative voltage, "SPACE" (active/data) as positive. When TTL logic was added later, convention was already set. MAX232 handles the inversion. | Tags: history, RS-232 | Difficulty: Intermediate

**CARD 2-06** | FRONT: RS-485: differential voltage for logic 1? | BACK: A > B by ≥200mV. (A is non-inverting, B is inverting). | Tags: voltage, RS-485 | Difficulty: Intermediate

**CARD 2-07** | FRONT: RS-485: termination value and placement? | BACK: 120Ω at EACH END of the bus (matching line impedance ~120Ω). Two total termination resistors. | Tags: RS-485, termination | Difficulty: Intermediate

**CARD 2-08** | FRONT: RS-485: max nodes? | BACK: 32 (standard drivers), 256 (enhanced/low-power drivers like MAX3485E). | Tags: RS-485 | Difficulty: Intermediate

**CARD 2-09** | FRONT: What is 16× oversampling? | BACK: Receiver samples each bit period 16 times. Middle sample (#8) used as actual value. Provides ±3 sample tolerance for clock mismatch and noise. | Tags: timing, receiver | Difficulty: Intermediate

**CARD 2-10** | FRONT: UART bit period at 115200 baud? | BACK: 1/115200 = 8.68 µs per bit. | Tags: timing, formula | Difficulty: Beginner

**CARD 2-11** | FRONT: UART frame time at 115200-8N1? | BACK: 10 bits × 8.68 µs = 86.8 µs per character. | Tags: timing, formula | Difficulty: Beginner

**CARD 2-12** | FRONT: Sampling clock frequency for 115200 baud with 16× oversampling? | BACK: 115200 × 16 = 1,843,200 Hz (1.8432 MHz). | Tags: timing, formula | Difficulty: Intermediate

**CARD 2-13** | FRONT: Max baud rate error tolerance? | BACK: ±2-3% total mismatch between TX and RX. Each side: ~±1.5%. For 10-bit frame: error < 0.5 bit at last sample. | Tags: timing, tolerance | Difficulty: Intermediate

**CARD 2-14** | FRONT: Why are crystals like 11.0592 MHz "UART-friendly"? | BACK: They divide evenly into standard baud rates: 11059200/(16×6) = 115200 EXACTLY. Zero baud rate error. | Tags: clock, hardware | Difficulty: Advanced

**CARD 2-15** | FRONT: What is the DB-9 pinout for RXD and TXD? | BACK: Pin 2 = RXD (receive data). Pin 3 = TXD (transmit data). Pin 5 = GND. | Tags: connector | Difficulty: Beginner

**CARD 2-16** | FRONT: What happens if UART TX wire breaks? | BACK: RX side sees permanent LOW → break condition detected → error flag set. This is WHY idle=HIGH: broken wire ≠ valid data. | Tags: safety, physical | Difficulty: Intermediate

**CARD 2-17** | FRONT: MAX232: what does it do? | BACK: Level shifter IC. Converts TTL (0/5V) ↔ RS-232 (±10V) using charge-pump from single 5V supply. Contains 2 TX drivers + 2 RX receivers. 3.3V version: MAX3232. | Tags: hardware, IC | Difficulty: Intermediate

**CARD 2-18** | FRONT: Can you connect 3.3V UART TX to 5V UART RX directly? | BACK: Usually YES — 3.3V > 2.0V (5V TTL input HIGH threshold). But 5V TX to 3.3V RX is DANGEROUS (exceeds absolute max). Use level shifter for 5V→3.3V direction. | Tags: voltage, practical | Difficulty: Intermediate

**CARD 2-19** | FRONT: What causes reflections on UART line? | BACK: Impedance mismatch at cable ends + high baud rate + long cable. Significant above ~1 Mbps with >30cm cable. Fix: proper termination, controlled impedance routing. | Tags: signal integrity | Difficulty: Advanced

**CARD 2-20** | FRONT: Standard baud rates (list 8 common ones). | BACK: 9600, 19200, 38400, 57600, 115200, 230400, 460800, 921600. Also: 1000000, 3000000. | Tags: basics | Difficulty: Beginner

**CARD 2-21** | FRONT: Why are standard baud rates multiples of 300? | BACK: Historical: 300 baud was the first modem speed. All subsequent rates derived by multiplying: 300×32=9600, 300×384=115200. Powers of 2 were NOT chosen (modem compatibility). | Tags: history | Difficulty: Intermediate

**CARD 2-22** | FRONT: What is RTS/CTS flow control? | BACK: Hardware flow control. When receiver buffer nearly full: de-assert RTS → sender sees CTS inactive → stops TX. When space available: re-assert RTS → sender resumes. Prevents buffer overflow. | Tags: flow control | Difficulty: Intermediate

**CARD 2-23** | FRONT: XON/XOFF values? | BACK: XON = 0x11 (Ctrl-Q, "resume sending"). XOFF = 0x13 (Ctrl-S, "stop sending"). Software flow control — can't use with binary data containing these values. | Tags: flow control | Difficulty: Intermediate

**CARD 2-24** | FRONT: RS-232 max cable capacitance spec? | BACK: 2500 pF total. This limits distance × speed. Longer cable = more capacitance = lower max speed. | Tags: RS-232, physical | Difficulty: Advanced

**CARD 2-25** | FRONT: How does receiver detect start bit vs noise? | BACK: On falling edge, receiver takes multiple samples during potential start bit. If majority of samples (e.g., 8 of 16) confirm LOW, it's a valid start bit. If not (noise glitch), ignore and continue waiting. | Tags: receiver, timing | Difficulty: Advanced

---

## DECK 3 — FRAME STRUCTURE (20 cards)

**CARD 3-01** | FRONT: UART start bit value? | BACK: 0 (LOW). Always. 1 bit long. Creates falling edge from idle (HIGH) for synchronization. | Tags: frame | Difficulty: Beginner

**CARD 3-02** | FRONT: UART stop bit value? | BACK: 1 (HIGH). Always. 1 or 2 bits long. Returns line to idle state. | Tags: frame | Difficulty: Beginner

**CARD 3-03** | FRONT: UART idle state? | BACK: HIGH (logic 1). Line stays high when nothing is being transmitted. | Tags: frame | Difficulty: Beginner

**CARD 3-04** | FRONT: Bit transmission order in UART? | BACK: LSB first (Least Significant Bit first). D0 is transmitted first, D7 last. | Tags: frame | Difficulty: Beginner

**CARD 3-05** | FRONT: What is "8N1"? | BACK: 8 data bits, No parity, 1 stop bit. Most common UART configuration. Total frame = 10 bits. | Tags: config | Difficulty: Beginner

**CARD 3-06** | FRONT: How many total bits in one 8N1 frame? | BACK: 10 bits: 1 start + 8 data + 1 stop. Efficiency = 80%. | Tags: frame, formula | Difficulty: Beginner

**CARD 3-07** | FRONT: How many total bits in one 8E1 frame? | BACK: 11 bits: 1 start + 8 data + 1 even parity + 1 stop. Efficiency = 72.7%. | Tags: frame, formula | Difficulty: Intermediate

**CARD 3-08** | FRONT: Even parity rule? | BACK: Total count of 1-bits (data + parity) must be EVEN. If data has odd number of 1s, parity bit = 1. If even, parity bit = 0. | Tags: parity | Difficulty: Beginner

**CARD 3-09** | FRONT: Can parity detect 2-bit errors? | BACK: NO. Two flipped bits cancel out — even parity still looks correct. Parity only reliably detects ODD numbers of bit errors. | Tags: parity, limitation | Difficulty: Intermediate

**CARD 3-10** | FRONT: What is a break condition? | BACK: TX line held LOW for longer than 1 frame time (>10 bit periods for 8N1). Distinguished from valid data because no valid frame can have stop bit = 0 for that long. | Tags: error, special | Difficulty: Intermediate

**CARD 3-11** | FRONT: Transmit 'A' (0x41) in 8N1 — what goes on the wire? | BACK: 0x41 = 0b01000001. LSB first: START(0), 1,0,0,0,0,0,1,0, STOP(1). Wire: idle HIGH → LOW → HIGH → LOW×5 → HIGH → LOW → HIGH(stop). | Tags: frame, example | Difficulty: Intermediate

**CARD 3-12** | FRONT: Why 'U' (0x55) for auto-baud? | BACK: 0x55 = 0b01010101. LSB first gives alternating 1/0/1/0/1/0/1/0 pattern on wire — maximum number of edges for timing measurement. | Tags: practical | Difficulty: Intermediate

**CARD 3-13** | FRONT: What data bit options exist? | BACK: 5, 6, 7, 8, or 9 data bits. 8 is standard. 7 was used for ASCII (7-bit). 9 is for multi-processor addressing mode. | Tags: config | Difficulty: Beginner

**CARD 3-14** | FRONT: What stop bit options exist? | BACK: 1, 1.5, or 2 stop bits. 1 is standard. 2 for slow receivers. 1.5 was for 5-bit Baudot code (historical). | Tags: config | Difficulty: Beginner

**CARD 3-15** | FRONT: UART frame efficiency formula? | BACK: Efficiency = data_bits / total_frame_bits. For 8N1: 8/10 = 80%. For 7E2: 7/11 = 63.6%. | Tags: formula | Difficulty: Intermediate

**CARD 3-16** | FRONT: Effective byte rate formula? | BACK: Byte_rate = baud_rate / total_frame_bits. For 115200 8N1: 115200/10 = 11,520 bytes/sec. | Tags: formula | Difficulty: Intermediate

**CARD 3-17** | FRONT: What is 9-bit multi-processor mode? | BACK: Bit 9 = 1 means ADDRESS frame (all slaves wake). Bit 9 = 0 means DATA frame (only addressed slave listens). Enables multi-device on single UART bus. | Tags: advanced | Difficulty: Advanced

**CARD 3-18** | FRONT: Mark parity vs Space parity? | BACK: Mark: parity bit ALWAYS = 1. Space: parity bit ALWAYS = 0. Not useful for error detection — used for compatibility/padding. | Tags: parity | Difficulty: Intermediate

**CARD 3-19** | FRONT: How does receiver know frame has ended? | BACK: Receiver counts expected bits (based on config). After all data + parity bits, it samples the stop bit. Stop bit = HIGH confirms valid frame end. Line returns to idle (HIGH). | Tags: frame, receiver | Difficulty: Intermediate

**CARD 3-20** | FRONT: What if two bytes sent back-to-back with no gap? | BACK: Valid! Stop bit of first frame provides the idle HIGH needed for next frame's start bit falling edge. No inter-frame gap required. Maximum throughput achieved with continuous back-to-back frames. | Tags: timing | Difficulty: Intermediate

---

## DECK 4 — ERROR HANDLING (20 cards)

**CARD 4-01** | FRONT: Name all 5 UART error types. | BACK: Framing Error (FE), Parity Error (PE), Overrun Error (OE), Break Error (BI), Noise Error (NE). | Tags: errors | Difficulty: Intermediate

**CARD 4-02** | FRONT: Framing Error: cause? | BACK: Stop bit reads as 0 instead of expected 1. #1 cause: baud rate mismatch. Also: noise, wrong config (7 vs 8 bits), interrupted TX. | Tags: errors | Difficulty: Intermediate

**CARD 4-03** | FRONT: Overrun Error: cause? | BACK: New byte received but previous byte not yet read by CPU. FIFO was full. Data lost permanently. Cause: CPU too slow/busy to service UART. | Tags: errors | Difficulty: Intermediate

**CARD 4-04** | FRONT: How to prevent overrun error? | BACK: Use DMA (zero CPU involvement), enable FIFO (buffers multiple bytes), increase ISR priority, use hardware flow control (RTS/CTS). | Tags: errors, fix | Difficulty: Intermediate

**CARD 4-05** | FRONT: Parity Error: what does it mean? | BACK: Calculated parity of received data ≠ received parity bit. At least 1 bit was corrupted during transmission. Note: cannot tell WHICH bit is wrong. | Tags: errors | Difficulty: Beginner

**CARD 4-06** | FRONT: Noise Error: how detected? | BACK: During 16× oversampling, the 3 samples around the center don't all agree (e.g., samples 7,8,9 are not unanimous). Byte received but flagged as potentially unreliable. | Tags: errors, hardware | Difficulty: Advanced

**CARD 4-07** | FRONT: Does UART have error CORRECTION? | BACK: NO. Only error DETECTION (parity, framing, overrun). No retransmission mechanism. Application layer must add CRC + ACK/NACK + retry if needed. | Tags: errors, limitation | Difficulty: Intermediate

**CARD 4-08** | FRONT: What happens on framing error at receiver? | BACK: FE flag set in status register. Byte is still stored (data may be valid despite framing issue). ISR/application must check FE and decide whether to discard. | Tags: errors | Difficulty: Intermediate

**CARD 4-09** | FRONT: How to clear error flags in 16550? | BACK: Read LSR register — reading it clears the error bits. On STM32: read SR then read DR (specific sequence required). | Tags: errors, hardware | Difficulty: Intermediate

**CARD 4-10** | FRONT: What error indicates baud rate mismatch? | BACK: Framing Error is the primary indicator. You may also see garbage data (wrong bit sampling) and possibly parity errors. | Tags: errors, debugging | Difficulty: Intermediate

---

## DECK 5 — HARDWARE ARCHITECTURE (20 cards)

**CARD 5-01** | FRONT: 16550 FIFO size? | BACK: 16 bytes TX + 16 bytes RX. The 16750 has 64 bytes. Modern SoC UARTs: 32–256 bytes. | Tags: hardware, FIFO | Difficulty: Intermediate

**CARD 5-02** | FRONT: What is DLAB in 16550? | BACK: Divisor Latch Access Bit (LCR bit 7). When DLAB=1: registers 0x00/0x01 access baud divisor (DLL/DLM). When DLAB=0: they access RBR/THR/IER. | Tags: hardware, registers | Difficulty: Intermediate

**CARD 5-03** | FRONT: 16550 register at offset 0x05? | BACK: LSR (Line Status Register). Contains: DR (data ready), OE, PE, FE, BI, THRE (TX empty), TEMT (TX complete). The most-read register during debug. | Tags: hardware, registers | Difficulty: Intermediate

**CARD 5-04** | FRONT: Baud rate divisor formula? | BACK: Divisor = Clock_Freq / (Baud_Rate × Oversampling). Example: 72MHz/(115200×16) = 39.06 → round to 39. | Tags: formula | Difficulty: Intermediate

**CARD 5-05** | FRONT: TX shift register vs TX holding register? | BACK: Holding register (THR/FIFO): where CPU writes data. Shift register: internal register that serializes bits one-by-one onto TX pin. CPU writes to holding; hardware moves to shift register when shift is empty. | Tags: hardware | Difficulty: Intermediate

**CARD 5-06** | FRONT: When does THRE interrupt fire? | BACK: TX Holding Register Empty — the FIFO/THR has space for more data. Signals CPU: "write more bytes to transmit." Does NOT mean transmission is complete on wire. | Tags: hardware, interrupt | Difficulty: Intermediate

**CARD 5-07** | FRONT: When does TEMT flag set? | BACK: Transmitter Empty — both the holding register AND shift register are empty. Transmission is truly complete. Use this before disabling UART or changing baud rate. | Tags: hardware | Difficulty: Advanced

**CARD 5-08** | FRONT: FIFO trigger levels on 16550? | BACK: RX FIFO interrupt triggers at: 1, 4, 8, or 14 bytes. Set via FCR bits [7:6]. Higher = fewer interrupts but more latency. | Tags: hardware, FIFO | Difficulty: Intermediate

**CARD 5-09** | FRONT: How to enable FIFO on 16550? | BACK: Write FCR (offset 0x02) with bit 0 = 1 (FIFO enable). Also set bits 1,2 to reset TX/RX FIFOs. Write 0xC7 for enable + reset + trigger=14. | Tags: hardware, config | Difficulty: Intermediate

**CARD 5-10** | FRONT: What is loopback mode? | BACK: MCR bit 4. Internal loopback: TX output connects to RX input internally. Used for self-test without external wiring. Useful during bring-up to verify driver without cable. | Tags: hardware, debug | Difficulty: Intermediate

**CARD 5-11** | FRONT: Name 3 USB-UART bridge ICs. | BACK: FTDI FT232R, Silicon Labs CP2102/CP2104, WCH CH340G. Also: Prolific PL2303, Cypress CY7C65213. | Tags: hardware, IC | Difficulty: Beginner

**CARD 5-12** | FRONT: How does DMA TX work for UART? | BACK: DMA reads bytes from memory buffer, writes to UART TX register, one byte per UART "TX empty" request. CPU only involved at setup and completion interrupt. Entire buffer sent with near-zero CPU overhead. | Tags: DMA | Difficulty: Intermediate

**CARD 5-13** | FRONT: How does DMA RX work for UART? | BACK: DMA reads from UART RX register (triggered by "RX not empty"), writes to circular memory buffer. Half-transfer and complete interrupts notify CPU. Combined with IDLE line interrupt for variable-length messages. | Tags: DMA | Difficulty: Intermediate

**CARD 5-14** | FRONT: STM32 IDLE line detection: what is it? | BACK: Hardware interrupt that fires when RX line is idle for 1 frame time after receiving data. Signals "message boundary" without needing delimiter characters. Perfect for DMA-based variable-length reception. | Tags: hardware, STM32 | Difficulty: Advanced

**CARD 5-15** | FRONT: What is the Qualcomm GENI Serial Engine? | BACK: Flexible hardware engine on Qualcomm SoCs that can be configured as UART, SPI, or I2C. Single HW block handles multiple protocols. Used in Snapdragon/SA8155P/SA8295P. | Tags: hardware, SoC | Difficulty: Advanced

**CARD 5-16** | FRONT: ARM PL011 UART: what is it? | BACK: ARM's AMBA Peripheral — a common UART IP block used in many ARM SoCs (Raspberry Pi, etc.). Linux driver: `amba-pl011.c`. Device: `/dev/ttyAMA0`. | Tags: hardware, ARM | Difficulty: Intermediate

**CARD 5-17** | FRONT: What is fractional baud rate divider? | BACK: Modern UARTs add fractional bits to the integer divisor for finer baud rate granularity. Example: STM32 BRR has 4 fractional bits (1/16th resolution). Reduces baud rate error. | Tags: hardware | Difficulty: Advanced

**CARD 5-18** | FRONT: What is auto-baud detection hardware? | BACK: Hardware that automatically measures incoming signal timing to determine baud rate. Methods: measure start bit width, or measure 0x55 pattern edges. Configures BRR without software calculation. | Tags: hardware | Difficulty: Advanced

**CARD 5-19** | FRONT: IER register bits on 16550? | BACK: Bit 0: RX data available, Bit 1: TX holding empty, Bit 2: RX line status (errors), Bit 3: Modem status change. Enable individual interrupt sources. | Tags: hardware, registers | Difficulty: Intermediate

**CARD 5-20** | FRONT: How to identify 16550 vs 8250 in software? | BACK: Enable FIFO (FCR bit 0=1). Read IIR bits [7:6]: if 11 = 16550A (FIFO works), if 10 = 16550 (buggy FIFO), if 00 = 8250/16450 (no FIFO). | Tags: hardware | Difficulty: Advanced

---

## DECK 6 — LINUX KERNEL (20 cards)

**CARD 6-01** | FRONT: Linux UART driver location in kernel tree? | BACK: `drivers/tty/serial/` — contains 8250/, amba-pl011.c, stm32-usart.c, msm_serial.c, samsung.c, etc. | Tags: linux, path | Difficulty: Intermediate

**CARD 6-02** | FRONT: Key struct for UART port in Linux? | BACK: `struct uart_port` — contains: iobase/membase, irq, uartclk, fifosize, ops (function pointers), type, line number. | Tags: linux, struct | Difficulty: Intermediate

**CARD 6-03** | FRONT: Key struct for UART driver in Linux? | BACK: `struct uart_driver` — contains: owner, driver_name, dev_name (e.g., "ttyS"), major, minor, nr (number of ports). Registered with `uart_register_driver()`. | Tags: linux, struct | Difficulty: Intermediate

**CARD 6-04** | FRONT: What is `uart_ops` in Linux? | BACK: Function pointer table: tx_empty(), set_mctrl(), get_mctrl(), stop_tx(), start_tx(), startup(), shutdown(), set_termios(), type(), config_port(). Driver implements these. | Tags: linux, driver | Difficulty: Advanced

**CARD 6-05** | FRONT: Linux: /dev/ttyS0 vs /dev/ttyAMA0 vs /dev/ttyUSB0? | BACK: ttyS0: 8250/16550-compatible. ttyAMA0: ARM PL011. ttyUSB0: USB-serial adapter. All use same termios userspace API — transparent to applications. | Tags: linux, device | Difficulty: Intermediate

**CARD 6-06** | FRONT: What is a line discipline in Linux TTY? | BACK: Processing layer between TTY core and serial driver. N_TTY: default (canonical mode, echo, special chars). N_HCI: Bluetooth. N_SLIP: IP-over-serial. Selectable per port. | Tags: linux, tty | Difficulty: Advanced

**CARD 6-07** | FRONT: How to check UART settings in Linux CLI? | BACK: `stty -F /dev/ttyS0 -a` — shows all termios settings. Or: `stty -F /dev/ttyS0 speed` for baud rate only. | Tags: linux, command | Difficulty: Beginner

**CARD 6-08** | FRONT: How to set baud rate from command line? | BACK: `stty -F /dev/ttyS0 115200` or `stty -F /dev/ttyS0 speed 115200 cs8 -parenb -cstopb` for 115200-8N1. | Tags: linux, command | Difficulty: Intermediate

**CARD 6-09** | FRONT: What is `earlycon` in Linux? | BACK: Early console — minimal polling UART output before full driver stack loads. Specified via cmdline: `earlycon=uart8250,mmio32,0x78af000,115200n8`. Allows boot messages from first printk. | Tags: linux, boot | Difficulty: Advanced

**CARD 6-10** | FRONT: What is `console=ttyS0,115200` kernel parameter? | BACK: Directs kernel console output (printk, panic) to serial port ttyS0 at 115200 baud. The primary debug mechanism for headless systems. | Tags: linux, boot | Difficulty: Intermediate

**CARD 6-11** | FRONT: Linux termios: what is VMIN and VTIME? | BACK: VMIN: minimum bytes before read() returns. VTIME: timeout in 0.1s units. VMIN=0,VTIME=0: non-blocking. VMIN=1,VTIME=0: block until 1 byte. VMIN=0,VTIME=10: 1s timeout. | Tags: linux, API | Difficulty: Intermediate

**CARD 6-12** | FRONT: Linux: canonical vs raw mode? | BACK: Canonical (ICANON): line-buffered, read() returns on newline. Raw (cfmakeraw): byte-by-byte, no processing. Raw needed for binary protocols/modems. | Tags: linux, API | Difficulty: Intermediate

**CARD 6-13** | FRONT: How to monitor serial port traffic in Linux? | BACK: `cat /dev/ttyS0` (read), `echo "test" > /dev/ttyS0` (write). Tools: minicom, picocom, screen, strace, interceptty (sniffer). | Tags: linux, debug | Difficulty: Beginner

**CARD 6-14** | FRONT: Linux UART device tree binding example? | BACK: `&uart0 { compatible = "ns16550a"; reg = <0x10000 0x100>; interrupts = <4>; clock-frequency = <1843200>; status = "okay"; };` | Tags: linux, DT | Difficulty: Advanced

**CARD 6-15** | FRONT: What does `serial8250_interrupt()` do? | BACK: Shared IRQ handler for 8250 family. Reads IIR to identify interrupt source (RX, TX, Error, Modem). Calls appropriate handler. Loops until IIR reports "no interrupt pending". | Tags: linux, driver | Difficulty: Advanced

**CARD 6-16** | FRONT: How to enable serial console login? | BACK: `systemctl enable serial-getty@ttyS0.service` (systemd). Or add to /etc/inittab: `T0:23:respawn:/sbin/getty -L ttyS0 115200 vt100`. | Tags: linux, admin | Difficulty: Intermediate

**CARD 6-17** | FRONT: What is `tty_flip_buffer`? | BACK: Kernel buffer where UART ISR pushes received data. Flipped to the line discipline for processing. Two buffers alternate (flip mechanism) to avoid locking between ISR and reader. | Tags: linux, kernel | Difficulty: Advanced

**CARD 6-18** | FRONT: Linux: how to send break? | BACK: `tcsendbreak(fd, 0)` sends 0.25-0.5s break. Kernel: `uart_ops->break_ctl(port, 1)` start break, `break_ctl(port, 0)` stop. Triggers BI flag at receiver. | Tags: linux, API | Difficulty: Intermediate

**CARD 6-19** | FRONT: /proc/tty/driver/serial — what info? | BACK: Shows all serial ports: base address, IRQ, baud, TX/RX byte counts, error counts (framing, parity, overrun, break). Quick health check. | Tags: linux, debug | Difficulty: Intermediate

**CARD 6-20** | FRONT: What is `setserial` command? | BACK: Configure/query serial port parameters beyond termios: port base address, IRQ, UART type, baud_base, flags (auto-IRQ, low-latency). Legacy tool, mostly replaced by device tree. | Tags: linux, command | Difficulty: Advanced

---

## DECK 7 — DEBUGGING (20 cards)

**CARD 7-01** | FRONT: Symptom: No output at all from UART TX. Top 3 causes? | BACK: (1) Clock not enabled for UART peripheral. (2) GPIO not configured as alternate function. (3) UART or TX enable bit not set. | Tags: debug | Difficulty: Intermediate

**CARD 7-02** | FRONT: Symptom: Garbled/random characters. #1 cause? | BACK: Baud rate mismatch. Either wrong divisor calculation, wrong clock assumption, or remote side set to different baud. Verify with oscilloscope. | Tags: debug | Difficulty: Beginner

**CARD 7-03** | FRONT: Symptom: Works in one direction only. Check what? | BACK: (1) TX/RX wiring crossover correct? (2) RX GPIO configured as AF input? (3) RX interrupt enabled? (4) CTS pin blocking TX? (5) Voltage levels compatible? | Tags: debug | Difficulty: Intermediate

**CARD 7-04** | FRONT: How to verify baud rate with oscilloscope? | BACK: Measure the narrowest pulse on TX/RX line. That = 1 bit period. Baud rate = 1/pulse_width. At 115200: expect ~8.68 µs per bit. | Tags: debug, scope | Difficulty: Intermediate

**CARD 7-05** | FRONT: Symptom: Overrun errors increasing. Fix? | BACK: (1) Enable DMA reception. (2) Increase FIFO trigger level. (3) Raise UART ISR priority. (4) Add hardware flow control (RTS/CTS). (5) Reduce baud rate. | Tags: debug, fix | Difficulty: Intermediate

**CARD 7-06** | FRONT: Symptom: Works at room temp, fails at hot temp. Cause? | BACK: Internal RC oscillator drifting with temperature. At extremes, baud rate error exceeds ±3% tolerance. Fix: use crystal oscillator or compensated (TCXO) reference. | Tags: debug, hardware | Difficulty: Advanced

**CARD 7-07** | FRONT: How to test UART without remote device? | BACK: Loopback: short TX pin to RX pin externally (or use internal loopback mode via MCR). Send data, verify you receive the same data back. | Tags: debug, test | Difficulty: Beginner

**CARD 7-08** | FRONT: Symptom: Every Nth byte is corrupted. Cause? | BACK: Likely FIFO overrun at a regular interval. Check if another periodic ISR (timer, DMA complete) is blocking UART ISR for >FIFO_depth byte times. | Tags: debug | Difficulty: Advanced

**CARD 7-09** | FRONT: Linux: how to check UART error counters? | BACK: `cat /proc/tty/driver/serial` shows tx, rx, fe (framing), pe (parity), brk (break), oe (overrun) counts per port. | Tags: debug, linux | Difficulty: Intermediate

**CARD 7-10** | FRONT: Symptom: UART stops after some minutes. Cause? | BACK: Likely uncleared error flag (silicon erratum) or DMA channel stall. Fix: add watchdog on communication timeout that fully re-initializes UART peripheral. | Tags: debug | Difficulty: Advanced

**CARD 7-11** | FRONT: Best character to send for UART waveform identification? | BACK: 'U' (0x55) — alternating bits give square wave pattern, easy to see on scope and measure bit timing. Also useful: 0xFF (all 1s shows minimum transitions), 0x00 (all 0s shows maximum LOW time). | Tags: debug, practical | Difficulty: Intermediate

**CARD 7-12** | FRONT: What tools for UART protocol analysis? | BACK: Logic analyzer (Saleae, Sigrok/PulseView), oscilloscope, USB-UART bridge + terminal software (minicom/PuTTY), Linux `interceptty` for snooping, Wireshark with serial plugin. | Tags: debug, tools | Difficulty: Intermediate

**CARD 7-13** | FRONT: Symptom: CRC errors on UART data in noisy environment. Fix? | BACK: (1) Switch to differential signaling (RS-485). (2) Add common-mode choke. (3) Use shielded twisted pair cable. (4) Add TVS diodes. (5) Reduce baud rate (wider bit time = more noise margin). | Tags: debug, EMI | Difficulty: Intermediate

**CARD 7-14** | FRONT: How to debug UART in QEMU/virtual environment? | BACK: QEMU emulates 16550 at standard IO port (0x3F8 for COM1). Use `-serial stdio` to see UART output. Use `-serial file:uart.log` to log. GDB breakpoint on serial ISR handler. | Tags: debug, QEMU | Difficulty: Advanced

**CARD 7-15** | FRONT: Symptom: Linux `write()` to serial returns success but no output on pin. | BACK: Data is in kernel buffer, not yet transmitted. Check: (1) UART driver probe'd successfully? (2) Port is runtime-PM-active? (3) FIFO enabled? (4) TX enable bit set? (5) Clock not gated? Use `cat /sys/kernel/debug/serial_*` for driver state. | Tags: debug, linux | Difficulty: Advanced

---

## DECK 8 — FORMULAS & CALCULATIONS (15 cards)

**CARD 8-01** | FRONT: Baud rate divisor formula? | BACK: Divisor = f_CLK / (BaudRate × Oversampling). Oversampling typically = 16. Round to nearest integer. | Tags: formula | Difficulty: Intermediate

**CARD 8-02** | FRONT: Actual baud rate from divisor? | BACK: Actual_Baud = f_CLK / (Divisor × Oversampling). | Tags: formula | Difficulty: Intermediate

**CARD 8-03** | FRONT: Baud rate error percentage? | BACK: Error% = (Actual_Baud - Target_Baud) / Target_Baud × 100%. Must be within ±2-3%. | Tags: formula | Difficulty: Intermediate

**CARD 8-04** | FRONT: Effective data rate (bytes/sec)? | BACK: Bytes/sec = Baud_Rate / Bits_Per_Frame. For 115200 8N1: 115200/10 = 11,520 bytes/sec. | Tags: formula | Difficulty: Beginner

**CARD 8-05** | FRONT: Time to transmit N bytes? | BACK: Time = N × Bits_Per_Frame / Baud_Rate. For 1000 bytes at 115200 8N1: 1000×10/115200 = 86.8 ms. | Tags: formula | Difficulty: Beginner

**CARD 8-06** | FRONT: Maximum inter-byte time before IDLE detection? | BACK: IDLE = 1 frame time of silence = Bits_Per_Frame / Baud_Rate. At 115200 8N1: 10/115200 = 86.8 µs. | Tags: formula | Difficulty: Intermediate

**CARD 8-07** | FRONT: ISR latency budget at given baud rate (no FIFO)? | BACK: ISR must complete within 1 byte time: Bits_Per_Frame/Baud_Rate. At 921600 8N1: 10/921600 = 10.85 µs. Very tight! | Tags: formula | Difficulty: Advanced

**CARD 8-08** | FRONT: ISR latency budget with 16-byte FIFO at trigger=14? | BACK: ISR has (16-14)=2 bytes of headroom after interrupt fires: 2 × 10/Baud. At 921600: 21.7 µs. Still tight! At 115200: 173.6 µs — comfortable. | Tags: formula | Difficulty: Advanced

**CARD 8-09** | FRONT: DMA buffer size for 1 second of 115200 8N1 data? | BACK: 11,520 bytes/sec × 1 sec = 11,520 bytes. Round up to power-of-2: 16384 bytes (16 KB). | Tags: formula, DMA | Difficulty: Intermediate

**CARD 8-10** | FRONT: Sample point formula (16× oversampling)? | BACK: Sample at middle: bit_start + 8×(1/16 × bit_period) = bit_start + 0.5×bit_period. Center of bit. | Tags: formula | Difficulty: Intermediate

---

## DECK 9 — COMPARISONS (10 cards)

**CARD 9-01** | FRONT: UART vs SPI: speed? | BACK: UART: max ~15 Mbps practical. SPI: 1–100+ MHz clock. SPI is 10-100× faster. Use SPI when speed matters on same PCB. | Tags: comparison | Difficulty: Beginner

**CARD 9-02** | FRONT: UART vs SPI: wires? | BACK: UART: 2 (TX+RX) + GND. SPI: 4 minimum (CLK+MOSI+MISO+CS) + GND. UART wins on wire count. | Tags: comparison | Difficulty: Beginner

**CARD 9-03** | FRONT: UART vs I2C: multi-device? | BACK: UART: point-to-point only (1 TX → 1 RX). I2C: up to 112 devices on same 2-wire bus with addressing. I2C wins for multi-sensor. | Tags: comparison | Difficulty: Beginner

**CARD 9-04** | FRONT: UART vs USB: when to use each? | BACK: UART: embedded debug, module AT-commands, inter-MCU. USB: PC-facing, hot-plug needed, power delivery, high speed. UART for development, USB for product. | Tags: comparison | Difficulty: Intermediate

**CARD 9-05** | FRONT: UART vs CAN: automotive? | BACK: CAN: multi-node bus, arbitration, error recovery, deterministic. UART: point-to-point only, no arbitration. UART for debug/bootloader; CAN for production vehicle network. | Tags: comparison | Difficulty: Intermediate

**CARD 9-06** | FRONT: RS-232 vs RS-485: distance? | BACK: RS-232: 15m. RS-485: 1200m. RS-485 wins massively for distance due to differential signaling and better noise immunity. | Tags: comparison | Difficulty: Beginner

**CARD 9-07** | FRONT: RS-232 vs RS-485: nodes? | BACK: RS-232: point-to-point only (1 sender, 1 receiver). RS-485: multi-drop up to 32-256 nodes on same bus. | Tags: comparison | Difficulty: Beginner

**CARD 9-08** | FRONT: UART polling vs interrupt vs DMA: CPU overhead? | BACK: Polling: 100% (busy-wait). Interrupt: ~1% (ISR per byte/FIFO). DMA: ~0.01% (1 ISR per entire transfer). | Tags: comparison | Difficulty: Intermediate

**CARD 9-09** | FRONT: Hardware flow control vs software flow control? | BACK: HW (RTS/CTS): reliable, works with binary data, needs extra wires. SW (XON/XOFF): no extra wires, but can't use with binary data containing 0x11/0x13. | Tags: comparison | Difficulty: Intermediate

**CARD 9-10** | FRONT: Crystal oscillator vs RC oscillator for UART? | BACK: Crystal: ±50 ppm (0.005%) — excellent. RC: ±1-2% — marginal at high baud rates. Crystal always preferred for reliable UART above 9600 baud. | Tags: comparison | Difficulty: Intermediate

---

END OF DOCUMENT 04 — FLASHCARDS
