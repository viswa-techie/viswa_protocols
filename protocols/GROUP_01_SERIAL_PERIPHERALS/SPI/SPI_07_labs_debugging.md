# SPI — LABS & DEBUGGING PLAYBOOK
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 07 of 08 — Hands-On Labs + Debug Guide
# ════════════════════════════════════════════════════════════════════

---

# PART A — PROGRESSIVE LAB EXERCISES

---

## LAB 01: SPI LOOPBACK TEST (Beginner, 30 min)

### Objective
Verify SPI TX/RX by connecting MOSI→MISO on the same MCU.

### Procedure
1. Configure SPI1: Master, Mode 0, 1 MHz, 8-bit, software CS̄
2. Connect MOSI pin → MISO pin with jumper wire
3. Send test patterns: 0x55, 0xAA, 0x00, 0xFF, 0x42
4. Verify each received byte matches sent byte
5. Try all 4 SPI modes — all should work in loopback

### Validation
```c
uint8_t test[] = {0x55, 0xAA, 0x00, 0xFF, 0x42};
for (int i = 0; i < 5; i++) {
    uint8_t rx = spi_transfer(test[i]);
    assert(rx == test[i]);  // Must match!
}
```

### Stretch: Try different speeds (1, 5, 10, 20 MHz). At what speed does loopback fail?

---

## LAB 02: READ FLASH JEDEC ID (Beginner, 45 min)

### Objective
Communicate with SPI NOR Flash and read its identification.

### Hardware
- MCU + SPI NOR Flash (W25Q128 or similar)
- 6 wires: SCLK, MOSI, MISO, CS̄, VCC, GND

### Procedure
```c
CS_LOW();
spi_transfer(0x9F);                    // JEDEC ID command
uint8_t mfr  = spi_transfer(0xFF);    // Manufacturer
uint8_t type = spi_transfer(0xFF);    // Device type
uint8_t cap  = spi_transfer(0xFF);    // Capacity
CS_HIGH();
printf("ID: %02X %02X %02X\n", mfr, type, cap);
```

### Expected Results
| Flash | MFR | Type | Cap | Meaning |
|-------|-----|------|-----|---------|
| W25Q128 | 0xEF | 0x40 | 0x18 | Winbond, 128 Mbit |
| W25Q64 | 0xEF | 0x40 | 0x17 | Winbond, 64 Mbit |
| IS25LP128 | 0x9D | 0x60 | 0x18 | ISSI, 128 Mbit |

### If you get 0xFF 0xFF 0xFF: CS̄ not reaching Flash, wrong wiring, or Flash not powered.

---

## LAB 03: FLASH READ/WRITE/ERASE CYCLE (Intermediate, 1.5 hours)

### Objective
Perform complete Flash operations: erase → write enable → program → read back → verify.

### Procedure
```c
// Step 1: Erase sector at address 0x000000
spi_cmd(0x06);                    // Write Enable
spi_cmd_addr(0x20, 0x000000);    // Sector Erase 4KB
wait_wip_clear();                 // Poll status until done (~30ms)

// Step 2: Program 256 bytes
spi_cmd(0x06);                    // Write Enable again
CS_LOW();
spi_transfer(0x02);               // Page Program
spi_transfer(0x00); spi_transfer(0x00); spi_transfer(0x00); // Address
for (int i = 0; i < 256; i++)
    spi_transfer(i);               // Data: 0x00..0xFF
CS_HIGH();
wait_wip_clear();                  // ~0.5ms

// Step 3: Read back and verify
CS_LOW();
spi_transfer(0x03);               // Read Data
spi_transfer(0x00); spi_transfer(0x00); spi_transfer(0x00);
for (int i = 0; i < 256; i++) {
    uint8_t val = spi_transfer(0xFF);
    assert(val == i);
}
CS_HIGH();
```

### Key Learning: Write Enable (0x06) must precede EVERY write/erase. WIP polling prevents data corruption.

---

## LAB 04: MULTI-SLAVE BUS (Intermediate, 1 hour)

### Objective
Connect 2+ SPI slaves on shared bus with individual CS̄ management.

### Setup
```
MCU                Flash (CS0)      Sensor (CS1)
 SCLK ────────────→ SCLK            SCLK
 MOSI ────────────→ MOSI            MOSI
 MISO ←────────── MISO             MISO
 PA4 ─────────────→ CS̄
 PA3 ──────────────────────────────→ CS̄
```

### Exercises
1. Read Flash ID → deselect → Read sensor WHO_AM_I → deselect
2. Verify: reading Flash doesn't corrupt sensor state
3. Intentionally activate both CS̄ LOW → observe bus contention on scope
4. Add RTOS mutex protection for shared bus access

### Critical: Never assert two CS̄ simultaneously!

---

## LAB 05: DMA SPI TRANSFER (Intermediate, 2 hours)

### Objective
Implement zero-CPU-overhead SPI Flash read using DMA.

### Concept
```
CPU: Setup DMA → Start → Free to do other work → ISR on completion
DMA TX: sends command+address+dummy from RAM → SPI_DR
DMA RX: captures response from SPI_DR → RAM
```

### Implementation Outline
```c
void spi_dma_read_flash(uint32_t addr, uint8_t *buf, uint16_t len) {
    uint8_t cmd[4] = {0x03, addr>>16, addr>>8, addr};
    
    CS_LOW();
    // Phase 1: Send command+address (DMA or polling, 4 bytes)
    for (int i = 0; i < 4; i++) spi_transfer(cmd[i]);
    
    // Phase 2: DMA for data read
    DMA_RX->M0AR = (uint32_t)buf;
    DMA_TX->M0AR = (uint32_t)dummy_buf;  // all 0xFF
    DMA_RX->NDTR = DMA_TX->NDTR = len;
    DMA_RX->CR |= DMA_EN;
    DMA_TX->CR |= DMA_EN;
    SPI1->CR2 |= SPI_CR2_TXDMAEN | SPI_CR2_RXDMAEN;
    
    // Wait (or use completion ISR)
    while (!(DMA_RX->ISR & DMA_FLAG_TC));
    CS_HIGH();
}
```

### Measure: CPU utilization during 4KB DMA transfer vs polling transfer.

---

## LAB 06: LINUX SPIDEV USERSPACE (Intermediate, 1.5 hours)

### Objective
Control SPI Flash from Linux userspace using spidev.

### Part 1: Setup
```bash
# Check for spidev device
ls /dev/spidev*
# If not present, enable in device tree overlay
# Raspberry Pi: dtparam=spi=on in /boot/config.txt
```

### Part 2: C Program
```c
#include <linux/spi/spidev.h>
#include <sys/ioctl.h>
#include <fcntl.h>

int main() {
    int fd = open("/dev/spidev0.0", O_RDWR);
    uint8_t mode = SPI_MODE_0;
    uint32_t speed = 10000000;
    ioctl(fd, SPI_IOC_WR_MODE, &mode);
    ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed);
    
    // Read JEDEC ID
    uint8_t tx[] = {0x9F, 0, 0, 0};
    uint8_t rx[4] = {0};
    struct spi_ioc_transfer xfer = {
        .tx_buf = (unsigned long)tx,
        .rx_buf = (unsigned long)rx,
        .len = 4,
        .speed_hz = speed,
    };
    ioctl(fd, SPI_IOC_MESSAGE(1), &xfer);
    printf("JEDEC ID: %02X %02X %02X\n", rx[1], rx[2], rx[3]);
    close(fd);
}
```

### Part 3: Python (quick test)
```python
import spidev
spi = spidev.SpiDev()
spi.open(0, 0)
spi.max_speed_hz = 10000000
spi.mode = 0
resp = spi.xfer2([0x9F, 0, 0, 0])
print(f"JEDEC ID: {resp[1]:02X} {resp[2]:02X} {resp[3]:02X}")
spi.close()
```

---

## LAB 07: SPI PROTOCOL ANALYZER (Advanced, 1.5 hours)

### Objective
Capture and decode SPI transactions with logic analyzer.

### Equipment
- Saleae Logic / FX2 clone + PulseView
- Connect probes: SCLK, MOSI, MISO, CS̄, GND

### Exercises
1. Capture JEDEC ID read (0x9F) — decode all 4 bytes
2. Capture Flash read (0x03) — identify command, address, data phases
3. Capture Page Program (0x02) — verify write enable preceded it
4. Verify timing: CS̄ setup time, CS̄ hold time, clock frequency
5. Change SPI mode → capture → see data corruption in decode

### Protocol Analyzer Settings
```
Protocol: SPI
SCLK channel: CH0
MOSI channel: CH1
MISO channel: CH2
CS̄ channel: CH3 (active low enable)
Mode: CPOL=0, CPHA=0
Bit order: MSB first
Word size: 8
```

---

## LAB 08: QSPI BOOT FLASH ON AUTOMOTIVE PLATFORM (Advanced, 2 hours)

### Objective
Explore QSPI NOR Flash boot on automotive SoC (e.g., Qualcomm/NXP dev board).

### Exercises
1. Identify QSPI Flash device on board (schematic/dmesg)
2. Read Flash info via MTD:
```bash
cat /proc/mtd
mtd_debug info /dev/mtd0
cat /sys/bus/spi/devices/spi0.0/modalias
```
3. Read SFDP (Serial Flash Discoverable Parameters):
```bash
dd if=/dev/mtd0 bs=256 count=1 | xxd | head
```
4. Dump boot sector and analyze (bootloader signature)
5. Measure read throughput: `dd if=/dev/mtd0 of=/dev/null bs=4096 count=1000`
6. Compare standard SPI read vs QSPI read timing

---

# PART B — DEBUGGING PLAYBOOK

---

## DEBUG 01: NO COMMUNICATION (All 0xFF or 0x00)

### Decision Tree
```
SPI reads all 0xFF or 0x00
├─ Check: Is CS̄ going LOW? (scope)
│   ├─ NO → GPIO misconfigured (input not output), wrong pin, pull-up too strong
│   └─ YES → Check: Is SCLK toggling?
│       ├─ NO → SPI clock not enabled, SPE=0, wrong AF
│       └─ YES → Check: Is MOSI sending data?
│           ├─ NO → TX not loaded, DR not written, DMA not started
│           └─ YES → Check: Is slave responding on MISO?
│               ├─ NO → Slave not powered, wrong CS̄, slave in reset
│               └─ YES but 0xFF → Slave sees wrong command (mode mismatch)
```

### Most Common Causes (ranked)
1. CS̄ GPIO not configured as output (50%)
2. SPI peripheral clock not enabled in RCC (20%)
3. GPIO AF number wrong (15%)
4. Slave not powered or wrong VCC (10%)
5. MOSI/MISO swapped (5%)

---

## DEBUG 02: CORRUPTED/SHIFTED DATA

### Symptoms
- Read 0x84 instead of 0x42 (shifted left)
- Read valid but wrong values
- First byte correct, rest wrong

### Causes
1. **CPOL/CPHA mismatch** → shifted by 1 bit → all data wrong
2. **MSB vs LSB mismatch** → bit-reversed
3. **Extra clock pulse** → CS̄ timing issue or hardware CS̄ glitch
4. **Wrong word size** → 16-bit mode reading 8-bit device
5. **Not waiting for BSY=0** before CS̄ deassert → last bit truncated

### Fix Procedure
```
1. Set lowest speed (1 MHz), verify with logic analyzer
2. Send 0x55 (01010101) → check scope for alternating pattern
3. Try all 4 modes systematically — one will produce correct data
4. Check bit order (MSB/LSB) in both MCU config and device datasheet
5. Check word size matches
```

---

## DEBUG 03: OVERRUN ERRORS (OVR)

### Symptoms
- OVR flag set in SR
- Missing bytes in received data
- Works at low speed, fails at high speed

### Solutions (in order)
1. **Enable FIFO** — buys N× more time per byte
2. **Use DMA** — hardware handles data movement
3. **Raise interrupt priority** — faster ISR response
4. **Reduce SPI speed** — more time per byte
5. **Read DR immediately** in ISR — don't process, just buffer

### Timing Check
```
At 10 MHz, 8-bit: byte time = 800 ns
Without FIFO: ISR must respond in < 800 ns!
With 8-byte FIFO: ISR has < 6.4 µs — much more feasible
```

---

## DEBUG 04: WORKS AT LOW SPEED, FAILS AT HIGH SPEED

### Root Causes
1. **Setup/hold time violation**: t_CO + t_SU > clock period
2. **Signal ringing**: impedance mismatch causes false clock edges
3. **Crosstalk**: adjacent traces coupling noise
4. **Device max speed exceeded**: check datasheet f_max
5. **Clock-data skew**: trace length mismatch

### Diagnostic
```
1. Oscilloscope on SCLK at failing speed — look for:
   - Ringing (>30% overshoot)
   - Slow rise time (>20% of period)
   - Jitter
2. Check MISO at sample point — is it stable?
3. Calculate: f_max = 1/(t_CO + t_SU + t_prop)
4. Add 33Ω series termination on SCLK → check improvement
5. Try reducing to 80% of failing speed → passes = margin issue
```

---

## DEBUG 05: INTERMITTENT FAILURES

### Pattern Analysis
| Pattern | Cause | Fix |
|---------|-------|-----|
| Fails under CPU load | ISR latency / priority | DMA, raise priority |
| Fails with temperature | Timing margin / crystal drift | Derate speed 20% |
| Fails after hours | Memory leak in driver | Check DMA descriptor recycling |
| Fails during motor on | EMI | Shielding, ferrite, ground plane |
| Random CS̄ glitches | Noise coupling | Filter, pull-up, shorter trace |
| Fails in multi-task | Mutex missing | Add SPI bus mutex |

---

## DEBUG 06: LINUX SPI ISSUES

### spidev not found
```bash
# Check kernel config
zcat /proc/config.gz | grep SPI
# Enable: CONFIG_SPI_SPIDEV=y/m

# Check device tree
dtc -I fs /sys/firmware/devicetree/base | grep spidev

# Load module
modprobe spidev
```

### Permission denied
```bash
sudo usermod -aG spi $USER  # Add to spi group
# Or: chmod 666 /dev/spidev0.0 (temporary)
```

### Wrong speed reported
```bash
# Actual speed may differ from requested (hardware prescaler)
# Read actual: check kernel logs
dmesg | grep spi
# Some controllers only support power-of-2 divisors
```

---

## DEBUG 07: QSPI/FLASH SPECIFIC

### Flash stuck in QPI mode
```
Problem: Flash was put in 4-4-4 mode, now standard SPI commands fail
Fix: Send exit QPI command (0xFF) using 4-line mode
     Or: power cycle the Flash (if VCC can be toggled)
     Or: Some Flash have hardware reset pin
```

### Flash write fails silently
```
Checklist:
1. Was Write Enable (0x06) sent before write? (WEL bit)
2. Is the sector/block write-protected? (status register)
3. Was previous operation complete? (WIP bit = 0)
4. Is page boundary crossed? (wraps at 256-byte boundary!)
5. Is VCC within write voltage range? (some need >2.7V)
```

### Flash read returns stale data after write
```
Problem: Cache coherency — CPU cache has old data
Fix: Invalidate cache after Flash write
     Or: Use uncached memory mapping for Flash reads
```

---

# PART C — QUICK DEBUG FLOWCHART

```
SPI NOT WORKING?
│
├─ Nothing received (0xFF)
│   ├─ Scope: CS̄ going LOW? → No: fix GPIO
│   ├─ Scope: SCLK toggling? → No: enable SPI/clock
│   └─ Scope: MISO responding? → No: check slave power/wiring
│
├─ Wrong data received
│   ├─ Bit-shifted? → Check CPHA, extra clocks
│   ├─ Bit-reversed? → Check MSB/LSB setting
│   └─ Random garbage? → Check CPOL+CPHA (try all 4 modes)
│
├─ Data lost (OVR)
│   ├─ Enable FIFO
│   ├─ Use DMA
│   └─ Reduce speed
│
├─ Works slow, fails fast
│   ├─ Check slave max speed
│   ├─ Add series termination
│   └─ Check signal integrity
│
└─ Intermittent
    ├─ Add error counters
    ├─ Check RTOS mutex
    └─ Check EMI/temperature
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
