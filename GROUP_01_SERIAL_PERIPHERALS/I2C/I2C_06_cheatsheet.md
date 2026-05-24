# I2C — CHEATSHEET (Quick Reference)
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C | Document: 06 of 08 — One-Page Reference
# Print this. Pin it. Use it daily.
# ════════════════════════════════════════════════════════════════════

---

## 1. I2C AT A GLANCE

```
┌────────────────────────────────────────────────┐
│  I2C = 2 wires + addressing + ACK             │
│  SDA (data) + SCL (clock) + external pull-ups  │
│  Open-drain, half-duplex, multi-master         │
│  MSB first, 9 clocks per byte (8 data + ACK)  │
└────────────────────────────────────────────────┘
```

| Parameter | Value |
|-----------|-------|
| Wires | 2 (SDA + SCL) + GND |
| Addressing | 7-bit (112 usable) or 10-bit |
| Speed | 100k / 400k / 1M / 3.4M / 5M |
| Duplex | Half |
| Error detection | ACK/NACK per byte |
| Pull-up (default) | 4.7KΩ |
| Bus cap max | 400 pF |

---

## 2. SPEED MODES TABLE

| Mode | Speed | Pull-Up | Rise Time | Use Case |
|------|-------|---------|-----------|----------|
| Standard (Sm) | 100 kHz | 4.7KΩ | ≤1 µs | EEPROM, RTC |
| Fast (Fm) | 400 kHz | 2.2KΩ | ≤300 ns | Sensors, codecs |
| Fast+ (Fm+) | 1 MHz | 1KΩ | ≤120 ns | High-perf sensors |
| High-Speed (HS) | 3.4 MHz | Active | ≤? | Special peripherals |
| Ultra-Fast (UFm) | 5 MHz | None (push-pull) | — | LED drivers (write-only!) |

---

## 3. BUS CONDITIONS

```
START:  SDA ‾‾\__ while SCL ‾‾‾   (SDA falls, SCL HIGH)
STOP:   SDA __/‾‾ while SCL ‾‾‾   (SDA rises, SCL HIGH)
ACK:    SDA = LOW  on 9th clock    (receiver pulls LOW)
NACK:   SDA = HIGH on 9th clock    (receiver releases)
DATA:   SDA stable while SCL HIGH  (changes only when SCL LOW)
```

---

## 4. TRANSACTION PATTERNS

**Write (master → slave):**
```
S [Addr+W] ACK [Reg] ACK [Data] ACK P
```

**Read (slave → master):**
```
S [Addr+W] ACK [Reg] ACK Sr [Addr+R] ACK [Data] NACK P
```

**Multi-byte Read:**
```
S [Addr+W] ACK [Reg] ACK Sr [Addr+R] ACK [D0] ACK [D1] ACK [D2] NACK P
                                            ↑ master ACK    ↑ master NACK (last)
```

---

## 5. COMMON DEVICE ADDRESSES

| Device | Address | Notes |
|--------|---------|-------|
| EEPROM (24Cxx) | 0x50–0x57 | A2/A1/A0 pins |
| Temp (LM75/TMP112) | 0x48–0x4F | A2/A1/A0 |
| RTC (DS3231) | 0x68 | Fixed |
| IMU (MPU6050) | 0x68/0x69 | AD0 pin |
| OLED (SSD1306) | 0x3C/0x3D | SA0 pin |
| GPIO (PCF8574) | 0x20–0x27 | A2/A1/A0 |
| ADC (ADS1115) | 0x48–0x4B | ADDR pin |
| I2C Mux (PCA9548A) | 0x70–0x77 | A2/A1/A0 |
| Touch (GT911) | 0x5D/0x14 | INT timing |
| Audio (TAS5805M) | 0x2C–0x2F | ADDR pins |

**Address conversion:** 7-bit addr × 2 = write byte; × 2 + 1 = read byte
- 0x68 → Write: 0xD0, Read: 0xD1

---

## 6. PULL-UP RESISTOR CALCULATOR

```
Rp_min = (VDD - 0.4V) / I_OL
       = (3.3 - 0.4) / 3mA = 967Ω → use 1KΩ

Rp_max = t_rise / (0.8473 × C_bus)
  Standard: 1000ns / (0.8473 × C)
  Fast:      300ns / (0.8473 × C)
  Fast+:     120ns / (0.8473 × C)
```

**Quick Reference:**

| C_bus | Standard | Fast | Fast+ |
|-------|----------|------|-------|
| 50 pF | 10KΩ | 4.7KΩ | 2.2KΩ |
| 200 pF | 4.7KΩ | 2.2KΩ | 1KΩ |
| 400 pF | 2.2KΩ | 1KΩ | 470Ω |

**Rule of thumb:** 4.7KΩ at 3.3V covers most standard/fast mode cases.

---

## 7. STM32 I2C REGISTER MAP

| Register | Key Bits | Purpose |
|----------|----------|---------|
| CR1 | PE, START, STOP, ACK, SWRST | Control |
| CR2 | FREQ, ITEVTEN, ITBUFEN, DMAEN | Config |
| OAR1 | ADD[7:1] or ADD[9:0] | Own address |
| DR | DATA[7:0] | Data in/out |
| SR1 | SB, ADDR, BTF, AF, BERR, OVR | Status |
| SR2 | BUSY, MSL, TRA | Status 2 |
| CCR | F/S, CCR[11:0] | Clock control |
| TRISE | TRISE[5:0] | Rise time |

**Clock formulas:**
```
Standard: CCR = APB/(2×100k) = 42M/200k = 210
Fast 2:1: CCR = APB/(3×400k) = 42M/1.2M = 35
TRISE_Sm: (APB_MHz × 1) + 1 = 43
TRISE_Fm: (APB_MHz × 0.3) + 1 = 13
```

---

## 8. CODE SNIPPETS

### Bare Metal Write
```c
I2C1->CR1 |= I2C_CR1_START;           // START
while(!(I2C1->SR1 & I2C_SR1_SB));     // Wait START
I2C1->DR = (addr << 1);               // Address+W
while(!(I2C1->SR1 & I2C_SR1_ADDR));   // Wait ADDR
(void)I2C1->SR2;                       // Clear ADDR
I2C1->DR = reg;                        // Register
while(!(I2C1->SR1 & I2C_SR1_BTF));    // Wait
I2C1->DR = val;                        // Data
while(!(I2C1->SR1 & I2C_SR1_BTF));    // Wait
I2C1->CR1 |= I2C_CR1_STOP;           // STOP
```

### Linux Userspace
```c
int fd = open("/dev/i2c-0", O_RDWR);
ioctl(fd, I2C_SLAVE, 0x68);
uint8_t buf[2] = {reg, val};
write(fd, buf, 2);           // Write reg+val

uint8_t r = reg;
write(fd, &r, 1);           // Set register pointer
read(fd, &val, 1);          // Read value
```

### Linux Kernel Driver
```c
val = i2c_smbus_read_byte_data(client, reg);
i2c_smbus_write_byte_data(client, reg, val);
```

---

## 9. LINUX I2C-TOOLS COMMANDS

```bash
# Scan bus for all devices
i2cdetect -y 0

# Read register
i2cget -y 0 0x68 0x0A

# Write register
i2cset -y 0 0x68 0x0A 0x42

# Dump all registers
i2cdump -y 0 0x68

# Raw: write 1 byte, read 2 bytes
i2ctransfer -y 0 w1@0x68 0x0A r2

# Check I2C adapter capabilities
i2cdetect -F 0

# List I2C buses
i2cdetect -l
```

---

## 10. TROUBLESHOOTING QUICK GUIDE

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| No devices detected | Missing pull-ups | Add 4.7KΩ to SDA+SCL |
| NACK on all addresses | Wrong bus / device unpowered | Check wiring, power |
| NACK on specific addr | Wrong address (7 vs 8-bit) | Verify datasheet format |
| SDA stuck LOW | Slave hung mid-byte | 9 clock pulses + STOP |
| SCL stuck LOW | Slave clock stretching hung | Power cycle slave |
| Intermittent errors | Marginal rise time / EMI | Lower Rp, add filtering |
| Data corruption | Bus cap exceeded / noise | Add mux, shorten traces |
| Works at 100k, fails 400k | Rise time too slow | Lower Rp (2.2KΩ) |
| Random NACK | Power supply noise | Add decoupling caps |
| Bus hangs after hours | Glitch → slave stuck | Add watchdog + recovery |

---

## 11. BUS RECOVERY PROCEDURE

```
1. Set SCL as GPIO output, SDA as GPIO input
2. FOR i = 0 to 8:
     SCL = LOW;  delay 5µs;
     SCL = HIGH; delay 5µs;
     IF SDA == HIGH: BREAK;
3. Generate STOP:
     SDA = LOW;  delay 5µs;
     SCL = HIGH; delay 5µs;
     SDA = HIGH; delay 5µs;  ← STOP!
4. Reconfigure pins back to I2C AF mode
```

---

## 12. INTERVIEW QUICK CARD

| Question | One-Liner Answer |
|----------|-----------------|
| Usable addresses? | 112 (7-bit minus 16 reserved) |
| Faster than SPI? | No. I2C max 3.4M, SPI 200M+ |
| Multi-master collision? | Arbitration via wired-AND, non-destructive |
| Error detection? | ACK/NACK only (SMBus adds CRC-8) |
| Pull-up why? | Open-drain can only pull LOW |
| Clock stretching? | Slave holds SCL LOW to pause master |
| Repeated START why? | Atomic register read (don't release bus) |
| TWI = ? | Same as I2C (Atmel naming) |
| Bus stuck fix? | 9 clock pulses + STOP |
| I2C successor? | MIPI I3C (backward compatible) |

---

END OF DOCUMENT 06 — CHEATSHEET
