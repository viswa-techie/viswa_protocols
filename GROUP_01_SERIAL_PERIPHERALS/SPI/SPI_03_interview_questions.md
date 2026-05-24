# SPI — INTERVIEW QUESTIONS & MODEL ANSWERS
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 03 of 08 — 120+ Questions with Answers
# Levels: Junior → Mid → Senior → Staff → Debugging → System Design
# ════════════════════════════════════════════════════════════════════

---

# PART A: JUNIOR / ENTRY-LEVEL (25 Questions)

---

### Q1. What does SPI stand for and who created it?
**A:** Serial Peripheral Interface. Created by Motorola in the mid-1980s, first appearing in the 68HC11 microcontroller. It has no formal standard — it's a de facto industry convention.

### Q2. Name the four SPI signals and their functions.
**A:**
- **SCLK** (Serial Clock): Master generates clock for synchronization
- **MOSI** (Master Out Slave In): Data from master to slave
- **MISO** (Master In Slave Out): Data from slave to master
- **CS̄/SS̄** (Chip Select, active LOW): Selects which slave is active

### Q3. Is SPI synchronous or asynchronous? Why does this matter?
**A:** Synchronous — the master provides a clock (SCLK). This means both sides are perfectly synchronized; no baud rate negotiation needed, no start/stop bits, and data can run at much higher speeds than UART.

### Q4. Is SPI full-duplex or half-duplex?
**A:** Standard 4-wire SPI is **full-duplex** — data moves on MOSI and MISO simultaneously with every clock cycle. However, Dual/Quad SPI variants are half-duplex since data lines are shared.

### Q5. What topology does SPI use?
**A:** Master-Slave with a star topology. One master connects to multiple slaves via a shared bus (SCLK, MOSI, MISO) with individual CS̄ lines per slave.

### Q6. Why is the chip select line active LOW?
**A:** Convention from TTL/CMOS design — active LOW means the idle state (HIGH) is the safe state. Pull-up resistors ensure slaves are deselected during master reset or power-up, preventing accidental activation.

### Q7. What happens if you forget to connect GND between SPI devices?
**A:** No common voltage reference → logic levels meaningless → no communication at all. GND is always required even though it's not listed as an "SPI signal."

### Q8. What is the maximum speed of SPI?
**A:** There is no defined maximum — it depends on the slave device and signal integrity. Common speeds: 1–50 MHz. High-performance: 100–200+ MHz. The practical limit is set by the slowest device on the bus plus PCB trace constraints.

### Q9. How does the master select which slave to talk to?
**A:** By driving that slave's CS̄ line LOW while keeping all other CS̄ lines HIGH. Only one CS̄ should be LOW at a time to prevent bus contention on MISO.

### Q10. What is the idle state of the MISO line when no slave is selected?
**A:** High impedance (tri-state/floating). The slave releases the MISO line when CS̄ is HIGH. A pull-up or pull-down resistor is recommended to prevent floating.

### Q11. What is a "dummy byte" in SPI and why is it needed?
**A:** A byte (typically 0x00 or 0xFF) sent by the master when it only wants to READ data from the slave. Because SPI is a shift-register exchange, the master must clock out data to receive data — the dummy byte provides those clocks.

### Q12. Is SPI MSB-first or LSB-first?
**A:** Most SPI devices use MSB-first, but some use LSB-first. Most MCU SPI peripherals are configurable. Always check the slave device datasheet.

### Q13. How many devices can an SPI bus support?
**A:** Limited by: (1) number of CS̄ pins available, (2) bus capacitance loading, and (3) signal integrity. Practically 4–16 devices; extendable to more with a CS̄ decoder (e.g., 74HC138).

### Q14. What makes SPI faster than UART?
**A:** (1) Synchronous clock → no start/stop bit overhead, (2) No baud rate mismatch possible, (3) Push-pull drivers → fast edges, (4) No protocol overhead → 100% data efficiency, (5) Clock can run at 10–200 MHz vs UART's ~1 MHz practical limit.

### Q15. What is the typical distance range for SPI?
**A:** SPI is designed for on-board (PCB) communication: 10–30 cm typical. With care (controlled impedance, termination), up to ~1 meter. For longer distances, use UART/RS-485 or CAN.

### Q16. Does SPI have built-in error detection?
**A:** No. No parity, no CRC, no ACK in the protocol itself. Error detection must be added in the application layer (read-back verification, CRC in payload, device ID checks).

### Q17. What happens if CPOL/CPHA (SPI mode) is set wrong?
**A:** Data is sampled at wrong clock edges → corrupted/shifted data. The master might read garbage or bit-shifted values from the slave.

### Q18. Can SPI operate with only 3 wires?
**A:** Yes — 3-wire SPI uses a single bidirectional data line (SDIO) instead of MOSI+MISO. It becomes half-duplex. Used in pin-constrained designs (small sensors).

### Q19. What is the difference between hardware CS̄ and software CS̄?
**A:** Hardware CS̄: MCU SPI peripheral controls CS̄ automatically, but often deasserts between bytes. Software CS̄ (GPIO): developer controls CS̄ manually, allowing it to stay LOW for multi-byte transfers. Software CS̄ is more common in practice.

### Q20. What is a "bus contention" in SPI?
**A:** When two slaves have CS̄ LOW simultaneously, both try to drive MISO — one HIGH, one LOW — causing a short circuit. This can damage devices and corrupts data.

### Q21. Compare SPI and I2C in 3 key aspects.
**A:** (1) Speed: SPI 10–200 MHz vs I2C 100 kHz–3.4 MHz, (2) Wires: SPI 4+ vs I2C 2, (3) Addressing: SPI uses hardware CS̄ vs I2C uses 7-bit software addresses.

### Q22. What is the data transfer unit in SPI?
**A:** Typically 8 bits (1 byte), but configurable: 4, 8, 16, or 32 bits depending on MCU peripheral and slave device requirements.

### Q23. Does SPI have acknowledgment like I2C?
**A:** No. There is no ACK/NACK mechanism. The master has no way to know if the slave received data correctly unless the application protocol includes verification.

### Q24. Can SPI work with mixed voltage devices?
**A:** Not directly — voltage levels must be compatible. Use level shifters when connecting 3.3V and 5V devices. A 5V MISO into a 3.3V master input can damage the master.

### Q25. What is the MODF error in SPI?
**A:** Mode Fault — occurs when a master detects that its SS̄ pin is driven LOW by another device, indicating a potential multi-master conflict. The SPI peripheral disables itself to prevent bus contention.

---

# PART B: MID-LEVEL (30 Questions)

---

### Q26. Explain all four SPI modes with timing diagrams.
**A:**
| Mode | CPOL | CPHA | SCLK Idle | Sample Edge |
|------|------|------|-----------|-------------|
| 0 | 0 | 0 | LOW | Rising (1st) |
| 1 | 0 | 1 | LOW | Falling (2nd) |
| 2 | 1 | 0 | HIGH | Falling (1st) |
| 3 | 1 | 1 | HIGH | Rising (2nd) |

CPHA determines whether data is sampled on the first or second edge after the idle state. CPOL determines what the first edge is (rising vs falling).

### Q27. Why do Mode 0 and Mode 3 sometimes appear interchangeable?
**A:** Both sample data on the **rising** edge of SCLK. The only difference is the idle state (LOW vs HIGH). For many devices, the actual data capture is identical — only the leading/trailing clock edge before/after CS̄ differs. This is why some devices list "Mode 0 or 3" as compatible.

### Q28. How do you calculate the actual SPI clock speed on an STM32?
**A:**
```
SPI_CLK = APB_CLK / (2^(BR+1))
```
Where BR is the 3-bit prescaler value (0–7). For APB2 = 84 MHz, BR=0: 42 MHz, BR=1: 21 MHz, etc. The SPI clock must not exceed the slave's maximum rated frequency.

### Q29. Explain the SPI shift register exchange mechanism.
**A:** Master and slave each have an 8-bit shift register connected in a ring. On each SCLK edge: master shifts MSB out onto MOSI → slave shifts it into its LSB; simultaneously slave shifts MSB out onto MISO → master shifts it into its LSB. After 8 clocks, the bytes have been fully exchanged. This is always bidirectional — you cannot send without receiving.

### Q30. What is a SPI transaction vs a SPI transfer?
**A:** A **transfer** is a single byte/word exchange (8/16 SCLK cycles). A **transaction** is a complete operation bounded by CS̄ assert/deassert — may include multiple transfers. Example: Flash read = CS̄↓ + [cmd transfer] + [addr transfers] + [data transfers] + CS̄↑.

### Q31. Explain the SPI Flash command protocol.
**A:** Most SPI Flash follows a command-address-data structure:
1. CS̄ assert LOW
2. Send command opcode (8 bits): 0x03=Read, 0x02=Write, 0x06=WriteEnable
3. Send address (24 or 32 bits)
4. Optional dummy clocks (for fast read timing)
5. Send/receive data bytes
6. CS̄ deassert HIGH

### Q32. What is the difference between SPI polling, interrupt, and DMA modes?
**A:**
- **Polling**: CPU checks TXE/RXNE flags in a loop. Simple but CPU is blocked.
- **Interrupt**: ISR triggered on TXE/RXNE. CPU free between bytes, but ISR overhead per byte.
- **DMA**: Hardware moves data between memory and SPI data register. CPU free for entire transfer. Best for large transfers (>16 bytes).

### Q33. Why is software CS̄ preferred over hardware CS̄ in most designs?
**A:** Many MCU SPI peripherals deassert CS̄ between each byte when in hardware mode. Most SPI slaves require CS̄ to remain LOW for multi-byte commands (Flash read, ADC conversion). Software GPIO control gives precise CS̄ timing.

### Q34. How does the SPI FIFO improve performance?
**A:** Without FIFO: CPU must respond within one byte time (e.g., 1 µs at 8 MHz) or data is lost. With 8-byte FIFO: CPU has 8× more time to respond. Reduces interrupt frequency by 8×. Critical at high clock rates.

### Q35. What is SPI daisy-chaining and when is it useful?
**A:** Slaves connected in series: master MOSI → slave1 DIN → slave1 DOUT → slave2 DIN → ... → last slave DOUT → master MISO. Shared CS̄ and SCLK. Data shifts through the entire chain. Used for LED drivers (WS2801), DAC arrays, shift registers. Saves CS̄ pins but adds latency.

### Q36. What causes SPI overrun errors (OVR)?
**A:** New data arrives in the RX shift register before the previous data was read from the RX data register. The old data is overwritten and lost. Causes: CPU too slow reading, missing DMA, high clock speed, ISR latency.

### Q37. How do you handle shared SPI bus with multiple devices in an RTOS?
**A:** Use a mutex/semaphore to protect the SPI bus. Before any transfer: acquire mutex → configure bus (mode, speed) for target device → assert CS̄ → transfer → deassert CS̄ → release mutex. Each device may need different mode/speed, so reconfigure per transaction.

### Q38. What is XIP (Execute-In-Place) in QSPI?
**A:** The QSPI controller memory-maps the external Flash into the CPU address space. The CPU reads instructions directly from Flash as if it were internal memory. Saves RAM (no need to copy code) but adds latency. Used extensively in automotive for calibration data and boot code.

### Q39. Explain Dual SPI vs Quad SPI vs Octal SPI.
**A:**
| Variant | Data Lines | Bits/Clock | Typical Use |
|---------|-----------|------------|-------------|
| Standard | 1 MOSI + 1 MISO | 1 | General |
| Dual | IO0 + IO1 (shared) | 2 | Fast Flash read |
| Quad | IO0–IO3 | 4 | High-speed Flash |
| Octal | IO0–IO7 + DQS | 8 | Ultra-high Flash |

All extended modes are half-duplex — data lines change direction between command and data phases.

### Q40. What is the DQS (Data Strobe) signal in OSPI/xSPI?
**A:** A data strobe signal generated by the slave that toggles with each data byte. The master uses DQS to capture data instead of its own clock, solving clock-to-data skew at very high frequencies (>100 MHz). Similar to DDR DRAM's DQS.

### Q41. How does SPI handle different endianness between master and slave?
**A:** SPI transfers one byte at a time; endianness is an application concern. When reading a 16-bit register: some devices send MSByte first, others LSByte first. Check datasheet and swap bytes in software if needed:
```c
uint16_t val = (rx[0] << 8) | rx[1];  // MSB-first device
uint16_t val = rx[0] | (rx[1] << 8);  // LSB-first device
```

### Q42. What is the SPI BSY (Busy) flag and why is it dangerous to ignore?
**A:** BSY=1 means the SPI peripheral is actively transferring. You must wait for BSY=0 before: disabling SPI, changing mode/speed, or deasserting CS̄. Ignoring BSY can truncate the last byte or corrupt the transfer.

### Q43. Explain how to implement SPI read-back verification.
**A:** After writing a register: (1) Write value, (2) Read register back, (3) Compare. If mismatch: retry or flag error. This compensates for SPI's lack of ACK mechanism:
```c
spi_write_reg(REG, value);
if (spi_read_reg(REG) != value) handle_error();
```

### Q44. What is a SPI CRC and which MCUs support it in hardware?
**A:** Some MCU SPI peripherals have a hardware CRC generator that appends/checks a CRC after data. STM32 SPI supports CRC-8/CRC-16 with configurable polynomial. The CRC is automatically calculated and appended to the last byte of a transfer. Useful for safety-critical applications.

### Q45. How does Linux's spidev work?
**A:** spidev exposes SPI via /dev/spidevX.Y (X=bus, Y=CS). Userspace uses ioctl() for configuration (mode, speed, bits) and SPI_IOC_MESSAGE for full-duplex transfers. The kernel handles CS̄ and clocking through the SPI master controller driver.

### Q46. How do you test SPI without a slave device?
**A:** (1) Loopback: connect MOSI to MISO, verify sent=received. (2) Scope: observe SCLK and MOSI with oscilloscope. (3) Logic analyzer: decode transmitted bytes. (4) Known device: use a JEDEC-standard Flash — read ID (0x9F) should return manufacturer/device ID.

### Q47. What is the SSOE bit in STM32 SPI?
**A:** Slave Select Output Enable. When set, the NSS pin is driven as an output (hardware CS̄). When clear, NSS is input-only (for slave mode or multi-master detection). With software CS̄ (SSM=1), SSOE is irrelevant.

### Q48. What causes "MISO stuck" problems?
**A:** (1) Slave not powered, (2) Slave CS̄ not connected/asserted, (3) Slave in wrong mode, (4) MISO pin on slave not configured as output, (5) Slave's internal state machine requires reset, (6) Bus contention (another slave driving MISO).

### Q49. Explain SPI in the context of Linux Device Tree.
**A:** The SPI controller and its slave devices are described in DTS:
- Controller node: compatible, reg, clocks, interrupts
- Slave nodes: compatible, reg (CS number), spi-max-frequency, spi-cpol, spi-cpha
The kernel matches "compatible" string to drivers and instantiates the SPI device.

### Q50. What are setup time and hold time in SPI, and what happens when violated?
**A:** Setup time (t_SU): data must be stable BEFORE the sampling clock edge. Hold time (t_HD): data must remain stable AFTER the sampling clock edge. Violation → metastability → random bit values → intermittent errors that are extremely hard to debug.

### Q51. How do you determine the maximum SPI clock for a given system?
**A:**
```
f_max = 1 / (t_CO_slave + t_SU_master + t_prop)
```
t_CO = clock-to-output delay of slave, t_SU = setup time of master, t_prop = propagation delay (PCB trace + connector). Always derate by 20–30% for margin.

### Q52. What is the difference between SPI and SSP?
**A:** SSP (Synchronous Serial Port) is a more generic peripheral found on ARM MCUs (e.g., LPC series) that supports SPI, SSI, and Microwire protocols. SPI is one mode of SSP operation.

### Q53. How does SPI flow control work?
**A:** SPI has no native flow control. If a slave can't keep up: (1) Use a BUSY/READY pin (extra GPIO from slave → master), (2) Return a busy status byte, (3) Master polls status register before data transfer. Flash devices use a Write-In-Progress (WIP) bit in status register.

### Q54. What precautions are needed for SPI across connectors (board-to-board)?
**A:** (1) Keep cables short (<30 cm), (2) Use shielded cables, (3) Add series termination resistors (22–33Ω), (4) Reduce clock speed (1–5 MHz), (5) Add ground wires between signal wires, (6) Consider RS-485/LVDS transceivers for longer runs.

### Q55. Explain the SPI bootloader protocol used by STM32.
**A:** STM32 system bootloader supports SPI boot: (1) Boot pins configure ROM boot, (2) Master sends sync frame (0x5A), (3) Bootloader ACKs (0x79) or NACKs (0x1F), (4) Master sends commands: Get, ReadMemory, WriteMemory, Erase, Go. Each command has a specific packet format with XOR checksum.

---

# PART C: SENIOR / LEAD-LEVEL (25 Questions)

---

### Q56. How would you design a robust SPI driver for a safety-critical automotive application?
**A:** Key elements: (1) Hardware CRC on transfers, (2) Read-back verification for all writes, (3) Timeout on every transaction, (4) Error counters with threshold alerts, (5) Watchdog on communication health, (6) Redundant SPI bus for critical devices, (7) AUTOSAR-compliant Spi Handler/Driver with Job/Sequence abstraction, (8) ASIL-rated MCU with SPI error detection features, (9) Diagnostics: report SPI health via UDS/DTC.

### Q57. Explain the Linux SPI framework architecture in detail.
**A:** Three layers: (1) **SPI Core** (spi.c): bus abstraction, device/driver matching, message queuing, (2) **SPI Master Controller Driver** (spi-xxx.c): hardware-specific — manages registers, DMA, interrupts for a specific SPI controller, (3) **SPI Protocol/Device Driver** (e.g., spi-nor.c, mcp251x.c): implements device-specific command protocol using spi_sync/spi_async API.

Transfers use `struct spi_message` containing one or more `struct spi_transfer` (each with tx_buf, rx_buf, len, speed, bits). The core queues messages and calls the controller driver's `transfer_one_message()`.

### Q58. How do you handle SPI in a multi-core SoC (e.g., Qualcomm SA8155P)?
**A:** (1) Assign SPI controllers to specific cores via device tree, (2) If shared: use spinlock (bare-metal) or mutex (RTOS/Linux), (3) Qualcomm QUP engine supports multi-threaded access with GSI DMA, (4) Each QUP instance has its own FIFO and DMA channels — prefer dedicating controllers to avoid contention, (5) In AAOS: SPI access from HAL service; serialize access via HIDL/AIDL interface.

### Q59. What is the AUTOSAR Spi Handler/Driver architecture?
**A:** Three-level abstraction: **Channel** (single buffer pair), **Job** (group of channels transferred in one CS̄), **Sequence** (ordered list of jobs). The Spi Handler manages: priority scheduling, async/sync transfer modes, sequence interleaving. The MCAL driver handles hardware registers. This separation allows ECU software to be hardware-independent.

### Q60. How do you debug SPI communication at 50+ MHz?
**A:** (1) Logic analyzer with ≥200 MHz sample rate for SPI decode, (2) Oscilloscope for signal integrity (rise time, ringing, overshoot), (3) Verify setup/hold margins: measure t_CO vs t_SU at operating temperature, (4) Check eye diagram at data receiver, (5) Reduce speed to verify functional correctness first, then increase, (6) Use differential probes to minimize probe loading, (7) Capture CS̄ timing — ensure adequate setup/hold.

### Q61. Explain the clock-to-data skew problem in high-speed SPI.
**A:** At high frequencies, the SCLK edge at the slave arrives at a different time than at the master due to trace length differences. If SCLK reaches the slave 2ns before data (MOSI), the slave may sample invalid data. Solution: match trace lengths, or use DQS (source-synchronous clocking) in xSPI. The slave latches MOSI using SCLK it receives, so the critical path is MISO → master.

### Q62. How do you implement a SPI slave on an MCU? What are the challenges?
**A:** Challenges: (1) Slave must respond within one clock cycle — at 10 MHz that's 100ns! (2) TX data must be pre-loaded before CS̄ assertion, (3) Need interrupt/DMA on CS̄ edge to prepare, (4) Variable-length transactions need state machine, (5) If master doesn't provide enough clocks, data truncated. Implementation: use DMA with pre-loaded buffer, CS̄ interrupt to reset state machine, ISR to handle command parsing.

### Q63. What are the security implications of SPI in automotive boot?
**A:** SPI NOR Flash stores bootloader → attack vector: (1) Read Flash via probing → steal firmware/keys, (2) Write Flash → inject malicious code, (3) Glitch SPI during secure boot → skip verification. Mitigations: encrypted Flash, signed boot images, hardware root of trust, OTP fuses, write protection, tamper detection, conformal coating over SPI traces.

### Q64. How does SPI NOR Flash wear leveling differ from NAND?
**A:** NOR Flash: endurance 100K–1M cycles (vs NAND 1K–100K), no bad block management needed, bit-addressable read. For NOR, wear leveling is simpler: (1) Distribute writes across sectors, (2) Track erase count per sector, (3) Swap hot/cold sectors periodically. In automotive: calibration data written infrequently, so NOR endurance is rarely an issue.

### Q65. Design a hot-standby SPI system for ASIL-D applications.
**A:** (1) Redundant SPI bus: primary + backup with separate controllers, (2) Cross-checking: both read same device, compare results, (3) Watchdog on each path, (4) Automatic failover: if primary fails (timeout/CRC error), switch to backup, (5) Diagnostics: report via UDS DID, (6) End-to-end data protection (E2E) per AUTOSAR, (7) SPI controller with ECC on internal FIFOs.

### Q66. How would you write a Linux SPI controller driver from scratch?
**A:** Steps: (1) Implement `probe()`: ioremap registers, setup clocks, allocate `spi_controller`, (2) Set `transfer_one_message()` callback, (3) In callback: configure CPOL/CPHA/speed per transfer, manage CS̄, start DMA or PIO, handle completion, (4) Register with `devm_spi_register_controller()`, (5) Handle: IRQ, DMA completion, error flags, power management, (6) Device tree compatible string for binding, (7) Add runtime PM for power savings.

### Q67. What is the Qualcomm QUP (Qualcomm Universal Protocol) engine?
**A:** A flexible serial engine that can be configured as SPI, I2C, or UART at runtime. Features: (1) Shared FIFO memory, (2) DMA via GPI (Generic Peripheral Interface), (3) Up to 12 QUP instances, (4) Hardware CS̄ management, (5) Supports up to 50 MHz for SPI, (6) Used in Snapdragon SoCs for all serial peripherals, (7) Linux driver: spi-geni-qcom.c.

### Q68. Explain the difference between SPI and DSPI (Deserialize SPI) in NXP automotive MCUs.
**A:** NXP's DSPI (on S32K, MPC5xxx) adds automotive features: (1) Multiple chip selects with individual configurations, (2) Continuous transfer mode (CS̄ stays active), (3) Modified transfer formats for serializer/deserializer chips, (4) Command FIFO for automated multi-step transactions, (5) Hardware timed CS̄-to-SCLK delays, (6) Better DMA integration for deterministic automotive operations.

### Q69. How do you handle SPI in a Linux suspend/resume cycle?
**A:** (1) SPI controller driver implements `suspend()`: disable clocks, save register state, (2) `resume()`: restore clocks, reconfigure registers, (3) SPI device drivers: re-initialize device state (slave may have lost settings during power loss), (4) Consider: SPI Flash may need wake-from-deep-power-down command, (5) Use PM notifiers for ordered restore, (6) QSPI XIP: special handling — cannot access code from Flash until SPI is restored.

### Q70. What is the relationship between SPI and the MTD (Memory Technology Device) subsystem in Linux?
**A:** MTD provides a generic interface for Flash memory. SPI NOR Flash: `drivers/mtd/spi-nor/` uses SPI framework to send read/write/erase commands, registering as MTD device. MTD then provides: (1) /dev/mtdX character device, (2) Block device via mtdblock, (3) JFFS2/UBIFS filesystem on top, (4) Partition support via device tree.

### Q71. How do you benchmark SPI throughput in an embedded system?
**A:**
```c
uint32_t start = get_timer();
for (int i = 0; i < 1000; i++)
    spi_dma_transfer(buf_4096, rx_buf, 4096);
uint32_t elapsed = get_timer() - start;
float throughput = (1000.0 * 4096 * 8) / (elapsed / 1000.0); // bps
// Also measure: CPU utilization during transfer (DMA vs polling)
// Measure: actual vs theoretical (clock_speed × efficiency)
```

### Q72. Explain multi-master SPI — is it practical?
**A:** SPI supports multi-master via MODF (Mode Fault) detection: if a master's SS̄ input goes LOW, it means another device is claiming master. The peripheral disables itself. However: (1) No arbitration protocol like I2C, (2) No retry mechanism, (3) No priority system, (4) Risk of simultaneous CS̄ assertion. In practice, multi-master SPI is rarely used — use I2C or CAN instead.

### Q73. How do you handle QSPI Flash firmware updates in automotive?
**A:** (1) Dual-bank Flash: write new firmware to inactive bank, (2) Verify CRC/signature before switching, (3) Bootloader manages bank switching, (4) Power-fail safety: old bank preserved until new is verified, (5) FOTA (Firmware Over-The-Air): download → staging area → SPI Flash write → verify → activate, (6) Rollback mechanism: if new firmware fails, revert to previous bank.

### Q74. What are the timing constraints for SPI in ASIL-B/D systems?
**A:** (1) Maximum latency from request to response must be guaranteed, (2) WCET (Worst Case Execution Time) of SPI ISR/DMA must be analyzed, (3) Bus scheduling: SPI transactions must not starve safety-critical tasks, (4) Timeout on every transaction with defined fallback, (5) End-to-end timing protection per AUTOSAR OS, (6) Periodic diagnostic checks (device ID read) to verify bus health.

### Q75. How would you optimize SPI Flash boot time in automotive?
**A:** (1) Use QSPI/OSPI instead of standard SPI (4–8× bandwidth), (2) Enable XIP for code execution without copy to RAM, (3) Use DDR mode for double data rate, (4) Optimize command sequence: reduce dummy cycles, (5) Enable continuous read mode (skip command/address for sequential reads), (6) Pre-compute CRC during transfer (pipelining), (7) Boot-critical code in first sectors (fast address access), (8) DMA for data copy while CPU initializes other peripherals.

### Q76. Describe the JEDEC xSPI (JESD251) standard.
**A:** xSPI standardizes high-speed SPI for NOR Flash: (1) Octal DDR up to 200 MHz (3.2 Gbps), (2) DQS for source-synchronous data capture, (3) Standardized command set, (4) Profile 1.0 (legacy compatible) and Profile 2.0 (new), (5) Differential clock option, (6) ECC support, (7) Individual and sector erase, (8) Compatible with existing SPI software by subset command support.

### Q77. How do you implement SPI communication between an automotive SoC and a safety MCU?
**A:** This is a common pattern (QM SoC + ASIL MCU): (1) SoC is SPI master, MCU is slave, (2) Define command protocol with CRC, (3) MCU pre-loads response in shift register on CS̄ interrupt, (4) Watchdog: MCU monitors SPI activity — if SoC stops polling, escalate to safety action, (5) Data format: header + payload + CRC + alive counter, (6) Timeout-based fault detection both sides.

### Q78. What are the power consumption implications of SPI speed selection?
**A:**
```
P_dynamic ∝ f × C × V²
```
Higher SPI clock → more switching → more power. But faster clock → shorter active time → can enter sleep sooner. Optimal strategy: run at maximum speed for minimum time, then sleep. At 1 MHz vs 10 MHz for same data: 10 MHz uses 10× instantaneous power but 1/10th the time → similar total energy but 10× lower average power.

### Q79. How do you test SPI at extreme temperatures (-40°C to +125°C)?
**A:** (1) Temperature chamber with board inside, (2) Monitor: setup/hold margins decrease at extremes (timing gets tighter), (3) Flash performance degrades: read/write times increase at low temp, (4) Crystal oscillator frequency shifts → SPI clock accuracy affected, (5) Test at corners: cold + slow voltage, hot + fast voltage, (6) Derate maximum SPI speed by 20–30% for full temp range operation.

### Q80. Explain SPI in the context of functional safety (ISO 26262).
**A:** SPI failures can violate safety goals: (1) Classify SPI bus per ASIL level, (2) FMEA: identify failure modes (stuck-at, bit flip, timeout), (3) Diagnostic coverage: periodic device ID read, CRC, read-back, (4) Dependent failure analysis: shared bus = common cause failure, (5) Safe state: define behavior when SPI fails (limp mode, default values), (6) Metrics: SPFM/LFM must meet ASIL target, (7) Technical safety requirements: max latency, min diagnostic rate.

---

# PART D: STAFF / ARCHITECT (10 Questions)

---

### Q81. Design a SPI subsystem for a multi-domain automotive SoC.
**A:** Domains: Infotainment (QM), Cluster (ASIL-B), ADAS (ASIL-D). Design: (1) Separate SPI controllers per safety domain — no sharing across ASIL boundaries, (2) ADAS domain: redundant SPI with cross-checking, hardware CRC, E2E protection, (3) Infotainment: shared SPI with OS mutex, (4) Cluster: dedicated SPI for display controller, (5) Firewall: MPU/SMMU blocks cross-domain SPI access, (6) Clock: independent clock sources per domain, (7) Diagnostics: per-domain SPI health monitoring reported to safety manager.

### Q82. How would you architect SPI driver software for AUTOSAR Classic + Adaptive?
**A:** Classic (MCU): AUTOSAR SPI Handler/Driver in BSW — Job/Sequence abstraction, MCAL for register access, SchM for interrupt protection. Adaptive (SoC): Linux SPI framework with POSIX API, but add E2E protection wrapper. Bridge: Classic MCU communicates with Adaptive SoC via SPI — define SOME/IP-compatible command protocol over SPI. Diagnostics: UDS DID for SPI health, DTCs for failures.

### Q83. Evaluate: Should we replace SPI Flash with eMMC in next-gen automotive?
**A:** Trade-offs:
| Aspect | SPI NOR Flash | eMMC |
|--------|--------------|------|
| Boot speed | Fast (XIP, no copy) | Slower (needs copy to RAM) |
| Random read | Excellent | Good |
| Write speed | Slow (page program) | Fast |
| Endurance | 100K-1M cycles | 3K-10K cycles |
| Capacity | 1-512 MB typical | 4-128 GB |
| Cost/MB | Expensive | Cheap |
| Safety | Easy to qualify | Complex controller |

Recommendation: Keep SPI NOR for boot + safety-critical code, use eMMC for OS/apps/data. Hybrid is optimal.

### Q84. Design a protocol for SoC↔MCU communication over SPI.
**A:**
```
Frame Format:
[SYNC:2B][CMD:1B][LEN:2B][SEQ:1B][PAYLOAD:0-256B][CRC32:4B]

Features:
- SYNC: 0xAA55 for frame detection
- CMD: 0x01=Read, 0x02=Write, 0x03=Event, 0x04=Alive
- SEQ: Rolling counter for ordering + lost frame detection
- CRC32: End-to-end data integrity
- Alive frame every 10ms for watchdog
- Master (SoC) polls every 1ms
- Slave (MCU) pre-loads response on CS̄ interrupt
- Priority: safety > diagnostic > normal data
```

### Q85-90. [System-level architecture questions covering: SPI+DMA power optimization, QSPI boot redundancy, SPI security hardening for HSM, multi-SoC SPI interconnect, SPI latency budgeting for ADAS, SPI test strategy at silicon validation]

*These questions are best answered in whiteboard discussions — the key is demonstrating system thinking across hardware, software, safety, and timing domains.*

---

# PART E: DEBUGGING SCENARIOS (5 Detailed)

---

### SCENARIO 1: Flash Read Returns All 0xFF
**Setup:** SPI NOR Flash on QSPI, reading JEDEC ID returns 0xFF 0xFF 0xFF.

**Analysis:**
1. Check power: is Flash VCC correct?
2. Check CS̄: is it actually going LOW? (scope)
3. Check SCLK: is it toggling? (scope)
4. Check MOSI: is 0x9F being sent? (logic analyzer)
5. Check MISO: is it high-Z or actively driven to 1?
6. 0xFF = all-1s = MISO might be floating (pull-up) → CS̄ not reaching Flash
7. Check SPI mode: Flash usually Mode 0 or 3

**Root Cause:** Most commonly: CS̄ GPIO configured as input (not output), so CS̄ never goes LOW. Second: MISO pin not configured as input/AF on master side.

### SCENARIO 2: Data Shifted by 1 Bit
**Setup:** Reading sensor register, expected 0x42, getting 0x84 (left-shifted by 1).

**Analysis:**
1. 0x84 = 0x42 << 1 → exactly 1 extra clock or CPHA mismatch
2. Check CPHA setting — if CPHA wrong, all data shifted by 1 bit position
3. Check CS̄ timing — if CS̄ deasserts too late, extra clock captured
4. Check: is there a dummy bit/byte in the device protocol?

**Root Cause:** CPHA mismatch or the device has a "don't care" bit at the beginning that wasn't accounted for.

### SCENARIO 3: Works at 1 MHz, Fails at 10 MHz
**Setup:** SPI ADC communicates perfectly at 1 MHz but returns garbage at 10 MHz.

**Analysis:**
1. Signal integrity issue — check with oscilloscope at 10 MHz
2. Measure rise/fall times — are they >20% of bit period?
3. Check for ringing/overshoot on SCLK
4. Check ADC datasheet: what is maximum rated SPI speed?
5. Check trace length and capacitance loading
6. Check setup/hold times at 10 MHz: t_CO + t_SU must be < 100ns

**Root Cause:** (a) ADC max speed is 5 MHz, or (b) signal integrity degradation at 10 MHz (ringing on SCLK causing double-clocking).

### SCENARIO 4: SPI Slave Device Randomly Not Responding
**Setup:** Temperature sensor on shared SPI bus, intermittently returns all 0xFF.

**Analysis:**
1. Check other devices on bus — is another CS̄ accidentally going LOW?
2. Check CS̄ pull-up — if floating during init, sensor may be in undefined state
3. Check power sequencing — does sensor need reset after power-up?
4. Check EMI — is there noise on CS̄ line?
5. Add error counter: correlate failures with other system events

**Root Cause:** During RTOS task switching, a brief period where another task's CS̄ glitches LOW simultaneously → bus contention → garbage → sensor state machine stuck.

### SCENARIO 5: QSPI XIP Crashes After Suspend/Resume
**Setup:** System executes code from QSPI Flash via XIP. After Linux suspend/resume, CPU crashes with instruction abort at Flash address.

**Analysis:**
1. After resume, QSPI controller registers are reset to defaults
2. XIP requires specific QSPI configuration (mode, speed, dummy cycles)
3. CPU tries to fetch instruction from Flash → QSPI sends wrong command → garbage instruction → crash
4. Resume path MUST re-initialize QSPI before any Flash access
5. Chicken-and-egg: resume code itself might be in Flash!

**Root Cause:** Resume code runs from RAM, must re-init QSPI controller before returning to XIP code. The resume path was not restoring QSPI configuration before enabling XIP mapping.

---

# PART F: QUICK-FIRE (25 Questions)

| # | Question | Answer |
|---|----------|--------|
| 1 | Default SPI mode for most devices? | Mode 0 (CPOL=0, CPHA=0) |
| 2 | SPI efficiency (data overhead)? | 100% — zero protocol overhead |
| 3 | What does a floating MISO read as? | Random (typically 0xFF with pull-up) |
| 4 | CS̄ active state? | LOW |
| 5 | Who generates SCLK? | Master, always |
| 6 | Can slave initiate communication? | No — master must poll or use separate IRQ line |
| 7 | SPI word size? | Usually 8-bit, configurable 4-32 |
| 8 | QSPI data lines? | 4 (IO0–IO3) |
| 9 | Typical NOR Flash SPI read command? | 0x03 |
| 10 | JEDEC ID read command? | 0x9F |
| 11 | SPI clock formula (STM32)? | APB_CLK / 2^(BR+1) |
| 12 | What is MODF? | Mode Fault — multi-master detection |
| 13 | What is OVR? | Overrun — RX data lost |
| 14 | SPI in Device Tree: speed property? | spi-max-frequency |
| 15 | Linux userspace SPI device? | /dev/spidevX.Y |
| 16 | SPI kernel header? | linux/spi/spi.h |
| 17 | What is DSPI? | NXP's enhanced SPI with automotive features |
| 18 | DQS purpose? | Source-synchronous data strobe for high-speed |
| 19 | SPI NOR endurance? | 100K–1M erase cycles |
| 20 | XIP means? | Execute-In-Place — run code from Flash |
| 21 | SPI bus speed on shared bus? | Limited by slowest device |
| 22 | I2S relation to SPI? | Audio variant by Philips (same electrical, different framing) |
| 23 | Hardware CRC in STM32 SPI? | Yes — configurable polynomial |
| 24 | AUTOSAR SPI abstraction? | Channel → Job → Sequence |
| 25 | 3 GPIOs → how many CS̄ via decoder? | 8 (using 3-to-8 decoder like 74HC138) |

---

END OF DOCUMENT 03 — INTERVIEW QUESTIONS
