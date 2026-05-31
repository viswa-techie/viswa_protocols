# SPI — CHEATSHEET & QUICK REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 06 of 08 — Desk Reference
# ════════════════════════════════════════════════════════════════════

---

# CHEATSHEET 01 — 60-SECOND OVERVIEW

| Field | Value |
|-------|-------|
| Full Name | Serial Peripheral Interface |
| Creator | Motorola, 1984 (68HC11) |
| Standard | None (de facto). JEDEC xSPI for Flash. |
| Wires | 4: SCLK, MOSI, MISO, CS̄ |
| Speed | 1–200+ MHz |
| Duplex | Full (standard), Half (Dual/Quad/Octal) |
| Topology | Master-Slave, shared bus + individual CS̄ |
| Overhead | 0% — no start/stop/address/ACK bits |
| Error Detection | None built-in |
| Distance | PCB: 10–30 cm. Cable: up to ~1 m |

**In One Sentence:** Master clocks data in and out of slaves simultaneously via shift registers, selecting slaves with dedicated chip-select lines.

---

# CHEATSHEET 02 — THE FOUR MODES

| Mode | CPOL | CPHA | Idle | Sample Edge | Common Devices |
|------|------|------|------|-------------|----------------|
| 0 | 0 | 0 | LOW | Rising (1st) | Flash, ADC, DAC, SD card (~80%) |
| 1 | 0 | 1 | LOW | Falling (2nd) | Some sensors |
| 2 | 1 | 0 | HIGH | Falling (1st) | Rare |
| 3 | 1 | 1 | HIGH | Rising (2nd) | IMU (ADXL345), some Flash |

**Rule:** When in doubt, try Mode 0 first.

---

# CHEATSHEET 03 — WIRING

```
MASTER              SLAVE
 SCLK ─────────────→ SCLK
 MOSI ─────────────→ MOSI/DIN
 MISO ←───────────── MISO/DOUT
 CS̄0 ─────────────→ CS̄
 GND ──────────────── GND
 
Multi-slave: Share SCLK/MOSI/MISO, separate CS̄ per slave.
```

---

# CHEATSHEET 04 — SPI FLASH COMMANDS

| Command | Opcode | Sequence |
|---------|--------|----------|
| Read JEDEC ID | 0x9F | CS̄↓ [9F] → [MFR][TYPE][CAP] CS̄↑ |
| Read Status | 0x05 | CS̄↓ [05] → [status] CS̄↑ |
| Write Enable | 0x06 | CS̄↓ [06] CS̄↑ |
| Read Data | 0x03 | CS̄↓ [03][A2][A1][A0] → [data...] CS̄↑ |
| Fast Read | 0x0B | CS̄↓ [0B][A2][A1][A0][dummy] → [data...] CS̄↑ |
| Page Program | 0x02 | CS̄↓ [02][A2][A1][A0][data..256B] CS̄↑ |
| Sector Erase 4K | 0x20 | CS̄↓ [20][A2][A1][A0] CS̄↑ |
| Chip Erase | 0xC7 | CS̄↓ [C7] CS̄↑ |

---

# CHEATSHEET 05 — STM32 SPI REGISTERS

| Register | Key Bits |
|----------|----------|
| CR1 | SPE, MSTR, BR[2:0], CPOL, CPHA, SSM, SSI, LSBFIRST, DFF, CRCEN |
| CR2 | TXEIE, RXNEIE, ERRIE, SSOE, TXDMAEN, RXDMAEN |
| SR | BSY, OVR, MODF, CRCERR, TXE, RXNE |
| DR | 8/16-bit data read/write |

### Baud Rate: `SPI_CLK = APB_CLK / 2^(BR+1)`

| BR | Divider | @84MHz | @42MHz |
|----|---------|--------|--------|
| 000 | /2 | 42M | 21M |
| 001 | /4 | 21M | 10.5M |
| 010 | /8 | 10.5M | 5.25M |
| 011 | /16 | 5.25M | 2.625M |
| 100 | /32 | 2.625M | 1.3M |

### Init Sequence
```
1. RCC: Enable SPI + GPIO clocks
2. GPIO: Set SCLK/MOSI/MISO as AF (correct AF#)
3. GPIO: Set CS̄ as push-pull output, default HIGH
4. CR1: MSTR=1, BR=xxx, CPOL, CPHA, SSM=1, SSI=1
5. CR1: SPE=1 (enable last!)
```

---

# CHEATSHEET 06 — CODE SNIPPETS

### Bare Metal Transfer (Polling)
```c
uint8_t spi_transfer(uint8_t tx) {
    while (!(SPI1->SR & SPI_SR_TXE));
    SPI1->DR = tx;
    while (!(SPI1->SR & SPI_SR_RXNE));
    return SPI1->DR;
}
```

### Read Register
```c
uint8_t spi_read_reg(uint8_t reg) {
    CS_LOW();
    spi_transfer(reg | 0x80);    // bit7=1 for read
    uint8_t val = spi_transfer(0xFF); // dummy
    CS_HIGH();
    return val;
}
```

### Write Register
```c
void spi_write_reg(uint8_t reg, uint8_t val) {
    CS_LOW();
    spi_transfer(reg & 0x7F);   // bit7=0 for write
    spi_transfer(val);
    CS_HIGH();
}
```

### Flash JEDEC ID Read
```c
uint32_t read_jedec_id(void) {
    CS_LOW();
    spi_transfer(0x9F);
    uint8_t mfr  = spi_transfer(0xFF);
    uint8_t type = spi_transfer(0xFF);
    uint8_t cap  = spi_transfer(0xFF);
    CS_HIGH();
    return (mfr << 16) | (type << 8) | cap;
}
```

### Linux Userspace
```c
int fd = open("/dev/spidev0.0", O_RDWR);
uint8_t mode = SPI_MODE_0;
uint32_t speed = 10000000;
ioctl(fd, SPI_IOC_WR_MODE, &mode);
ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed);

struct spi_ioc_transfer xfer = {
    .tx_buf = (unsigned long)tx,
    .rx_buf = (unsigned long)rx,
    .len = len,
    .speed_hz = speed,
};
ioctl(fd, SPI_IOC_MESSAGE(1), &xfer);
```

---

# CHEATSHEET 07 — LINUX COMMANDS

```bash
# List SPI devices
ls /dev/spidev*
ls /sys/bus/spi/devices/

# Check SPI masters
ls /sys/class/spi_master/

# Device info
cat /sys/bus/spi/devices/spi0.0/modalias

# Quick transfer with spi-pipe
echo -ne '\x9F' | spi-pipe -d /dev/spidev0.0 -s 1000000 -b 1 | xxd

# Enable SPI overlay (RPi)
dtoverlay spi0-1cs
```

---

# CHEATSHEET 08 — THROUGHPUT TABLE

| Clock | Standard | Dual | Quad | Octal | Octal DDR |
|-------|---------|------|------|-------|-----------|
| 1 MHz | 1 Mbps | 2 | 4 | 8 | 16 Mbps |
| 10 MHz | 10 | 20 | 40 | 80 | 160 |
| 50 MHz | 50 | 100 | 200 | 400 | 800 |
| 100 MHz | 100 | 200 | 400 | 800 | 1600 |
| 200 MHz | 200 | 400 | 800 | 1600 | 3200 |

**Transfer time for 1 KB @ 10 MHz standard:** 1024×8/10M = 0.82 ms

---

# CHEATSHEET 09 — ERROR QUICK REFERENCE

| Error | Flag | Cause | Fix |
|-------|------|-------|-----|
| Overrun | OVR | CPU too slow reading RX | DMA, bigger FIFO |
| Mode Fault | MODF | SS̄ pulled LOW in master mode | Check wiring, multi-master |
| CRC Error | CRCERR | Hardware CRC mismatch | Retransmit, check signal |
| All 0xFF | — | CS̄ not asserted / MISO floating | Check CS̄ GPIO, slave power |
| Bit-shifted | — | CPHA mismatch | Change SPI mode |
| Works slow, fails fast | — | Signal integrity | Add termination, check traces |

### Debug Checklist
```
□ SPI clock enabled (RCC)?
□ GPIO set to Alternate Function (correct AF#)?
□ CS̄ GPIO configured as push-pull output?
□ CS̄ default HIGH (pull-up)?
□ CPOL/CPHA match slave datasheet?
□ Speed ≤ slave's max rated frequency?
□ MSB/LSB order matches slave?
□ Wait for TXE before write, RXNE before read?
□ Wait for BSY=0 before CS̄ HIGH?
□ Shared bus: mutex protecting transactions?
```

---

# CHEATSHEET 10 — INTERVIEW-DAY CARD

## 10 Things to Remember About SPI
1. 4 wires: SCLK, MOSI, MISO, CS̄ (active LOW)
2. Synchronous — master provides clock, no baud rate issues
3. Shift-register exchange — always bidirectional, send dummy to read
4. 4 modes (CPOL/CPHA) — Mode 0 is default for ~80% of devices
5. Zero overhead — 100% data efficiency (no start/stop/address)
6. No error detection — add CRC/readback at application layer
7. Speed: 1–200 MHz (100–1000× faster than UART)
8. CS̄ per slave — hardware addressing, no software addresses
9. MISO is tri-state — only driven when CS̄ is LOW
10. No formal standard — de facto convention from Motorola

## 5 Key Numbers
1. Speed range: 1–200+ MHz
2. Overhead: 0%
3. Max practical distance: ~1 m
4. Typical slaves: 4–16
5. STM32 prescaler: APB/2 to APB/256

## 3 Trick Questions
1. "Is SPI a standard?" → No formal standard. De facto only.
2. "Is SPI always full-duplex?" → Standard: yes. Dual/Quad: no (half-duplex).
3. "Can SPI detect errors?" → Protocol: no. Some MCUs have HW CRC (STM32).

## The 1 Analogy
> "SPI is like two people passing cards in a circle — each clock tick, both pass one card to each other simultaneously. The master decides the pace."

---

END OF CHEATSHEET
