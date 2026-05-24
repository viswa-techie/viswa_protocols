# SPI — FLASHCARD DECKS
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI | Document: 04 of 08 — 250+ Anki-Style Flashcards
# Format: Q: (front) / A: (back) — 10 Decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 01: IDENTITY & BASICS (25 cards)

**Q1:** What does SPI stand for?
**A:** Serial Peripheral Interface

**Q2:** Who created SPI and when?
**A:** Motorola, mid-1980s (first in 68HC11 MCU, 1984)

**Q3:** Is there a formal SPI standard?
**A:** No. De facto convention. JEDEC xSPI (JESD251) only covers high-speed Flash.

**Q4:** Name the 4 SPI signals.
**A:** SCLK (clock), MOSI (master out slave in), MISO (master in slave out), CS̄ (chip select, active LOW)

**Q5:** What OSI layer does SPI operate at?
**A:** Layer 1 (Physical) + partial Layer 2 (Data Link)

**Q6:** Is SPI synchronous or asynchronous?
**A:** Synchronous — master provides clock (SCLK)

**Q7:** Is SPI full-duplex or half-duplex?
**A:** Standard 4-wire: full-duplex. Dual/Quad/Octal: half-duplex.

**Q8:** What is SPI's topology?
**A:** Master-Slave, star (shared bus with individual CS̄ per slave)

**Q9:** What is the typical SPI speed range?
**A:** 1 MHz – 200+ MHz (device dependent)

**Q10:** Does SPI have built-in error detection?
**A:** No — no parity, no CRC, no ACK. Must add at application layer.

**Q11:** What is the idle state of CS̄?
**A:** HIGH (inactive). CS̄ is active LOW.

**Q12:** Who generates the clock in SPI?
**A:** Always the master.

**Q13:** What does "de facto standard" mean for SPI?
**A:** No governing body published it; everyone adopted Motorola's convention.

**Q14:** What is the typical distance for SPI?
**A:** 10–30 cm (PCB), up to ~1 m with care.

**Q15:** How does SPI address slaves?
**A:** Hardware chip-select lines (CS̄). No software addressing.

**Q16:** What is the protocol overhead of SPI?
**A:** Zero — 100% data efficiency. No start/stop/address/ACK bits.

**Q17:** Can SPI support multi-master?
**A:** Technically yes (via MODF detection), but not practical. No arbitration protocol.

**Q18:** What voltage levels does SPI support?
**A:** 1.8V, 2.5V, 3.3V, 5V CMOS — depends on devices. Level shifters needed for mismatched levels.

**Q19:** What is the newer naming convention for SPI signals?
**A:** MOSI→COPI (Controller Out, Peripheral In), MISO→CIPO, Master→Controller, Slave→Peripheral

**Q20:** What is the minimum wire count for SPI?
**A:** 3 wires (SCLK + SDIO + CS̄) in 3-wire mode, or 4 wires standard (+ GND always needed)

**Q21:** Name 5 common SPI slave devices.
**A:** NOR Flash, ADC, DAC, accelerometer/IMU, display controller (LCD/OLED)

**Q22:** Why is SPI faster than UART?
**A:** Synchronous clock (no start/stop bits), push-pull drivers, zero overhead, clock up to 200 MHz

**Q23:** What is SPI's main disadvantage vs I2C?
**A:** More wires. SPI needs 4+ wires (1 CS̄ per slave), I2C needs only 2 wires for many devices.

**Q24:** Can a slave initiate communication in SPI?
**A:** No. Slave must wait for master to clock. Use a separate IRQ line if slave needs attention.

**Q25:** What is the data transfer unit in SPI?
**A:** Typically 8 bits (configurable: 4, 8, 16, 32 bits per word)

---

# DECK 02: THE FOUR MODES — CPOL/CPHA (25 cards)

**Q1:** What does CPOL mean?
**A:** Clock Polarity — idle state of SCLK. CPOL=0: idle LOW. CPOL=1: idle HIGH.

**Q2:** What does CPHA mean?
**A:** Clock Phase — which edge samples data. CPHA=0: first edge. CPHA=1: second edge.

**Q3:** Mode 0: CPOL=? CPHA=? Sample edge?
**A:** CPOL=0, CPHA=0. Sample on rising edge (first edge from LOW idle).

**Q4:** Mode 1: CPOL=? CPHA=? Sample edge?
**A:** CPOL=0, CPHA=1. Sample on falling edge (second edge from LOW idle).

**Q5:** Mode 2: CPOL=? CPHA=? Sample edge?
**A:** CPOL=1, CPHA=0. Sample on falling edge (first edge from HIGH idle).

**Q6:** Mode 3: CPOL=? CPHA=? Sample edge?
**A:** CPOL=1, CPHA=1. Sample on rising edge (second edge from HIGH idle).

**Q7:** Which SPI mode is most common?
**A:** Mode 0 (~80% of devices)

**Q8:** Which is the second most common mode?
**A:** Mode 3

**Q9:** Why do Mode 0 and Mode 3 sometimes work interchangeably?
**A:** Both sample on the rising edge. Only difference is SCLK idle state.

**Q10:** What happens if CPOL is wrong?
**A:** Data sampled at transition instead of stable — every bit wrong.

**Q11:** What happens if CPHA is wrong?
**A:** Data shifted by half a clock — bit-shifted garbage.

**Q12:** What SPI mode does W25Q Flash use?
**A:** Mode 0 or Mode 3

**Q13:** What SPI mode does MCP3008 ADC use?
**A:** Mode 0

**Q14:** What SPI mode does ADXL345 accelerometer use?
**A:** Mode 3

**Q15:** What SPI mode does SD card (SPI mode) use?
**A:** Mode 0

**Q16:** What does "sample on first edge" mean (CPHA=0)?
**A:** Data is captured on the first clock transition after idle state.

**Q17:** What does "sample on second edge" mean (CPHA=1)?
**A:** Data is captured on the second clock transition after idle state (data changes on first edge).

**Q18:** In Mode 0, when does data change on MOSI?
**A:** On the falling edge (data setup), sampled on rising edge.

**Q19:** In Mode 3, when does data change on MOSI?
**A:** On the falling edge (data setup), sampled on rising edge.

**Q20:** How many clock edges per bit?
**A:** 2 — one for setup (data change), one for sample (data capture).

**Q21:** What happens if you use Mode 0 on a Mode 1 device?
**A:** Total garbage — sampling on opposite edges.

**Q22:** Which modes share the same sample edge (rising)?
**A:** Mode 0 and Mode 3 both sample on rising edge.

**Q23:** Which modes share the same sample edge (falling)?
**A:** Mode 1 and Mode 2 both sample on falling edge.

**Q24:** SPI mode in Linux Device Tree — how to set Mode 2?
**A:** Add `spi-cpol;` (no spi-cpha). CPOL=1, CPHA=0 = Mode 2.

**Q25:** SPI mode in Linux Device Tree — how to set Mode 3?
**A:** Add both `spi-cpol;` and `spi-cpha;`. CPOL=1, CPHA=1 = Mode 3.

---

# DECK 03: SHIFT REGISTER & DATA TRANSFER (20 cards)

**Q1:** Describe the fundamental SPI transfer mechanism.
**A:** Two shift registers (master + slave) connected in a ring. Each clock shifts 1 bit out and 1 bit in simultaneously. After 8 clocks, bytes are fully exchanged.

**Q2:** What direction does SPI shift data?
**A:** MSB first (most common). MSB shifts out, new bit enters at LSB.

**Q3:** Can you send without receiving in SPI?
**A:** No. Every transfer is an exchange. To "just write," ignore the received byte.

**Q4:** Can you receive without sending in SPI?
**A:** No. To "just read," send dummy bytes (0x00 or 0xFF) to generate clocks.

**Q5:** What is a "dummy byte"?
**A:** A byte (0x00/0xFF) sent by master to clock data out of slave when master has no useful data to send.

**Q6:** If master sends N bytes, how many bytes does it receive?
**A:** Exactly N. Transfer is always symmetric.

**Q7:** Is SPI bit order configurable?
**A:** Yes — most MCU peripherals support MSB-first or LSB-first. Check datasheet.

**Q8:** What word sizes does SPI support?
**A:** 8-bit is standard. Many controllers support 4, 8, 16, or 32-bit words.

**Q9:** How do multi-byte transfers work?
**A:** CS̄ stays LOW, clock keeps running. Bytes flow sequentially through shift registers.

**Q10:** What is the MISO state between bytes in multi-byte transfer?
**A:** Remains driven by slave (CS̄ still LOW). Slave shifts out next byte.

**Q11:** What determines when a transfer ends?
**A:** CS̄ going HIGH (deassert). The master controls this.

**Q12:** What is a SPI transaction?
**A:** CS̄ assert → one or more byte exchanges → CS̄ deassert. A complete logical operation.

**Q13:** What is SPI daisy-chaining?
**A:** Slaves connected in series: MOSI→Slave1→Slave2→...→MISO. Data shifts through all devices. Shared CS̄.

**Q14:** When is daisy-chaining used?
**A:** LED drivers (WS2801), shift register chains, DAC arrays.

**Q15:** How many bytes to update N devices in daisy-chain?
**A:** N bytes. First byte ends up in last device, last byte in first device.

**Q16:** What is a SPI Flash "command phase"?
**A:** First byte after CS̄ assert — the opcode telling the Flash what to do (read, write, erase, etc.).

**Q17:** What is a SPI Flash "address phase"?
**A:** 3 bytes (24-bit) or 4 bytes (32-bit) after command — the memory address.

**Q18:** What is a SPI Flash "dummy phase"?
**A:** Extra clock cycles after address (no data) — gives Flash time to fetch data internally. Required for fast reads.

**Q19:** What is continuous read mode in SPI Flash?
**A:** After first read, Flash doesn't need command+address again for sequential addresses. Just keep clocking → data flows continuously.

**Q20:** What does the Flash status register WIP bit indicate?
**A:** Write In Progress — Flash is busy with erase/program operation. Master must wait until WIP=0.

---

# DECK 04: CHIP SELECT & BUS MANAGEMENT (20 cards)

**Q1:** What happens if two CS̄ lines are LOW simultaneously?
**A:** Bus contention on MISO — both slaves drive it. Possible device damage and data corruption.

**Q2:** What is tri-state and why does MISO need it?
**A:** Tri-state = high impedance (floating). When CS̄=HIGH, slave releases MISO so another slave can drive it.

**Q3:** What is CS̄ setup time (t_CSS)?
**A:** Time CS̄ must be LOW before first SCLK edge. Lets slave prepare. Typically 5–100 ns.

**Q4:** What is CS̄ hold time (t_CSH)?
**A:** Time CS̄ must stay LOW after last SCLK edge. Lets slave latch final data. Typically 5–100 ns.

**Q5:** What is CS̄ deselect time (t_CSD)?
**A:** Minimum time CS̄ must be HIGH between transactions. Typically 50–500 ns.

**Q6:** Why is software CS̄ preferred over hardware CS̄?
**A:** Hardware CS̄ often deasserts between bytes. Software GPIO gives precise multi-byte transaction control.

**Q7:** How to scale beyond 4 CS̄ pins?
**A:** Use a 3-to-8 decoder (74HC138): 3 GPIO → 8 chip selects.

**Q8:** What should CS̄ pins be during master reset/boot?
**A:** HIGH (inactive). Use external pull-up resistors to ensure this.

**Q9:** What is GPIO CS̄ code pattern?
**A:** `CS_LOW(pin); spi_transfer(data); CS_HIGH(pin);`

**Q10:** Can multiple slaves share a single CS̄?
**A:** Only in daisy-chain configuration. Otherwise each slave needs its own CS̄.

**Q11:** What happens to MISO with no slave selected?
**A:** Floats (high-Z). Add pull-up/down to define idle state.

**Q12:** Why is MISO floating a problem?
**A:** Master reads random values. Can cause false reads, phantom data, ISR triggers.

**Q13:** Can CS̄ be active HIGH?
**A:** Some devices use active HIGH, but it's rare. Always check datasheet.

**Q14:** What is the maximum number of slaves on SPI bus?
**A:** Limited by: CS̄ pin count, bus capacitance (~10 pF per device), signal integrity. Practical: 8–16.

**Q15:** How to handle CS̄ in RTOS with shared SPI?
**A:** Mutex protect entire CS̄ LOW → transfer → CS̄ HIGH sequence. Never split.

**Q16:** What is the BUG with STM32 hardware NSS?
**A:** In multi-slave config, NSS toggles between bytes, breaking multi-byte commands. Use SSM+GPIO instead.

**Q17:** What is SSOE on STM32?
**A:** Slave Select Output Enable — drives NSS pin as hardware output in master mode.

**Q18:** What is SSM on STM32?
**A:** Software Slave Management — CS̄ controlled by software (SSI bit) instead of hardware pin.

**Q19:** Decoder 74HC138: inputs A2 A1 A0 = 011, which output is LOW?
**A:** Y3 (output 3). Binary 011 = 3.

**Q20:** What pull-up value for CS̄?
**A:** 4.7K–10KΩ typical. Must not slow down CS̄ transitions excessively.

---

# DECK 05: HARDWARE & REGISTERS (25 cards)

**Q1:** Name 4 key STM32 SPI registers.
**A:** SPI_CR1 (control), SPI_CR2 (IRQ/DMA enable), SPI_SR (status), SPI_DR (data).

**Q2:** What does TXE flag mean?
**A:** TX buffer Empty — ready to accept new data for transmission.

**Q3:** What does RXNE flag mean?
**A:** RX buffer Not Empty — received data available to read.

**Q4:** What does BSY flag mean?
**A:** SPI Busy — transfer in progress. Wait for BSY=0 before disabling SPI.

**Q5:** What does OVR flag mean?
**A:** Overrun — new RX data arrived before old was read. Old data lost.

**Q6:** How to clear OVR on STM32?
**A:** Read SPI_DR, then read SPI_SR. Both reads required.

**Q7:** What does MODF flag mean?
**A:** Mode Fault — SS̄ went LOW in master mode. Multi-master conflict. SPI disables itself.

**Q8:** How to set baud rate on STM32 SPI?
**A:** CR1.BR[2:0] bits. Divider = 2^(BR+1). BR=0 → /2, BR=7 → /256.

**Q9:** APB2=84 MHz, BR=010, what is SPI clock?
**A:** 84 MHz / 2^(2+1) = 84/8 = 10.5 MHz

**Q10:** How to configure 8N1 equivalent in SPI?
**A:** DFF=0 (8-bit), no parity, no stop bits needed (SPI doesn't use them).

**Q11:** What is DFF/DS in STM32 SPI?
**A:** Data Frame Format/Size — selects 8-bit or 16-bit word size (newer STM32: configurable 4-32 bit).

**Q12:** What is LSBFIRST in SPI?
**A:** When set: LSB transmitted first. When clear: MSB first (default).

**Q13:** What is BIDIMODE in STM32 SPI?
**A:** Bidirectional mode — enables 3-wire SPI (single data line, half-duplex).

**Q14:** What is RXONLY in STM32 SPI?
**A:** Receive-only mode — generates clocks without sending. Useful for streaming input devices.

**Q15:** What are typical SPI FIFO sizes?
**A:** STM32F4: 1 byte. STM32H7: 8 bytes. NXP i.MX RT: 16 words. TI: 32 bytes.

**Q16:** What is the SPI CRC polynomial register?
**A:** SPI_CRCPR — defines CRC polynomial for hardware CRC computation during transfers.

**Q17:** How to enable SPI hardware CRC (STM32)?
**A:** Set CRCEN in CR1, write polynomial to CRCPR. CRC auto-computed and appended.

**Q18:** What happens if you write SPI_DR before TXE is set?
**A:** Write is ignored or overwrites pending data. Always wait for TXE=1.

**Q19:** What happens if you don't read SPI_DR before next RX?
**A:** OVR (overrun) — previous data lost, new data also not reliable.

**Q20:** What is the SPI enable bit?
**A:** SPE in CR1. Must set SPE=1 to activate SPI peripheral after configuration.

**Q21:** Order of SPI init: SPE before or after config?
**A:** Configure all settings FIRST, then set SPE=1 last. Don't change config while SPE=1.

**Q22:** What GPIO mode for SPI pins?
**A:** Alternate Function (AF) mode, with correct AF number (varies by MCU/pin).

**Q23:** Typical AF number for SPI1 on STM32F4?
**A:** AF5 (check datasheet — varies by pin and SPI instance).

**Q24:** What is the Qualcomm QUP engine?
**A:** Qualcomm Universal Protocol engine — configurable as SPI, I2C, or UART. Used in Snapdragon SoCs.

**Q25:** What is DSPI in NXP automotive MCUs?
**A:** Enhanced SPI with command FIFO, individual CS̄ configs, continuous mode, automotive features.

---

# DECK 06: LINUX & DEVICE TREE (25 cards)

**Q1:** What is the Linux SPI userspace device node?
**A:** /dev/spidevX.Y (X=bus number, Y=chip select)

**Q2:** How to set SPI mode via ioctl?
**A:** `ioctl(fd, SPI_IOC_WR_MODE, &mode);` where mode = SPI_MODE_0/1/2/3

**Q3:** How to set SPI speed via ioctl?
**A:** `ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed);`

**Q4:** How to do a full-duplex transfer in userspace?
**A:** Use `struct spi_ioc_transfer` with tx_buf+rx_buf, then `ioctl(fd, SPI_IOC_MESSAGE(1), &xfer);`

**Q5:** What kernel file implements spidev?
**A:** drivers/spi/spidev.c

**Q6:** What kernel file is the SPI core?
**A:** drivers/spi/spi.c

**Q7:** Name a SPI controller driver for Raspberry Pi.
**A:** spi-bcm2835.c

**Q8:** Name a SPI controller driver for Qualcomm.
**A:** spi-geni-qcom.c (or spi-qup.c for older)

**Q9:** How is SPI device tree "reg" property used?
**A:** It specifies the chip select number (e.g., reg = <0> = CS0).

**Q10:** How to set CPOL=1 in device tree?
**A:** Add property `spi-cpol;` (boolean, present = CPOL=1)

**Q11:** How to set CPHA=1 in device tree?
**A:** Add property `spi-cpha;` (boolean, present = CPHA=1)

**Q12:** How to set max speed in device tree?
**A:** `spi-max-frequency = <50000000>;` (50 MHz in Hz)

**Q13:** What is `struct spi_message` in kernel?
**A:** Container for one or more `spi_transfer` structs — represents a complete CS̄-bounded transaction.

**Q14:** What is `struct spi_transfer` in kernel?
**A:** Single transfer within a message: tx_buf, rx_buf, len, speed_hz, bits_per_word.

**Q15:** What does `spi_sync()` do?
**A:** Submits SPI message and blocks until completion. Returns 0 on success.

**Q16:** What does `spi_async()` do?
**A:** Submits SPI message asynchronously. Calls completion callback when done. Non-blocking.

**Q17:** What is `spi_write_then_read()`?
**A:** Convenience function: writes tx_buf, then reads rx_buf in one CS̄ transaction. Common for register read operations.

**Q18:** How to list SPI devices in Linux?
**A:** `ls /sys/bus/spi/devices/` or `ls /dev/spidev*`

**Q19:** How to check SPI master info?
**A:** `ls /sys/class/spi_master/`

**Q20:** What is SPI NOR framework in Linux?
**A:** drivers/mtd/spi-nor/ — generic SPI NOR Flash driver supporting JEDEC-standard commands.

**Q21:** How is SPI NOR registered in Linux?
**A:** As MTD device. Provides /dev/mtdX, block device via mtdblock, filesystem support.

**Q22:** What is the `compatible` string for SPI NOR Flash in DT?
**A:** `"jedec,spi-nor"` (generic) or vendor-specific like `"winbond,w25q128"`

**Q23:** How does spi-tools help in debugging?
**A:** `spi-config`: shows/sets config. `spi-pipe`: sends/receives data. Quick command-line SPI access.

**Q24:** What permission is needed for /dev/spidev?
**A:** Read/Write. User typically needs to be in `spi` or `dialout` group, or use root.

**Q25:** What is `devm_spi_register_controller()` in kernel?
**A:** Registers a managed SPI master controller with the SPI core. Auto-cleanup on driver removal.

---

# DECK 07: QSPI / OSPI / xSPI (20 cards)

**Q1:** How many data lines in Quad SPI?
**A:** 4 (IO0, IO1, IO2, IO3)

**Q2:** How many data lines in Octal SPI?
**A:** 8 (IO0–IO7)

**Q3:** What does "1-1-4" mean in QSPI?
**A:** Command on 1 line, Address on 1 line, Data on 4 lines.

**Q4:** What does "4-4-4" mean in QSPI?
**A:** All phases (command, address, data) on 4 lines.

**Q5:** Is QSPI full-duplex?
**A:** No — half-duplex. Data lines change direction between phases.

**Q6:** What is XIP in QSPI?
**A:** Execute-In-Place — Flash memory-mapped to CPU address space. CPU reads code directly.

**Q7:** What is DDR in context of SPI?
**A:** Double Data Rate — data transferred on both rising and falling edges → 2× throughput.

**Q8:** What is DQS?
**A:** Data Strobe — slave-generated signal toggling with data, used by master for capture timing at high speed.

**Q9:** QSPI at 50 MHz: what's the max throughput?
**A:** 50 MHz × 4 bits = 200 Mbps (SDR). 400 Mbps if DDR.

**Q10:** OSPI at 200 MHz DDR: max throughput?
**A:** 200 MHz × 8 bits × 2 (DDR) = 3.2 Gbps

**Q11:** What JEDEC standard covers xSPI?
**A:** JESD251

**Q12:** What were IO2 and IO3 originally used for in NOR Flash?
**A:** IO2 = Write Protect (WP̄), IO3 = Hold (HOLD̄). Repurposed as data lines in quad mode.

**Q13:** What is the "dummy cycles" phase in fast read?
**A:** Extra clock cycles after address — gives Flash internal time to access data before output.

**Q14:** How many dummy cycles for typical QSPI fast read?
**A:** 6–10 dummy cycles (varies by device and speed)

**Q15:** Can standard SPI commands work on QSPI Flash?
**A:** Yes — QSPI Flash supports standard 1-1-1 commands (backward compatible).

**Q16:** What is the "Enter QPI" command?
**A:** Special command (e.g., 0x38) that switches Flash to 4-4-4 mode (all phases on 4 lines).

**Q17:** What is Continuous Read mode?
**A:** After first read, Flash expects only address (no command byte) for subsequent reads. Saves 8 clocks per read.

**Q18:** XIP latency vs internal Flash?
**A:** Higher — QSPI XIP adds command+address+dummy cycles. Typically 10–20 clock cycles overhead per read.

**Q19:** What is SFDP in SPI Flash?
**A:** Serial Flash Discoverable Parameters (JEDEC JESD216) — standardized table in Flash describing capabilities, speeds, commands.

**Q20:** Why is OSPI used in automotive?
**A:** High bandwidth for code/data storage, memory-mapped XIP for fast boot, JEDEC standardized, reliable NOR technology.

---

# DECK 08: ERRORS & DEBUGGING (20 cards)

**Q1:** SPI returns all 0xFF — most likely cause?
**A:** CS̄ not going LOW (MISO floating with pull-up). Check CS̄ GPIO config.

**Q2:** SPI returns bit-shifted data — most likely cause?
**A:** CPHA mismatch or extra/missing clock pulse.

**Q3:** SPI works at 1 MHz but not 10 MHz — most likely cause?
**A:** Signal integrity (ringing, setup/hold violation) or device max speed exceeded.

**Q4:** How to test SPI without any slave?
**A:** Loopback: connect MOSI→MISO. Sent data should equal received data.

**Q5:** What does OVR mean and how to fix?
**A:** Overrun — CPU didn't read DR before next byte arrived. Fix: use DMA, enable FIFO, faster ISR.

**Q6:** What is bus contention in SPI?
**A:** Two slaves driving MISO simultaneously (both CS̄ LOW). Can damage devices.

**Q7:** How to detect bus contention?
**A:** Oscilloscope shows MISO voltage between HIGH and LOW (indeterminate level). Or unexpected current spike.

**Q8:** What does MODF indicate?
**A:** Mode Fault — another device pulled SS̄ LOW while in master mode. Multi-master conflict.

**Q9:** SPI works in debug mode but fails at full speed — why?
**A:** Timing-dependent bug: debugger slows everything, masking race condition. Check volatile, ISR priority, DMA.

**Q10:** How to verify correct SPI mode for a device?
**A:** Send known command (like JEDEC ID 0x9F), check response matches datasheet.

**Q11:** SPI Flash JEDEC ID = 0xEF4018 means what?
**A:** Winbond (0xEF), device type 0x40, capacity 0x18 (2^24 = 16 MB = W25Q128).

**Q12:** What is "first byte garbage" in SPI?
**A:** First byte after CS̄ is corrupted. Cause: insufficient CS̄ setup time. Fix: add delay after CS̄ assert.

**Q13:** CS̄ deasserts between bytes — how to fix?
**A:** Don't use hardware CS̄. Use GPIO CS̄ with manual control.

**Q14:** How to diagnose intermittent SPI errors?
**A:** Add error counters (OVR, bit errors). Correlate with events (temperature, load, EMI source).

**Q15:** What oscilloscope bandwidth for 50 MHz SPI?
**A:** ≥250 MHz (rule: 5× signal frequency for accurate waveform capture).

**Q16:** SPI trace length limit at 50 MHz?
**A:** ~10 cm uncontrolled. With impedance matching, up to 30 cm. Use λ/10 rule.

**Q17:** What is ringing on SPI signals?
**A:** Oscillation at signal edges due to impedance mismatch. Causes false clock edges, bit errors.

**Q18:** How to fix ringing?
**A:** Add series termination resistor (22–33Ω) at source. Shorten traces. Add ground plane.

**Q19:** What causes "CS̄ glitch"?
**A:** Noise on CS̄ line causes brief LOW pulse → slave partially activates → state machine corrupted.

**Q20:** QSPI XIP crash after resume — why?
**A:** QSPI controller reset during suspend. CPU tries XIP read before controller re-initialized. Resume code must be in RAM.

---

# DECK 09: AUTOMOTIVE & SAFETY (20 cards)

**Q1:** Where is SPI used in automotive?
**A:** Boot Flash (QSPI NOR), sensors (ADC, IMU), displays, CAN transceivers, audio codecs.

**Q2:** What is the Qualcomm QUP engine?
**A:** Unified serial engine configurable as SPI/I2C/UART. Used in SA8155P/SA8295P for peripheral access.

**Q3:** What is AUTOSAR SPI Channel?
**A:** Single data buffer pair (TX+RX) for one transfer unit.

**Q4:** What is AUTOSAR SPI Job?
**A:** Collection of channels transferred in one CS̄ assertion.

**Q5:** What is AUTOSAR SPI Sequence?
**A:** Ordered list of jobs. May span multiple CS̄ cycles with priority.

**Q6:** What ASIL level typically applies to SPI boot Flash?
**A:** ASIL-B to ASIL-D (boot Flash failure → no system boot → safety violation).

**Q7:** How to detect SPI bus failure in safety system?
**A:** Periodic JEDEC ID read, CRC on data, timeout monitoring, alive counter check.

**Q8:** What is E2E protection for SPI?
**A:** End-to-End data protection: CRC + counter + data ID in payload. Detects corruption, repetition, loss.

**Q9:** Why NOR Flash over NAND for automotive boot?
**A:** NOR: bit-addressable, XIP capable, higher endurance (100K-1M), no bad block management.

**Q10:** What is OTP in SPI NOR Flash?
**A:** One-Time Programmable — write-once region for security keys, calibration, serial numbers.

**Q11:** How to protect SPI Flash from unauthorized write?
**A:** WP̄ pin (hardware), block protection bits (software), OTP lock, secure boot verification.

**Q12:** What is dual-bank Flash in automotive?
**A:** Two copies of firmware; update inactive bank, verify, switch. Power-fail safe updates.

**Q13:** SPI boot time optimization — 3 techniques?
**A:** QSPI instead of standard SPI, XIP mode, DDR clocking.

**Q14:** What diagnostic test for SPI in production?
**A:** Device ID read, loopback test, read/write verification, timing margin test.

**Q15:** What DTC (Diagnostic Trouble Code) for SPI failure?
**A:** Vendor-specific, but typically under communication/ECU internal. Report via UDS $19.

**Q16:** SPI failure safe state — what should happen?
**A:** Depends on application. Boot Flash fail → stay in bootloader. Sensor fail → use last known value or default.

**Q17:** How does ISO 26262 classify SPI?
**A:** SPI itself is not classified. The function using SPI inherits its ASIL level. Bus is a dependent failure path.

**Q18:** What is conformal coating over SPI traces?
**A:** Protective layer preventing physical probing/manipulation of SPI signals. Anti-tamper measure.

**Q19:** What is the automotive temperature range for SPI?
**A:** Grade 1: -40°C to +125°C. SPI timing margins decrease at extremes.

**Q20:** What is SPI in context of HSM (Hardware Security Module)?
**A:** HSM may use SPI to access external secure Flash. SPI bus inside HSM domain must be isolated from non-secure world.

---

# DECK 10: COMPARISONS & FORMULAS (20 cards)

**Q1:** SPI throughput formula (standard)?
**A:** Throughput = SCLK_frequency × 1 bit/clock (100% efficient)

**Q2:** QSPI throughput formula (SDR)?
**A:** Throughput = SCLK_frequency × 4 bits/clock

**Q3:** OSPI DDR throughput formula?
**A:** Throughput = SCLK_frequency × 8 bits/clock × 2 (DDR)

**Q4:** Maximum SPI clock formula?
**A:** f_max = 1 / (t_CO + t_SU + t_propagation)

**Q5:** SPI baud rate on STM32?
**A:** SPI_CLK = APB_CLK / 2^(BR+1)

**Q6:** SPI vs UART: speed?
**A:** SPI: 1–200 MHz. UART: typically ≤1 Mbps. SPI is 100–1000× faster.

**Q7:** SPI vs I2C: speed?
**A:** SPI: 1–200 MHz. I2C: 100 kHz–3.4 MHz. SPI is 10–100× faster.

**Q8:** SPI vs I2C: wires?
**A:** SPI: 4 + 1 per additional slave. I2C: 2 wires regardless of slave count.

**Q9:** SPI vs CAN: use case?
**A:** SPI: on-board peripherals. CAN: inter-ECU automotive network. Different domains.

**Q10:** SPI vs I2C: addressing?
**A:** SPI: hardware CS̄ (no address). I2C: 7/10-bit software address.

**Q11:** SPI vs UART: duplex?
**A:** Both full-duplex. SPI is synchronous, UART is asynchronous.

**Q12:** SPI vs UART: overhead?
**A:** SPI: 0% overhead. UART: 20% (start+stop bits for 8N1).

**Q13:** Time to transfer 1 KB via SPI at 10 MHz?
**A:** 1024 bytes × 8 bits / 10 MHz = 819.2 µs ≈ 0.82 ms

**Q14:** Time to transfer 1 MB via QSPI at 50 MHz?
**A:** 1M bytes × 8 bits / (50 MHz × 4) = 41.9 ms

**Q15:** Capacitance budget at 50 MHz SPI?
**A:** Keep total bus capacitance < 50 pF. Each device ~5–10 pF.

**Q16:** Series termination resistor value for SPI?
**A:** 22–33Ω at source (near driver output).

**Q17:** CS̄ pull-up resistor value?
**A:** 4.7K–10KΩ

**Q18:** Required scope bandwidth for SPI at 50 MHz?
**A:** ≥250 MHz (5× rule)

**Q19:** NOR Flash endurance vs NAND?
**A:** NOR: 100K–1M cycles. NAND: 1K–100K cycles. NOR is 10–1000× more durable.

**Q20:** SPI power formula?
**A:** P_dynamic ∝ f × C_load × V² — higher speed = more power, but shorter active time.

---

END OF DOCUMENT 04 — FLASHCARDS
