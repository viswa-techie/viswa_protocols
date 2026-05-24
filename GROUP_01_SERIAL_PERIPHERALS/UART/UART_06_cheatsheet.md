# UART — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: UART | Document: 06 of 08 — Desk Reference
# Print this. Pin it. Use daily.
# ════════════════════════════════════════════════════════════════════

---

# CHEATSHEET 01 — 60-SECOND OVERVIEW

| Field | Value |
|-------|-------|
| Full Name | Universal Asynchronous Receiver/Transmitter |
| Domain | Embedded / Automotive / Industrial / Everything |
| Layer | L1 (Physical) + partial L2 (Framing) |
| Standard | TIA-232-F (2012), ITU-T V.24 |
| Speed | 110 bps – 15 Mbps (common: 115200) |
| Distance | 15m (RS-232), 1200m (RS-485), 2m (TTL) |
| Nodes | 2 (point-to-point native) |
| Topology | Point-to-Point |
| Sync/Async | Asynchronous (no clock line) |
| Duplex | Full-duplex (TX + RX simultaneous) |
| Encoding | NRZ (Non-Return-to-Zero) |
| Safety | None inherent (add app-layer CRC) |

**In One Sentence:** Two devices exchange bytes serially using start/stop bit framing at a pre-agreed speed, without a clock wire.

---

# CHEATSHEET 02 — PHYSICAL LAYER

## Voltage Levels

| Interface | Logic 1 | Logic 0 | Idle | Note |
|-----------|---------|---------|------|------|
| TTL 5V | +5V | 0V | HIGH | Direct MCU pins |
| CMOS 3.3V | +3.3V | 0V | HIGH | Modern standard |
| RS-232 | -3 to -15V | +3 to +15V | NEGATIVE | **INVERTED!** |
| RS-485 | A>B ≥200mV | B>A ≥200mV | — | Differential |

## Key Numbers

| Parameter | Value |
|-----------|-------|
| Bit period @ 115200 | 8.68 µs |
| Frame time @ 115200 8N1 | 86.8 µs |
| Byte rate @ 115200 8N1 | 11,520 bytes/sec (11.25 KB/s) |
| Clock accuracy needed | ±2-3% max mismatch |
| 16× oversampling clock @ 115200 | 1.8432 MHz |
| RS-232 max cable capacitance | 2500 pF |
| RS-485 termination | 120Ω each end |

## Wiring (3-Wire Minimum)

```
Device A        Device B
  TX ──────────→ RX
  RX ←────────── TX
  GND ────────── GND
  
  (Optional: RTS→CTS, CTS←RTS for flow control)
```

---

# CHEATSHEET 03 — FRAME FORMAT

```
IDLE │ START │ D0  D1  D2  D3  D4  D5  D6  D7 │ [PAR] │ STOP │ IDLE
 1   │   0   │←────── LSB first ──────────────→│ 0/1   │  1   │  1
```

| Config | Frame Bits | Overhead | Efficiency |
|--------|-----------|----------|------------|
| 8N1 | 10 | 2 bits | 80% |
| 8E1 | 11 | 3 bits | 72.7% |
| 8N2 | 11 | 3 bits | 72.7% |
| 7E1 | 10 | 3 bits | 70% |
| 8O1 | 11 | 3 bits | 72.7% |

---

# CHEATSHEET 04 — BAUD RATE FORMULAS

## Divisor Calculation
```
Divisor = Clock_Freq / (Baud_Rate × Oversampling)
Oversampling = 16 (standard) or 8 (some MCUs)
```

## Worked Examples

| Clock | Target Baud | Divisor | Actual Baud | Error |
|-------|-------------|---------|-------------|-------|
| 72 MHz | 115200 | 39 (39.06) | 115384.6 | +0.16% ✓ |
| 48 MHz | 115200 | 26 (26.04) | 115384.6 | +0.16% ✓ |
| 8 MHz | 9600 | 52 (52.08) | 9615.4 | +0.16% ✓ |
| 16 MHz | 115200 | 8 (8.68) | 125000 | +8.5% ✗ BAD! |
| 11.0592 MHz | 115200 | 6 (exact!) | 115200 | 0.00% ✓ |

## Sample Point
```
Sample Point = bit_start + (8/16) × bit_period = center of bit
```

## Time & Rate
```
Bit_period(µs) = 1,000,000 / Baud_Rate
Frame_time(µs) = Bits_per_frame × Bit_period
Bytes_per_sec  = Baud_Rate / Bits_per_frame
Transfer_time  = N_bytes × Bits_per_frame / Baud_Rate
```

---

# CHEATSHEET 05 — 16550 REGISTER MAP

| Offset | DLAB=0 R | DLAB=0 W | DLAB=1 | Purpose |
|--------|----------|----------|--------|---------|
| 0x00 | RBR | THR | DLL | RX data / TX data / Baud Low |
| 0x01 | IER | IER | DLM | IRQ enable / Baud High |
| 0x02 | IIR | FCR | — | IRQ ID / FIFO control |
| 0x03 | LCR | LCR | — | Line control (format) |
| 0x04 | MCR | MCR | — | Modem control |
| 0x05 | LSR | — | — | Line status (errors) |
| 0x06 | MSR | — | — | Modem status |
| 0x07 | SCR | SCR | — | Scratch |

## Critical Bits

**LCR (0x03):** `[DLAB][BRK][stick][EPS][PEN][STB][WLS1][WLS0]`
- WLS: 00=5bit, 01=6bit, 10=7bit, 11=8bit
- STB: 0=1stop, 1=2stop
- PEN: parity enable
- DLAB: baud divisor access

**LSR (0x05):** `[ERF][TEMT][THRE][BI][FE][PE][OE][DR]`
- DR: data ready (byte available)
- OE/PE/FE/BI: errors
- THRE: TX register empty (can write more)
- TEMT: TX completely done (shift reg empty)

**FCR (0x02 write):** `[trig1][trig0][—][—][DMA][TxRST][RxRST][FIFO_EN]`
- 0xC7 = enable FIFO + reset both + trigger=14

## Configuration Sequence
```
1. LCR = 0x80        (DLAB=1)
2. DLL = divisor_low  (baud rate)
3. DLM = divisor_high
4. LCR = 0x03        (DLAB=0, 8N1)
5. FCR = 0xC7        (FIFO on, reset, trigger=14)
6. MCR = 0x0B        (DTR, RTS, OUT2=IRQ enable)
7. IER = 0x01        (RX interrupt enable)
```

---

# CHEATSHEET 06 — LINUX COMMANDS

## Setup & Inspection
```bash
# List serial ports
dmesg | grep -i tty
ls /dev/tty{S,USB,ACM,AMA}*

# Check current settings
stty -F /dev/ttyS0 -a

# Configure 115200-8N1 raw mode
stty -F /dev/ttyS0 115200 cs8 -parenb -cstopb raw -echo

# Quick test — send data
echo "Hello" > /dev/ttyS0

# Quick test — receive data
cat /dev/ttyS0
```

## Monitoring
```bash
# Monitor with minicom
minicom -D /dev/ttyS0 -b 115200

# Monitor with picocom (simpler)
picocom -b 115200 /dev/ttyS0

# Monitor with screen
screen /dev/ttyS0 115200

# Hex dump incoming data
xxd /dev/ttyS0

# Log to file with timestamp
cat /dev/ttyS0 | ts '[%Y-%m-%d %H:%M:%.S]' > uart.log
```

## Debugging
```bash
# Check error counters
cat /proc/tty/driver/serial

# Check driver info
cat /sys/class/tty/ttyS0/device/driver/module/parameters/*

# Enable kernel debug logging
echo 8 > /proc/sys/kernel/printk
echo "file 8250_port.c +p" > /sys/kernel/debug/dynamic_debug/control

# Trace serial ISR
trace-cmd record -e irq -f 'name == "serial"'

# Check port info (legacy)
setserial /dev/ttyS0

# USB-serial info
lsusb -v | grep -A5 "CDC"
udevadm info /dev/ttyUSB0
```

## Loopback Test
```bash
# Short TX to RX pin externally, then:
stty -F /dev/ttyS0 115200 raw -echo
echo "test123" > /dev/ttyS0 &
cat /dev/ttyS0  # Should show "test123"
```

---

# CHEATSHEET 07 — CODE SNIPPETS

## Bare Metal Init (STM32F4)
```c
// Enable clocks
RCC->APB2ENR |= RCC_APB2ENR_USART1EN;
RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
// GPIO AF7 for PA9(TX), PA10(RX)
GPIOA->MODER |= (2<<18)|(2<<20);
GPIOA->AFR[1] |= (7<<4)|(7<<8);
// 115200 @ 84MHz APB2
USART1->BRR = (45<<4)|9;  // 84M/(16*115200)=45.57
USART1->CR1 = USART_CR1_TE|USART_CR1_RE|USART_CR1_UE;
```

## Bare Metal TX (Polling)
```c
void uart_putc(char c) {
    while (!(USART1->SR & USART_SR_TXE));
    USART1->DR = c;
}
```

## Bare Metal RX (Polling)
```c
char uart_getc(void) {
    while (!(USART1->SR & USART_SR_RXNE));
    return USART1->DR;
}
```

## Bare Metal RX (Interrupt)
```c
void USART1_IRQHandler(void) {
    if (USART1->SR & USART_SR_RXNE) {
        uint8_t byte = USART1->DR;
        ring_buf_push(&rx_ring, byte);
    }
    if (USART1->SR & USART_SR_ORE)
        (void)USART1->DR;  // Clear overrun
}
```

## Linux Userspace Open
```c
int fd = open("/dev/ttyS0", O_RDWR|O_NOCTTY);
struct termios t;
tcgetattr(fd, &t);
cfsetispeed(&t, B115200);
cfsetospeed(&t, B115200);
t.c_cflag = CS8|CLOCAL|CREAD;
t.c_iflag = 0;
t.c_oflag = 0;
t.c_lflag = 0;
t.c_cc[VMIN] = 1;
t.c_cc[VTIME] = 10;
tcsetattr(fd, TCSANOW, &t);
```

## Linux TX/RX
```c
write(fd, "Hello\n", 6);

char buf[256];
int n = read(fd, buf, sizeof(buf));
```

---

# CHEATSHEET 08 — ERROR QUICK REFERENCE

| Error | Cause | Detection | Fix |
|-------|-------|-----------|-----|
| Framing | Stop bit = 0 | LSR.FE | Fix baud rate / config mismatch |
| Overrun | CPU too slow | LSR.OE | Use DMA / bigger FIFO / faster ISR |
| Parity | Bit corruption | LSR.PE | Check cable / EMI / add CRC |
| Break | Line LOW > frame | LSR.BI | Intentional signal or wire fault |
| Noise | Samples disagree | LSR.NE | Improve signal integrity |

## Quick Debug Checklist
```
□ Clock enabled for UART peripheral?
□ GPIO configured as Alternate Function (not plain GPIO)?
□ Correct AF number selected (varies by MCU)?
□ TX and RX wires CROSSED (TX→RX, RX→TX)?
□ Common GND connected?
□ Baud rate same on BOTH sides?
□ Same config: data bits, parity, stop bits?
□ Voltage levels compatible (3.3V vs 5V vs RS-232)?
□ FIFO enabled? DMA enabled?
□ No flow control blocking (CTS stuck)?
□ Terminal in RAW mode (not canonical)?
```

---

# CHEATSHEET 09 — PERFORMANCE AT A GLANCE

| Baud | Byte/sec | KB/s | 1KB Transfer | 64KB Transfer |
|------|---------|------|-------------|---------------|
| 9600 | 960 | 0.94 | 1.04 sec | 66.7 sec |
| 19200 | 1920 | 1.88 | 0.52 sec | 33.3 sec |
| 115200 | 11520 | 11.25 | 86.8 ms | 5.6 sec |
| 460800 | 46080 | 45 | 21.7 ms | 1.4 sec |
| 921600 | 92160 | 90 | 10.9 ms | 0.7 sec |
| 3000000 | 300000 | 293 | 3.3 ms | 0.2 sec |

---

# CHEATSHEET 10 — INTERVIEW-DAY CARD

## 10 Things to Remember About UART
1. Asynchronous — no clock line — syncs on START bit falling edge
2. Frame: START(0) + DATA(LSB first) + [PARITY] + STOP(1)
3. Idle = HIGH; broken wire → LOW = detectable fault
4. 115200-8N1 is the universal default
5. 16× oversampling → sample at bit center → ±3 sample tolerance
6. Baud rate accuracy: ±2-3% max total mismatch
7. Errors: Framing (baud wrong), Overrun (CPU slow), Parity (corruption)
8. 16550 UART register map = universal standard
9. RS-232 voltages are INVERTED from TTL logic
10. No addressing, no arbitration, no error correction — just raw serial

## 5 Numbers You Must Know
1. Max speed: 115200 (traditional), 1-15 Mbps (modern)
2. Max distance: 15m (RS-232), 1200m (RS-485)
3. Frame overhead: 20% for 8N1 (2 of 10 bits)
4. Bit time @ 115200: 8.68 µs
5. 16550 FIFO: 16 bytes

## 3 Trick Questions
1. Q: "Is 115200 the maximum?" A: No — modern SoCs do 1-15 Mbps
2. Q: "Is RS-232 same as UART?" A: No — UART=protocol, RS-232=electrical standard
3. Q: "Does parity correct errors?" A: No — only DETECTS (1-bit), never corrects

## The 1 Analogy
> "UART is like two people with walkie-talkies who agreed on speaking speed beforehand — each word starts with a click (start bit) and ends with a pause (stop bit)."

---

END OF DOCUMENT 06 — CHEATSHEET
